# Unique ID Generator in a Distributed System

Requirements
- Unique
- Numeric
- Sortable by time
- 64 bits
- can generate more than 10,000 IDs / second

Options
- Multi-master replication
  - Database auto_inc ID
  - 3 servers, round robin
  - Hard to scale up/down
- Ticket server
  - Single pointer of failure; challenge of replication
- UUID
  - 128 bits
  - Not sortable by time
  - Not numeric
- Snowflake ID
  - Good choice
  - 1 bit unused, 41 bits for timestamp in ms, 10 bits for machine ID, 12 bits for sequence number in 1 ms (allow 64 million IDs per second)
  - 41 bits: good for 69 years


## UUID

- https://en.wikipedia.org/wiki/Universally_unique_identifier

A universally unique identifier (UUID) is a 128-bit number designed to be a unique identifier for objects in computer systems.

- There are on the order of 10^38 possible UUID values
- Versions 1 and 6 (date-time and MAC address)
  - Version 1 concatenates the 48-bit MAC address of the "node" (that is, the computer generating the UUID), with a 60-bit timestamp, 
    being the number of 100-nanosecond intervals since midnight 15 October 1582 Coordinated Universal Time (UTC)

## Snowflake ID

- https://en.wikipedia.org/wiki/Snowflake_ID
- https://medium.com/@jitenderkmr/demystifying-snowflake-ids-a-unique-identifier-in-distributed-computing-72796a827c9d

Snowflake IDs, or snowflakes, are a form of unique identifier used in distributed computing. 
The format was created by X (formerly Twitter) and is used for the IDs of tweets.

Snowflakes are 64 bits in binary. (Only 63 are used to fit in a signed integer.) 
- First bit is unused.
- The first 41 bits are a timestamp, representing milliseconds since the chosen epoch.
- The next 10 bits represent a machine ID, preventing clashes. (E.g., Data center ID + Machine ID)
- Twelve more bits represent a per-machine sequence number, to allow creation of multiple snowflakes in the same millisecond.
- The final number is generally serialized in decimal.

```
+--------------------------------------------------------------------------+
| 1 Bit Unused | 41 Bit Timestamp |  10 Bit NodeID  |   12 Bit Sequence ID |
+--------------------------------------------------------------------------+
```
