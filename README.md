# erlcloud: AWS APIs library for Erlang #

[![Build Status](https://secure.travis-ci.org/erlcloud/erlcloud.png?branch=master)](http://travis-ci.org/erlcloud/erlcloud)

This library is not developed or maintained by AWS thus lots of functionality is still missing comparing to [aws-cli](https://aws.amazon.com/cli/) or [boto](https://github.com/boto/boto).
Required functionality is being added upon request.

Service APIs implemented:
- Amazon Elastic Compute Cloud (EC2)
- Amazon Simple Storage Service (S3)
- Amazon Simple Queue Service (SQS)
- Amazon DynamoDB & DDB streams (ddb2)
- Amazon Autoscalling(as)
- Amazon CloudTrail (CT)
- Cloud Formation (CFN)
- ElasticLoadBalancing (ELB)
- Identity and Access Management (IAM)
- Kinesis
- CloudWatch
- MechanicalTurk
- Simple DB (SDB)
- Relational Data Service (RDS)
- Simple Email Service (SES)
- Short Token Service (STS)
- Simple Notification Service (SNS)
- Web Application Firewall (WAF)
- and more to come

Majority of API functions have been implemented.
Not all functions have been thoroughly tested, so exercise care when integrating this library into production code.
Please send issues and patches.

The libraries can be used two ways:
- either you can specify configuration parameters in the process dictionary. Useful for simple tasks
- you can create a configuration object and pass that to each request as the final parameter. Useful for Cross AWS Account access

## Roadmap ##

Below is the proposed library roadmap update along with regular features and fixes.

- 0.13.9
 * Existing code

- 2.0.X
 * Going further we would like to merge in [Alert Logic](https://github.com/alertlogic/erlcloud/tree/v1.2.3) fork into upstream.
 This is a major version bump which contains lots of new features and functionality.
 Unfortunately, it also contains quite a number of low level APIs incompatibilities since the fork diverged for a long while.
 * No APIs have been removed and it's on branched of 0.13.8 at the moment. Any minor version delta added during notice time will be compensated before the merge.
 Making it backward compatible does not seem feasible and valuable at the moment.
 * intentionally jumping to v2.0.X as AL fork has v1.0.X

- 2.1.X
 * fix dialyzer findings and make it mandatory for the library
 * make full support of Mix/HEX

- 2.2.X
 * Further deprecation of legacy functionality
  * Only SigV4 signing and generalised in one module. Keep SigV2 in SBD section only
  * no more `erlang:error()` use and use of regular tuples as error API.

## Getting started ##
You need to clone the repository and download rebar/rebar3 (if it's not already available in your path).
```
git clone https://github.com/erlcloud/erlcloud.git
cd erlcloud
wget https://s3.amazonaws.com/rebar3/rebar3
chmod a+x rebar3
```
To compile and run erlcloud
```
make
make run
```

If you're using erlcloud in your application, add it as a dependency in your application's configuration file.
To use erlcloud in the shell, you can start it by calling:

```
application:ensure_all_started(erlcloud).
```
You can provide your amazon credentials in environmental variables.

```
export AWS_ACCESS_KEY_ID=<Your AWS Access Key>
export AWS_SECRET_ACCESS_KEY=<Your AWS Secret Access Key>
```
If you did not provide your amazon credentials in the environmental variables, then you need to provide the per-process configuration:

```
erlcloud_ec2:configure(AccessKeyId, SecretAccessKey [, Hostname]).
```
Hostname defaults to non-existing `"ec2.amazonaws.com"` intentionally to avoid mix with US-East-1
Refer to [aws_config](https://github.com/erlcloud/erlcloud/blob/master/include/erlcloud_aws.hrl) for full description of all services configuration.

Then you can start making api calls, like:

```
erlcloud_ec2:describe_images().
erlcloud_s3:list_buckets().
```

Configuration object usage:

```
EC2 = erlcloud_ec2:new(AccessKeyId, SecretAccessKey [, Hostname])
erlcloud_ec2:describe_images(EC2).
```

Creating an EC2 instance may look like this:
```erlang
start_instance(Ami, KeyPair, UserData, Type, Zone) ->
    Config = #aws_config{
            access_key_id = application:get_env(aws_key),
            secret_access_key = application:get_env(aws_secret)
           },

    InstanceSpec = #ec2_instance_spec{image_id = Ami,
                                      key_name = KeyPair,
                                      instance_type = Type,
                                      availability_zone = Zone,
                                      user_data = UserData},
    erlcloud_ec2:run_instances(InstanceSpec, Config).
```

For usage information, consult the source code and https://hexdocs.pm/erlcloud.
For detailed API description refer to the AWS references at:

- http://docs.aws.amazon.com/AWSEC2/latest/APIReference/Welcome.html
- http://docs.aws.amazon.com/AmazonS3/latest/API/Welcome.html
and other services https://aws.amazon.com/documentation/

### Using Temporary Security Credentials

The access to AWS resource might be managed through [third-party identity provider](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-idp.html).
The access is managed using [temporary security credentials](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html).

You can provide your amazon credentials in environmental variables

```
export AWS_ACCESS_KEY_ID=<Your AWS Access Key>
export AWS_SECRET_ACCESS_KEY=<Your AWS Secret Access Key>
export AWS_SECURITY_TOKEN=<Your AWS Security Token>
```
If you did not provide your amazon credentials in the environmental variables, then you need to provide configuration read from your profile:
```
{ok, Conf} = erlcloud_aws:profile().
erlcloud_s3:list_buckets(Conf).
```

## Notes ##

Indentation in contributions should follow indentation style of surrounding text.
In general it follows default indentation rules of official erlang-mode as provided by OTP team.
