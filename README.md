# Advanced Node.js Logging

Node Logger is a powerful tool that allows you to log messages to the console, file, or other destinations. It provides a simple and flexible API for logging messages, making it easy to debug and monitor your Node.js applications. Console logs are great for quick debugging, but they can be difficult to manage and analyze.


## Installation
To install Winston, you can use the following command:
     
    npm install winston morgan

This will install the winston and morgan packages, which are used to configure and use the Winston logger.

## Configuration
To configure Winston, you can create a new file called logger.js in the root directory of your project and add the following code:
#
**logger.js**
   
    import {createLogger, format, transports} from "winston";
    const {combine, timestamp, json, colorize} = format;

    // Custom format for console logging with colors
    const consoleLogFormat = format.combine(
      format.colorize(),
      format.printf(({ level, message, timestamp }) => {
        return `${level}: ${message}`;
      })
    );

    // Create a Winston logger
    const logger = createLogger({
      level: "info",
      format: combine(colorize(), timestamp(), json()),
      transports: [
        new transports.Console({
          format: consoleLogFormat,
        }),
        new transports.File({ filename: "app.log" }),
      ],
    });

    export default logger;

This code configures Winston to log messages to the console and a file called app.log. It also sets the log level to info and uses the colorize and timestamp formatters to add colors and timestamps to the console logs.

## Usage

To use the Winston logger, you can import it in your code and use it as follows:

First go to your index.js file and add the following code:
#
**index.js**

    import logger from "./logger";
    import morgan from "morgan";

    const morganFormat = ":method :url :status :response-time ms";

    app.use(
      morgan(morganFormat, {
        stream: {
          write: (message) => {
            const logObject = {
              method: message.split(" ")[0],
              url: message.split(" ")[1],
              status: message.split(" ")[2],
              responseTime: message.split(" ")[3],
            };
            logger.info(JSON.stringify(logObject));
          },
        },
      })
    );

This will log the messages to the console and the file app.log. The morgan package is used to format the log messages and the stream option is used to write the log messages to the console.

## Easy log messages
To use the Winston logger, you can import it in your code and use it as follows:

    import logger from "./logger";

    logger.info("This is an info message");
    logger.error("This is an error message");
    logger.warn("This is a warning message");
    logger.debug("This is a debug message");

This will log the messages to the console and the file app.log.


## Advanced logger.js file 

To configure Winston to write in different files like error and all

#
**Logger.js** 

    import winston from "winston";

    const { combine, timestamp, printf, errors } = winston.format;

    const logFormat = printf(({ level, message, timestamp, stack }) => {
      return `${timestamp} [${level.toUpperCase()}]: ${stack || message}`;
    });

    const logger = winston.createLogger({
      level: "info",
      format: combine(
        timestamp({ format: "YYYY-MM-DD HH:mm:ss" }),
        errors({ stack: true }),
        logFormat
      ),
      transports: [
        new winston.transports.File({
          filename: "logs/error.log",
          level: "error",
        }),
        new winston.transports.File({
          filename: "logs/combined.log",
        }),
      ],
    });

    export default logger;


## Summary
This guide demonstrates the use of advanced logging in Node.js using Winston and Morgan. It covers configuring Winston as a centralized logger and integrating it into the application to capture structured logs with different severity levels.

By using an advanced logging setup, logs can be written to the console, files, or other transports, enabling better visibility into application behavior. This approach significantly improves debugging, error tracking, and monitoring, especially in production environments.

With this logging strategy in place, Node.js applications become easier to maintain, diagnose, and scale.
