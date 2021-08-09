# aws-cli-extension
Add more commands for AWS CLI by two separate ways.

1. by defining aws aliases
2. by sourcing `aws_runner` script

### Command List

* [lambda download](#lambda-download)
* [lambda getenv](#lambda-getenv)
* [s3 peek](#s3_peek)
* [s3 random cp](#s3_random_cp)
* [kinesis peek](#kinesis_peek)
* [elbv2 describe\_target\_groups --load-balancer-name](#elbv2-describe_target_groups---load-balancer-name)
* [elbv2 describe\_load\_balancer --load-balancer-name](#elbv2-describe_load_balancer---load-balancer-name)
* [elbv2 dnsname --load-balancer-name](#elbv2-dnsname---load-balancer-name)

## Quickstart

### Using AWSCLI Aliases

```
$ git clone https://github.com/djdhyun/aws-cli-extension
$ mkdir -p ~/.aws/cli
$ cp aws-cli-extension/alias ~/.aws/cli/alias

$ aws kinesis_peek --stream-name=your_stream_name --limit=2
```

### Using `aws_runner`

```
$ git clone https://github.com/djdhyun/aws-cli-extension
$ source aws-cli-extension/source.me

$ aws_runner kinesis peek --stream-name=your_stream_name --limit=2
```

## AWSCLI Aliases

* For the information about awscli alias, please refer to [awslabs/awscli-aliases](https://github.com/awslabs/awscli-aliases)

#### Limitation of the use

* Unfortunately, you **CANNOT** pass any arguments that would apply to `aws` itself when using aws aliases.
* i.e, Theses might not work as you intended.

```
$ aws --region-name=us-west-1 kinesis_peek --stream-name=...
$ aws kinesis_peek --stream-name=... --region-name=us-west-1
```

#### Possible workarounds

1. Take advantage of [AWS CLI Enviroment variables](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html).
2. Set `AWS_OPTS`
3. ..or consider using `aws_runner` instead.

```
# This is not gonna work.
aws --region-name=us-west-1 kinesis_peek --stream-name=...

# [Option 1]
AWS_DEFAULT_REGION=us-west-1 aws kinesis_peek --stream-name=...
AWS_PROFILE=your_alternative_profile aws kinesis_peek --stream-name=...

# [Option 2]
AWS_OPTS="--region=us-west-1" aws kinesis_peek --stream-name=...

# [Option 3]
aws_runner --region-name=us-west-1 kinesis peek --stream-name=...
aws_runner kinesis peek --stream-name=... --region-name=us-west-1
```

## `aws_runner`

* Since it simply just wraps the `aws` cli, you can issue any aws commands along with corresponding options.

```
# What you could do with the original aws cli
aws_runner sts get-caller-identity
aws_runner --region=us-west-1 s3 ls ...

# Extended commands
aws_runner kinesis peek --stream-name=... --region-name=us-west-1
```

* Configure internal `aws` command by setting/exporting `AWS` enviroment variable.

```
# Instead of this,
aws_runner kinesis peek --stream-name=... --region-name=us-west-1

# Try this.
AWS="aws --region-name=us-west-1" aws_runner kinesis peek --stream-name=...

# It would be convenient if you're currently using "aws-vault"
AWS="aws-vault exec my_profile -- aws" aws_runner kinesis peek
```

## Dependencies

### `jq` command (Command-line JSON processor)
* For installation, please refer to https://stedolan.github.io/jq/download/

## Commands

#### lambda download

* Download the lambda function code in your device.
* Arguments
	* `--function-name|--function`: The name of the lambda function

```
$ aws lambda_download --function-name=your_function_name
$ aws_runner lambda download --function-name=your_function_name

$ ls your_function_name
```

#### lambda getenv

* Show the running enviroment variables for the lambda function.
* Arguments
	* `--function-name|--function`: The name of the lambda function

```
$ aws lambda_getenv --function-name=your_function_name
$ aws_runner lambda getenv --function-name=your_function_name

APP_NAME=YOUR_APP_NAME
API_KEY_CACHE_TTL=180
STAGE=prod
...
```

#### s3 peek

* Read contents of a sample object that belongs to the given s3 path.
* Positional Arguments
	* S3Uri

```
$ aws_runner s3 peek s3://your_bucket_name/path1/path2

{ .. contents of the object .. }
```

#### s3 random cp

* Download a sample object that belongs to the given s3 path.
* Positional Arguments
	* S3Uri
    * LocalPath to be downloaded 
    <br>(optional, default will current path along with the filename of the random object)

```
$ aws_runner s3 random-cp s3://your_bucket_name/path1/path2 ./your_path/target.gz
```

#### kinesis peek

* Read a sample record from a given kinesis stream.
* Arguments
	* `--stream-name|--stream`: The name of the kinesis stream
	* `--limit`: The number of records to be fetched (optional, default=1)

```
$ aws kinesis_peek --stream-name=your_stream_name --limit=10
$ aws_runner kinesis peek --stream-name=your_stream_name --limit=10

{ .. message_fetched_from_the_kinesis_stream .. }
```

#### elbv2 describe\_load\_balancer --load-balancer-name

* Describes the specified load balancer by the name of a load balancer.
* Arguments
	* `--load-balancer-name`: The name of a load balancer

```
$ aws_runner describe_target_groups --load-balancer-name=your_lb_name

{ "LoadBalancerArn": "...", "DNSName" : "...", .. }
```

#### elbv2 describe\_target\_groups --load-balancer-name

* Describes the specified target groups by the name of a load balancer.
* Arguments
	* `--load-balancer-name`: The name of a load balancer

```
$ aws_runner describe_target_groups --load-balancer-name=your_lb_name

{ "TargetGroups": [{ ... }] }
```

#### elbv2 dnsname --load-balancer-name

* Print the dns address of a given load balancer name.
* Arguments
	* `--load-balancer-name`: The name of a load balancer

```
$ aws_runner elbv2 dnsname --load-balancer-name=your_lb_name

your.lb.dns.address.com
```
