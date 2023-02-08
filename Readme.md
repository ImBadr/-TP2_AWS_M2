# TP AWS : VPCs - Peer Connection - CloudWatch

Authors : Badr TADJER | Adrien PLOT

## Introduction

VPCs - Peer Connection - CloudWatch (Logs)

## Part 1 : Lab Connection

- Region : eu-west-1 and eu-west-2
- Group : Lab_Student_3

## Part 2 : Introduction

### Architecture

![Architecture](./assets/part1/1_architecture.png)

### 2.1 : Create a VPC_1

- name : ``my-vpc1-ab = 10.7.0.0/16``
- Region ``eu-west-2``

![VPC_Creation](./assets/part1/2_create_vpc.png)

Subnets :

```txt
- my-private-subnet-vpc1-ab = 10.7.1.0/24
- my-public-subnet-vpc1-ab = 10.7.2.0/24
```

![Subnet_Creation](./assets/part1/3_create_subnet.png)

Internet Gateway : ``my-internet-gateway-vpc1-ab``

![Gateway_Creation](./assets/part1/4_create_igw.png)

Route Table :

```txt
- my-route-table-private-subnet-vpc1-ab
- my-route-table-public-subnet-vpc1-ab
```

![Route_Table_Creation](./assets/part1/5_create_route_table.png)

Internet Connection :

![Internet_Connection](./assets/part1/6_internet_connection.png)

### 2.2 : Create a VPC_2

name : ``my-vpc2-ab = 10.8.0.0/16``

![VPC_Creation](./assets/part1/7_create_vpc_2.png)

Subnets :

```txt
- my-private-subnet-vpc2-ab = 10.8.1.0/24
```

![Subnet_Creation](./assets/part1/8_create_subnet_2.png)

Route Table :

```txt
- my-route-table-private-subnet-vpc2-ab
```

![Route_Table_Creation](./assets/part1/9_create_route_table_2.png)

### 2.3 : Create a Instances

Instance creation :

```txt
- AB_VM1_VPC_01
- AB_VM2_VPC_01
- AB_VM3_VPC_02
```

![Instance_Creation](./assets/part1/10_create_instances.png)

### 2.4 : Ping test

AB_VM1_VPC_01 ssh connection :

![VM1_SSH_Connection](./assets/part1/11_connecte_VM1.png)

Ping ``AB_VM2_VPC_01``

![VM1_Ping_VM2](./assets/part1/12_ping_VM2.png)

Create Peer Connection :

![Peer_Connection](./assets/part1/13_create_peer_connection.png)

Ping ``AB_VM3_VPC_02``

![VM1_Ping_VM3](./assets/part1/14_ping_VM3.png)

## Part 3 : Create VPC on different Regions

### AWS Architecture

![Architecture](./assets/part2/1_architecture.png)

### 3.1 : Create a VPC_3

- Name : ``my-vpc3-ab = 10.9.0.0/16``
- Region : ``eu-west-1``

![VPC_Creation](./assets/part2/2_create_vpc.png)

Subnet :

```txt
my-public-subnet-vpc1-ab = 10.9.1.0/24
```

![Subnet_Creation](./assets/part2/3_create_subnet.png)

Internet Gateway : ``my-internet-gateway-vpc3``

![Gateway_Creation](./assets/part2/4_create_internet_gateway.png)

Route Table :

```txt
my-route-table-vpc3-ab
```

![Route_Table_Creation](./assets/part2/5_create_route_table.png)

Instance creation : ``AB_VM4_VPC_03``

![Instance_Creation](./assets/part2/6_create_instance.png)

### 3.2 : Create a Peer Connection VPC_1 & VPC_3

![Peer_Connection](./assets/part2/7_create_peer_connection.png)

### 3.3 : Ping test

Ping ``AB_VM1_VPC_01`` to ``AB_VM4_VPC_03``

![Ping_VM1_VM4](./assets/part2/8_ping_VM4.png)

## Part 4 : CloudWatch

### 4.1 : Configure log flow

Create policie

![Policie_Creation](./assets/part3/1_create_policie.png)

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents",
                "logs:DescribeLogGroups",
                "logs:DescribeLogStreams"
            ],
            "Effect": "Allow",
            "Resource": "*"
        }
    ]
}
```

Create role

![Role_Creation](./assets/part3/2_create_role.png)

```json
{
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Principal": {
           "Service": "vpc-flow-logs.amazonaws.com"
        },
        "Action": "sts:AssumeRole"
      }
    ]
}
```

Create log group

![Log_Group_Creation](./assets/part3/3_create_log_group.png)

Create logs flow

![Log_Stream_Creation](./assets/part3/4_create_flow_logs.png)

### 4.2 : Create a web server and get logs

Create a web server page

![Web_Server_Creation](./assets/part3/5_create_web_server.png)

Log request 1

```request
stats sum(packets) as packetsTransferred 
by srcAddr, dstAddr 
| sort packetsTransferred desc 
| limit 10
```

Result :

![Log_Request_1](./assets/part3/6_request_1.png)

Log request 2

```request
fields @timestamp, @message 
| stats count(*) as records by dstPort, srcAddr, dstAddr as Destination 
| filter interfaceId="eni-051f20d7aa3334d64" 
| filter dstPort="80" or dstPort="443" or dstPort="22" or dstPort="25" 
| sort HitCount desc 
| limit 10
```

Result :

![Log_Request_2](./assets/part3/7_request_2.png)
