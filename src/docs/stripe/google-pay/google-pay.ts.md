---
file: "google-pay.ts"
---

```ts
import { Stripe, GooglePayEventsEnum } from '@capacitor-community/stripe';

(async () => {
  // Check to be able to use Google Pay on device
  const isAvailable = Stripe.isGooglePayAvailable().catch(() => undefined);
  if (isAvailable === undefined) {
    // disable to use Google Pay
    return;
  }
  
  Stripe.addListener(GooglePayEventsEnum.Completed, () => {
    console.log('GooglePayEventsEnum.Completed');
  });
  
  // Connect to your backend endpoint, and get paymentIntent.
  const { paymentIntent } = await this.http.post<{
    paymentIntent: string;
  }>(environment.api + 'payment-sheet', {}).pipe(first()).toPromise(Promise);

  // Prepare Google Pay
  await Stripe.createGooglePay({
    paymentIntentClientSecret: paymentIntent,
  });

  // Present Google Pay
  const result = await Stripe.presentGooglePay();
  if (result.paymentResult === GooglePayEventsEnum.Completed) {
    // Happy path
  }
})();
　
```
