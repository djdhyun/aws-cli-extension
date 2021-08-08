# aws-cli-extension
Add more functions for AWS CLI by two separate ways.

1. by defining aws aliases
2. by sourcing `aws_runner` script

## Quickstart

#### Using AWSCLI Aliases

```
$ git clone https://github.com/djdhyun/aws-cli-extension
$ mkdir -p ~/.aws/cli
$ cp aws-cli-extension/alias ~/.aws/cli/alias

$ aws kinesis_peek --stream-name=your_stream_name --limit=2
```

#### Using `aws_runner`

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

* Since it simply just wraps the `aws` cli, you can issue any aws commands by using the function.

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


## Commands

#### lambda download

* Download the function code in your device.
* Arguments
	* `--function-name |--function`: The name of the lambda function

```
$ aws lambda_download --function-name=your_function_name
$ aws_runner lambda download --function-name=your_function_name

$ ls your_function_name
```

#### lambda getenv

* Show the running enviroment variables for the function.
* Arguments
	* `--function-name |--function`: The name of the lambda function

```
$ aws lambda_getenv --function-name=your_function_name
$ aws_runner lambda getenv --function-name=your_function_name

APP_NAME=YOUR_APP_NAME
API_KEY_CACHE_TTL=180
STAGE=prod
...
```

#### kinesis peek

* Read a sample record from a given kinesis stream.
* Arguments
	* `--stream-name|--stream`: The name of the kinesis stream
	* `--limit`: The number of records to be fetched

```
$ aws kinesis_peek --stream-name=your_stream_name --limit=10
$ aws_runner kinesis peek --stream-name=your_stream_name --limit=10

{ .. message_fetched_from_the_kinesis_stream .. }
```


