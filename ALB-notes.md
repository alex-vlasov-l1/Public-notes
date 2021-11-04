


# Appllication Load-Balancer Ingress  main annotations

### Subnets
`ingress.alb.yc.io/subnet` - (**mandatory**) one or a list of subnets where Apllication Load-Balancer nodes must be deployed. Multiple subnets can be specified with comma-separated list of subnet-ids. For example:
```
ingress.alb.yc.io/subnets: b0c2kotoidcoh6haf8cu,e2lnhhdj9a0aqmr78d36,e9bud5itjnl8mkjj7td1
```
### Security-Groups
`ingress.alb.yc.io/security-groups` - (**optional**) one or a list of vpc security-groups applied to Application Load Balancer nodes. **Must** include a rule that permits inbound traffic from health-check range, so that health-checks can reach the service.
For example:
```
ingress {
   protocol       = "TCP"
   description    = "allows health_checks from load-balancer health check address range, needed for HA K8S cluster to work as well as for load balancer services to work"
   v4_cidr_blocks = ["198.18.235.0/24", "198.18.248.0/24"]
   from_port      = 0
   to_port        = 65535
 }
```
Multiple security-groups can be specified with comma-separated list of security-group-ids. For example:
```
ingress.alb.yc.io/security-groups: b0c2kotoidcoh6haf8cu,e2lnhhdj9a0aqmr78d36,e9bud5itjnl8mkjj7td1
```

If no security-groups are specified then **default-security-group** is used (can be enabled per-cloud via support request)
If no security-groups are specified and no **default-security-group** is present - then all the traffic is allowed (implicit allow)

### External ipv4 address
`ingress.alb.yc.io/external-ipv4-address` — (**mandatory**) creates external ipv4 address for ALB - so that it can be accesed from the Internet. Can be set in "auto" to create new address, or existing static ip address (e.g. "8.8.8.8") can be specified. this address is replicated by Application Load Balancer across whole region, making it Highly-Available

### Group-name
`ingress.alb.yc.io/group-name` — (**mandatory**) grouping of Kubernetes Ingress objects. Each group is being served by unique Application Load Blancer.
If you wish all your ingress objects to share one ALB - use one group name (for example "default")
If you wish for your ingress onbjects to have unique ALBs - use different group names for each ingress.
```
ingress.alb.yc.io/group-name: my-group-1
```
# Application Load Balancer additional annotations
### Internal ipv4 address
`ingress.alb.yc.io/internal-ipv4-address` — (**mutualy-exclusive** with external-ipv4-address) creates internal ipv4 address for ALB (borrowed from subnet) - so that it can be accesed from the VPC, VPN, cloud-interconnect and other privately-addresed (RFC1918) resources. Can be set in "auto" to create new address, or can reference desired internal ip address (e.g. "192.168.5.6"). This address is replicated by Application Load Balancer across whole region, making it Highly-Available

**Attention**
```
You can only choose one option per Application Load Balancer: ingress.alb.yc.io/external-ipv4-address or ingress.alb.yc.io/internal-ipv4-address. So ALB can "listen" either on public ipv4 endpoint accesible from the Internet, or on private endpoiint accesible from the VPC and other private networks.
```

### Internal alb subnet
`ingress.alb.yc.io/internal-alb-subnet` - Subnet from which  internal IP-address for Application Load Balancer will be "borrowed"
Even thouhgh subnet belong to a particular Availability Zone, this address is replicated by Application Load Balancer across whole region, making it Highly-Available  **mandatory** if ingress.alb.yc.io/internal-ipv4-address was selected.
### Prefix rewrite
`ingress.alb.yc.io/prefix-rewrite` — (**optional**) Replacement for the path prefix matched by StringMatch. For instance, if prefixMatch value is /foo and prefix-rewrite value is /bar, a request with /foobaz path is forwarded with /barbaz path.


### Upgrade-types
`ingress.alb.yc.io/upgrade-types` — (**optional**)  allowed values of HTTP-header 'Upgrade", for example **websocket**.

### Request-timeout
`ingress.alb.yc.io/request-timeout` — (**optional**)  maximum period, while ALB waits for response from backend

### Idle-timeout
`ingress.alb.yc.io/idle-timeout` — (**optional**)  maximum period, for which ALB allows connection to exist without any data being transfered.
Values for  request-timeout and idle-timeout should be specified with correct time-tunits, fot example: 300ms, 1.5h. Allowed units:

ns — nanoseconds;
us — microesconds;
ms — milliseconds;
s — seconds;
m — minutes;
h — hours.
