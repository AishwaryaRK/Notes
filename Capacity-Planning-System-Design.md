# Capacity Planning

### Capacity Calculation

1. Number of users [100M users]
2. Throughput - Request per sec (rps) [10B req/day → 100k req/sec]
    - [1 users 10 requests per day]
3. Request per sec at Peak [200k req/sec (peak)]
4. disk seeks per sec (IOPS) [2M disk seeks/sec IOPS]
    - [1 req 10 disk seeks]
5. Number of disks (2M / 100 = 20k disk drives)
    - [100 IOPS per disk]
6. Number of servers [20k / 24 = 834 servers]
    - 24 disks / server
7. Network inbound - Bandwidth (incoming data) [600 QPS * 4MB = 2400MB/s] 
    - upload image(4MB) eg.
    1. Number of NICs required [2400MB/s / 1000MB/s = 3NICs]
        - 1 NIC can have 1000MB
        - OR  [1000MB/s / 4MB = 250 QPS per NIC]
    2. Writing to storage (outbound)

### Steps to Creating System Design: 

1. Determine the requirements. 
    1. Enumerate all of the use cases.  
    2. Sort the requirements by their order of importance.  
    3. Work through the sub-requirements. 
2. Ask about the contraints of the system. 
    1. How many daily active users do we have? 
    2. Make predictions. Number of Users 
    3. Average Concurrent users = daily active users / 5 (?Ask)  
    4. Peak Concurrent users = average concurrent users * 3(? Ask) 
    5. Max peak users in 3 months = current peak concurrent users * 10 (? Ask)
    6. Network Traffic 
        1. Calculate requests per user per minute. 
        2. Calculate size of a single request. 
        3. Calculate requests per user * users * size of request / 60 = network traffic per second. (QPS queries/requests per second) 
    7. Calculate max memory usage. 
    8. Calculate memory usage per user. 
    9. Calculate max peak traffic. Memory Usage 
3. Come up with an application with services. 
    1. Create a service for each use case. 
    2. Have each service connect to a supervisor / load balancer that distributes requests. 
4. Create the datastores. 
    1. First create a datastore for each service. 
    2. SQL for relational - NoSQL for non-relational / document / key-value 
    3. File Buckets (S3) for files 
5. Evolve your design. 
    1. Add better performance contraints to the system. 
    2. Add additional services for other use cases. 
    3. Go into more detail on how a service may work.  
    4. Look at performance, scalability, fault-tolerance and discuss tradeoffs.
6. Additional Notes 
    1. Create a dispatcher that communicates with Logger Service, Web Service, Worker Service, and Datastore. 
    2. You can draw out a state machine diagram to show the user flow and various paths through your service to see if you've missed cases.
