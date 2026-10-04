# django-task-manager-aws-ecs
Production-style Django Task Manager deployed on AWS ECS using Docker, Application Load Balancer, and Amazon RDS PostgreSQL.


# Django Task Manager – AWS ECS Deployment

A Django-based Task Manager application containerized using Docker and deployed on AWS ECS with Amazon RDS PostgreSQL and an Application Load Balancer.

The project demonstrates containerized Django deployment, AWS networking, database integration, ECS task configuration, and production-oriented application deployment.

## Architecture

```text
                    Internet
                       |
                       v
                Application Load
                    Balancer
                       |
                       v
                Amazon ECS
              Docker Container
                       |
                       v
                Django + Gunicorn
                       |
                       v
             Amazon RDS PostgreSQL
```

## Technologies Used

### Application

* Python
* Django
* PostgreSQL
* HTML/CSS
* Django Authentication

### Containerization

* Docker
* Dockerfile
* Docker Compose (local development)

### AWS

* Amazon ECS
* Application Load Balancer
* Amazon RDS PostgreSQL
* AWS Secret Manager
* Amazon VPC
* Security Groups
* IAM
* CloudWatch Logs

## Application Features

* User registration and authentication
* User login/logout
* Task creation
* Task management
* Task status management
* Database-backed task storage
* Django admin
* PostgreSQL integration

## AWS Deployment

The application was containerized using Docker and deployed to Amazon ECS.

### Deployment Flow

```text
Django Application
       |
       v
    Docker
       |
       v
   ECS Task
       |
       v
Application Load Balancer
       |
       v
      User
```

The Django application connects to Amazon RDS PostgreSQL using environment variables for database configuration.

## Database Configuration

The application uses PostgreSQL hosted on Amazon RDS.

Database configuration is provided through environment variables:

```text
DB_HOST
DB_PORT
DB_NAME
DB_USER
DB_PASSWORD
```

Example Django configuration:

```python
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql",
        "NAME": os.environ.get("DB_NAME"),
        "USER": os.environ.get("DB_USER"),
        "PASSWORD": os.environ.get("DB_PASSWORD"),
        "HOST": os.environ.get("DB_HOST"),
        "PORT": os.environ.get("DB_PORT", "5432"),
        "OPTIONS": {
            "sslmode": "require",
        },
    }
}
```

Credentials are not stored in the repository.

## Docker

The Django application is packaged as a Docker image.

The container starts Django after applying database migrations:

```bash
python manage.py migrate --noinput
```

Static files are collected during container startup and the application is started using Gunicorn.

## Local Development

Clone the repository:

```bash
git clone <your-repository-url>
cd django-task-manager-aws-ecs
```

Build and start the application:

```bash
docker compose up --build
```

The application can then be accessed at:

```text
http://localhost:5000
```

## Django Database Migrations

Database migrations are automatically applied when the ECS container starts:

```bash
python manage.py migrate --noinput
```

This ensures required Django tables such as `auth_user` are created before the application starts.

## AWS Components

### Amazon ECS

Runs the Dockerized Django application as an ECS task.

### Application Load Balancer

Receives incoming HTTP/HTTPS requests and forwards traffic to the ECS task.

### Amazon RDS

Provides managed PostgreSQL database storage for the Django application.

### IAM

Provides permissions required by ECS and AWS services.

### VPC & Security Groups

Controls network communication between the ALB, ECS tasks, and RDS database.

## Security Considerations

* Database credentials are provided through environment variables.
* RDS is not exposed directly to the public internet.
* Security Groups restrict application and database traffic.
* Django CSRF trusted origins are configured for the deployed application.
* Sensitive credentials are excluded from Git.

## Problems Solved During Deployment

### Django database tables missing

Initial ECS deployment failed because Django authentication tables did not exist.

Error:

```text
relation "auth_user" does not exist
```

This was resolved by applying Django migrations during container startup:

```bash
python manage.py migrate --noinput
```


### CSRF trusted origin

The ECS application URL was added to Django's trusted origins:

```python
CSRF_TRUSTED_ORIGINS = [
    "https://<ecs-application-domain>"
]
```

## Key Learning Outcomes

* Containerizing Django applications
* Deploying Docker containers to Amazon ECS
* Configuring ALB with ECS
* Connecting ECS applications to private RDS PostgreSQL
* AWS Secrets Manager integration
* Managing Django environment variables
* Handling database migrations in containers
* Configuring Django CSRF protection behind AWS infrastructure
* Configuring AWS Security Groups and networking
* Troubleshooting ECS task and ALB health issues
* Understanding container startup and deployment failures

## Future Improvements

* HTTPS using ACM
* Custom domain using Route 53
* ECS service auto scaling
* CI/CD using GitHub Actions
* Infrastructure as Code using Terraform
* CloudWatch monitoring and alarms
* Blue/green deployment
