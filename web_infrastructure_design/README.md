# Introduction to Web Infrastructure Design

This directory contains the web infrastructure designs for this project, one
section per task, together with the diagram source and its rendered image.

Each design is delivered as a plain-text diagram (`.mmd`) and a rendered image
(`.png`). The plain-text representation is used instead of Mermaid source with
the agreement of the project supervisor; the diagram content, labels, boundaries
and connection directions follow the requirements of the task in full.

## 0. Model a Single-Server Web Stack

### Diagram

![Single-Server Web Stack](single_server_stack.png)

The plain-text version of the same design is in `single_server_stack.mmd`.

### Request path

1. The **User** opens `www.foobar.com` in a browser.
2. The name `www.foobar.com` is resolved by **DNS**, which answers with the
   IPv4 address `8.8.8.8`.
3. The browser then connects straight to `8.8.8.8`, the address held in the
   **A record** for `www.foobar.com`. This is the connection into the
   server-side entry point of the design.
4. The **Web server (Nginx)** is that entry point. It accepts the HTTP request.
5. Nginx serves static content itself, and reverse-proxies anything that needs
   processing to the **Application server**.
6. The application server runs the **Application code**.
7. The application code reads from and writes to the
   **Database (PotsgreSQL/MySQL)**.
8. The response travels back the other way: database → application code →
   application server → Nginx → user.

Name resolution happens *before* the connection is opened. It is a lookup to the
side of the request path, not a hop along it, which is why the response does not
travel back through it. The diagram shows this by placing the resolution step
beside `www.foobar.com` rather than between it and the server.

### What is a server?

A server is a computer that provides services to other computers over a network.
It may be a **physical machine**, or a **virtual machine** running on physical
hardware shared with other virtual machines.

A server runs an **operating system**, such as Linux, which manages its CPU,
memory, storage and network interfaces and which the services depend on.

A server is commonly hosted in a **data centre**, which supplies reliable
electricity, network connectivity, cooling and physical security rather than
relying on an office or a home connection.

The **server is the machine**; Nginx, the application server, the application
code and PostgreSQL/MySQL are **services and software running on that machine**.
Restarting a service is not the same as restarting the server, and the machine
can be healthy while a service on it has failed.

### DNS and the A record

**DNS** stands for **Domain Name System**. It is the distributed directory that
translates a human-readable name such as `www.foobar.com` into the IP address a
browser needs in order to open a connection. Without it, users would have to
know and type the address of every site they visit.

In this scenario the `www` record is an **A record** because the value it has to
return is a **literal IPv4 address**, `8.8.8.8`. An A record is exactly the
record type that maps a hostname to an IPv4 address. Had `www` needed to point
at *another hostname* instead, the correct record type would have been a
**CNAME**; had the target been an IPv6 address, it would have been an **AAAA**
record. The design places one machine at one IPv4 address, so an A record is the
right choice.

### Web server — Nginx

The **Web server (Nginx)** is what listens for incoming HTTP requests and is the
first component of the server side to see a request. It serves static files such
as images, stylesheets and scripts directly, and acts as a **reverse proxy**,
forwarding requests that need application logic to the application server and
returning that answer to the client.

### Application server

The **Application server** is the runtime that hosts the application. It receives
the requests Nginx forwards, executes the application code for each one inside a
process it manages, and hands the produced response back to Nginx.

### Application code

The **Application code** is the part written for this particular website — its
business logic. It validates input, handles authentication, decides which data is
needed, applies the rules of the site and builds the response body.

### Database

The **Database (PotsgreSQL/MySQL)** stores the data that has to outlive a single
request: accounts, sessions, posts, orders and so on. The application code reads
from it to answer requests and writes to it to record changes.

### TCP/IP communication

The user's computer and the server communicate across a network using the
**TCP/IP protocol suite**.

**IP** is responsible for addressing and routing: it is what makes `8.8.8.8`
reachable and gets packets from the user's network to the server's network.
**TCP** runs on top of IP and provides a reliable, ordered, connection-oriented
byte stream between the two endpoints, retransmitting anything lost on the way.
**HTTP** (or HTTPS) is then carried inside that TCP connection. The request and
the response in this design both cross the network this way.

### LAMP

**LAMP** is a historical acronym for a common web stack:

- **L** — Linux, the operating system
- **A** — Apache, the web server
- **M** — MySQL, the database
- **P** — PHP (or Perl/Python), the application language

This design is **LAMP-like rather than a literal LAMP stack**. It follows the
same shape — one Linux machine hosting a web server, an application runtime and a
relational database — but it uses **Nginx in place of Apache**, and it does not
prescribe PHP as the application language. Calling it a LAMP stack would
therefore be inaccurate.

### Limitations of this design

#### Single point of failure — SPOF

Every component sits on one machine, which makes that machine a **single point of
failure (SPOF)**. If it loses power, loses network, runs out of disk or suffers a
hardware fault, the web server, the application server, the application code and
the database all go down together. There is nothing else to serve the site.

#### Downtime during maintenance

Any maintenance that requires restarting the machine — a kernel upgrade, a
hardware change, a configuration change that needs a reboot — takes the whole
site offline for the duration. Even restarting a single service, such as
deploying new application code, interrupts the requests it was serving, because
there is no second copy to take the traffic in the meantime.

#### Capacity limit

One machine has a fixed amount of CPU, memory, disk I/O and network bandwidth,
which every service must share. As the number of queries per second (QPS) rises,
those services compete with each other — the database and the application code in
particular — and response times grow until requests start being refused. The only
way to grow is to make the single machine bigger, and that has a ceiling.

### Self-validation

- [x] `single_server_stack.mmd` renders successfully.
- [x] The diagram contains every required label and one explicit server boundary.
- [x] The diagram shows directional request and response paths.
- [x] The README correctly identifies the A record and explains DNS.
- [x] The README distinguishes the machine, web server, application server, application code, and database.
- [x] The README explains TCP/IP and defines LAMP without calling the Nginx design a literal LAMP stack.
- [x] The README identifies the three required limitations.

Note on the first item: with the supervisor's agreement, `single_server_stack.mmd`
holds a plain-text diagram rather than Mermaid source, and
`single_server_stack.png` is the rendered image. Both were checked to display
correctly and to carry the same components, labels, boundary and connection
directions.

Additional checks carried out on this design:

- [x] Each required label appears exactly once: `User`, `www.foobar.com`, `DNS`,
      `Web server (Nginx)`, `Application server`, `Application code`,
      `Database (PotsgreSQL/MySQL)`.
- [x] The connection leaving `www.foobar.com` towards the server-side entry point
      carries the label `A record → 8.8.8.8`.
- [x] Every connection is drawn with a direction.
- [x] The request reaches `Web server (Nginx)` before the application layer.
- [x] The response returns database → application code → application server →
      Nginx → user, and is not routed back through name resolution.
- [x] No load balancer, redundant application path, security control or
      monitoring control appears in the design.
- [x] All text files are UTF-8 and end with a newline.

## Repository structure

```text
holbertonschool-web_infrastructure_design/
├── README.md
└── web_infrastructure_design/
    ├── README.md
    ├── single_server_stack.mmd
    └── single_server_stack.png
```
