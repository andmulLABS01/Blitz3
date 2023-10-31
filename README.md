
# Blitz 3
October 31, 2023

By: Andrew Mullen

- What was the purpose?
  - During the US holidays, the URL Shortener is expected to receive unexpected traffic spikes between 900 - 1,000 requests per second. The URL Shortener can handle a maximum of 700 requests per second on a single T.2 micro. There are 12 government-recognized holidays in a year. What should you add to the infrastructure to handle the 900 - 1,000 unexpected requests per second?

- What happened during the blitz?
  - During the blitz, we tested the T.2 micro capacity by sending traffic to it using JMeter. We tested the maximum requests a T.2 mico can handle, as confirmation, and tested the expected maximum, 1000 requests per second, as that is our estimated ceiling. Below are the results.
 
    - 700: 0 errors
	- 1000: 68 errors
  
  - Based on the tests, our T.2 micro would not be able to handle the unexpected traffic spikes during the 12 US holidays.  To remediate the issue we will need to increase the compute capacity, vertical scaling, or the number of instances, horizontal scaling, to handle the increased traffic. 
  - Note when you do these tests you would want to take the average as some tests will show that the infrastructure can handle the requests and that can be looked at as a false positive.
  - Note we when we needed to The test not only increased our server CPU usage to 100%, we dropped 710 of the 14,000 test connections to the server.
    
- What did you do to resolve the issue?
  - Since we will only need the increased infrastructure for only 12 days out of the year. To resolve the issue, I decided to implement an auto-scaling group to create additional instances when traffic reaches a capacity level. 
  - We created an environment in ELB to deploy a second ELB infrastructure. (Note you will need to use an application that is not currently running)
	- Platform: Pytohn 3.9
	- Application code: existing (select version)
	- Configuration Presets: Single instance
	- Service Access: Select Service-role and EC2 profile
	- VPC: Select default VPC
	- Public IP address: Check activated
	- Instance Subnets: select two subnets
	- Root Volume type: Select general purpose
	- Size: increase to 10 GB
	- Amazon CloudWatch monitoring: Change interval to 1 minute
	- Capacity environment type: Load Balanced
	- Instances: Min 1; Max 2
	- Instance Type: Select T.2 Micro
	- Scaling trigger Metric: request count
	- Statistic: Sum
	- Units: Count
	- Period: change to 1
	- Breach duration: change to 1
	- Upper threshold: 700
	- Lower threshold: 600 (creates instance based on this setting)
	
  - Once the Auto-scaling group environment was created. The JMeter tests were rerun, 1000 requests per second, to see if the auto-scaling group would handle the maximum expected requests.  Below are the results.
	- 1000: 0 errors    
	
- What are the economic benefits of using auto-scaling and an ALB vs a stronger instance with more bandwidth (Like a c5.4xlarge)?

- 
# Diagram:
Click to see the visual diagram [HERE](https://github.com/andmulLABS01/Blitz2/blob/main/Blitz2.drawio.png)
