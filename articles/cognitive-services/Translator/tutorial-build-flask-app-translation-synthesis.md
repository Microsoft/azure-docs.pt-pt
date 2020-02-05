---
title: 'Tutorial: Construa uma aplicação de Frasco para traduzir, sintetizar e analisar texto - Tradutor Text API'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você vai construir uma aplicação web baseada em Flask para traduzir texto, analisar sentimentos e sintetizar texto traduzido em discurso.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 12/09/2019
ms.author: swmachan
ms.openlocfilehash: 0075862e198ce67cc7367efe94d624ad18e6eb3b
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984172"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>Tutorial: Construir uma app de frasco com serviços cognitivos Azure

Neste tutorial, você vai construir uma aplicação web Do Flask que usa serviços cognitivos Azure para traduzir texto, analisar o sentimento e sintetizar texto traduzido em fala. O nosso foco está nas rotas do código Python e do Flask que permitem a nossa aplicação, no entanto, vamos ajudá-lo com o HTML e javascript que reúne a app. Se tiver algum problema, avise-nos através do botão de feedback abaixo.

Eis o que este tutorial aborda:

> [!div class="checklist"]
> * Obtenha chaves de subscrição Azure
> * Instale o seu ambiente de desenvolvimento e instale dependências
> * Criar uma aplicação De Balão
> * Utilize a API de texto tradutor para traduzir texto
> * Utilize o Text Analytics para analisar o sentimento positivo/negativo do texto e traduções de entrada
> * Use os Serviços de Fala para converter texto traduzido em discurso sintetizado
> * Executar a sua aplicação Flask localmente

> [!TIP]
> Se quiser saltar à frente e ver todo o código de uma vez, toda a amostra, juntamente com instruções de construção, estão disponíveis no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="what-is-flask"></a>O que é O Flask?

O balão é um microquadro para a criação de aplicações web. Isto significa que o Flask fornece-lhe ferramentas, bibliotecas e tecnologias que lhe permitem construir uma aplicação web. Esta aplicação web pode ser algumas páginas web, um blog, um wiki ou ir tão substantivo como uma aplicação de calendário baseada na web ou um site comercial.

Para aqueles que querem mergulhar profundamente depois deste tutorial aqui são alguns links úteis:

* [Documentação do balão](http://flask.pocoo.org/)
* [Balão para Bonecos - Um Guia de Principiantepara O Balão](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>Pré-requisitos

Vamos rever o software e as chaves de subscrição que você precisará para este tutorial.

* [Python 3.5.2 ou mais tarde](https://www.python.org/downloads/)
* [Ferramentas Git](https://git-scm.com/downloads)
* Um IDE ou editor de texto, como [Visual Studio Code](https://code.visualstudio.com/) ou [Atom](https://atom.io/)  
* [Chrome](https://www.google.com/chrome/browser/) ou [Firefox](https://www.mozilla.org/firefox)
* Uma chave de subscrição de **texto tradutor** (note que não é necessário selecionar uma região.)
* Uma chave de subscrição de **Text Analytics** na região dos **EUA Ocidentais.**
* Uma chave de subscrição dos **Serviços de Fala** na região **dos EUA Ocidentais.**

## <a name="create-an-account-and-subscribe-to-resources"></a>Criar uma conta e subscrever recursos

Como mencionado anteriormente, vai precisar de três chaves de subscrição para este tutorial. Isto significa que precisa de criar um recurso dentro da sua conta Azure para:
* Texto do Tradutor
* Análise de Texto
* Serviços de Voz

Utilizar Criar uma Conta de [Serviços Cognitivos no portal Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para instruções passo a passo para criar recursos.

> [!IMPORTANT]
> Para este tutorial, por favor crie os seus recursos na região dos EUA Ocidentais. Se utilizar uma região diferente, terá de ajustar o URL base em cada um dos seus ficheiros Python.

## <a name="set-up-your-dev-environment"></a>Configurar o ambiente de desenvolvimento

Antes de construir a sua aplicação web Do Flask, terá de criar um diretório de trabalho para o seu projeto e instalar alguns pacotes Python.

### <a name="create-a-working-directory"></a>Criar um diretório de trabalho

1. Linha de comando aberta (Windows) ou terminal (macOS/Linux). Em seguida, crie um diretório de trabalho e subdirectios para o seu projeto:  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. Mude para o diretório de trabalho do seu projeto:  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>Crie e ative o seu ambiente virtual com `virtualenv`

Vamos criar um ambiente virtual para a nossa aplicação Flask usando `virtualenv`. A utilização de um ambiente virtual garante que tem um ambiente limpo para trabalhar.

1. No seu diretório de trabalho, execute este comando para criar um ambiente virtual: **macOS/Linux:**
   ```
   virtualenv venv --python=python3
   ```
   Declarámos explicitamente que o ambiente virtual devia usar python 3. Isto garante que os utilizadores com múltiplas instalações python estão a utilizar a versão correta.

   **Windows CMD / Windows Bash:**
   ```
   virtualenv venv
   ```
   Para manter as coisas simples, estamos a dar o nome ao teu ambiente virtual.

2. Os comandos para ativar o seu ambiente virtual variarão dependendo da sua plataforma/concha:   

   | Plataforma | Shell | Comando |
   |----------|-------|---------|
   | macOS/Linux | bash/zsh | `source venv/bin/activate` |
   | Windows | bash | `source venv/Scripts/activate` |
   | | Linha de Comandos | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   Depois de executar este comando, a sua linha de comando ou sessão terminal deve ser pré-encarada com `venv`.

3. Pode desativar a sessão a qualquer momento digitando-a na linha de comando ou terminal: `deactivate`.

> [!NOTE]
> Python tem documentação extensa para criar e gerir ambientes virtuais, ver [virtualenv](https://virtualenv.pypa.io/en/latest/).

### <a name="install-requests"></a>Instalar pedidos

Pedidos é um módulo popular que é usado para enviar pedidos HTTP 1.1. Não há necessidade de adicionar manualmente cordas de consulta aos seus URLs, ou de codificar os seus dados POST.

1. Para instalar pedidos, executar:

   ```
   pip install requests
   ```

> [!NOTE]
> Se quiser saber mais sobre pedidos, consulte [Pedidos: HTTP para Humanos](https://2.python-requests.org/en/master/).

### <a name="install-and-configure-flask"></a>Instalar e configurar o Balão

Em seguida, precisamos instalar o Flask. O Flask trata do encaminhamento para a nossa aplicação web e permite-nos fazer chamadas de servidor-a-servidor que escondem as nossas chaves de subscrição do utilizador final.

1. Para instalar o Flask, corra:
   ```
   pip install Flask
   ```
   Vamos certificar-nos de que o Flask foi instalado. Execução:
   ```
   flask --version
   ```
   A versão deve ser impressa em terminal. Qualquer outra coisa significa que algo correu mal.

2. Para executar a aplicação Flask, pode utilizar o comando do balão ou o interruptor Python-m com o Flask. Antes de poder fazê-lo, tem de dizer ao seu terminal com que aplicação trabalhar exportando a variável ambiente `FLASK_APP`:

   **macOS/Linux**:
   ```
   export FLASK_APP=app.py
   ```

   **Windows**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>Crie a sua aplicação Flask

Nesta secção, vai criar uma aplicação Desfiada de Flask que devolve um ficheiro HTML quando os utilizadores atingirem a raiz da sua aplicação. Não gastes muito tempo a tentar escolher o código, voltamos para atualizar este ficheiro mais tarde.

### <a name="what-is-a-flask-route"></a>O que é uma rota do Flask?

Vamos tirar um minuto para falar sobre[rotas](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)". O encaminhamento é usado para ligar um URL a uma função específica. O Frasco utiliza decoradores de rotas para registar funções em URLs específicos. Por exemplo, quando um utilizador navega para a raiz (`/`) da nossa aplicação web, `index.html` é renderizado.  

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

Este código garante que quando um utilizador navega para `http://your-web-app.com/about` que o ficheiro `about.html` é prestado.

Enquanto estas amostras ilustram como renderizar páginas html para um utilizador, as rotas também podem ser usadas para ligar para APIs quando um botão é premido, ou tomar qualquer número de ações sem ter que navegar para longe da página inicial. Verá isto em ação quando criar rotas para a tradução, sentimento e síntese da fala.

### <a name="get-started"></a>Começar

1. Abra o projeto no seu IDE e, em seguida, crie um ficheiro chamado `app.py` na raiz do seu diretório de trabalho. Em seguida, copie este código para `app.py` e guarde:

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   Este bloco de códigos diz à aplicação para apresentar `index.html` sempre que um utilizador navega para a raiz da sua aplicação web (`/`).

2. Em seguida, vamos criar o front-end para a nossa aplicação web. Crie um ficheiro chamado `index.html` no diretório `templates`. Em seguida, copie este código para `templates/index.html`.

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

3. Vamos testar a aplicação do Flask. A partir do terminal, corra:

   ```
   flask run
   ```

4. Abra um navegador e navegue para o URL fornecido. Devia ver a sua aplicação de página única. Prima **Ctrl + c** para matar a aplicação.

## <a name="translate-text"></a>Traduzir texto

Agora que tem uma ideia de como funciona uma simples aplicação do Flask, vamos:

* Escreva um pouco de Python para ligar para a API de Texto tradutor e devolver uma resposta
* Crie uma rota de Balão para ligar para o seu código Python
* Atualizar o HTML com uma área para entrada e tradução de texto, um seletor de idiomas e botão de tradução
* Escreva Javascript que permite que os utilizadores interajam com a sua aplicação Flask a partir do HTML

### <a name="call-the-translator-text-api"></a>Ligue para a API de Texto tradutor

A primeira coisa que precisa fazer é escrever uma função para chamar a API de Texto tradutor. Esta função terá dois argumentos: `text_input` e `language_output`. Esta função é chamada sempre que um utilizador pressiona o botão de tradução na sua aplicação. A área de texto no HTML é enviada como a `text_input`, e o valor de seleção de idiomas no HTML é enviado como `language_output`.

1. Vamos começar por criar um ficheiro chamado `translate.py` na raiz do teu diretório de trabalho.
2. Em seguida, adicione este código para `translate.py`. Esta função requer dois argumentos: `text_input` e `language_output`.
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
3. Adicione a sua chave de subscrição de Texto tradutor e poupe.

### <a name="add-a-route-to-apppy"></a>Adicione uma rota para `app.py`

Em seguida, terá de criar uma rota na sua aplicação Do Flask que chama `translate.py`. Esta rota será chamada sempre que um utilizador premir o botão de tradução na sua aplicação.

Para esta aplicação, a sua rota vai aceitar `POST` pedidos. Isto porque a função espera que o texto se traduza e uma linguagem de saída para a tradução.

O Flask fornece funções de ajudante para ajudá-lo a analisar e gerir cada pedido. No código fornecido, `get_json()` devolve os dados do pedido de `POST` como JSON. Em seguida, utilizando `data['text']` e `data['to']`, os valores da linguagem de texto e de saída são passados para `get_translation()` função disponível a partir de `translate.py`. O último passo é devolver a resposta como JSON, uma vez que terá de apresentar estes dados na sua aplicação web.

Nas seguintes secções, irá repetir este processo à medida que cria rotas para análise de sentimentos e síntese de fala.

1. Abra `app.py` e localize a declaração de importação no topo da `app.py` e adicione a seguinte linha:

   ```python
   import translate
   ```
   Agora a nossa aplicação Flask pode usar o método disponível através de `translate.py`.

2. Copie este código até ao final do `app.py` e guarde:

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

Agora que tem uma função para traduzir texto, e uma rota na sua aplicação Do Flask para chamá-lo, o próximo passo é começar a construir o HTML para a sua aplicação. O HTML abaixo faz algumas coisas:

* Fornece uma área de texto onde os utilizadores podem inserir texto para traduzir.
* Inclui um seletor de idiomas.
* Inclui elementos HTML para tornar as pontuações de linguagem e confiança detetadas devolvidas durante a tradução.
* Fornece uma área de texto apenas para leitura onde a saída de tradução é exibida.
* Inclui espaços reservados para análise de sentimentos e código de síntese de fala que irá adicionar a este ficheiro mais tarde no tutorial.

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

O próximo passo é escrever um javascript. Esta é a ponte entre a sua rota HTML e Flask.

### <a name="create-mainjs"></a>Criar `main.js`  

O ficheiro `main.js` é a ponte entre a sua rota HTML e Flask. A sua aplicação utilizará uma combinação de jQuery, Ajax e XMLHttpRequest para renderizar conteúdos e fazer `POST` pedidos às suas rotas do Flask.

No código abaixo, o conteúdo do HTML é utilizado para construir um pedido para a sua rota Do Balão. Especificamente, o conteúdo da área de texto e do seletor de idiomas são atribuídos a variáveis, e depois transmitidos no pedido de `translate-text`.

O código então iterates através da resposta, e atualiza o HTML com a tradução, linguagem detetada e pontuação de confiança.

1. A partir do seu IDE, crie um ficheiro chamado `main.js` no diretório `static/scripts`.
2. Copie este código para `static/scripts/main.js`:
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

Navegue para o endereço do servidor fornecido. Digite texto na área de entrada, selecione um idioma e prima traduzir. Devia sacá-lo. Se não funcionar, certifique-se de que adicionou a sua chave de subscrição.

> [!TIP]
> Se as alterações que fez não aparecerem, ou a aplicação não funcionar como espera, tente limpar a cache ou abrir uma janela privada/incógnita.

Prima **CTRL + c** para matar a aplicação e, em seguida, dirija-se à secção seguinte.

## <a name="analyze-sentiment"></a>Analisar sentimento

A [API](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) text analytics pode ser usada para realizar análises de sentimentos, extrair frases-chave do texto ou detetar a linguagem de origem. Nesta aplicação, vamos usar a análise de sentimento para determinar se o texto fornecido é positivo, neutro ou negativo. A API devolve uma pontuação numérica entre 0 e 1. As pontuações próximas de 1 indicam um sentimento positivo, enquanto as pontuações próximas de 0 indicam um sentimento negativo.

Nesta secção, vais fazer algumas coisas:

* Escreva um pouco de Python para ligar para a API de Análise de Texto para realizar análise de sentimentos e devolver uma resposta
* Crie uma rota de Balão para ligar para o seu código Python
* Atualize o HTML com uma área para pontuações de sentimento, e um botão para realizar análises
* Escreva Javascript que permite que os utilizadores interajam com a sua aplicação Flask a partir do HTML

### <a name="call-the-text-analytics-api"></a>Chamar a API de Análise de Texto

Vamos escrever uma função para chamar a API de Análise de Texto. Esta função terá quatro argumentos: `input_text`, `input_language`, `output_text`e `output_language`. Esta função é chamada sempre que um utilizador pressiona o botão de análise de sentimento sinuoso na sua aplicação. Os dados fornecidos pelo utilizador a partir da área de texto e do seletor de idiomas, bem como o idioma e a saída de tradução detetados são fornecidos com cada pedido. O objeto de resposta inclui pontuações de sentimento para a fonte e tradução. Nas seguintes secções, vai escrever um pouco de Javascript para analisar a resposta e usá-la na sua aplicação. Por enquanto, concentremo-nos em ligar para a API de Análise de Texto.

1. Vamos criar um ficheiro chamado `sentiment.py` na raiz do teu diretório de trabalho.
2. Em seguida, adicione este código para `sentiment.py`.
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
3. Adicione a sua chave de subscrição de Texto Analytics e poupe.

### <a name="add-a-route-to-apppy"></a>Adicione uma rota para `app.py`

Vamos criar uma rota na sua aplicação Do Flask que chama `sentiment.py`. Esta rota será chamada cada vez que um utilizador pressiona o botão de análise de sentimento sonuoso na sua aplicação. Tal como a rota de tradução, esta rota vai aceitar `POST` pedidos, uma vez que a função espera argumentos.

1. Abra `app.py` e localize a declaração de importação no topo da `app.py` e atualize-a:

   ```python
   import translate, sentiment
   ```
   Agora a nossa aplicação Flask pode usar o método disponível através de `sentiment.py`.

2. Copie este código até ao final do `app.py` e guarde:
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

Agora que tem uma função para executar a análise de sentimentos, e uma rota na sua aplicação Do Flask para chamá-lo, o próximo passo é começar a escrever o HTML para a sua aplicação. O HTML abaixo faz algumas coisas:

* Adiciona um botão à sua app para executar análise de sentimento
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
      <p>Sentiment scores are provided on a 1 point scale. The closer the sentiment score is to 1, indicates positive sentiment. The closer it is to 0, indicates negative sentiment.</p>
      <strong>Sentiment score for input:</strong> <span id="input-sentiment"></span><br />
      <strong>Sentiment score for translation:</strong> <span id="translation-sentiment"></span>
   </div>
   ```

### <a name="update-mainjs"></a>Atualizar `main.js`

No código abaixo, o conteúdo do HTML é utilizado para construir um pedido para a sua rota Do Balão. Especificamente, o conteúdo da área de texto e do seletor de idiomas são atribuídos a variáveis, e depois passados no pedido para a rota `sentiment-analysis`.

O código então iterates através da resposta, e atualiza o HTML com as pontuações de sentimento.

1. A partir do seu IDE, crie um ficheiro chamado `main.js` no diretório `static`.

2. Copie este código para `static/scripts/main.js`:
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

### <a name="test-sentiment-analysis"></a>Análise de sentimento de teste

Vamos testar a análise de sentimentos na aplicação.

```
flask run
```

Navegue para o endereço do servidor fornecido. Digite texto na área de entrada, selecione um idioma e prima traduzir. Devia sacá-lo. Em seguida, pressione o botão de análise de sentimento sonorizador. Devia ver duas pontuações. Se não funcionar, certifique-se de que adicionou a sua chave de subscrição.

> [!TIP]
> Se as alterações que fez não aparecerem, ou a aplicação não funcionar como espera, tente limpar a cache ou abrir uma janela privada/incógnita.

Prima **CTRL + c** para matar a aplicação e, em seguida, dirija-se à secção seguinte.

## <a name="convert-text-to-speech"></a>Converter texto em voz

A [API text-to-speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) permite que a sua aplicação converta texto em discurso sintetizado humano natural. O serviço suporta vozes padrão, neurais e personalizadas. A nossa aplicação de amostras utiliza um punhado de vozes disponíveis, para uma lista completa, ver [idiomas suportados.](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)

Nesta secção, vais fazer algumas coisas:

* Escreva um pouco de Python para converter texto-a-discurso com a API texto-a-fala
* Crie uma rota de Balão para ligar para o seu código Python
* Atualize o HTML com um botão para converter texto-a-fala, e um elemento para reprodução áudio
* Escreva Javascript que permite que os utilizadores interajam com a sua aplicação Flask

### <a name="call-the-text-to-speech-api"></a>Ligue para a API texto-a-fala

Vamos escrever uma função para converter texto-a-fala. Esta função terá dois argumentos: `input_text` e `voice_font`. Esta função é chamada sempre que um utilizador pressiona o botão de conversão de texto a fala na sua aplicação. `input_text` é a saída de tradução devolvida pela chamada para traduzir texto, `voice_font` é o valor do seletor de fontes de voz no HTML.

1. Vamos criar um ficheiro chamado `synthesize.py` na raiz do teu diretório de trabalho.

2. Em seguida, adicione este código para `synthesize.py`.
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
3. Adicione a sua chave de subscrição do Serviço de Fala e poupe.

### <a name="add-a-route-to-apppy"></a>Adicione uma rota para `app.py`

Vamos criar uma rota na sua aplicação Do Flask que chama `synthesize.py`. Esta rota será chamada sempre que um utilizador premir o botão de conversão de texto-a-fala na sua aplicação. Tal como as rotas para a tradução e análise de sentimentos, esta rota vai aceitar `POST` pedidos, uma vez que a função espera dois argumentos: o texto para sintetizar, e a fonte de voz para reprodução.

1. Abra `app.py` e localize a declaração de importação no topo da `app.py` e atualize-a:

   ```python
   import translate, sentiment, synthesize
   ```
   Agora a nossa aplicação Flask pode usar o método disponível através de `synthesize.py`.

2. Copie este código até ao final do `app.py` e guarde:

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

Agora que tem uma função de converter texto-a-fala, e uma rota na sua aplicação Do Flask para chamá-lo, o próximo passo é começar a escrever o HTML para a sua aplicação. O HTML abaixo faz algumas coisas:

* Proporciona uma queda de seleção de voz
* Adiciona um botão para converter texto-a-fala
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

5. Certifique-se de salvar o seu trabalho.

### <a name="update-mainjs"></a>Atualizar `main.js`

No código abaixo, o conteúdo do HTML é utilizado para construir um pedido para a sua rota Do Balão. Especificamente, a tradução e a fonte de voz são atribuídas a variáveis, e depois transmitidas no pedido para a rota `text-to-speech`.

O código então iterates através da resposta, e atualiza o HTML com as pontuações de sentimento.

1. A partir do seu IDE, crie um ficheiro chamado `main.js` no diretório `static`.
2. Copie este código para `static/scripts/main.js`:
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
3. Está quase concluído. A última coisa que vai fazer é adicionar algum código a `main.js` para selecionar automaticamente um tipo de letra de voz com base no idioma selecionado para tradução. Adicione este bloco de código para `main.js`:
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

Navegue para o endereço do servidor fornecido. Digite texto na área de entrada, selecione um idioma e prima traduzir. Devia sacá-lo. Em seguida, selecione uma voz e, em seguida, pressione o botão de conversão texto-a-fala. a tradução deve ser reproduzida como discurso sintetizado. Se não funcionar, certifique-se de que adicionou a sua chave de subscrição.

> [!TIP]
> Se as alterações que fez não aparecerem, ou a aplicação não funcionar como espera, tente limpar a cache ou abrir uma janela privada/incógnita.

É isso, tens uma aplicação de trabalho que executa traduções, analisa sentimentos e fala sintetizada. Prima **CTRL + c** para matar a aplicação. Certifique-se de verificar os outros [Serviços Cognitivos Azure.](https://docs.microsoft.com/azure/cognitive-services/)

## <a name="get-the-source-code"></a>Obtenha o código fonte

O código fonte para este projeto está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="next-steps"></a>Passos seguintes

* [Referência da API de Texto do Microsoft Translator](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
* [Text Analytics API reference](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) (Referência à API de Análise de Texto)
* [Referência da API de conversão de texto em voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)
