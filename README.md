Essa ETL pega dados ficticios de ações de 3 empresas de um arquivo CSV e simula seus índices aleatóriamente, resultando no final os dados de cada empresa em um arquivo CSV diferente, assim como um gráfico de análise diferente para cada uma empresa.

# 1. **Extração**:
   - Nesta etapa de extração se lê os dados do arquivo CSV original, assumindo que ele contenha dados da Apple, Amazon e Microsoft. No entanto, pode se estender essa etapa para lidar com dados de diferentes empresas se necessário.
``` python
import pandas as pd
import random
import numpy as np

# Preço inicial fictício
preco_inicial = 100.0

# Lista de empresas
empresas = ['Apple', 'Amazon', 'Microsoft']

# Número de elementos
qtde_elementos = 2000

# Dicionário para armazenar os DataFrames de cada empresa
dados_empresas = {}

# Ler dados do arquivo CSV original (supondo que o arquivo contenha dados da Apple)
df = pd.read_csv('arquivo-inicial-csv.csv') # obs: copie o caminho do arquivo correto para evitar erros
```
# 2. **Transformação**:
   -A etapa de transformação gera dados simulados para três empresas, calcula a soma cumulativa e as médias móveis de 20 e 200 elementos, mostrando a variação desses dados de forma aleatória, simulando o mercado financeiro. Além disso, atribui um nome de empresa, preço inicial e preço atual aos dados.
``` python
for empresa in empresas:
    lista_numeros = []

    for i in range(0, qtde_elementos):
        lista_numeros.append(random.choice([-1, 1]))

    df_lista_numeros = pd.DataFrame(lista_numeros, columns=['numero'])
    df_lista_numeros['soma'] = df_lista_numeros['numero'].cumsum()
    df_lista_numeros['media_20'] = df_lista_numeros['soma'].rolling(20).mean()
    df_lista_numeros['media_200'] = df_lista_numeros['soma'].rolling(200).mean()

    # Adiciona nome da empresa e calcular o preço com base na soma cumulativa
    df_lista_numeros['empresa'] = empresa
    df_lista_numeros['preco_inicial'] = preco_inicial
    df_lista_numeros['preco_atual'] = df_lista_numeros['preco_inicial'] + df_lista_numeros['soma']

    dados_empresas[empresa] = df_lista_numeros
```
# 3. **Carregamento**
Escreve os DataFrames de cada empresa em arquivos CSV separados e Plota gráficos para cada empresa mostrando seus índices variando.
``` python
Escreve os DataFrames de cada empresa em arquivos CSV separados
for empresa in empresas:
    dados_empresas[empresa].to_csv(f'dados_transformados_{empresa.lower()}.csv', index=False)

# Plota gráficos para cada empresa
import matplotlib.pyplot as plt

for empresa in empresas:
    dados_empresas[empresa].plot(figsize=(12, 9), y=['preco_atual', 'media_20', 'media_200'])
    plt.title(f'Dados de {empresa}')
    plt.show()
```
