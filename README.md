# Deno Paddle Webhook Verify

A small deno module to verify webhook signatures so you can easily build support
for Paddle Webhooks in to
[Supabase Edge Functions](https://supabase.com/docs/guides/functions).

This is for the billing version of Paddle not Classic. Read the Paddle
[signature verification docs](https://developer.paddle.com/webhooks/signature-verification).

## Using in Supabase function

Write a Supabase edge function that looks a bit like this:

```javascript
import { validateSignature } from "https://raw.githubusercontent.com/SnapDBApp/deno-paddle-verify/main/mod.ts";

Deno.serve(async (req)=>{
  const secretKey = Deno.env.get("PADDLE_WEBHOOK_SECRET");
  const signature = req.headers.get("Paddle-Signature");
  if (!signature) {
    return new Response("Missing paddle signature", {
      status: 400,
    });
  }
  const body = await req.text();
  const data = {
    isValid: await validateSignature(signature, body, secretKey)
    ? "Signature is good"
    : "Signature is bad",
  };
  return new Response(JSON.stringify(data), {
    headers: {
      "Content-Type": "application/json",
    },
  });
});
```

## Tests

Run:

```
deno test
```

## License

The MIT License. See the bundled [LICENSE](./LICENSE) file for details.
