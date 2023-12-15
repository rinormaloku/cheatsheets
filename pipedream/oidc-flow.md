Evaluate `ID_Token` Contents Using Pipedream for Authorization Code Flow

1. Create a Pipedream Workflow:
    - Trigger the workflow with an HTTP request.
    - Use the workflow URL in the subsequent steps.

2. Add Node.js Step in Workflow:
    - Use the following code snippet:

```javascript
// To use any npm package, just import it
import axios from "axios"
import { jwtDecode } from "jwt-decode";
    
export default defineComponent({
  async run({ steps, $ }) {
    let code = steps.trigger.event.query.code

    console.log(code)

    let resp = await axios.post('<issuer URL>/oauth/v2/token', new URLSearchParams({
        'grant_type': 'authorization_code',
        'code': code,
        'redirect_uri': '<pipedream URL>',
        'client_id': '<client id>',
        'client_secret': '<client_secret>'
        }))

    console.log('access_token', resp.data.access_token)
    console.log('id_token', resp.data.id_token)

    console.log(jwtDecode(resp.data.id_token))

    return jwtDecode(resp.data.id_token)
  },
})
```

3. Configure an Application in your IdP provider:
    - Set the Pipedream URL as the callback URL.

4. Update Workflow with Application Info:
    - Input the application details in step 2.
    - Save changes and deploy the workflow.

5. Testing:
    - Generate the Authorization URL. If you use Zitadel, the have a playground: [Authorization Code Flow Playground](https://zitadel.com/docs/apis/openidoauth/authrequest).
    - Fill in your apps data and the Pipedream Webhook URL
    - Complete the authorization code flow and inspect the webhook logs for token contents.
