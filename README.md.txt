The application provides two methods – one for sending information about a new post, which should be converted into an MP3 file, and one for retrieving information about the post (including a link to the MP3 file stored in an S3 bucket). Both methods are exposed as RESTful web services through Amazon API Gateway. Let’s look at how the interaction works in the application.


When the application sends information about new posts:

The information is received by the RESTful web service exposed by Amazon API Gateway. In our scenario, this web service is invoked by a static webpage hosted on Amazon Simple Storage Service (Amazon S3).
Amazon API Gateway sets off a dedicated Lambda function, “New Post,” which is responsible for initializing the process of generating MP3 files.
The Lambda function inserts information about the post into a DynamoDB table, where information about all posts is stored.
To run the whole process asynchronously, we use Amazon SNS to decouple the process of receiving information about new posts and starting their conversion.
Another Lambda function, “Convert to Speech,” is subscribed to our SNS topic whenever a new message appears (which means that a new post should be converted into an audio file). This is the trigger.
The “Convert to Speech” Lambda function uses Amazon Polly to convert the text into an audio file in the specified language (the same as the language of the text).
The new MP3 file is saved in a dedicated S3 bucket.
Information about the post is updated in the DynamoDB table. Then, the reference (URL) to the S3 bucket is saved with the previously stored data.
When the application retrieves information about posts:

The RESTful web service is deployed using Amazon API Gateway. Amazon API Gateway exposes the method for retrieving information about posts. These methods contain the text of the post and the link to the S3 bucket where the MP3 file is stored. In our scenario, this web service is invoked by a static webpage hosted on Amazon S3.
Amazon API Gateway invokes the “Get Post” Lambda function, which deploys the logic for retrieving the post data.
The “Get Post” Lambda function retrieves information about the post (including the reference to Amazon S3) from the DynamoDB table.
