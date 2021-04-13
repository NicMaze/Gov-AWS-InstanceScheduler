# AWS-InstanceScheduler

## Gov Cloud CFT Deployment ##

* PreReq: Upload the .zip file into an S3 bucket as it's needed for the template deployment.
* The template (govcloud-instance-scheduler.yml) has default values populated so it can be easily deployed as-is after putting in the S3 bucket and .zip locations.
* The default value only monitors EC2 but it can also be used for RDS.
* The default values setup CloudWatch logging with 7 days retention and anonymous usage data is not reported.
* **Instance Scheduler tag name:** This is the Tag:Key that will be added to each EC2 instance that you want to schedule, if left as-is it will be 'Schedule'.
* You will set the Key:Value after deployment of the template. Steps covered below under Gov Cloud Configuration.

## Gov Cloud Configuration ##

* After the template is successfully deployed click on the template:Resources section. This will give you a list and links to the resources that were deployed with the template.
* The resource we need to configure is 'ConfigTable' with Type 'AWS::DynamoDB::Table'. Click the Physical ID link and you will be taken to the right location automatically.
* Once inside the 'ConfigTable' click the Items tab. You will see a few types of items; config, schedule, and period.
* Period is the window for the scheduler should run. Modify an existing period to desired window or select a period > click 'Actions' near the top and duplicate.
* Schedule references one of those periods and it's name is used to assign the Tag:Value. Configure your desired Schedule to use your desired period.
* From there you just need to tag your instances you want to be assigned to the schedule.

## Configure EC2 Instance ##

* If you didn't change the 'Instance Scheduler tag name' you simply tag each instance with key:Schedule, the value is the Schedule Name from the DB table.
* A prebuild Schedule is named "seattle-office-hours" so if you tagged an instance with key:Schedule and value:seattle-office-hours, the Instance Scheduler will run based on the period assigned to the "seattle-office-hours" schedule.

### Non-Gov Cloud AWS Account ###

The AWS Instance Scheduler can be easily deployed following the steps found [here](https://aws.amazon.com/solutions/implementations/instance-scheduler/)

### Helpful Resources ###

The AWS Instance Scheduler [github repo](https://github.com/awslabs/aws-instance-scheduler)

Useful [resource](https://github.com/awslabs/aws-instance-scheduler/issues/11) discussing issues that had to be resolved. All modifications have been made to the .zip file and template so this should not be needed.

Official documentation of the [AWS Instance Scheduler](https://docs.aws.amazon.com/solutions/latest/instance-scheduler/instance-scheduler.pdf)

### Troubleshooting ###

* In CloudWatch, IF you get a message: "WARNING : Skipping instance EC2:**InstanceID** (**InstanceName**) in region us-gov-east-1 for account **AccountNumber**, schedule name "**ScheduleName**" is unknown"
--> This was caused by the 'period' used in the schedule. I was using 00:00 for midnight and it threw the error. Changed to 23:00 and the issue was resolved.
