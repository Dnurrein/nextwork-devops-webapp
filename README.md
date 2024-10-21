Part 1: Web App in the Cloud
Introduction
This project focuses on hosting a web application on Amazon Web Services (AWS), leveraging cloud infrastructure to ensure scalability, reliability, and performance. AWS provides a wide range of services that facilitate efficient deployment and management of web applications, allowing us to take full advantage of cloud computing benefits like auto-scaling, cost optimization, and high availability.
The key AWS services utilized in this project include:
•	Amazon EC2 for hosting the web application servers.
•	Amazon S3 for storing static assets such as images, CSS, and JavaScript files.
•	Amazon RDS for managing the relational database, ensuring scalability and high availability.
•	Elastic Load Balancing (ELB) to distribute incoming traffic across multiple EC2 instances for better performance and fault tolerance.
•	Amazon CloudFront as a Content Delivery Network (CDN) to speed up content delivery to users globally.
•	Amazon Route 53 for domain name management and DNS routing.
Architecture Diagram
The architecture diagram will illustrate the key components and their interactions within the AWS environment. It will show the flow of traffic from users through the AWS services, such as the load balancer and the application tier, to the database and storage layers (yet to be designed). This visual representation will help to clarify how the components are connected and how the web application is hosted and managed in the cloud.
 
![image](https://github.com/user-attachments/assets/209bd658-3ef4-4343-b1b1-72234251e388)

Execution
Launching an EC2 instance
Launching an EC2 instance creates a virtual server that is accessible on the public internet. This server will host my webapp and will receive traffic from clients interacting with my application.
I also enabled SSH (secure shell) a protocol that secure remote access to computers or servers over an encrypted connection. I enabled SSH traffic so that I can connect to my ELB. My instance only allows traffic from my ELB. Here my server is secure.
Setting up a key pair - A key pair is a private security key that enables you to connect to your virtual machine through your physical computer. Once I setup my key pair, AWS automatically downloads a copy of the key pair into your local machine. AWS offers provision to either download the key pair as a .PEM file for “SSHing” via VSCode/CMD or .PPK, a PuTTY private key file. I used SSH in this project to connect to my instance, either approach is works.
Connect into the EC2 instance
Once the instance was up and running, I opened a terminal from VSCode and established connection to my virtual server. The terminal allowed me to interact with my AMI by entering commands. It provides access to the system’s command-line interface (CLI), where users can run programs, execute scripts, manage files.
The first command I ran for this project is "ipconfig /all" to get a list of my local IP. I made sure that my EC2 IP is the same as my local IP. This will be important when I connect to the server. Alternatively, you can also paste your public IPv4 DNS into a web browser and see if connection is established. If connection is not established, this could be due to limitation of the security group rules on the instance. Troubleshoot this by ensures that the SG allows traffic from your IP to the EC2 instance or ELB if your EC2 is fronted by an ALB.
I also updated my private key's permissions by running the following commands from my terminal. 
icacls "nextwork-keypair (1).pem" /reset – Resets all permission on the key file.
icacls "nextwork-keypair (1).pem" /grant:r "$($env:username):R" – Grants read access to the username running the terminal. This can also be set to “RW” permission. However, for this case “R” works just fine.
icacls "nextwork-keypair.pem" /inheritance:r – Removes any inherited permission on the key file.

![image](https://github.com/user-attachments/assets/29c8444e-68b8-442d-b44b-1142d69ff18b)

SSH connection to EC2 instance
To connect to my EC2 instance, I ran the command " ssh -i [path to key pair] ec2-user@[Public IPv4 address]. This command required an IPv4 address A public IPv4 DNS is a publicly accessible domain name system (DNS) address that maps domain names to IPv4 addresses. My local machine will connect to my EC2 instance through this IP.
Public IPv4 DNS

![image](https://github.com/user-attachments/assets/9605d5fe-f03c-4357-8b76-d7224f104459)

Successful SSH connection from terminal
 
![image](https://github.com/user-attachments/assets/c0ae6366-5b7a-42ce-a925-1e0aa637db4e)
 
Creating the application
Install Maven and Java on the server
Apache Maven is a build automation tool used for Java projects. It simplifies the process of managing project dependencies, compiling code, running tests, and packaging applications. Maven uses a file called pom.xml to define project structure. Maven is required in this project because it automates the project build. Java is a high-level, OOP language designed to be platform-independent, meaning code written in Java can run on any system with a JVM. It is widely used for building web applications. Java is required in this project because I am building a versatile webapp that will handle sensitive client data and Java's built-in security features make it a reliable choice for handling sensitive data in web applications.
Run the following command to install Apache Maven:
wget https://archive.apache.org/dist/maven/maven-3/3.5.2/binaries/apache-maven-3.5.2-bin.tar.gz
sudo tar -xzf apache-maven-3.5.2-bin.tar.gz -C /opt
echo "export PATH=/opt/apache-maven-3.5.2/bin:$PATH" >> ~/.bashrc
source ~/.bashrc
Maven installation

![image](https://github.com/user-attachments/assets/f404f3a4-7787-4386-a646-b1cc2bd60626)
 
Run the following command to install Amazon Coretto 8 (a java module):
sudo dnf install -y java-1.8.0-amazon-corretto-devel
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-amazon-corretto.x86_64
export PATH=/usr/lib/jvm/java-1.8.0-amazon-corretto.x86_64/jre/bin/:$PATH
 
Java installation

![image](https://github.com/user-attachments/assets/e23060a6-904a-49e9-9b82-6c418c2ca2d7)
 
I ran the following command to ensure that indeed Maven and Coretto 8 were successfully installed.
mvn -v
java -version
I generated a Java web app using the command: mvn archetype:generate \ - DgroupId=com.nextwork.app \ -DartifactId=nextwork-web-project \ - DarchetypeArtifactId=maven-archetype-webapp \ -DinteractiveMode=false

![image](https://github.com/user-attachments/assets/d164db2a-761f-4358-8519-7d3dbcd66df0)
 
Connecting VSCode with EC2

![image](https://github.com/user-attachments/assets/4ff35621-100f-40e6-bf2f-2d3fa5da20f3)

I installed the Remote - SSH extension to get the full IDE capabilities of VSCode in my EC2 instance. I will use this IDE to edit my java web app file. It is easier that using command line editors. Configuration details required to set up a remote connection include: 1. Installing the Remote - SSH extension. 2. Connecting to the SSH host, similar to “SSHing” into your EC2. Run the code ssh -i[path to keypair] ec2-user@[Public IPv4 name]
 
Two of the project folders created by Maven are src and webapp, which contained the source code files and webapp files in html respectively.

![image](https://github.com/user-attachments/assets/74331515-4002-4262-a028-c7226365bc49)
 
This project took me...
This project took me approximately 1 hour to setup the infrastructure. Writing the actual build takes time but this had already been done before hand. Documenting the infra part of the project also took me about 1 hour. So, 2 hours give or take.



