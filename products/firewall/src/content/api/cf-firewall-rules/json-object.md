---
title: JSON object
weight: 315
---
### Firewall rule object structure and properties
A fully populated firewall rule JSON object has the following structure:

```json
{
  "id": "772bf1026a72c400ea576db1ffa16407",
  "filter": {
    "id": "6f58318e7fa2477a23112e8118c66f61",
    "expression": "http.request.uri.path ~ \"^.*/wp-login.php$\" or http.request.uri.path ~ \"^.*/xmlrpc.php$\""
    "paused": false,
    "description": "Wordpress login paths",
    "ref": ""
  },
  "action": "challenge",
  "priority": 1000,
  "paused": false,
  "description": "Protect blog login page",
  "ref": ""
}
```

The table below summarizes the JSON object properties.  See also, [Avoiding priority conflicts](#avoiding-priority-conflicts).

<table style="border: solid 2px darkgrey; width:70%;">
    <thead style="background:#ffeadf;">
        <tr>
            <th>Property</th>
            <th style="width:30%">Description</th>
            <th style="width:30%">Value</th>
            <th>Notes</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>id</td>
            <td>- ID generated by Cloudflare</td>
            <td>32-char UUIDv4 identifier</td>
            <td>
                - Unique
                <br/> - Read-only
            </td>
        </tr>
        <tr>
            <td>filter</td>
            <td>- An existing Cloudflare filter</td>
            <td><em>See <a href="/firewall/api/cf-filters/">the Cloudflare Filters guide</a></em></td>
            <td>- Required</td>
        </tr>
        <tr>
            <td>action</td>
            <td>- The firewall action to perform</td>
            <td><em>
log<br/>
bypass<br/>
allow<br/>
challenge<br/>
js_challenge<br/>
block<br/></em>
            </td>
            <td>- Required</td>
        </tr>
        <tr>
            <td>priority</td>
            <td>
                - The rule's priority
                <br/> - Gets lowest priority if omitted
            </td>
            <td>
                <p><em>1</em> (highest)</p>
                <p><em>2147483647</em> (lowest)</p>
            </td>
            <td>- Optional
                <br/> - Read <a href='#avoiding-priority-conflicts'>Avoiding priority conflicts</a></td>
        </tr>
        <tr>
            <td>paused</td>
            <td>- Indicates if the rule is active</td>
            <td>
                <p><em>true</em></p>
                <p><em>false</em></p>
            </td>
            <td>- Optional
                <br/> - Default is <em>false</em></td>
        </tr>
        <tr>
            <td>description</td>
            <td>
                - To briefly describe the rule
                <br/> - Omitted from object if empty
            </td>
            <td>text</td>
            <td>
                <p>- Optional
                    <br/> - Default is empty</p>
            </td>
        </tr>
        <tr>
            <td>ref</td>
            <td>- Unique, user-supplied identifier or reference</td>
            <td>At user's discretion</td>
            <td>- Useful for identifying a rule if Cloudflare ID is unknown</td>
        </tr>
    </tbody>
</table>

### Avoiding priority conflicts
Priority plays a key role in configuring firewall rules because of the power of Cloudflare Filters in contrast to previous Cloudflare Firewall functionality.

With Cloudflare Filters, it is possible to construct conflicting rules such as:

* allow requests from the office IP range, and
* block requests with a specific user agent.

Requests from the office IP range using the user agent to block would trigger both rules, but we cannot both allow and block the request. To solve this problem, the Firewall Rules follow a strict ordering depending on action and priority.

Cloudflare prioritizes rules in descending order, such that priority 1 is first and rules with no priority are last. For rules of equal priority, Cloudflare orders them by action using the following order of precedence:

1. log
2. bypass
3. allow
4. challenge
5. js_challenge
6. block

In the example above if no priority is set, the `allow request from the office IP range` would apply because the *allow* action has a higher precedence than *block*.

To reduce the risk of unintended behavior, it's best to explicitly specify the desired priority for potentially conflicting rules.
