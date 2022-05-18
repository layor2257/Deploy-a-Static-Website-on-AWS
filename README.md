# Deploy-a-Static-Website-on-AWS
## Hosting a static website on S3 and Accessing the cached website pages using CloudFront content delivery network (CDN) service.

### Create S3 Bucket

- Navigate to the "AWS Management Console" Page, type "S3" in the "Find Service" box and then select "S3" and click on "Bucket"

![image](https://user-images.githubusercontent.com/49678841/169008769-60cf67dd-c3c1-4d67-a3ae-56f8c166f215.png)

- The Amazon S3 dashboard displays. Click “Create bucket”

![image](https://user-images.githubusercontent.com/49678841/169010280-74635978-d195-41e6-b8ec-313c88324d66.png)

     In the General configuration, enter a “Bucket name” and a region of your choice. Note: Bucket names must be globally unique.

  - In the **Bucket settings for Block Public Access** section, uncheck the “Block all public access”. It will enable the public access to the bucket objects via the S3 object URL.

_**Note** - We are allowing the public access to the bucket contents because we are going to host a static website in this bucket. Hosting requires the content should be publicly readable_.

![image](https://user-images.githubusercontent.com/49678841/169014356-26079465-0737-4630-8101-95cc2eaa0df3.png)


Click “Next” and click “Create bucket”.

Once the bucket is created, click on the name of the bucket to open the bucket to the contents.

![image](https://user-images.githubusercontent.com/49678841/169010772-43b27aed-f059-4137-a9e1-020378070ab3.png)

I created my "S3" bucket called "layorstatic2257bucket"


### Upload files to the S3 bucket

- Once the bucket is open to its contents, click the “Upload” button.

![image](https://user-images.githubusercontent.com/49678841/169016003-008321b8-88ba-4b2c-9c9d-38bf65ae4b5d.png)

- Click the "Add files" and “Add folder” button, and upload the [Student-ready starter code](https://drive.google.com/open?id=15vQ7-utH7wBJzdAX3eDmO9ls35J5_sEQ) content from your local computer to the S3 bucket.

- Click "Add files" to upload the `index.html` file, and click 'Add folder' to upload the `css`,`image` and `vendor` folders.

![image](https://user-images.githubusercontent.com/49678841/169020260-a0e1f525-6323-4771-a133-b21c47e23432.png)


### Secure bucket via IAM

- Click on the “Permissions” tab.

![image](https://user-images.githubusercontent.com/49678841/169020663-b7e89827-2b58-4bad-ad37-906893f872d4.png)

- The “Bucket Policy” section shows it is empty. Click on the Edit button.

![image](https://user-images.githubusercontent.com/49678841/169027585-640539b7-9572-460c-91f0-8ca4f502ca33.png)
Empty bucket policy. Check this policy again after setting up the CloudFront distribution.

- Enter the following bucket policy replacing `your-website` with the name of your bucket and click “Save”.
```JSON
{
"Version":"2012-10-17",
"Statement":[
 {
   "Sid":"AddPerm",
   "Effect":"Allow",
   "Principal": "*",
   "Action":["s3:GetObject"],
   "Resource":["arn:aws:s3:::your-website/*"]
 }
]
}
```
You will see warnings about making your bucket public, **but this step is required for static website hosting**.

### Configure S3 bucket

- Go to the Properties tab and then scroll down to edit the Static website hosting section.

![image](https://user-images.githubusercontent.com/49678841/169028806-1cbfae34-f27b-4766-9bb2-ebc1e31c6284.png)

![image](https://user-images.githubusercontent.com/49678841/169028900-4315ff4f-0741-4184-b45e-6750b174308c.png)

- Click on the “Edit” button to see the **Edit static website hosting** screen. Now, enable the **Static website hosting**, and provide the default home page and error page for your website.

![image](https://user-images.githubusercontent.com/49678841/169029189-19dfc4f4-b038-4781-9ba3-40189e3c3591.png)

**Specify your index document (must be the same as the index.html file so in my case it would be index.html)
Specify your error document (must be the same as the index.html file so in my case it would be index.html)**

[Link that explain above point](https://stackoverflow.com/questions/44382709/aws-s3-bucket-404-not-found)

_Did you notice that enabling the static website hosting requires you to make your bucket public?
In the snapshot above, it says "For your customers to access the content at the website endpoint, you must make all your content **publicly readable**."_

- After successfully saving the settings, check the **Static website hosting** section again under the **Properties** tab. You must now be able to view the [website endpoint](https://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteEndpoints.html) as shown below

![image](https://user-images.githubusercontent.com/49678841/169030238-f03f962a-83b8-432d-8871-586af730a773.png)

### Distribute website via cloudfront

- Select “Services” from the top left corner and enter “cloud front” in the “Find a service by name or feature” text box and select “CloudFront”.
![image](https://user-images.githubusercontent.com/49678841/169030518-bd56cb1e-d0ac-4199-9040-92e274db0d0d.png)

- From the CloudFront dashboard, click “Create a cloudfront Distribution”.
![image](https://user-images.githubusercontent.com/49678841/169030667-b61c39c4-f282-4b11-ad97-63965e107049.png)

- Use the following details to create a distribution:
![image](https://user-images.githubusercontent.com/49678841/169030923-9911e188-bd68-483b-820d-c15b6a1fb90f.png)

![image](https://user-images.githubusercontent.com/49678841/169031034-4fd59538-7f73-482f-aafe-d88f5e319e7a.png)

![image](https://user-images.githubusercontent.com/49678841/169031149-9184920d-c8e4-4a11-97b0-8d137d04b046.png)
                      Configurations - Origin details
 
 ![image](https://user-images.githubusercontent.com/49678841/169031349-0a7c5638-f574-47ba-a1ef-4bd049a96ea2.png)
                     Configurations - Cache behavior, key and origin requests
  
  - Leave the defaults for the rest of the options, and click “Create Distribution”. It may take up to 10 minutes for the CloudFront Distribution to get created.
  
  
  _**Note** - Remember, as soon as your CloudFront distribution is **Deployed**, it attaches to S3 and starts caching the S3 pages. CloudFront may take 10-30 minutes (or more) to cache the S3 page. Once the caching is complete, the CloudFront domain name URL will stop redirecting to the S3 object URL_.
  
  ![image](https://user-images.githubusercontent.com/49678841/169031800-34c54b06-04e1-483a-bbda-940ab106af21.png)

           
### Access website in a browser

_**Note** - In the steps below, the exact domain name and the S3 URLs will be different in your case._

- Open a web browser like Google Chrome, and paste the copied CloudFront domain name (such as, `d2bsi81nksibhz.cloudfront.net`) **without appending** at the end. The CloudFront domain name should show you the content of the default home-page, as shown below: 

- Access the website via website-endpoint, such as `http://layorstatic2257bucket.s3-website-us-east-1.amazonaws.com`

- Access the bucket object via its S3 object URL, such as,`http://layorstatic2257bucket.s3-website-us-east-1.amazonaws.com`

![image](https://user-images.githubusercontent.com/49678841/169040053-fb62ed5d-f079-4559-b0da-6e6455a6bf7f.png)




