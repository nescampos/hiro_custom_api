# Build a Custom Blockchain API

This demo uses Chainhooks, for Hiro Hacks, to review events in the Stacks testnet with an API.

Steps:

1. Enter to the chainhooks folder:
```sh
cd apps\chainhook
```

2. Open the folder with your favorite editor (VS Code, etc.)
3. Edit the src\server.ts with the following code:
```js
const express = require('express');

const app = express();
app.use(express.json());

app.post("/api/events", async (req, res) => {
  const events = req.body;
  // Loop through each item in the apply array
  events.apply.forEach((item: any) => {
    // Loop through each transaction in the item
    item.transactions.forEach((transaction: any) => {
      // If the transaction has operations, loop through them
      if (transaction.operations) {
        transaction.operations.forEach((operation: any) => {
          // Log the operation
          console.log({ operation });
        });
      }
    });
  });

  // Send a response back to Chainhook to acknowledge receipt of the event
  res.status(200).send({ message: "Proposal added!" });
});

app.listen(3000, () => {
  console.log("Server is running on port 3000");
});
```

4. Deploy the project with localtunnel:
```sh
npx localtunnel --port 4523
```

5. In the console, you will get a URL. Copy that URL, with the endpoint _/api/events_ (ex: **https://tired-oranges-invite.loca.lt/api/events**)
6. Paste the full endpoint (step 5) in the _src\chainhooks\predicate.json_, in the **url** field in the **then_that** section:
```json
...
  },
        "then_that": {
          "http_post": {
            "URL": "<<URL to paste>>",
            "authorization_header": "Bearer 12345"
          }
        },
        "start_block": 138339
      }
...
```

7. In the [Hiro Platform](https://platform.hiro.so/), create a project and upload the _predicate.json_ file.
![image](https://github.com/nescampos/hiro_custom_api/assets/7274106/6570caf8-35bd-46f0-a96a-e540895abfae)

9. Test the app and follow all the events in the **grant proposal smart contract** in the Stacks testnet.
