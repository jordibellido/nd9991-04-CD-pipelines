# Exercise 19: Config and Deployment
## nd9991-04-CD-pipelines

1. Add the contents of your PEM file to the SSH keys in your Circle CI account:
    * Project Settings -> SSH keys -> Additional SSH Keys -> button 'Add SSH Key' -> hostname: (empty); private Key: (copy the content of the PEM file from AWS key pair).
1. Add some environment variables to the Project Settings in Circle CI:
    * AWS_ACCESS_KEY_ID
    * AWS_DEFAULT_REGION
    * AWS_SECRET_ACCESS_KEY
    * AWS_SESSION_TOKEN
1. Manually create an EC2 instance:
    * Amazon Machine Image: Ubuntu Server 20.04 LTS (HVM), SSD Volume Type - ami-09e67e426f25ce0d7 (64-bit x86)
    * Instance Type: t2.micro
    * Auto-assign Public IP: enabled
1. Add a simple ansible configuration file to prevent being blocked by SSH authenticity checking:
    * File: `ansible.cfg`
    * Content:
        ```
        [defaults]
        host_key_checking = False
        ```
1. Push the commit to the git repository and wait for the trigger to start the playbook execution.
1. After the pipeline executes successfully in Circle CI, verify the instance was properly configured.
1. Clean up your EC2 instance by terminating it.
