# API endpoints

These endpoints allow you to handle Stripe subscriptions for Publish and Analyze.

## GET
`official client only` [/1/billing/retrieve-billing-data.json](#post-1billingretrieve-billing-datajson) <br/>

## POST
`official client only` [/1/billing/start-trial.json](#post-1billingstart-trialjson) <br/>
`official client only` [/1/billing/cancel-trial.json](#post-1billingcancel-trialjson) <br/>
`official client only` [/1/billing/start-or-update-subscription.json](#post-1billingstart-or-update-subscriptionjson) <br/>
`official client only` [/1/billing/cancel-subscription.json](#post-1billingcancel-subscriptionjson) <br/>
___

### GET /1/billing/retrieve-billing-data.json
Get basics billing data for the current user. (it has been poorly implemented for now to unblock the Analyze team, and should only be used by Analyze) `official client only`

**Parameters**

|          Name | Required |  Type   | Description                                                                                                                                                           |
| -------------:|:--------:|:-------:| --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|     `product` | required | string  | The product for which to perform the action. <br/><br/> Supported values: `publish` or `analyze`.                                                                     |

**Response**

```
{
    "success": true,
    "data": {
        "has_subscription": false
    }
}

or

{
    "success": true,
    "data": {
        "has_subscription": true,
        "plan_name": "early-access-10", //could be any supported plan
        "cycle": "month|year"
    }
}

or any implemented error from https://buffer.com/developers/api/errors

{
    "code": 1000,
    "error": "An error message"
}
```
___

### POST /1/billing/start-trial.json
Starts a trial for a user. `official client only`

**Parameters**

|          Name | Required |  Type   | Description                                                                                                                                                           |
| -------------:|:--------:|:-------:| --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|     `product` | required | string  | The product for which to perform the action. <br/><br/> Supported values: `publish` or `analyze`.                                                                     |
|        `plan` | required | string  | The plan for which to start the trial period. <br/><br/> Supported values for Publish: `pro`, `small`, `business`, `agency`.  <br/>Supported values for Analyze: `early-access-10`, `early-access-25`, `early-access-50`, `early-access-100`. |
| `trialLength` | optional | integer | Length of the trial in days. <br/><br/>Default is `null`. <br/><br/>If value is null, relies on the product hook logic to define the trial length for the given plan and product.                    |
|       `cycle` | optional | string  | Default is `null`. <br/><br/>If value is null, relies on the product hook logic to define the cycle. <br/><br/> Supported values: `null`, `month` or `year`          |

**Response**

```
{
    "success": true
}

or any implemented error from https://buffer.com/developers/api/errors

{
    "code": 1000,
    "error": "An error message"
}
```
___

### POST /1/billing/cancel-trial.json
Cancels a trial for a user. `official client only`

**Parameters**

|          Name | Required |  Type   | Description                                                                                                                                                         |
| -------------:|:--------:|:-------:| ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|     `product` | required | string  | The product for which to perform the action. <br/><br/> Supported values: `publish` or `analyze`.                                                                   |

**Response**

```
{
    "success": true
}

or any implemented error from https://buffer.com/developers/api/errors

{
    "code": 1000,
    "error": "An error message"
}
```
___

### POST /1/billing/start-or-update-subscription.json
Starts a new subscription or updates an existing one. Can (and should) also be used to complete a trial period. `official client only`

**Parameters**

|          Name | Required |   Type  | Description                                                                                                                                                         |
| -------------:|:--------:|:-------:| ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|     `product` | required | string  | The product for which to perform the action. <br/><br/> Supported values: `publish` or `analyze`.                                                                   |
|        `plan` | required | string  | The plan for which to start a subscription. <br/><br/> Supported values for Publish: `pro`, `small`, `business`, `agency`.  <br/>Supported values for Analyze: `early-access-10`, `early-access-25`, `early-access-50`, `early-access-100`. |
| `stripeToken` | optional | string | Is `required` only the first time when the Stripe customer has no registered credit card. <br/><br/>Stripe tokens are usually generated on the frontend: see [Stripe doc](https://stripe.com/docs/stripe-js/elements/quickstart#create-token) and as an example [Add credit card form Buffer component](https://github.com/bufferapp/buffer-web/blob/master/app/webroot/js/creditCard/components/addCreditCardForm.jsx).<br/><br/>*Stripe will error if we start/update a subscription for a customer who has no credit card: only trials can be started without a credit card.*  <br/> *Please use [/1/billing/start-trial.json](#post-1billingstart-trialjson) to start a trial.*|
|       `cycle` | optional | string | Default is `null`. <br/><br/>If value is null, relies on the product hook logic to define the cycle. <br/><br/> Support values: `null`, `month` or `year`          |

**Response**

```
{
    "success": true
}

or any implemented error from https://buffer.com/developers/api/errors

{
    "code": 1000,
    "error": "An error message"
}
```
___

### POST /1/billing/cancel-subscription.json
Cancels an existing subscription. Will cancel any existing and trialing subscriptions. `official client only`

**Parameters**

|          Name | Required |  Type   | Description                                                                                                                                                         |
| -------------:|:--------:|:-------:| ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|     `product` | required | string  | The product for which to perform the action. <br/><br/> Supported values: `publish` or `analyze`.                                                                   |
| `atPeriodEnd` | optional | boolean | Default is `true`. Specifies if the subscription should be deleted now or when the subscription is due to end. <br/><br/> *Common use case is to pass `true` since we want to let the customers use the full period they paid for.* <br/>*Should only pass `false` (i.e. cancel the subscription right now) when a Stripe customer switches to iOS/Android.)* |

**Response**

```
{
    "success": true
}

or any implemented error from https://buffer.com/developers/api/errors

{
    "code": 1000,
    "error": "An error message"
}
```