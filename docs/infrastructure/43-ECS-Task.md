# 4.3 ECS Task Definitions

A task definition is required to run Docker containers in Amazon ECS. The following are some of the parameters that you can specify in a task definition:

1. The Docker image to use with each container in your task
2. How much CPU and memory to use with each task or each container within a task
3. The launch type to use, which determines the infrastructure that your tasks are hosted on
4. The Docker networking mode to use for the containers in your task
5. The logging configuration to use for your tasks
6. Whether the task continues to run if the container finishes or fails
7. The command that the container runs when it's started
8. Any data volumes that are used with the containers in the task
9. The IAM role that your tasks use

---
#### Instructions

Now we will take a look at `ecs-containers.yml` to understand the creation of our ECS Task Definition and observe we point our loadbalancers to the right tasks.

```
AWSTemplateFormatVersion: '2010-09-09'
Description: container on ecs cluster

# Parameters enable you to input custom values to your template each time you create or update a stack.
Parameters:

  FrontendRepository:
    Description: >-
      Provide Frontend ECR repository name
    Type: String
  BackendRepository:
    Description: >-
      Provide Backend ECR repository name
    Type: String

Resources:
  # Define ECS task definition for frontend application
  TaskFrontend:
    Type: AWS::ECS::TaskDefinition
    Properties:
      Family: frontend
      Cpu: 512
      Memory: 1024
      NetworkMode: awsvpc
      RequiresCompatibilities:
        - FARGATE
      ExecutionRoleArn: !ImportValue 'ECSTaskExecutionRole'
      ContainerDefinitions:
        - Name: frontend-container
          Image: !Ref FrontendRepository
          Cpu: 512
          Memory: 1024
          PortMappings:
            - ContainerPort: 3000
              Protocol: tcp
          # Send logs to CloudWatch Logs
          LogConfiguration:
            LogDriver: awslogs
            Options:
              awslogs-region: !Ref AWS::Region
              awslogs-group: !Ref LogGroup
              awslogs-stream-prefix: ecs

  LogGroup:
    Type: AWS::Logs::LogGroup
    Properties:
      LogGroupName: 'loggroup-frontend-td'

  # Define ECS task definition for backend application
  TaskBackend:
    Type: AWS::ECS::TaskDefinition
    Properties:
      Family: backend
      Cpu: 512
      Memory: 1024
      NetworkMode: awsvpc
      RequiresCompatibilities:
        - FARGATE
      ExecutionRoleArn: !ImportValue 'ECSTaskExecutionRole'
      ContainerDefinitions:
        - Name: backend-container
          Image: !Ref BackendRepository
          Cpu: 512
          Memory: 1024
          PortMappings:
            - ContainerPort: 9000
              Protocol: tcp
          # Send logs to CloudWatch Logs
          LogConfiguration:
            LogDriver: awslogs
            Options:
              awslogs-region: !Ref AWS::Region
              awslogs-group: !Ref LogGroupBackend
              awslogs-stream-prefix: ecs
  
  LogGroupBackend:
    Type: AWS::Logs::LogGroup
    Properties:
      LogGroupName: 'loggroup-backend-td'

  # Define ecs service in public subnet to run 
  # frontend task definition
  FrontendService:
    Type: AWS::ECS::Service
    DependsOn: PublicListenerRuleFrontend
    Properties:
      ServiceName: frontend-app
      TaskDefinition: !Ref 'TaskFrontend'
      Cluster: !ImportValue 'ECSCluster'
      LaunchType: FARGATE
      DesiredCount: 1
      DeploymentConfiguration:
        MaximumPercent: 200
        MinimumHealthyPercent: 70
      NetworkConfiguration:
        AwsvpcConfiguration:
          AssignPublicIp: ENABLED
          Subnets:
            - !ImportValue PublicSubnet1
            - !ImportValue PublicSubnet2
          SecurityGroups:
            - !ImportValue FargateContainerSecurityGroup
      LoadBalancers:
        - ContainerName: frontend-container
          ContainerPort: 3000
          TargetGroupArn: !ImportValue PublicTargetGroupFrontend
  
  # Define ecs service in private subnet to run 
  # backend task definition
  BackendService:
    Type: AWS::ECS::Service
    DependsOn: PublicListenerRuleBackend
    Properties:
      ServiceName: backend-app
      TaskDefinition: !Ref 'TaskBackend'
      Cluster: !ImportValue 'ECSCluster'
      LaunchType: FARGATE
      DesiredCount: 1
      DeploymentConfiguration:
        MaximumPercent: 200
        MinimumHealthyPercent: 70
      NetworkConfiguration:
        AwsvpcConfiguration:
          AssignPublicIp: ENABLED
          Subnets:
            - !ImportValue PublicSubnet1
            - !ImportValue PublicSubnet1
          SecurityGroups:
            - !ImportValue FargateContainerSecurityGroup
      LoadBalancers:
        - ContainerName: backend-container
          ContainerPort: 9000
          TargetGroupArn: !ImportValue PublicTargetGroupBackend

  # The rules that you define for your listener determine how the load balancer routes 
  # requests to the targets in one or more target groups.
  PublicListenerRuleFrontend:
    Type: AWS::ElasticLoadBalancingV2::ListenerRule
    Properties:
      ListenerArn: !ImportValue PublicLoadBalancerListener
      Priority: 2
      Conditions:
        - Field: path-pattern
          Values:
            - /*
      Actions:
        - TargetGroupArn: !ImportValue PublicTargetGroupFrontend
          Type: forward
  
  PublicListenerRuleBackend:
    Type: AWS::ElasticLoadBalancingV2::ListenerRule
    Properties:
      ListenerArn: !ImportValue PublicLoadBalancerListener
      Priority: 1
      Conditions:
        - Field: path-pattern
          Values:
            - /api/*
      Actions:
        - TargetGroupArn: !ImportValue PublicTargetGroupBackend
          Type: forward

Outputs:

  FrontendEndpoint:
    Description: Frontend Endpoint
    Value: !Join ['', ['http://', !ImportValue PublicDomainName, '/']]
    Export:
      Name: 'FrontendEndpoint'
  
  BackendEndpoint:
    Description: Backend Endpoint
    Value: !Join ['', ['http://', !ImportValue PublicDomainName, '/api']]
    Export:
      Name: 'BackendEndpoint'
```
---

Proceed to upload this template to Cloudformation to provision our Frontend and Backend tasks. The following is a high level diagram to illustrate what you have just built.

![ecs-task](../../static/img/ecs-task-defintiion.png)

#### Definition of Done

Congratulations! If done correctly you can access your ECS console to observe the tasks that you have created.

You can find the domain in the output tab of the cloudformation console where you created your ecs-task.

1. You should be able to access the backend application from your browser
2. Your frontend application should be able to access your backend application