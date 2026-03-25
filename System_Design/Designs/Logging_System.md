# Logging Eco-System

11/3. System Design. Apple

## Question

- library
- where it's stored
- framework for everyone to use and expand.


## Answer

### Requirements

- functional 
  - log
  - Indexing/Query (assume we already have this)
  - levels: info, warning, error
  - different languages. Java for now
- non-functional
  - CAP (AP, eventual consistency)
  - availability
  - consistency
  - scalability
  - safety
  - maintainability
  - extensibility (expand)
- additional
  - monitoring, alerting
- assumptions, estimation, calculation
  - user:
    - developers: target users
    - production 
  - log storage
  - DAU
  - volume: millions entries / hour

### High level design

- workflow
  - save to file (local)
  - save to database (local / remote)
- data model
  - RDBMS
  - NoSQL
    - logging append structure: just append, which is more efficient
  - Read/Write: 
    - Write intensive
    - Read may be spiky (for warning / error messages)
- API
  - write: Application
    - void log(String message, LogType type, LogLevel level, String source, Format format, Date timestamp)
    - Type: info/warning/error
    - Level: 1, 2, 3, ... (higher level means higher precedence / significance)
    - Source: file path and line number
  - read: Server side:
    - Class logEntry {
        long logId,
        Type type,
        int level,
        String message,
        String source,
        Date timestamp,
        LogFormat format
      }
    - logEntry read(Level level, Date startTime, Date, endTime, format)
    - logEntry read(long logId)
    - logEntry read(...)

### Deep dive

- core components
  - Log library
    - a library to be imported into Java Application codebase
    - Remote logging server
      - application server to send log to
      - RPC / gRPC
    - local
      - (default and preferred) file system: /tmp
      - (not preferred, but configurable) local database
    - default + configuration
    - Installation package
      - Install
      - configuration file
        - location of the log
        - format of the log
        - storage space of the log
- details
  - interface
    - void log(String message, Level level, String source, Date timestamp)
    - concurrency
      - lock a file when write
        - lock, shared variable, atomic, mutex (CPU based)
      - race, keep track of which thread
      - another possibility: 1 log file for each thread
      - millions / 60 sec
        - store in memory first
          - queue messages in memory, async write, write in batch
          - filter: only write critical ones (warning, error)
        - parallel write to multiple logs (one file for each thread)
        - file size: 
          - going around (wrap around from beginning)
          - separate messages at different levels: 
            - errors in one file: not so frequent, don't overwrite
            - warnings in one file: not so frequent, don't overwrite
            - info in one file: not that important, can overwrite (wrap around).
      - change to use database as storage
        - both file and database
          - configuration with default settings
            - default: 
              - log errors and warnings to database
                - provide monitoring dashboard, alerting service, visualization, metrics reporting
              - info logs can just go to file
              - configuration: the size for each
      - scalability (discussed above)
        - depends on volume 
      - sensitive logs
        - redact sensitive data
          - 1) default ones: such as PII
          - 2) configurable: by rule (e.g., regular expressions)
      - reliability
        - correctness
        - scalable (discussed above)
        - safety: avoid data loss
          - shouldn't exceed disk capacity 
            - if log size is over 90% of disk capacity, alert user to increase capacity, or clear/move logs
          - make copies (e.g., a background process to make copies to another place, configurable)
  - configuration
    - multiple files
    - control file size
  - implementation details and challenges


Scaling considerations (discussed above)
