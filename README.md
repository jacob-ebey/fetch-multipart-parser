# fetch-multipart-parser

`fetch-multipart-parser` is a streaming multipart parser for JavaScript's fetch API.

## Features

This package is a streaming multipart parser for JavaScript's fetch API, making it easy to parse incoming `Request` bodies that are generated by `<form enctype="multipart/form-data">` in the browser.

## Installation

```sh
$ npm install fetch-multipart-parser
```

## Usage

```typescript
import { MultipartParseError, parseMultipartFormData } from 'fetch-multipart-parser';

function handleMultipartRequest(request: Request): void {
  try {
    // The parser `yield`s each part as a MultipartPart as it becomes available.
    for await (let part of parseMultipartFormData(request)) {
      console.log(part.name);
      console.log(part.filename);
      console.log(part.mediaType);

      if (/^text\//.test(part.mediaType)) {
        console.log(new TextDecoder().decode(part.content));
      } else {
        // part.content is binary data, save it to a file
      }
    }
  } catch (error) {
    if (error instanceof MultipartParseError) {
      console.error('Failed to parse multipart/form-data:', error.message);
    } else {
      console.error('An unexpected error occurred:', error);
    }
  }
}
```

## Benchmark

The results of running the benchmarks on my laptop:

```
Platform: Darwin (23.5.0)
CPU: Apple M2 Pro
Node.js v20.15.1
Date: 7/18/2024, 12:36:05 PM
┌────────────────────────┬──────────────────┬──────────────────┬──────────────────┬───────────────────┐
│ (index)                │ 1 small file     │ 1 large file     │ 100 small files  │ 5 large files     │
├────────────────────────┼──────────────────┼──────────────────┼──────────────────┼───────────────────┤
│ fetch-multipart-parser │ '0.02 ms ± 0.10' │ '1.95 ms ± 0.29' │ '0.13 ms ± 0.03' │ '29.52 ms ± 1.46' │
│ busboy                 │ '0.03 ms ± 0.11' │ '4.10 ms ± 0.49' │ '0.17 ms ± 0.03' │ '43.38 ms ± 2.96' │
│ @fastify/busboy        │ '0.03 ms ± 0.07' │ '1.86 ms ± 0.67' │ '0.32 ms ± 0.04' │ '21.15 ms ± 1.63' │
└────────────────────────┴──────────────────┴──────────────────┴──────────────────┴───────────────────┘
```
