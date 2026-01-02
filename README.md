# LAMP-Stack


## 📌 **Project Title**

**Scalable WordPress Deployment on AWS Using LAMP Stack, EC2, RDS, ALB, Auto Scaling, and CloudFront**

---

## 📘 **Project Overview**

This project involves architecting, implementing, and documenting a **high-performance, highly available WordPress hosting solution on AWS**. The core of the web server tier is built on a **LAMP stack** — **Linux (OS), Apache (HTTP server), MySQL (database), and PHP (scripting language)** — running on Amazon EC2 instances. The WordPress database uses **Amazon RDS for MySQL**, and the overall system integrates **Application Load Balancer (ALB)**, **Auto Scaling Groups (ASG)**, and **CloudFront CDN** for scalability, availability, and global performance optimization. ([Amazon Web Services, Inc.][1])

---

## 🛠️ **Technical Architecture**

### 📍 **Core Components**

* **LAMP Stack on Amazon EC2**
  Deploys **Linux** as the operating system, **Apache** as the web server, **PHP** as the server-side scripting language, and integration with **MySQL** for database operations (though in this design, the database is offloaded to RDS). This stack provides the foundational runtime environment for WordPress to serve both static and dynamic content. ([Amazon Web Services, Inc.][1])

* **Amazon RDS for MySQL**
  A managed relational database service that stores all WordPress data securely, with automated backups, snapshots, and optional Multi-AZ deployment for enhanced uptime.

* **Application Load Balancer (ALB)**
  Distributes incoming HTTP/S requests across multiple EC2 instances in the LAMP web tier, improving availability and fault tolerance.

* **Auto Scaling Group (ASG)**
  Ensures that EC2 capacity scales automatically according to load (e.g., CPU utilization, traffic patterns), enabling efficient cost management and responsiveness during traffic spikes.

* **Amazon CloudFront**
  A global content delivery network (CDN) that caches static assets (images, CSS, JavaScript) at edge locations, reducing latency for users worldwide and offloading traffic from the origin servers.

---

## 🏗️ **Workflow**

1. **DNS & CDN Integration**
   Users access the WordPress site through a domain that points to a CloudFront distribution. CloudFront caches static content globally and forwards dynamic requests to the ALB.

2. **Traffic Balancing & Scaling**
   The **Application Load Balancer** receives incoming requests and evenly distributes them to EC2 instances running the **LAMP stack** inside an **Auto Scaling Group** configured across multiple Availability Zones for high availability.

3. **WordPress Processing (LAMP Stack)**

   * **Apache** receives incoming requests and, for dynamic pages, hands them over to **PHP**.
   * **PHP** executes WordPress code and interacts with **MySQL** (via Amazon RDS) to fetch or store content.
   * WordPress dynamically generates and returns HTML based on the database responses and business logic.

4. **Database Operations**
   All persistent content (posts, media references, settings) is managed by **MySQL on Amazon RDS**, which simplifies maintenance and enables automatic backups.

---

## 🔐 **Security & Networking**

* A **VPC** with public subnets (for ALB) and private subnets (for EC2 & RDS) isolates each layer, reducing security risks.
* **Security Groups** restrict traffic — only allow HTTP/S to the load balancer and database connections from the EC2 web tier.
* **SSL/TLS** termination is configured at the ALB using certificates from **AWS Certificate Manager (ACM)**.

---

## 📈 **Key Benefits**

✔ **Scalability** — Auto Scaling adjusts capacity based on actual demand.
✔ **High Availability** — ALB and multi-AZ deployments improve uptime.
✔ **Global Performance** — CloudFront ensures fast content delivery worldwide.
✔ **Managed Database** — RDS simplifies maintenance, backups, and upgrades.

By leveraging the classic **LAMP stack** as the application layer foundation on EC2, you benefit from a proven, widely supported environment used in countless web hosting scenarios, including WordPress deployments. ([Amazon Web Services, Inc.][1])

---

## 📌 **Optional Extensions**

* **Amazon ElastiCache** for in-memory caching (Redis/Memcached) to boost PHP performance.
* **AWS WAF** (Web Application Firewall) in front of CloudFront for security filtering.
* **EFS / S3** for shared media storage across instances.

---

## 🧩 **Conclusion**

This project delivers a **robust, scalable WordPress infrastructure** leveraging AWS best practices and a **LAMP stack** to ensure a stable, performant platform capable of handling traffic spikes while maintaining high availability and operational manageability. ([Amazon Web Services, Inc.][1])

---
