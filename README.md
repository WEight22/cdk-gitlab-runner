[![NPM version](https://badge.fury.io/js/cdk-gitlab-runner.svg)](https://badge.fury.io/js/cdk-gitlab-runner)
[![PyPI version](https://badge.fury.io/py/cdk-gitlab-runner.svg)](https://badge.fury.io/py/cdk-gitlab-runner)
![Release](https://github.com/guan840912/cdk-gitlab-runner/workflows/Release/badge.svg)

![Downloads](https://img.shields.io/badge/-DOWNLOADS:-brightgreen?color=gray)
![npm](https://img.shields.io/npm/dt/cdk-gitlab-runner?label=npm&color=orange)
![PyPI](https://img.shields.io/pypi/dm/cdk-gitlab-runner?label=pypi&color=blue)

![](https://img.shields.io/badge/iam_role_self-enable-green=?style=plastic&logo=appveyor)
![](https://img.shields.io/badge/vpc_self-enable-green=?style=plastic&logo=appveyor)
![](https://img.shields.io/badge/gitlab_url-customize-green=?style=plastic&logo=appveyor)
# Welcome to `cdk-gitlab-runner`
 
This repository template helps you create gitlab runner on your aws account via AWS CDK one line.

## Note 
### Default will help you generate below services:
- VPC
  - Public Subnet (2)
- EC2 (1 T3.micro)

## Before start you need gitlab runner token in your  `gitlab project` or   `gitlab group`

###  In Group
Group > Settings > CI/CD 
![group](image/group_runner_page.png)

###  In Group
Project > Settings > CI/CD > Runners 
![project](image/project_runner_page.png)

## Usage
Replace your gitlab runner token in `$GITLABTOKEN`

### Instance Type
```typescript
import { GitlabContainerRunner } from 'cdk-gitlab-runner';

// If want change instance type to t3.large .
new GitlabContainerRunner(this, 'runner-instance', { gitlabtoken: '$GITLABTOKEN', ec2type:'t3.large' });
// OR
// Just create a gitlab runner , by default instance type is t3.micro .
import { GitlabContainerRunner } from 'cdk-gitlab-runner';

new GitlabContainerRunner(this, 'runner-instance', { gitlabtoken: '$GITLABTOKEN' });})
```
### Gitlab Server Customize Url .
If you want change  what  you want tag name .
```typescript
// If you want change  what  your self Gitlab Server Url .
import { GitlabContainerRunner } from 'cdk-gitlab-runner';

new GitlabContainerRunner(this, 'runner-instance-change-tag', { gitlabtoken: '$GITLABTOKEN',gitlaburl: 'https://gitlab.my.com/' });
```

### Tags
If you want change  what  you want tag name .
```typescript
// If you want change  what  you want tag name .
import { GitlabContainerRunner } from 'cdk-gitlab-runner';

new GitlabContainerRunner(this, 'runner-instance-change-tag', { gitlabtoken: '$GITLABTOKEN', tag1: 'aa', tag2: 'bb', tag3: 'cc' });
```

### IAM Policy
If you want add runner other IAM Policy like s3-readonly-access.
```typescript
// If you want add runner other IAM Policy like s3-readonly-access.
import { GitlabContainerRunner } from 'cdk-gitlab-runner';
import { ManagedPolicy } from '@aws-cdk/aws-iam';

const runner =  new GitlabContainerRunner(this, 'runner-instance-add-policy', { gitlabtoken: '$GITLABTOKEN', tag1: 'aa', tag2: 'bb', tag3: 'cc' });
runner.runnerRole.addManagedPolicy(ManagedPolicy.fromAwsManagedPolicyName('AmazonS3ReadOnlyAccess'));
```

### Security Group
If you want add runner other SG Ingress .
```typescript
// If you want add runner other SG Ingress .
import { GitlabContainerRunner } from 'cdk-gitlab-runner';
import { Port, Peer } from '@aws-cdk/aws-ec2';

const runner =  new GitlabContainerRunner(this, 'runner-add-SG-ingress', { gitlabtoken: 'GITLABTOKEN', tag1: 'aa', tag2: 'bb', tag3: 'cc' });

// you can add ingress in your runner SG .
runner.runneEc2.connections.allowFrom(Peer.ipv4('0.0.0.0/0'), Port.tcp(80));

```

### Use self VPC
> 2020/06/27 , you can use your self exist VPC or new VPC , but please check your `vpc public Subnet` Auto-assign public IPv4 address must be Yes ,or `vpc private Subnet` route table associated `nat gateway` .
```typescript
import { GitlabContainerRunner } from 'cdk-gitlab-runner';
import { Port, Peer, Vpc, SubnetType } from '@aws-cdk/aws-ec2';
import { ManagedPolicy } from '@aws-cdk/aws-iam';

const newvpc = new Vpc(stack, 'VPC', {
  cidr: '10.1.0.0/16',
  maxAzs: 2,
  subnetConfiguration: [{
    cidrMask: 26,
    name: 'RunnerVPC',
    subnetType: SubnetType.PUBLIC,
  }],
  natGateways: 0,
});

const runner = new GitlabContainerRunner(this, 'testing', { gitlabtoken: '$GITLABTOKEN', ec2type: 't3.small', selfvpc: newvpc });
```

### Use your self exist role
> 2020/06/27 , you can use your self exist role assign to runner
```typescript
import { GitlabContainerRunner } from 'cdk-gitlab-runner';
import { Port, Peer } from '@aws-cdk/aws-ec2';
import { ManagedPolicy, Role, ServicePrincipal } from '@aws-cdk/aws-iam';

const role = new Role(this, 'runner-role', {
  assumedBy: new ServicePrincipal('ec2.amazonaws.com'),
  description: 'For Gitlab EC2 Runner Test Role',
  roleName: 'TestRole',
});

const runner = new GitlabContainerRunner(stack, 'testing', { gitlabtoken: '$GITLAB_TOKEN', ec2iamrole: role });
runner.runnerRole.addManagedPolicy(ManagedPolicy.fromAwsManagedPolicyName('AmazonS3ReadOnlyAccess'));
```
# Note
![](https://img.shields.io/badge/version-1.47.1-green=?style=plastic&logo=appveyor) vs ![](https://img.shields.io/badge/version-1.49.1-green=?style=plastic&logo=appveyor)
> About change instance type

This is before ![](https://img.shields.io/badge/version-1.47.1-green=?style) ( included ![](https://img.shields.io/badge/version-1.47.1-green=?style) ) 
```typescript
import { InstanceType, InstanceClass, InstanceSize } from '@aws-cdk/aws-ec2';
import { GitlabContainerRunner } from 'cdk-gitlab-runner';

// If want change instance type to t3.large .
new GitlabContainerRunner(this, 'runner-instance', { gitlabtoken: '$GITLABTOKEN', ec2type: InstanceType.of(InstanceClass.T3, InstanceSize.LARGE) });
```

This is ![](https://img.shields.io/badge/version-1.49.1-green=?style)
```typescript
import { GitlabContainerRunner } from 'cdk-gitlab-runner';

// If want change instance type to t3.large .
new GitlabContainerRunner(this, 'runner-instance', { gitlabtoken: '$GITLABTOKEN', ec2type:'t3.large' });
```

## Wait about 6 mins , If success you will see your runner in that page .
![runner](image/group_runner2.png)

#### you can use tag `gitlab` , `runner` , `awscdk`  , 
## Example     _`gitlab-ci.yaml`_  
[gitlab docs see more ...](https://docs.gitlab.com/ee/ci/yaml/README.html)
```yaml
dockerjob:
  image: docker:18.09-dind
  variables:
  tags:
    - runner
    - awscdk
    - gitlab
  variables:
    DOCKER_TLS_CERTDIR: ""
  before_script:
    - docker info
  script:
    - docker info;
    - echo 'test 123';
    - echo 'hello world 1228'
```





### If your want to debug you can go to aws console 
# `In your runner region !!!`
## AWS Systems Manager  >  Session Manager  >  Start a session
![system manager](image/session.png)
#### click your `runner` and click `start session`
#### in the brower console in put `bash` 
```bash
# become to root 
sudo -i 

# list runner container .
root# docker ps -a

# modify gitlab-runner/config.toml

root# cd /home/ec2-user/.gitlab-runner/ && ls 
config.toml

```