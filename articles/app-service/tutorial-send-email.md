---
title: 'Tutorial: envie e-mail com Aplicações Lógicas'
description: Saiba como invocar processos empresariais a partir da sua app App Service. Envie e-mails, tweets e posts no Facebook, adicione às listas de correio eletrónico, e muito mais.
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: mvc
ms.openlocfilehash: 4073b49a134356943bd7da8d54bf574f2e0d5eea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81604857"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>Tutorial: Envie e-mail e invoque outros processos de negócio do App Service

Neste tutorial, aprende a integrar a sua app de Serviço de Aplicações com os seus processos de negócio. Isto é comum a cenários de aplicações web, tais como:

- Enviar e-mail de confirmação para uma transação
- Adicionar utilizador ao grupo do Facebook
- Ligue-se a sistemas de terceiros como SAP, SalesForce, etc.
- Trocar mensagens B2B padrão

Neste tutorial, envia e-mails com o Gmail a partir da sua app App Service utilizando [aplicações da Azure Logic](../logic-apps/logic-apps-overview.md). Existem outras formas de enviar e-mails de uma aplicação web, como a configuração SMTP fornecida pelo seu enquadramento linguístico. No entanto, as Aplicações Lógicas trazem muito mais energia à sua aplicação App Service sem adicionar complexidade ao seu código. Logic Apps fornece uma interface de configuração simples para as integrações de negócios mais populares, e a sua aplicação pode chamá-las a qualquer momento com um pedido HTTP.

## <a name="prerequisite"></a>Pré-requisito

Implemente uma aplicação com o enquadramento linguístico da sua escolha para o App Service. Para seguir um tutorial para implementar uma aplicação de amostra, consulte abaixo:

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[Tutorial: Criar uma aplicação ASP.NET no Azure com a Base de Dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md)

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[Tutorial: Construa uma app de base de dados core e SQL ASP.NET no Serviço de Aplicações Azure](app-service-web-tutorial-dotnetcore-sqldb.md)

# <a name="nodejs"></a>[Node.js](#tab/node)

[Tutorial: Construa uma app Node.js e MongoDB em Azure](app-service-web-tutorial-nodejs-mongodb-app.md)

# <a name="php"></a>[PHP](#tab/php)

[Tutorial: Construa uma app PHP e MySQL em Azure](app-service-web-tutorial-php-mysql.md)

# <a name="python"></a>[Python](#tab/python)

[Tutorial: Executar uma aplicação web Python (Django) com PostgreSQL no Azure App Service](containers/tutorial-python-postgresql-app.md)

# <a name="ruby"></a>[Ruby](#tab/ruby)

[Construa uma app Ruby and Postgres no Azure App Service em Linux](containers/tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>Criar a App Lógica

1. No [portal Azure, crie](https://portal.azure.com)uma aplicação lógica vazia seguindo as instruções na [Create your logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app). Quando vir o Designer de **Aplicações Lógicas,** volte a este tutorial.
1. Na página de splash para Logic Apps Designer, selecione **Quando um pedido HTTP é recebido** em Iniciar com um gatilho **comum**.

    ![](./media/tutorial-send-email/receive-http-request.png)
1. No diálogo **para Quando um pedido HTTP é recebido,** selecione Utilize a carga útil da amostra para gerar **esquema**.

    ![](./media/tutorial-send-email/generate-schema-with-payload.png)

1. Copie a amostra seguinte JSON na caixa de texto e selecione **Done**.

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    O esquema é agora gerado para os dados de pedido que pretende. Na prática, pode simplesmente capturar os dados de pedidos reais que o seu código de aplicação gera e deixar que o Azure gere o esquema JSON para si. 
1. No topo do Logic Apps Designer, selecione **Save**. 

    Pode agora ver o URL do seu gatilho de pedido HTTP. Selecione o ícone da cópia para copiá-lo para posterior utilização.

    ![](./media/tutorial-send-email/http-request-url.png)

    Esta definição de pedido http é um gatilho para qualquer coisa que você quer fazer nesta aplicação lógica, seja o Gmail ou qualquer outra coisa. Mais tarde, irá invocar este URL na sua aplicação App Service. Para obter mais informações sobre o gatilho do pedido, consulte a referência de [pedido/resposta HTTP](../connectors/connectors-native-reqres.md).

1. Na parte inferior do designer, clique em **Novo passo,** digite o **Gmail** na caixa de pesquisa de ações e encontre e selecione **Enviar e-mail (V2)**.
    
    > [!TIP]
    > Pode procurar outros tipos de integrações, tais como SendGrid, MailChimp, Office 365 e SalesForce. Para mais informações, consulte a documentação das [Aplicações Lógicas.](https://docs.microsoft.com/azure/logic-apps/)
1. No diálogo **gmail,** selecione **Iniciar sessão** e iniciar sessão na conta do Gmail a partir da sua deseja enviar o e-mail.

    ![](./media/tutorial-send-email/gmail-sign-in.png)

1. Uma vez inscrito, clique na caixa de texto **para** e o diálogo de conteúdo dinâmico é automaticamente aberto.

1. Junto ao **pedido Quando um pedido HTTP é recebido,** selecione Ver **mais**.

    ![](./media/tutorial-send-email/expand-dynamic-content.png)

    Deverá agora ver as três propriedades dos dados json da sua amostra que utilizou anteriormente. Neste passo, utiliza estas propriedades a partir do pedido http para construir um e-mail.
1. Uma vez que está a selecionar o valor para o campo **To,** escolha **e-mail**. Se desejar, altere o diálogo de conteúdo dinâmico clicando em **Adicionar conteúdo dinâmico**.

    ![](./media/tutorial-send-email/hide-dynamic-content.png)

1. No **add novo parâmetro** dropdown, selecione **Assunto** e **Corpo**.

1. Clique na caixa de texto **do Assunto** e, da mesma forma, escolha **a tarefa**. Com o cursor ainda na caixa **do Assunto,** o tipo *criado.* 

1. Clique no **Corpo**, e da mesma forma, escolha **o devido**. Mova o cursor para a esquerda do **devido** e escreva Este artigo de *trabalho deve-se .*

    > [!TIP]
    > Se quiser editar conteúdo HTML diretamente no corpo de e-mail, selecione **a vista de Código** no topo da janela Logic Apps Designer. Basta certificar-se de que preserva o `@{triggerBody()?['due']}`código de conteúdo dinâmico (por exemplo, )
    >
    > ![](./media/tutorial-send-email/edit-rich-html-email.png) 

1. Em seguida, adicione uma resposta http assíncrona ao gatilho HTTP. Entre o gatilho HTTP e a **+** ação do Gmail, clique no sinal e selecione **Adicionar um ramo paralelo**.

    ![](./media/tutorial-send-email/add-http-response.png)

1. Na caixa de pesquisa, procure **resposta**e, em seguida, selecione a ação **Resposta.**

    ![](./media/tutorial-send-email/choose-response-action.png)

    Por padrão, a ação de resposta envia um HTTP 200. É bom o suficiente para este tutorial. Para mais informações, consulte a [referência http/resposta](../connectors/connectors-native-reqres.md).

1. No topo do Logic Apps Designer, selecione **Save** novamente. 

## <a name="add-http-request-code-to-app"></a>Adicione código de pedido HTTP à app

Certifique-se de que copiou o URL do gatilho de pedido HTTP de há pouco. Como contém informação sensível, é a melhor prática que não a coloque diretamente no código. Com o Serviço de Aplicações, pode referenciar como uma variável ambiental, utilizando as definições da aplicação. 

Na [Cloud Shell,](https://shell.azure.com)crie a definição da aplicação * \< *com o seguinte comando (substitua * \<o nome da aplicação>, *>de nome de grupo de recursos e>de>de * \<aplicações lógicas): *

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

No seu código, faça um post HTTP padrão no URL utilizando qualquer idioma cliente HTTP que esteja disponível para o seu quadro linguístico, com a seguinte configuração:

- O organismo de pedido contém o mesmo formato JSON que forneceu à sua aplicação lógica:

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- O pedido contém `Content-Type: application/json`a rubrica . 
- Para otimizar o desempenho, envie o pedido sincronicamente, se possível.

Clique no separador idioma/quadro preferido abaixo para ver um exemplo.

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

Em ASP.NET, pode enviar o post HTTP com a classe [System.Net.Http.HttpClient.](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) Por exemplo:

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

Se estiver a testar este código na aplicação de amostras para [tutorial: Construa uma aplicação ASP.NET em Azure](app-service-web-tutorial-dotnet-sqldatabase.md)com `Todo` base de dados SQL, poderá usá-lo para enviar uma confirmação de e-mail na ação [Create](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63), depois de adicionado o item. Para utilizar o código assíncrono acima, converta a ação Create em assíncrona.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

Em ASP.NET Core, pode enviar o post HTTP com a classe [System.Net.Http.HttpClient.](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) Por exemplo:

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
    // Requires DI configuration to access app settings. See https://docs.microsoft.com/azure/app-service/containers/configure-language-dotnetcore#access-environment-variables
    _configuration["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

> [!NOTE]
> Este código está escrito para a simplicidade da demonstração. Na prática, não instantaneamente `HttpClient` um objeto para cada pedido. Siga as orientações na [Use IHttpClientFactory para implementar pedidos RESILIENTES HTTP](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

Se estiver a testar este código na aplicação de amostras para [tutorial: Construa uma aplicação de base de dados Core e SQL ASP.NET no Serviço de Aplicações Azure,](app-service-web-tutorial-dotnetcore-sqldb.md)poderá usá-lo para enviar uma confirmação por e-mail na [ação Create](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65), depois de adicionado o `Todo` item.

# <a name="nodejs"></a>[Node.js](#tab/node)

Node.js, pode enviar o post HTTP facilmente com um pacote npm como [axios](https://www.npmjs.com/package/axios). Por exemplo:

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

Se estiver a testar este código na aplicação de amostras para [tutorial: Construa uma aplicação Node.js e MongoDB em Azure,](app-service-web-tutorial-nodejs-mongodb-app.md)poderá usá-lo para enviar uma confirmação por e-mail na [função criar](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27), depois [de o artigo ser guardado com sucesso](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24).

# <a name="php"></a>[PHP](#tab/php)

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

Se estiver a testar este código na aplicação de amostras para [tutorial: Construa uma aplicação PHP e MySQL no Azure,](app-service-web-tutorial-php-mysql.md)poderá usá-lo para enviar uma confirmação de e-mail na [função Route::post](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48), pouco antes da declaração de devolução.

# <a name="python"></a>[Python](#tab/python)

Em Python, pode enviar facilmente o post HTTP com [pedidos](https://pypi.org/project/requests/). Por exemplo:

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

Se estiver a testar este código na aplicação de amostras para [tutorial: Executar uma aplicação web Python (Django) com postgreSQL no Serviço de Aplicações Azure,](containers/tutorial-python-postgresql-app.md)poderá usá-lo para enviar uma confirmação por e-mail na [função Route::post](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48), pouco antes da declaração de devolução.

# <a name="ruby"></a>[Ruby](#tab/ruby)

Em Ruby, pode enviar facilmente o post HTTP com [o JSONClient](https://www.rubydoc.info/gems/httpclient/JSONClient). Por exemplo:

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

Se estiver a testar este código na aplicação de amostras para [construir uma aplicação Ruby e Postgres no Azure App Service no Linux,](containers/tutorial-ruby-postgres-app.md)poderá usá-lo para enviar uma confirmação por e-mail na ação [de criação,](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38) [quando @task.save for bem sucedido](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30).

---

# <a name="more-resources"></a>Mais recursos

[Tutorial: Alojar uma API RESTful com CORS no Serviço de Aplicações do Azure](app-service-web-tutorial-rest-api.md)  
[HTTP pedido/referência de resposta para Aplicações Lógicas](../connectors/connectors-native-reqres.md)  
[Quickstart: Crie o seu primeiro fluxo de trabalho utilizando aplicações da Azure Logic - portal Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md)