# AWS CLI Intro

This terminal was located right next to [Jill Underpole](jillunderpole.md).

!!! note
    You may not know this, but AWS CLI help messages are very easy to access. First, try typing:
    $ `aws help`


This command brings up the aws man page.


!!! note
    Great! When you're done, you can quit with q.
    Next, please configure the default aws cli credentials with the access key `AKQAAYRKO7A5Q5XUY2IY`, the secret key `qzTscgNdcdwIo/soPKPoJn9sBrl5eMQQL19iO5uf` and the region `us-east-1`.
    https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-config

Using `aws configure` I went through the motions:

!!! success ""
    ```
    elf@dc3a94f9f683:~$ aws configure
    AWS Access Key ID [None]: AKQAAYRKO7A5Q5XUY2IY
    AWS Secret Access Key [None]: qzTscgNdcdwIo/soPKPoJn9sBrl5eMQQL19iO5uf
    Default region name [None]: us-east-1
    Default output format [None]: 
    ```

!!! note
    Excellent! To finish, please get your caller identity using the AWS command line. For more details please reference:
    $ aws sts help
    or reference:
    https://awscli.amazonaws.com/v2/documentation/api/latest/reference/sts/index.html

This command can be found by perusing the man pages that are displayed after running `aws sts help`. For this, I ran:

!!! success ""
    `#!sh aws sts get-caller-identity`

```json
{
    "UserId": "AKQAAYRKO7A5Q5XUY2IY",
    "Account": "602143214321",
    "Arn": "arn:aws:iam::602143214321:user/elf_helpdesk"
}
```