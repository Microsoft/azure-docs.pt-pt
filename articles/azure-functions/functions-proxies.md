---
title: Trabalhar com os proxies de funções do Azure | Documentos da Microsoft
description: Descrição geral de como utilizar os Proxies de funções do Azure
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: 2fbf29385b9a14cf5d4a9df621f0767a32079587
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58094580"
---
# <a name="work-with-azure-functions-proxies"></a>Trabalhar com os Proxies de funções do Azure

Este artigo explica como configurar e trabalhar com os Proxies de funções do Azure. Com esta funcionalidade, pode especificar pontos finais na sua aplicação de funções que são implementados por outro recurso. Pode utilizar estes proxies para dividir uma API grande em várias aplicações de funções (como numa arquitetura de microsserviços), mas apresentando uma única superfície de API para os clientes.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> As funções padrão de faturação que se aplica a execuções de proxy. Para obter mais informações, consulte [preços de funções do Azure](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create"></a>Criar um proxy

Esta secção mostra-lhe como criar um proxy no portal de funções.

1. Abra o [portal do Azure]e, em seguida, vá para a aplicação de funções.
2. No painel esquerdo, selecione **novo proxy**.
3. Forneça um nome para o proxy.
4. Configurar o ponto final que é exposto nesta aplicação de função, especificando o **modelo de rota** e **métodos HTTP**. Se comportam estes parâmetros, de acordo com as regras para [acionadores HTTP].
5. Definir o **URL de back-end** para outro ponto final. Este ponto final pode ser uma função na outra aplicação de função, ou pode ser qualquer outra API. O valor não precisa de ser estáticos e pode referenciar [Definições da aplicação] e [parâmetros do pedido de cliente original].
6. Clique em **Criar**.

O proxy agora existe como um novo ponto final na sua aplicação de funções. Da perspectiva do cliente, é equivalente a um HttpTrigger nas funções do Azure. Pode experimentar o seu novo proxy ao copiar o URL de Proxy e testá-lo com o seu cliente favorito de HTTP.

## <a name="modify-requests-responses"></a>Modificar as solicitações e respostas

Com os Proxies de funções do Azure, pode modificar as solicitações e respostas de back-end. Essas transformações podem utilizar variáveis, conforme definido na [utilizar variáveis].

### <a name="modify-backend-request"></a>Modificar o pedido de back-end

Por predefinição, o pedido de back-end é inicializado como uma cópia do pedido original. Além de definir o URL de back-end, pode efetuar alterações para o método HTTP, cabeçalhos e os parâmetros de cadeia de caracteres de consulta. Podem referenciar os valores modificados [Definições da aplicação] e [parâmetros do pedido de cliente original].

Pedidos de back-end podem ser modificados no portal, expandindo a *substituição do pedido* secção da página de detalhes do proxy. 

### <a name="modify-response"></a>Modificar a resposta

Por predefinição, a resposta do cliente é inicializada como uma cópia da resposta de back-end. Pode efetuar alterações ao código de estado, frase da razão, cabeçalhos e corpo da resposta. Podem referenciar os valores modificados [Definições da aplicação], [parâmetros do pedido de cliente original], e [parâmetros da resposta de back-end].

Pedidos de back-end podem ser modificados no portal, expandindo a *substituição de resposta* secção da página de detalhes do proxy. 

## <a name="using-variables"></a>Utilizar variáveis

A configuração de um proxy não precisa de ser estáticos. Pode de condição-o para utilizar variáveis a partir do pedido do cliente original, a resposta de back-end ou as definições da aplicação.

### <a name="reference-localhost"></a>Funções de locais de referência
Pode usar `localhost` para fazer referência a uma função na mesma aplicação de função diretamente, sem um pedido de proxy de ida e volta.

`"backendurl": "https://localhost/api/httptriggerC#1"` fará referência uma função acionada por HTTP local na rota `/api/httptriggerC#1`

 
>[!Note]  
>Se utilizar a sua função *função, administrador ou sys* níveis de autorização, precisará fornecer o código e o ID de cliente, de acordo com o URL da função original. Neste caso a referência teria o seguinte aspeto: `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"`

### <a name="request-parameters"></a>Parâmetros do pedido de referência

Pode utilizar parâmetros do pedido como entradas para a propriedade de URL de back-end ou como parte de modificação de solicitações e respostas. Alguns parâmetros podem ser ligados a partir do modelo de rota que é especificado na configuração do proxy de base e outras pessoas podem vir de propriedades do pedido a receber.

#### <a name="route-template-parameters"></a>Parâmetros de modelo de rota
Parâmetros que são utilizados no modelo de rota estão disponíveis para ser referenciado por nome. Os nomes de parâmetro são colocados entre chavetas ({}).

Por exemplo, se um proxy, tem um modelo de rota, como `/pets/{petId}`, o URL de back-end pode incluir o valor de `{petId}`, como em `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Se o modelo de rota termina no caráter universal, por exemplo, `/api/{*restOfPath}`, o valor `{restOfPath}` é uma representação de cadeia de caracteres de segmentos de caminho restantes da solicitação recebida.

#### <a name="additional-request-parameters"></a>Parâmetros de pedido adicionais
Além de parâmetros de modelo de rota, os seguintes valores podem ser utilizados em valores de configuração:

* **{request.method}**: O método HTTP que é utilizado na solicitação original.
* **{request.headers.\<HeaderName\>}**: Um cabeçalho que pode ser lidos a partir do pedido original. Substitua *\<HeaderName\>* com o nome do cabeçalho que queira ler. Se o cabeçalho não está incluído na solicitação, o valor será a cadeia vazia.
* **{request.querystring.\<ParameterName\>}**: Um parâmetro de cadeia de caracteres de consulta que pode ser lidos a partir do pedido original. Substitua *\<ParameterName\>* com o nome do parâmetro que pretende ler. Se o parâmetro não está incluído na solicitação, o valor será a cadeia vazia.

### <a name="response-parameters"></a>Parâmetros de resposta de back-end de referência

Parâmetros de resposta podem ser utilizados como parte de modificar a resposta ao cliente. Os seguintes valores podem ser utilizados em valores de configuração:

* **{backend.response.statusCode}**: O código de estado HTTP devolvido na resposta de back-end.
* **{backend.response.statusReason}**: A frase de razão HTTP que é devolvida na resposta de back-end.
* **{backend.response.headers.\<HeaderName\>}**: Um cabeçalho que pode ser lidos da resposta de back-end. Substitua *\<HeaderName\>* com o nome do cabeçalho que pretende ler. Se o cabeçalho não está incluído na resposta, o valor será a cadeia vazia.

### <a name="use-appsettings"></a>Definições da aplicação de referência

Também pode fazer referência [as definições da aplicação definidas para a aplicação de funções](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) rodeando o nome da definição com símbolos de percentagem (%).

Por exemplo, um URL de back-end de *https://%ORDER_PROCESSING_HOST%/api/orders* teria "ORDER_PROCESSING_HOST %" substituída pelo valor da definição ORDER_PROCESSING_HOST.

> [!TIP] 
> Utilize as definições da aplicação para os anfitriões de back-end se tiver várias implementações ou ambientes de teste. Dessa forma, pode tornar-se de que estamos a falar sempre para o back-end certo para esse ambiente.

## <a name="debugProxies"></a>Resolver problemas de Proxies

Ao adicionar o sinalizador `"debug":true` para qualquer proxy na sua `proxies.json` irá ativar o registo de depuração. Os registos são armazenados em `D:\home\LogFiles\Application\Proxies\DetailedTrace` e acessível através das ferramentas avançadas (kudu). As respostas HTTP também conterá uma `Proxy-Trace-Location` cabeçalho com um URL para aceder ao ficheiro de registo.

Pode depurar um proxy do lado do cliente ao adicionar um `Proxy-Trace-Enabled` cabeçalho definido como `true`. Isto também um rastreio de registo para o sistema de ficheiros e retornasse a URL de rastreio como um cabeçalho na resposta.

### <a name="block-proxy-traces"></a>Rastreios de proxy do bloco

Por motivos de segurança poderá não permitir que qualquer pessoa que chamar o seu serviço para gerar um rastreamento. Não poderão acessar o conteúdo de rastreio sem as suas credenciais de início de sessão, mas a gerar o rastreio consome recursos e expõe o que está a utilizar Proxies de funções.

Desativar completamente os rastreios adicionando `"debug":false` para qualquer proxy específico na sua `proxies.json`.

## <a name="advanced-configuration"></a>Configuração avançada

Os proxies que configurar são armazenados num *proxies* arquivo, o que está localizado na raiz de um diretório de aplicação de função. Pode editar este ficheiro manualmente e implementá-lo como parte da sua aplicação ao utilizar qualquer um da [métodos de implantação](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) que suporta as funções. 

> [!TIP] 
> Se não tiver definido um dos métodos de implementação, também pode trabalhar com o *proxies* ficheiro no portal. Aceda à sua aplicação de função, selecione **funcionalidades de plataforma**e, em seguida, selecione **Editor do serviço de aplicações**. Ao fazer isso, pode ver a estrutura de arquivo inteiro da sua aplicação de função e, em seguida, fazer alterações.

*Proxies* é definido por um objeto de proxies, que é composto por proxies nomeados e as respetivas definições. Opcionalmente, se o seu editor de suportar, pode referenciar um [esquema JSON](http://json.schemastore.org/proxies) pela conclusão de código. Um exemplo de arquivo pode ter o seguinte aspeto:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Cada proxy tem um nome amigável, como *proxy1* no exemplo anterior. O objeto de definição de proxy correspondente é definido pelas seguintes propriedades:

* **matchCondition**: É necessário – um objeto definindo os pedidos que acionam a execução desse proxy. Ele contém duas propriedades que são partilhadas com [acionadores HTTP]:
    * _métodos_: Uma matriz dos métodos HTTP que o proxy responde a. Se não for especificado, o proxy responde a todos os métodos HTTP na rota.
    * _rota_: Necessário – define o modelo de rota, controlar qual solicitação URLs seu proxy responde a. Ao contrário de acionadores HTTP, não existe nenhum valor predefinido.
* **backendUri**: O URL do recurso de back-end para o qual o pedido deve ser transmitidas por proxy. Este valor pode referenciar as definições da aplicação e os parâmetros do pedido de cliente original. Se esta propriedade não está incluída, as funções do Azure responde com um HTTP 200 OK.
* **requestOverrides**: Um objeto que define as transformações para o pedido de back-end. Ver [definir um objeto de requestOverrides].
* **responseOverrides**: Um objeto que define as transformações de resposta do cliente. Ver [definir um objeto de responseOverrides].

> [!NOTE] 
> O *rota* propriedade nos Proxies de funções do Azure não cumpra o *routePrefix* propriedade da configuração do anfitrião de aplicação de funções. Se quiser incluir um prefixo, como `/api`, tem de ser incluído nos *rota* propriedade.

### <a name="disableProxies"></a> Desativar os proxies individuais

Pode desativar os proxies individuais, adicionando `"disabled": true` para o proxy no `proxies.json` ficheiro. Isso fará com que quaisquer pedidos a matchCondition de reunião devolver 404.
```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "Root": {
            "disabled":true,
            "matchCondition": {
                "route": "/example"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

### <a name="applicationSettings"></a> Definições da aplicação

O comportamento de proxy pode ser controlado por várias configurações de aplicação. Todos estão descritas no [referência das definições de aplicação de funções](./functions-app-settings.md)

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reservedChars"></a> Carateres reservados (cadeia de caracteres de formatação)

Proxies ler todas as cadeias de caracteres fora de um JSON de ficheiros, usando \ como um símbolo de escape. Os proxies também interpretam chavetas. Ver um conjunto completo de exemplos abaixo.

|Caráter|Caráter de escape|Exemplo|
|-|-|-|
|{ou}|{{ou}}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="requestOverrides"></a>Definir um objeto de requestOverrides

O objeto de requestOverrides define as alterações feitas à solicitação quando o recurso de back-end é chamado. O objeto é definido pelas seguintes propriedades:

* **backend.request.method**: O método HTTP que é utilizado para chamar o back-end.
* **backend.request.querystring.\<ParameterName\>**: Um parâmetro de cadeia de caracteres de consulta que pode ser definido para a chamada para o back-end. Substitua *\<ParameterName\>* com o nome do parâmetro que pretende definir. Se não for fornecida a cadeia vazia, o parâmetro não está incluído no pedido de back-end.
* **backend.request.headers.\<HeaderName\>**: Um cabeçalho que pode ser definido para a chamada para o back-end. Substitua *\<HeaderName\>* com o nome do cabeçalho que queira definir. Se fornecer a cadeia vazia, o cabeçalho não está incluído no pedido de back-end.

Valores podem referenciar as definições da aplicação e os parâmetros do pedido de cliente original.

Um exemplo de configuração pode ter um aspeto semelhante ao seguinte:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="responseOverrides"></a>Definir um objeto de responseOverrides

O objeto de requestOverrides define as alterações efetuadas à resposta que é transmitida ao cliente. O objeto é definido pelas seguintes propriedades:

* **response.statusCode**: O código de estado HTTP a ser devolvida ao cliente.
* **response.statusReason**: A frase de razão HTTP a ser devolvida ao cliente.
* **response.body**: A representação de cadeia de caracteres do corpo a ser devolvida ao cliente.
* **response.headers.\<HeaderName\>**: Um cabeçalho que pode ser definido para a resposta ao cliente. Substitua *\<HeaderName\>* com o nome do cabeçalho que queira definir. Se fornecer a cadeia vazia, o cabeçalho não está incluído na resposta.

Valores podem referenciar as definições da aplicação, parâmetros de pedido do cliente original e os parâmetros da resposta de back-end.

Um exemplo de configuração pode ter um aspeto semelhante ao seguinte:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> Neste exemplo, o corpo da resposta é definido diretamente, por isso, não `backendUri` propriedade é necessária. O exemplo mostra como pode utilizar os Proxies de funções do Azure para a simulação de APIs.

[Portal do Azure]: https://portal.azure.com
[Acionadores HTTP]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Definir um objeto de requestOverrides]: #requestOverrides
[Definir um objeto de responseOverrides]: #responseOverrides
[Definições da aplicação]: #use-appsettings
[Utilizar variáveis]: #using-variables
[parâmetros do pedido de cliente original]: #request-parameters
[parâmetros da resposta de back-end]: #response-parameters
