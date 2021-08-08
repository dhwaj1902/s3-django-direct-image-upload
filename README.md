## S3 direct image upload using Rest API

- Use this code to generate pre-signed URL with Django.(using get request)

<pre>import argparse
import logging
import boto3
from rest_framework import permissions, status
from botocore.exceptions import ClientError
import requests
from rest_framework.response import Response
from django.http import JsonResponse
import random
import uuid

logger = logging.getLogger(__name__)


def generate_presigned_url(s3_client, client_method, method_parameters, expires_in):
    try:
        url = s3_client.generate_presigned_url(
            ClientMethod=client_method,
            Params=method_parameters,
            ExpiresIn=expires_in
        )
        logger.info("Got presigned URL: %s", url)
    except ClientError:
        logger.exception(
            "Couldn't get a presigned URL for client method '%s'.", client_method)
        raise
    return url


def usage_demo(request):
    logging.basicConfig(level=logging.INFO,
                        format='%(levelname)s: %(message)s')

    random_48_bits = random.randint(0, 2**48 - 1)
    key = str(uuid.uuid1(random_48_bits))
    s3_client = boto3.client('s3',    aws_access_key_id=YOUR_AWS_ACCESS_KEY,
                             aws_secret_access_key=YOUR_AWS_ACCESS_SECRET,
                             region_name=YOUR_AWS_REGION
                             )
    client_action = 'put_object'
    url = generate_presigned_url(
        s3_client, client_action, {'Bucket': BUCKET_NAME, 'Key': key}, 1000)
</pre>

- Use ``PUT`` request from Front-end to upload the image to s3 using that pre-signed URL that you get in the previuos step.
