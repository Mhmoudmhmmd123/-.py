import time
import asyncio
import aiohttp
import json
import uuid
import secrets
from datetime import datetime
from colorama import Fore, Style, init
import random

# Initialize colorama
init(autoreset=True)

count = int("1000")
PAUSE_DURATION = 7 * 60
PAUSE = 2 * 60   # مدة التوقف بالثواني (10 دقائق)
TOTAL_LIMIT = 1000  # الحد الأقصى لمجموع الأرقام العشوائية قبل التوقف لكل توكن

# قاموس لتتبع المجموع لكل توكن
token_counts = {}
EDGE_USERAGENTS = [
    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/122.0.0.0 Safari/537.36 Edg/122.0.2365.57",
    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/122.0.0.0 Safari/537.36 Edg/122.0.2365.52",
    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/122.0.0.0 Safari/537.36 Edg/122.0.2365.46",
    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.0.0 Safari/537.36 Edg/121.0.2277.128",
    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.0.0 Safari/537.36 Edg/121.0.2277.112",
    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.0.0 Safari/537.36 Edg/121.0.2277.98",
    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.0.0 Safari/537.36 Edg/121.0.2277.83",
    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.2210.133",
    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.2210.121",
    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.2210.91"
]

# قائمة التوكنات
TOKENS = [
    "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjp7ImlkIjoiMjI0MTliYmYtYzJlNi00ODc1LWIwZmUtNDcxNjBlZjA1MjJiIiwiZmlyc3RfbmFtZSI6Ik5vcmFsZGluIiwidXNlcm5hbWUiOiJUMjc3VCJ9LCJzZXNzaW9uX2lkIjo2NTg5MjUsInN1YiI6IjIyNDE5YmJmLWMyZTYtNDg3NS1iMGZlLTQ3MTYwZWYwNTIyYiIsImV4cCI6MTczODU2NDIxM30.0HZKZ4ouZxmv9dSlwFEprW5S6FFhxOUNzvszGRbIm6g",
    "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjp7ImlkIjoiYmMyNGYyMTktODhiNy00ZDc0LWI2OGQtYmY1ZDYyNTg1MGU5IiwiZmlyc3RfbmFtZSI6IvCfjYXwn5C42KzZhdmF2YjYpyIsInVzZXJuYW1lIjpudWxsfSwic2Vzc2lvbl9pZCI6NjYwMjQ0LCJzdWIiOiJiYzI0ZjIxOS04OGI3LTRkNzQtYjY4ZC1iZjVkNjI1ODUwZTkiLCJleHAiOjE3Mzg1Njc3MzR9.AFkKJtpAYopx1bbmDD47Mgg-KUhqOiTmcsKco79C3ik",
    "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjp7ImlkIjoiYTY5ZWI2ZWEtZjNjMi00N2ExLTk4MjEtYzZhZWFlMWRhNTYyIiwiZmlyc3RfbmFtZSI6ItmG2KjYqtio2KrYqPCfjYUiLCJ1c2VybmFtZSI6bnVsbH0sInNlc3Npb25faWQiOjY2MDQxMywic3ViIjoiYTY5ZWI2ZWEtZjNjMi00N2ExLTk4MjEtYzZhZWFlMWRhNTYyIiwiZXhwIjoxNzM4NTY4MDkzfQ.OStR8aLTfous1we_ZBDN4vc42cQRHB7d3Q3ZRS_g8WQ",
    "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjp7ImlkIjoiY2UzNmZkZjYtZWU5ZS00NzIwLWFhMjAtYjIwMjk3ZWU4NzcwIiwiZmlyc3RfbmFtZSI6IkFsYWFhIiwidXNlcm5hbWUiOm51bGx9LCJzZXNzaW9uX2lkIjo2NjEwMTAsInN1YiI6ImNlMzZmZGY2LWVlOWUtNDcyMC1hYTIwLWIyMDI5N2VlODc3MCIsImV4cCI6MTczODU2OTMxNX0.imt1cZ5hEvozjlCkBp8ih3F3GMrcBuKHlTiYrDRPEu4",
    "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjp7ImlkIjoiZTcwNmM2NWMtOGQ2OS00MTY5LThhNDEtOWMxNTA5Mjg2OWEyIiwiZmlyc3RfbmFtZSI6IkhocyIsInVzZXJuYW1lIjpudWxsfSwic2Vzc2lvbl9pZCI6NjYxNDQ3LCJzdWIiOiJlNzA2YzY1Yy04ZDY5LTQxNjktOGE0MS05YzE1MDkyODY5YTIiLCJleHAiOjE3Mzg1NzAxNTR9.epC7MNcukc97dT5MuVCYv0FqG1YCNB7mgmDxOKUymmQ",
    "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjp7ImlkIjoiOTkyYmIyMTgtMGNmNS00ZjljLTkwYTQtNTQyNWQxOThkZDlkIiwiZmlyc3RfbmFtZSI6IlNqc2pzaiIsInVzZXJuYW1lIjpudWxsfSwic2Vzc2lvbl9pZCI6NjYxNTgyLCJzdWIiOiI5OTJiYjIxOC0wY2Y1LTRmOWMtOTBhNC01NDI1ZDE5OGRkOWQiLCJleHAiOjE3Mzg1NzA0MjV9.3fFp3v0OR278tm8tthvVYTfh866EgPY_qByFNHeW1C0",
    "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjp7ImlkIjoiN2ZiYzJhZWYtMDRjYi00ZmZkLThmOTEtODcyNTcxNWE4ZGIzIiwiZmlyc3RfbmFtZSI6IkdhbWFsIiwidXNlcm5hbWUiOm51bGx9LCJzZXNzaW9uX2lkIjo2NjE4NDksInN1YiI6IjdmYmMyYWVmLTA0Y2ItNGZmZC04ZjkxLTg3MjU3MTVhOGRiMyIsImV4cCI6MTczODU3MDkzOX0.ogONx9VIco_HDNb04llCnE6vvYUSPYOjuSOgGl3gwnI"
]

def get_current_timestamp():
    """Retrieve the current timestamp."""
    return int(time.time())

def print_response(acc_number, user_id, coins_amount, counts, total):
    """Print the extracted data with colors and timestamps."""
    current_time = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    print(
        f"{Fore.GREEN}[{current_time}] {Fore.BLUE} Acc: [{acc_number}]:{Style.RESET_ALL} "
        f"User ID: {Fore.CYAN}[{user_id}]{Style.RESET_ALL}, Coins: {Fore.YELLOW}[{coins_amount}]{Style.RESET_ALL}, "
        f"Count: {Fore.MAGENTA}[{counts}]{Style.RESET_ALL}, Total Counts: {Fore.RED}[{total}]{Style.RESET_ALL}"
    )

async def send_request(token, acc_number):
    """Send an asynchronous HTTP POST request for a specific token."""
    global token_counts  # استخدم القاموس لتتبع مجموع كل توكن
    random_user_agent = random.choice(EDGE_USERAGENTS)
    url = "https://api-gw.geagle.online/tap"
    headers = {
        'User-Agent': random_user_agent,
        'Accept': "application/json, text/plain, */*",
        'Accept-Encoding': "gzip, deflate, br, zstd",
        'Content-Type': "application/json",
        'authorization': f"Bearer {token}",
        'origin': "https://telegram.geagle.online",
        'accept-language': "en,en-US;q=0.9",
    }

    if acc_number not in token_counts:
        token_counts[acc_number] = 0

    async with aiohttp.ClientSession() as session:
        while True:
            if token_counts[acc_number] >= TOTAL_LIMIT:
                print(f"{Fore.RED}Acc {acc_number} reached {TOTAL_LIMIT} total counts. Pausing for {PAUSE_DURATION // 60} minutes...")
                await asyncio.sleep(PAUSE_DURATION)
                token_counts[acc_number] = 0

            salt = str(uuid.uuid4())
            counts = secrets.randbelow(41) + 50
            payload = {
                "available_taps": count,
                "count": counts,
                "timestamp": get_current_timestamp(),
                "salt": f"{salt}",
                "unique_id": str(uuid.uuid4())
            }

            try:
                async with session.post(url, json=payload, headers=headers) as response:
                    response_text = await response.text()
                    if response.status == 200:
                        data = json.loads(response_text)
                        user_id = data.get("user_id")
                        coins_amount = data.get("coins_amount")

                        token_counts[acc_number] += counts
                        print_response(acc_number, user_id, coins_amount, counts, token_counts[acc_number])
                    else:
                        print(f"{Fore.RED}Error: Received status code {response.status} for token {token[:10]}")

            except aiohttp.ClientError as e:
                print(f"{Fore.RED}Network error for acc {acc_number}: {e}. Retrying...")
                await asyncio.sleep(5)
                continue
            except Exception as e:
                print(f"{Fore.RED}Unexpected error for acc {acc_number}: {e}. Retrying...")
                await asyncio.sleep(5)
                continue

            if token_counts[acc_number] % 10 == 0:
                random_user_agent = random.choice(EDGE_USERAGENTS)
                headers['User-Agent'] = random_user_agent

            if token_counts[acc_number] % 50 == 0:
                print(f"{Fore.YELLOW}Taking a longer pause for token anti detect 👾👽{acc_number}...")
                await asyncio.sleep(PAUSE * 2)

            random_delay = secrets.randbelow(11) + 15
            await asyncio.sleep(random_delay)

async def main():
    """Main function to handle sending requests for all tokens."""
    if not TOKENS:
        print("No tokens found. Exiting...")
        return

    tasks = [send_request(token, idx + 1) for idx, token in enumerate(TOKENS)]
    await asyncio.gather(*tasks)

if __name__ == "__main__":
    asyncio.get_event_loop().run_until_complete(main())
