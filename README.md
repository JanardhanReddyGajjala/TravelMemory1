# TravelMemory -- AWS Deployment Documentation

## 1. Project overview

-   **Application:** TravelMemory (MERN stack: MongoDB, Express.js,
    React, Node.js)
-   **Cloud:** Amazon Web Services (AWS)
-   **Region:** US East (N. Virginia), `us-east-1`
-   **Database:** MongoDB Atlas
-   **Web server / reverse proxy:** Nginx
-   **Load balancing:** Application Load Balancers (ALB)

This README records the deployment workflow, configuration stages,
screenshot evidence checklist, and troubleshooting performed. The
screenshot names below are suggested filenames. Add the actual images to
`screenshots/`; this README does not embed image files automatically.

## 2. High-level architecture

``` text
Browser
  |
  v
Frontend ALB -> Frontend target group -> Frontend EC2 (React + Nginx)
                                              |
                                         /api requests
                                              |
                                              v
Backend ALB -> Backend target group (port 3001)
                 |                  |
                 v                  v
          backend-master2        backend-2
            Node/Express         Node/Express
                 \                  /
                  \                /
                   v              v
                      MongoDB Atlas
```

## 3. AWS resources recorded

### EC2 instances

  --------------------------------------------------------------------------------------------
  Role           Name                 Instance ID             Private IP        Port
  -------------- -------------------- ----------------------- ----------------- --------------
  Frontend       `frontend-master3`   `i-0e1e9d882b83025c6`   `172.31.28.50`    HTTP 80

  Backend        `backend-master2`    `i-09d31dbea45a9ce09`   `172.31.42.142`   3001

  Backend        `backend-2`          `i-0eca7791e1a3a9cd7`   `172.31.32.170`   3001
  --------------------------------------------------------------------------------------------

Public IP addresses may change after stop/start unless Elastic IPs are
assigned. Always check the current addresses before changing Atlas
network access.

### Load balancers and target groups

  --------------------------------------------------------------------------------------------------------
  Component                           Recorded name / value
  ----------------------------------- --------------------------------------------------------------------
  Frontend ALB                        `travelmemory-frontend-alb`

  Frontend ALB DNS                    `travelmemory-frontend-alb-1334734304.us-east-1.elb.amazonaws.com`

  Frontend target group               `travelmemory-frontend-tg`

  Backend ALB                         `travelmemory-backend-alb`

  Backend target group                `travelmemory-backend-tg`

  Backend target port                 `3001`

  Frontend listener                   HTTP port `80`
  --------------------------------------------------------------------------------------------------------

## 4. Screenshot checklist

Create a `screenshots/` folder and save the screenshots using the
suggested names. Include only images actually captured; omit or mark
unavailable items as pending.

### A. MongoDB Atlas

  ---------------------------------------------------------------------------------------
  No.                     Suggested filename                      Screenshot should show
  ----------------------- --------------------------------------- -----------------------
  01                      `01-mongodb-atlas-cluster.png`          Atlas project and
                                                                  TravelMemory cluster.

  02                      `02-mongodb-database-collections.png`   Database and
                                                                  `tripdetails`
                                                                  collection, if shown.

  03                      `03-mongodb-network-access.png`         Security → Network
                                                                  Access / IP Access
                                                                  List. Hide sensitive
                                                                  details.

  04                      `04-mongodb-connection-success.png`     Successful database
                                                                  connection in
                                                                  application or
                                                                  terminal.
  ---------------------------------------------------------------------------------------

### B. EC2 creation and configuration

  -------------------------------------------------------------------------------------
  No.                     Suggested filename                    Screenshot should show
  ----------------------- ------------------------------------- -----------------------
  05                      `05-ec2-instances-overview.png`       Frontend and backend
                                                                instances in EC2.

  06                      `06-frontend-ec2-details.png`         Frontend instance
                                                                details and networking.

  07                      `07-backend-master-ec2-details.png`   `backend-master2`
                                                                instance details.

  08                      `08-backend-2-ec2-details.png`        `backend-2` instance
                                                                details.

  09                      `09-ec2-security-groups.png`          Relevant
                                                                inbound/outbound rules.

  10                      `10-ec2-availability-zones.png`       Instance Availability
                                                                Zone placement.
  -------------------------------------------------------------------------------------

### C. Backend deployment

  -------------------------------------------------------------------------------------------------------------
  No.                     Suggested filename                          Screenshot should show
  ----------------------- ------------------------------------------- -----------------------------------------
  11                      `11-backend-project-files.png`              Backend files and `index.js`.

  12                      `12-backend-dependencies.png`               Package configuration / dependency
                                                                      installation.

  13                      `13-backend-systemd-service.png`            `travelmemory-backend.service`
                                                                      configuration; redact secrets.

  14                      `14-backend-service-running.png`            `systemctl status travelmemory-backend`
                                                                      showing active service.

  15                      `15-backend-trip-api-200.png`               `curl -i http://localhost:3001/trip`
                                                                      returning HTTP 200 and trip data.

  16                      `16-backend-mongodb-connection-error.png`   MongoDB connection failure encountered.

  17                      `17-backend-mongodb-connection-fixed.png`   Successful database connection after
                                                                      correction.
  -------------------------------------------------------------------------------------------------------------

### D. Frontend build and Nginx

  -----------------------------------------------------------------------------------
  No.                     Suggested filename                  Screenshot should show
  ----------------------- ----------------------------------- -----------------------
  18                      `18-frontend-project-files.png`     React project
                                                              structure.

  19                      `19-frontend-build-success.png`     Successful production
                                                              build.

  20                      `20-nginx-configuration.png`        Nginx site and API
                                                              routing configuration;
                                                              hide secrets.

  21                      `21-nginx-service-status.png`       Nginx service active.

  22                      `22-nginx-api-trip-200.png`         `/api/trip` through
                                                              Nginx returning HTTP
                                                              200.

  23                      `23-frontend-browser-working.png`   TravelMemory frontend
                                                              in browser.
  -----------------------------------------------------------------------------------

### E. AMI and additional backend instance

  ------------------------------------------------------------------------------------
  No.                     Suggested filename                   Screenshot should show
  ----------------------- ------------------------------------ -----------------------
  24                      `24-backend-ami-created.png`         AMI created from
                                                               configured backend.

  25                      `25-ami-available.png`               AMI available status.

  26                      `26-second-backend-instance.png`     Additional backend
                                                               instance
                                                               created/configured.

  27                      `27-backend-instances-running.png`   Both backend instances
                                                               running.
  ------------------------------------------------------------------------------------

### F. Target groups and load balancers

  ----------------------------------------------------------------------------------------------
  No.                     Suggested filename                             Screenshot should show
  ----------------------- ---------------------------------------------- -----------------------
  28                      `28-backend-target-group-configuration.png`    Backend target group
                                                                         health check and port
                                                                         3001.

  29                      `29-backend-targets-healthy.png`               Both backend targets
                                                                         healthy.

  30                      `30-backend-alb-listener.png`                  Backend ALB listener
                                                                         and forwarding rule.

  31                      `31-backend-alb-details.png`                   Backend ALB details and
                                                                         DNS.

  32                      `32-frontend-target-group-configuration.png`   Frontend target group
                                                                         configuration.

  33                      `33-frontend-target-healthy.png`               Frontend target
                                                                         healthy.

  34                      `34-frontend-alb-listener.png`                 Frontend ALB HTTP
                                                                         listener and forwarding
                                                                         rule.

  35                      `35-frontend-alb-details.png`                  Frontend ALB details
                                                                         and DNS.

  36                      `36-travelmemory-alb-browser-test.png`         Application accessed
                                                                         through frontend ALB
                                                                         DNS.
  ----------------------------------------------------------------------------------------------

### G. Domain, DNS and HTTPS

  ---------------------------------------------------------------------------------------
  No.                     Suggested filename                    Screenshot should show
  ----------------------- ------------------------------------- -------------------------
  37                      `37-domain-registration.png`          Domain management page
                                                                for
                                                                `shreeyaanshitech.net`;
                                                                hide account details.

  38                      `38-godaddy-dns-records.png`          Relevant GoDaddy DNS
                                                                records.

  39                      `39-acm-certificate-request.png`      ACM request for root
                                                                domain and `www` name.

  40                      `40-acm-validation-cnames.png`        Exact ACM validation
                                                                CNAME names and values.

  41                      `41-acm-certificate-status.png`       ACM pending-validation or
                                                                issued status.

  42                      `42-domain-dns-validation-test.png`   DNS lookup used to verify
                                                                CNAME records.

  43                      `43-https-alb-listener.png`           HTTPS listener on port
                                                                443 with issued
                                                                certificate, once
                                                                configured.

  44                      `44-custom-domain-browser-test.png`   Custom domain loading
                                                                over HTTPS, once working.
  ---------------------------------------------------------------------------------------

### H. Troubleshooting and recovery

  -----------------------------------------------------------------------------------------
  No.                     Suggested filename                        Screenshot should show
  ----------------------- ----------------------------------------- -----------------------
  45                      `45-ec2-stopped-instances.png`            EC2 console showing
                                                                    stopped instances.

  46                      `46-cloudtrail-stopinstances-event.png`   CloudTrail
                                                                    `StopInstances` event;
                                                                    redact account/session
                                                                    details.

  47                      `47-backend-targets-unhealthy.png`        Backend target group
                                                                    showing unhealthy
                                                                    targets.

  48                      `48-backend-systemd-mongodb-error.png`    Backend logs showing
                                                                    Atlas connection
                                                                    failure.

  49                      `49-backend-api-retest.png`               API test after
                                                                    database/network
                                                                    correction.

  50                      `50-target-group-health-after-fix.png`    Target group status
                                                                    after recovery.

  51                      `51-frontend-503-or-502-error.png`        Browser 503/502 error
                                                                    during troubleshooting,
                                                                    if retained.

  52                      `52-final-application-validation.png`     Final successful
                                                                    frontend and API
                                                                    checks.
  -----------------------------------------------------------------------------------------

## 5. Useful deployment commands

### Backend service

``` bash
sudo systemctl status travelmemory-backend --no-pager
sudo systemctl restart travelmemory-backend
sudo journalctl -u travelmemory-backend -n 50 --no-pager
```

### Backend API

``` bash
curl -i http://localhost:3001/trip
```

Expected successful response: `HTTP/1.1 200 OK` with trip data.

### Nginx

``` bash
sudo systemctl status nginx --no-pager
sudo nginx -t
sudo systemctl restart nginx
curl -i http://localhost/api/trip
```

## 6. Troubleshooting notes

### MongoDB Atlas connection failure

A backend log showed `DB disconnected` and an error stating that the
application could not connect to any servers in the MongoDB Atlas
cluster. The message identified an IP address not being present in the
Atlas allowlist as one common cause.

Check: 1. EC2 instance state. 2. Current public IP address of each
backend instance. 3. MongoDB Atlas Security → Network Access / IP Access
List. 4. Database username, password, connection string and database
name. 5. Backend service logs and the `/trip` API response.

Add only the current required IP address using `/32` where appropriate.
Do not use `0.0.0.0/0` as a permanent workaround.

### Unhealthy backend targets / HTTP 503

A target group screenshot showed two registered backend targets on port
`3001`, both unhealthy with `Health checks failed`.

Troubleshooting sequence: 1. Confirm both backend EC2 instances are
running. 2. Confirm the Node.js service remains active. 3. Test
`http://localhost:3001/trip` on each backend. 4. Verify MongoDB Atlas
connectivity. 5. Check security group rules and target group
health-check path/port. 6. Refresh the target group and inspect health
status details.

A service can briefly report `active (running)` and then exit if
database connection fails. Check both service status and API response.

### EC2 instances stopped automatically

The CloudTrail history reviewed during troubleshooting showed
`StopInstances` actions associated with an assumed role/session
identified as `JanitorCrossAccountRole` / `aws-janitor`. The AWS account
was provided by a training institute. Contact the institute
administrator about the lab run window or stop policy. Do not attempt to
bypass institute-managed controls.

### ACM certificate pending validation

1.  Copy the exact validation CNAME name and value from ACM.
2.  Add the CNAME records to the authoritative DNS provider.
3.  Verify public DNS resolution.
4.  Wait for ACM validation.

Certificate validation is separate from the backend 503 issue.

## 7. Recorded deployment status

These statuses were observed at different points in the work; they are
not necessarily simultaneous.

  -----------------------------------------------------------------------
  Area                                Recorded observation
  ----------------------------------- -----------------------------------
  React frontend build                Successful build output was
                                      observed.

  Backend API                         `/trip` returned HTTP 200 during an
                                      earlier successful test.

  Nginx API route                     `/api/trip` returned HTTP 200
                                      during an earlier test.

  MongoDB Atlas                       A connection failure occurred when
                                      backend IP access was not allowed.

  Backend target group                A later screenshot showed 2
                                      registered targets and 0 healthy;
                                      both failed health checks.

  Frontend ALB                        Browser returned HTTP 503 while
                                      backend targets were unhealthy.

  EC2 lifecycle                       Instances were stopped by an
                                      automated assumed-role action;
                                      institute administrator follow-up
                                      is required.

  Custom domain / ACM                 Certificate DNS validation was
                                      pending in the latest recorded
                                      troubleshooting.
  -----------------------------------------------------------------------

### Next validation steps

-   [ ] Confirm all required EC2 instances are running.
-   [ ] Verify current backend public IP addresses.
-   [ ] Verify MongoDB Atlas Network Access.
-   [ ] Confirm both backend services remain active.
-   [ ] Confirm both local `/trip` API tests return HTTP 200.
-   [ ] Confirm backend targets become healthy.
-   [ ] Confirm frontend target is healthy.
-   [ ] Test the frontend ALB DNS name.
-   [ ] Complete ACM DNS validation and HTTPS/domain configuration
    separately.

## 8. Suggested repository structure

``` text
TravelMemory/
├── README.md
└── screenshots/
    ├── TravelMemory_AWS_Deployment_Actual_Screenshots.docx
    
```

Only add screenshots you actually captured. If an image is unavailable,
omit it or mark it as pending.

## 9. Followed Security checklist before sharing

-   [ ] Remove MongoDB usernames, passwords and credential-bearing
    connection strings.
-   [ ] Never upload `.pem` private keys, AWS access keys, secret keys
    or `.env` files.
-   [ ] Hide AWS account IDs, personal email addresses and session
    identifiers where not needed.
-   [ ] Review security group rules and avoid exposing unnecessary
    ports.
-   [ ] Do not publish private application configuration.
