import requests
import time

URL = "https://api.dexscreener.com/latest/dex/search?q=SOL"

def scan():
    try:
        response = requests.get(URL, timeout=10)
        response.raise_for_status()

        data = response.json()
        pairs = data.get("pairs", [])

        solana_pairs = [
            pair for pair in pairs
            if pair.get("chainId") == "solana"
        ]

        print("\n=== SOLANA TOKEN SCANNER ===\n")

        for pair in solana_pairs[:10]:
            base = pair.get("baseToken", {})
            name = base.get("name", "Unknown")
            symbol = base.get("symbol", "???")

            price = pair.get("priceUsd", "N/A")
            liquidity = pair.get("liquidity", {}).get("usd", 0)
            volume = pair.get("volume", {}).get("h24", 0)

            print(f"{name} ({symbol})")
            print(f"Price: ${price}")
            print(f"Liquidity: ${liquidity:,.0f}")
            print(f"24h Volume: ${volume:,.0f}")
            print("-" * 40)

    except Exception as error:
        print("Scanner error:", error)


while True:
    scan()
    print("\nNext scan in 60 seconds...")
    time.sleep(60)
