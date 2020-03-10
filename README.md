# System-Arthitecture-image-processor-
## Overview
This simple image processor allows user to upload an image in some format and preform combinations of the following operations:
* Flip horizontal and vertical
* Rotate +/- n degrees
* Convert to grayscale
* Resize
* Generate a thumbnail
* Rotate left
* Rotate right

Assumption:
* The target user of this processor is only programmer which means there’s no UI
* There won’t be too many programmers use  the processor so the load balancer is unnecessary at this moment

Concerns:
* Displaying the image on the console
* Authorizing users to upload the image
* Authorizing users to download the image
* Validation the image data (the format and size of the image)
* Processing the image (rotate, clip, resize, convert)
* Generating image variants (thumbnails)
* Storing the images  

Tradeoffs:
* Scaling:
  - In order to avoid the server crashing or timeouts caused by image uploads, the architecture offloading the actual upload to another service (Cloud S3):
* Security:
  - In order to avoid security issue, the system require user authorization
  
How it works:
The programmer use Postman to access our API via REST request. There are two ways for him to access the image, upload a new one or selected an existed one.  For uploading part, he first send a upload request to the UD service, then, the UD service will send the image with its original url to UD API.  After that, UD API will generate a upload url to protect the image won’t be accessed by others who know the original URL and upload the image to Cloud S3. Then,  the user could request an adjustment on the image to Operation Service and the service will ask ImgOperator API to do it by getting the image from the Cloud S3 and by calling different operations to adjust.  After the user finished all adjustment, he needs to sends confirm request to Operation service again and the service will tell ImgOperation APT to replace the original URL of the image with the new one which generate by Operation Service and save  it to the DB.  For downloading part, after receiving the download request from UD Service, the UD API will generate a download URL for security concern and ask Cloud S3 to download it.
