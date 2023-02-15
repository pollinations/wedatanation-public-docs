# WeDataNation <> Pollinations Manual

## Endpoint
The endpoint to use is:
```
https://worker-prod.pollinations.ai/wedatanation/avatars
```
The endpoint is documented here:
```
https://worker-prod.pollinations.ai/wedatanation/docs
```

Python example to consume the endpoint:
```python
import requests


def get_token():
    return "<your token here>"


def get_wedatanation_request():
    request = {
        "description": "a hippie Sloth with sunglasses",
        "user_id": "niels",
        "num_suggestions": 2,
    }
    return request


def wedatanation_client():
    request = get_wedatanation_request()
    response = requests.post(
        f"{backend_url}/wedatanation/avatar",
        json=request,
        headers={"Authorization": f"Bearer {get_token()}"},
    )
    print(generate_test_token())
    print(response.text)
    print(response)
    avatar = response.json()
    print(avatar)
    # response = requests.post(
    #     f"{backend_url}/wedatanation/avatar/reserve",
    #     json=avatar,
    #     headers={"Authorization": f"Bearer {generate_test_token()}"},
    # )
    # print(response.json())
```

## Token

Tokens can be managed on our dashboard (coming soon). Here you can create and revoke tokens and see usage statistics.


## Content management

There are two folders in an s3 bucket that are relevant for content management:
- [approved avatars](https://s3.console.aws.amazon.com/s3/buckets/pollinations-ci-bucket?region=us-east-1&prefix=meme-avatars/&showversions=false)
- [`raw/`: ready for approval avatars](https://s3.console.aws.amazon.com/s3/buckets/pollinations-ci-bucket?region=us-east-1&prefix=raw/&showversions=false)

The status of available (approved, not reserved) avatars can be checked [here](https://worker-prod.pollinations.ai/wedatanation/available).

Whenever an avatar is requested, we first check the approved folder and only if every avatar has been used, we also serve from the unfiltered avatars. In order to control which avatars are served, you can simply upload or avatars to the approved folder. We use the filename to keep track of which ones have been used already, so please don't change the filename.

In order to manage the content easier, we recommend to download the `raw/` folder, move the approved images to another folder and then upload this folder using the aws cli.

Steps to do this are:
- [Install the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
- Add your credentials to aws by running [TODO]
- open a folder in the terminal to which you would like to download the `raw/` folder
- Run `aws s3 cp --recursive s3://pollinations-ci-bucket/raw/ .`
- Delete all shitty avatars or move all good ones to another folder
- Open the folder with good avatars in your terminal and run `aws s3 cp --recursive . s3://pollinations-ci-bucket/meme-avatars/ --acl public-read` tp upload the approved avatars.
