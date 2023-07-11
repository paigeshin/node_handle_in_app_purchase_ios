# node_handle_in_app_purchase_ios

```jsx
const express = require("express");
const app = express();
const axios = require("axios");
app.use(express.json());

const verifyReceipt = async (receipt, shareSecret) => {
  try {
    const response = await axios.post(
      "https://buy.itunes.apple.com/verifyReceipt",
      {
        "receipt-data": receipt,
        password: "shareSecret",
      }
    );
    return response.data;
  } catch (error) {
    console.error(error);
  }
};

app.post("/verifyReceipt", async (req, res) => {
  const receipt = req.body.receipt;
  const appleResponse = await verifyReceipt(receipt);

  if (appleResponse.status === 0) {
    // The receipt is valid.
    const purchase = appleResponse.receipt.in_app[0];
    // TODO: Process the purchase.
  } else {
    // The receipt is not valid. Handle the error.
    res.status(400).json({ error: "Invalid receipt" });
  }
});

/// https://developer.apple.com/documentation/appstoreservernotifications/notificationtype
app.post("/appstore/notifications", (req, res) => {
  const notification = req.body;

  switch (notification.notification_type) {
    case "DID_CHANGE_RENEWAL_STATUS":
      // Handle a change in renewal status
      if (notification.auto_renew_status === "false") {
        // The user has cancelled the subscription
        const userId =
          notification.unified_receipt.latest_receipt_info[0]
            .original_transaction_id;
        // Update the user's subscription status in your database
        // and take necessary actions, such as notifying the user or
        // logging the event.
      }
      break;
    case "DID_RENEW":
      // Handle a successful renewal
      break;
    // Handle other types of notifications
    default:
      console.log(
        `Unhandled notification type: ${notification.notification_type}`
      );
  }

  res.status(200).end(); // Responding is important
});

```
