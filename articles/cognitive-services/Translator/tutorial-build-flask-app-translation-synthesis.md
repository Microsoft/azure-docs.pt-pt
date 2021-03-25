---
title: 'Tutorial: Construir uma app Flask para traduzir, sintetizar e analisar texto - Tradutor'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você vai construir uma aplicação web baseada em Flask para traduzir texto, analisar sentimentos e sintetizar texto traduzido em discurso.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 03/04/2021
ms.author: lajanuar
ms.custom: devx-track-python, devx-track-js
ms.openlocfilehash: 6ec951e57b40ae1440f541c02b26e7788b3cf151
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043738"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>Tutorial: Construir uma app Flask com Serviços Cognitivos Azure

Neste tutorial, você vai construir uma aplicação web Flask que usa os Serviços Cognitivos Azure para traduzir texto, analisar sentimentos e sintetizar texto traduzido em discurso. O nosso foco está nas rotas python e flask que permitem a nossa aplicação, no entanto, vamos ajudá-lo com o HTML e JavaScript que reúne a app. Se encontrar algum problema, avise-nos usando o botão de feedback abaixo.

Aqui está o que este tutorial cobre:

> [!div class="checklist"]
> * Obtenha chaves de subscrição Azure
> * Configurar o seu ambiente de desenvolvimento e instalar dependências
> * Criar uma aplicação Flask
> * Utilize o Tradutor para traduzir texto
> * Use o Text Analytics para analisar o sentimento positivo/negativo do texto de entrada e traduções
> * Use serviços de fala para converter texto traduzido em discurso sintetizado
> * Executar a sua app Flask localmente

> [!TIP]
> Se quiser antecipar-se e ver todo o código de uma só vez, toda a amostra, juntamente com instruções de construção estão disponíveis no [GitHub.](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial)

## <a name="what-is-flask"></a>O que é o Flask?

O frasco é um micro-quadro para a criação de aplicações web. Isto significa que o Flask fornece-lhe ferramentas, bibliotecas e tecnologias que lhe permitem construir uma aplicação web. Esta aplicação web pode ser algumas páginas web, um blog, um wiki ou ir tão substantivo como uma aplicação de calendário baseada na web ou um site comercial.

Para aqueles que querem mergulhar profundamente depois deste tutorial aqui estão alguns links úteis:

* [Documentação de frasco](http://flask.pocoo.org/)
* [Frasco para bonecos - Um Guia de Principiantes para O Frasco](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>Pré-requisitos

Vamos rever o software e as chaves de subscrição que você precisa para este tutorial.

* [Python 3.6 ou mais tarde](https://www.python.org/downloads/)
* [Ferramentas de git](https://git-scm.com/downloads)
* Um IDE ou editor de texto, como [Visual Studio Code](https://code.visualstudio.com/) ou [Átomo](https://atom.io/)  
* [Chrome](https://www.google.com/chrome/browser/) ou [Firefox](https://www.mozilla.org/firefox)
* Uma chave de subscrição **de Tradutor** (é provável que utilize a localização **global.)**
* Uma chave de subscrição **de Text Analytics** na região oeste dos **EUA.**
* Uma chave de subscrição **de Serviços de Fala** na região oeste dos **EUA.**

## <a name="create-an-account-and-subscribe-to-resources"></a>Criar uma conta e subscrever recursos

Como mencionado anteriormente, vai precisar de três chaves de subscrição para este tutorial. Isto significa que precisa de criar um recurso dentro da sua conta Azure para:
* Tradutor
* Análise de Texto
* Serviços de Voz

Utilizar [Criar uma Conta de Serviços Cognitivos no portal Azure](../cognitive-services-apis-create-account.md) para instruções passo a passo para criar recursos.

> [!IMPORTANT]
> Para este tutorial, por favor crie os seus recursos na região oeste dos EUA. Se utilizar uma região diferente, terá de ajustar o URL base em cada um dos seus ficheiros Python.

## <a name="set-up-your-dev-environment"></a>Configurar o ambiente de desenvolvimento

Antes de construir a sua aplicação web Flask, terá de criar um diretório de trabalho para o seu projeto e instalar alguns pacotes Python.

### <a name="create-a-working-directory"></a>Criar um diretório de trabalho

1. Linha de comando aberta (Windows) ou terminal (macOS/Linux). Em seguida, crie um diretório de trabalho e sub-directórios para o seu projeto:  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. Mude para o diretório de trabalho do seu projeto:  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>Crie e ative o seu ambiente virtual com `virtualenv`

Vamos criar um ambiente virtual para a nossa aplicação Flask `virtualenv` utilizando. A utilização de um ambiente virtual garante que tem um ambiente limpo para trabalhar.

1. No seu diretório de trabalho, gere este comando para criar um ambiente virtual: **macOS/Linux:**
   ```
   virtualenv venv --python=python3
   ```
   Declaramos explicitamente que o ambiente virtual deve usar Python 3. Isto garante que os utilizadores com várias instalações Python estão a utilizar a versão correta.

   **Windows CMD / Windows Bash:**
   ```
   virtualenv venv
   ```
   Para manter as coisas simples, vamos nomear o seu ambiente virtual venv.

2. Os comandos para ativar o seu ambiente virtual variam consoante a sua plataforma/concha:   

   | Plataforma | Shell | Comando |
   |----------|-------|---------|
   | macOS/Linux | bash/zsh | `source venv/bin/activate` |
   | Windows | bash | `source venv/Scripts/activate` |
   | | Linha de Comandos | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   Depois de executar este comando, a sua linha de comando ou sessão terminal deve ser prefaciada com `venv` .

3. Pode desativar a sessão a qualquer momento digitando-a na linha de comando ou terminal: `deactivate` .

> [!NOTE]
> Python tem documentação extensa para criar e gerir ambientes virtuais, ver [virtualenv.](https://virtualenv.pypa.io/en/latest/)

### <a name="install-requests"></a>Instalar pedidos

Os pedidos são um módulo popular que é usado para enviar pedidos HTTP 1.1. Não é necessário adicionar manualmente cordas de consulta aos seus URLs, ou para codificar os seus dados POST.

1. Para instalar pedidos, corra:

   ```
   pip install requests
   ```

> [!NOTE]
> Se quiser saber mais sobre pedidos, consulte [pedidos: HTTP para Humanos](https://2.python-requests.org/en/master/).

### <a name="install-and-configure-flask"></a>Instalar e configurar o balão

A seguir temos de instalar o Flask. O Flask trata do encaminhamento para a nossa aplicação web e permite-nos fazer chamadas de servidor a servidor que ocultam as nossas chaves de subscrição do utilizador final.

1. Para instalar o Flask, corra:
   ```
   pip install Flask
   ```
   Vamos certificar-nos de que o Flask foi instalado. Executar:
   ```
   flask --version
   ```
   A versão deve ser impressa no terminal. Qualquer outra coisa significa que algo correu mal.

2. Para executar a aplicação Flask, pode utilizar o comando do frasco ou o interruptor Python-m com o Flask. Antes de poder fazê-lo, precisa de dizer ao seu terminal com qual app trabalhar, exportando a `FLASK_APP` variável ambiental:

   **macOS/Linux:**
   ```
   export FLASK_APP=app.py
   ```

   **Windows**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>Crie a sua aplicação Flask

Nesta secção, vai criar uma aplicação Barebones Flask que devolve um ficheiro HTML quando os utilizadores atingirem a raiz da sua aplicação. Não perca muito tempo tentando escolher o código, voltaremos para atualizar este ficheiro mais tarde.

### <a name="what-is-a-flask-route"></a>O que é uma rota flask?

Vamos falar sobre "[rotas](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)". O encaminhamento é utilizado para ligar um URL a uma função específica. O frasco utiliza decoradores de rota para registar funções em URLs específicos. Por exemplo, quando um utilizador navega para a raiz `/` () da nossa aplicação web, `index.html` é renderizado.  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

Vamos dar uma olhada em mais um exemplo para martelar esta casa.

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

Este código garante que quando um utilizador navega para `http://your-web-app.com/about` que o ficheiro seja `about.html` prestado.

Embora estas amostras ilustrem como renderizar páginas html para um utilizador, as rotas também podem ser usadas para chamar APIs quando um botão é pressionado, ou tomar qualquer número de ações sem ter que navegar para longe da página inicial. Verá isto em ação quando criar rotas para tradução, sentimento e síntese de fala.

### <a name="get-started"></a>Introdução

1. Abra o projeto no seu IDE e, em seguida, crie um ficheiro nomeado `app.py` na raiz do seu diretório de trabalho. Em seguida, copie este código `app.py` e guarde:

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   Este bloco de códigos diz à aplicação para exibir `index.html` sempre que um utilizador navega até à raiz da sua aplicação web `/` ().

2. Em seguida, vamos criar o front-end para a nossa aplicação web. Crie um ficheiro nomeado `index.html` no `templates` diretório. Em seguida, copie este código em `templates/index.html` .

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

3. Vamos testar a aplicação Flask. A partir do terminal, corra:

   ```
   flask run
   ```

4. Abra um navegador e navegue para o URL fornecido. Devia ver o seu aplicativo de página única. Pressione **ctrl + C** para matar a aplicação.

## <a name="translate-text"></a>Traduzir texto

Agora que tem uma ideia de como funciona uma simples aplicação Flask, vamos:

* Escreva um python para ligar para o Tradutor e devolva uma resposta
* Crie uma rota flask para ligar para o seu código Python
* Atualize o HTML com uma área para entrada e tradução de texto, um seletor de idiomas e botão de tradução
* Escreva JavaScript que permite que os utilizadores interajam com a sua aplicação Flask a partir do HTML

### <a name="call-the-translator"></a>Ligue para o Tradutor

A primeira coisa a fazer é escrever uma função para chamar o Tradutor. Esta função terá dois argumentos: `text_input` e `language_output` . Esta função é chamada sempre que um utilizador carrega no botão de tradução na sua aplicação. A área de texto no HTML é enviada como `text_input` , e o valor de seleção de idiomas no HTML é enviado como `language_output` .

1. Vamos começar por criar um ficheiro chamado `translate.py` na raiz do seu diretório de trabalho.
2. Em seguida, adicione este código a `translate.py` . Esta função requer dois argumentos: `text_input` e `language_output` .
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
   location = 'YOUR_TRANSLATOR_RESOURCE_LOCATION'
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
           'Ocp-Apim-Subscription-Region': location,
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
3. Adicione a sua chave de subscrição de Tradutor e guarde.

### <a name="add-a-route-to-apppy"></a>Adicione uma rota para `app.py`

Em seguida, terá de criar uma rota na sua aplicação Flask que `translate.py` ligue. Esta rota será chamada sempre que um utilizador premir o botão de tradução na sua aplicação.

Para esta aplicação, a sua rota vai aceitar `POST` pedidos. Isto porque a função espera que o texto traduza e uma linguagem de saída para a tradução.

O frasco fornece funções de ajudante para ajudá-lo a analisar e gerir cada pedido. No código fornecido, `get_json()` devolve os dados do `POST` pedido como JSON. Em `data['text']` seguida, utilizando `data['to']` e, os valores da linguagem de texto e de saída são passados para `get_translation()` a função disponível a partir de `translate.py` . O último passo é devolver a resposta como JSON, uma vez que terá de apresentar estes dados na sua aplicação web.

Nas secções seguintes, irá repetir este processo à medida que cria rotas para análise de sentimentos e síntese de fala.

1. Abra `app.py` e localize a declaração de importação no topo `app.py` e adicione a seguinte linha:

   ```python
   import translate
   ```
   Agora a nossa aplicação Flask pode usar o método disponível via `translate.py` .

2. Copie este código para o fim `app.py` e guarde:

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

Agora que tem uma função para traduzir texto, e uma rota na sua app Flask para chamá-lo, o próximo passo é começar a construir o HTML para a sua aplicação. O HTML abaixo faz algumas coisas:

* Fornece uma área de texto onde os utilizadores podem inserir texto para traduzir.
* Inclui um seletor de idiomas.
* Inclui elementos HTML para tornar as notas de idioma e confiança detetadas devolvidas durante a tradução.
* Fornece uma área de texto apenas de leitura onde a saída de tradução é exibida.
* Inclui espaços reservados para análise de sentimentos e código de síntese de fala que irá adicionar a este ficheiro mais tarde no tutorial.

Vamos `index.html` atualizar.

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

O próximo passo é escrever javaScript. Esta é a ponte entre a sua rota HTML e Flask.

### <a name="create-mainjs"></a>Criar `main.js`  

O `main.js` ficheiro é a ponte entre a sua rota HTML e Flask. A sua aplicação utilizará uma combinação de jQuery, Ajax e XMLHttpRequest para renderizar conteúdo e fazer `POST` pedidos para as suas rotas Flask.

No código abaixo, o conteúdo do HTML é utilizado para construir um pedido para a sua rota Flask. Especificamente, o conteúdo da área de texto e do seletor de idiomas são atribuídos a variáveis, e depois transmitidos no pedido para `translate-text` .

O código imita então através da resposta, e atualiza o HTML com a tradução, linguagem detetada e pontuação de confiança.

1. A partir do seu IDE, crie um ficheiro nomeado `main.js` no `static/scripts` diretório.
2. Copie este código `static/scripts/main.js` em:
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

### <a name="test-translation"></a>Tradução de teste

Vamos testar a tradução na aplicação.

```
flask run
```

Navegue para o endereço do servidor fornecido. Digite texto na área de entrada, selecione um idioma e prima a tradução. Devia conseguir uma tradução. Se não funcionar, certifique-se de que adicionou a sua chave de subscrição.

> [!TIP]
> Se as alterações que fez não aparecerem, ou se a aplicação não funcionar como espera, tente limpar o cache ou abrir uma janela privada/incógnita.

Pressione **CTRL + c** para matar a aplicação e, em seguida, dirija-se à secção seguinte.

## <a name="analyze-sentiment"></a>Analisar sentimento

A [API de Análise de Texto](../text-analytics/overview.md) pode ser usada para realizar análises de sentimentos, extrair frases-chave do texto ou detetar a linguagem de origem. Nesta aplicação, vamos usar a análise de sentimento para determinar se o texto fornecido é positivo, neutro ou negativo. A API devolve uma pontuação numérica entre 0 e 1. Pontuações próximas de 1 indicam sentimento positivo, e pontuações perto de 0 indicam sentimento negativo.

Nesta secção, vais fazer algumas coisas:

* Escreva alguns Python para ligar para a API de Análise de Texto para realizar análise de sentimento e devolver uma resposta
* Crie uma rota flask para ligar para o seu código Python
* Atualize o HTML com uma área para pontuações de sentimento, e um botão para realizar análises
* Escreva JavaScript que permite que os utilizadores interajam com a sua aplicação Flask a partir do HTML

### <a name="call-the-text-analytics-api"></a>Chamar a API de Análise de Texto

Vamos escrever uma função para chamar a API de Análise de Texto. Esta função terá quatro argumentos: `input_text` `input_language` , , e `output_text` `output_language` . Esta função é chamada sempre que um utilizador carrega no botão de análise de sentimento de execução na sua aplicação. Os dados fornecidos pelo utilizador a partir da área de texto e do seletor de idiomas, bem como a saída de idioma e tradução detetadas são fornecidos com cada pedido. O objeto de resposta inclui pontuações de sentimento para a origem e tradução. Nas secções seguintes, vai escrever alguns JavaScript para analisar a resposta e usá-la na sua aplicação. Por enquanto, concentremo-nos em chamar a API de Análise de Texto.

1. Vamos criar um ficheiro chamado `sentiment.py` na raiz do seu diretório de trabalho.
2. Em seguida, adicione este código a `sentiment.py` .
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   subscription_key = 'YOUR_TEXT_ANALYTICS_SUBSCRIPTION_KEY'
   endpoint = "YOUR_TEXT_ANALYTICS_ENDPOINT" 
   # Our Flask route will supply four arguments: input_text, input_language,
   # output_text, output_language.
   # When the run sentiment analysis button is pressed in our Flask app,
   # the Ajax request will grab these values from our web app, and use them
   # in the request. See main.js for Ajax calls.

   def get_sentiment(input_text, input_language):
       path = '/text/analytics/v3.0/sentiment'
       constructed_url = endpoint + path

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
           ]
       }
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Adicione a sua chave de subscrição text Analytics e guarde.

### <a name="add-a-route-to-apppy"></a>Adicione uma rota para `app.py`

Vamos criar uma rota na sua aplicação Flask que `sentiment.py` liga. Esta rota será chamada sempre que um utilizador premir o botão de análise de sentimento de execução na sua aplicação. Tal como a rota para a tradução, esta rota vai aceitar `POST` pedidos, uma vez que a função espera argumentos.

1. Abra `app.py` e localize a declaração de importação no topo `app.py` e atualize-a:

   ```python
   import translate, sentiment
   ```
   Agora a nossa aplicação Flask pode usar o método disponível via `sentiment.py` .

2. Copie este código para o fim `app.py` e guarde:
   ```python
   @app.route('/sentiment-analysis', methods=['POST'])
   def sentiment_analysis():
       data = request.get_json()
       input_text = data['inputText']
       input_lang = data['inputLanguage']
       response = sentiment.get_sentiment(input_text, input_lang)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>Atualizar `index.html`

Agora que tem uma função para executar a análise de sentimento, e uma rota na sua app Flask para chamá-lo, o próximo passo é começar a escrever o HTML para a sua aplicação. O HTML abaixo faz algumas coisas:

* Adiciona um botão à sua app para executar a análise de sentimento
* Adiciona um elemento que explica a pontuação do sentimento
* Adiciona um elemento para mostrar as pontuações de sentimento

1. Abra `index.html` e localize estes comentários de código:
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. Substitua os comentários de código por este bloco HTML:
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment can be labeled as "positive", "negative", "neutral", or "mixed". </p>
      <strong>Sentiment label for input:</strong> <span id="input-sentiment"></span><br />
   </div>
   ```

### <a name="update-mainjs"></a>Atualizar `main.js`

No código abaixo, o conteúdo do HTML é utilizado para construir um pedido para a sua rota Flask. Especificamente, o conteúdo da área de texto e do seletor de idiomas são atribuídos a variáveis, e depois transmitidos no pedido para a `sentiment-analysis` rota.

O código em seguida itera através da resposta, e atualiza o HTML com as pontuações de sentimento.

1. A partir do seu IDE, crie um ficheiro nomeado `main.js` no `static` diretório.

2. Copie este código `static/scripts/main.js` em:
   ```javascript
   //Run sentiment analysis on input and translation.
   $("#sentiment-analysis").on("click", function(e) {
     e.preventDefault();
     var inputText = document.getElementById("text-to-translate").value;
     var inputLanguage = document.getElementById("detected-language-result").innerHTML;
     var outputText = document.getElementById("translation-result").value;
     var outputLanguage = document.getElementById("select-language").value;

     var sentimentRequest = { "inputText": inputText, "inputLanguage": inputLanguage};

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
                 document.getElementById("input-sentiment").textContent = data.documents[i].sentiment;
               }
             }
           }
           for (var i = 0; i < data.errors.length; i++) {
             if (typeof data.errors[i] !== "undefined"){
               if (data.errors[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.errors[i].message;
               }
             }
           }
           if (document.getElementById("input-sentiment").textContent !== ''){
             document.getElementById("sentiment").style.display = "block";
           }
         }
       });
     }
   });
   // In the next section, you'll add code for speech synthesis here.
   ```

### <a name="test-sentiment-analysis"></a>Análise de sentimento de teste

Vamos testar a análise de sentimento na aplicação.

```
flask run
```

Navegue para o endereço do servidor fornecido. Digite texto na área de entrada, selecione um idioma e prima a tradução. Devia conseguir uma tradução. Em seguida, pressione o botão de análise de sentimento de execução. Devia ver duas notas. Se não funcionar, certifique-se de que adicionou a sua chave de subscrição.

> [!TIP]
> Se as alterações que fez não aparecerem, ou se a aplicação não funcionar como espera, tente limpar o cache ou abrir uma janela privada/incógnita.

Pressione **CTRL + c** para matar a aplicação e, em seguida, dirija-se à secção seguinte.

## <a name="convert-text-to-speech"></a>Converter texto em voz

A [API text-to-speech](../speech-service/text-to-speech.md) permite que a sua app converta o texto em linguagem humana natural. O serviço suporta vozes padrão, neurais e personalizadas. A nossa aplicação de amostras utiliza um punhado de vozes disponíveis, para uma lista completa, ver [idiomas suportados.](../speech-service/language-support.md#text-to-speech)

Nesta secção, vais fazer algumas coisas:

* Escreva um python para converter texto-a-discurso com a API text-to-speech
* Crie uma rota flask para ligar para o seu código Python
* Atualize o HTML com um botão para converter texto-a-discurso, e um elemento para reprodução áudio
* Escreva JavaScript que permite aos utilizadores interagir com a sua aplicação Flask

### <a name="call-the-text-to-speech-api"></a>Ligue para a API text-to-speech

Vamos escrever uma função para converter texto-a-discurso. Esta função terá dois argumentos: `input_text` e `voice_font` . Esta função é chamada sempre que um utilizador pressiona o botão de conversão texto-a-fala na sua aplicação. `input_text` é a saída de tradução devolvida pela chamada para traduzir texto, `voice_font` é o valor do seletor de fonte de voz no HTML.

1. Vamos criar um ficheiro chamado `synthesize.py` na raiz do seu diretório de trabalho.

2. Em seguida, adicione este código a `synthesize.py` .
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
3. Adicione a sua chave de subscrição de Serviços de Fala e guarde.

### <a name="add-a-route-to-apppy"></a>Adicione uma rota para `app.py`

Vamos criar uma rota na sua aplicação Flask que `synthesize.py` liga. Esta rota será chamada sempre que um utilizador premir o botão de conversão texto-a-fala na sua aplicação. Tal como as rotas para a tradução e análise de sentimentos, esta rota vai aceitar `POST` pedidos, uma vez que a função espera dois argumentos: o texto para sintetizar, e a fonte de voz para reprodução.

1. Abra `app.py` e localize a declaração de importação no topo `app.py` e atualize-a:

   ```python
   import translate, sentiment, synthesize
   ```
   Agora a nossa aplicação Flask pode usar o método disponível via `synthesize.py` .

2. Copie este código para o fim `app.py` e guarde:

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

Agora que tem uma função para converter texto-a-fala, e uma rota na sua app Flask para chamá-lo, o próximo passo é começar a escrever o HTML para a sua aplicação. O HTML abaixo faz algumas coisas:

* Proporciona uma queda de seleção de voz
* Adiciona um botão para converter texto-a-discurso
* Adiciona um elemento áudio, que é usado para reproduzir o discurso sintetizado

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
       <option value="(zh-TW, Yating, Apollo)">Chinese (Taiwan)| Female | Yating, Apollo</option>
       <option value="(zh-TW, Zhiwei, Apollo)">Chinese (Taiwan) | Male | Zhiwei, Apollo</option>
       <option value="(hr-HR, Matej)">Croatian | Male | Matej</option>
       <option value="(en-US, AriaRUS)">English (US) | Female | AriaRUS</option>
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
       <option value="(ko-KR, HeamiRUS)">Korean | Female | Heami</option>
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

5. Certifique-se de salvar o seu trabalho.

### <a name="update-mainjs"></a>Atualizar `main.js`

No código abaixo, o conteúdo do HTML é utilizado para construir um pedido para a sua rota Flask. Especificamente, a tradução e o tipo de voz são atribuídos a variáveis, e depois transmitidos no pedido para a `text-to-speech` rota.

O código em seguida itera através da resposta, e atualiza o HTML com as pontuações de sentimento.

1. A partir do seu IDE, crie um ficheiro nomeado `main.js` no `static` diretório.
2. Copie este código `static/scripts/main.js` em:
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
3. Está quase concluído. A última coisa que vai fazer é adicionar algum código `main.js` para selecionar automaticamente um tipo de letra de voz baseado no idioma selecionado para tradução. Adicione este bloco de código `main.js` para:
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

Vamos testar a síntese da fala na aplicação.

```
flask run
```

Navegue para o endereço do servidor fornecido. Digite texto na área de entrada, selecione um idioma e prima a tradução. Devia conseguir uma tradução. Em seguida, selecione uma voz e, em seguida, pressione o botão de conversão texto-a-voz. a tradução deve ser reproduzida como discurso sintetizado. Se não funcionar, certifique-se de que adicionou a sua chave de subscrição.

> [!TIP]
> Se as alterações que fez não aparecerem, ou se a aplicação não funcionar como espera, tente limpar o cache ou abrir uma janela privada/incógnita.

É isso, tens uma aplicação de trabalho que executa traduções, analisa sentimentos e discurso sintetizado. Pressione **CTRL + c** para matar a aplicação. Certifique-se de verificar os outros [Serviços Cognitivos Azure.](../index.yml)

## <a name="get-the-source-code"></a>Obter o código-fonte

O código-fonte deste projeto está disponível no [GitHub.](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial)

## <a name="next-steps"></a>Passos seguintes

* [Referência do tradutor](./reference/v3-0-reference.md)
* [Text Analytics API reference](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) (Referência à API de Análise de Texto)
* [Referência da API de conversão de texto em voz](../speech-service/rest-text-to-speech.md)
