## Step-by-Step Guide to Set Up Three-Tier Architecture Manually After CloudFormation Deployment

**Step 1: Get Necessary Details from CloudFormation**

Go to AWS CloudFormation Console → Select Your Stack.

Check the "Outputs" tab:

Copy the Load Balancer DNS Name (e.g., http://my-alb-123456789.ap-southeast-2.elb.amazonaws.com).

Note down the EC2 Instance IDs (WebServer & AppServer).

Note the RDS Endpoint.

Step 2: Connect to EC2 Instances via SSH

Connect to WebServer (Nginx):
Find the Public IP of the WebServer:

Go to AWS EC2 → Instances → WebServer.

Copy the Public IPv4 Address.

SSH into the WebServer:
```
ssh -i my-key.pem ec2-user@<WebServer_Public_IP>
```
Replace my-key.pem with your key pair file.

Replace <WebServer_Public_IP> with the actual IP.

**Step 3: Install and Configure Nginx on WebServer**

Update the System and Install Nginx:
```
sudo yum update -y
sudo yum install -y nginx
```
Start and Enable Nginx:
```
sudo systemctl start nginx
sudo systemctl enable nginx
```
Configure Nginx as Reverse Proxy:

1. Open the Nginx configuration file:
```
sudo nano /etc/nginx/nginx.conf
```
2. Find the server {} block and modify it to forward requests to the AppServer:
```
server {
    listen 80;
    server_name _;

    location / {
        proxy_pass http://<AppServer_Private_IP>:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```
Replace <AppServer_Private_IP> with the actual AppServer private IP.

Restart Nginx:
```
sudo systemctl restart nginx
```

**Connect to AppServer (Flask):**

First, SSH into the WebServer

From WebServer, SSH into the AppServer* (since it’s in a private subnet):*
(ensure you have ssh key in webserver with 400 permision)
```
ssh keyfilename.pem ec2-user@<AppServer_Private_IP>
```
**Step 4: Install and Run Flask Application on AppServer**

Install Python and Flask:
```
sudo yum update -y
sudo yum install -y python3 python3-pip
pip3 install flask pymysql
```
Create a Flask App:
```
mkdir ~/flask_app && cd ~/flask_app
nano app.py
```
Paste this Python Flask code inside app.py:
```
from flask import Flask
import pymysql

app = Flask(__name__)

@app.route('/')
def home():
    return "Flask App Running!"

@app.route('/db')
def db_test():
    connection = pymysql.connect(
        host='YOUR_RDS_ENDPOINT',
        user='admin',
        password='YourPassword',
        database='YourDatabase'
    )
    cursor = connection.cursor()
    cursor.execute("SELECT 'Connected to RDS!'")
    result = cursor.fetchone()
    connection.close()
    return str(result)

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```
Replace YOUR_RDS_ENDPOINT with your actual RDS endpoint from CloudFormation Outputs.

Replace YourPassword and YourDatabase with your database credentials.
**Run the Flask App:**
python3 app.py
The Flask app should now be running on port 5000.

**Step 6: Test the Application**

Open your ALB DNS Name in a browser:

http://my-alb-123456789.ap-southeast-2.elb.amazonaws.com

If everything is configured correctly, you should see:

Flask App Running!


