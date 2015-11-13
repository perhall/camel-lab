# External Resources

Thanks to Christina Lin for the example code at [homeloan-part1](https://github.com/weimeilin79/homeloan-part1) which is being used for this lab.

# Important Note

> Make sure to have the workspace of the JBoss Developer Studio set to your *VAGRANT_HOME*, i.e. where the *Vagrantfile* of your vagrant instance is located. 
> 
> Thus the workspace will be automatically shared into the vagrant instance.  

# PART--1 PreReq - 
flik JBoss Central - tab Software/Update - kryssa i Enable Early Access - installera *JBoss Fuse Development*

Låt starta om.
Välj Perspektive - others - Fuse Integration

# PART-0 Initial Project Setup

## Github Starting Tag

Step *PART-0* can be skipped by means of checking out

	https://github.com/lekkehart/camel-lab/releases/tag/PART-1-0-BEFORE

## Create Empty Fuse Project in Developer Studio

### Create

- File > New > Fuse Project
	- name > demo-1
	- archetype > camel-archetype-blueprint

### Remove Starter Code

- Remove
	- blueprint.xml > remove route contents & bean
	- Hello.java
	- HelloBean.java
	- RouteTest.java
- Run *mvn clean install*

# PART-1 Add Route with Content-Based-Router

## Github Starting Tag

	https://github.com/lekkehart/camel-lab/releases/tag/PART-1-0-BEFORE

## Add Route "part1Route"

### Add
File Pos: lab-1/src/main/resources/OSGI-INF/blueprint/blueprint.xml
Components ska hittas i det grafiska läget under perspektivet Fuse Integration - högerklick-ment

- Rename route > Id > "whatever"  to "part1Route"
- right click > add *Components* > add *File*
	- Check out the embedded *documentation* tab.    mark the component, see the Documentation tab under Property window below
	- *Uri* > file:test/in?include=.*xml		set this value under Properties/Generic  
- *Routing* > add *Choice*				right-hand pop-in menu
- *Routing* > add *When*
	- Check out structure of *homeloancust.xml* in order to set the proper expression 	in lab-1/Z-PRE-PREQUISITES/xmls/ ...
	- *Language* > xpath						\ we can get LoanCustomer or HouseInfo files from that directory above
	- *Expression* > /CustInfo[@infotype='LoanCustomer'] 		
- *Routing* > add *Otherwise*
- *Components* > add *Log*
	- *Message* > "Got Customer file with name ${file:name}"
- Draw arrows between the chains - the Log above is after the WHEN-box, make another after the OTHERWISE-box
- Select filetree : project "lab-1" rightclick > *Run As* > mvn clean install
- (( right-click blueprint.xml > Run As > 2. Local Camel Context is the same cannot be done before the above has built the project ))

### Test

- Select project > *Run As* > mvn camel:run	projectname - rightclick - other - Maven Build - Goal= "camel:run" + Name it +´Base Dir=lab-1 + Apply + Run
- Copy files from /lab-one/Z-PRE-PREQUISITES/xmls to /lab-one/test/in 
	- logs should show that route consumes files


# MELLANSPEL: Virtuella maskinen, starta JBOSS

Har inte jboss packats upp vid uinstallation, unzippa jboss...zip på lämplig plats, starta med bin/fuse
http://localhost:18181/hawtio/login	- id r2m/r2m

# PART-2 Add JAXB Unmarshaller

## Github Starting Tag

	https://github.com/lekkehart/camel-lab/releases/tag/PART-2-0-BEFORE

## Generate POJOs from Schema Files

	xjc -d src/main/java -p org.r2m.lab.model Z-PRE-PREQUISITES/schema/HouseInfo.xsd Z-PRE-PREQUISITES/schema/CustInfo.xsd

## Add JAXB Dependency

Add to pom.xml:

	<dependency>
      <groupId>org.apache.camel</groupId>
      <artifactId>camel-jaxb</artifactId>
      <version>2.15.1.redhat-620133</version>
    </dependency>
	
## Unmarshall XML into POJO

### Unmarshall in the *when* Branch

- *Transformation* > add *Unmarshall*
	- select *jaxb*
	- *Context Path* > org.r2m.lab.model
	- *Part Class* > org.r2m.lab.model.CustInfo
	- *Pretty Print* > Y
- *Components* > add *Log*
	- *Message* > "Customer ID is ${body.nationalID}"

### Unmarshall in the *otherwise* Branch

- Repeat the similiar steps like in the *when* branch

### Test

# PART-3 Add AppraisalProcessor Bean

## Github Starting Tag

	https://github.com/lekkehart/camel-lab/releases/tag/PART-3-0-BEFORE

## Add AppraisalProcessor POJO

	package org.r2m.lab.processor;

	import org.r2m.lab.model.HouseInfo;
	
	public class AppraisalProcessor {
	
		public HouseInfo quote(HouseInfo houseInfo) {
	
			int appraisedValue = 0;
			appraisedValue += houseInfo.getLandSize() * 1000;
			appraisedValue += houseInfo.getBathroom() * 2000;
			appraisedValue += houseInfo.getBedroom() * 2500;
	
			houseInfo.setAppraisedValue(appraisedValue);
	
			return houseInfo;
		}
	}

## Add *bean* to *otherwise* branch

- *Components* > add *bean*
	- *Bean Type* > org.r2m.lab.processor.AppraisalProcessor
	- *Method* > quote
- *Components* > add *log*
	- *Message* > "House ID is ${body.nationalID} with appraisedValue ${body.appraisedValue}"

## Test

# Further Labs

Christina Lin has published a number of youtube demos & blogs that are very helpful in trying out Camel functionality, see:

- [Home Loan Demo Part 1](https://www.youtube.com/watch?v=JMaYhxfup2M)
- ...
- [Home Loan Demo Part 7](https://www.youtube.com/watch?v=IFv5HbwUhpU)
