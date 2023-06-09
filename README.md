# Hotspotty Payment Email

The goal of this project is about developing a command line tool that take as input a list of commission reports in csv
format exported from the [Commission report](https://app.hotspotty.net/workspace/helium/commission-reports) section of
Hotspotty platform and send a gmail e-mail to each user inside the input reports to inform them about the
rewards they'll receive.

## Setup your Google account and get the file credentials.json

1. Set up a [Google Cloud Platform](https://cloud.google.com/) project, click on the hamburger menu, and select view all
   products. Under the management
   section, select [APIs and services](https://console.cloud.google.com/apis/dashboard)
2. Next, select [Library](https://console.cloud.google.com/apis/library) and type “Gmail API” in the search bar, and
   click on the Gmail API card.
3. Finally, select the enable
   the [Gmail API](https://console.cloud.google.com/apis/library/gmail.googleapis.com?project=geocaching-366015) button.
4. Now, you’ll need to download the client secrets file for your project. Start by selecting create credentials. In this
   section, select the Gmail API as your preferred API and user data as the type of data you will be accessing.
5. To get the OAuth client ID, select your application type as a Desktop App, set the application name, and select
   create. Next, download and store the credentials in your local file system.

After downloading the secret file, you should have a file in this format:

```json
{
  "installed": {
    "client_id": "463225603869-un1ijjk75iguesbh4nhclm74edprhj5p.apps.googleusercontent.com",
    "project_id": "geocaching-366015",
    "auth_uri": "https://accounts.google.com/o/oauth2/auth",
    "token_uri": "https://oauth2.googleapis.com/token",
    "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
    "client_secret": "TOCSXX-wXkVvnUSGrqC_OcH722jmnFPZHIE",
    "redirect_uris": [
      "http://localhost"
    ]
  }
}
```

## How to use the script

1. Export a one or more contact commissions as show below in the
   [Commission report](https://app.hotspotty.net/workspace/helium/commission-reports) section of Hotspotty:
   ![export_contact_commissions.png](docs/img/export_contact_commissions.png)

2. See script help to get more details about how to use the script. 
   
   ```bash
      hotspotty-payment-email --help
   ```
3. Here below a concrete example which assumes that:
   - You have followed stesps described in [Setup you googl account](#setup-you-googl-account) section and you have a 
     credentials.json file in the local folder.
   - You have 2 contact commissions files exported from hotspotty in the local folder called commission-report-1.csv
     and commission-report-2.csv and these files have ',' as delimiter.
   - You have a jinja emial template called mail_template.html in the local folder.

   ```bash
    hotspotty-payment-email \
     --draft True \
     --subject "Hotspot report from 2023/04/01 to 2023/05/01" \
     --start 2023-04-01 \
     --end 2023-05-01 \
     --template "mail_template.html" \
     --credentials "credentials.json" \
     --delimiter "," \
     --report "commission-report-1.csv" --report "commission-report-2.csv"
   ```

## Sample template e-mail
Here below a sample template e-mail that you can use as a starting point to create your own template e-mail:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
</head>
<body>
	<p>
      Hi {{name}},<br>
      Below the tokens accrued by your hotspot in the following
      period: {{start_date}} - {{end_date}}. Payments will be made to your wallet:
      <a href="https://explorer.solana.com/address/{{wallet_address}}">{{wallet_address}}</a> and in total you will receive:
      <ul>
         {% if amount_hnt > 0 %}
             <li>{{amount_hnt}} HNT.</li>
         {% endif %}
         {% if amount_iot > 0 %}
             <li>{{amount_iot}} IOT.</li>
         {% endif %}
      </ul>
	</p>
    <p>
      Thanks,<br>
      Bye bye
    </p>
</body>
</html>
```

