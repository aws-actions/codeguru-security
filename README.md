# Amazon CodeGuru Security for GitHub Actions

Amazon CodeGuru Security finds issues in your code and provides recommendations on how to remediate them. CodeGuru Security identifies [Security vulnerabilities](https://aws.amazon.com/blogs/devops/tightening-application-security-with-amazon-codeguru/), such as risks from the top [10 OWASP categories](https://owasp.org/www-project-top-ten/).

Amazon CodeGuru Security action can be triggered by a pull request, push, or scheduled run of your CI/CD pipeline.

For more information about setup, see [Create code security reviews with GitHub Actions](https://docs.aws.amazon.com/codeguru/latest/security-ug/get-started-github.html) in the *Amazon CodeGuru Security User Guide*.

To add CodeGuru Security to your CI/CD pipeline, follow the steps under the *Usage* section.

## Usage

**Step 1: Set up Your workflow.yml File**

* **Add checkout to your workflow:**

For CodeGuru Security to run, check out your repository using [actions/checkout@v3](https://github.com/actions/checkout). **You will need to set fetch-depth: 0 to fetch all history for all branches and tags.** For example:

```
- name: Checkout repository
  uses: actions/checkout@v3
  with:
    fetch-depth: 0 # This is a required field for CodeGuru
```

* **Provide your AWS Credentials:**

We recommend following the instructions and using [aws-actions/configure-aws-credentials](https://github.com/aws-actions/configure-aws-credentials) to configure your credentials for a job. The IAM user or IAM role requires [AmazonCodeGuruSecurityScanAccess](<url place holder>) policy. The CodeGuru Security action supports credentials from GitHub hosted runners and self-hosted runners.

**Step 2: Add Amazon CodeGuru Security Action**

Input Parameters:

* source_path: **Required**. The source_path is assumed to be the root of the repository (e.g. ".").

* aws_region: **Required**. AWS region where you want to run workflow.

* fail_on_severity: Fails the action run if any finding is higher than or equal to severity is provided. Default the script does not break the action run. [example: Info, Low, Medium, High, Critical]'

```
- name: CodeGuru Security
  uses: aws-actions/codeguru-security@v1
  with:
    source_path: .
    aws_region: eu-north-1
    fail_on_severity: Critical # Critical findings will fail the build
- name: Print findings
  run: |
    ls -l
    cat codeguru-security-results.sarif.json
```

**Step 3: Upload Results to GitHub**

After your job is completed, you can view your results within the AWS Console or GitHub. To view the results in GitHub, we recommend uploading the results generated in the SARIF (Static Analysis Results Interchange Format) into GitHub using the following example codeql-action. For more details, see the upload instructions in the [GitHub documentation](https://docs.github.com/en/code-security/secure-coding/uploading-a-sarif-file-to-github#example-workflow-for-sarif-files-generated-outside-of-a-repository).

```
- name: Upload review result
  uses: github/codeql-action/upload-sarif@v1
  with:
    sarif_file: codeguru-security-results.sarif.json # Your results file will be named codeguru-security-results.sarif.json
```

## Recommendations

After you run the CodeGuru Security Action, security findings are posted on the Security tab in the GitHub UI and in the Finding section of the CodeGuru Security console.

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the Apache-2.0 License. See the [LICENSE](LICENSE) file.
