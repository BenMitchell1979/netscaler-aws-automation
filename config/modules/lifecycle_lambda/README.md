# Terraform template to create a lambda function to manage VPX lifecycle
Lambda function that operates as a lifecycle hook for an autoscaling group of VPX

# Operation
The autoscaling group is created with this lifecycle hook. A VPX is launched per availability zone. The VPX is launched with 1 network interface (ENI) which is the NSIP ENI in the server subnet.
The lifecycle hook is called with `INSTANCE_LAUNCHING` or `INSTANCE_TERMINATING` event which in turn calls this lifecycle lambda function. The lifecycle lambda creates an additional ENI in the client subnet and attache it to the VPX and then tells AWS autoscaling  to CONTINUE. In addition the lifecycle lambda

* configures the SNIP of the VPX
* enables features (LB, SSL)
* saves this config
* warm reboots the VPX to allow it to work with the newly attached interfaces


# Pre-requisites
Build `lifecycle.zip`

```
cd ../../../vpx_lifecycle/
make 
```

The lambda function relies on the `notification_metadata` relayed by the lifecycle hook. This is set up when creating the autoscaling group. See `../vpx/asg.tf`
