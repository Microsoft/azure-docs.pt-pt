---
title: 'Tutorial: Criar uma aplicação Flask para traduzir, sintetizar e analisar texto - API de texto do tradutor'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, irá criar uma aplicação Flask com base na web que utiliza serviços cognitivos do Azure para traduzir texto, analisar sentimentos e sintetizar o texto traduzido em voz. Nosso foco é o código de Python e rotas de Flask que permitem a nossa aplicação. Nós não gastar muito tempo em Javascript que controla a aplicação, mas fornece todos os ficheiros para que possa inspecionar.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: 4df2de7f0428ffe35712a29bfef645b6feb66813
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515012"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>Tutorial: Criar uma aplicação Flask com os serviços cognitivos do Azure

Neste tutorial, irá criar uma aplicação web Flask que utiliza serviços cognitivos do Azure para traduzir texto, analisar sentimentos e sintetizar o texto traduzido em voz. Nosso foco é o código de Python e rotas de Flask que permitem a nossa aplicação, no entanto, irá ajudá-lo com o HTML e Javascript que reúne a aplicação. Caso se depare com quaisquer problemas informe-nos sabe com o botão de comentários abaixo.

Eis o que este tutorial abrange:

> [!div class="checklist"]
> * Obter chaves de subscrição do Azure
> * Configurar o ambiente de desenvolvimento e instalar dependências
> * Criar uma aplicação Flask
> * Utilizar a API de texto do Translator para traduzir texto
> * Utilizar a análise de texto para analisar o sentimento positivo/negativo de texto de entrada e traduções
> * Utilizar os serviços de voz para converter o texto traduzido em fala sintetizada
> * Executar localmente a aplicação Flask

> [!TIP]
> Se pretender avançar diretamente e ver todo o código de uma só vez, o exemplo inteiro, juntamente com a compilação instruções estão disponíveis na [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="what-is-flask"></a>O que é Flask?

Flask é um microframework para a criação de aplicativos web. Isso significa que flask fornece-lhe ferramentas, bibliotecas e tecnologias que permitem-lhe criar uma aplicação web. Esta aplicação web pode ser algumas páginas da web, um blogue, um wiki ou go como essenciais como um aplicativo de calendário e baseado na web ou um Web site comercial.

Para aqueles que desejam mergulhe após este tutorial, aqui estão alguns links úteis:

* [Documentação do flask](http://flask.pocoo.org/)
* [Flask para Dummies - manual para principiantes para Flask](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>Pré-requisitos

Vamos analisar as chaves de subscrição e de software que precisará para este tutorial.

* [Python 3.5.2 ou posterior](https://www.python.org/downloads/)
* [Ferramentas do Git](https://git-scm.com/downloads)
* Um editor de texto ou IDE, como [Visual Studio Code](https://code.visualstudio.com/) ou [Atom](https://atom.io/)  
* [Chrome](https://www.google.com/chrome/browser/) ou [Firefox](https://www.mozilla.org/firefox)
* R **de texto do tradutor** chave de subscrição (Observe que não são necessárias para selecionar uma região).
* R **análise de texto** chave de subscrição no **E.U.A. oeste** região.
* R **serviços de voz** chave de subscrição no **E.U.A. oeste** região.

## <a name="create-an-account-and-subscribe-to-resources"></a>Criar uma conta e subscrever a recursos

Como mencionado anteriormente, vai precisar de três chaves de subscrição para este tutorial. Isso significa que precisa criar um recurso na sua conta do Azure para:
* Texto do Tradutor
* Análise de Texto
* Serviços de Voz

Uso [criar uma conta dos serviços cognitivos no portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para obter instruções passo a passo criar recursos.

> [!IMPORTANT]
> Para este tutorial, crie os recursos na região E.U.A. oeste. Se utilizar uma região diferente, terá de ajustar o URL de base em cada um dos seus ficheiros de Python.

## <a name="set-up-your-dev-environment"></a>Configurar o ambiente de desenvolvimento

Antes de criar a sua aplicação web do Flask, terá de criar um diretório de trabalho para o seu projeto e instalar alguns pacotes de Python.

### <a name="create-a-working-directory"></a>Criar um diretório de trabalho

1. Abra a linha de comandos (Windows) ou o terminal (macOS/Linux). Em seguida, crie um diretório de trabalho e sub-diretórios para o seu projeto:  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. Alterar o diretório de trabalho do seu projeto:  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>Criar e ativar o seu ambiente virtual com o `virtualenv`

Vamos criar um ambiente virtual para a nossa aplicação Flask com `virtualenv`. A utilização de um ambiente virtual assegura que tem um ambiente limpo para trabalhar a partir de.

1. No diretório de trabalho, execute este comando para criar um ambiente virtual: **macOS/Linux:**
   ```
   virtualenv venv --python=python3
   ```
   Estamos explicitamente declarar que o ambiente virtual deve utilizar o Python 3. Isto garante que os utilizadores com várias instalações de Python estão a utilizar a versão correta.

   **Windows CMD / Windows Bash:**
   ```
   virtualenv venv
   ```
   Para simplificar as coisas, podemos estiver nomeando seu venv do ambiente virtual.

2. Os comandos para ativar o seu ambiente virtual irão variar consoante a plataforma/shell:   

   | Plataforma | Shell | Comando |
   |----------|-------|---------|
   | macOS/Linux | bash/zsh | `source venv/bin/activate` |
   | Windows | Bash | `source venv/Scripts/activate` |
   | | Linha de Comandos | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   Depois de executar este comando, a sua linha de comandos ou a sessão de terminal deve ser precedido pela `venv`.

3. Pode desativar a sessão em qualquer altura, digitando na linha de comandos ou terminal: `deactivate`.

> [!NOTE]
> Python possui documentação extensa sobre o para criar e gerir ambientes virtuais, consulte [virtualenv](https://virtualenv.pypa.io/en/latest/).

### <a name="install-requests"></a>Instalar pedidos

Pedidos é um módulo popular, que é utilizado para enviar pedidos de HTTP 1.1. Não é necessário adicionar manualmente as cadeias de consulta para suas URLs, ou os dados de POSTAGEM de formulário-codificar.

1. Para instalar os pedidos, execute:

   ```
   pip install requests
   ```

> [!NOTE]
> Se gostaria de saber mais sobre pedidos, consulte o artigo [pedidos: HTTP para os humanos](http://docs.python-requests.org/en/master/).

### <a name="install-and-configure-flask"></a>Instalar e configurar o Flask

Em seguida, precisamos instalar Flask. Flask processa o encaminhamento para a nossa aplicação web e permite-nos fazer chamadas para-servidor que ocultam as nossas chaves de subscrição do utilizador final.

1. Para instalar o Flask, execute:
   ```
   pip install Flask
   ```
   Vamos certificar-se de que flask foi instalado. Execução:
   ```
   flask --version
   ```
   A versão deve ser impresso para o terminal. Qualquer outra coisa significa que algo correu mal.

2. Para executar a aplicação Flask, pode usar o comando de flask ou comutador de -m do Python com o Flask. Antes de poder fazer que precisa informar ao seu terminal aplicação que trabalhar com exportando o `FLASK_APP` variável de ambiente:

   **macOS/Linux**:
   ```
   export FLASK_APP=app.py
   ```

   **Windows**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>Criar a sua aplicação Flask

Nesta secção, vamos criar uma aplicação Flask de barebones que retorna um arquivo HTML quando os utilizadores aceder a raiz da sua aplicação. Não gaste muito tempo tentando escolher-o código, vamos voltar ao atualizar este ficheiro mais tarde.

### <a name="what-is-a-flask-route"></a>O que é uma rota de Flask?

Vamos dispensar um minuto para falar sobre "[rotas](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)". Encaminhamento é usado para ligar um URL para uma função específica. Flask utiliza decoradores de rota para registrar as funções para URLs específicos. Por exemplo, quando um utilizador navega para a raiz (`/`) da nossa aplicação web, `index.html` é processado.  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

Vamos dar uma olhada num exemplo mais para hammer esta home.

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

Este código assegura que, quando um utilizador navega para `http://your-web-app.com/about` que o `about.html` ficheiro é processado.

Embora estes exemplos ilustram como renderizar páginas html para um utilizador, os rotas também podem ser utilizadas para chamar as APIs, quando um botão é premido ou fazer qualquer número de ações sem ter de navegar para fora da home page. Verá isso em ação quando criar rotas para tradução, sentimento e síntese de fala.

### <a name="get-started"></a>Introdução

1. Abra o projeto no IDE, em seguida, crie um ficheiro denominado `app.py` na raiz do seu diretório de trabalho. Em seguida, copie este código para `app.py` e guarde:

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   Este bloco de código diz à aplicação a apresentar `index.html` sempre que um utilizador navega para a raiz da sua aplicação web (`/`).

2. Em seguida, vamos criar um front-end para a nossa aplicação web. Crie um ficheiro denominado `index.html` no `templates` diretório. Em seguida, copie este código para `templates/index.html`.

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

3. Vamos testar a aplicação Flask. A partir do terminal, execute:

   ```
   flask run
   ```

4. Abra um browser e navegue para o URL fornecido. Deve ver a sua aplicação de página única. Prima **Ctrl + c** para eliminar a aplicação.

## <a name="translate-text"></a>Traduzir texto

Agora que tem uma ideia de como uma aplicação Flask simples funciona, vamos:

* Escrever alguns Python para chamar a API de texto do Translator e retornar uma resposta
* Criar uma rota de Flask para chamar o código de Python
* Atualize o HTML com uma área para entrada de texto e tradução, um Seletor de idioma e traduzir botão
* Escrever Javascript que permite que os utilizadores interajam com a sua aplicação Flask de HTML

### <a name="call-the-translator-text-api"></a>Chamar a API de texto do tradutor

A primeira coisa que precisa fazer é escrever uma função para chamar a API de texto do Translator. Esta função irá usar dois argumentos: `text_input` e `language_output`. Esta função é chamada sempre que um usuário pressiona o botão translate na sua aplicação. A área de texto no HTML é enviada como o `text_input`, e o valor de seleção de idioma no HTML enviado como `language_output`.

1. Vamos começar por criar um arquivo chamado `translate.py` na raiz do seu diretório de trabalho.
2. Em seguida, adicione este código para `translate.py`. Esta função aceita dois argumentos: `text_input` e `language_output`.
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'

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
3. Adicione a chave de subscrição do Microsoft Translator texto e guarde.

### <a name="add-a-route-to-apppy"></a>Adicione uma rota para `app.py`

Em seguida, terá de criar uma rota na sua aplicação Flask que chama `translate.py`. Esta rota será chamada sempre que um usuário pressiona o botão translate na sua aplicação.

Para esse aplicativo, sua rota vai aceitar `POST` pedidos. Isto acontece porque a função espera que o texto a traduzir e uma linguagem de saída para a tradução.

Flask fornece funções de programa auxiliar para o ajudar a analisar e gerir cada pedido. No código fornecido, `get_json()` devolve os dados a partir do `POST` pedido como JSON. Em seguida, utilizar `data['text']` e `data['to']`, os valores de idioma do texto e de saída são transmitidos ao `get_translation()` função disponível a partir do `translate.py`. A última etapa é retornar a resposta como JSON, uma vez que precisará apresentar estes dados na sua aplicação web.

Nas seções a seguir, deverá repetir este processo à medida que cria rotas para a sintetização sentimento de análise e conversão de voz.

1. Open `app.py` e localize a instrução de importação no topo da `app.py` e adicione a seguinte linha:

   ```python
   import translate
   ```
   Agora a nossa aplicação Flask pode utilizar o método disponível por meio de `translate.py`.

2. Copie este código ao final do `app.py` e guarde:

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

Agora que tem uma função para traduzir texto e uma rota na sua aplicação Flask chamá-la, a próxima etapa é começar a criar o HTML para a sua aplicação. O HTML abaixo faz algumas coisas:

* Fornece uma área de texto em que os utilizadores podem inserir texto a traduzir.
* Inclui um Seletor de idioma.
* Inclui elementos HTML para processar o idioma detetado e pontuações de confiança devolvidas durante a tradução.
* Fornece uma área de texto só de leitura em que é apresentado o resultado de tradução.
* Inclui espaços reservados para sentimento análise e conversão de voz síntese código que terá de adicionar a este ficheiro mais tarde no tutorial.

Vamos atualizar `index.html`.

1. Abra `index.html` e localize esses comentários de código:
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. Substitua os comentários de código este bloco HTML:
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

A próxima etapa é escrever um Javascript. Esta é a ponte entre o percurso HTML e Flask.

### <a name="create-mainjs"></a>Criar `main.js`  

O `main.js` arquivo é a ponte entre o percurso HTML e Flask. A aplicação irá utilizar uma combinação de jQuery, o Ajax e o XMLHttpRequest para processar conteúdo e tornar `POST` pedidos para as rotas de Flask.

No código abaixo, o conteúdo do HTML é utilizado para criar um pedido para o percurso de Flask. Especificamente, o conteúdo da área de texto e o Seletor de idioma é atribuído às variáveis e, em seguida, passado para o pedido para `translate-text`.

O código, em seguida, itera através de resposta e atualiza o HTML com a tradução, o idioma detetado e a pontuação de confiança.

1. A partir do seu IDE, crie um ficheiro denominado `main.js` no `static/scripts` diretório.
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

Vamos testar tradução na aplicação.

```
flask run
```

Navegue para o endereço do servidor fornecido. Digite um texto para a área entrada, selecione que um idioma e prima traduzir. Obterá uma tradução. Se não funcionar, certifique-se de que adicionou a chave de subscrição.

> [!TIP]
> Se as alterações efetuadas não aparecerem, ou a aplicação não funciona da forma espera, tente limpar a cache ou abrir uma janela de privado/incognito.

Prima **CTRL + c** para eliminar a aplicação, em seguida, avançar para a secção seguinte.

## <a name="analyze-sentiment"></a>Analisar sentimento

O [API de análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) pode ser utilizado para efetuar a análise de sentimentos, extrair expressões-chave de texto ou detetar o idioma de origem. Esta aplicação, vamos utilizar a análise de sentimentos para determinar se o texto fornecido for negativo, positivo ou neutro. A API devolve uma pontuação numérica entre 0 e 1. As pontuações próximas de 1 indicam um sentimento positivo, enquanto as pontuações próximas de 0 indicam um sentimento negativo.

Nesta secção, vamos fazer algumas coisas:

* Escrever alguns Python para chamar a API de análise de texto para efetuar a análise de sentimentos e devolver uma resposta
* Criar uma rota de Flask para chamar o código de Python
* Atualizar o HTML com uma área para as classificações de sentimentos e um botão para executar uma análise
* Escrever Javascript que permite que os utilizadores interajam com a sua aplicação Flask de HTML

### <a name="call-the-text-analytics-api"></a>Chamar a API de Análise de Texto

Vamos escrever uma função para chamar a API de análise de texto. Esta função irá usar quatro argumentos: `input_text`, `input_language`, `output_text`, e `output_language`. Esta função é chamada sempre que um usuário pressiona o botão de análise de sentimentos de execução na sua aplicação. Dados fornecidos pelo utilizador do Seletor de área e o idioma de texto, bem como a saída de tradução e o idioma detetada são fornecidos com cada solicitação. O objeto de resposta inclui as classificações de sentimentos para a origem e a tradução. As secções seguintes, vamos escrever um Javascript para analisar a resposta e utilizá-lo na sua aplicação. Por enquanto, vamos nos concentrar na chamada da API de análise de texto.

1. Vamos criar um arquivo chamado `sentiment.py` na raiz do seu diretório de trabalho.
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
3. Adicione a chave de subscrição de análise de texto e guarde.

### <a name="add-a-route-to-apppy"></a>Adicione uma rota para `app.py`

Vamos criar uma rota na sua aplicação Flask que chama `sentiment.py`. Esta rota será chamada sempre que um usuário pressiona o botão de análise de sentimentos de execução na sua aplicação. Como a rota para a tradução, esta rota vai aceitar `POST` solicita uma vez que a função espera os argumentos.

1. Open `app.py` e localize a instrução de importação no topo da `app.py` e atualizá-lo:

   ```python
   import translate, sentiment
   ```
   Agora a nossa aplicação Flask pode utilizar o método disponível por meio de `sentiment.py`.

2. Copie este código ao final do `app.py` e guarde:
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

Agora que tem uma função para executar a análise de sentimentos e uma rota na sua aplicação Flask chamá-lo, o próximo passo é começar a escrever o HTML para a sua aplicação. O HTML abaixo faz algumas coisas:

* Adiciona um botão à sua aplicação para executar a análise de sentimentos
* Adiciona um elemento que explica a classificação de sentimento
* Adiciona um elemento para apresentar as classificações de sentimentos

1. Abra `index.html` e localize esses comentários de código:
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. Substitua os comentários de código este bloco HTML:
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment scores are provided on a 1 point scale. The closer the sentiment score is to 1, indicates positive sentiment. The closer it is to 0, indicates negative sentiment.</p>
      <strong>Sentiment score for input:</strong> <span id="input-sentiment"></span><br />
      <strong>Sentiment score for translation:</strong> <span id="translation-sentiment"></span>
   </div>
   ```

### <a name="update-mainjs"></a>Atualizar `main.js`

No código abaixo, o conteúdo do HTML é utilizado para criar um pedido para o percurso de Flask. Especificamente, o conteúdo da área de texto e o Seletor de idioma é atribuído às variáveis e, em seguida, passado para o pedido para o `sentiment-analysis` rota.

O código, em seguida, itera através de resposta e atualiza o HTML com as classificações de sentimentos.

1. A partir do seu IDE, crie um ficheiro denominado `main.js` no `static` diretório.

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

### <a name="test-sentiment-analysis"></a>Análise de sentimentos de teste

Vamos testar a análise de sentimentos na aplicação.

```
flask run
```

Navegue para o endereço do servidor fornecido. Digite um texto para a área entrada, selecione que um idioma e prima traduzir. Obterá uma tradução. Em seguida, prima o botão de análise de sentimentos de execução. Deverá ver duas classificações. Se não funcionar, certifique-se de que adicionou a chave de subscrição.

> [!TIP]
> Se as alterações efetuadas não aparecerem, ou a aplicação não funciona da forma espera, tente limpar a cache ou abrir uma janela de privado/incognito.

Prima **CTRL + c** para eliminar a aplicação, em seguida, avançar para a secção seguinte.

## <a name="convert-text-to-speech"></a>Converter texto em voz

O [API de voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) permite à sua aplicação converter texto em fala sintetizada de semelhante à humana natural. O serviço suporta vozes padrão, neurais e personalizadas. A nossa aplicação de exemplo utiliza um punhado das vozes disponíveis, para obter uma lista completa, consulte [idiomas suportados](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech).

Nesta secção, vamos fazer algumas coisas:

* Escrever alguns Python para converter voz com a API de voz
* Criar uma rota de Flask para chamar o código de Python
* Atualizar o HTML com um botão para converter voz e um elemento para reprodução de áudio
* Escrever Javascript que permite que os utilizadores interajam com a sua aplicação Flask

### <a name="call-the-text-to-speech-api"></a>Chamar a API de texto para discurso

Vamos escrever uma função para converter voz. Esta função irá usar dois argumentos: `input_text` e `voice_font`. Esta função é chamada sempre que um usuário pressiona o botão de texto para discurso de conversão na sua aplicação. `input_text` é a saída de tradução retornada pela chamada para traduzir texto, `voice_font` é o valor do Seletor de tipo de letra de voz no HTML.

1. Vamos criar um arquivo chamado `synthesize.py` na raiz do seu diretório de trabalho.

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
3. Adicione a chave de subscrição de serviços de voz e guarde.

### <a name="add-a-route-to-apppy"></a>Adicione uma rota para `app.py`

Vamos criar uma rota na sua aplicação Flask que chama `synthesize.py`. Esta rota será chamada sempre que um usuário pressiona o botão de texto para discurso de conversão na sua aplicação. Como as rotas de tradução e análise de sentimentos, esta rota vai aceitar `POST` solicita uma vez que a função espera dois argumentos: o texto para sintetizar e o tipo de voz para a reprodução.

1. Open `app.py` e localize a instrução de importação no topo da `app.py` e atualizá-lo:

   ```python
   import translate, sentiment, synthesize
   ```
   Agora a nossa aplicação Flask pode utilizar o método disponível por meio de `synthesize.py`.

2. Copie este código ao final do `app.py` e guarde:

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

Agora que tem uma função para converter voz e uma rota na sua aplicação Flask chamá-lo, o próximo passo é começar a escrever o HTML para a sua aplicação. O HTML abaixo faz algumas coisas:

* Fornece uma seleção de voz pendente
* Adiciona um botão para converter voz
* Adiciona um elemento de áudio, o que é utilizado para reproduzir a fala sintetizada

1. Abra `index.html` e localize esses comentários de código:
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. Substitua os comentários de código este bloco HTML:
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

3. Em seguida, localize esses comentários de código:
   ```html
   <!-- Add Speech Synthesis button and audio element -->

   <!-- End Speech Synthesis button -->
   ```

4. Substitua os comentários de código este bloco HTML:

```html
<button type="submit" class="btn btn-primary mb-2" id="text-to-speech">Convert text-to-speech</button>
<div id="audio-playback">
  <audio id="audio" controls>
    <source id="audio-source" type="audio/mpeg" />
  </audio>
</div>
```

5. Certifique-se de guardar o seu trabalho.

### <a name="update-mainjs"></a>Atualizar `main.js`

No código abaixo, o conteúdo do HTML é utilizado para criar um pedido para o percurso de Flask. Especificamente, a tradução e o tipo de voz são atribuídos a variáveis e, em seguida, passados para o pedido para o `text-to-speech` rota.

O código, em seguida, itera através de resposta e atualiza o HTML com as classificações de sentimentos.

1. A partir do seu IDE, crie um ficheiro denominado `main.js` no `static` diretório.
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
3. Está quase concluído. A última coisa que vamos fazer é adicionar um código para `main.js` para selecionar automaticamente um tipo de voz com base no idioma selecionado para a tradução. Adicione este bloco de código para `main.js`:
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

Vamos testar síntese de fala na aplicação.

```
flask run
```

Navegue para o endereço do servidor fornecido. Digite um texto para a área entrada, selecione que um idioma e prima traduzir. Obterá uma tradução. Em seguida, selecione uma voz, em seguida, prima o botão de texto para discurso convert. a tradução deve ser reproduzida como fala sintetizada. Se não funcionar, certifique-se de que adicionou a chave de subscrição.

> [!TIP]
> Se as alterações efetuadas não aparecerem, ou a aplicação não funciona da forma espera, tente limpar a cache ou abrir uma janela de privado/incognito.

É isso, tem uma aplicação de trabalho que efetua as traduções, analisa os sentimentos e fala sintetizada. Prima **CTRL + c** para eliminar a aplicação. Certifique-se de que veja as outras [serviços cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/).

## <a name="get-the-source-code"></a>Obter o código-fonte

O código-fonte para este projeto está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="next-steps"></a>Passos Seguintes

* [Referência da API de Texto do Microsoft Translator](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
* [Text Analytics API reference](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) (Referência à API de Análise de Texto)
* [Referência da API de conversão de texto em voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)
