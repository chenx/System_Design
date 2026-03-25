# Full Stack Design

##  Creating a Full-Stack Web Application Platform from Scratch and Operating it for (almost) Free

https://medium.com/@isaac.adams/part-1-from-0-to-1-creating-a-full-stack-web-application-platform-from-scratch-and-operating-it-cd56ed8c2b0a

### Things Every Web Application Should Have

- Core Languages & Frameworks
- Source Control
- Developer Tooling
- Domain Name Services (DNS)
- Runtime/Compute (Container, Docker/Kubernete)
- Authentication and Authorization (Authn/Authz)
- Persistent Storage (Database)
- Backups
- Continuous Integration / Continuous Deployment (CI/CD)
- Testing and Test Coverage Analysis
- Monitoring and Observability
- Application Security Analysis
- Acceptable Performance Characteristics

### Back of envelope calculation:

- DAU, QPS
- CPU/RAM/HD/Network
- Read/Write volume

### Others

- handling large amounts of traffic
- handling high transaction volumes
- high availability
- performance for a globally distributed audience
- mobile devices
- internationalization (i18n) and localization (l10n)

## Scaling a Full-Stack Web Application Platform to Handle 1M Daily Visitors

https://medium.com/@isaac.adams/part-2-from-1-to-1m-scaling-a-full-stack-web-application-platform-to-handle-1m-daily-visitors-24994ef1532b

<br/>

# Building Robust Web Apps: A Full-Stack System Design Overview

https://www.linkedin.com/pulse/building-robust-web-apps-full-stack-system-design-overview-elmore-gpd4e/

- Frontend Frameworks: The User Interface (UI) Layer
  - React, Vue, Angular
- Version Control & CI/CD: Automating the Development Workflow
  - GitHub – Manages source code and collaboration
  - Jenkins/Docker – Automates testing and deployment
  - CI/CD Pipelines – Ensures rapid, error-free releases
- Web Servers & Cloud Hosting: Where Your App Lives
  - AWS, Azure, DigitalOcean, Google Cloud – Scalable cloud solutions
  - NGINX, Apache – Popular web servers for handling requests
  - API Gateway & Load Balancer – Distributes incoming traffic across multiple servers
- Backend, APIs & Database Storage
  - Node.js, Express, or Django – Backend frameworks to handle business logic
  - REST or GraphQL APIs – Facilitates communication between the frontend and backend
  - Database Storage (SQL or NoSQL) – Stores user data (PostgreSQL, MongoDB, etc.)
- Monitoring & Logging: Keeping the System Healthy
  - PM2 – Process management for Node.js applications
  - Sentry – Tracks application errors and performance issues
  - Log Monitoring & Alerts – Notifies admins via mobile/email if something goes wrong

