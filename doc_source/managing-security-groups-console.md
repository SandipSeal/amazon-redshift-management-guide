# Managing Cluster Security Groups Using the Console<a name="managing-security-groups-console"></a>

You can create, modify, and delete cluster security groups by using the Amazon Redshift console\. You can also manage the default cluster security group in the Amazon Redshift console\. All of the tasks start from the cluster security group list\. You must choose a cluster security group to manage it\.

In the example cluster security group list below, there are two cluster security groups, the `default` cluster security group and a custom cluster security group called `securitygroup1`\. Because `securitygroup1` is selected \(highlighted\), you can delete it or manage tags for it, and also see the rules and tags associated with it\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/mgmt/images/security-group-list-10.png)

You cannot delete the default cluster security group, but you can modify it by authorizing or revoking ingress access\. 

To add or modify the rules associated with a security group, choose the security group to go to the **Security Group Connections** page\.

## Creating a Cluster Security Group<a name="security-group-create"></a><a name="security-group-create-task"></a>

**To create a cluster security group**

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. In the navigation pane, choose **Security**\.

1. On the **Security Groups** tab, choose **Create Cluster Security Group**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/mgmt/images/security-group-create-10.png)

1. In the **Create Cluster Security Group** dialog box, specify a cluster security group name and description\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/mgmt/images/security-group-create-20.png)

1. Choose **Create**\.

   The new group is displayed in the list of cluster security groups\.

## Tagging a Cluster Security Group<a name="security-group-tag"></a><a name="security-group-tag-task"></a>

**To tag a cluster security group**

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. In the navigation pane, choose **Security**\.

1. On the **Security Groups** tab, choose the cluster security group and choose **Manage Tags**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/mgmt/images/security-group-revoke.png)

1. In the **Manage Tags** dialog box, do one of the following:

   1. Remove a tag\.
      + In the **Applied Tags** section, choose **Delete** next to the tag you want to remove\.
      + Choose **Apply Changes**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/mgmt/images/security-group-remove-tag.png)

   1. Add a tag\.
      + In the **Add Tags** section, type a key\-value pair for the tag\.
      + Choose **Apply Changes**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/mgmt/images/security-group-add-tag.png)

      For more information about tagging an Amazon Redshift resource, see [How to Manage Tags in the Amazon Redshift Console](rs-mgmt-tagging-console.md#rs-mgmt-console-tags-how-to)\.

## Managing Ingress Rules for a Cluster Security Group<a name="security-group-modify"></a><a name="security-group-modify-task"></a>

**To manage ingress rules for a cluster security group**

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. In the navigation pane, choose **Security**\.

1. On the **Security Groups** tab, in the cluster security group list, choose the cluster security group whose rules you want to manage\.

1. On the **Security Group Connections** tab, choose **Add Connection Type**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/mgmt/images/security-group-modify-10.png)

1. In the **Add Connection Type** dialog, do one of the following:

   1. Add an ingress rule based on CIDR/IP:
      + In the **Connection Type** box, choose **CIDR/IP**\.
      + In the **CIDR/IP to Authorize** box, specify the range\.
      + Choose **Authorize**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/mgmt/images/security-group-modify-20.png)

   1. Add an ingress rule based on an EC2 security group:
      + Under **Connection Type**, choose **EC2 Security Group**\.
      + Choose the AWS account to use\. By default, the account currently logged into the console is used\. If you choose **Another account**, you must specify the AWS account ID\. 
      + Choose the name of the EC2 security group you want in the **EC2 Security Group Name** box\. 
      + Choose **Authorize**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/mgmt/images/security-group-modify-30.png)

## Revoking Ingress Rules for a Cluster Security Group<a name="security-group-revoke"></a><a name="security-group-revoke-task"></a>

**To revoke ingress rules for a cluster security group**

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. In the navigation pane, choose **Security**\.

1. On the **Security Groups** tab, in the cluster security group list, choose the cluster security group whose rules you want to manage\.

1. On the **Security Group Connections** tab, choose the rule you want to remove and choose **Revoke**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/mgmt/images/security-group-revoke.png)

## Tagging Ingress Rules for a Cluster Security Group<a name="security-rule-tag"></a><a name="security-rule-tag-task"></a>

**To tag ingress rules for a cluster security group**

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. In the navigation pane, choose **Security**\.

1. On the **Security Groups** tab, choose the cluster security group whose rules you want to manage\.

1. On the **Security Group Connections** tab, choose the rule you want to tag and choose **Manage Tags**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/mgmt/images/security-group-tag-rule.png)

1. In the **Manage Tags** dialog box, do one of the following:

   1. Remove a tag:
      + In the **Applied Tags** section, choose **Delete** next to the tag you want to remove\.
      + Choose **Apply Changes**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/mgmt/images/security-group-remove-tag.png)

   1. Add a tag\.
**Note**  
Tagging an EC2 Security Group rule only tags that rule, not the EC2 Security Group itself\. If you want the EC2 Security Group tagged as well, you must do that separately\.
      + In the **Add Tags** section, type a key\-value pair for the tag\.
      + Choose **Apply Changes**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/mgmt/images/security-group-add-tag.png)

   For more information about tagging an Amazon Redshift resource, see [How to Manage Tags in the Amazon Redshift Console](rs-mgmt-tagging-console.md#rs-mgmt-console-tags-how-to)\.

## Deleting a Cluster Security Group<a name="security-group-delete"></a>

If a cluster security group is associated with one or more clusters, you cannot delete it\. <a name="security-group-delete-task"></a>

**To delete a cluster security group**

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. In the navigation pane, choose **Security**\.

1. On the **Security Groups** tab, choose the cluster security group that you want to delete, and then choose **Delete**\.

   One row must be selected for the **Delete** button to be enabled\.
**Note**  
You cannot delete the `default` cluster security group\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/mgmt/images/security-group-delete-10.png)

1. In the **Delete Cluster Security Groups** dialog box, choose **Continue**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/mgmt/images/security-group-delete-20.png)

   If the cluster security group is used by a cluster, you can't delete it\. The following example shows that `securitygroup1` is used by `examplecluster2`\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/mgmt/images/security-group-delete-30.png)

## Associating a Cluster Security Group with a Cluster<a name="security-group-associate"></a>

If you are on the EC2\-VPC platform, see [Managing VPC Security Groups for a Cluster](managing-vpc-security-groups.md) for more information about associating VPC security groups with your cluster\. We recommend that you launch your cluster in an EC2\-VPC platform\. However, you can restore an EC2\-Classic snapshot to an EC2\-VPC cluster using the Amazon Redshift console\. For more information, see [Restoring a Cluster from a Snapshot](managing-snapshots-console.md#snapshot-restore)\.

Each cluster you provision on the EC2\-Classic platform has one or more cluster security groups associated with it\. You can associate a cluster security group with a cluster when you create the cluster, or you can associate a cluster security group later by modifying the cluster\. For more information, see [Creating a Cluster by Using Launch Cluster](managing-clusters-console.md#create-cluster-task) and [To modify a cluster](managing-clusters-console.md#modify-cluster-task)\. 