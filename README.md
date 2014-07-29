ec2list
=======

List hosts across multiple accounts and all regions. Saves you from having to go searching through accounts if you're looking for a specific instance.

The application is multi-threaded and should return results in 5 to 6 seconds depending on how many accounts and regions you have. To get better performance comment out regions that you aren't actively using.

Usage
=====
```
usage: ec2list [-h] [-i INSTANCES] [-v] [-a] [-f FILTER]

optional arguments:
  -h, --help            show this help message and exit
  -i INSTANCES, --instances INSTANCES
                        Comma-separated list of instances to filter results.
  -v, --verbose         Print all host metadata.
  -a, --all             List all hosts, even those that aren't running.
  -f FILTER, --filter FILTER
                        key value filters to pass directly to command. Eg:
                        Name=instance-type,Value=m1.small
```

Prerequisites
=============
You must have a $HOME/.aws/config file setup with the proper credentials.

An example profile:
```
[profile production]
aws_access_key_id=AKIAFOO
aws_secret_access_key=grzOgbar
region=us-west-1

[profile staging]
aws_access_key_id=AKIAIFOO
aws_secret_access_key=BAYS232bar
region=us-west-1
```

You can test that the different profiles are active by running the aws cli tool
```
aws ec2 describe-instances --profile=production
aws ec2 describe-instances --profile=staging
```

### Listing all instances in a succinct manner.
The columns when run this way will always be predictable (unlike -v). Useful if you're using awk or other things.
```
$ ./list
i-7e15d971 stephen_test_001 10.0.0.100 54.183.84.15 m3.large production
i-7e15d971 stephen_test_002 10.0.0.100 54.183.84.16 m3.large staging
...
```

### List instances that are also stopped
By default only running instances will be shown. Use the -a or --all flag to show all instances, even those that are stopped.
```
$ ./list -a
```

### Be verbose (prints all available instance info)
Verbose prints everything returned by ec2. The return values are unordered.
```
./list -v
i-7e15d971:
  Monitoring: {"State": "disabled"}
  PublicDnsName: ec2-54-131-88-95.us-west-2.compute.amazonaws.com
  RootDeviceType: ebs
  State: {"Code": 16, "Name": "running"}
  EbsOptimized: False
  ...
i-bba5d6e6:
  Monitoring: {"State": "disabled"}
  PublicDnsName: ec2-54-210-162-78.us-west-1.compute.amazonaws.com
  RootDeviceType: ebs
  State: {"Code": 16, "Name": "running"}
  EbsOptimized: False
  ...
```

### Print only information for a select number of instances
To easily filter instances by instance id, they can be passed as a comma-separated list with the ```-i``` or ```--instances``` flag.
```
./list -i i-bba5d6e6,i-cad1b0c2,i-afa406f2
```

### Print only instances that are an m1.small
Note that the filters arg allows you to pass filters down directory to the underlying aws ec2 command. See the ec2 api for information on the types of filtering you can do.
```
$ ./list -f Name=instance-type,Values=m1.small
```
