---
title: Trabalhar com proxies no Azure Functions
description: Descrição geral de como utilizar os Proxies de funções do Azure
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: dffdffdfa80d940c4a50d0a6630c665164f24d5c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230462"
---
# <a name="work-with-azure-functions-proxies"></a>Trabalhar com os Proxies de funções do Azure

Este artigo explica como configurar e trabalhar com os Proxies de funções do Azure. Com esta funcionalidade, pode especificar pontos finais na sua aplicação de funções que são implementados por outro recurso. Pode utilizar estes proxies para dividir uma API grande em várias aplicações de funções (como numa arquitetura de microsserviços), mas apresentando uma única superfície de API para os clientes.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> As funções padrão de faturação que se aplica a execuções de proxy. Para obter mais informações, consulte [preços de Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create"></a>Criar um proxy

Esta secção mostra-lhe como criar um proxy no portal de funções.

1. Abra o [portal do Azure]e, em seguida, vá para seu aplicativo de funções.
2. No painel esquerdo, selecione **novo proxy**.
3. Forneça um nome para o proxy.
4. Configure o ponto de extremidade exposto neste aplicativo de funções especificando o **modelo de rota** e os **métodos http**. Esses parâmetros se comportam de acordo com as regras para [gatilhos http].
5. Defina a **URL de back-end** para outro ponto de extremidade. Este ponto final pode ser uma função na outra aplicação de função, ou pode ser qualquer outra API. O valor não precisa ser estático e pode referenciar [configurações de aplicativo] e [os parâmetros da solicitação original do cliente].
6. Clique em **Criar**.

O proxy agora existe como um novo ponto final na sua aplicação de funções. Da perspectiva do cliente, é equivalente a um HttpTrigger nas funções do Azure. Pode experimentar o seu novo proxy ao copiar o URL de Proxy e testá-lo com o seu cliente favorito de HTTP.

## <a name="modify-requests-responses"></a>Modificar solicitações e respostas

Com os Proxies de funções do Azure, pode modificar as solicitações e respostas de back-end. Essas transformações podem usar variáveis conforme definido em [Usar variáveis].

### <a name="modify-backend-request"></a>Modificar a solicitação de back-end

Por predefinição, o pedido de back-end é inicializado como uma cópia do pedido original. Além de definir o URL de back-end, pode efetuar alterações para o método HTTP, cabeçalhos e os parâmetros de cadeia de caracteres de consulta. Os valores modificados podem referenciar [configurações de aplicativo] e [os parâmetros da solicitação original do cliente].

As solicitações de back-end podem ser modificadas no portal expandindo a seção de *substituição de solicitação* da página de detalhes do proxy. 

### <a name="modify-response"></a>Modificar a resposta

Por predefinição, a resposta do cliente é inicializada como uma cópia da resposta de back-end. Pode efetuar alterações ao código de estado, frase da razão, cabeçalhos e corpo da resposta. Os valores modificados podem referenciar [configurações de aplicativo], [os parâmetros da solicitação original do cliente]e [os parâmetros da resposta de back-end].

As solicitações de back-end podem ser modificadas no portal expandindo a seção de *substituição de resposta* da página de detalhes do proxy. 

## <a name="using-variables"></a>Usar variáveis

A configuração de um proxy não precisa de ser estáticos. Pode de condição-o para utilizar variáveis a partir do pedido do cliente original, a resposta de back-end ou as definições da aplicação.

### <a name="reference-localhost"></a>Funções locais de referência
Você pode usar `localhost` para fazer referência a uma função dentro do mesmo aplicativo de funções diretamente, sem uma solicitação de proxy de ida e volta.

`"backendurl": "https://localhost/api/httptriggerC#1"` fará referência a uma função disparada por HTTP local na rota `/api/httptriggerC#1`

 
>[!Note]  
>Se sua função usar os níveis de autorização *Function, admin ou sys* , você precisará fornecer o código e clientId, de acordo com a URL da função original. Nesse caso, a referência ficaria assim: `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` é recomendável armazenar essas chaves nas [configurações de aplicativo] e fazer referência a elas em seus proxies. Isso evita o armazenamento de segredos em seu código-fonte. 

### <a name="request-parameters"></a>Parâmetros de solicitação de referência

Pode utilizar parâmetros do pedido como entradas para a propriedade de URL de back-end ou como parte de modificação de solicitações e respostas. Alguns parâmetros podem ser ligados a partir do modelo de rota que é especificado na configuração do proxy de base e outras pessoas podem vir de propriedades do pedido a receber.

#### <a name="route-template-parameters"></a>Parâmetros de modelo de rota
Parâmetros que são utilizados no modelo de rota estão disponíveis para ser referenciado por nome. Os nomes de parâmetro são colocados entre chaves ({}).

Por exemplo, se um proxy tiver um modelo de rota, como `/pets/{petId}`, a URL de back-end poderá incluir o valor de `{petId}`, como em `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Se o modelo de rota terminar em um curinga, como `/api/{*restOfPath}`, o valor `{restOfPath}` será uma representação de cadeia de caracteres dos segmentos de caminho restantes da solicitação de entrada.

#### <a name="additional-request-parameters"></a>Parâmetros de pedido adicionais
Além de parâmetros de modelo de rota, os seguintes valores podem ser utilizados em valores de configuração:

* **{Request. Method}** : o método HTTP usado na solicitação original.
* **{Request. Headers.\<headername\>}** : um cabeçalho que pode ser lido da solicitação original. Substitua *\<headername\>* pelo nome do cabeçalho que você deseja ler. Se o cabeçalho não está incluído na solicitação, o valor será a cadeia vazia.
* **{Request. QueryString.\<ParameterName\>}** : um parâmetro de cadeia de caracteres de consulta que pode ser lido da solicitação original. Substitua *\<ParameterName\>* pelo nome do parâmetro que você deseja ler. Se o parâmetro não está incluído na solicitação, o valor será a cadeia vazia.

### <a name="response-parameters"></a>Referenciar parâmetros de resposta de back-end

Parâmetros de resposta podem ser utilizados como parte de modificar a resposta ao cliente. Os seguintes valores podem ser utilizados em valores de configuração:

* **{back-end. Response. StatusCode}** : o código de status http retornado na resposta de back-end.
* **{back-end. Response. statusReason}** : a frase de motivo http retornada na resposta de back-end.
* **{backend. Response. Headers.\<headername\>}** : um cabeçalho que pode ser lido da resposta de back-end. Substitua *\<headername\>* pelo nome do cabeçalho que você deseja ler. Se o cabeçalho não está incluído na resposta, o valor será a cadeia vazia.

### <a name="use-appsettings"></a>Configurações do aplicativo de referência

Você também pode referenciar [as configurações do aplicativo definidas para o aplicativo de funções](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) ao redor do nome da configuração com sinais de porcentagem (%).

Por exemplo, uma URL de back-end de *https://%ORDER_PROCESSING_HOST%/api/orders* teria "% ORDER_PROCESSING_HOST%" substituída pelo valor da configuração de ORDER_PROCESSING_HOST.

> [!TIP] 
> Utilize as definições da aplicação para os anfitriões de back-end se tiver várias implementações ou ambientes de teste. Dessa forma, pode tornar-se de que estamos a falar sempre para o back-end certo para esse ambiente.

## <a name="debugProxies"></a>Solucionar problemas de proxies

Ao adicionar o sinalizador `"debug":true` a qualquer proxy no seu `proxies.json` você habilitará o log de depuração. Os logs são armazenados em `D:\home\LogFiles\Application\Proxies\DetailedTrace` e acessíveis por meio das ferramentas avançadas (kudu). Todas as respostas HTTP também conterão um cabeçalho `Proxy-Trace-Location` com uma URL para acessar o arquivo de log.

Você pode depurar um proxy do lado do cliente adicionando um cabeçalho de `Proxy-Trace-Enabled` definido como `true`. Isto também um rastreio de registo para o sistema de ficheiros e retornasse a URL de rastreio como um cabeçalho na resposta.

### <a name="block-proxy-traces"></a>Rastreios de proxy do bloco

Por motivos de segurança poderá não permitir que qualquer pessoa que chamar o seu serviço para gerar um rastreamento. Não poderão acessar o conteúdo de rastreio sem as suas credenciais de início de sessão, mas a gerar o rastreio consome recursos e expõe o que está a utilizar Proxies de funções.

Desabilite completamente os rastreamentos adicionando `"debug":false` a qualquer proxy específico em seu `proxies.json`.

## <a name="advanced-configuration"></a>Configuração avançada

Os proxies que você configura são armazenados em um arquivo *proxies. JSON* , que está localizado na raiz de um diretório de aplicativo de funções. Você pode editar manualmente esse arquivo e implantá-lo como parte de seu aplicativo ao usar qualquer um dos [métodos de implantação](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) com suporte do functions. 

> [!TIP] 
> Se você não tiver configurado um dos métodos de implantação, também poderá trabalhar com o arquivo *proxies. JSON* no Portal. Vá para seu aplicativo de funções, selecione **recursos de plataforma**e, em seguida, selecione **Editor do serviço de aplicativo**. Ao fazer isso, pode ver a estrutura de arquivo inteiro da sua aplicação de função e, em seguida, fazer alterações.

*Proxies. JSON* é definido por um objeto de proxies, que é composto de proxies nomeados e suas definições. Opcionalmente, se o seu editor oferecer suporte a ele, você poderá fazer referência a um [esquema JSON](http://json.schemastore.org/proxies) para a conclusão de código. Um exemplo de arquivo pode ter o seguinte aspeto:

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

Cada proxy tem um nome amigável, como *Proxy1* no exemplo anterior. O objeto de definição de proxy correspondente é definido pelas seguintes propriedades:

* **matchCondition**: obrigatório – um objeto que define as solicitações que disparam a execução desse proxy. Ele contém duas propriedades que são compartilhadas com [gatilhos http]:
    * _métodos_: uma matriz dos métodos http aos quais o proxy responde. Se não for especificado, o proxy responde a todos os métodos HTTP na rota.
    * _rota_: obrigatório – define o modelo de rota, controlando a quais URLs de solicitação seu proxy responde. Ao contrário de acionadores HTTP, não existe nenhum valor predefinido.
* **backendUri**: a URL do recurso de back-end para o qual a solicitação deve ser proxy. Este valor pode referenciar as definições da aplicação e os parâmetros do pedido de cliente original. Se esta propriedade não está incluída, as funções do Azure responde com um HTTP 200 OK.
* **requestOverrides**: um objeto que define as transformações para a solicitação de back-end. Consulte [definir um objeto requestOverrides].
* **responseOverrides**: um objeto que define as transformações para a resposta do cliente. Consulte [definir um objeto responseOverrides].

> [!NOTE] 
> A propriedade *Route* em proxies do Azure Functions não respeita a propriedade *routePrefix* da configuração de host aplicativo de funções. Se você quiser incluir um prefixo, como `/api`, ele deve ser incluído na propriedade *Route* .

### <a name="disableProxies"></a>Desabilitar proxies individuais

Você pode desabilitar proxies individuais adicionando `"disabled": true` ao proxy no arquivo `proxies.json`. Isso fará com que as solicitações que atendem ao matchCondition retornem 404.
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

### <a name="applicationSettings"></a>Configurações do aplicativo

O comportamento de proxy pode ser controlado por várias configurações de aplicação. Eles são todos descritos na [referência de configurações do aplicativo do Functions](./functions-app-settings.md)

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reservedChars"></a>Caracteres reservados (formatação de cadeia de caracteres)

Os proxies lêem todas as cadeias de caracteres de um arquivo JSON, usando \ como um símbolo de escape. Os proxies também interpretam chaves. Veja um conjunto completo de exemplos abaixo.

|Caráter|Caráter de escape|Exemplo|
|-|-|-|
|{ou}|{{ou}}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="requestOverrides"></a>Definir um objeto requestOverrides

O objeto de requestOverrides define as alterações feitas à solicitação quando o recurso de back-end é chamado. O objeto é definido pelas seguintes propriedades:

* **back-end. Request. Method**: o método http que é usado para chamar o back-end.
* **back-end. Request. QueryString.\<ParameterName\>** : um parâmetro de cadeia de caracteres de consulta que pode ser definido para a chamada para o back-end. Substitua *\<ParameterName\>* pelo nome do parâmetro que você deseja definir. Se não for fornecida a cadeia vazia, o parâmetro não está incluído no pedido de back-end.
* **back-end. Request. Headers.\<headername\>** : um cabeçalho que pode ser definido para a chamada para o back-end. Substitua *\<headername\>* pelo nome do cabeçalho que você deseja definir. Se fornecer a cadeia vazia, o cabeçalho não está incluído no pedido de back-end.

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

### <a name="responseOverrides"></a>Definir um objeto responseOverrides

O objeto de requestOverrides define as alterações efetuadas à resposta que é transmitida ao cliente. O objeto é definido pelas seguintes propriedades:

* **Response. StatusCode**: o código de status HTTP a ser retornado ao cliente.
* **Response. statusReason**: a frase de motivo http a ser retornada ao cliente.
* **Response. Body**: a representação da cadeia de caracteres do corpo a ser retornado ao cliente.
* **Response. Headers.\<headername\>** : um cabeçalho que pode ser definido para a resposta ao cliente. Substitua *\<headername\>* pelo nome do cabeçalho que você deseja definir. Se fornecer a cadeia vazia, o cabeçalho não está incluído na resposta.

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
> Neste exemplo, o corpo da resposta é definido diretamente, portanto, nenhuma propriedade de `backendUri` é necessária. O exemplo mostra como pode utilizar os Proxies de funções do Azure para a simulação de APIs.

[Portal do Azure]: https://portal.azure.com
[Gatilhos HTTP]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Definir um objeto requestOverrides]: #requestOverrides
[Definir um objeto responseOverrides]: #responseOverrides
[configurações de aplicativo]: #use-appsettings
[Usar variáveis]: #using-variables
[os parâmetros da solicitação original do cliente]: #request-parameters
[os parâmetros da resposta de back-end]: #response-parameters
