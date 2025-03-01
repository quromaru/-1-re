import requests
from bs4 import BeautifulSoup
import datetime

# Steamの新作ゲームリストのURL（カテゴリによって変わるかも）
STEAM_NEW_RELEASES_URL = "https://store.steampowered.com/explore/new/"

def get_new_games():
    # Steamの新作ページを取得
    response = requests.get(STEAM_NEW_RELEASES_URL)
    soup = BeautifulSoup(response.text, "html.parser")

    # ゲームリストを解析
    games = []
    for item in soup.select(".tab_item"):  # ゲーム情報のブロック
        title = item.select_one(".tab_item_name").text  # ゲームタイトル
        link = item["href"]  # Steamストアのリンク

        # 発売日を取得（表示形式がバラバラなので注意）
        release_date_text = item.select_one(".tab_item_details span").text.strip()
        
        # ゲームデータをリストに追加
        games.append({
            "title": title,
            "link": link,
            "release_date": release_date_text
        })

    return games

# テスト実行
if __name__ == "__main__":
    new_games = get_new_games()
    for game in new_games[:5]:  # 5つだけ表示
        print(f"{game['title']} - {game['link']} ({game['release_date']})")
