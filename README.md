# Exercise 28: Promote to Production
## nd9991-04-CD-pipelines

1. Add the contents of your PEM file to the SSH keys in your Circle CI account:
    * Project Settings -> SSH keys -> Additional SSH Keys -> button 'Add SSH Key' -> hostname: (empty); private Key: (copy the content of the PEM file from AWS key pair).
1. Add some environment variables to the Project Settings in Circle CI:
    * AWS_ACCESS_KEY_ID
    * AWS_DEFAULT_REGION
    * AWS_SECRET_ACCESS_KEY
    * AWS_SESSION_TOKEN
1. Manually create a public S3 bucket.
1. Create a plain text file `index.html with the following content, then upload it to the brand new S3 bucket and mark it as public:

    ```
    <html>
    <body>
      <h1>Hello World version 1</h1>
    </body>
    </html>
    ```

1. We want to use CloudFormation to modify a CloudFront Distribution later on. It will be MUCH easier to do this if CloudFormation was responsible for creating the distribution in the first place, thus creating a "stack" that can be easily accessed. Create a CloudFormation template file named cloudfront.yml with the following contents in your code repository:

    ```
    Parameters:
      PipelineID:
        Description: Unique identifier.
        Type: String
    
    Resources:
    
      CloudFrontOriginAccessIdentity:
        Type: "AWS::CloudFront::CloudFrontOriginAccessIdentity"
        Properties:
          CloudFrontOriginAccessIdentityConfig:
            Comment: Origin Access Identity for Serverless Static Website
    
      WebpageCDN:
        Type: AWS::CloudFront::Distribution
        Properties:
          DistributionConfig:
            Origins:
              - DomainName: !Sub "${PipelineID}.s3.amazonaws.com"
                Id: webpage
                S3OriginConfig:
                  OriginAccessIdentity: !Sub "origin-access-identity/cloudfront/${CloudFrontOriginAccessIdentity}"
            Enabled: True
            DefaultRootObject: index.html
            DefaultCacheBehavior:
              ForwardedValues:
                QueryString: False
              TargetOriginId: webpage
              ViewerProtocolPolicy: allow-all
    
    Outputs:
      PipelineID:
        Value: !Sub ${PipelineID}
        Export:
          Name: PipelineID
    ```

1. Execute the template with the following command:

    ```
    S3_BUCKET_NAME='exercise28-promote-to-production-20210828-063050'
    
    aws cloudformation deploy \
    --template-file cloudfront.yml \
    --stack-name production-distro \
    --parameter-overrides PipelineID="${S3_BUCKET_NAME}"
    --tags project=udapeople
    ```

1. Create a CloudFormation template named `bucket.yml` that will create a new S3 bucket and copy production files every time we deploy to production. You can use the following template:

    ```
    Parameters:
    NAME:
      Type: String
    Resources:
    WebsiteBucket:
      Type: AWS::S3::Bucket
      Properties:
        BucketName: !Sub "${NAME}"
        AccessControl: PublicRead
        WebsiteConfiguration:
          IndexDocument: index.html
          ErrorDocument: 404.html
    WebsiteBucketPolicy:
      Type: AWS::S3::BucketPolicy
      Properties:
        Bucket: !Ref 'WebsiteBucket'
        PolicyDocument:
          Statement:
          - Sid: PublicReadForGetBucketObjects
            Effect: Allow
            Principal: '*'
            Action: s3:GetObject
            Resource: !Join ['', ['arn:aws:s3:::', !Ref 'WebsiteBucket', /*]]
    ```

1. Push the commit to the git repository and wait for the trigger to start the playbook execution.
1. Run the pipeline successfully.
1. Verify "version 2" is browsable using CloudFront URL.
1. Clean your S3 buckets and CloudFront stacks.
