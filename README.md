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
