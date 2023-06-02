# ssm-put-parameter
Composite GitHub action to put parameters to AWS Parameter Store
1) Validates input parameters (if each parameter in list of possible values)
2) Runs `aws ssm put-parameter` command with the parameters

### Parameters

| name | required | possible values | default value |
|------|----------|-----------------|--------------|
| name | true | any | none |
| value | true | any | none |
| description | false | any | "" |
| type | false | ["String", "SecureString", "StringList"] | "String" |
| overwrite | false | ["overwrite", "no-overwrite"] | "overwrite" |
| tier | false | ["Standard" "Advanced", "Intelligent-Tiering"] | "Standard" |
 
### How to use
Before running aws cli should be set up. Can be used many times in the same job on 'step' level. 

e.g.:
```yaml
jobs:
  staging_parameters:
    runs-on: ubuntu-latest
    steps:
      - name: checkout
        uses: actions/checkout@v3

      # set up aws cli before running the action
      - name: setup awscli
        uses: aws-actions/configure-aws-credentials@v1-node16
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: eu-west-1

      # run the action first time
      - name: add_test_string_parameter
        uses: landtechnologies/ssm-put-parameter@v1
        with:
          # input parameters for the first run
          name: /test/parameter/string
          value: test_string_parameter_value
          description: Test string parameter
          type: String
          overwrite: overwrite
          tier: Standard

      # run the action second time
      - name: add_test_secret_parameter
        uses: landtechnologies/ssm-put-parameter@v1
        with:
          # input parameters for the second run
          name: /test/parameter/encoded
          value: ${{ secrets.TEST_SECRET_PARAMETER_VALUE }}
          description: Test encoded parameter
          type: SecureString
          overwrite: overwrite
          tier: Standard
```


