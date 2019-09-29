---
title: 'Tutorial: Crie um aplicativo Flask para traduzir, sintetizar e analisar o texto API de Tradução de Texto'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você criará um aplicativo Web baseado em Flask que usa os serviços cognitivas do Azure para traduzir texto, analisar sentimentos e sintetizar texto traduzido em fala. Nosso foco é o código Python e as rotas Flask que habilitam nosso aplicativo. Não gastaremos muito tempo no JavaScript que controla o aplicativo, mas fornecemos todos os arquivos para você inspecionar.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 8d85db0e9aa9da48713ca0c119a12160cc99dbff
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671845"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>Tutorial: Criar um aplicativo Flask com os serviços cognitivas do Azure

Neste tutorial, você criará um aplicativo Web Flask que usa os serviços cognitivas do Azure para traduzir texto, analisar sentimentos e sintetizar texto traduzido em fala. Nosso foco é o código Python e as rotas Flask que habilitam nosso aplicativo, no entanto, vamos ajudá-lo com o HTML e o JavaScript que efetuam o Pull do aplicativo. Se você encontrar algum problema, informe-nos usando o botão de comentários abaixo.

Eis o que este tutorial aborda:

> [!div class="checklist"]
> * Obter chaves de assinatura do Azure
> * Configurar seu ambiente de desenvolvimento e instalar dependências
> * Criar um aplicativo Flask
> * Use o API de Tradução de Texto para traduzir o texto
> * Use Análise de Texto para analisar as opiniões positivas/negativas do texto de entrada e das traduções
> * Usar os serviços de fala para converter o texto traduzido em fala sintetizada
> * Executar o aplicativo Flask localmente

> [!TIP]
> Se você quiser pular e ver todo o código de uma só vez, o exemplo inteiro, juntamente com as instruções de compilação, estão disponíveis no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="what-is-flask"></a>O que é o Flask?

Flask é uma microestrutura para a criação de aplicativos Web. Isso significa que o Flask fornece ferramentas, bibliotecas e tecnologias que permitem que você crie um aplicativo Web. Esse aplicativo Web pode ser algumas páginas da Web, um blog, um wiki ou um suplemento como um aplicativo de calendário baseado na Web ou um site comercial.

Para aqueles que desejam aprofundar-se neste tutorial, aqui estão alguns links úteis:

* [Documentação do Flask](http://flask.pocoo.org/)
* [Flask para cópias – um guia para iniciantes de Flask](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>Pré-requisitos

Vamos examinar as chaves de software e assinatura que você precisará para este tutorial.

* [Python 3.5.2 ou posterior](https://www.python.org/downloads/)
* [Ferramentas git](https://git-scm.com/downloads)
* Um IDE ou editor de texto, como [Visual Studio Code](https://code.visualstudio.com/) ou [Atom](https://atom.io/)  
* [Chrome](https://www.google.com/chrome/browser/) ou [Firefox](https://www.mozilla.org/firefox)
* Uma chave de assinatura **tradução de texto** (Observe que não é necessário selecionar uma região).
* Uma chave de assinatura **análise de texto** na região **oeste dos EUA** .
* Uma chave de assinatura **dos serviços de fala** na região **oeste dos EUA** .

## <a name="create-an-account-and-subscribe-to-resources"></a>Criar uma conta e assinar recursos

Conforme mencionado anteriormente, você precisará de três chaves de assinatura para este tutorial. Isso significa que você precisa criar um recurso em sua conta do Azure para:
* Texto do Tradutor
* Análise de Texto
* Serviços de Voz

Use [criar uma conta de serviços cognitivas no portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para obter instruções passo a passo para criar recursos.

> [!IMPORTANT]
> Para este tutorial, crie seus recursos na região oeste dos EUA. Se estiver usando uma região diferente, você precisará ajustar a URL base em cada um dos seus arquivos Python.

## <a name="set-up-your-dev-environment"></a>Configurar o ambiente de desenvolvimento

Antes de criar seu aplicativo Web Flask, você precisará criar um diretório de trabalho para seu projeto e instalar alguns pacotes do Python.

### <a name="create-a-working-directory"></a>Criar um diretório de trabalho

1. Abra a linha de comando (Windows) ou terminal (macOS/Linux). Em seguida, crie um diretório de trabalho e subpastas para seu projeto:  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. Altere para o diretório de trabalho do seu projeto:  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>Criar e ativar seu ambiente virtual com o `virtualenv`

Vamos criar um ambiente virtual para nosso aplicativo Flask usando `virtualenv`. O uso de um ambiente virtual garante que você tenha um ambiente limpo do qual trabalhar.

1. Em seu diretório de trabalho, execute este comando para criar um ambiente virtual: **MacOS/Linux:**
   ```
   virtualenv venv --python=python3
   ```
   Declaramos explicitamente que o ambiente virtual deve usar o Python 3. Isso garante que os usuários com várias instalações do Python estejam usando a versão correta.

   **Bash do Windows CMD/Windows:**
   ```
   virtualenv venv
   ```
   Para simplificar as coisas, estamos nomeando seu ambiente virtual venv.

2. Os comandos para ativar seu ambiente virtual variam de acordo com sua plataforma/Shell:   

   | Plataforma | Shell | Comando |
   |----------|-------|---------|
   | macOS/Linux | bash/zsh | `source venv/bin/activate` |
   | Windows | Raso | `source venv/Scripts/activate` |
   | | Linha de Comandos | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   Depois de executar esse comando, sua linha de comando ou sessão de terminal deve ser precedida por `venv`.

3. Você pode desativar a sessão a qualquer momento digitando-a na linha de comando ou terminal: `deactivate`.

> [!NOTE]
> O Python tem uma ampla documentação para criar e gerenciar ambientes virtuais, consulte [virtualenv](https://virtualenv.pypa.io/en/latest/).

### <a name="install-requests"></a>Solicitações de instalação

Solicitações é um módulo popular que é usado para enviar solicitações HTTP 1,1. Não é necessário adicionar manualmente as cadeias de caracteres de consulta às suas URLs ou para codificar seus dados POST.

1. Para instalar solicitações, execute:

   ```
   pip install requests
   ```

> [!NOTE]
> Se você quiser saber mais sobre solicitações, consulte [Requests: HTTP para seres humanos @ no__t-0.

### <a name="install-and-configure-flask"></a>Instalar e configurar o Flask

Em seguida, precisamos instalar o Flask. O Flask lida com o roteamento de nosso aplicativo Web e nos permite fazer chamadas de servidor para servidor que ocultam nossas chaves de assinatura do usuário final.

1. Para instalar o Flask, execute:
   ```
   pip install Flask
   ```
   Vamos verificar se o Flask foi instalado. Execução:
   ```
   flask --version
   ```
   A versão deve ser impressa no terminal. Qualquer outra coisa significa que algo deu errado.

2. Para executar o aplicativo Flask, você pode usar o comando Flask ou a opção-m do Python com Flask. Antes de fazer isso, você precisa informar ao seu terminal qual aplicativo trabalhar exportando a variável de ambiente `FLASK_APP`:

   **macOS/Linux**:
   ```
   export FLASK_APP=app.py
   ```

   **Windows**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>Criar seu aplicativo Flask

Nesta seção, você criará um aplicativo básicas Flask que retorna um arquivo HTML quando os usuários atingem a raiz do seu aplicativo. Não gaste muito tempo tentando separar o código, voltaremos a atualizar esse arquivo mais tarde.

### <a name="what-is-a-flask-route"></a>O que é uma rota Flask?

Vamos levar um minuto para falar sobre "[rotas](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)". O roteamento é usado para associar uma URL a uma função específica. O Flask usa decoradores de rota para registrar funções em URLs específicas. Por exemplo, quando um usuário navega para a raiz (`/`) de nosso aplicativo Web, `index.html` é renderizado.  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

Vamos dar uma olhada em mais um exemplo para desmartelo essa casa.

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

Esse código garante que quando um usuário navega para `http://your-web-app.com/about` que o arquivo `about.html` é renderizado.

Embora esses exemplos ilustrem como renderizar páginas HTML para um usuário, as rotas também podem ser usadas para chamar APIs quando um botão é pressionado ou executar qualquer número de ações sem precisar navegar para fora da página inicial. Você verá isso em ação ao criar rotas para tradução, sentimentos e síntese de fala.

### <a name="get-started"></a>Introdução

1. Abra o projeto no IDE e crie um arquivo chamado `app.py` na raiz do seu diretório de trabalho. Em seguida, copie esse código para `app.py` e salve:

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   Esse bloco de código informa ao aplicativo para exibir `index.html` sempre que um usuário navega para a raiz do seu aplicativo Web (`/`).

2. Em seguida, vamos criar o front-end para nosso aplicativo Web. Crie um arquivo chamado `index.html` no diretório `templates`. Em seguida, copie esse código para `templates/index.html`.

   ```html
   <!doctype html>
   <html lang="en">
     <head>
       <!-- Required metadata tags -->
       <meta charset="utf-8">
       <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
       <meta name="description" content="Translate and analyze text with Azure Cognitive Services.">
       <!-- Bootstrap CSS -->
       <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
       <title>Translate and analyze text with Azure Cognitive Services</title>
     </head>
     <body>
       <div class="container">
         <h1>Translate, synthesize, and analyze text with Azure</h1>
         <p>This simple web app uses Azure for text translation, text-to-speech conversion, and sentiment analysis of input text and translations. Learn more about <a href="https://docs.microsoft.com/azure/cognitive-services/">Azure Cognitive Services</a>.
        </p>
        <!-- HTML provided in the following sections goes here. -->

        <!-- End -->
       </div>

       <!-- Required Javascript for this tutorial -->
       <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
       <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
       <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
       <script type = "text/javascript" src ="static/scripts/main.js"></script>
     </body>
   </html>
   ```

3. Vamos testar o aplicativo Flask. No terminal, execute:

   ```
   flask run
   ```

4. Abra um navegador e navegue até a URL fornecida. Você deve ver seu aplicativo de página única. Pressione **Ctrl + c** para encerrar o aplicativo.

## <a name="translate-text"></a>Traduzir texto

Agora que você tem uma ideia de como um aplicativo Flask simples funciona, vamos:

* Escrever algum Python para chamar o API de Tradução de Texto e retornar uma resposta
* Criar uma rota Flask para chamar seu código Python
* Atualizar o HTML com uma área para entrada e conversão de texto, um seletor de idioma e um botão de conversão
* Escrever JavaScript que permita que os usuários interajam com seu aplicativo Flask do HTML

### <a name="call-the-translator-text-api"></a>Chamar o API de Tradução de Texto

A primeira coisa que você precisa fazer é escrever uma função para chamar o API de Tradução de Texto. Essa função usará dois argumentos: `text_input` e `language_output`. Essa função é chamada sempre que um usuário pressiona o botão traduzir em seu aplicativo. A área de texto no HTML é enviada como o `text_input`, e o valor de seleção de idioma no HTML é enviado como `language_output`.

1. Vamos começar criando um arquivo chamado `translate.py` na raiz do seu diretório de trabalho.
2. Em seguida, adicione este código a `translate.py`. Essa função usa dois argumentos: `text_input` e `language_output`.
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
   
   # Don't forget to replace with your Cog Services location!
   # Our Flask route will supply two arguments: text_input and language_output.
   # When the translate text button is pressed in our Flask app, the Ajax request
   # will grab these values from our web app, and use them in the request.
   # See main.js for Ajax calls.
   def get_translation(text_input, language_output):
       base_url = 'https://api.cognitive.microsofttranslator.com'
       path = '/translate?api-version=3.0'
       params = '&to=' + language_output
       constructed_url = base_url + path + params

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Ocp-Apim-Subscription-Region': 'location',
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = [{
           'text' : text_input
       }]
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Adicione sua chave de assinatura do Tradução de Texto e salve.

### <a name="add-a-route-to-apppy"></a>Adicionar uma rota a `app.py`

Em seguida, você precisará criar uma rota em seu aplicativo Flask que chama `translate.py`. Essa rota será chamada cada vez que um usuário pressionar o botão traduzir em seu aplicativo.

Para esse aplicativo, sua rota vai aceitar solicitações `POST`. Isso ocorre porque a função espera que o texto seja convertido e um idioma de saída para a tradução.

O Flask fornece funções auxiliares para ajudá-lo a analisar e gerenciar cada solicitação. No código fornecido, `get_json()` retorna os dados da solicitação `POST` como JSON. Em seguida, usando `data['text']` e `data['to']`, os valores de linguagem de texto e saída são passados para a função `get_translation()` disponível em `translate.py`. A última etapa é retornar a resposta como JSON, já que você precisará exibir esses dados em seu aplicativo Web.

Nas seções a seguir, você repetirá esse processo ao criar rotas para análise de sentimentos e síntese de fala.

1. Abra `app.py` e localize a instrução de importação na parte superior de `app.py` e adicione a seguinte linha:

   ```python
   import translate
   ```
   Agora, nosso aplicativo Flask pode usar o método disponível via `translate.py`.

2. Copie este código para o final de `app.py` e salve:

   ```python
   @app.route('/translate-text', methods=['POST'])
   def translate_text():
       data = request.get_json()
       text_input = data['text']
       translation_output = data['to']
       response = translate.get_translation(text_input, translation_output)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>Atualizar `index.html`

Agora que você tem uma função para traduzir texto e uma rota em seu aplicativo Flask para chamá-lo, a próxima etapa é começar a criar o HTML para seu aplicativo. O HTML abaixo faz algumas coisas:

* Fornece uma área de texto onde os usuários podem inserir texto para traduzir.
* Inclui um seletor de idioma.
* Inclui elementos HTML para renderizar a linguagem detectada e as pontuações de confiança retornadas durante a tradução.
* Fornece uma área de texto somente leitura na qual a saída de tradução é exibida.
* Inclui espaços reservados para análise de sentimentos e código de síntese de fala que você adicionará a esse arquivo posteriormente no tutorial.

Vamos atualizar `index.html`.

1. Abra `index.html` e localize estes comentários de código:
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. Substitua os comentários de código por este bloco HTML:
   ```html
   <div class="row">
     <div class="col">
       <form>
         <!-- Enter text to translate. -->
         <div class="form-group">
           <label for="text-to-translate"><strong>Enter the text you'd like to translate:</strong></label>
           <textarea class="form-control" id="text-to-translate" rows="5"></textarea>
         </div>
         <!-- Select output language. -->
         <div class="form-group">
           <label for="select-language"><strong>Translate to:</strong></label>
           <select class="form-control" id="select-language">
             <option value="ar">Arabic</option>
             <option value="ca">Catalan</option>
             <option value="zh-Hans">Chinese (Simplified)</option>
             <option value="zh-Hant">Chinese (Traditional)</option>
             <option value="hr">Croatian</option>
             <option value="en">English</option>
             <option value="fr">French</option>
             <option value="de">German</option>
             <option value="el">Greek</option>
             <option value="he">Hebrew</option>
             <option value="hi">Hindi</option>
             <option value="it">Italian</option>
             <option value="ja">Japanese</option>
             <option value="ko">Korean</option>
             <option value="pt">Portuguese</option>
             <option value="ru">Russian</option>
             <option value="es">Spanish</option>
             <option value="th">Thai</option>
             <option value="tr">Turkish</option>
             <option value="vi">Vietnamese</option>
           </select>
         </div>
         <button type="submit" class="btn btn-primary mb-2" id="translate">Translate text</button></br>
         <div id="detected-language" style="display: none">
           <strong>Detected language:</strong> <span id="detected-language-result"></span><br />
           <strong>Detection confidence:</strong> <span id="confidence"></span><br /><br />
         </div>

         <!-- Start sentiment code-->

         <!-- End sentiment code -->

       </form>
     </div>
     <div class="col">
       <!-- Translated text returned by the Translate API is rendered here. -->
       <form>
         <div class="form-group" id="translator-text-response">
           <label for="translation-result"><strong>Translated text:</strong></label>
           <textarea readonly class="form-control" id="translation-result" rows="5"></textarea>
         </div>

         <!-- Start voice font selection code -->

         <!-- End voice font selection code -->

       </form>

       <!-- Add Speech Synthesis button and audio element -->

       <!-- End Speech Synthesis button -->

     </div>
   </div>
   ```

A próxima etapa é escrever algum JavaScript. Essa é a ponte entre a rota de HTML e Flask.

### <a name="create-mainjs"></a>Criar `main.js`  

O arquivo `main.js` é a ponte entre a rota de HTML e Flask. Seu aplicativo usará uma combinação de jQuery, Ajax e XMLHttpRequest para renderizar conteúdo e fazer solicitações `POST` para suas rotas Flask.

No código a seguir, o conteúdo do HTML é usado para construir uma solicitação para sua rota do Flask. Especificamente, o conteúdo da área de texto e do seletor de idioma são atribuídos a variáveis e, em seguida, passados na solicitação para `translate-text`.

Em seguida, o código faz a iteração por meio da resposta e atualiza o HTML com a tradução, o idioma detectado e a pontuação de confiança.

1. Em seu IDE, crie um arquivo chamado `main.js` no diretório `static/scripts`.
2. Copie este código em `static/scripts/main.js`:
   ```javascript
   //Initiate jQuery on load.
   $(function() {
     //Translate text with flask route
     $("#translate").on("click", function(e) {
       e.preventDefault();
       var translateVal = document.getElementById("text-to-translate").value;
       var languageVal = document.getElementById("select-language").value;
       var translateRequest = { 'text': translateVal, 'to': languageVal }

       if (translateVal !== "") {
         $.ajax({
           url: '/translate-text',
           method: 'POST',
           headers: {
               'Content-Type':'application/json'
           },
           dataType: 'json',
           data: JSON.stringify(translateRequest),
           success: function(data) {
             for (var i = 0; i < data.length; i++) {
               document.getElementById("translation-result").textContent = data[i].translations[0].text;
               document.getElementById("detected-language-result").textContent = data[i].detectedLanguage.language;
               if (document.getElementById("detected-language-result").textContent !== ""){
                 document.getElementById("detected-language").style.display = "block";
               }
               document.getElementById("confidence").textContent = data[i].detectedLanguage.score;
             }
           }
         });
       };
     });
     // In the following sections, you'll add code for sentiment analysis and
     // speech synthesis here.
   })
   ```

### <a name="test-translation"></a>Conversão de teste

Vamos testar a tradução no aplicativo.

```
flask run
```

Navegue até o endereço do servidor fornecido. Digite o texto na área de entrada, selecione um idioma e clique em traduzir. Você deve obter uma tradução. Se não funcionar, certifique-se de ter adicionado sua chave de assinatura.

> [!TIP]
> Se as alterações feitas não estiverem aparecendo ou se o aplicativo não funcionar da maneira esperada, tente limpar o cache ou abrir uma janela particular/Incognito.

Pressione **Ctrl + c** para eliminar o aplicativo e, em seguida, vá para a próxima seção.

## <a name="analyze-sentiment"></a>Analisar sentimento

O [API de análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) pode ser usado para executar a análise de sentimentos, extrair frases-chave do texto ou detectar o idioma de origem. Neste aplicativo, vamos usar a análise de sentimentos para determinar se o texto fornecido é positivo, neutro ou negativo. A API devolve uma pontuação numérica entre 0 e 1. As pontuações próximas de 1 indicam um sentimento positivo, enquanto as pontuações próximas de 0 indicam um sentimento negativo.

Nesta seção, você vai fazer algumas coisas:

* Escreva alguns Python para chamar o API de Análise de Texto para executar a análise de sentimentos e retornar uma resposta
* Criar uma rota Flask para chamar seu código Python
* Atualizar o HTML com uma área para pontuações de sentimentos e um botão para executar a análise
* Escrever JavaScript que permita que os usuários interajam com seu aplicativo Flask do HTML

### <a name="call-the-text-analytics-api"></a>Chamar a API de Análise de Texto

Vamos escrever uma função para chamar o API de Análise de Texto. Essa função usará quatro argumentos: `input_text`, `input_language`, `output_text` e `output_language`. Essa função é chamada sempre que um usuário pressiona o botão executar análise de sentimentos em seu aplicativo. Os dados fornecidos pelo usuário na área de texto e no seletor de idioma, bem como o idioma detectado e a saída de tradução são fornecidos com cada solicitação. O objeto de resposta inclui pontuações de sentimentos para a origem e a tradução. Nas seções a seguir, você vai escrever algum JavaScript para analisar a resposta e usá-la em seu aplicativo. Por enquanto, vamos nos concentrar em chamar o API de Análise de Texto.

1. Vamos criar um arquivo chamado `sentiment.py` na raiz do seu diretório de trabalho.
2. Em seguida, adicione este código a `sentiment.py`.
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   subscription_key = 'YOUR_TEXT_ANALYTICS_SUBSCRIPTION_KEY'

   # Our Flask route will supply four arguments: input_text, input_language,
   # output_text, output_language.
   # When the run sentiment analysis button is pressed in our Flask app,
   # the Ajax request will grab these values from our web app, and use them
   # in the request. See main.js for Ajax calls.

   def get_sentiment(input_text, input_language, output_text, output_language):
       base_url = 'https://westus.api.cognitive.microsoft.com/text/analytics'
       path = '/v2.0/sentiment'
       constructed_url = base_url + path

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = {
           'documents': [
               {
                   'language': input_language,
                   'id': '1',
                   'text': input_text
               },
               {
                   'language': output_language,
                   'id': '2',
                   'text': output_text
               }
           ]
       }
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Adicione sua chave de assinatura do Análise de Texto e salve.

### <a name="add-a-route-to-apppy"></a>Adicionar uma rota a `app.py`

Vamos criar uma rota em seu aplicativo Flask que chama `sentiment.py`. Essa rota será chamada cada vez que um usuário pressionar o botão executar análise de sentimentos em seu aplicativo. Como a rota para a tradução, essa rota vai aceitar solicitações `POST`, pois a função espera argumentos.

1. Abra `app.py` e localize a instrução de importação na parte superior de `app.py` e atualize-a:

   ```python
   import translate, sentiment
   ```
   Agora, nosso aplicativo Flask pode usar o método disponível via `sentiment.py`.

2. Copie este código para o final de `app.py` e salve:
   ```python
   @app.route('/sentiment-analysis', methods=['POST'])
   def sentiment_analysis():
       data = request.get_json()
       input_text = data['inputText']
       input_lang = data['inputLanguage']
       output_text = data['outputText']
       output_lang =  data['outputLanguage']
       response = sentiment.get_sentiment(input_text, input_lang, output_text, output_lang)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>Atualizar `index.html`

Agora que você tem uma função para executar a análise de sentimentos e uma rota em seu aplicativo Flask para chamá-la, a próxima etapa é começar a gravar o HTML para seu aplicativo. O HTML abaixo faz algumas coisas:

* Adiciona um botão ao seu aplicativo para executar a análise de sentimentos
* Adiciona um elemento que explica a pontuação de sentimentos
* Adiciona um elemento para exibir as pontuações de sentimentos

1. Abra `index.html` e localize estes comentários de código:
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. Substitua os comentários de código por este bloco HTML:
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment scores are provided on a 1 point scale. The closer the sentiment score is to 1, indicates positive sentiment. The closer it is to 0, indicates negative sentiment.</p>
      <strong>Sentiment score for input:</strong> <span id="input-sentiment"></span><br />
      <strong>Sentiment score for translation:</strong> <span id="translation-sentiment"></span>
   </div>
   ```

### <a name="update-mainjs"></a>Atualizar `main.js`

No código a seguir, o conteúdo do HTML é usado para construir uma solicitação para sua rota do Flask. Especificamente, o conteúdo da área de texto e do seletor de idioma são atribuídos a variáveis e, em seguida, passados na solicitação para a rota `sentiment-analysis`.

Em seguida, o código faz a iteração pela resposta e atualiza o HTML com as pontuações de sentimentos.

1. Em seu IDE, crie um arquivo chamado `main.js` no diretório `static`.

2. Copie este código em `static/scripts/main.js`:
   ```javascript
   //Run sentinment analysis on input and translation.
   $("#sentiment-analysis").on("click", function(e) {
     e.preventDefault();
     var inputText = document.getElementById("text-to-translate").value;
     var inputLanguage = document.getElementById("detected-language-result").innerHTML;
     var outputText = document.getElementById("translation-result").value;
     var outputLanguage = document.getElementById("select-language").value;

     var sentimentRequest = { "inputText": inputText, "inputLanguage": inputLanguage, "outputText": outputText,  "outputLanguage": outputLanguage };

     if (inputText !== "") {
       $.ajax({
         url: "/sentiment-analysis",
         method: "POST",
         headers: {
             "Content-Type":"application/json"
         },
         dataType: "json",
         data: JSON.stringify(sentimentRequest),
         success: function(data) {
           for (var i = 0; i < data.documents.length; i++) {
             if (typeof data.documents[i] !== "undefined"){
               if (data.documents[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.documents[i].score;
               }
               if (data.documents[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.documents[i].score;
               }
             }
           }
           for (var i = 0; i < data.errors.length; i++) {
             if (typeof data.errors[i] !== "undefined"){
               if (data.errors[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.errors[i].message;
               }
               if (data.errors[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.errors[i].message;
               }
             }
           }
           if (document.getElementById("input-sentiment").textContent !== '' && document.getElementById("translation-sentiment").textContent !== ""){
             document.getElementById("sentiment").style.display = "block";
           }
         }
       });
     }
   });
   // In the next section, you'll add code for speech synthesis here.
   ```

### <a name="test-sentiment-analysis"></a>Análise de sentimentos de teste

Vamos testar a análise de sentimentos no aplicativo.

```
flask run
```

Navegue até o endereço do servidor fornecido. Digite o texto na área de entrada, selecione um idioma e clique em traduzir. Você deve obter uma tradução. Em seguida, pressione o botão executar análise de sentimentos. Você deve ver duas pontuações. Se não funcionar, certifique-se de ter adicionado sua chave de assinatura.

> [!TIP]
> Se as alterações feitas não estiverem aparecendo ou se o aplicativo não funcionar da maneira esperada, tente limpar o cache ou abrir uma janela particular/Incognito.

Pressione **Ctrl + c** para eliminar o aplicativo e, em seguida, vá para a próxima seção.

## <a name="convert-text-to-speech"></a>Converter texto em voz

A API de conversão de [texto em fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) permite que seu aplicativo Converta texto em fala sintetizada semelhante à humana. O serviço oferece suporte a vozes padrão, neural e personalizadas. Nosso aplicativo de exemplo usa algumas das vozes disponíveis, para obter uma lista completa, consulte [idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech).

Nesta seção, você vai fazer algumas coisas:

* Escreva alguns Python para converter o texto em fala com a API de conversão de texto em fala
* Criar uma rota Flask para chamar seu código Python
* Atualize o HTML com um botão para converter conversão de texto em fala e um elemento para reprodução de áudio
* Escrever JavaScript que permita que os usuários interajam com seu aplicativo Flask

### <a name="call-the-text-to-speech-api"></a>Chamar o texto para Speech API

Vamos escrever uma função para converter conversão de texto em fala. Essa função usará dois argumentos: `input_text` e `voice_font`. Essa função é chamada sempre que um usuário pressiona o botão converter conversão de texto em fala em seu aplicativo. `input_text` é a saída de tradução retornada pela chamada para traduzir texto, `voice_font` é o valor do seletor de fonte de voz no HTML.

1. Vamos criar um arquivo chamado `synthesize.py` na raiz do seu diretório de trabalho.

2. Em seguida, adicione este código a `synthesize.py`.
   ```Python
   import os, requests, time
   from xml.etree import ElementTree

   class TextToSpeech(object):
       def __init__(self, input_text, voice_font):
           subscription_key = 'YOUR_SPEECH_SERVICES_SUBSCRIPTION_KEY'
           self.subscription_key = subscription_key
           self.input_text = input_text
           self.voice_font = voice_font
           self.timestr = time.strftime('%Y%m%d-%H%M')
           self.access_token = None

       # This function performs the token exchange.
       def get_token(self):
           fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
           headers = {
               'Ocp-Apim-Subscription-Key': self.subscription_key
           }
           response = requests.post(fetch_token_url, headers=headers)
           self.access_token = str(response.text)

       # This function calls the TTS endpoint with the access token.
       def save_audio(self):
           base_url = 'https://westus.tts.speech.microsoft.com/'
           path = 'cognitiveservices/v1'
           constructed_url = base_url + path
           headers = {
               'Authorization': 'Bearer ' + self.access_token,
               'Content-Type': 'application/ssml+xml',
               'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
               'User-Agent': 'YOUR_RESOURCE_NAME',
           }
           # Build the SSML request with ElementTree
           xml_body = ElementTree.Element('speak', version='1.0')
           xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
           voice = ElementTree.SubElement(xml_body, 'voice')
           voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
           voice.set('name', 'Microsoft Server Speech Text to Speech Voice {}'.format(self.voice_font))
           voice.text = self.input_text
           # The body must be encoded as UTF-8 to handle non-ascii characters.
           body = ElementTree.tostring(xml_body, encoding="utf-8")

           #Send the request
           response = requests.post(constructed_url, headers=headers, data=body)

           # Write the response as a wav file for playback. The file is located
           # in the same directory where this sample is run.
           return response.content
   ```
3. Adicione sua chave de assinatura dos serviços de fala e salve.

### <a name="add-a-route-to-apppy"></a>Adicionar uma rota a `app.py`

Vamos criar uma rota em seu aplicativo Flask que chama `synthesize.py`. Essa rota será chamada cada vez que um usuário pressionar o botão converter conversão de texto em fala em seu aplicativo. Assim como as rotas para a análise de informações e de tradução, essa rota vai aceitar solicitações `POST`, pois a função espera dois argumentos: o texto a ser sintetizado e a fonte de voz para reprodução.

1. Abra `app.py` e localize a instrução de importação na parte superior de `app.py` e atualize-a:

   ```python
   import translate, sentiment, synthesize
   ```
   Agora, nosso aplicativo Flask pode usar o método disponível via `synthesize.py`.

2. Copie este código para o final de `app.py` e salve:

   ```Python
   @app.route('/text-to-speech', methods=['POST'])
   def text_to_speech():
       data = request.get_json()
       text_input = data['text']
       voice_font = data['voice']
       tts = synthesize.TextToSpeech(text_input, voice_font)
       tts.get_token()
       audio_response = tts.save_audio()
       return audio_response
   ```

### <a name="update-indexhtml"></a>Atualizar `index.html`

Agora que você tem uma função para converter conversão de texto em fala e uma rota em seu aplicativo Flask para chamá-la, a próxima etapa é começar a gravar o HTML para seu aplicativo. O HTML abaixo faz algumas coisas:

* Fornece uma lista suspensa de seleção de voz
* Adiciona um botão para converter conversão de texto em fala
* Adiciona um elemento Audio, que é usado para reproduzir a fala sintetizada

1. Abra `index.html` e localize estes comentários de código:
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. Substitua os comentários de código por este bloco HTML:
   ```html
   <div class="form-group">
     <label for="select-voice"><strong>Select voice font:</strong></label>
     <select class="form-control" id="select-voice">
       <option value="(ar-SA, Naayf)">Arabic | Male | Naayf</option>
       <option value="(ca-ES, HerenaRUS)">Catalan | Female | HerenaRUS</option>
       <option value="(zh-CN, HuihuiRUS)">Chinese (Mainland) | Female | HuihuiRUS</option>
       <option value="(zh-CN, Kangkang, Apollo)">Chinese (Mainland) | Male | Kangkang, Apollo</option>
       <option value="(zh-HK, Tracy, Apollo)">Chinese (Hong Kong)| Female | Tracy, Apollo</option>
       <option value="(zh-HK, Danny, Apollo)">Chinese (Hong Kong) | Male | Danny, Apollo</option>
       <option value="(zh-TW, Yating, Apollo)">Chinese (Taiwan)| Female | Yaiting, Apollo</option>
       <option value="(zh-TW, Zhiwei, Apollo)">Chinese (Taiwan) | Male | Zhiwei, Apollo</option>
       <option value="(hr-HR, Matej)">Croatian | Male | Matej</option>
       <option value="(en-US, Jessa24kRUS)">English (US) | Female | Jessa24kRUS</option>
       <option value="(en-US, Guy24kRUS)">English (US) | Male | Guy24kRUS</option>
       <option value="(en-IE, Sean)">English (IE) | Male | Sean</option>
       <option value="(fr-FR, Julie, Apollo)">French | Female | Julie, Apollo</option>
       <option value="(fr-FR, HortenseRUS)">French | Female | Julie, HortenseRUS</option>
       <option value="(fr-FR, Paul, Apollo)">French | Male | Paul, Apollo</option>
       <option value="(de-DE, Hedda)">German | Female | Hedda</option>
       <option value="(de-DE, HeddaRUS)">German | Female | HeddaRUS</option>
       <option value="(de-DE, Stefan, Apollo)">German | Male | Apollo</option>
       <option value="(el-GR, Stefanos)">Greek | Male | Stefanos</option>
       <option value="(he-IL, Asaf)">Hebrew (Isreal) | Male | Asaf</option>
       <option value="(hi-IN, Kalpana, Apollo)">Hindi | Female | Kalpana, Apollo</option>
       <option value="(hi-IN, Hemant)">Hindi | Male | Hemant</option>
       <option value="(it-IT, LuciaRUS)">Italian | Female | LuciaRUS</option>
       <option value="(it-IT, Cosimo, Apollo)">Italian | Male | Cosimo, Apollo</option>
       <option value="(ja-JP, Ichiro, Apollo)">Japanese | Male | Ichiro</option>
       <option value="(ja-JP, HarukaRUS)">Japanese | Female | HarukaRUS</option>
       <option value="(ko-KR, HeamiRUS)">Korean | Female | Haemi</option>
       <option value="(pt-BR, HeloisaRUS)">Portuguese (Brazil) | Female | HeloisaRUS</option>
       <option value="(pt-BR, Daniel, Apollo)">Portuguese (Brazil) | Male | Daniel, Apollo</option>
       <option value="(pt-PT, HeliaRUS)">Portuguese (Portugal) | Female | HeliaRUS</option>
       <option value="(ru-RU, Irina, Apollo)">Russian | Female | Irina, Apollo</option>
       <option value="(ru-RU, Pavel, Apollo)">Russian | Male | Pavel, Apollo</option>
       <option value="(ru-RU, EkaterinaRUS)">Russian | Female | EkaterinaRUS</option>
       <option value="(es-ES, Laura, Apollo)">Spanish | Female | Laura, Apollo</option>
       <option value="(es-ES, HelenaRUS)">Spanish | Female | HelenaRUS</option>
       <option value="(es-ES, Pablo, Apollo)">Spanish | Male | Pablo, Apollo</option>
       <option value="(th-TH, Pattara)">Thai | Male | Pattara</option>
       <option value="(tr-TR, SedaRUS)">Turkish | Female | SedaRUS</option>
       <option value="(vi-VN, An)">Vietnamese | Male | An</option>
     </select>
   </div>
   ```

3. Em seguida, localize estes comentários de código:
   ```html
   <!-- Add Speech Synthesis button and audio element -->

   <!-- End Speech Synthesis button -->
   ```

4. Substitua os comentários de código por este bloco HTML:

```html
<button type="submit" class="btn btn-primary mb-2" id="text-to-speech">Convert text-to-speech</button>
<div id="audio-playback">
  <audio id="audio" controls>
    <source id="audio-source" type="audio/mpeg" />
  </audio>
</div>
```

5. Certifique-se de salvar seu trabalho.

### <a name="update-mainjs"></a>Atualizar `main.js`

No código a seguir, o conteúdo do HTML é usado para construir uma solicitação para sua rota do Flask. Especificamente, a conversão e a fonte de voz são atribuídas a variáveis e, em seguida, passadas na solicitação para a rota `text-to-speech`.

Em seguida, o código faz a iteração pela resposta e atualiza o HTML com as pontuações de sentimentos.

1. Em seu IDE, crie um arquivo chamado `main.js` no diretório `static`.
2. Copie este código em `static/scripts/main.js`:
   ```javascript
   // Convert text-to-speech
   $("#text-to-speech").on("click", function(e) {
     e.preventDefault();
     var ttsInput = document.getElementById("translation-result").value;
     var ttsVoice = document.getElementById("select-voice").value;
     var ttsRequest = { 'text': ttsInput, 'voice': ttsVoice }

     var xhr = new XMLHttpRequest();
     xhr.open("post", "/text-to-speech", true);
     xhr.setRequestHeader("Content-Type", "application/json");
     xhr.responseType = "blob";
     xhr.onload = function(evt){
       if (xhr.status === 200) {
         audioBlob = new Blob([xhr.response], {type: "audio/mpeg"});
         audioURL = URL.createObjectURL(audioBlob);
         if (audioURL.length > 5){
           var audio = document.getElementById("audio");
           var source = document.getElementById("audio-source");
           source.src = audioURL;
           audio.load();
           audio.play();
         }else{
           console.log("An error occurred getting and playing the audio.")
         }
       }
     }
     xhr.send(JSON.stringify(ttsRequest));
   });
   // Code for automatic language selection goes here.
   ```
3. Está quase concluído. A última coisa que você vai fazer é adicionar algum código a `main.js` para selecionar automaticamente uma fonte de voz com base no idioma selecionado para tradução. Adicione este bloco de código a `main.js`:
   ```javascript
   // Automatic voice font selection based on translation output.
   $('select[id="select-language"]').change(function(e) {
     if ($(this).val() == "ar"){
       document.getElementById("select-voice").value = "(ar-SA, Naayf)";
     }
     if ($(this).val() == "ca"){
       document.getElementById("select-voice").value = "(ca-ES, HerenaRUS)";
     }
     if ($(this).val() == "zh-Hans"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "zh-Hant"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "hr"){
       document.getElementById("select-voice").value = "(hr-HR, Matej)";
     }
     if ($(this).val() == "en"){
       document.getElementById("select-voice").value = "(en-US, Jessa24kRUS)";
     }
     if ($(this).val() == "fr"){
       document.getElementById("select-voice").value = "(fr-FR, HortenseRUS)";
     }
     if ($(this).val() == "de"){
       document.getElementById("select-voice").value = "(de-DE, HeddaRUS)";
     }
     if ($(this).val() == "el"){
       document.getElementById("select-voice").value = "(el-GR, Stefanos)";
     }
     if ($(this).val() == "he"){
       document.getElementById("select-voice").value = "(he-IL, Asaf)";
     }
     if ($(this).val() == "hi"){
       document.getElementById("select-voice").value = "(hi-IN, Kalpana, Apollo)";
     }
     if ($(this).val() == "it"){
       document.getElementById("select-voice").value = "(it-IT, LuciaRUS)";
     }
     if ($(this).val() == "ja"){
       document.getElementById("select-voice").value = "(ja-JP, HarukaRUS)";
     }
     if ($(this).val() == "ko"){
       document.getElementById("select-voice").value = "(ko-KR, HeamiRUS)";
     }
     if ($(this).val() == "pt"){
       document.getElementById("select-voice").value = "(pt-BR, HeloisaRUS)";
     }
     if ($(this).val() == "ru"){
       document.getElementById("select-voice").value = "(ru-RU, EkaterinaRUS)";
     }
     if ($(this).val() == "es"){
       document.getElementById("select-voice").value = "(es-ES, HelenaRUS)";
     }
     if ($(this).val() == "th"){
       document.getElementById("select-voice").value = "(th-TH, Pattara)";
     }
     if ($(this).val() == "tr"){
       document.getElementById("select-voice").value = "(tr-TR, SedaRUS)";
     }
     if ($(this).val() == "vi"){
       document.getElementById("select-voice").value = "(vi-VN, An)";
     }
   });
   ```

### <a name="test-your-app"></a>Testar a aplicação

Vamos testar a síntese de fala no aplicativo.

```
flask run
```

Navegue até o endereço do servidor fornecido. Digite o texto na área de entrada, selecione um idioma e clique em traduzir. Você deve obter uma tradução. Em seguida, selecione uma voz e pressione o botão converter conversão de texto em fala. a tradução deve ser reproduzida como fala sintetizada. Se não funcionar, certifique-se de ter adicionado sua chave de assinatura.

> [!TIP]
> Se as alterações feitas não estiverem aparecendo ou se o aplicativo não funcionar da maneira esperada, tente limpar o cache ou abrir uma janela particular/Incognito.

É isso, você tem um aplicativo de trabalho que executa traduções, analisa sentimentos e fala sintetizada. Pressione **Ctrl + c** para encerrar o aplicativo. Certifique-se de conferir os outros [Serviços cognitivas do Azure](https://docs.microsoft.com/azure/cognitive-services/).

## <a name="get-the-source-code"></a>Obter o código-fonte

O código-fonte deste projeto está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="next-steps"></a>Passos seguintes

* [Referência da API de Texto do Microsoft Translator](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
* [Text Analytics API reference](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) (Referência à API de Análise de Texto)
* [Referência da API de conversão de texto em voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)
