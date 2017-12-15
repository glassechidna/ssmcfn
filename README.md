# SSM Parameter Store helper for CloudFormation templates

(Extending https://github.com/glassechidna/ssmcfn)

You know what's lame? CloudFormation not (yet) having support for storing values
in [Parameter Store][aws-pstore]. You know what's less lame? [Lambda-backed custom resources][cfn-res]
so that we can polyfill this ourselves. Simply deploy [`cfn.yml`](cfn.yml) into
your AWS region and use like this:

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Parameters:
  BuildNumber:
    Description: A unique number to ensure that the GetParam is run
    Type: Number
Resources:
  SecureParam:
    Type: Custom::CfnParamStore
    Properties:
      ServiceToken: !ImportValue CfnParamStoreCreate
      KeyName: somevalue
      Type: SecureString
      KeyValue: this-is-the-new-password

  GetParam:
    Type: Custom::CfnParamStoreGet
    Properties:
      ServiceToken: !ImportValue CfnParamStoreGet
      KeyName: somevalue
      BuildNumber: !Ref BuildNumber
```

The above makes some extra changes to the original code.

* It now supports a GetParm event
* The GetParm returns a phoney result (eg "Please set cfn-param-stack-notreal in the console") if one is not set


[https://github.com/glassechidna/ssmcfn]: https://github.com/glassechidna/ssmcfn
[aws-pstore]: https://aws.amazon.com/ec2/systems-manager/parameter-store/
[cfn-res]: http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-custom-resources-lambda.html


