What happens when you hit api.razorpay.com? 
- Razorpay has green cluster & blue cluster. Depending on the load & availability, you will be redirected accordingly.
	- dig +short api.razorpay.com gave the following result
```
prod-api.razorpay.com.

prod-api-weighted.razorpay.com.

cde-blue-ext.razorpay.com.

35.154.234.0

65.0.38.54
```
Next request directed to the blue cluster
```
prod-api.razorpay.com.

prod-api-weighted.razorpay.com.

cde-blue-ext.razorpay.com.

35.154.234.0

65.0.38.54
```

What does dig do? - CLI for DNS resolution. Goes to ISP to get the IP address for a web address. 
+short gives the complete trace till the IP address is returned for a path. 

- Razorpay has an network level segregation. 
	- All external requests (ex: www.razorpay.com) goes to the external load balancer.
	- All internal requests (ex : service to service ) communication goes to the internal load balancer. 
	- Admin users on VPN are behind concierge load balancer.
	- This gives advantage to control the network congestion. Incase Internal network is congested, it doesn't other networks.

- Razorpay has three clusters Blue/Green/White. One is reserved for infra activities like Migration , etc. 
- Are the internal service to service load balancers not kubernetes objects? No, every internal S2S request goes to the entire path of going out of k8s cluster, going through loadbalancer & coming back to the k8s cluster.
- Razorpay uses amazon Application LB for external LB. 
- CDE is terminology for dataholder information. 