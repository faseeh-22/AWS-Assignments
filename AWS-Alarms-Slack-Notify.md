# Assignment: 
Send alarm on slack if AWS bill exceeds 10 USD , Send 2nd alarm on 20 USD and 3rd alarm on 50 USD.
Following are the steps to complete the assignment.



## Step 1: Create a Slack App

 **Go to the Slack App Management Page:**
    	Open your web browser and navigate to the Slack App Management page.

 **Create a New App:**
 
  -Click on the "Create New App" button.
     
  -Provide a name for your app and choose the workspace where you want to deploy it. Click on "Create App."





## Step 2: Create an Incoming Webhook

 **Navigate to Incoming Webhooks:**
    	In the left sidebar of your newly created app, find and click on "Incoming Webhooks."

 **Activate Incoming Webhooks:**
    	Toggle the switch to activate incoming webhooks. This allows your app to send messages to channels.

 **Add a New Webhook to Your Workspace:**
    	Scroll down to the "Webhooks" section and click on "Add New Webhook to Workspace."

 **Choose a Channel:**
    	Select the channel where you want to send the alerts. Click "Allow" to grant the necessary permissions.

 **Copy the Webhook URL:**
    	After you've allowed the app to access the selected channel, you'll see a new section with a Webhook URL. Copy this URL.




## Step 3: Replace "YOUR_SLACK_WEBHOOK_URL" in the Script

 **Open the Script in a Text Editor:**
    	Open the shell script (aws_billing_alarm.sh) in a text editor. You can use a command-line editor like nano or a graphical editor like VSCode.

 **Replace the Placeholder:**
   Locate the line where the Slack webhook URL is defined in the script. It should look like this:
    	`slack_webhook_url="YOUR_ACTUAL_SLACK_WEBHOOK_URL"`

   Replace "YOUR_ACTUAL_SLACK_WEBHOOK_URL" with the webhook URL you copied from the Slack App.

 **Save the Script:**
    	Save the changes to the script file.




## Conclusion

With these steps, you have successfully replaced the placeholder in the script with the actual Slack webhook URL.
Now, when the script runs and triggers an alarm, it will send the alert to the specified Slack channel using the provided webhook URL.

To achieve this, you can use the AWS Command Line Interface (AWS CLI) to fetch the billing information and a shell script to send alarms to Slack based on the billing amount. 
Below is an example shell script that you can use. Please note that you need to have the AWS CLI and curl installed on your machine.


```
#!/bin/bash

# Set your AWS profile
AWS_PROFILE="your_aws_profile"

# Set your Slack webhook URL
SLACK_WEBHOOK_URL="your_slack_webhook_url"

# Fetch the current month's estimated charges from AWS
billing_amount=$(aws ce get-cost-and-usage \
	--profile $AWS_PROFILE \
	--time-period "Start=$(date -u '+%Y-%m-01'),End=$(date -u '+%Y-%m-%d')" \
	--granularity MONTHLY \
	--metrics "UnblendedCost" \
	--output json | jq -r '.ResultsByTime[0].Total["UnblendedCost"].Amount')
# Convert billing amount to float
billing_amount_float=$(awk "BEGIN {print $billing_amount+0; exit}")
# Set the threshold amounts
threshold1=10
threshold2=20
threshold3=50
# Check if billing amount exceeds thresholds and send alarms to Slack
if (( $(echo "$billing_amount_float > $threshold1" | bc -l) )); then
	message="AWS bill has exceeded $10. Current amount: $billing_amount_float USD"
	curl -X POST -H 'Content-type: application/json' --data "{\"text\":\"$message\"}" $SLACK_WEBHOOK_URL
elif (( $(echo "$billing_amount_float > $threshold2" | bc -l) )); then
	message="AWS bill has exceeded $20. Current amount: $billing_amount_float USD"
	curl -X POST -H 'Content-type: application/json' --data "{\"text\":\"$message\"}" $SLACK_WEBHOOK_URL
elif (( $(echo "$billing_amount_float > $threshold3" | bc -l) )); then
	message="AWS bill has exceeded $50. Current amount: $billing_amount_float USD"
	curl -X POST -H 'Content-type: application/json' --data "{\"text\":\"$message\"}" $SLACK_WEBHOOK_URL
else
	ok_message="AWS bill is within acceptable limits. Current amount: $billing_amount_float USD"
	curl -X POST -H 'Content-type: application/json' --data "{\"text\":\"$ok_message\"}" $SLACK_WEBHOOK_URL
fi
```


Please make sure to replace the placeholder values for your_aws_profile and your_slack_webhook_url with your actual AWS CLI profile name and Slack webhook URL.
Also, ensure that jq is installed on your system, as it is used for parsing JSON in the script.

To run this script regularly, you can set up a cron job or use a scheduler like systemd timers. Additionally, ensure that your AWS CLI is configured with
the necessary credentials and permissions to access the billing information.



## Steps to set up a Cron Job and Configure AWS CLI:

-Open a terminal on your machine.
-Edit the crontab file using the following command:
 
 `crontab -e`

 This will open the crontab file in the default editor.

-Add a new line at the end of the file to schedule the script execution. For example, to run the script every day at 8 AM, add the following line:

	`0 8 * * * /path/to/your/script.sh`

-Make sure to replace /path/to/your/script.sh with the actual path where your script is located.

-Save the file and exit the editor.



**Configuring AWS CLI:**

-Make sure you have the AWS CLI installed. If not, you can download and install it from the official AWS CLI website.
-Configure the AWS CLI with the necessary credentials and profile. Run the following command:

`aws configure`

You will be prompted to enter your AWS Access Key ID, Secret Access Key, default region, and output format. Enter the required information.

-Ensure that the configured AWS user or role has the necessary permissions to access the AWS Cost Explorer API.
You can attach the AWSCostExplorerReadOnlyAccess policy to your user or role in the AWS Identity and Access Management (IAM) console.

**Note:** If you want to limit permissions further, you can create a custom policy with the minimum required permissions.

-Test your AWS CLI configuration by running a simple command, such as:

	`aws ce get-cost-and-usage --profile your_aws_profile --time-period Start=$(date -d '1 month ago' +%Y-%m-01)T00:00:00Z,End=$(date -d 'today' +%Y-%m-%d)T23:59:59Z --granularity MONTHLY --metrics "UnblendedCost" --output json`

Replace your_aws_profile with the actual profile name you configured.

-Now, the cron job is set up to run the script at the scheduled time, and your AWS CLI is configured to access the billing information.
The script will fetch the AWS billing information and send alerts to Slack based on the specified thresholds.



## How to find your profile name on AWS "your_aws_profile ":

-Your AWS CLI profile name is typically found in the AWS CLI configuration file. The default configuration file is located at `~/.aws/config` on Linux/macOS or `%userprofile%\.aws\config` on Windows.



Here's how you can find your AWS CLI profile name:

**Linux/macOS:**

-Open the AWS CLI configuration file in a text editor. You can use commands like cat or nano:

`cat ~/.aws/config`

-Look for a section that starts with [profile your_aws_profile]. The profile name is the text inside the square brackets. For example:

```
[profile your_aws_profile]
region = us-west-2
output = json
```
In this example, the profile name is your_aws_profile.




**Alternatively:**

If you don't have a named profile in your configuration file and are using the default profile, you can omit the --profile option in the
AWS CLI commands, and it will use the default profile.

**For example:**

`aws configure`

This will set up the default profile. If you've only set up the default profile, you can use commands without specifying a profile:

`aws ce get-cost-and-usage --time-period Start=$(date -d '1 month ago' +%Y-%m-01)T00:00:00Z,End=$(date -d 'today' +%Y-%m-%d)T23:59:59Z --granularity MONTHLY --metrics "UnblendedCost" --output json`

If you still have trouble finding your profile name, you can open the configuration file and look for any profiles or contact your AWS administrator for assistance.




## Which profile to set? :
"
**Set your AWS profile**
AWS_PROFILE="your_aws_profile"
"

In your AWS CLI configuration file, you mentioned having two profiles:

```
[profile demo]
region = us-east-1
[default]
region = us-east-1
output = json
```


You can choose either demo or default as your AWS CLI profile. If you want to use the demo profile, set your AWS_PROFILE variable as follows:

`AWS_PROFILE="demo"`

If you prefer to use the default profile, you can set it as:

`AWS_PROFILE="default"`

Make sure to use the correct profile name based on your preference and AWS CLI configuration.



## In case of error of aws slack-notification script error:

The error message indicates that the AWS CLI is unable to locate the credentials for the specified AWS profile (alarm-bills-user). This typically happens when the AWS CLI is not able to find the necessary access and secret keys for the specified profile.

To resolve this issue, you can take the following steps:

Run aws configure for the specified profile:
Open a terminal and run the following command:

`aws configure --profile alarm-bills-user`

This command will prompt you to enter the AWS Access Key ID, Secret Access Key, default region, and output format. Make sure to enter the correct credentials for the alarm-bills-user profile.

Ensure correct AWS CLI configuration:
After running the aws configure command, verify that the configuration is correct. You can check the `~/.aws/credentials` file to ensure that the credentials are properly configured. It should look something like this:

```
[alarm-bills-user]
aws_access_key_id = YOUR_ACCESS_KEY
aws_secret_access_key = YOUR_SECRET_KEY
region = YOUR_REGION
```


Check AWS CLI version:
Ensure that you are using a version of the AWS CLI that supports the ce (Cost Explorer) commands. You can check your AWS CLI version by running:

	`aws --version`

Make sure it's a recent version that supports the ce commands.

## Verify IAM permissions:
	Ensure that the IAM user associated with the alarm-bills-user profile has the necessary permissions to execute the ce get-cost-and-usage command.

After performing these steps, try running your script again. If the issue persists, double-check the AWS CLI configuration and IAM permissions.





## IN CASE OF POLICY ERROR:

It seems like the policies available for attachment to the alarm-bills-user IAM user currently do not include the necessary permissions for the `ce:GetCostAndUsage` action.
If the policies available in the AWS Management Console do not provide the required access, you may need to create a custom policy and attach it to the IAM user.

Here are the steps to create a custom policy using the AWS Management Console:

-Open the AWS Management Console and navigate to the IAM service.

-In the left navigation pane, click on "Policies" and then click on the "Create policy" button.

-Choose the "JSON" tab and replace the existing JSON with the policy you previously provided:


```
	{
    	"Version": "2012-10-17",
    	"Statement": [
        	{
            	"Effect": "Allow",
            	"Action": [
                	"ce:GetCostAndUsage"
            	],
            	"Resource": "*"
        	}
    	]
	}
```

-Click on the "Review policy" button.

-Provide a name and optional description for the policy, and click on the "Create policy" button.



Now that you have created the custom policy, you can attach it to the alarm-bills-user IAM user:

-Navigate to the IAM service.

-Select "Users" from the left navigation pane.

-Find and click on the alarm-bills-user user.

-Go to the "Permissions" tab.

-Click on the "Attach policies" button.

-Search for the policy by entering its name or description (the one you just created).

-Select the policy and click on the "Attach policy" button.

-After attaching the custom policy, try running your script again. The IAM user should now have the necessary permissions to perform the `ce:GetCostAndUsage` action.








## Verification command or general command for aws slack-notification:

Below is a verfication or for checking date of aws account for script of aws slack notification.


```
aws ce get-cost-and-usage \
	--time-period "Start=$(date -u -d '1 month ago' '+%Y-%m-01'),End=$(date -u '+%Y-%m-%d')" \
	--granularity MONTHLY \
	--metrics "UnblendedCost" \
	--output json
```

