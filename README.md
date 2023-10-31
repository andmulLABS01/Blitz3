
# Blitz 3
October 31, 2023

By: Andrew Mullen

- What was the purpose?
- During the US holiday season, the URL Shortener is expected to experience unexpected traffic spikes ranging from 900 to 1,000 requests per second. On a single T.2 micro, the URL Shortener can handle up to 700 requests per second. A year has 12 government-designated holidays. What infrastructure should you build to handle the 900-1,000 unexpected requests per second?

- What happened during the blitz?
- During the blitz, we tested the T.2 micro capacity by sending traffic to it with JMeter. As confirmation, we tested the maximum number of requests a T.2 mico can handle, as well as the expected maximum, of 1000 requests per second, which is our estimated ceiling. The results are shown below.
 
    - 700: 0 errors
    - 1000: 68 errors
  
- According to the results of the tests, our T.2 micro would be unable to handle the unexpected traffic spikes during the 12 US holidays. To address the issue, we will need to increase compute capacity (vertical scaling) or the number of instances (horizontal scaling) to handle the increased traffic.
- Keep in mind that when performing these tests, you should take the average because some tests will show that the infrastructure can handle the requests, which can be interpreted as a false positive.

   
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
Click to see the visual diagram [HERE](https://github.com/andmulLABS01/Blitz3/blob/main/Blitz3.drawio.png)
