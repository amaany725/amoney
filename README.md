####versi di vscoode####

from bs4 import BeautifulSoup
import requests
import pandas as pd

page = requests.get('https://news.ycombinator.com/')
bs = BeautifulSoup(page.text, 'html.parser')
bs

item= bs.find_all('tr', class_='athing')
ranks = []
titles = []
sites = []
scores = []
users = []
times = []
comments_list = []

for detail in item:
  rank= detail.find('span', class_='rank').text.strip()
  title = detail.find('span', class_='titleline').a.text.strip()
  site = detail.find('span', class_='sitebit').text.strip().replace('(', '').replace(')', '')  # Menghapus tanda kurung
  subtext_detail = detail.find_next_sibling('tr')

  if subtext_detail:
    subtext = subtext_detail.find('span', class_='subline')
    if subtext:
      if subtext.find('span', class_='score'):
        score = subtext.find('span', class_='score').text.strip()
      else:
        score= '0 points'
      user = subtext.find('a', class_='hnuser').text.strip()
      time = subtext.find('span', class_='age').text.strip()
      comments = subtext.find_all('a')[-1].text.strip()  
    else:
      score = '0 points'  
      user = 'N/A'  # Handle missing user
      time = 'N/A'  # Handle missing time
      comments = 'N/A'  # Handle missing comments
              
      print(f"Rank: {rank}, Title: {title}, Site: {site} - This item has no subtext details.")
      print('---')
  ranks.append(rank)
  titles.append(title)
  sites.append(site)
  scores.append(score)
  users.append(user)
  times.append(time)
  comments_list.append(comments)

df = pd.DataFrame(
    {'Rank':ranks,
     'Title':titles,
     'Site':sites,
     'Score':scores,
     'User':users,
     'Time':times,
     'Comments':comments_list
     })
print(df)
    
