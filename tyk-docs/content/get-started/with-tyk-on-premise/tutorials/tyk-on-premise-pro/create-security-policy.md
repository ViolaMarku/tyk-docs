---
date: 2017-03-23T11:11:18Z
title: Create a security policy - Pro Edition
menu:
  main:
    parent: "Pro Edition"
    identifier: pro-edition-create-security-policy
weight: 3
---

## <a name="with-dashboard"></a>Tutorial: Create a security policy with the Dashboard

To create a security policy with the dashboard, follow these steps:

### Step 1: Select "Policies" from the "System Management" section

![Policies menu link location][1]

### Step 2: Click Add Policy

![Add policy button location][2]

This page lists out all the policies that you have created. Once you have reached the policies list you need to click the *add policy* button.

### Step 3: Give your policy a name

![Policy name form][3]

All policies require a descriptive name, this helps you to reference it later, and it will appear in drop-down options where you can attach policies to objects such as tokens or OAuth client IDs.

### Step 4: Set Rate limits

![Rate limit form][4]

A rate limit is enforced on all keys, set the number of requests per second that a user of a key with this policy is allowed to use.

> **NOTE:** The Rate Limit set by a policy will override the limits applied to an individual key.

### Step 5: Set Usage Quotas

![Quota form][5]

Usage quotas limit the number of total requests a user is allowed to have over a longer period of time. So while a rate limit is a rolling window, a quota is an absolute maximum that a user is allowed to have over a week, a day or a month.
**NOTE:** The Usage Quota set by a policy will override a quota applied to an individual key.

### Step 6: Add a security entry

![Access rights form][6]

**Required** - A security entry is required for all policies (even partitioned ones) as we need to ensure access is always explicit for APIs managed by Tyk.

### Step 7: Save the policy

![Policy partitions form][7]

To make the policy active, click **Create** . Once the policy is saved, you will be able to use it when generating keys, OAuth clients and custom JWT tokens.

## <a name="with-api"></a>Tutorial: Create a Policy with the Dashboard API

To create an API security policy using the API is a single call. It is very similar to the token creation object. To generate a simple security policy using the Tyk Dashboard API you can use the following curl command:

```{.copyWrapper}
    curl -X POST -H "authorization: {API-TOKEN}" \
     -s \
     -H "Content-Type: application/json" \
     -X POST \
     -d '{
          "access_rights": {
            "{API-ID}": {
              "allowed_urls": [],
              "api_id": "{API-ID}",
              "api_name": "{API-NAME}",
              "versions": [
                "Default"
              ]
            }
          },
          "active": true,
          "name": "POLICY NAME",
          "rate": 100,
          "per": 1,
          "quota_max": 10000,
          "quota_renewal_rate": 3600,
          "tags": ["Startup Users"]
        }' https://{your-dashboard-domain}:{port}/api/portal/policies | python -mjson.tool
```

You must replace:

*   `{API-TOKEN}`: Your API Token for the dashboard API.
*   `{API-ID}`: The API ID you wish this policy to grant access to, there can be more than one of these entries.
*   `{API-NAME}`: The name of the API that is being granted access to (this is not required, but helps when debugging or auditing).
*   `POLICY NAME`: The name of this security policy.

The main elements that are important are:

*   `access_rights`: A list of objects representing which APIs that you have configured to grant access to.
*   `rate` and `per`: The number of requests per second to allow.
*   `quota_max`: The maximum number of allowed requests over a quota period.
*   `quota_renewal_rate`: How often the quota resets, in seconds. In this case we have set it to renew every hour.

When you send this request, you should see the following reply with your policy ID:

```
    {
        "Message": "577a8589428a6b0001000017",
        "Meta": null,
        "Status": "OK"
    }
```

You can then use this policy ID in the `apply_policy_id` field of an API Token. Please see the relevant documentation on session objects for more information about how tokens are attached to policies.

For more information on how policies are constructed and a detailed explanation of their properties, please see the Security Policies section.

 [1]: /docs/img/dashboard/system-management/nav_policies.png
 [2]: /docs/img/dashboard/system-management/AddPolicyButton.png
 [3]: /docs/img/dashboard/system-management/policyNameField.png
 [4]: /docs/img/dashboard/system-management/rateLimit.png
 [5]: /docs/img/dashboard/system-management/usageQuotas.png
 [6]: /docs/img/dashboard/system-management/securityEntry.png
 [7]: /docs/img/dashboard/system-management/savePolicy.png

