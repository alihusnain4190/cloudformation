
What is Parameters
	Parameter is a way to provide input to your AWS cloudforamtion template
		Some input can not be determine ahead of time,
		You want to re-use template 
		
		
When to use parameter ?
	Ask yourself this question
		Is cloudformation resources configration change in future ?
		Then make is paramter
		
		
How to reference a paramter ?
	The Fn:Ref function can be leveraged to refernec paramter 
	Paramter can be use anywhere is template
	Except 
		AWSTEMPLATEvERSION
		Description
		Transform
		Mapping
		
		
		
		
Optional Attribute For resources
	DependOn
		Very usefull to draw a dependency between two resources
		For example, Create ECS cluster after creating ASG
	DeletionPolicy
		Protect resources being delete if you delete Stack
	UpdateReplacePolicy
		Protect resource being replace during cloudformation replace
	CreatePolicy
		
	UpdatePolicy
	
	Metadata
		
		
		
		
Mapping 
	Mapping is fixed varibale , it differentiate between enviourment like dev, prod etc
	We should use mapping when we know in advance all value like AZ, ami etc
	
	
AccessMapping (Fn::FindInMap)
	
	
	
	
Output
	We can import output value into other stack
	You can view the output in AWS CLI or AWS console
	


#### Condition:
	Condition are use to control the creating of resource or output base on Condition
	Most common area to use condition is 
		1 Enviourmet (dev, test, prod)
		2 AWS region
		3 Any parameter value
	The intrint function we can use 
	 * Fn::And
	 * 	Fn::Equal
	 *	Fn::If
	 *	Fn::Not
	 *	Fn::Or

	Fn:GetAtt 
		* attribute that attach to any resources you create


#### Cloudformation rules
	Rule we use to perform parameter validation base on value of other parameter
	* For example ensure all subnet select within the same VPC

	 How to define Rule condition
		Each rule consist of 
			1 Rule condition (optional): when rule take effect/ assertions applied on rule
			2 Assertion: What value are allowed for perticuler parameter. Can contain one or more assert

	If you do not define rule condition , the rule assert will take effect with every create and update operation

	Rule Instrint function
		1 Fn::And
		2 Fn::Contain
		3 Fn::EachMemberEqual
		4 Fn::EachMemberln
		5 Fn::Equal
		6 Fn::Not
		7 Fn::If
		8 Fn::Or
		9 Fn::RefAll
		10 Fn::ValueOf
		11 Fn::ValueOfAll 



##### User Data
	If you want to install any script before provision ec2 that time we use user data

#### Cloudformation init
	The Problem with User Data
		What if you have very larg instance configration?
		What if we want to evolve the state of Ec2 instnace without terminate it and creating new one?
		How do we make a user data more readable?
		How do we know our script is successfull?

	Enter the cloudformation helper script

		we have 4 python script , that come directly with amazon-linux2 AMI or can be install using yum 
			1 cfn-init : Use to retrive and interept the meta-data , install packages, create file and start service
			2 cfn-singal: A simple wrapper to signal with CreatePolicy or waitCondition, enable synchroniza with other services in stack with application being ready
			3 cfn-get-metadata: A wrapper script make it easy to retrive all meta data define for a resource path or path to spacific key or subtree of resource data
			4 cfn-hup: A demon to check update to the metadata and execute custom hoook when change are detected
		Usuall flow is cfn-int then cfn-singal and cfn-hub

	AWS Cloudformation init (1/2)
		A config contain following and execute in order
		  1 Package: use to download and install pre-package app and component on Linux (MySQL, php )
		  2 Group : define user group
		  3 Users: define user, which group they belong to 
		  4 Sources: download file and archive them and place them into Ec2 instnace
		  5 File: create file on Ec2 instance , using inline and can be pull from URL
		  6 Command : run a series of command
		  7 Service: launch a sysvinit 

	AWS Cloudformation init (2/2)
		You can have a multiple config 	in your AWS::Cloudformation::Init
		You create a Config Set with multiple configs
		And invoke multiple config from user-data 


###### Packages
	you can install package from following repository, python, rpm, apt,msi and yum
	Package can process in following order rpm, apt/yum and then python/ rubygems
	You can also spacify version or no version
##### Group and User
	if you want to add multiple user, group in Ec2 

###### Source
	you can download whole archive compressed from web.
	It is very good if you have set of resource store in s3 or download whole project into Ec2 from Github
	It support tar, gzip, zip 

#### File




##### Nested Stack
	Nested stack is a stack as a part of other stack
	They allow you to isolate repeated pattren in sperate stack and call them from other stack
		Example:
			load balamcer configure and reuse 
			security group configure and resuse
	To update nested stack always udpate parrent stack


#### StackSet 
		#### Overview
			Create, update , delete stack across multiple account and region with single operation
			When you update the stackset all associate stack instance are update throughout all 
			account and region
			Can be applied to all accounts from organization

		Stack set Operation
			1 Create StackSet
					1 Provide template , target account/ region
			2 Update StackSet
					1 Update always effect all the stack 
			3 Delete Stack
					1 Delete stack and source from target account
					2 Delete stack from stack Set
					3 Delete all stack from stack set
			4 Delete StackSet
					Must delete all stack instance within stackset to delete it 

		StackSet Deployment Option
			Deployment Order 
				Order of region where stack deployed
				Operation perform one region at time
			Maximum concurent Accounts
				Max.number / percentage of target account per region to which you can deploy stack one time
			FailureTolerance 
				Max number / percentage of stack operation failure that can occur before cloudformation stop operation
			Region concurency
				Whether stackset applied sequence or parallel
			Retain stack
				Used when delete the stackSet to keep stack and their resource running when moved from stackset


#### Permession Model for stackSet
	Self Managed permession
		Create IAM role for both administrator and target account 
	Service Managed permession
		Deploy to account that managed by AWS Organization
		StackSet Create IAM role on your behalf 
		Must enable all feature in AWS Organization
		