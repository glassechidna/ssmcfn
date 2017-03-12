# SSM Parameter Store helper for CloudFormation templates

You know what's lame? CloudFormation not (yet) having support for storing values 
in [Parameter Store][aws-pstore]. You know what's less lame? [Lambda-backed custom resources][cfn-res]
so that we can polyfill this ourselves. Simply deploy [`cfn.yml`](cfn.yml) into
your AWS region and use like this:

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Parameters:
  SecretValue:
    Description: Sssh, it's a secret
    Type: String
    NoEcho: true
Resources:
  SecureParam:
    Type: Custom::CfnParamStore
    Properties:
      ServiceToken: !ImportValue CfnParamStore
      Type: SecureString
      Value: !Ref SecretValue
Outputs:
  ParamArn:
    Description: Arn of param in SSM param store
    Value: !GetAtt SecureParam.Arn
```

Note that neither CloudFormation, Lambda nor Parameter Store are global resources,
so you will have to deploy the helper stack into each region
that you wish to use this in.

[aws-pstore]: https://aws.amazon.com/ec2/systems-manager/parameter-store/
[cfn-res]: http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-custom-resources-lambda.html