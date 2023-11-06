# aws-private-assistant

![Digrama parte 1](/imagenes/gif_01.gif)

---

🇻🇪🇨🇱 [Dev.to](https://dev.to/elizabethfuentes12) [Linkedin](https://www.linkedin.com/in/lizfue/) [GitHub](https://github.com/elizabethfuentes12/) [Twitter](https://twitter.com/elizabethfue12) [Instagram](https://www.instagram.com/elifue.tech) [Youtube](https://www.youtube.com/channel/UCr0Gnc-t30m4xyrvsQpNp2Q)
[Linktr](https://linktr.ee/elizabethfuentesleone)

---

## Step 0: Activate WhatsApp account Facebook Developers

1- [Get Started with the New WhatsApp Business Platform](https://www.youtube.com/watch?v=CEt_KMMv3V8&list=PLX_K_BlBdZKi4GOFmJ9_67og7pMzm2vXH&index=2&t=17s&pp=gAQBiAQB)

2- [How To Generate a Permanent Access Token — WhatsApp API](https://www.youtube.com/watch?v=LmoiCMJJ6S4&list=PLX_K_BlBdZKi4GOFmJ9_67og7pMzm2vXH&index=1&t=158s&pp=gAQBiAQB)

3- [Get started with the Messenger API for Instagram](https://www.youtube.com/watch?v=Pi2KxYeGMXo&list=PLX_K_BlBdZKi4GOFmJ9_67og7pMzm2vXH&index=5&t=376s&pp=gAQBiAQB)


## Step 1: 

In [private_assistant_stack.py](/private-assistant/private_assistant/private_assistant_stack.py) edit this line with the whatsapp Facebook Developer app number: 

`
DISPLAY_PHONE_NUMBER = 'YOU-NUMBER'`

Deploy architecture with CDK.

Follow steps [here](/private-assistant/README.md)

![Digrama parte 1](/imagenes/arquitectura.png)

## Step 2:

Edit WhatsApp configuration values in Facebook Developer in AWS Secrets Manager.

![Digrama parte 1](/imagenes/secret.png)

## Step 3:

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
