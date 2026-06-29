Phase 1: Backend Setup & Server Provisioning
EC2 Initialization: I launched an AWS Ubuntu EC2 instance (t2.micro - Free Tier) and configured a Security Group to open ports 22 (SSH), 80 (HTTP), and 3000 (Node Backend).
Environment Setup: I SSH'd into the instance and installed the required dependencies: Node.js, npm, and Git.
Backend Configuration: I cloned the project repository, installed backend dependencies, and tested the Node.js server to ensure it successfully listened on port 3000.

<img width="1637" height="1024" alt="TM_EC2 Instance created_1" src="https://github.com/user-attachments/assets/f80abb63-a077-463a-b2cb-4c7ae4bf1e26" />
<img width="1637" height="1024" alt="TM_Setting up security group for EC2_2" src="https://github.com/user-attachments/assets/a54db034-4bc0-4ae3-ae84-643e4b090676" />
<img width="1637" height="1024" alt="TM_Connected to AWS Console_3" src="https://github.com/user-attachments/assets/cf2593b8-358f-42c7-84eb-46b66092af89" />
<img width="1637" height="1024" alt="TM_performing Git clone_4" src="https://github.com/user-attachments/assets/4985f17f-c38f-4174-8c8d-562ea87a1018" />



Phase 2: Database Integration
MongoDB Atlas: I created a free M0 tier cluster on MongoDB Atlas to host my database in the cloud, removing the need to manage a local database on the EC2 server.
Network Security: I configured the Atlas network access to allow connections from anywhere (0.0.0.0/0) to ensure my EC2 instance could communicate with the cluster.
Connection: I securely connected my backend to the database by injecting the MongoDB URI connection string into the .env file on my server.

<img width="1637" height="1024" alt="TM_creating MongoDB cluster_5" src="https://github.com/user-attachments/assets/919bb2b2-939b-49e7-9bf8-526af2e0a923" />
<img width="1637" height="1024" alt="TM_adding MongoDB URI_6" src="https://github.com/user-attachments/assets/3cdbd109-d9a5-4225-96fd-9fe35a9c47d9" />
<img width="1637" height="1024" alt="TM_backend able to reach DB_7" src="https://github.com/user-attachments/assets/dd9ed5dc-d01a-4462-9e16-cc0ad6246e1e" />



Phase 3: Frontend Build & Configuration
Environment Mapping: I updated the React frontend configuration (urls.js) to point API calls to my EC2 instance's Public IPv4 address.
Production Build: I ran npm run build to compile the React code into optimized, static HTML/CSS/JS files inside the build/ directory, ready to be served to users.

<img width="1637" height="1024" alt="Connecting to server from new terminal for Frontend_8" src="https://github.com/user-attachments/assets/608cac28-bd36-4db0-8b0d-e87905e6cb96" />


Phase 4: Nginx Reverse Proxy & PM2 Daemon
Process Management (PM2): Instead of running the backend manually, I installed pm2 and started the Node.js app as a background daemon (pm2 start index.js). This ensured the backend would stay online 24/7 and automatically restart if it crashed.
Nginx Setup: I installed Nginx to act as my web server and reverse proxy.
Routing Configuration: I modified the /etc/nginx/sites-available/default file with two specific routing rules:
Route 1 (/): Served the static React build folder to incoming users.
Route 2 (/api/): Proxied backend data requests directly to localhost:3000.
Permissions: I updated Linux directory permissions (chmod 755) to ensure Nginx had read access to my frontend build folder, eliminating 500 Internal Server Errors.


<img width="1637" height="1024" alt="Displaying Ngnix default page_9" src="https://github.com/user-attachments/assets/37ec2075-04c5-4262-a5f9-3c4f47f95528" />
<img width="1637" height="1024" alt="TM_able to access TM website successfully_10" src="https://github.com/user-attachments/assets/62360b5f-1dba-4a13-a5cc-26bd05ce7704" />
<img width="1637" height="1024" alt="TM_MongoDB cluster is fully setup_11" src="https://github.com/user-attachments/assets/120fbc18-d77e-4ca9-ae26-2483483ed5fe" />




Phase 5: High Availability & Load Balancing (Scaling)
AMI Creation: To scale the application, I took a "snapshot" of my fully configured server by creating an Amazon Machine Image (AMI).
Horizontal Scaling: I launched a completely identical second EC2 instance (Server-2) from the AMI, ensuring it was placed in a different Availability Zone (us-east-1c) than the first server (us-east-1a) for fault tolerance.
Target Group: I created an AWS Target Group and registered both EC2 instances to monitor their health on port 80.
Application Load Balancer (ALB): I deployed an Internet-facing ALB spanning across multiple subnets. I configured it to intelligently distribute incoming web traffic across both healthy servers in my Target Group.

<img width="1637" height="1024" alt="TM_creating copy of my EC2 to setup multiple servers_12" src="https://github.com/user-attachments/assets/f5de6cc4-789a-448f-933e-5fc6c1ac1878" />
<img width="1637" height="1024" alt="TM_EC2 Copy image created_13" src="https://github.com/user-attachments/assets/e9b0c900-101d-45e0-b38d-58e6989bd6f2" />
<img width="1637" height="1024" alt="TM_2 EC2 instances running side by side_14" src="https://github.com/user-attachments/assets/1c4453b5-d18f-4d38-b6bd-362d917a0624" />
<img width="1637" height="1024" alt="TM_Created Target group_15" src="https://github.com/user-attachments/assets/d10c2bb8-20d4-407f-83c7-bf17f3727e19" />
<img width="1637" height="1024" alt="TM_creating load balancer_16" src="https://github.com/user-attachments/assets/02bf5cee-3703-47fa-853e-97ed120ec14f" />
<img width="1637" height="1024" alt="TM_ALB activated with DNS name generated to access the React app_17" src="https://github.com/user-attachments/assets/663dc4b3-6dc3-4cc3-b802-725b94f702a5" />
<img width="1637" height="1024" alt="TM_app running through ALB_18" src="https://github.com/user-attachments/assets/f827e854-bf54-4992-98da-fc29f18722d1" />



Phase 6: Custom Domain & Cloudflare (Optional)
DNS Management: I registered a custom domain and pointed its nameservers to Cloudflare.

CNAME Mapping: In Cloudflare's DNS settings, I created a CNAME record pointing my root domain to the messy AWS Load Balancer DNS URL, giving users a clean entry point.

SSL/TLS Encryption: I enabled Cloudflare's "Flexible" SSL mode, upgrading my application from standard HTTP to secure HTTPS, adding the professional green padlock to the site.


<img width="1637" height="1024" alt="TM_custom domain name setup using Cloudlare_19" src="https://github.com/user-attachments/assets/650f7f23-140d-4259-918a-92b2486e5bcd" />
