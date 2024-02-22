# Open Source Private Assistant on AWS


🇻🇪🇨🇱 [Dev.to](https://dev.to/elizabethfuentes12) [Linkedin](https://www.linkedin.com/in/lizfue/) [GitHub](https://github.com/elizabethfuentes12/) [Twitter](https://twitter.com/elizabethfue12) [Instagram](https://www.instagram.com/elifue.tech) [Youtube](https://www.youtube.com/channel/UCr0Gnc-t30m4xyrvsQpNp2Q)
[Linktr](https://linktr.ee/elizabethfuentesleone)

---

Private Assistant is an application integrated with WhatsApp that allows you to chat with an LLM hosted on Amazon Bedrock and you can also send it voice notes and it will return the transcription of it. 

All data you send to this application will be hosted in your AWS account and will not be shared with anyone or used to retrain models... But the security of data with WhatsApp is not guaranteed, so it is not recommended share private information.

![Digrama parte 1](/imagenes/gif_01.gif)

✅ **AWS Level**: Intermediate - 200   

**Prerequisites:**

- [AWS Account](https://aws.amazon.com/resources/create-account/?sc_channel=el&sc_campaign=datamlwave&sc_content=cicdcfnaws&sc_geo=mult&sc_country=mult&sc_outcome=acq) 
-  [Foundational knowledge of Python](https://catalog.us-east-1.prod.workshops.aws/workshops/3d705026-9edc-40e8-b353-bdabb116c89c/) 

💰 **Cost to complete**: 
- [Amazon Bedrock Pricing](https://aws.amazon.com/bedrock/pricing/)
- [Amazon Lambda Pricing](https://aws.amazon.com/lambda/pricing/)
- [Amazon Transcribe Pricing](https://aws.amazon.com/transcribe/pricing/)
- [Amazon DynamoDB Pricing](https://aws.amazon.com/dynamodb/pricing/)
- [Amazon APIGateway Pricing](https://aws.amazon.com/api-gateway/pricing/)
- [Whatsapp pricing](https://developers.facebook.com/docs/whatsapp/pricing/)

## How The App Works
![Digrama parte 1](/imagenes/flow.jpg)

### 1- Message input:

![Digrama parte 1](/imagenes/1_step.jpg)

1. WhatsApp receives the message: voice/text.
2. [Amazon API Gateway](https://aws.amazon.com/api-gateway/) receives the message from the [WhatsApp webhook](https://business.whatsapp.com/blog/how-to-use-webhooks-from-whatsapp-business-api) (previously authenticated).
3. Then, an [AWS Lambda Functions](https://aws.amazon.com/es/pm/lambda) named [whatsapp_in](/private-assistant/lambdas/code/whatsapp_in/lambda_function.py) processes the message and sends it to an [Amazon DynamoDB](https://aws.amazon.com/pm/dynamodb/) table named whatsapp-metadata to store it.
4. The DynamoDB table whtsapp-metadata has a [DynamoDB streaming](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html) configured, which triggers the [process_stream](/private-assistant/lambdas/code/process_stream/lambda_function.py) Lambda Function.

### 2 - Message processing:

#### Text Message:

![Digrama parte 1](/imagenes/2_step.jpg)
[process_stream](/private-assistant/lambdas/code/process_stream/lambda_function.py) Lambda Function sends the text of the message to the lambda function named [langchain_agent_text](/private-assistant/lambdas/code/langchain_agent_text/lambda_function.py) (in the next step we will explore it).

#### Voice Message:

![Digrama parte 1](/imagenes/2_1_step.jpg)

1. The [audio_job_transcriptor](/private-assistant/lambdas/code/audio_job_transcriptor/lambda_function.py) Lambda Function is triggered. This Lambda Function downloads the WhatsApp audio from the link in the message in an [Amazon S3](https://aws.amazon.com/es/s3/) bucket, using authentication, then converts the audio to text using the Amazon Transcribe start_transcription_job API, which leaves the transcript file in an Output Amazon S3 bucket.

Function that invokes audio_job_transcriptor looks like this:

```python
def start_job_transciptor (jobName,s3Path_in,OutputKey,codec):
    response = transcribe_client.start_transcription_job(
            TranscriptionJobName=jobName,
            IdentifyLanguage=True,
            MediaFormat=codec,
            Media={
            'MediaFileUri': s3Path_in
            },
            OutputBucketName = BucketName,
            OutputKey=OutputKey 
            )
```
            
> ✅ Notice that the IdentifyLanguage parameter is configured to True. Amazon Transcribe can determine the primary language in the audio.
  
![Digrama parte 1](/imagenes/2_2_step.jpg)

2. The [transcriber_done](/private-assistant/lambdas/code/transcriber_done/lambda_function.py) Lambda Function is triggered once the Transcribe Job is complete. It extracts the transcript from the Output S3 bucket and sends it to [whatsapp_out](/private-assistant/lambdas/code/transcriber_done/lambda_function.py) Lambda Function to respond to WhatsApp.

> ✅ You have the option to uncomment the code in the [transcriber_done](/private-assistant/lambdas/code/transcriber_done/lambda_function.py) Lambda Function and send the voice note transcription to [langchain_agent_text](/private-assistant/lambdas/code/langchain_agent_text/lambda_function.py) Lambda Function. 

```Python
try:       
    response_3 = lambda_client.invoke(
        FunctionName = LAMBDA_AGENT_TEXT,
        InvocationType = 'Event' ,#'RequestResponse', 
        Payload = json.dumps({
            'whats_message': text,
            'whats_token': whats_token,
            'phone': phone,
            'phone_id': phone_id,
            'messages_id': messages_id

        })
    )

    print(f'\nRespuesta:{response_3}')

    return response_3
    
except ClientError as e:
    err = e.response
    error = err
    print(err.get("Error", {}).get("Code"))
    return f"Un error invocando {LAMBDA_AGENT_TEXT}
```

### 3- LLM Processing:

## Let's build!

### Step 0: Activate WhatsApp account Facebook Developers

1- [Get Started with the New WhatsApp Business Platform](https://www.youtube.com/watch?v=CEt_KMMv3V8&list=PLX_K_BlBdZKi4GOFmJ9_67og7pMzm2vXH&index=2&t=17s&pp=gAQBiAQB)

2- [How To Generate a Permanent Access Token — WhatsApp API](https://www.youtube.com/watch?v=LmoiCMJJ6S4&list=PLX_K_BlBdZKi4GOFmJ9_67og7pMzm2vXH&index=1&t=158s&pp=gAQBiAQB)

3- [Get started with the Messenger API for Instagram](https://www.youtube.com/watch?v=Pi2KxYeGMXo&list=PLX_K_BlBdZKi4GOFmJ9_67og7pMzm2vXH&index=5&t=376s&pp=gAQBiAQB)


### Step 1: Deploy architecture with CDK.

- Configure the [AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)

- Deploy architecture with CDK [Follow steps:](/private-assistant/README.md)

![Digrama parte 1](/imagenes/arquitectura.png)


✅ **Clone the repo**

```
git clone https://github.com/elizabethfuentes12/aws-private-assistant
```

✅ **Go to**: 

```
cd private-assistant
```

✅ **Create The Virtual Environment**: by following the steps in the [README](/private-assistant/README.md)

```
python3 -m venv .venv
```

```
source .venv/bin/activate
```
for windows: 

```
.venv\Scripts\activate.bat
```

✅ **Install The Requirements**:

```
pip install -r requirements.txt
```

✅ **Synthesize The Cloudformation Template With The Following Command**:

```
cdk synth
```

✅🚀 **The Deployment**:

```
cdk deploy
```

![Deployment Time](/imagenes/deployment_time.jpg)

### Step 2: APP Set Up

In [private_assistant_stack.py](/private-assistant/private_assistant/private_assistant_stack.py) edit this line with the whatsapp Facebook Developer app number: 

`
DISPLAY_PHONE_NUMBER = 'YOU-NUMBER'
`

This agent manages conversation memory, and you must set the session time [here](/private-assistant/lambdas/code/langchain_agent_text/lambda_function.py) in this line:

`
if diferencia > 240:  #session time in seg
`

> **Tip:** [Kenton Blacutt](https://github.com/KBB99), an AWS Associate Cloud App Developer, collaborated with Langchain, creating the [Amazon Dynamodb based memory class](https://github.com/langchain-ai/langchain/pull/1058) that allows us to store the history of a langchain agent in an [Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html?sc_channel=el&sc_campaign=genaiwave&sc_content=working-with-your-live-data-using-langchain&sc_geo=mult&sc_country=mult&sc_outcome=acq).



### Step 3: WhatsApp Configuration

Edit WhatsApp configuration values in Facebook Developer in [AWS Secrets Manager](https://aws.amazon.com/secrets-manager/) [console](https://console.aws.amazon.com/secretsmanager/).

![Digrama parte 1](/imagenes/secret.png)

> ✅ The verification token is any value, but it must be the same in step 3 and 4.

### Step 4: Webhook Configuration

Configure Webhook in the Facebook developer application

![Digrama parte 1](/imagenes/webhook.png)

----

## 🚨 Did you like this blog? 👩🏻‍💻 Do you have comments?🎤 tell me everything[here](https://www.pulse.aws/survey/6V3IYE9H)

----

## ¡Gracias!

🇻🇪🇨🇱 [Dev.to](https://dev.to/elizabethfuentes12) [Linkedin](https://www.linkedin.com/in/lizfue/) [GitHub](https://github.com/elizabethfuentes12/) [Twitter](https://twitter.com/elizabethfue12) [Instagram](https://www.instagram.com/elifue.tech) [Youtube](https://www.youtube.com/channel/UCr0Gnc-t30m4xyrvsQpNp2Q)
[Linktr](https://linktr.ee/elizabethfuentesleone)

---

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.
