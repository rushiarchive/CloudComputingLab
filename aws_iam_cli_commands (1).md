# AWS IAM CLI Commands -- Proper Format

> **Security Warning:** If real AWS credentials were pasted into a chat
> or shared anywhere, treat them as compromised. Deactivate/delete the
> exposed access key and create a new one before continuing.

## 1. Configure AWS CLI

``` bash
aws configure
```

Enter:

``` text
AWS Access Key ID: <YOUR_ACCESS_KEY>
AWS Secret Access Key: <YOUR_SECRET_KEY>
Default region name: ap-south-1
Default output format: json
```

Verify the current AWS identity:

``` bash
aws sts get-caller-identity
```

------------------------------------------------------------------------

## 2. Create IAM Users

Create `shreyash`:

``` bash
aws iam create-user --user-name shreyash
```

Create `yash`:

``` bash
aws iam create-user --user-name yash
```

------------------------------------------------------------------------

## 3. List IAM Users

``` bash
aws iam list-users
```

There is no `--user-name` option for `list-users`.

To filter for a specific user:

``` bash
aws iam list-users --query "Users[?UserName=='shreyash']"
```

------------------------------------------------------------------------

## 4. Create an IAM Group

``` bash
aws iam create-group --group-name AWS_Group
```

------------------------------------------------------------------------

## 5. Add Users to the Group

Windows CMD:

``` cmd
aws iam add-user-to-group --user-name shreyash --group-name AWS_Group
```

``` cmd
aws iam add-user-to-group --user-name yash --group-name AWS_Group
```

------------------------------------------------------------------------

## 6. Check Users in the Group

``` bash
aws iam get-group --group-name AWS_Group
```

This shows the users that belong to `AWS_Group`.

------------------------------------------------------------------------

## 7. Attach ReadOnlyAccess Directly to a User

For `shreyash`:

``` cmd
aws iam attach-user-policy --user-name shreyash --policy-arn arn:aws:iam::aws:policy/ReadOnlyAccess
```

For `yash`:

``` cmd
aws iam attach-user-policy --user-name yash --policy-arn arn:aws:iam::aws:policy/ReadOnlyAccess
```

------------------------------------------------------------------------

## 8. Attach VPC Read-Only Policy to the Group

Correct AWS managed policy name:

``` text
AmazonVPCReadOnlyAccess
```

Command:

``` cmd
aws iam attach-group-policy --group-name AWS_Group --policy-arn arn:aws:iam::aws:policy/AmazonVPCReadOnlyAccess
```

------------------------------------------------------------------------

## 9. Check Policies Attached to the Group

``` bash
aws iam list-attached-group-policies --group-name AWS_Group
```

------------------------------------------------------------------------

## 10. Check Policies Attached Directly to a User

``` bash
aws iam list-attached-user-policies --user-name shreyash
```

------------------------------------------------------------------------

# Inline Policies

An inline policy is a policy embedded directly into a specific IAM user,
group, or role.

For practicing **group inline policies**, these are the main IAM
permissions required:

``` json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "iam:PutGroupPolicy",
        "iam:GetGroupPolicy",
        "iam:ListGroupPolicies",
        "iam:DeleteGroupPolicy"
      ],
      "Resource": "*"
    }
  ]
}
```

## Inline Policy Permissions

  Permission                Purpose
  ------------------------- -----------------------------------
  `iam:PutGroupPolicy`      Create or update an inline policy
  `iam:GetGroupPolicy`      View an inline policy
  `iam:ListGroupPolicies`   List inline policies
  `iam:DeleteGroupPolicy`   Delete an inline policy

For users:

  Permission               Purpose
  ------------------------ ---------------------------------------
  `iam:PutUserPolicy`      Create or update user's inline policy
  `iam:GetUserPolicy`      View user's inline policy
  `iam:ListUserPolicies`   List user's inline policies
  `iam:DeleteUserPolicy`   Delete user's inline policy

For roles:

  Permission               Purpose
  ------------------------ ---------------------------------------
  `iam:PutRolePolicy`      Create or update role's inline policy
  `iam:GetRolePolicy`      View role's inline policy
  `iam:ListRolePolicies`   List role's inline policies
  `iam:DeleteRolePolicy`   Delete role's inline policy

------------------------------------------------------------------------

# 11. Create a Group Inline Policy

Create a file called:

``` text
policy.json
```

Example:

``` json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeInstances"
      ],
      "Resource": "*"
    }
  ]
}
```

Apply it to `AWS_Group`:

``` cmd
aws iam put-group-policy --group-name AWS_Group --policy-name MyInlinePolicy --policy-document file://policy.json
```

------------------------------------------------------------------------

# 12. List Group Inline Policies

``` cmd
aws iam list-group-policies --group-name AWS_Group
```

------------------------------------------------------------------------

# 13. Get a Group Inline Policy

``` cmd
aws iam get-group-policy --group-name AWS_Group --policy-name MyInlinePolicy
```

------------------------------------------------------------------------

# 14. Delete a Group Inline Policy

``` cmd
aws iam delete-group-policy --group-name AWS_Group --policy-name MyInlinePolicy
```

------------------------------------------------------------------------

# 15. Create a User Inline Policy

Create/update an inline policy for `shreyash`:

``` cmd
aws iam put-user-policy --user-name shreyash --policy-name MyUserInlinePolicy --policy-document file://policy.json
```

List it:

``` cmd
aws iam list-user-policies --user-name shreyash
```

Get it:

``` cmd
aws iam get-user-policy --user-name shreyash --policy-name MyUserInlinePolicy
```

Delete it:

``` cmd
aws iam delete-user-policy --user-name shreyash --policy-name MyUserInlinePolicy
```

------------------------------------------------------------------------

# Complete IAM Flow

``` text
AWS CLI
   |
   v
aws configure
   |
   v
Create IAM users
   |
   v
create-user
   |
   v
Create IAM group
   |
   v
create-group
   |
   v
Add users to group
   |
   v
add-user-to-group
   |
   v
Attach managed policy
   |
   v
attach-group-policy
   |
   v
Create inline policy
   |
   v
put-group-policy
   |
   v
Check / Get / Delete inline policy
```

------------------------------------------------------------------------

# Managed Policy vs Inline Policy

## Managed Policy

A separate policy that can be attached to users, groups, or roles.

Example:

``` cmd
aws iam attach-group-policy --group-name AWS_Group --policy-arn arn:aws:iam::aws:policy/AmazonVPCReadOnlyAccess
```

## Inline Policy

A policy directly embedded into one user, group, or role.

Example:

``` cmd
aws iam put-group-policy --group-name AWS_Group --policy-name MyInlinePolicy --policy-document file://policy.json
```

------------------------------------------------------------------------

# Recommended IAM Practice

For normal user permission management:

``` text
IAM Group
    |
    +-- Managed Policy
    |
    +-- User 1
    |
    +-- User 2
    |
    +-- User 3
```

This is generally easier to manage than attaching permissions
individually to every user.

Inline policies are useful when you need a policy that is specifically
tied to one particular user, group, or role.
