---
title: Leaderboard Integration
description: Cara mendapatkan endpoint leaderboard dan mengintegrasikannya ke aplikasi atau overlay.
---

import Tabs from "@theme/Tabs";
import TabItem from "@theme/TabItem";

# Leaderboard Integration

Updated on: January 2, 2026

Halo Sultanku! pada artikel ini, mimin akan membahas bagaimana cara kamu untuk mendapatkan dan mengintegrasikan data leaderboard BagiBagi dari Bagibagi.co, sehingga kamu dapat menampilkan daftar user teratas di aplikasi atau website yang kamu inginkan.

Mimin akan menjelaskan bagaimana cara kamu mendapatkan endpoint leaderboard dan mengimplementasikannya di aplikasi kamu, beserta cara mengatur overlay leaderboard di streaming kamu. Dibawah ini adalah sesi integrasi yang kamu dapat ikuti:

- [Cara Mendapatkan Leaderboard Endpoint](#cara-mendapatkan-leaderboard-endpoint)
- [Setup Overlay Leaderboard](#setup-overlay-leaderboard)
- [Format Response API](#format-response-api)
- [Contoh Implementasi](#contoh-implementasi)

## Cara Mendapatkan Leaderboard Endpoint

Kamu bisa mendapatkan endpoint leaderboard dengan streamKey kamu melalui halaman [Stream Overlay](https://bagibagi.co/stream-overlay), pada tab **Integration Page**. Dengan cara:

1. Pergi ke halaman [Stream Overlay](https://bagibagi.co/stream-overlay) dengan akun Bagibagi.co kamu.
2. klik tab "Integration Page".
3. Scroll ke bagian "Leaderboard Integration" dan expand panelnya.
4. Klik pada input field Leaderboard Endpoint URL untuk copy endpoint.

Format endpoint yang akan kamu dapatkan adalah:

```url
https://bagibagi.co/api/partnerintegration/top-donator/streamkey?streamkey={STREAM_KEY_KAMU}
```

_Note:_ Endpoint ini unik untuk setiap streamer dan tidak perlu merchant code atau token tambahan karena sudah menggunakan streamKey.

## Setup Overlay Leaderboard

Sebelum menghit API Bagibagi.co untuk mendapatkan data leaderboard, pastikan kamu telah mengatur leaderboard overlay dengan benar dengan mengikuti langkah berikut:

1. Pergi ke halaman [Stream Overlay](https://bagibagi.co/stream-overlay) menggunakan akun yang terintegrasi.
2. Pindah ke halaman "Leaderboard".
3. Pastikan semua setting sudah lengkap dan sesuai dengan kebutuhan kamu.
4. Test overlay leaderboard untuk memastikan data tampil dengan benar.

## Format Response API

Ketika kamu memanggil leaderboard endpoint, kamu akan mendapatkan response dalam format JSON. Berikut adalah contoh response:

### Response Success

```json
{
  "data": [
    {
      "userName": "BagiBagi",
      "amount": 300000,
      "isVerified": true,
      "isAnonymous": true
    },
    {
      "userName": "bagibagi",
      "amount": 148351,
      "isVerified": true,
      "isAnonymous": true
    },
    {
      "userName": "Seseorang",
      "amount": 81845,
      "isVerified": false,
      "isAnonymous": true
    }
  ],
  "success": true,
  "message": "Success"
}
```

### Response Error

```json
{
  "data": null,
  "success": false,
  "message": "Validation Failed"
}
```

### Penjelasan Fields

- **userName:** Nama user (akan tampil "Seseorang" untuk BagiBagi anonim)
- **amount:** Total jumlah BagiBagi dalam Rupiah
- **isVerified:** Status verifikasi user di platform BagiBagi.co
- **isAnonymous:** True jika user tidak login saat melakukan BagiBagi

## Contoh Implementasi

Berikut adalah contoh implementasi untuk pengambilan data leaderboard dalam beberapa bahasa pemrograman:

<Tabs groupId="language">
  <TabItem value="typescript" label="TypeScript">
  ```ts
  interface LeaderboardEntry {
    userName: string;
    amount: number;
    isVerified: boolean;
    isAnonymous: boolean;
  }

  interface LeaderboardResponse {
    data: LeaderboardEntry[];
    success: boolean;
    message: string;
  }

  async function getLeaderboard(endpointUrl: string): Promise<LeaderboardEntry[]> {
    try {
      const response = await fetch(endpointUrl, {
        method: "GET",
        headers: {
          "Content-Type": "application/json",
        },
      });

      const result: LeaderboardResponse = await response.json();

      if (result.success && result.data) {
        console.log("Top Donors:", result.data);
        return result.data;
      } else {
        console.error("Failed to get leaderboard:", result.message);
        return [];
      }
    } catch (error) {
      console.error("Error fetching leaderboard:", error);
      return [];
    }
  }

  // Example usage
  const leaderboardEndpoint =
    "https://bagibagi.co/api/partnerintegration/top-donator/streamkey?streamkey=YOUR_STREAM_KEY";
  getLeaderboard(leaderboardEndpoint).then((donors) => {
    // Process the leaderboard data
    donors.forEach((donor, index) => {
      console.log(
        `Rank ${index + 1}: ${donor.userName} - Rp ${donor.amount.toLocaleString()}`,
      );
    });
  });
  ```
  </TabItem>
  <TabItem value="javascript" label="JavaScript">
  ```js
  async function getLeaderboard(endpointUrl) {
    try {
      const response = await fetch(endpointUrl, {
        method: "GET",
        headers: {
          "Content-Type": "application/json",
        },
      });

      const result = await response.json();

      if (result.success && result.data) {
        console.log("Top Donors:", result.data);
        return result.data;
      } else {
        console.error("Failed to get leaderboard:", result.message);
        return [];
      }
    } catch (error) {
      console.error("Error fetching leaderboard:", error);
      return [];
    }
  }

  // Example usage
  const leaderboardEndpoint =
    "https://bagibagi.co/api/partnerintegration/top-donator/streamkey?streamkey=YOUR_STREAM_KEY";
  getLeaderboard(leaderboardEndpoint).then((donors) => {
    // Process the leaderboard data
    donors.forEach((donor, index) => {
      console.log(
        `Rank ${index + 1}: ${donor.userName} - Rp ${donor.amount.toLocaleString("id-ID")}`,
      );
    });
  });
  ```
  </TabItem>
  <TabItem value="python" label="Python">
  ```python
  import requests
  from typing import List, Dict, Any

  def get_leaderboard(endpoint_url: str) -> List[Dict[str, Any]]:
      """
      Fetch leaderboard data from BagiBagi.co API

      Args:
          endpoint_url: The leaderboard endpoint URL with streamkey

      Returns:
          List of donor entries or empty list if failed
      """
      try:
          response = requests.get(endpoint_url)
          result = response.json()

          if result.get("success") and result.get("data"):
              print("Top Donors:", result["data"])
              return result["data"]
          else:
              print(f'Failed to get leaderboard: {result.get("message")}')
              return []
      except Exception as error:
          print(f"Error fetching leaderboard: {error}")
          return []

  # Example usage
  leaderboard_endpoint = (
      "https://bagibagi.co/api/partnerintegration/top-donator/streamkey?streamkey=YOUR_STREAM_KEY"
  )
  donors = get_leaderboard(leaderboard_endpoint)

  # Process the leaderboard data
  for index, donor in enumerate(donors, 1):
      print(
          f"Rank {index}: {donor['userName']} - Rp {donor['amount']:,} (Verified: {donor['isVerified']})"
      )
  ```
  </TabItem>
  <TabItem value="php" label="PHP">
  ```php
  <?php

  function getLeaderboard($endpointUrl) {
      try {
          $ch = curl_init();
          curl_setopt($ch, CURLOPT_URL, $endpointUrl);
          curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
          curl_setopt($ch, CURLOPT_HTTPHEADER, [
              'Content-Type: application/json',
          ]);

          $response = curl_exec($ch);
          $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
          curl_close($ch);

          $result = json_decode($response, true);

          if ($result['success'] && isset($result['data'])) {
              echo 'Top Donors: ' . json_encode($result['data'], JSON_PRETTY_PRINT) . "\n";
              return $result['data'];
          } else {
              echo 'Failed to get leaderboard: ' . $result['message'] . "\n";
              return [];
          }
      } catch (Exception $error) {
          echo 'Error fetching leaderboard: ' . $error->getMessage() . "\n";
          return [];
      }
  }

  // Example usage
  $leaderboardEndpoint = 'https://bagibagi.co/api/partnerintegration/top-donator/streamkey?streamkey=YOUR_STREAM_KEY';
  $donors = getLeaderboard($leaderboardEndpoint);

  // Process the leaderboard data
  foreach ($donors as $index => $donor) {
      $rank = $index + 1;
      $amount = number_format($donor['amount'], 0, ',', '.');
      echo "Rank {$rank}: {$donor['userName']} - Rp {$amount} (Verified: " . ($donor['isVerified'] ? 'Yes' : 'No') . ")\n";
  }
  ?>
  ```
  </TabItem>
</Tabs>
