# AWS Config to remove the restricted port from security if it is OpenToAnywhere

![image](https://github.com/kohlidevops/aws-config-to-remove-when-ssh-openend/assets/100069489/b49fd171-90ec-4e89-bd8a-3014f70f0eca)

AWS Config continuously monitors the resources configurations and allows you to automate the evaluation of recorded configurations against desired configurations. 

Go to AWS config in AWS console 

Select add rule 

![image](https://github.com/kohlidevops/aws-config-to-remove-when-ssh-openend/assets/100069489/5681f357-8220-4993-bc95-6683020c2e0b)

Select AWS Managed rules 

![image](https://github.com/kohlidevops/aws-config-to-remove-when-ssh-openend/assets/100069489/aeeb22ba-0388-4ab2-96fa-cd4a7087e605)

AWS Manage rules – Select – restricted-ssh 

Config Rules 

Name – restricted-ssh 

Scope of changes – Resources 

Resource type – AWS EC2 Security Groups 

Next – Create Rule 

Go to actions and select Manage Remediation. 

![image](https://github.com/kohlidevops/aws-config-to-remove-when-ssh-openend/assets/100069489/1acc0e2e-b7eb-473a-abea-e88faed512e5)

Here you have 2 options: Automatic and Manual. Choose Automatic since we want the inbound rule to get deleted automatically. 

![image](https://github.com/kohlidevops/aws-config-to-remove-when-ssh-openend/assets/100069489/b122af6f-f19c-41c9-81ac-17cb2138370b)

Now in Remediation action details choose AWS-DisableIncomingSSHOnPort22 from the drop-down list. Let the other settings be same and come at the Parameter Section. 

Now in the Resource ID Parameter section -choose SecurityGroupIds. Here you will see that the parameters section at the last for securitygroup ids is greyed out. Let that be same. But we also need a AutomationAssumeRole which is required there. 

![image](https://github.com/kohlidevops/aws-config-to-remove-when-ssh-openend/assets/100069489/b0121013-0d9d-4d34-84d6-2776055c6470)

Now why we need this is because in automatic remediation, AWS SYSTEM MANAGER will delete the rule using AWS config. But to perform any action on any resource the System Manager service requires a role which will give the power to do the action to the security group (which is to delete the rule). 

Now first you need to create the roles from IAM service. So open IAM service in a new tab. 

Select roles and create a role. 

Now select AWS Service. 

![image](https://github.com/kohlidevops/aws-config-to-remove-when-ssh-openend/assets/100069489/9cf95b39-53b2-4a61-bf05-c7e8b11359d2)

Then select System Manager from the list 

![image](https://github.com/kohlidevops/aws-config-to-remove-when-ssh-openend/assets/100069489/3abd988a-f09e-452e-a809-6267a0b073a4)

Then again select System Manager from bottom of the page and press Next Permissions. 

![image](https://github.com/kohlidevops/aws-config-to-remove-when-ssh-openend/assets/100069489/a5c25c69-ba87-4234-97a8-48eb8e9125b9)

In policy section, search for the role: AmazonSSMAutomationRole. Select it by ticking from the left and press Next Tags. You can leave the tags for now and select next: review at bottom.

![image](https://github.com/kohlidevops/aws-config-to-remove-when-ssh-openend/assets/100069489/ceb08598-068a-48c3-9c32-27aa9552caaa)

Now give you role any name you want AND press create role. 

After creating the role, go to the role and copy its Role ARN from the top in Summary. 

Now come back to the AWS config screen where it was required to paste the AutomationAssumeRole. Here paste the ARN you copied from the role in the previous step. 

![image](https://github.com/kohlidevops/aws-config-to-remove-when-ssh-openend/assets/100069489/3058373b-af58-448c-a886-bcc879d0b105)

Then select save changes. So now you have specified everything. 

After this try to add a ssh inbound rule to any ec2 instance from your console. 

Now check AWS config rule which you created. 

It will show you the resource which is not following the rule you created. Or we can say — it will show you the resource (here security group) which is not compliant against the rule created and finally it will delete that rule in some seconds Automatically. 

Now to see which resource or security group has ssh allowed — select the rule restricted ssh. 

Then go at the bottom and check in Resources in scope. You will see that the security group id is mentioned there. 

After some seconds or in a minute your ssh rule in security group will delete automatically and here in AWS config, there will be no security group in the resources in scope section.

This is because now the ssh is deleted and hence the resource is following the rule so it is compliant with it. 
