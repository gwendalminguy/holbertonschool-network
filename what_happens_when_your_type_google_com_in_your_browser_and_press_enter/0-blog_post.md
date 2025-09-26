## What happens when you type `google.com` in your browser and press enter?

To answer this question, let's cut it down to small sections, covering each step of the process.

### DNS Resolution

When a user tries to access a website such as `https://www.google.com` from their browser, the **Domain Name** must first be translated into an **IP Address** to establish the connection. To find this address, the browser will first check its own cache, where the entry may exist if the website has been visited recently. If not, it will ask the operating system, which will also check its cache and then the `hosts` file. If the IP address is still not found, the operating system will contact the configured **DNS Server**, which acts like a directory for the internet. It translates human-readable names (`google.com`) into numerical IP addresses (`8.8.8.8`). The IP address is then sent back to the operating system and passed on to the browser.

### TCP Connection

Now that it knows the correct IP, the browser opens a connection to the server using the **Transmission Control Protocol** (TCP). When data is sent over the internet, it is broken down into small chunks called packets. The TCP connection establishes a **reliable channel** by ensuring that both the client and the server agree on how these packets will be numbered and acknowledged, so they can be reassembled in the right order. This guarantees that the full message arrives correctly, even if packets take different routes across the network.

### HTTPS & TLS Certificate

Once the connection is established, there is another step before the browser actually asks for the desired page, since the **Hypertext Transfer Protocol Secure** (HTTPS) is being used. The secure version of HTTP means all traffic must be encrypted. To achieve this, the browser signals to the server that it wants to use the **Transport Layer Security** (TLS) protocol and requests a **TLS Certificate**. This certificate is used so that the browser and the server can securely exchange keys, enabling encryption of all future communication.

### Firewall

Most servers today have a **Firewall** set up for security purposes. It helps filter unwanted connections and strengthens network security by allowing or blocking attempts based on several criteria:

- **Port Blocking**: A classic firewall can be configured to restrict available ports for external connections, such as allowing only port `443` to enforce the use of `HTTPS`. If a client tries to connect to any other port, the connection will be dropped. This ensures that only the intended services are reachable.

- **Traffic Filtering**: A firewall can block certain **types of traffic**, such as `P2P`, or restrict access to a **specific IP range** by denying connections from outside that range. This can also be used to implement **geolocation rules** to deny connections from specific countries or regions.

- **Port Scanning**: A firewall can block probes that attempt to scan for open ports in search of weak entry points. This prevents malicious actors from trying to break into the server.

- **Common Attacks**: A **Web Application Firewall** (WAF), placed where traffic is already unencrypted, can filter malicious payloads to prevent **SQL or XSS injections**, or detect and drop abnormal surges of traffic to mitigate **Denial-of-Service** (DoS) attacks.

### Load-Balancer

In the case of a **Distributed Web Infrastructure**, where the web stack (such as the combination of a **Web Server**, an **Application Server**, and a **Database Server**) is replicated across several instances to handle high traffic, the request first arrives at a **Load Balancer** before being treated. This is a software or hardware component that uses a **distribution algorithm** to spread requests across multiple servers. The **round robin** algorithm is a common and simple approach, forwarding requests sequentially to each server in turn. By distributing the load, heavy traffic can be handled efficiently, reducing performance issues and ensuring a smoother user experience.

### Web Server

When the request reaches one of the servers, the first component it encounters is the **Web Server** (commonly software like `Nginx` or `Apache`). The web server’s role is to handle incoming HTTP requests and decide what to do with them. If the request is for a **static resource** (such as an image, a CSS file, or a JavaScript file), the web server can serve it directly from its file system without needing to ask anything else. If the request concerns a **dynamic resource**, such as a page that depends on user data or account information, the web server forwards the request to the **Application Server** for further processing.

### Application Server

The **Application Server** is responsible for running the actual **business logic** of the website or application. It takes the request forwarded by the web server and interprets it according to the application code (written in languages like `Python`, `Java`, `PHP`, or `Node.js`). For example, if the user requested their profile page, the application server determines what data is needed, applies any required rules or checks (like verifying that the user is authenticated), and prepares the necessary instructions to fetch data from the **Database Server**. It then combines that data with templates or logic defined in the application code to generate a dynamic response, usually in the form of an HTML page.

### Database Server

The **Database Server** is where the persistent data of the application is stored and managed (using systems like `MySQL`, `PostgreSQL`, or `MongoDB`). When the application server sends a query (such as getting all information for a specific user), the database server processes it by searching its stored tables or documents and returns the matching results. The application server then takes this raw data, structures it according to the application’s needs, and sends it back to the web server. Finally, the web server packages this response into an HTTP reply that travels back over the network to the user’s browser, where the requested page is displayed.

In a **Distributed Web Infrastructure**, where several instances of the application exist to handle heavy traffic, the **Database Server** also needs special consideration. If every server had its own isolated database, the data could quickly become inconsistent. To solve this, most systems rely on a **Primary-Replica model**. In this setup, a single **Primary Database** handles all write operations (such as `inserting` or `updating` user information), and these changes are then replicated to one or more **Replica Databases**, which can be used for read operations. This ensures that data stays synchronized across the infrastructure: users can connect to different servers without seeing outdated or conflicting information, while the workload is distributed between multiple database instances.
