  <div
    class="px-4 mx-md-auto"
    style="max-width: 960px">
    <h1 class="legal__header__title">Leaderboard Integration</h1>
    <p class="mb-4 text-italic mt-0">Updated on: {{ currentDate }}</p>
    <p>

      Halo Sultanku! pada artikel ini, mimin akan membahas bagaimana cara kamu 
      untuk mendapatkan dan mengintegrasikan data leaderboard BagiBagi dari Bagibagi.co,
      sehingga kamu dapat menampilkan daftar user teratas di aplikasi atau website
      yang kamu inginkan.
    </p>
    <p>
      Mimin akan menjelaskan bagaimana cara kamu mendapatkan endpoint leaderboard
      dan mengimplementasikannya di aplikasi kamu, beserta cara mengatur overlay leaderboard
      di streaming kamu. Dibawah ini adalah sesi integrasi yang kamu dapat ikuti:
    </p>
    <ul>
      <li class="tw-font-bold">
        <a href="#getting-endpoint">Cara Mendapatkan Leaderboard Endpoint</a>
      </li>
      <li class="tw-font-bold">
        <a href="#setup-overlay">Setup Overlay Leaderboard</a>
      </li>
      <li class="tw-font-bold">
        <a href="#api-response">Format Response API</a>
      </li>
      <li class="tw-font-bold">
        <a href="#implementation-examples">Contoh Implementasi</a>
      </li>
    </ul>

    <h2
      id="getting-endpoint"
      class="legal__title">
      Cara Mendapatkan Leaderboard Endpoint
    </h2>
    <p>
      Kamu bisa mendapatkan endpoint leaderboard dengan streamKey kamu melalui halaman
      <a
        href="/stream-overlay"
        target="__blank"
        >Stream Overlay</a
      >, pada tab <strong>Integration Page</strong>. Dengan cara:
    </p>
    <ol>
      <li class="tw-font-semibold">
        Pergi ke halaman
        <a
          href="/stream-overlay"
          target="__blank"
          >Stream Overlay</a
        > dengan akun Bagibagi.co kamu.
      </li>
      <li class="tw-font-semibold">klik tab "Integration Page"</li>
      <li class="tw-font-semibold">
        Scroll ke bagian "Leaderboard Integration" dan expand panelnya
      </li>
      <li class="tw-font-semibold">Klik pada input field Leaderboard Endpoint URL untuk copy endpoint</li>
    </ol>
    <p>
      Format endpoint yang akan kamu dapatkan adalah:
    </p>
    <CodeBlock
      content="https://bagibagi.co/api/partnerintegration/top-donator/streamkey?streamkey={STREAM_KEY_KAMU}"
      language="url"
      :show-header="false"
    />
    <p class="text-italic text-sm">
      <strong>Note:</strong> Endpoint ini unik untuk setiap streamer dan tidak perlu
      merchant code atau token tambahan karena sudah menggunakan streamKey.
    </p>

    <h2
      id="setup-overlay"
      class="legal__title">
      Setup Overlay Leaderboard
    </h2>
    <p>
      Sebelum menghit API Bagibagi.co untuk mendapatkan data leaderboard, pastikan kamu 
      telah mengatur leaderboard overlay dengan benar dengan mengikuti langkah berikut:
    </p>
    <ol>
      <li class="tw-font-semibold">
        Pergi ke halaman
        <a
          href="/stream-overlay"
          target="__blank"
          >Stream Overlay</a
        > menggunakan akun yang terintegrasi
      </li>
      <li class="tw-font-semibold">Pindah ke halaman "Leaderboard"</li>
      <li class="tw-font-semibold">
        Pastikan semua setting sudah lengkap dan sesuai dengan kebutuhan kamu
      </li>
      <li class="tw-font-semibold">
        Test overlay leaderboard untuk memastikan data tampil dengan benar
      </li>
    </ol>

    <h2
      id="api-response"
      class="legal__title">
      Format Response API
    </h2>
    <p>
      Ketika kamu memanggil leaderboard endpoint, kamu akan mendapatkan response
      dalam format JSON. Berikut adalah contoh response:
    </p>

    <h3 class="tw-font-semibold tw-mt-6 tw-mb-3">Response Success:</h3>
    <CodeBlock
      content='{
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
}'
      language="json"
    />

    <h3 class="tw-font-semibold tw-mt-6 tw-mb-3">Response Error:</h3>
    <CodeBlock
      content='{
  "data": null,
  "success": false,
  "message": "Validation Failed"
}'
      language="json"
    />

    <h3 class="tw-font-semibold tw-mt-6 tw-mb-3">Penjelasan Fields:</h3>
    <ul class="tw-list-disc tw-ml-5">
      <li><strong>userName:</strong> Nama user (akan tampil "Seseorang" untuk BagiBagi anonim)</li>
      <li><strong>amount:</strong> Total jumlah BagiBagi dalam Rupiah</li>
      <li><strong>isVerified:</strong> Status verifikasi user di platform BagiBagi.co</li>
      <li><strong>isAnonymous:</strong> True jika user tidak login saat melakukan BagiBagi</li>
    </ul>

    <h2
      id="implementation-examples"
      class="legal__title">
      Contoh Implementasi
    </h2>
    <p>
Berikut adalah contoh implementasi untuk pengambilan data leaderboard dalam beberapa bahasa pemrograman:
    </p>

    <div
      v-if="selectedLanguage"
      class="bg-backgroundSecondary tw-relative tw-rounded-md">
      <pre class="tw-overflow-auto tw-px-6">
        <v-select
            v-model="selectedLanguage"
            :items="languages"
            item-value="name"
            item-text="name"
            class="tw-absolute tw-flex-grow  tw-right-0 tw-top-0" />
        <code>{{ languageData.find(lang => lang.name === selectedLanguage)?.code }}</code>
    </pre>
      <ElementsButton
        @click="copyCode"
        variant="outline"
        class="tw-absolute tw-right-2 tw-bottom-2">
        Copy Code
      </ElementsButton>
    </div>
    <Footer class="flex-grow-1" />
  </div>
</template>

<script setup lang="ts">
import { useSeo } from "~/composables/useSeo";
import CodeBlock from "~/components/CodeBlock.vue";
import { isLoggedIn } from "~/composables/isLoggedIn";

useSeo(
	"Leaderboard Integration | Bagibagi.co",
	"Bagibagi.co adalah platform inovatif yang membantu siapa saja untuk menerima dukungan finansial sebagai bentuk apresiasi dengan cara saling berbagi dan memberikan penghargaan, menjadikannya pilihan terbaik untuk streamer Indonesia yang ingin mendapatkan dukungan dari penggemar mereka.",
);

const isUserLoggedIn = ref(false);

onMounted(async () => {
	isUserLoggedIn.value = await isLoggedIn();
});

// Get current date in the same format as webhook page
const currentDate = new Date()
	.toLocaleDateString("en-US", {
		year: "numeric",
		month: "long",
		day: "numeric",
	})
	.replace(/(\d+)(st|nd|rd|th)/, "$1$2");

// snackbar
const addSnackBar = inject("snackbar") as (
	color: string,
	message: string,
) => void;

// Array of languages with their corresponding code
interface Language {
	name: string;
	code: string;
}

const languageData: Language[] = [
	{
		name: "TypeScript",
		code: `
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
      method: 'GET',
      headers: {
        'Content-Type': 'application/json',
      },
    });

    const result: LeaderboardResponse = await response.json();

    if (result.success && result.data) {
      console.log('Top Donors:', result.data);
      return result.data;
    } else {
      console.error('Failed to get leaderboard:', result.message);
      return [];
    }
  } catch (error) {
    console.error('Error fetching leaderboard:', error);
    return [];
  }
}

// Example usage
const leaderboardEndpoint = 'https://bagibagi.co/api/partnerintegration/top-donator/streamkey?streamkey=YOUR_STREAM_KEY';
getLeaderboard(leaderboardEndpoint)
  .then(donors => {
    // Process the leaderboard data
    donors.forEach((donor, index) => {
      console.log(\`Rank \${index + 1}: \${donor.userName} - Rp \${donor.amount.toLocaleString()}\`);
    });
  });
`,
	},
	{
		name: "JavaScript",
		code: `
async function getLeaderboard(endpointUrl) {
  try {
    const response = await fetch(endpointUrl, {
      method: 'GET',
      headers: {
        'Content-Type': 'application/json',
      },
    });

    const result = await response.json();

    if (result.success && result.data) {
      console.log('Top Donors:', result.data);
      return result.data;
    } else {
      console.error('Failed to get leaderboard:', result.message);
      return [];
    }
  } catch (error) {
    console.error('Error fetching leaderboard:', error);
    return [];
  }
}

// Example usage
const leaderboardEndpoint = 'https://bagibagi.co/api/partnerintegration/top-donator/streamkey?streamkey=YOUR_STREAM_KEY';
getLeaderboard(leaderboardEndpoint)
  .then(donors => {
    // Process the leaderboard data
    donors.forEach((donor, index) => {
      console.log(\`Rank \${index + 1}: \${donor.userName} - Rp \${donor.amount.toLocaleString('id-ID')}\`);
    });
  });
`,
	},
	{
		name: "Python",
		code: `
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

        if result.get('success') and result.get('data'):
            print('Top Donors:', result['data'])
            return result['data']
        else:
            print(f'Failed to get leaderboard: {result.get("message")}')
            return []
    except Exception as error:
        print(f'Error fetching leaderboard: {error}')
        return []

# Example usage
leaderboard_endpoint = 'https://bagibagi.co/api/partnerintegration/top-donator/streamkey?streamkey=YOUR_STREAM_KEY'
donors = get_leaderboard(leaderboard_endpoint)

# Process the leaderboard data
for index, donor in enumerate(donors, 1):
    print(f"Rank {index}: {donor['userName']} - Rp {donor['amount']:,} (Verified: {donor['isVerified']})")
`,
	},
	{
		name: "PHP",
		code: `
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
            echo 'Top Donors: ' . json_encode($result['data'], JSON_PRETTY_PRINT) . "\\n";
            return $result['data'];
        } else {
            echo 'Failed to get leaderboard: ' . $result['message'] . "\\n";
            return [];
        }
    } catch (Exception $error) {
        echo 'Error fetching leaderboard: ' . $error->getMessage() . "\\n";
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
    echo "Rank {$rank}: {$donor['userName']} - Rp {$amount} (Verified: " . ($donor['isVerified'] ? 'Yes' : 'No') . ")\\n";
}
?>
`,
	},
];

// Bind the languageData to the select input
const languages = languageData.map((language) => language.name);

// State to hold the selected language
const selectedLanguage = ref(languageData[0].name); // Default to the first language
const copyCode = () => {
	const selectedCode =
		languageData.find((lang) => lang.name === selectedLanguage.value)?.code ||
		"";
	navigator.clipboard.writeText(selectedCode).then(() => {
		addSnackBar("green", "Code copied to clipboard");
	});
};

definePageMeta({
	layout: "legal",
});
</script>
