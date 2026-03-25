# Waymo callback service

## Requirements

### functional
- 5000 cars
- management info about these cars (inventory, registry)
- get weather or other reasons
- call vehicles to return to the base

### non-funcitonal
- low latency
- availability
- scalability
- security

### extra

### calculations

CPU, RAM, HD/Storage, Network bandwidth, Read/Write

DAU/QPS

#### Storage: RDBMS: car info
 10k of text data, 1MB of iamge
 5000 cars -> 50m Text, 5GB of image

#### calculate and aggregate location information

- Data transmission
 - location of each vehicle
   - QPS: 10 / sec: 50k / sec
     - adaptable: QPS depends on situation: driving, or parking, or waiting in traffic.
   - Assume each data point: 1k.
     - field: carID, location (lat/long), speed, direction, car params
     - 50k / sec * 1k = 50 mb / sec
       - network bandwith should be ok.
       - 50 mb * 86400 seconds / day =~ 50 * 10^5 mb = 5000 GB = 5 TB
       - 5 TB / day * 365 days / year = 1.8 PB / year
     - Storage for data
     - data retention policy: remove old data in 6 month or 1 year, or longer
 - sensor collection info while driving


## High level design

### flow

 Monitoring Service
 - Weather center
 - Information reported from each vehicle
 - Database Model
   { message_id, message_body, category_id }

 Decision Service (make decision based on monitoring)
 - Database Model
   { decision_id, action_id }
  CallBack Service (ask cars to return)
 - Database Model
   { callback_id, vehical_id, reason_id, time } (1 : N relation)
 - Decide location of car
 - Given location, decide all cars in this area
 - Send command to cars
 - Cars should be able to respond with location, and reason if lagging behind schedule
   - Geohashing
     - Base32 (RFC 4648)
     - A-Z, 2-7 (0, 1, 8 ignored b/c they may be confused with O,l,B)
     - 7eie28jei
   - Quadtree

### Data model

### API

## Deep dive

### Core components

- scaling

### Case study: Bad weather

Monitoring Service
- Weather, Location / area

Decision Service
- Threashold, alert is issued.

CallBack Service (ask cars to return)
- Input: Location / area
- Output: find relevant cars
- Send message to all relevant cars
 - base location
 - time

Car
- report situation (has passenger or not, when can I return)
- periodically send data about location
- CallBack Service periodically check updates, make adjustments if needed
- This cycle continues until all cars return successfully
- Emergy handling: may contact human operator to take actions if needed.

Bottleneck:
- data transmission
- latency
- in case of many cars: thruough - horizontal scaling
- Large amount of data, don't want to loss any data packet.
  - queue
  - server load
  - data transmission
  - if car doesn't get instruction, may wait by road side.
