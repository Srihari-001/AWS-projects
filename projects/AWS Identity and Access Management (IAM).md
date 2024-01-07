# Introduction to AWS Identity and Access Management (IAM) Lab

## Introduction
AWS Identity and Access Management (IAM) is a service for managing user access and permissions in AWS accounts. In this lab, we will explore IAM's foundational aspects, focusing on user and group management and access assignment using IAM-managed policies.

## Solution
1. **Log in to AWS Management Console:**
   - Use provided credentials.
   - Ensure N. Virginia (us-east-1) Region is selected.

2. **Explore Users and Groups Walkthrough:**
   a. **Explore Users:**
      - Navigate to IAM.
      - Select Users.
      - Review details of user-1:
        - Permissions and Groups tabs.
        - Security credentials.
        - Access Advisor tab.
        - User's ARN, path, and creation time.
   b. **Explore Groups:**
      - Navigate to IAM.
      - Select User groups.
      - Review EC2-Admin, EC2-Support, and S3-Support groups.
      - Explore permissions and policies associated with each group.

3. **Add Users to Proper Groups:**
   a. **Add user-1 to S3-Support:**
      - Navigate to IAM.
      - Select User groups.
      - Select S3-Support group.
      - Add user-1.
   b. **Add user-2 to EC2-Support:**
      - Navigate to IAM.
      - Select User groups.
      - Select EC2-Support group.
      - Add user-2.
   c. **Add user-3 to EC2-Admin:**
      - Navigate to IAM.
      - Select User groups.
      - Select EC2-Admin group.
      - Add user-3.

4. **Review Permissions for user-3:**
   - Navigate to IAM.
   - Select Users.
   - Select user-3.
   - Review permissions, including the inline policy.

5. **Use IAM Sign-In Link to Sign In as Each User:**
   a. **Sign In as user-1:**
      - Navigate to IAM Dashboard.
      - Copy sign-in URL.
      - Open a new tab, paste URL, and log in.
      - Attempt S3 and EC2 actions, observe access limitations.
   b. **Sign In as user-2:**
      - Log in using copied Account ID.
      - Attempt EC2 and S3 actions, observe access limitations.
   c. **Sign In as user-3:**
      - Log in using copied Account ID.
      - Attempt EC2 actions, observe access permissions.

## Conclusion
Congratulations! You have completed the hands-on lab for IAM. These steps provide insights into IAM user and group management, access assignments, and real-world use case scenarios.
