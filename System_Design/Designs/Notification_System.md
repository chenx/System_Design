# Notification System

## Clarifying questions

- What types of notifications does the system support?  Push notification, SMS message, and email.
- Is it a real-time system? Soft real-time system; asap, but short delay is acceptable
- Supported devices: iOS devices, android devices, and laptop/desktop
- Trigger: Notifications can be triggered by client applications. They can also be scheduled on the server-side.
- Can users be able to opt-out? Yes
- Volume per day: 10 million mobile push notifications, 1 million SMS messages, and 5 million emails.

SMS: Short Message Service

## High Level Design

- iOS: Provider -> APNS (Apple Push Notification Service) -> iOS
- Android: Provider -> FCM (Firebase Cloud Messaging) -> Android
- SMS: Provider -> SMS service (Twilio, Nexmo) -> SMS
- Email: Provider -> Email Service -> Email

### Contact Info gathering flow

mobile device tokens, phone numbers, or email

Sign up for notification: User -> Load Balancer -> API servers -> Databases

### Notification sending/receiving flow

services (1, 2, ... , N) => Notification System => APN, FCM, SMS service, Email service -> iOS / Android / SMS / Email

<br/>

## Deep Dive

- Reliability.
  - prevent data loss: persist data in database; retry mechanism
  - will recipients receive notice exactly once? No.
    - Dedupe: check if eventID has been seen before; show notice to user only when not seen before.

- Additional component and considerations:
  - notification template,
  - notification settings: user_id, channel, opt_in
  - rate limiting,
  - retry mechanism,
  - security in push notifications,
  - monitor queued notifications and event tracking.
    - start -> pending -> sent -> deliver -> click / unsubscribe

- Updated design

