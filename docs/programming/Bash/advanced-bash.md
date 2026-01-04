I will be sampling a few bash code snippets that will include some advanced bash scripting techniques. 

??? note "**Sample One: Password Base64 Decoding**"
    This sample script take a Base64 encoded password string and decode it to a plain text password. 

    ```bash
    #!/bin/bash

    # This script decodes a Base64 encoded password string to a plain text password

    # Define the password string
    password_string="cGFzc3dvcmQ="

    # Decode the password string
    password=$(echo $password_string | base64 -d)

    # Print the decoded password
    echo $password
    ```
    
    
??? note "**Sample Two: Ping Multiple IPs**"
    This sample script takes a list of IP addresses and pings them to check if they are alive. 

    ```bash 
    #!/bin/bash

    # This script takes a list of IP addresses and pings them to check if they are alive

    # Define the list of IP addresses
    ips=("192.168.1.1" "192.168.1.2" "192.168.1.3")

    # Loop through the list of IP addresses
    for ip in "${ips[@]}"; do
        # Ping the IP address
        ping -c 1 $ip
        # Check the exit status of the ping command
        if [ $? -eq 0 ]; then
            echo "$ip is alive"
        else
            echo "$ip is not alive"
        fi
    done
    ```


??? note "**Sample Three: Project folder creation**"    
    This sample will automate the creation of your project directory structure and it's files. 

    ```bash     
    #!/bin/bash 
    # This script will automate the creation of your project directory structure and it's files

    # Define the project name
    project_name="my-project"

    # Define the project directory
    project_dir="$HOME/projects/$project_name"

    # Create the project directory
    mkdir -p $project_dir

    project_files=(README.md, LICENSE, CONTRIBUTING.md, CODE_OF_CONDUCT.md, SECURITY.md)

    # Loop through the project files
    for file in "${project_files[@]}"; do
        # Create the file in the project directory
        touch $project_dir/$file
        # Add some content to the file
        echo "This is a sample content for $file" >> $project_dir/$file
    done
    ```
    
??? note "**Sample Four: Password Generator**"
    This sample will automate the creation of a password generator script.

    ```bash
    #!/bin/bash

    # This script will generate a random password

    # Define the length of the password
    echo "Enter the length of the password:"
    read -r password_length

    # Generate a random password

    if [ $password_length -lt 8 ]; then
        echo "Password length must be at least 8"
        exit 1
    else
        password=$(openssl rand -base64 $((password_length * 3/4)) | cut -c 1-$password_length)
    fi

    # Print the generated password
    echo "Generated password: $password"
    ```

    

    



