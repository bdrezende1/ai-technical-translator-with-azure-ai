# ai-technical-translator-with-azure-ai
Uma solução de tradução automática de artigos técnicos utilizando o AzureAI, com foco em garantir precisão terminológica e contexto específico do domínio técnico, facilitando o acesso a conteúdos especializados em diferentes idiomas.

--------------------------------------------------------------------------------------------------------------------------------

Esse script usa a API de Tradução do Microsoft Azure para traduzir textos automaticamente. Detalhando as principais seções:

### 1. **Importação de Bibliotecas**
   ```python
   import requests
   from docx import Document
   import os
   ```
   - **`requests`**: usada para fazer requisições HTTP, especialmente a API de tradução.
   - **`Document`**: da biblioteca `python-docx`, permite manipular documentos no formato `.docx`.
   - **`os`**: usada para operações no sistema, como gerar IDs de rastreamento.

### 2. **Configuração das Variáveis de Autenticação**
   ```python
   subscription_key = "94774de57816dc1ec4f27142fa9d2dccce71381c"
   endpoint = "https://api.cognitive.microsofttranslator.com"
   location = "eastus2"
   language_destination = "pt-br"
   ```
   - **`subscription_key`**: chave da API para autenticação.
   - **`endpoint`**: URL base da API do Azure.
   - **`location`**: localização do servidor da API.
   - **`language_destination`**: idioma de destino, aqui configurado para "pt-br" (português do Brasil).

### 3. **Função `translator_text`**
   A função `translator_text` recebe o texto e o idioma de destino para traduzir o conteúdo.

   ```python
   def translator_text(text, target_language):
       path = '/translate'
       constructed_url = endpoint + path
       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Ocp-Apim-Subscription-Region': location,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(os.urandom(16))
       }

       body = [{'text': text}]
       params = {
           'api-version': '3.0',
           'from': 'en',
           'to': target_language
       }

       request = requests.post(constructed_url, params=params, headers=headers, json=body)
       return request.json()
   ```
   - **`constructed_url`**: URL completa da API.
   - **`headers`**: inclui as informações de autenticação e tipo de conteúdo.
   - **`body`**: JSON com o texto a ser traduzido.
   - **`params`**: parâmetros da API, definindo a versão, idioma de origem (`en` para inglês) e de destino.
   - **`requests.post`**: faz uma requisição POST à API de tradução e retorna a resposta em JSON.

-----------------------------------------------------------------------------------------------------------------------------
# ai_technical_translator_with_azure_openai.ipynb

O segundo arquivo é um código Python que combina funcionalidades de extração de texto da web e tradução usando a API do Azure e a API da OpenAI. 

Detalhando cada célula de código:

### 1. **Instalação de Bibliotecas**
   ```python
   !pip install requests beautifulsoup4 openai langchain-openai
   ```
   - **`requests`**: usada para fazer requisições HTTP.
   - **`beautifulsoup4`**: facilita a extração de conteúdo de páginas HTML.
   - **`openai` e `langchain-openai`**: provavelmente, para integrar serviços da OpenAI, como modelos de linguagem.

### 2. **Função `extract_text_from_url`**
   ```python
   import requests
   from bs4 import BeautifulSoup

   def extract_text_from_url(url):
       response = requests.get(url)

       if response.status_code == 200:
           soup = BeautifulSoup(response.text, 'html.parser')
           for script_or_style in soup(['script', 'style']):
               script_or_style.decompose()
           text = soup.get_text()
           lines = (line.strip() for line in text.splitlines())
           chunks = (phrase.strip() for line in lines for phrase in line.split("  "))
           text = '\n'.join(chunk for chunk in chunks if chunk)
           return text
       else:
           return None
   ```
   Essa função faz uma **extração de texto de uma URL**:
   - Requisição ao site usando `requests.get`.
   - Verificação do código de resposta (200 significa sucesso).
   - Remoção de elementos `<script>` e `<style>` com o BeautifulSoup.
   - Extração e limpeza do texto, removendo quebras de linha desnecessárias e espaços extras.

### 3. **Função de Tradução com OpenAI e Azure**

O notebook continua com funções de tradução, usa tanto a API do Azure Translator quanto a OpenAI.
Vamos descrever como essas integrações normalmente funcionam em um contexto como esse, com base nas funcionalidades típicas:

#### Função de Tradução com a API OpenAI e Azure
```python
import openai

def translate_text_with_openai(text, target_language):
    openai.api_key = "SUA_CHAVE_API_OPENAI"
    response = openai.Completion.create(
        engine="text-davinci-003",
        prompt=f"Traduza o seguinte texto para {target_language}: {text}",
        max_tokens=1000
    )
    return response.choices[0].text.strip()
```

#### Entendendo a Função de Tradução com a API de Tradução do Azure
Abaixo está uma função semelhante à função de tradução Azure que vimos no primeiro arquivo, com uma estrutura típica para tradução usando a API do Azure Translator:

```python
import requests

def translate_text_with_azure(text, target_language="pt-br"):
    subscription_key = "SUA_CHAVE_AZURE"
    endpoint = "https://api.cognitive.microsofttranslator.com"
    location = "eastus2"
    
    path = '/translate'
    constructed_url = endpoint + path
    headers = {
        'Ocp-Apim-Subscription-Key': subscription_key,
        'Ocp-Apim-Subscription-Region': location,
        'Content-type': 'application/json'
    }
    body = [{'text': text}]
    params = {
        'api-version': '3.0',
        'to': target_language
    }

    response = requests.post(constructed_url, params=params, headers=headers, json=body)
    return response.json()[0]['translations'][0]['text']
```

### Explicação dos Elementos
- **Integração com OpenAI**: A função `translate_text_with_openai` usa um modelo da OpenAI para gerar uma tradução a partir de um prompt que define o idioma de destino.
- **Integração com o Azure Translator**: A função `translate_text_with_azure` conecta-se à API do Microsoft Translator para realizar a tradução diretamente.

### 4. **Fluxo de Trabalho Completo (Extração e Tradução)**
Com ambas as funções de tradução e a função de extração de texto de URLs, o fluxo de trabalho completo parece ser o seguinte:
1. Extração de texto de uma página web usando `extract_text_from_url`.
2. Tradução do texto extraído, usando uma das funções de tradução, dependendo da escolha do usuário ou das configurações.

Esse tipo de projeto é útil para tradução automática de conteúdos de páginas web em escala, permitindo que textos de diversos sites sejam traduzidos para o português ou outro idioma escolhido.
