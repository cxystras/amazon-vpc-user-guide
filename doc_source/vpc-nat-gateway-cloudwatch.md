# Monitoring Your NAT Gateway with Amazon CloudWatch<a name="vpc-nat-gateway-cloudwatch"></a>

You can monitor your NAT gateway using CloudWatch, which collects information from your NAT gateway and creates readable, near real\-time metrics\. You can use this information to monitor and troubleshoot your NAT gateway\. NAT gateway metric data is provided at 1\-minute frequency, and statistics are recorded for a period of 15 months\.

For more information about Amazon CloudWatch, see the [Amazon CloudWatch User Guide](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/)\. For more information about pricing, see [Amazon CloudWatch Pricing](http://aws.amazon.com/cloudwatch/pricing)\.


+ [NAT Gateway Metrics and Dimensions](#metrics-dimensions-nat-gateway)
+ [Creating CloudWatch Alarms to Monitor a NAT Gateway](#creating-alarms-nat-gateway)

## NAT Gateway Metrics and Dimensions<a name="metrics-dimensions-nat-gateway"></a>

NAT gateway metrics are sent to CloudWatch at 1\-minute intervals\. You can use the following procedures to view the metrics for your NAT gateway\.

Currently, you can view NAT gateway metrics using the CloudWatch console or a command line tool only\.

**To view metrics using the CloudWatch console**

Metrics are grouped first by the service namespace, and then by the various dimension combinations within each namespace\.

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Metrics**\.

1. Under **All metrics**, choose the **NAT gateway** metric namespace\.

1. To view the metrics, select the metric dimension \.

**To view metrics using the AWS CLI**

+ At a command prompt, use the following command to list the metrics that are available for the NAT gateway service:

  ```
  aws cloudwatch list-metrics --namespace "AWS/NATGateway"
  ```

The following metrics are available from the NAT gateway service\.


| Metric | Description | 
| --- | --- | 
|  PacketsOutToDestination  |  The number of packets sent out through the NAT gateway to the destination\. A value greater than zero indicates that there is traffic going to the internet from clients that are behind the NAT gateway\. If the value for `PacketsOutToDestination` is less than the value for `PacketsInFromSource`, there may be data loss during NAT gateway processing\. Unit: Count  | 
|  PacketsOutToSource  |  The number of packets sent through the NAT gateway to the clients in your VPC\. A value greater than zero indicates that there is traffic coming from the internet to clients that are behind the NAT gateway\. If the value for `PacketsOutToSource` is less than the value for `PacketsInFromDestination`, there may be data loss during NAT gateway processing, or traffic being actively blocked by the NAT gateway\. Unit: Count  | 
|  PacketsInFromSource  |  The number of packets received by the NAT gateway from clients in your VPC\. If the value for `PacketsOutToDestination` is less than the value for `PacketsInFromSource`, there may be data loss during NAT gateway processing\. Unit: Count  | 
|  PacketsInFromDestination  |  The number of packets received by the NAT gateway from the destination\. If the value for `PacketsOutToSource` is less than the value for `PacketsInFromDestination`, there may be data loss during NAT gateway processing, or traffic being actively blocked by the NAT gateway\. Unit: Count  | 
|  BytesOutToDestination  |  The number of bytes sent out through the NAT gateway to the destination\. A value greater than zero indicates that there is traffic going to the internet from clients that are behind the NAT gateway\. If the value for `BytesOutToDestination` is less than the value for `BytesInFromSource`, there may be data loss during NAT gateway processing\. Unit: Bytes  | 
|  BytesOutToSource  |  The number of bytes sent through the NAT gateway to the clients in your VPC\. A value greater than zero indicates that there is traffic coming from the internet to clients that are behind the NAT gateway\. If the value for `BytesOutToSource` is less than the value for `BytesInFromDestination`, there may be data loss during NAT gateway processing, or traffic being actively blocked by the NAT gateway\. Units: Bytes  | 
|  BytesInFromSource  |  The number of bytes received by the NAT gateway from clients in your VPC\. If the value for `BytesOutToDestination` is less than the value for `BytesInFromSource`, there may be data loss during NAT gateway processing\. Units: Bytes  | 
|  BytesInFromDestination  |  The number of bytes received by the NAT gateway from the destination\. If the value for `BytesOutToSource` is less than the value for `BytesInFromDestination`, there may be data loss during NAT gateway processing, or traffic being actively blocked by the NAT gateway\. Units: Bytes  | 
|  ErrorPortAllocation  |  The number of times the NAT gateway could not allocate a source port\.  A value greater than zero indicates that too many concurrent connections are open through the NAT gateway\. Units: Count  | 
|  ActiveConnectionCount  |  The total number of concurrent active TCP connections through the NAT gateway\. A value of zero indicates that there are no active connections through the NAT gateway\. Units: Count  | 
|  ConnectionAttemptCount  |  The number of connection attempts made through the NAT gateway\. If the value for `ConnectionEstablishedCount` is less than the value for `ConnectionAttemptCount`, this indicates that clients behind the NAT gateway attempted to establish new connections for which there was no response\. Unit: Count  | 
|  ConnectionEstablishedCount  |  The number of connections established through the NAT gateway\. If the value for `ConnectionEstablishedCount` is less than the value for `ConnectionAttemptCount`, this indicates that clients behind the NAT gateway attempted to establish new connections for which there was no response\. Unit: Count  | 
|  IdleTimeoutCount  |  The number of connections that transitioned from the active state to the idle state\. An active connection transitions to idle if it was not closed gracefully and there was no activity for the last 350 seconds\. A value greater than zero indicates that there are connections that have been moved to an idle state\. If the value for `IdleTimeoutCount` increases, it may indicate that clients behind the NAT gateway are re\-using stale connections\.  Unit: Count  | 
|  PacketsDropCount  |  The number of packets dropped by the NAT gateway\. A value greater than zero may indicate an ongoing transient issue with the NAT gateway\. If this value is high, see the [AWS service health dashboard](http://status.aws.amazon.com/)\. Units: Count  | 

You can filter the NAT gateway data using the following dimensions\.


| Dimension | Description | 
| --- | --- | 
| NatGatewayId | This dimension filters data by the NAT gateway ID\. | 

## Creating CloudWatch Alarms to Monitor a NAT Gateway<a name="creating-alarms-nat-gateway"></a>

You can create a CloudWatch alarm that sends an Amazon SNS message when the alarm changes state\. An alarm watches a single metric over a time period that you specify\. It sends a notification to an Amazon SNS topic based on the value of the metric relative to a given threshold over a number of time periods\. 

For example, you can create an alarm that monitors the amount of traffic coming in or leaving the NAT gateway\. The following alarm monitors the amount of outbound traffic from clients in your VPC through the NAT gateway to the internet\. It sends a notification when the number of bytes reaches a threshold of 5,000,000 during a 15\-minute period\.

**To create an alarm for outbound traffic through the NAT gateway**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Alarms**, **Create Alarm**\.

1. Choose **NAT gateway**\.

1. Select the NAT gateway and the **BytesOutToDestination** metric and choose **Next**\.

1. Configure the alarm as follows, and choose **Create Alarm** when you are done:

   + Under **Alarm Threshold**, enter a name and description for your alarm\. For **Whenever**, choose **>=** and enter `5000000`\. Enter **1** for the consecutive periods\.

   + Under **Actions**, select an existing notification list or choose **New list** to create a new one\. 

   + Under **Alarm Preview**, select a period of 15 minutes and specify a statistic of **Sum**\.

You can create an alarm that monitors the `ErrorPortAllocation` metric and sends a notification when the value is greater than zero \(0\) for three consecutive 5\-minute periods\. 

**To create an alarm to monitor port allocation errors**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Alarms**, **Create Alarm**\.

1. Choose **NAT Gateway**\.

1. Select the NAT gateway and the **ErrorPortAllocation** metric and choose **Next**\.

1. Configure the alarm as follows, and choose **Create Alarm** when you are done:

   + Under **Alarm Threshold**, enter a name and description for your alarm\. For **Whenever**, choose **>** and enter `0`\. Enter **3** for the consecutive periods\.

   + Under **Actions**, select an existing notification list or choose **New list** to create a new one\. 

   + Under **Alarm Preview**, select a period of 5 minutes and specify a statistic of **Maximum**\.

For more examples of creating alarms, see [Creating Amazon CloudWatch Alarms](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html) in the *Amazon CloudWatch User Guide*\.