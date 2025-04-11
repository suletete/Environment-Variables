```markdown
# Mini Project - Understanding Environment Variables & Infrastructure Environments: Key Differences

As we delve deeper into the world of technology and its building blocks, two essential concepts often come to the forefront: "Infrastructure Environments" and "Environment Variables." Despite both terms featuring "Environment," they play distinct roles in the realm of scripting and software development. This common terminology can lead to confusion, making it crucial to distinguish and understand each concept from the outset.

## Infrastructure Environments

Infrastructure environments refer to the various settings where software applications are developed, tested, and deployed, each serving a unique purpose in the software lifecycle.

Let’s say you are working with a development team to build a FinTech product. They have two different AWS accounts. The journey would be something like:

- **VirtualBox + Ubuntu**: The development environment where all local development is done on your laptop.
- **AWS Account 1**: The testing environment, where, after local development is completed, the code is pushed to an EC2 instance here for further testing.
- **AWS Account 2**: The production environment, where, after tests are completed in AWS Account 1, the code is pushed to an EC2 instance in AWS Account 2, where customers consume the FinTech product through a website.

Each setup is considered an Infrastructure Environment.

## Environment Variables

On the other hand, environment variables are key-value pairs used in scripts or computer code to manage configuration values and control software behavior dynamically.

Imagine your FinTech product needs to connect to a database to fetch financial data. However, the details of this database connection, like the database URL, username, and password differ between your development, testing, and production environments.

If you need to develop a shell script that will be reused across all the 3 different environments, then it is important to dynamically fetch the correct value for your connectivity to those environments.

Here’s how environment variables come into play:

### Development Environment (VirtualBox + Ubuntu):
```bash
DB_URL=localhost
DB_USER=test_user
DB_PASS=test_pass
```
Here, the environment variables point to a local database on your laptop where you can safely experiment without affecting real or test data.

### Testing Environment (AWS Account 1):
```bash
DB_URL=testing-db.example.com
DB_USER=testing_user
DB_PASS=testing_pass
```
In this environment, the variables are configured to connect to a remote database dedicated to testing. This ensures that tests are performed in a controlled environment that simulates production settings without risking actual customer data.

### Production Environment (AWS Account 2):
```bash
DB_URL=production-db.example.com
DB_USER=prod_user
DB_PASS=prod_pass
```
Finally, when the application is running in the production environment, the environment variables switch to ensure the application connects to the live database. This is where real customer interactions happen, and the data needs to be accurate and secure.

By clarifying these differences early on, we set a solid foundation for navigating the complexities of technology development with greater ease and precision.

## aws_cloud_manager.sh Script

By the end of this mini project, we would have started working on the `aws_cloud_manager.sh` script, where environment variables will be defined, and command-line arguments are added to control if the script should run for local, testing, or production environments.

### Initial Script Setup
```bash
#!/bin/bash

# Checking and acting on the environment variable
if [ "$ENVIRONMENT" == "local" ]; then
  echo "Running script for Local Environment..."
  # Commands for local environment
elif [ "$ENVIRONMENT" == "testing" ]; then
  echo "Running script for Testing Environment..."
  # Commands for testing environment
elif [ "$ENVIRONMENT" == "production" ]; then
  echo "Running script for Production Environment..."
  # Commands for production environment
else
  echo "No environment specified or recognized."
  exit 2
fi
```

### Adding Permissions
```bash
sudo chmod +x aws_cloud_manager.sh
```

You can test the script by setting the `ENVIRONMENT` variable dynamically:
```bash
export ENVIRONMENT=production
./aws_cloud_manager.sh
```
This will output:
```bash
Running script for Production Environment...
```

### Dynamic Environment Selection
Alternatively, you can hard-code the environment variable:
```bash
#!/bin/bash

# Initialize environment variable
ENVIRONMENT="testing"

# Checking and acting on the environment variable
if [ "$ENVIRONMENT" == "local" ]; then
  echo "Running script for Local Environment..."
elif [ "$ENVIRONMENT" == "testing" ]; then
  echo "Running script for Testing Environment..."
elif [ "$ENVIRONMENT" == "production" ]; then
  echo "Running script for Production Environment..."
else
  echo "No environment specified or recognized."
  exit 2
fi
```

However, this is not dynamic, and using command-line arguments is a better approach.

### Positional Parameters in Shell Scripting

Positional parameters allow passing arguments to scripts at runtime, making the script flexible. For example:
```bash
./aws_cloud_manager.sh testing
```
Inside the script:
```bash
ENVIRONMENT=$1
```

For multiple parameters:
```bash
./aws_cloud_manager.sh testing 5
```
Inside the script:
```bash
ENVIRONMENT=$1
NUMBER_OF_INSTANCES=$2
```

### Argument Validation

To ensure the script gets the correct number of arguments:
```bash
if [ "$#" -ne 1 ]; then
  echo "Usage: $0 <environment>"
  exit 1
fi
```

Updated script:
```bash
#!/bin/bash

# Checking the number of arguments
if [ "$#" -ne 1 ]; then
  echo "Usage: $0 <environment>"
  exit 1
fi

# Accessing the first argument
ENVIRONMENT=$1

# Acting based on the argument value
if [ "$ENVIRONMENT" == "local" ]; then
  echo "Running script for Local Environment..."
elif [ "$ENVIRONMENT" == "testing" ]; then
  echo "Running script for Testing Environment..."
elif [ "$ENVIRONMENT" == "production" ]; then
  echo "Running script for Production Environment..."
else
  echo "Invalid environment specified. Please use 'local', 'testing', or 'production'."
  exit 2
fi
```
