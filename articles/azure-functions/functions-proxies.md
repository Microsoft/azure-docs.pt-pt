---
title: Trabalhar com proxies em Funções Azure
description: Visão geral de como usar Proxies de Funções Azure
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: fb263239f99bcb4ec4c893b700d5c1cce078659f
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601378"
---
# <a name="work-with-azure-functions-proxies"></a>Trabalhar com proxies de funções Azure

Este artigo explica como configurar e trabalhar com proxies de funções Azure. Com esta funcionalidade, pode especificar pontos finais na sua aplicação de função que são implementados por outro recurso. Você pode usar estes proxies para quebrar uma grande API em aplicações de múltiplas funções (como em uma arquitetura de microserviço), enquanto ainda apresenta uma única superfície API para os clientes.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> A faturação padrão das funções aplica-se a execuções por procuração. Para obter mais informações, consulte [os preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create-a-proxy"></a><a name="create"></a>Criar um representante

Esta secção mostra-lhe como criar um proxy no portal Funções.

1. Abra o [portal Azure]e, em seguida, vá para a sua aplicação de função.
2. No painel esquerdo, selecione **Novo representante**.
3. Forneça um nome para o seu representante.
4. Configure o ponto final que está exposto nesta aplicação de função especificando o modelo de **rota** e os **métodos HTTP**. Estes parâmetros comportam-se de acordo com as regras dos [gatilhos HTTP].
5. Desaponte o **URL de backend** para outro ponto final. Este ponto final pode ser uma função em outra aplicação de função, ou pode ser qualquer outra API. O valor não precisa de ser estático, podendo referenciar [as definições] e parâmetros da aplicação [a partir do pedido original do cliente.]
6. Clique em **Criar**.

O seu representante existe agora como um novo ponto final na sua aplicação de função. Do ponto de vista do cliente, é equivalente a um HttpTrigger em Funções Azure. Pode experimentar o seu novo representante copiando o URL proxy e testando-o com o seu cliente HTTP favorito.

## <a name="modify-requests-and-responses"></a><a name="modify-requests-responses"></a>Modificar pedidos e respostas

Com Azure Functions Proxies, pode modificar pedidos e respostas a partir do back-end. Estas transformações podem utilizar variáveis como definidas nas [variáveis de utilização.]

### <a name="modify-the-back-end-request"></a><a name="modify-backend-request"></a>Modifique o pedido de back-end

Por predefinição, o pedido de back-end é inicializado como uma cópia do pedido original. Além de configurar o URL de back-end, pode esboar alterações ao método HTTP, cabeçalhos e parâmetros de cadeia de consulta. Os valores modificados podem referenciar [as configurações] e parâmetros da aplicação [a partir do pedido original do cliente.]

Os pedidos de back-end podem ser modificados no portal, expandindo a secção de substituição de *pedidos* da página de detalhes do proxy. 

### <a name="modify-the-response"></a><a name="modify-response"></a>Modifique a resposta

Por predefinição, a resposta do cliente é inicializada como uma cópia da resposta de back-end. Pode fazer alterações ao código de estado da resposta, frase de razão, cabeçalhos e corpo. Os valores modificados podem referenciar as definições de [aplicações,] [parâmetros do pedido original do cliente,]e [parâmetros da resposta de back-end].

Os pedidos de back-end podem ser modificados no portal, expandindo a secção de *sobreposição* de resposta da página de detalhes do proxy. 

## <a name="use-variables"></a><a name="using-variables"></a>Utilizar variáveis

A configuração para um proxy não precisa de ser estática. Pode condicioná-lo a utilizar variáveis a partir do pedido original do cliente, da resposta de back-end ou das definições de aplicação.

### <a name="reference-local-functions"></a><a name="reference-localhost"></a>Funções locais de referência
Pode utilizar `localhost` para fazer referência a uma função dentro da mesma aplicação de função diretamente, sem um pedido de procuração de ida e volta.

`"backendUri": "https://localhost/api/httptriggerC#1"` irá referenciar uma função local HTTP desencadeada na rota `/api/httptriggerC#1`

 
>[!Note]  
>Se a sua função utilizar os níveis de autorização *de função, administração ou sys,* terá de fornecer o código e o clientId, de acordo com o URL de função original. Neste caso, a referência seria semelhante: `"backendUri": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` Recomendamos que guarde estas chaves nas [definições] de aplicação e as referências às dos seus proxies. Isto evita guardar segredos no seu código fonte. 

### <a name="reference-request-parameters"></a><a name="request-parameters"></a>Parâmetros de pedido de referência

Pode utilizar parâmetros de pedido como entradas para a propriedade URL de back-end ou como parte da modificação de pedidos e respostas. Alguns parâmetros podem ser ligados do modelo de rota que é especificado na configuração de procuração base, e outros podem vir de propriedades do pedido de entrada.

#### <a name="route-template-parameters"></a>Parâmetros do modelo de rota
Os parâmetros que são usados no modelo de rota estão disponíveis para serem referenciados pelo nome. Os nomes dos parâmetros são incluídos em aparelhos {} ().

Por exemplo, se um proxy tiver um modelo de rota, `/pets/{petId}` como, por exemplo, o URL de back-end pode incluir o valor de `{petId}` , como em `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}` . Se o modelo de rota terminar num wildcard, tal `/api/{*restOfPath}` como, o valor `{restOfPath}` é uma representação de cadeia dos segmentos de caminho restantes a partir do pedido de entrada.

#### <a name="additional-request-parameters"></a>Parâmetros de pedido adicionais
Além dos parâmetros do modelo de rota, os seguintes valores podem ser usados em valores config:

* **{request.method}**: O método HTTP utilizado no pedido original.
* **{request.headers. \<HeaderName\> }**: Um cabeçalho que pode ser lido a partir do pedido original. *\<HeaderName\>* Substitua-o pelo nome do cabeçalho que pretende ler. Se o cabeçalho não estiver incluído no pedido, o valor será a corda vazia.
* **{request.consulta. \<ParameterName\> }**: Um parâmetro de cadeia de consulta que pode ser lido a partir do pedido original. *\<ParameterName\>* Substitua-o pelo nome do parâmetro que pretende ler. Se o parâmetro não estiver incluído no pedido, o valor será a cadeia vazia.

### <a name="reference-back-end-response-parameters"></a><a name="response-parameters"></a>Parâmetros de resposta de back-end de referência

Os parâmetros de resposta podem ser usados como parte da modificação da resposta ao cliente. Os seguintes valores podem ser utilizados em valores config:

* **{backend.response.statusCode}**: O código de estado HTTP que é devolvido na resposta de back-end.
* **{backend.response.statusReason}**: A frase http reason que é devolvida na resposta de back-end.
* **{backend.response.headers. \<HeaderName\> }**: Um cabeçalho que pode ser lido a partir da resposta de fundo. *\<HeaderName\>* Substitua-o pelo nome do cabeçalho que pretende ler. Se o cabeçalho não estiver incluído na resposta, o valor será a corda vazia.

### <a name="reference-application-settings"></a><a name="use-appsettings"></a>Definições de pedidos de referência

Também pode referenciar [as definições de aplicação definidas para a aplicação de função,](./functions-how-to-use-azure-function-app-settings.md) rodeando o nome de definição com sinais por cento (%).

Por exemplo, um URL back-end *https://%ORDER_PROCESSING_HOST%/api/orders* teria "%ORDER_PROCESSING_HOST%" substituído pelo valor da definição ORDER_PROCESSING_HOST.

> [!TIP] 
> Utilize as definições de aplicação para anfitriões de back-end quando tiver múltiplas implementações ou ambientes de teste. Assim, podes certificar-te de que estás sempre a falar com o lateral-direito para esse ambiente.

## <a name="troubleshoot-proxies"></a><a name="debugProxies"></a>Proxies de resolução de problemas

Ao adicionar a bandeira `"debug":true` a qualquer procuração no `proxies.json` seu, permitirá a depuragem de depurar. Os registos são armazenados `D:\home\LogFiles\Application\Proxies\DetailedTrace` e acessíveis através das ferramentas avançadas (kudu). Quaisquer respostas HTTP também conterão um `Proxy-Trace-Location` cabeçalho com um URL para aceder ao ficheiro de registo.

Pode depurar um proxy do lado do cliente adicionando um `Proxy-Trace-Enabled` conjunto de cabeçalho para `true` . Isto também registará um traço para o sistema de ficheiros, e devolverá o URL de traços como cabeçalho na resposta.

### <a name="block-proxy-traces"></a>Bloco de procuração vestígios

Por razões de segurança, pode não querer permitir que ninguém que chame o seu serviço para gerar vestígios. Não conseguirão aceder ao conteúdo do rastreio sem as suas credenciais de login, mas gerar o rastreio consome recursos e expõe que está a utilizar Os Proxies de Função.

Desative completamente os vestígios adicionando `"debug":false` a qualquer procuração em particular no seu `proxies.json` .

## <a name="advanced-configuration"></a>Configuração avançada

Os proxies que configura são armazenados num *proxies.jsem* ficheiro, que está localizado na raiz de um diretório de aplicações de funções. Pode editar manualmente este ficheiro e implantá-lo como parte da sua aplicação quando utilizar qualquer um dos métodos de [implementação](./functions-continuous-deployment.md) que o Functions suporta. 

> [!TIP] 
> Se não tiver configurado um dos métodos de implantação, também pode trabalhar com o *proxies.jsficheiro no* portal. Aceda à sua aplicação de função, selecione **funcionalidades da Plataforma** e, em seguida, selecione **App Service Editor**. Ao fazê-lo, pode visualizar toda a estrutura de ficheiros da sua aplicação de função e, em seguida, fazer alterações.

*Proxies.jsé* definido por um objeto proxies, que é composto por proxies nomeados e suas definições. Opcionalmente, se o seu editor o apoiar, pode fazer referência a um [esquema JSON](http://json.schemastore.org/proxies) para a conclusão do código. Um ficheiro de exemplo pode parecer o seguinte:

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

Cada procuração tem um nome amigável, como *proxy1* no exemplo anterior. O objeto de definição de procuração correspondente é definido pelas seguintes propriedades:

* **matchCondition**: Required -- um objeto que define os pedidos que desencadeiam a execução deste representante. Contém duas propriedades que são partilhadas com [gatilhos HTTP]:
    * _métodos_: Uma matriz dos métodos HTTP a que o proxy responde. Se não for especificado, o proxy responde a todos os métodos HTTP na rota.
    * _rota_: Obrigatório -- define o modelo de rota, controlando a que solicitam URLs a que o seu proxy responde. Ao contrário dos disparadores HTTP, não existe valor predefinido.
* **backendUri**: O URL do recurso back-end ao qual o pedido deve ser proxied. Este valor pode referenciar configurações e parâmetros de aplicação a partir do pedido original do cliente. Se esta propriedade não estiver incluída, a Azure Functions responde com um HTTP 200 OK.
* **requestOverrides**: Um objeto que define transformações para o pedido de back-end. Ver [Definir um objeto de pedidoOverrides].
* **respostaSOverrides**: Um objeto que define transformações para a resposta do cliente. Ver [Definir um objeto respostaOverrides].

> [!NOTE] 
> A propriedade *de rota* em Azure Functions Proxies não honra a propriedade *de rotaPrefix* da configuração do anfitrião da App de Função. Se quiser incluir um prefixo `/api` como, deve ser incluído na propriedade da *rota.*

### <a name="disable-individual-proxies"></a><a name="disableProxies"></a> Desativar os proxies individuais

Pode desativar os proxies individuais adicionando `"disabled": true` ao representante no `proxies.json` ficheiro. Isto fará com que quaisquer pedidos que cumpram a partidaCondição de retornar 404.
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

### <a name="application-settings"></a><a name="applicationSettings"></a> Definições de aplicação

O comportamento por procuração pode ser controlado por várias configurações de aplicações. Todos eles estão delineados na referência de Definições de [Aplicações de Funções](./functions-app-settings.md)

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reserved-characters-string-formatting"></a><a name="reservedChars"></a> Caracteres Reservados (formatação de cordas)

Os proxies lêem todas as cordas de um ficheiro JSON, utilizando \ como símbolo de fuga. Os proxies também interpretam aparelhos encaracolados. Veja um conjunto completo de exemplos abaixo.

|Caráter|Personagem escapado|Exemplo|
|-|-|-|
|{ ou }|{{ ou }}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="define-a-requestoverrides-object"></a><a name="requestOverrides"></a>Definir um objeto de pedidoOverrides

O objeto requestOverrides define as alterações feitas ao pedido quando o recurso back-end é chamado. O objeto é definido pelas seguintes propriedades:

* **backend.request.method**: O método HTTP que é usado para chamar de back-end.
* **backend.request.consulta. \<ParameterName\>**: Um parâmetro de cadeia de consulta que pode ser definido para a chamada para a extremidade traseira. *\<ParameterName\>* Substitua-o pelo nome do parâmetro que pretende definir. Note que se for fornecida uma corda vazia, o parâmetro ainda está incluído no pedido de back-end.
* **backend.request.headers. \<HeaderName\>**: Um cabeçalho que pode ser definido para a chamada para a parte de trás. *\<HeaderName\>* Substitua-o pelo nome do cabeçalho que pretende definir. Note que se for fornecida uma corda vazia, o parâmetro ainda está incluído no pedido de back-end.

Os valores podem referenciar as definições e parâmetros da aplicação original a partir do pedido original do cliente.

Uma configuração de exemplo pode parecer o seguinte:

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

### <a name="define-a-responseoverrides-object"></a><a name="responseOverrides"></a>Definir um objeto respostaOverrides

O objeto requestOverrides define as alterações que são feitas à resposta que é transmitida de volta ao cliente. O objeto é definido pelas seguintes propriedades:

* **resposta.statusCode**: O código de estado HTTP a ser devolvido ao cliente.
* **response.statusReason**: A frase http reason to retorna ao cliente.
* **resposta.corpo**: A representação da corda do corpo a ser devolvida ao cliente.
* **resposta.cabeçalhos. \<HeaderName\>**: Um cabeçalho que pode ser definido para a resposta ao cliente. *\<HeaderName\>* Substitua-o pelo nome do cabeçalho que pretende definir. Se fornecer a corda vazia, o cabeçalho não está incluído na resposta.

Os valores podem referenciar as definições de aplicações, os parâmetros do pedido original do cliente e os parâmetros da resposta de back-end.

Uma configuração de exemplo pode parecer o seguinte:

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
> Neste exemplo, o corpo de resposta é definido diretamente, pelo que não `backendUri` é necessária nenhuma propriedade. O exemplo mostra como pode usar Proxies de Funções Azure para ridicularizar APIs.

[Portal do Azure]: https://portal.azure.com
[Disparadores HTTP]: ./functions-bindings-http-webhook.md
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Definir um objeto de pedidoOverrides]: #requestOverrides
[Definir um objeto respostaOverrides]: #responseOverrides
[definições de aplicações]: #use-appsettings
[Utilizar variáveis]: #using-variables
[parâmetros do pedido original do cliente]: #request-parameters
[parâmetros da resposta back-end]: #response-parameters
