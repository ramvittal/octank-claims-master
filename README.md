# Octank Claims Master

This is a prototype for migrating on-prem claims app to AWS.  This repo provides information on how to setup the environment 
for migration. 

## Setup Instructions

### Setup baseline AWS resources 

1. Run oracle2aurorastack.json CFN template to create AWS resources
2. Example inputs for CFN template:
     VPC cidr block: 20.0.0.0/16  
     Subnet 1 : 20.0.0.0/24  
     Subnet 2 : 20.0.1.0/24  
     MyIp: 105.251.233.0/24  
     Aurora creds: auradmin/auradmin123

### Setup and Launch Oracle DB
1. Connect to ec2 oracle linux instance using ec2-user
2. Change to root user: su root
3. Change directory: cd /u01/script
4. In oracleipchg.sh Replace IP address with your ec2 oracle linux private IP and run it as ./oracleipchg.sh
5. Switch to oracle user - su oracle.  And restart oracle ./oraclerestart.sh
6. Create oracle sequence tables CLAIM_SEQ, PATIENT_SEQ in oracle CLAIMS db


### Setup and Launch DMS

1.	Create Oracle source endpoint 
        servername=<ec2 private ip>
        Port=1521
        SID=orcl
        username=claims
        password=claims123
        useLogminerReader=N on Advanced extra connection attributes
2.	Create rds aurora target endpoint. Use all defaults
3.	Create task using source and target endpoints. 
        Migration type = migrate existing and ongoing changes
        Schema = CLAIMS




## Authors

* **Ram Vittal** - *Initial work* - [RamVittal](https://github.com/ramvittal)

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Acknowledgments
