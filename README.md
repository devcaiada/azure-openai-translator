# Tradutor de Documento utilizando Azure e OpenAI GPT4o-mini

Criando um serviço de tradução no Microsoft Azure e usando a API para traduzir um arquivo do Word com uma música em inglês para português (Brasil).

## Criando o Serviço de Tradução no Azure

### 1. Acesse o Portal do Azure:

- Acesse o [Portal do Azure](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?redirect_uri=https%3A%2F%2Fportal.azure.com%2Fsignin%2Findex%2F&response_type=code%20id_token&scope=https%3A%2F%2Fmanagement.core.windows.net%2F%2Fuser_impersonation%20openid%20email%20profile&state=OpenIdConnect.AuthenticationProperties%3D_ZvB7Dex0LoCtOMBba3b5EF55VoYbA__Jg14bZc9FWRyn-cGqCP22hTKOhDR37rVgBz9ZJpV1P3sxnuJiQVbP8fg9BjJFW9MsecrjOu3OM2yikbEUFG9XKIwFP76Cu_WlTPsB3wdSbm1Ocots4bfsDMTtZO-Zdm1BSj-7Wz1ibjZUemvQSaymb0a5Bft6hXqKLq1auG_poiqn53x0cSW4wGEoIeT8H1EgkJjuGNfYEySVAaRukgz1ZRDpZrmcdt79ah9UH9bvcsCxhHSuqHPTR1ZnneZ64oSZCAxaAlx0XoRTU4AFAXmtyn5yau5LvvtvKXB7yjiRUTWrPRlXd3bcSUnHXQMj4b4JhMi4Wz0oz28zkB2sZaUN4uEYcN1cs6Vgku-Vsp2LA-MRcDiffxcPTGDBQrUnpjnISCuEyifZjkOr40AauFgoFvYFEK-w8_oMikGNpE9KWvZ2PahAI8euhZRqSGenEkmXs_clt7uaO0&response_mode=form_post&nonce=638659947937869079.ZmFlMmY4ZjYtNjQ5MC00NDE3LTkzNmItOTc2YmJiMGI4Nzg2Zjc5YTk1NGMtNTVmYi00NjZjLTlkYjQtZmVhNjFkMjRkNTg4&client_id=c44b4083-3bb0-49c1-b47d-974e53cbdf3c&site_id=501430&client-request-id=e7b361e0-8e67-4f94-ba8a-78b1cc5b204d&x-client-SKU=ID_NET472&x-client-ver=7.5.0.0&sso_reload=true).
- Faça login com sua conta.

### 2. Crie um Novo Recurso:

- No menu do lado esquerdo, clique em **Criar um recurso**.
- Na barra de pesquisa, digite **Translator** e selecione **Translator**.

### 3. Configurar o Serviço de Tradução:

- Selecione **Criar** e configure os detalhes do serviço:
  - **Assinatura**: Selecione a sua assinatura do Azure.
  - **Grupo de recursos**: Crie um novo grupo ou selecione um existente.
  - **Região**: Escolha a região mais próxima para a implantação global.
  - **Nome do recurso**: Defina um nome único para seu serviço.
- Após configurar, clique em **Revisar + Criar** e em seguida em **Criar**.

### 4. Obtenha a Chave de API e o Endpoint:

- Após a criação, vá para o recurso de **Translator**.
- No menu lateral, clique em **Chaves e endpoint**. Aqui, você encontrará a **Chave de API** e o **Endpoint** que serão usados no código Python.

## Utilizando o GPT-4o-mini para Traduções (Opção Alternativa)

No caso do **GPT-4**, você precisa selecionar o recurso de **Azure OpenAI** em vez do **Translator** se deseja utilizar modelos da série **GPT**. para traduções. O **Azure Translator**. oferece uma camada gratuita para a tradução de textos de até **2 milhões de caracteres**. por mês, ideal para empresas e indivíduos que precisam de traduções rápidas e eficientes sem custos iniciais. Esse volume de caracteres é ótimo para pequenas demandas, como tradução de documentos curtos, websites ou conteúdo de marketing.

No entanto, para volumes maiores, o custo pode crescer significativamente. O Translator do Azure, ao ultrapassar os 2 milhões de caracteres, passa a cobrar cerca de **$10 por milhão de caracteres**. adicionais. Em situações de alta demanda, como em grandes fluxos de tradução ou necessidades contínuas, essa cobrança pode se tornar mais custosa do que o uso de modelos como o **GPT-4o-mini**.

**Para criar um recurso Azure OpenAI:**

- Siga os mesmos passos de criação de um recurso, mas pesquise por **Azure OpenAI**.
- Escolha uma configuração que permita acesso ao **GPT-4o-mini** (ou **GPT-4** se disponível).
- Após a criação, você obterá uma **Chave de API** e um **Endpoint** específico para esse recurso.

## Código Python para Tradução de Arquivo Word

Abaixo está um exemplo de código Python para ler um arquivo **.docx** (com a letra da música), traduzir o texto e salvar o resultado em um novo arquivo **.docx** traduzido:

**Requisitos:**

```python
pip install python-docx requests
```

**Código Python:**

```python
import requests
from docx import Document
import os

# Caminhos para os arquivos de entrada e saída
input_path = "input/letra_musica.docx"
output_path = "output/letra_musica_traduzida.docx"

# Configurações da API
api_key = "SUA_CHAVE_DE_API"
endpoint = "SEU_ENDPOINT"
translator_endpoint = f"{endpoint}/translate?api-version=3.0"

# Função para ler o texto do arquivo Word
def read_word_file(file_path):
   doc = Document(file_path)
   return "\n".join([para.text for para in doc.paragraphs])

# Função para escrever o texto traduzido em um novo arquivo Word
def write_word_file(text, file_path):
   doc = Document()
   doc.add_paragraph(text)
   doc.save(file_path)

# Função para traduzir o texto
def translate_text(text, to_language="pt-br"):
   headers = {
       "Ocp-Apim-Subscription-Key": api_key,
       "Content-Type": "application/json",
       "Ocp-Apim-Subscription-Region": "REGIÃO_DO_SEU_SERVIÇO"  # Ex: "brazilsouth"
   }
   body = [{"text": text}]
   params = {"to": to_language}

   response = requests.post(translator_endpoint, headers=headers, json=body, params=params)
   response.raise_for_status()

   # Extrai o texto traduzido
   translation = response.json()[0]["translations"][0]["text"]
   return translation

# Executa a tradução
try:
   # Leitura do texto original
   original_text = read_word_file(input_path)

   # Tradução do texto
   translated_text = translate_text(original_text)

   # Salvando o texto traduzido no arquivo de saída
   write_word_file(translated_text, output_path)

   print("Tradução realizada com sucesso!")
except Exception as e:
   print(f"Erro ao realizar a tradução: {e}")
```

### Explicação do Código

1. **Configuração**: Insira sua chave de API e o endpoint que você obteve ao criar o serviço no Azure.
2. **Funções**:

- **read_word_file**: Lê o arquivo de entrada e extrai o texto.
- **translate_text**: Envia o texto para o endpoint de tradução e recebe o texto traduzido.
- **write_word_file**: Escreve o texto traduzido em um novo arquivo .docx.

3. **Execução**: O texto é lido, traduzido e salvo em uma nova pasta **output**.

Esse código permitirá traduzir o conteúdo do arquivo .docx de uma música em inglês para português e salvar o resultado em um novo arquivo.
