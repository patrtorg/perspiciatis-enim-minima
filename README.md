# Tie Logger

> 👔 Fully typed minimal platform-agnostic logger

[![Test Status](https://github.com/patrtorg/perspiciatis-enim-minima/actions/workflows/test.yml/badge.svg)](https://github.com/patrtorg/perspiciatis-enim-minima)
[![Downloads](https://img.shields.io/npm/dt/@patrtorg/perspiciatis-enim-minima.svg)](https://npmjs.com/package/@patrtorg/perspiciatis-enim-minima)
[![last commit](https://img.shields.io/github/last-commit/AlexXanderGrib/@patrtorg/perspiciatis-enim-minima.svg)](https://github.com/patrtorg/perspiciatis-enim-minima)
[![codecov](https://img.shields.io/codecov/c/github/AlexXanderGrib/@patrtorg/perspiciatis-enim-minima/main.svg)](https://codecov.io/gh/AlexXanderGrib/@patrtorg/perspiciatis-enim-minima)
[![GitHub](https://img.shields.io/github/stars/AlexXanderGrib/@patrtorg/perspiciatis-enim-minima.svg)](https://github.com/patrtorg/perspiciatis-enim-minima)
[![@patrtorg/perspiciatis-enim-minima](https://snyk.io/advisor/npm-package/@patrtorg/perspiciatis-enim-minima/badge.svg)](https://snyk.io/advisor/npm-package/@patrtorg/perspiciatis-enim-minima)
[![Known Vulnerabilities](https://snyk.io/test/npm/@patrtorg/perspiciatis-enim-minima/badge.svg)](https://snyk.io/test/npm/@patrtorg/perspiciatis-enim-minima)
[![Quality](https://img.shields.io/npms-io/quality-score/@patrtorg/perspiciatis-enim-minima.svg?label=quality%20%28npms.io%29&)](https://npms.io/search?q=@patrtorg/perspiciatis-enim-minima)
[![npm](https://img.shields.io/npm/v/@patrtorg/perspiciatis-enim-minima.svg)](https://npmjs.com/package/@patrtorg/perspiciatis-enim-minima)
[![license MIT](https://img.shields.io/npm/l/@patrtorg/perspiciatis-enim-minima.svg)](https://github.com/patrtorg/perspiciatis-enim-minima/blob/main/LICENSE.txt)
[![Size](https://img.shields.io/bundlephobia/minzip/@patrtorg/perspiciatis-enim-minima)](https://bundlephobia.com/package/@patrtorg/perspiciatis-enim-minima)
[![Codacy Badge](https://app.codacy.com/project/badge/Grade/c32597c51ac540b08a2474575ae25cbb)](https://www.codacy.com/gh/AlexXanderGrib/@patrtorg/perspiciatis-enim-minima/dashboard?utm_source=github.com&utm_medium=referral&utm_content=AlexXanderGrib/@patrtorg/perspiciatis-enim-minima&utm_campaign=Badge_Grade)

## 📦 Installation

- **Using `npm`**
  ```shell
  npm i @patrtorg/perspiciatis-enim-minima
  ```
- **Using `Yarn`**
  ```shell
  yarn add @patrtorg/perspiciatis-enim-minima
  ```
- **Using `pnpm`**
  ```shell
  pnpm add @patrtorg/perspiciatis-enim-minima
  ```

## Usage

### Initialization

```javascript
/** @file: logger.js */
import { Logger, logLevels, filter } from "@patrtorg/perspiciatis-enim-minima";

export const logger = new Logger(
  "app", // Root logger name
  logLevels(), // Define log levels. By default are: verbose, debug, info, warn, error, fatal
  // You can use custom levels by using
  // logLevels("info", "warn", "error")

  {
    // Custom data
    appVersion: "3.1"
    moduleName: "root",
    moduleVersion: "1.0.0"
  }
);

export const child = logger.child(
  // Child logger name
  "auth",

  // Child logger data
  { moduleName: "auth", moduleVersion: "0.3.1" }
);

const criticalLogs = [];

const unsubscribe = logger.subscribe(
  // Subscribe to all logs, they go to console
  (log) => console.log(...log.message.parts),

  // All logs, that level is greater or equal than "warn" will be added to critical logs

  // Severity is determined by index of level in levels array
  // Current array is: verbose, debug, info, warn, error, fatal
  //                             [less] <<<  ^^^^   >> [greater]
  filter(">=", "warn", (log) => criticalLogs.push(log))
)

process.on("SIGINT", () => {
  unsubscribe();
})
```

## Logging

```javascript
/** @file: index.js */
import { child, logger } from "./logger.js";

const PORT = parseInt(process.env.PORT) || 3000;
logger.subscribe(log => console.log(log));

child.log.debug`Application initialized. Port: ${{ port: PORT }}. Environment: ${{process.env}}`;
// Level:  ^^^^^

// Here goes app
```

## Log format

```javascript
({
  // One of defined levels
  level: "debug",

  message: {
    template:
      "Application initialized. Port: {port}. Environment: {SHELL,COLORTERM,PWD}",
    plain:
      'Application initialized. Port: 3000. Environment: {"SHELL":"/bin/bash","COLORTERM":"truecolor","PWD":"/home/alexxgrib/Projects/@patrtorg/perspiciatis-enim-minima"}',
    parts: [
      "Application initialized. Port:",
      { port: 3000 },
      ". Environment: ",
      {
        SHELL: "/bin/bash",
        COLORTERM: "truecolor",
        PWD: "/home/alexxgrib/Projects/@patrtorg/perspiciatis-enim-minima"
      }
    ]
  },

  // merge of
  // - logger data
  // - logger parents data
  // - data passed in log message
  data: {
    appVersion: "3.1",
    moduleName: "auth",
    moduleVersion: "0.3.1",
    port: 3000,
    SHELL: "/bin/bash",
    COLORTERM: "truecolor",
    PWD: "/home/alexxgrib/Projects/@patrtorg/perspiciatis-enim-minima"
  },

  context: {
    // name of the logger
    name: "auth",

    // list of logger inheritance
    path: ["app", "auth"]
  },

  // logger object
  origin: child
});
```
