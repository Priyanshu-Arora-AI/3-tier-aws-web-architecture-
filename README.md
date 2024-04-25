## Pre-requisites:
1. An AWS account. If you don’t have an AWS account, follow the instructions [here](https://aws.amazon.com/console/) and
click on “Create an AWS Account” button in the top right corner to create one.
1. IDE or text editor of your choice.

## Architecture Overview
![Alt text](https://github.com/Priyanshu-Arora-AI/3-tier-aws-web-architecture-/blob/main/application-code/web-tier/src/assets/3TierArch.png)



![Architecture Diagram]
In this architecture, a public-facing Application Load Balancer forwards client traffic to our web tier EC2 instances. The web tier is running Nginx webservers that are configured to serve a React.js website and redirects our API calls to the application tier’s internal facing load balancer. The internal facing load balancer then forwards that traffic to the application tier, which is written in Node.js. The application tier manipulates data in an Aurora MySQL multi-AZ database and returns it to our web tier. Load balancing, health checks 






#setup guides
# --> 1. create vpc
# --> 2. create 6 subnet in vpc [ public - 2  for instance A & B] [ private - 2 for instance C & D ] [[ private - 2 for RDS ]
# --> 3. create internet gateway
# --> 4. connect internet gateway to vpc , 
# --> 5. create route table and set route to igw , associate public subnet [for instance a and b]
# --> 6. create nat gateway , allocate public ip , lauch in public subnet 
# --> 7. create route table and set route to netgateway , associate private subnet subnet [for instance c and d]
# --> 8. create instance a and b in public subnet [each in one means instance a in public subnet 1 and b in public subnet 2 ]
# --> 9. run setup command in both instance that are mention below   
# --> 10. create load balancer type internet and route taffic to public instance [ l.b listning port 80] [target port 80 ]
# --> 11. create instance c and d in private  subnet [each in one means instance a in private subnet 1 and b in private subnet 2 ]
# --> 12. run setup command in both instance that are mention below
# --> 13. create load balancer type internal facing and route taffic to private instance [ l.b listning port 80] [target port 4000 ]
# --> 14. create database in private subnet and create read replica 







steps to implement aws project 3 tier
	
{	
# 	create vpc 
# 		1.vpc - name 
# 		2.label name - myvpc example
# 		3. ip [give desired cidr]
# 			example : 10.0.0.0/16
# 	create subnet
#    		public subnet
# 		#create six subnet and assign cidr for subnets
# 			1. name - public subnet1 for ec2
# 				ip 10.0.1.0/24 --> subnet cidr example

# 			2. name public subnet2 for ec2
# 				ip 10.0.2.0/24 --> subnet cidr example

# :			3. private subnet1 for ec2 
# 				ip 10.0.3.0/24  --> subnet cidr example  

# 			4. private subnet2 for ec2
# 				ip 10.0.4.0/24  --> subnet cidr example 

# 			5. private subnet1 for database
# 				ip 10.0.5.0/24  --> subnet cidr example

# 			6. private subnet2 for database
# 				ip 10.0.6.0/24  --> subnet cidr example
	
#     create internet gateay 
# 			give name to internet gateway example -> myingateway
# 			attach to myvpc
    
#     create route table for internet gateway
# 			give name to route table1 example -> myroute
# 			2. attach to myvpc
# 			3. associate subnet
# 				public subnet1 for ec2
# 				public subnet2 for ec2
# 			4. edit routes
# 				associtae 0.0.0.0/0 to internet gateway
	

# 	create netgateway
# 		name - give name to netgateway example -> mynetgateway
# 		attach public subnet1 for ec2 
# 		connectivity type - public
# 		allocate public ip
	
# 	create route table for netgateway
# 		name private_subnet
# 		edit routes
# 		associtae 0.0.0.0/0 to net gateway
# 		subnet association 
# 			private subnet1 for ec2 
# 			private subnet2 for ec2
}
 

load balancer setup
{ #create load balancer internet  {
	# 	name assign name to it example -> mylb
	# 	connectivity type internet
	# 	select subnet where lauch load balancer
	# 	select target for l.b								
	# 			select target type - instance
	# 			protocol 80 [webserver port 80 for http and for https 440 ]
	# 			select pvc {myvpc}
	# 			select protocol http
	# 			register target `								
	# 				register instance
		
	# 	select httpd protocol and listning port 80 in which l.b. handle request
	# 	select security group
	# 	}
	# create load balancer internel facing {
	# 	name assign name example->mypvtlb
	# 	connectivity internal-facing
	# 	select subnet where lauch load balancer
	# 	select target for l.b								
	# 			select target type - instance
	# 			protocol  port_number  give port number in which api listning it in my case --> 4000 {node js port}
	# 			select myvpc
	# 			select protocol http
	# 			register target `								
	# 				register instance
		
	# 	select httpd protocol and listning port 80 in which l.b. handle request
	# 	select security group
	# 	}
}


 

ec2 setup 

# 	setup ec2 to your requirement 
# 	for example :
# 		in public subnet1
# 					create instance
# 						ami aws
# 						name web tier instance 1
# 						key select key
# 						instance type t2.micro


# 					set security group 

# 					storage 8gib	
# 					network vpc : myvpc
# 					ubnet : public subnet1 for ec2
# 					allocate public ip

# 		in public subnet2
# 					create instance
# 						ami aws
# 						name web tier instance 2
# 						key select key
# 						instance type t2.micro
	
# 						security group 
# 							setup security group
# 						storage 8gib	
# 						network vpc : myvpc
# 						subnet : public subnet2 for ec2
# 						allocate public ip enable
	
# 		ec2 setup in private subnet lauch two instance
    
# 					create instance
# 						ami aws
# 						name app tier 1
# 						key select key
# 						instance type t2.micro
# 						security group 
# 							setup security group

# 					storage 8gib	
# 					network vpc : myvpc
# 					subnet : private subnet1 for ec2
# 					allocate public ip disable  }

	

    
setup command for ec2 public instance
		web tier
	
			sudo -i
			sudo yum update
			sudo yum install nginx -y
			sudo systemctl enable  nginx --now 
			sudo yum install nodejs npm -y
			yum install -y git
			git clone https://github.com/Priyanshu-Arora-AI/aws-three-tier-web-architecture
   
	 		mv aws-three-tier-web-architecture-workshop/application-code/web-tier .
			cp /root/aws-three-tier-web-architecture-workshop/application-code/nginx.conf /etc/nginx/
			rm -rf aws-three-tier-web-architecture-workshop
			mv web-tier /usr/share/nginx
			cd /usr/share/nginx
			cd web-tier
			npm install
			npm start
			npm run build
			cd /etc/nginx
	
			vim nginx.cong {}							
				-> edit #react app and front end files
        		location / {
        			root    /usr/share/nginx/web-tier/build;
        			index index.html index.htm
        			try_files $uri /index.html; }
        			systemctl restart nginx
	
	

database setup
	#   go to aws rds
	# 	select db
	# 	database name db1
	# 	give master username
	# 	give master password 
	
lauch ubuntu in aws than connect to it mysql 
	steps 
		ubuntu 	
			conf same as upper public instance
		cmd
			 sudo apt update
  			 sudo apt install mysql-client 
			 mysql -h database-1.cxiu8ua0yny3.ap-south-1.rds.amazonaws.com -u admin -p12345678 -e "CREATE DATABASE db;"
			 mysql -h database-1.cxiu8ua0yny3.ap-south-1.rds.amazonaws.com -u admin -p12345678 -e "SHOW DATABASES;"		
			 mysql -h database-1.cxiu8ua0yny3.ap-south-1.rds.amazonaws.com -u admin  -p12345678 -e "USE db;"
			 mysql -h database-1.cxiu8ua0yny3.ap-south-1.rds.amazonaws.com -u admin  -p12345678 -e "CREATE TABLE transactions (
					    id INT AUTO_INCREMENT PRIMARY KEY,
    						amount DECIMAL(10, 2),
    					description VARCHAR(255)
						);	 
						"
			OR USE 
				mysql -h database-1.cxiu8ua0yny3.ap-south-1.rds.amazonaws.com -u admin 
                        -p12345678 db
				to enter into db and run query


setup private instance
{
now again go to public instance 
	copy NEW.pem key to instance
	chmod 400 key

setup command for private instance
	through public instance
ssh ec2-user@ip [of private instance] -i key
		sudo -i
		yum install git -y
		git clone https://github.com/Priyanshu-Arora-AI/aws-three-tier-web-architecture
		curl -fsSL https://rpm.nodesource.com/setup_16.x | sudo -E bash -
		sudo yum install -y nodejs
		cd aws-three-tier-web-architecture-workshop/
		cd application-code/
		mv app-tier/ ../../
		cd
		rm -rf aws-three-tier-web-architecture-workshop
		cd app-tier/
		npm install
		vim app-tier/Db.conf
		enter db-> endpoint , user , password , db-nsame[that we created from query]  
		npm install pm2 -g
		cd /path/to/your/nodejs/app
		pm2 start index.js
		pm2 list
		troubleshoot mysql connected or not
			curl http://localhost:4000/health
			curl -X POST -H "Content-Type: application/json" -d '{"amount": 100, "desc": "Test transaction"}' http://localhost:4000/transaction
			curl http://localhost:4000/transaction


}	


	

troubleshoot
	 pm2 logs
		

don't run { it stop nodejs}
	pm2 stop all
	pm2 delete all
						 		
	

	

