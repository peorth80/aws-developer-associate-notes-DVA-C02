#### CodeCommit
- **Version Control** is the ability to understand changes that happened to the code over time (and possibly roll back)
- This is enabled by using a version control system such as Git
- A git repository can live on your machine or on a central online repository
- Benefits:
    - Collaborate with a team of developers
    - Make sure the code is backed-up somewhere
    - Makes sure it's fully viewable and auditable
- AWS CodeCommit
    - Private git repositories
    - No size limits on the repositories (scale seamlessly)
    - Fully managed and highly available
    - The code is only in your AWS cloud account
    - Increased security and compliance
    - Secure (encrypted access control, etc)
- CodeCommit Security
    - Interactions are done using git
    - Authentication with Git
        - SSH Keys: AWS Users can configure SSH keys in their IAM Console
        - HTTPS: Done through AWS CLI Authentication helper or generate HTTPS credentials
        - MFA: Multi-Factor Authentication
    - Authorization with Git
        - IAM Policies manage user / roles rights to the repositories
    - Encryption
        - Repositories are automatically encrypted at rest using KMS
        - Encrypted in transit (can only user HTTPS or SSH)
    - Cross Account access:
        - Do not share your SSH keys
        - Do not share your AWS credentials
        - Use IAM Role in your AWS account and use AWS STS (with AssumeRole API)  
- Github vs. CodeCommit
    - Similarities
        - Github and CodeCommit can be integrated with AWS CodeBuild
        - Both support HTTPS and SSH authentication
    - Differences
        - Security
            - Github: Github Users
            - CodeCommit: AWS IAM users & roles
        - Hosted
            - Github: hosted by Github
            - CodeCommit: managed & hosted by AWS
        - UI
            - Github UI is fully featured
            - CodeCommit is minimal
- CodeCommit notifications
    - You can trigger notifications in CodeCommit using AWS SNS (Simple Notification Service) or AWS Lambda or AWS CloudWatch Event Rules
    - Use cases for notifications SNS / AWS Lambda notifications:
        - Deletion of branches
        - Trigger for pushes that happens in master branch
        - Notify external Build System
        - Trigger AWS Lambda function to perform codebase analysis (maybe credentials got committed in the code?)
    - Use cases for CloudWatch Event Rules:
        - Trigger for pull request updates (created / updated / deleted / commented)
        - Commit comment events
        - CloudWatch Event Rules goes into an SNS topic

**NOTE**: CodeCommit has been deprecated and you can only use it if you have repos on your account. You can't create new repos.