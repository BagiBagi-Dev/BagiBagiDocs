---
title: Webhook Integration
description: Integrasi webhook untuk menerima notifikasi transaksi dan validasi signature.
---

import Tabs from "@theme/Tabs";
import TabItem from "@theme/TabItem";

# Webhook Integration

Updated on: 30 September 2024

Halo Sultanku!, pada artikel ini, mimin akan membahas bagaimana cara kamu untuk mengintegrasikan Custom Webhook, sehingga kamu dapat membuat Bagibagi.co dapat berkomunikasi dengan aplikasi yang kamu inginkan.

Mimin akan menjelaskan bagaimana cara kamu mengintegrasikan aplikasi kamu dengan Bagibagi.co, sekaligus melakukan validasi transaksi demi mencegah transaksi palsu yang masuk ke-aplikasi kamu, dibawah ini adalah beberapa sesi integrasi Webhook yang kamu dapat ikuti:

- [Integrasi Platform Lain Ke-Bagibagi.co](#integrasi-platform-lain-ke-bagibagi.co)
- [Integrasi Aplikasi kamu Ke-Bagibagi.co](#integrasi-aplikasi-kamu-ke-bagibagi.co)
- [Validasi Transaksi Dari Bagibagi.co Ke-Aplikasi kamu](#validasi-transaksi-dari-bagibagi.co-ke-aplikasi-kamu)

## Integrasi Platform Lain Ke-Bagibagi.co

Kamu dapat melakukan integrasi Platform lain Ke-Bagibagi.co dengan memasukkan url Webhook yang ada, pada bagian tabs Integration Page dalam halaman [Stream Overlay](https://bagibagi.co/stream-overlay), dan memasukkan Webhook Url pada bagian Webhook Integration yang kamu inginkan.

![Stream Overlay Webhook Url](/img/stream-overlay-webhook-url.png)
_Contoh lokasi Webhook URL di halaman Stream Overlay._

## Integrasi Aplikasi kamu Ke-Bagibagi.co

kamu dapat melakukan integrasi Aplikasi kamu sehingga menerima notifikasi transaksi dari Bagibagi.co dengan memasukkan Custom Webhook Url pada input form Custom Url yang ada pada bagian tabs Integration Page dalam halaman [Stream Overlay](https://bagibagi.co/stream-overlay), dan memasukkan Custom Url aplikasi kamu pada bagian ini. Bagibagi.co akan mengirimkan notifikasi transaksi dengan metode POST dalam bentuk application/json Dengan contoh seperti di-bawah ini:

```json
{
  "transaction_id": "bagibagi-965b3d64-1f5e-4361-a01b-5b58df37190c",
  "name": "Seseorang",
  "amount": 10000,
  "message": "Sukses selalu ya om",
  "mediaShareUrl": "https://www.youtube.com/watch?v=jHonT8q2MOQ",
  "created_at": "8/24/2024 10:30:00 AM"
}
```

## Validasi Transaksi Dari Bagibagi.co Ke-Aplikasi kamu

Jika kamu ingin mencegah transaksi palsu yang masuk pada Custom Url yang kamu masukkan, kamu dapat melakukan validasi berdasarkan header X-Bagibagi-Signature yang kami kirimkan pada Custom Url kamu. Signature dapat kamu validasi dengan algoritma SHA-256, dengan signing key sesuai dengan Webhook Token yang kamu dapat lihat pada Tab Integrasi Akun kamu. kamu dapat melakukan validasi seperti contoh skenario yang kami berikan dibawah ini.

Semisal Webhook Token kamu adalah "secret", dan kamu mendapatkan notifikasi transaksi yang memiliki body seperti dibawah ini:

```json
{
  "transaction_id": "bagibagi-965b3d64-1f5e-4361-a01b-5b58df37190c",
  "name": "Seseorang",
  "amount": 10000,
  "message": "Sukses selalu ya om",
  "mediaShareUrl": "https://www.youtube.com/watch?v=jHonT8q2MOQ",
  "created_at": "2/17/2025 10:46:21 AM"
}
```

Kami juga akan mengirimkan header X-Bagibagi-Signature yang akan memiliki value "5c46b62f3348b62089671c2ad640e52b91789039339e94bdaaec1e4fd04950c6" dan dapat kamu validasi dengan beberapa contoh bahasa pemrograman dibawah ini:

<Tabs groupId="language">
  <TabItem value="typescript" label="TypeScript">
  ```ts
  import { createHmac, timingSafeEqual } from "node:crypto";

  function isValidSignature(
    value: object,
    webhookToken: string,
    signature: string,
  ): boolean {
    const generatedSignature = createHmac("sha256", webhookToken)
      .update(JSON.stringify(value))
      .digest("hex");
    const signatureBuffer = Buffer.from(signature, "hex");
    const generatedSignatureBuffer = Buffer.from(generatedSignature, "hex");
    return timingSafeEqual(
      new Uint8Array(signatureBuffer),
      new Uint8Array(generatedSignatureBuffer),
    );
  }

  const body = {
    transaction_id: "bagibagi-965b3d64-1f5e-4361-a01b-5b58df37190c",
    name: "Seseorang",
    amount: 10000,
    message: "Sukses selalu ya om",
    mediaShareUrl: "https://www.youtube.com/watch?v=jHonT8q2MOQ",
    created_at: "2/17/2025 10:46:21 AM",
  };

  const webhookToken = "secret";
  const signature =
    "e9b17b5f09e7cfbe09bc677799aa8cb586f3bbe7b1c19a4be2545c16b240d250";
  console.log(isValidSignature(body, webhookToken, signature));
  ```
  </TabItem>
  <TabItem value="python" label="Python">
  ```python
  import hmac
  import hashlib
  import json

  def generate_signature(value: dict, secret_key: str) -> str:
      value_str = json.dumps(value, separators=(",", ":"))
      hmac_obj = hmac.new(secret_key.encode(), value_str.encode(), hashlib.sha256)
      return hmac_obj.hexdigest()

  def is_valid_signature(value: dict, secret_key: str, signature: str) -> bool:
      generated_signature = generate_signature(value, secret_key)
      return hmac.compare_digest(generated_signature, signature)

  hello_world = {
      "transaction_id": "bagibagi-965b3d64-1f5e-4361-a01b-5b58df37190c",
      "name": "Seseorang",
      "amount": 10000,
      "message": "Sukses selalu ya om",
      "mediaShareUrl": "https://www.youtube.com/watch?v=jHonT8q2MOQ",
      "created_at": "2/17/2025 10:46:21 AM",
  }
  secret = "secret"
  signature = "e9b17b5f09e7cfbe09bc677799aa8cb586f3bbe7b1c19a4be2545c16b240d250"
  print(f"Is valid signature: {is_valid_signature(hello_world, secret, signature)}")
  ```
  </TabItem>
  <TabItem value="php" label="PHP (Laravel)">
  ```php
  <?php

  namespace App\Http\Controllers;

  use Illuminate\Http\Request;

  class WebhookController extends Controller {
      public function generateSignature(array $value, string $webhookToken): string {
          $valueJson = json_encode($value, JSON_UNESCAPED_SLASHES | JSON_UNESCAPED_UNICODE);
          return hash_hmac("sha256", $valueJson, $webhookToken);
      }

      public function isValidSignature(array $value, string $webhookToken, string $signature): bool {
          $generatedSignature = $this->generateSignature($value, $webhookToken);
          return hash_equals($generatedSignature, $signature);
      }

      public function handleWebhook(Request $request) {
          $value = $request->all();
          $webhookToken = "your_webhook_token";
          $signature = $request->header("X-Bagibagi-Signature");

          if ($this->isValidSignature($value, $webhookToken, $signature)) {
              return response()->json(["message" => "Signature is valid"], 200);
          }

          return response()->json(["message" => "Invalid signature"], 400);
      }
  }
  ```
  </TabItem>
</Tabs>
