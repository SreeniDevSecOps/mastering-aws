### Step 1: Create S3 VPC Endpoint
terraform
resource "aws_vpc_endpoint" "s3" {
  vpc_id       = (link unavailable)
  service_name = "com.amazonaws.${aws_region}.s3"
}

resource "aws_vpc_endpoint_policy" "s3_policy" {
  vpc_endpoint_id = (link unavailable)
  policy          = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Sid       = "AllowS3Access"
        Effect    = "Allow"
        Principal = "*"
        Action    = "s3:*"
        Resource  = "*"
      }
    ]
  })
}

### Step 2: Create Fargate ECS Cluster and Task Definition

terraform
resource "aws_ecs_cluster" "example" {
  name = "example-cluster"
}

resource "aws_ecs_task_definition" "example" {
  family                   = "example-task-definition"
  requires_compatibilities = ["FARGATE"]
  network_mode             = "awsvpc"
  cpu                      = 1024
  memory                  = 2048
  execution_role_arn       = aws_iam_role.ecs_task_execution.arn
}

resource "aws_ecs_service" "example" {
  name            = "example-service"
  cluster         = (link unavailable)
  task_definition = aws_ecs_task_definition.example.arn
  launch_type      = "FARGATE"
  network_configuration {
    assign_public_ip = "ENABLED"
    subnets          = [(link unavailable)]
  }
}


### Step 3: Configure Security Groups and IAM Roles

terraform
resource "aws_security_group" "example" {
  name   = "example-sg"
  vpc_id = (link unavailable)
}

resource "aws_iam_role" "ecs_task_execution" {
  name        = "example-ecs-task-execution"
  description = "Example ECS Task Execution Role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Sid       = ""
        Effect    = "Allow"
        Principal = {
          Service = "(link unavailable)"
        }
        Action = "sts:AssumeRole"
      }
    ]
  })
}

resource "aws_iam_policy_attachment" "example" {
  name       = "example-policy-attachment"
  roles      = [aws_iam_role.ecs_task_execution.name]
  policy_arn = aws_iam_policy.example.arn
}

resource "aws_iam_policy" "example" {
  name        = "example-policy"
  description = "Example Policy"

  policy      = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Sid       = "AllowS3Access"
        Effect    = "Allow"
        Action    = "s3:*"
        Resource  = "*"
      }
    ]
  })
}

### Step 4: Configure VPC Endpoint Policy

terraform
resource "aws_vpc_endpoint_policy" "example" {
  vpc_endpoint_id = (link unavailable)
  policy          = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Sid       = "AllowS3Access"
        Effect    = "Allow"
        Principal = "*"
        Action    = "s3:*"
        Resource  = "*"
      }
    ]
  })
}


