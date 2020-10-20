# PriceAlert

### For Dev
![PriceAlert](/Dev.jpg)

### For Dev_Scalable
![PriceAlert](/DevScalable.jpg)

### For Serverless
![PriceAlert](/Serverless.jpg)

## Brain
* Brain connects to database 
   *  database having below information:
```js
// below are the tables in Database.
Trains:[// this is the table of list of trains
    {
        trainDescription:'',
        trainId:'' // primary key 
    }
]
```
* here is SQL code for above mentioned Trains table
```sql
CREATE TABLE Trains
(trainDescription varchar(200) NOT NULL,
trainId int IDENTITY(1,1) PRIMARY KEY)
```

```js
TrainsRoute:[// this is the table which has the itinerary deatils and price
    {
        TrainsRouteId:'',// composite key of TrainsRouteID and price
        trainId:'', // foreign key from Train table
        origin:'',
        destination:'',
        timeStampPrice:'',
        price:''// composite key of TrainsRouteID and price
    }
]
```
* here is SQL code for above mentioned TrainsRoute table
```sql
CREATE TABLE TrainsRoute
(TrainsRouteId int IDENTITY(1,1),
 trainId int FOREIGN KEY REFERENCES Trains(trainId), 
 origin varchar(200) NOT NULL,
 destination varchar(200) NOT NULL,
 timeStampPrice date default CURRENT_TIMESTAMP,
 price float(10,2) NOT NULL,
 CONSTRAINT TrainsRouteIdPrice PRIMARY KEY (TrainsRouteId, price)
 )

```
```js
Users:[
    {
        userid:'', //primary key
        userName:'', 
        MobileNumber:'',
        emailId:''
    },
    {
        userid:'', //primary key
        userName:'', 
        MobileNumber:'',
        emailId:''
    }
]
```
* here is SQL code for above mentioned Users table

```sql
CREATE TABLE Users
 (userid int IDENTITY(1,1) PRIMARY KEY,
 userName varchar(200) NOT NULL,
 MobileNumber varchar(15), 
 emailId varchar(200) NOT NULL
 )
```

```js
UsersRequests:[
    {
        RequestId:'',// primary key
        UserId:'', // foreign key from User table
        UsersMaxPrice:'',
        TrainsRouteId:'', // foreign key from TrainsRoute
        TrainsRouteIdPrice:'', // foreign key from TrainsRoute
        RequestDateTime:'',
        GetPercentage:''
    },
    {
        RequestId:'',// primary key
        UserId:'', // foreign key from User table
        UsersMaxPrice:'',
        TrainsRouteId:'', // foreign key from TrainsRoute
        TrainsRouteIdPrice:'', // foreign key from TrainsRoute
        RequestDateTime:'',
        GetPercentage:''
    }
]
```
* here is SQL code for above mentioned UsersRequests table
```sql

 CREATE TABLE UsersRequests 
 (RequestId int IDENTITY(1,1) PRIMARY KEY,
  UserId int FOREIGN KEY REFERENCES Users(UserId),
  UsersMaxPrice numeric(5,2),
  TrainsRouteId int ,
  TrainsRouteIdPrice NUMERIC(10,2),
  RequestDateTime date default CURRENT_TIMESTAMP, 
  GetPercentage NUMERIC(3,2) ,
  constraint FKTrainsrouteRequest Foreign KEY(TrainsRouteId,TrainsRouteIdPrice) references TrainsRoute(TrainsRouteId,Price)
  )

```

* form of each message is below
```js
Brain to Alert:
{
  name: SEND_ALERT
  data: [{
     UserId:123,
     UserName:xx,
     emailId:xxxx@x.com
  }] // this the userlist we have many users emails
}

Alert to Brain:
success
{
  name:ALERT_SEND_SUCCESSFUL
}
failure
{
  name:ALERT_SEND_FAIL,
  reason: 'API Limit exceeded'
}

Brain to Prices:
{
  name: SEND_ALERT,
  data: [{
    TrainsRouteId:123,
    UsersMaxPrice:25
  }]
}

Prices to Brain:
success
{
name:GET_PRICES_SUCCESSFUL
data:[
  TrainsRouteId:123,
  price: 50,
  GetPercentage:12
]
}

failure
{
name:GET_PRICES_FAIL,
reason: 'API Limit exceeded' or 'no details available'
}
```
* Brain ask Prices_node when to check the price
* Brain ask Alert_node to inform customers


## Prices_node
It hosts hosts the functions to check prices: checkPrice()

* Input: list of Trains
    for every train in the list
      check the itinerary details (train id, origin, destination) and crawl/scrape for the price or connect to an API to check the current price.

* Output: 
    Notify brain about updated price success/failure and changed price details, percentage change

## Alert_node
It has the code send emails: sendEmail()

* Input: list of users
    for every user in the list
      send an email using sendEmail()

* output: Notify brain about sendEmail success/failure

## Notes: 
1. Where is the price details that needs to be delivered to users ?


## Next_Steps:
 VPC , subnets and securitypolicies 
