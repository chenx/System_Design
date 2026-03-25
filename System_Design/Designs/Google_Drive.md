# Design Google Drive

Google Drive, Dropbox, Microsoft OneDrive, and Apple iCloud.

## Clarifying questions

- Major features? Upload and download files, file sync, and notifications.
- mobile app, a web app, or both? Both.
- supported file formats? Any type.
- file size limit? <= 10 GB
- users? 10M DAU

Features to discuss in this design
- Add files. The easiest way to add a file is to drag and drop a file into Google drive.
- Download files.
- Sync files across multiple devices. When a file is added to one device, it is automatically synced to other devices.
- See file revisions.
- Share files with your friends, family, and coworkers
- Send a notification when a file is edited, deleted, or shared with you.

Features not discussed:
- Doc editting and collaboration

Non-functional requirements:
- Reliability. Reliability is extremely important for a storage system. Data loss is unacceptable.
- Fast sync speed. If file sync takes too much time, users will become impatient and abandon the product.
- Bandwidth usage. Especially when they are on a mobile data plan.
- Scalability. The system should be able to handle high volumes of traffic.
- High availability. Should be usable when some servers are offline, slowed down, or have unexpected network errors.

Calculations:
- Assume the application has 50 million signed up users and 10 million DAU.
- Users get 10 GB free space.
- Assume users upload 2 files per day. The average file size is 500 KB.
- 1:1 read to write ratio.
- Total space allocated: 50 million * 10 GB = 500 Petabyte
- QPS for upload API: 10 million * 2 uploads / 24 hours / 3600 seconds = ~ 240
- Peak QPS = QPS * 2 = 480

## High Level Design

Let us start with a single server setup as listed below:
- A web server to upload and download files.
- A database to keep track of metadata like user data, login info, files info, etc.
- A storage system to store files. We allocate 1TB of storage space to store files.

### API

#### Upload file

Types:
- Simple upload
- Resumable upload

A resumable upload is achieved by the following 3 steps [2]:
- Send the initial request to retrieve the resumable URL.
- Upload the data and monitor upload state.
- If upload is disturbed, resume the upload.

https://api.example.com/files/upload?uploadType=resumable

#### Download

Example API: https://api.example.com/files/download

Params:
- path: download file path.

Example params:
{
  "path": "/recipes/soup/best_soup.txt"
}

#### See revisions

Example API: https://api.example.com/files/list_revisions

Params:
- path: The path to the file you want to get the revision history.
- limit: The maximum number of revisions to return.

Example params:
{
  "path": "/recipes/soup/best_soup.txt",
  "limit": 20
}

### Move away from single server

Amazon Simple Storage Service (Amazon S3)
- An object storage service that offers industry-leading scalability, data availability, security, and performance
- Amazon S3 supports same-region and cross-region replication

Points:
- Load balancer: Add a load balancer to distribute network traffic. A load balancer ensures
  evenly distributed traffic, and if a web server goes down, it will redistribute the traffic.
- Web servers: After a load balancer is added, more web servers can be added/removed
  easily, depending on the traffic load.
- Metadata database: Move the database out of the server to avoid single point of failure.

In the meantime, set up data replication and sharding to meet the availability and scalability requirements.
- File storage: Amazon S3 is used for file storage. To ensure availability and durability,
  files are replicated in two separate geographical regions.

#### Workflow

Users / Clients -> Load Balancer -> API Servers -> 1) File Storage, 2) Metadata DB

#### Sync conflicts

<br/>

## Deep Dive

### Block servers

High consistency requirement

Metadata database

Upload flow

Download flow

Notification service

Save storage space

Failure Handling

