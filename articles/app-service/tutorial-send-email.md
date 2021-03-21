---
title: 'Tutorial: envie e-mail com Apps Lógicas'
description: Saiba como invocar processos de negócio a partir da sua aplicação App Service. Envie e-mails, tweets e posts do Facebook, adicione às listas de correio e muito mais.
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 8072a941cd89290af3e25cc63c4fccccce705df9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95014667"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>Tutorial: Enviar e-mail e invocar outros processos de negócio do Serviço de Aplicações

Neste tutorial, aprende a integrar a sua app De Serviço de Aplicações com os seus processos de negócio. Isto é comum a cenários de aplicações web, tais como:

- Enviar e-mail de confirmação para uma transação
- Adicionar utilizador ao grupo do Facebook
- Ligue-se a sistemas de terceiros como SAP, Salesforce, etc.
- Trocar mensagens B2B padrão

Neste tutorial, envia e-mails com o Gmail a partir da sua aplicação de Serviço de [Aplicações, utilizando aplicações Azure Logic.](../logic-apps/logic-apps-overview.md) Existem outras formas de enviar e-mails a partir de uma aplicação web, como a configuração SMTP fornecida pela sua estrutura linguística. No entanto, as Aplicações Lógicas trazem muito mais energia à sua aplicação de Serviço de Aplicações sem adicionar complexidade ao seu código. As Aplicações Lógicas fornecem uma interface de configuração simples para as integrações de negócios mais populares, e a sua aplicação pode chamá-las a qualquer momento com um pedido HTTP.

## <a name="prerequisite"></a>Pré-requisito

Implemente uma aplicação com o enquadramento linguístico da sua escolha para o Serviço de Aplicações. Para seguir um tutorial para implementar uma aplicação de amostra, consulte abaixo:

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[Tutorial: Criar uma aplicação ASP.NET no Azure com a Base de Dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md)

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[Tutorial: Construir uma aplicação de base de dados core e SQL de ASP.NET no Azure App Service](tutorial-dotnetcore-sqldb-app.md)

### <a name="nodejs"></a>[Node.js](#tab/node)

[Tutorial: Construa uma aplicação Node.js e MongoDB em Azure](tutorial-nodejs-mongodb-app.md)

### <a name="php"></a>[PHP](#tab/php)

[Tutorial: Construa uma app PHP e MySQL em Azure](tutorial-php-mysql-app.md)

### <a name="python"></a>[Python](#tab/python)

[Tutorial: Executar uma aplicação web Python (Django) com PostgreSQL em Azure App Service](tutorial-python-postgresql-app.md)

### <a name="ruby"></a>[Ruby](#tab/ruby)

[Construa uma app Ruby e Postgres no Azure App Service em Linux](tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>Criar a App Lógica

1. No [portal Azure,](https://portal.azure.com)crie uma aplicação lógica vazia seguindo as instruções na [Create your logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app). Quando vir o **Designer de Aplicações Lógicas,** volte a este tutorial.
1. Na página de respingo para o Designer de Aplicações Lógicas, selecione **Quando um pedido HTTP for recebido** no Início com um gatilho **comum**.

    ![Screenshot que mostra a página de respingo para o Designer de Aplicações Lógicas com quando um pedido de H T T P é recebido em destaque.](./media/tutorial-send-email/receive-http-request.png)
1. No diálogo para **quando for recebido um pedido HTTP**, selecione Utilize a carga útil da amostra para gerar **esquema**.

    ![Screenshot que mostra a caixa de diálogo de pedido de H T T P e a carga útil da amostra de utilização para gerar esquema opion selecionado. ](./media/tutorial-send-email/generate-schema-with-payload.png)

1. Copie a amostra seguinte JSON na caixa de texto e selecione **Feito**.

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    O esquema é agora gerado para os dados de pedido que deseja. Na prática, pode apenas capturar os dados de pedido real que o seu código de aplicação gera e deixar o Azure gerar o esquema JSON para si. 
1. No topo do Logic Apps Designer, **selecione Save**. 

    Pode agora ver o URL do seu gatilho de pedido HTTP. Selecione o ícone de cópia para copiá-lo para posterior utilização.

    ![Screenshot que realça o ícone da cópia para copiar o U R L do seu gatilho de pedido H T T. P.](./media/tutorial-send-email/http-request-url.png)

    Esta definição de pedido HTTP é um gatilho para tudo o que pretende fazer nesta aplicação lógica, seja o Gmail ou qualquer outra coisa. Mais tarde, irá invocar este URL na sua aplicação App Service. Para obter mais informações sobre o gatilho do pedido, consulte a [referência HTTP pedido/resposta.](../connectors/connectors-native-reqres.md)

1. Na parte inferior do designer, clique em **Novo passo,** **digite** Gmail na caixa de pesquisa de ações e encontre e selecione **Enviar e-mail (V2)**.
    
    > [!TIP]
    > Pode procurar outros tipos de integrações, tais como SendGrid, MailChimp, Microsoft 365 e SalesForce. Para mais informações, consulte [a documentação da Logic Apps](../logic-apps/index.yml).

1. No diálogo do **Gmail,** selecione **Iniciar s.S.** e iniciar scontabilidade na conta do Gmail a partir do seguinte.

    ![Screenshot que mostra a caixa de diálogo do Gmail que usa para assinar na conta do Gmail a partir da a quais pretende enviar e-mail.](./media/tutorial-send-email/gmail-sign-in.png)

1. Uma vez assinado, clique na caixa de texto **Para** e o diálogo de conteúdo dinâmico é automaticamente aberto.

1. Ao lado da ação **Quando um pedido HTTP for recebido,** selecione Ver **mais**.

    ![Screenshot que mostra o botão Ver Mais ao lado Quando um pedido de H T T P é recebido ação.](./media/tutorial-send-email/expand-dynamic-content.png)

    Deverá agora ver as três propriedades da sua amostra de dados JSON que utilizou anteriormente. Neste passo, utilize estas propriedades a partir do pedido HTTP para a construção de um e-mail.
1. Uma vez que está a selecionar o valor para o campo **To,** escolha **o e-mail.** Se desejar, desligue o diálogo dinâmico do conteúdo clicando **em Adicionar conteúdo dinâmico**.

    ![Screenshot que mostra a opção de e-mail e adicionar a opção de contenção dinâmica realçada.](./media/tutorial-send-email/hide-dynamic-content.png)

1. No **dropdown de parâmetros adicionado,** **selecione Subject** and **Body**.

1. Clique na caixa de texto **'Assunto'** e, da mesma forma, escolha **a tarefa**. Com o cursor ainda na caixa **Assunto,** tipo *criado*. 

1. Clique no **Corpo**, e da mesma forma, escolha **o devido**. Mover o cursor para a esquerda do **devido e** tipo Este item de *trabalho deve-se .*

    > [!TIP]
    > Se pretender editar conteúdo HTML diretamente no corpo de e-mail, selecione **a vista** de código na parte superior da janela Logic Apps Designer. Apenas certifique-se de preservar o código de conteúdo dinâmico (por exemplo, `@{triggerBody()?['due']}` )
    >
    > ![Screenshot que mostra a vista de código para visualizar o conteúdo de H T M L diretamente no corpo de e-mail.](./media/tutorial-send-email/edit-rich-html-email.png) 

1. Em seguida, adicione uma resposta HTTP assíncronea ao gatilho HTTP. Entre o gatilho HTTP e a ação do Gmail, clique no **+** sinal e **selecione Adicione um ramo paralelo**.

    ![Screenshot que mostra o sinal + e Adicione uma opção de ramo paralelo realçada.](./media/tutorial-send-email/add-http-response.png)

1. Na caixa de pesquisa, procure **resposta** e, em seguida, selecione a ação **Resposta.**

    ![Screenshot que mostra a barra de pesquisa e ação de resposta realçada.](./media/tutorial-send-email/choose-response-action.png)

    Por predefinição, a ação de resposta envia um HTTP 200. É bom o suficiente para este tutorial. Para mais informações, consulte a [referência HTTP pedido/resposta.](../connectors/connectors-native-reqres.md)

1. No topo do Logic Apps Designer, **selecione Save** again. 

## <a name="add-http-request-code-to-app"></a>Adicionar código de pedido HTTP para app

Certifique-se de que copiou o URL do gatilho do pedido HTTP de anteriormente. Porque contém informação sensível, é melhor que não a coloque diretamente no código. Com o Serviço de Aplicações, pode referenciar-se como uma variável ambiental, utilizando as definições de aplicações. 

No [Cloud Shell](https://shell.azure.com), crie a definição da aplicação com o seguinte comando (substituir *\<app-name>* , e *\<resource-group-name>* *\<logic-app-url>* ):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

No seu código, faça uma publicação HTTP padrão para o URL utilizando qualquer idioma de cliente HTTP disponível para a sua estrutura linguística, com a seguinte configuração:

- O corpo de pedido contém o mesmo formato JSON que forneceu à sua aplicação lógica:

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- O pedido contém a rubrica `Content-Type: application/json` . 
- Para otimizar o desempenho, envie o pedido assíncronosamente, se possível.

Clique no separador linguagem/quadro preferido abaixo para ver um exemplo.

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

Em ASP.NET, pode enviar o post HTTP com a classe [System.net.http.httpClient.](/dotnet/api/system.net.http.httpclient) Por exemplo:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // requires using System.Configuration;
    ConfigurationManager.AppSettings["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

Se estiver a testar este código na aplicação da amostra para [Tutorial: Construa uma aplicação ASP.NET em Azure com base de dados SQL,](app-service-web-tutorial-dotnet-sqldatabase.md)poderá usá-lo para enviar uma confirmação de e-mail na [ação Create](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63), depois de o `Todo` item ser adicionado. Para utilizar o código assíncronos acima, converta a ação Create em assíncronos.

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

Em ASP.NET Core, pode enviar o post HTTP com a classe [System.net.http.httpClient.](/dotnet/api/system.net.http.httpclient) Por exemplo:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // Requires DI configuration to access app settings. See https://docs.microsoft.com/azure/app-service/configure-language-dotnetcore#access-environment-variables
    _configuration["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

> [!NOTE]
> Este código está escrito para a simplicidade da demonstração. Na prática, não instantiem um `HttpClient` objeto para cada pedido. Siga as orientações da [Use IHttpClientFactory para implementar pedidos HTTP resilientes](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

Se estiver a testar este código na aplicação da amostra para [Tutorial: Construa uma aplicação core e SQL Database ASP.NET no Azure App Service,](tutorial-dotnetcore-sqldb-app.md)poderá usá-lo para enviar uma confirmação de e-mail na [ação Create](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65), depois de o `Todo` item ser adicionado.

### <a name="nodejs"></a>[Node.js](#tab/node)

Em Node.js, pode enviar facilmente o posto HTTP com um pacote npm como [axios.](https://www.npmjs.com/package/axios) Por exemplo:

```javascript
// Requires npm install --save axios
const axios = require('axios');

var jsonData = {
        email: "a-valid@emailaddress.com",
        due: "4/1/2020",
        task: "My new task!"
};

(async function(data) {
    try {
        const response = await axios.post(process.env.LOGIC_APP_URL, jsonData);
        console.log(response.status);
    } catch (error) {
        console.log(error);
    }
})(jsonData);

```

Se estiver a testar este código na aplicação da amostra para [Tutorial: Construa uma aplicação Node.js e MongoDB em Azure,](tutorial-nodejs-mongodb-app.md)poderá usá-lo para enviar uma confirmação por e-mail na [função de criação,](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27)depois [de o artigo ser guardado com sucesso](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24).

### <a name="php"></a>[PHP](#tab/php)

Em PHP, pode enviar facilmente o post HTTP com [guzzle](http://docs.guzzlephp.org/en/stable/index.html). Por exemplo:

```php
// Requires composer require guzzlehttp/guzzle:~6.0
use GuzzleHttp\Client;
...
$client = new Client();
$options = [
    'json' => [ 
        'email' => 'a-valid@emailaddress.com',
        'due' => '4/1/2020',
        'task' => "My new task!"
    ]
];

$promise = $client-> postAsync(getenv($LOGIC_APP_URL), $options)->then( 
    function ($response) {
        return $response->getStatusCode();
    }, function ($exception) {
        return $exception->getResponse();
    }
);

$response = $promise->wait();
// Requires Laravel to run Log::info(). Check the documentation of your preferred framework for logging instructions.
Log::info(print_r($response, TRUE));
```

Se estiver a testar este código na aplicação de amostras para [Tutorial: Construa uma aplicação PHP e MySQL em Azure,](tutorial-php-mysql-app.md)poderá usá-lo para enviar uma confirmação de e-mail na [função Route::p ost](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48), pouco antes da declaração de devolução.

### <a name="python"></a>[Python](#tab/python)

Em Python, pode enviar o posto HTTP facilmente com [pedidos.](https://pypi.org/project/requests/) Por exemplo:

```python
# Requires pip install requests && pip freeze > requirements.txt
import requests
...
payload = {
    "email": "a-valid@emailaddress.com",
    "due": "4/1/2020",
    "task": "My new task!"
}
response = requests.post("https://prod-112.westeurope.logic.azure.com:443/workfl$
print(response.status_code)
```
<!-- ```python
# Requires pip install aiohttp && pip freeze > requirements.txt
import aiohttp
...
payload = {
        'email': 'a-valid@emailaddress.com',
        'due': '4/1/2020',
        'task': 'My new task!'
}
async with aiohttp.post('http://httpbin.org/post', data=json.dump(payload)) as resp:
    print(await resp.status())
``` -->

Se estiver a testar este código na aplicação de amostras para [Tutorial: Executar uma aplicação web Python (Django) com PostgreSQL no Azure App Service,](tutorial-python-postgresql-app.md)poderá usá-lo para enviar uma confirmação de e-mail na [função Route::p ost](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48), pouco antes da declaração de devolução.

### <a name="ruby"></a>[Ruby](#tab/ruby)

Em Ruby, pode enviar o post HTTP facilmente com jSONClient. Por exemplo:

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

Se estiver a testar este código na aplicação de amostras para [construir uma aplicação Ruby e Postgres no Azure App Service no Linux,](tutorial-ruby-postgres-app.md)poderá usá-lo para enviar uma confirmação de e-mail na ação [de criação,](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38) quando [tiver @task.save sucesso.](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30)

---

## <a name="more-resources"></a>Mais recursos

[Tutorial: Alojar uma API RESTful com CORS no Serviço de Aplicações do Azure](app-service-web-tutorial-rest-api.md)  
[HTTP pedido/referência de resposta para Apps Lógicas](../connectors/connectors-native-reqres.md)  
[Quickstart: Crie o seu primeiro fluxo de trabalho utilizando aplicações Azure Logic - portal Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md)