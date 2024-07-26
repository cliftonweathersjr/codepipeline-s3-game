# Continuous Deployment using AWS CodePipeline and S3

In this project, I will be building a memory card game (using images) using HTML, Javascript and CSS files. The website will be hosted statically through an S3 bucket and the code will live in GitHub. I will use AWS CodePipeline to pull from GitHub and deploy to S3 bucket.


# Step One: Configure S3 Bucket

Navigate to S3 using the search in the AWS Management Console. Once there, use the `Create Bucket` to begin configuring the new bucket. After naming the bucket, `Block all public access` will need to be unchecked so that the static website can be available to the world. (Usually, when building something for work or private use, this option would say checked). After unchecking the box, acknowledge that the current settings and it's risks. Then, create bucket.

Before leaving, changes will need to be made to the properties of the newly created bucket. Under the properties settings for the bucket, Static website hosting will need to be enabled. Choose the edit option for the section and select enable. The home/default page will need to be specified as `index.html` and then save changes.

The permissions for the bucket will also need to be updated with a bucket policy. Using the policy below, the S3 bucket will allow everything to be read/viewed from everyone. Ensure to update the `Bucket-Name`.

```
{
    "Version": "2012-10-17",
    "Statement": [
    	{
        	"Sid": "PublicReadGetObject",
        	"Effect": "Allow",
        	"Principal": "*",
        	"Action": [
            	"s3:GetObject"
        	],
        	"Resource": [
                "arn:aws:s3:::Bucket-Name/*"
        	]
    	}
    ]
}
```

# Step Two: Create CodePipeline

From the console, navigate to the CodePipeline service. Begin customizing a new pipeline using `Create Pipeline`. Once the pipeline is named, `Queued` will be selected for the Execution mode. V1 pipelines can no longer be created through the console so the Pipeline type will default to V2.

Create a new service role, name and make sure the box is checked for `Allow AWS CodePipeline to create a service role so it can be used with this new pipeline`. Scroll down and click next.

The source provider will be `GitHub (Version 2)`. Once chosen, select the `Connect to GitHub` option. It will open a pop-up and ask for a connection name and to connect to GitHub again. Once connected, select the `Install a new app` option and the GitHub profile will populate. Access can then be given for all repositories or only select repositories. Select `Only select repositories` and from the drop-down menu, select the repository that possesses the files needed for the game. After hitting save, the pop-up will ask once again to connect but with the new app selected in the previous screen.

After being brought back to the main page, the app that was connection should populate under connection. Repository name and Default branch should also have the appropiate option populated in the drop-down menus. The next step would be to set up the trigger for the deployment. This will decide what causes the pipeline to redeploy the project with updates. For this project, The trigger type will be `specify filter`, the event type will be `push`, and the filter type will be `branch` and the branch that will be included is `main`. Then select next.

When asking for build provider, select skip stage. Next, a deploy provider will need to be selected and that will be Amazon S3. Choose region and select the appropiate bucket afterwards. Check the box for `Extract file before deploy` and select next. Review all the information for the pipelineand click `Create pipeline`.

The pipeline will then show you the progress of the deployment. Once it finishes deploying, you will then be able to access the site using the link made by the S3. To find this link, navigate to the properties tab under the S3 bucket used for this project and scroll down to the Static website hosting section. A link will be present and will take you to the completed deployment of the matching game.

The purpose of this set up using AWS CodePipelin and Github is to create a pipeline that allows for the deployment of changes in the file to be automated. To test this, you can edit the index.html file residing in your GitHub repository and commit the changes. Once the commit has been pushed, you can check the Pipeline that you created and notice that it has already begun the redploy to include the new changes. After finsihing, the changes should be present with a refresh.
