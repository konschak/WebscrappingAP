# WebscrappingAP
Código de webscrapping do apartamento
!pip install requests
!pip install bs4
import pandas as pd
from bs4 import BeautifulSoup
import requests
import re
import math

url = 'https://www.chavesnamao.com.br/apartamentos-a-venda/pr-curitiba/?utm_source=google&utm_medium=conversao_venda&utm_campaign=conversao_venda_pr_cwb&utm_content=&gclid=Cj0KCQjw5f2lBhCkARIsAHeTvlgjJp5ARf3TRrjw_2-ALBhu4JcJo0MeJibemBCcMosjALMJrUBzFUkaArTHEALw_wcB'

headers = {'User-Agent': "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.0.0 Safari/537.36"}

site = requests.get(url, headers=headers)

soup = BeautifulSoup(site.content, 'html.parser')

qtd_itens = soup.find('b').get_text().strip()
print(qtd_itens)

qtd_itens = qtd_itens.replace('.','')
qtd_itens

ultima_pagina = math.ceil(int(qtd_itens)/ 10)
ultima_pagina

dic_produtos = {'preco':[], 'metros_quadrados':[], 'endereco':[], 'link' : []}

for i in range(1, 200):
    url_pag = f'https://www.chavesnamao.com.br/apartamentos-a-venda/pr-curitiba/?pg={i}'
    site = requests.get(url_pag, headers=headers)
    soup = BeautifulSoup(site.content, 'html.parser')
    produtos = soup.find_all('div', class_=re.compile('imoveis__Card'))

    for produto in produtos:
        preco = produto.find('p', class_=re.compile('price')).contents[0].get_text().strip()
        metros_quadrados = produto.find('ul', class_=re.compile('list im')).contents[0].get_text().strip().replace('m²','')
        endereco = produto.find('address').contents[1].get_text().strip().replace(', Curitiba','')
        link = produto.find('span', class_=re.compile('link')).contents[0].get('href')

        print(preco, metros_quadrados,endereco, link)
        dic_produtos['preco'].append(preco)
        dic_produtos['metros_quadrados'].append(metros_quadrados)
        dic_produtos['endereco'].append(endereco)
        dic_produtos['link'].append(link)

from google.colab import drive
drive.mount ('/drive')
df.to_csv('/drive/My Drive/Colab Notebooks/Apartamentos.csv', encoding='utf-8', sep=';')
