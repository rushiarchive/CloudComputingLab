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
Attach permissions
   |
   v
attach-group-policy
```

## Recommended IAM Practice

Instead of attaching policies individually to every user:

``` text
IAM Group
    |
    +-- Policy
    |
    +-- User 1
    |
    +-- User 2
    |
    +-- User 3
```

This makes permission management easier because the policy is managed at
the group level.
