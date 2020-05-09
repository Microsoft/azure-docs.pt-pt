---
title: Trabalhar com proxies em Funções Azure
description: Visão geral de como usar proxies de funções azure
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: 5e756258bb92d7def195959d909068e87e765c0f
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562071"
---
# <a name="work-with-azure-functions-proxies"></a>Trabalho com proxies de funções azure

Este artigo explica como configurar e trabalhar com proxies de funções Azure. Com esta funcionalidade, pode especificar pontos finais na sua aplicação de função que são implementados por outro recurso. Você pode usar estes proxies para quebrar uma grande API em aplicações de múltiplas funções (como numa arquitetura de microserviço), enquanto ainda apresenta uma única superfície DeAPi para os clientes.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> A faturação de Funções Padrão aplica-se às execuções por procuração. Para mais informações, consulte o preço das [Funções Azure](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create-a-proxy"></a><a name="create"></a>Criar um proxy

Esta secção mostra-lhe como criar um proxy no portal Funções.

1. Abra o [portal Azure]e depois vá para a sua aplicação de funções.
2. No painel esquerdo, selecione **Novo proxy**.
3. Forneça um nome para o seu representante.
4. Configure o ponto final exposto nesta aplicação de função especificando o modelo de **rota** e **os métodos HTTP**. Estes parâmetros comportam-se de acordo com as regras relativas aos [gatilhos HTTP].
5. Coloque o **URL de backend** noutro ponto final. Este ponto final pode ser uma função em outra aplicação de função, ou pode ser qualquer outra API. O valor não precisa de ser estático, podendo referenciar [as definições] e [parâmetros da aplicação a partir do pedido original do cliente.]
6. Clique em **Criar**.

O seu representante existe agora como um novo ponto final na sua aplicação de funções. Do ponto de vista do cliente, é equivalente a um HttpTrigger em Funções Azure. Pode experimentar o seu novo representante copiando o URL proxy e testando-o com o seu cliente HTTP favorito.

## <a name="modify-requests-and-responses"></a><a name="modify-requests-responses"></a>Modificar pedidos e respostas

Com as Funções Azure Proxies, pode modificar pedidos e respostas a partir do back-end. Estas transformações podem usar variáveis como definidas nas [variáveis de utilização.]

### <a name="modify-the-back-end-request"></a><a name="modify-backend-request"></a>Modificar o pedido de back-end

Por predefinição, o pedido de back-end é inicializado como uma cópia do pedido original. Além de definir o URL de back-end, pode fazer alterações no método HTTP, cabeçalhos e parâmetros de corda de consulta. Os valores modificados podem referenciar [as definições] e parâmetros de aplicação [a partir do pedido original do cliente.]

Os pedidos de back-end podem ser modificados no portal, expandindo a secção de *sobreposição* de pedidos da página de detalhes de procuração. 

### <a name="modify-the-response"></a><a name="modify-response"></a>Modificar a resposta

Por predefinição, a resposta do cliente é inicializada como uma cópia da resposta de back-end. Pode fazer alterações no código de estado da resposta, na frase da razão, nos cabeçalhos e no corpo. Os valores modificados podem alterar as definições de [aplicações,] [parâmetros do pedido original do cliente,]e [parâmetros da resposta de back-end].

Os pedidos de back-end podem ser modificados no portal, expandindo a secção de *substituição* de resposta da página de detalhes proxy. 

## <a name="use-variables"></a><a name="using-variables"></a>Utilizar variáveis

A configuração para um proxy não precisa de ser estática. Pode condicionar a utilização de variáveis a partir do pedido original do cliente, da resposta de back-end ou das definições de aplicação.

### <a name="reference-local-functions"></a><a name="reference-localhost"></a>Funções locais de referência
Pode utilizar `localhost` para fazer referência a uma função dentro da mesma aplicação de função diretamente, sem um pedido de procuração de ida e volta.

`"backendurl": "https://localhost/api/httptriggerC#1"`irá referir uma função local HTTP desencadeada na rota`/api/httptriggerC#1`

 
>[!Note]  
>Se a sua função utilizar níveis de autorização de *função, administração ou sys,* terá de fornecer o código e o clientid, de acordo com o URL de função original. Neste caso, a referência `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` seria como: Recomendamos armazenar estas chaves nas definições de [aplicação] e referenciar as dos seus proxies. Isto evita guardar segredos no seu código fonte. 

### <a name="reference-request-parameters"></a><a name="request-parameters"></a>Parâmetros de pedido de referência

Pode utilizar os parâmetros de pedido como inputs para a propriedade DE URL de back-end ou como parte da modificação de pedidos e respostas. Alguns parâmetros podem ser vinculados a partir do modelo de rota especificado na configuração de procuração base, e outros podem vir de propriedades do pedido de entrada.

#### <a name="route-template-parameters"></a>Parâmetros do modelo de rota
Os parâmetros utilizados no modelo de rota estão disponíveis para serem referenciados pelo nome. Os nomes dos parâmetros estão fechados em aparelhos ({}).

Por exemplo, se um proxy tiver `/pets/{petId}`um modelo de rota, como, `{petId}`como, `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`o URL de back-end pode incluir o valor de , como em . Se o modelo de rota terminar `/api/{*restOfPath}`num wildcard, como, por exemplo, o valor `{restOfPath}` é uma representação de cordas dos restantes segmentos de caminho a partir do pedido de entrada.

#### <a name="additional-request-parameters"></a>Parâmetros de pedido adicionais
Além dos parâmetros do modelo de rota, os seguintes valores podem ser utilizados em valores de config:

* **{request.method}**: O método HTTP utilizado no pedido original.
* **{request.headers.\< HeaderName\>}**: Um cabeçalho que pode ser lido a partir do pedido original. Substitua * \<\> o HeaderName* pelo nome do cabeçalho que pretende ler. Se o cabeçalho não estiver incluído no pedido, o valor será a corda vazia.
* **{request.consultastring.\< Nome\>** do parâmetro } : Um parâmetro de corda de consulta que pode ser lido a partir do pedido original. Substitua * \<\> * o Nome do Parâmetro pelo nome do parâmetro que pretende ler. Se o parâmetro não estiver incluído no pedido, o valor será a corda vazia.

### <a name="reference-back-end-response-parameters"></a><a name="response-parameters"></a>Parâmetros de resposta back-end de referência

Os parâmetros de resposta podem ser usados como parte da modificação da resposta ao cliente. Os seguintes valores podem ser utilizados em valores de config:

* **{backend.response.statusCode}**: O código de estado HTTP que é devolvido na resposta de back-end.
* **{backend.response.statusReason}**: A frase da razão HTTP que é devolvida na resposta de back-end.
* **{backend.response.headers.\< HeaderName\>}**: Um cabeçalho que pode ser lido a partir da resposta traseira. Substitua * \<\> o HeaderName* pelo nome do cabeçalho que pretende ler. Se o cabeçalho não estiver incluído na resposta, o valor será a corda vazia.

### <a name="reference-application-settings"></a><a name="use-appsettings"></a>Definições de aplicação de referência

Também pode consultar as definições de [aplicação definidas para a aplicação de função,](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) rodeando o nome de definição com sinais por cento (%).

Por exemplo, um URL *https://%ORDER_PROCESSING_HOST%/api/orders* de back-end de teria "%ORDER_PROCESSING_HOST%" substituído pelo valor da definição de ORDER_PROCESSING_HOST.

> [!TIP] 
> Utilize as definições de aplicação para os anfitriões de back-end quando tiver múltiplas implementações ou ambientes de teste. Assim, podes certificar-te de que estás sempre a falar com o fundo direito para esse ambiente.

## <a name="troubleshoot-proxies"></a><a name="debugProxies"></a>Proxies de resolução de problemas

Ao adicionar `"debug":true` a bandeira a `proxies.json` qualquer procuração no seu, permitirá a depuração da exploração. Os registos são `D:\home\LogFiles\Application\Proxies\DetailedTrace` armazenados e acessíveis através das ferramentas avançadas (kudu). Quaisquer respostas HTTP `Proxy-Trace-Location` também conterão um cabeçalho com um URL para aceder ao ficheiro de registo.

Pode depurar um proxy do lado `Proxy-Trace-Enabled` do `true`cliente adicionando um conjunto de cabeçalho para . Isto também registará um vestígio no sistema de ficheiros e devolverá o URL do rastreio como um cabeçalho na resposta.

### <a name="block-proxy-traces"></a>Vestígios de procuração de blocos

Por razões de segurança, pode não querer permitir que alguém que chame o seu serviço gere vestígios. Não poderão aceder aos conteúdos dos vestígios sem as suas credenciais de login, mas gerar o vestígio consome recursos e expõe que está a usar proxies de função.

Desative completamente `"debug":false` os vestígios `proxies.json`adicionando a qualquer procuração em particular no seu .

## <a name="advanced-configuration"></a>Configuração avançada

Os proxies que configura são armazenados num ficheiro *proxies.json,* que está localizado na raiz de um diretório de aplicações de funções. Pode editar manualmente este ficheiro e implementá-lo como parte da sua aplicação quando utilizar qualquer um dos métodos de [implementação](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) que o Functions suporta. 

> [!TIP] 
> Se não tiver configurado um dos métodos de implementação, também pode trabalhar com o ficheiro *proxies.json* no portal. Vá à sua aplicação de função, selecione **funcionalidades da Plataforma,** e, em seguida, selecione **App Service Editor**. Ao fazê-lo, pode ver toda a estrutura de ficheiros da sua aplicação de função e, em seguida, fazer alterações.

*Proxies.json* é definido por um objeto de proxies, que é composto por proxies nomeados e suas definições. Opcionalmente, se o seu editor o apoiar, pode fazer referência a um [esquema JSON](http://json.schemastore.org/proxies) para a conclusão do código. Um ficheiro de exemplo pode parecer o seguinte:

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

* **matchCondition**: Necessário -- um objeto que define os pedidos que desencadeiam a execução deste representante. Contém duas propriedades que são partilhadas com [gatilhos HTTP:]
    * _métodos_: Uma série dos métodos HTTP a que o representante responde. Se não for especificado, o representante responde a todos os métodos HTTP na rota.
    * _rota_: Necessário-- define o modelo de rota, controlando quais os URLs que o seu representante responde. Ao contrário dos gatilhos HTTP, não há valor predefinido.
* **backendUri**: O URL do recurso back-end para o qual o pedido deve ser proxi. Este valor pode referenciar as definições e parâmetros de aplicação a partir do pedido original do cliente. Se esta propriedade não estiver incluída, a Azure Functions responde com um HTTP 200 OK.
* **solicitaçõesOverrides**: Um objeto que define transformações ao pedido de back-end. Ver [Definir um objeto de pedidoOverrides].
* **respostaOverrides**: Um objeto que define transformações na resposta do cliente. Ver [Definir um objeto de sobreposição]de respostas .

> [!NOTE] 
> A propriedade da *rota* em Azure Functions Proxies não honra a *propriedade routePrefix* da configuração do anfitrião da App de Função. Se quiser incluir um prefixo `/api`como, deve ser incluído na propriedade da *rota.*

### <a name="disable-individual-proxies"></a><a name="disableProxies"></a>Desativar proxies individuais

Pode desativar os `"disabled": true` proxies individuais adicionando ao representante no `proxies.json` ficheiro. Isto fará com que quaisquer pedidos que satisfaçam a matchCondition de volver 404.
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

### <a name="application-settings"></a><a name="applicationSettings"></a>Definições de aplicação

O comportamento proxy pode ser controlado por várias configurações da aplicação. Todos eles estão delineados na referência de Definições de Aplicações de [Funções](./functions-app-settings.md)

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reserved-characters-string-formatting"></a><a name="reservedChars"></a>Caracteres reservados (formatação de cordas)

Os proxies lêem todas as cordas de um ficheiro JSON, usando \ como símbolo de fuga. Os proxies também interpretam aparelhos encaracolados. Veja um conjunto completo de exemplos abaixo.

|Caráter|Personagem fugitivo|Exemplo|
|-|-|-|
|{ ou }|{{ ou }}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="define-a-requestoverrides-object"></a><a name="requestOverrides"></a>Defina um objeto de pedidoOverrides

O objeto requestOverrides define alterações feitas ao pedido quando o recurso de back-end é chamado. O objeto é definido pelas seguintes propriedades:

* **backend.request.method**: O método HTTP que é usado para chamar o back-end.
* **backend.request.consultastring. Nome do\>parâmetro : Um parâmetro de corda de consulta que pode ser definido para a chamada para a parte de trás. \<** Substitua * \<\> * o Nome do Parâmetro com o nome do parâmetro que pretende definir. Note que se for fornecida uma corda vazia, o parâmetro ainda está incluído no pedido de back-end.
* **backend.request.cabeçalhos. Nome \<do cabeçalho : Um cabeçalho que pode ser definido para a chamada para a parte de\>** trás. Substitua * \<\> o HeaderName* pelo nome do cabeçalho que pretende definir. Note que se for fornecida uma corda vazia, o parâmetro ainda está incluído no pedido de back-end.

Os valores podem referenciar as definições e parâmetros de aplicação a partir do pedido original do cliente.

Uma configuração de exemplo pode parecer a seguinte:

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

### <a name="define-a-responseoverrides-object"></a><a name="responseOverrides"></a>Defina um objeto de sobreposição de resposta

O objeto requestOverrides define alterações que são feitas para a resposta que é passada de volta para o cliente. O objeto é definido pelas seguintes propriedades:

* **resposta.statusCódigo**: O código de estado HTTP a devolver ao cliente.
* **resposta.statusReason**: A frase razão HTTP a ser devolvida ao cliente.
* **resposta.corpo**: A representação de cordas do corpo a ser devolvido ao cliente.
* **resposta.cabeçalhos. HeaderName\>: Um cabeçalho que pode ser definido para a resposta ao \<** cliente. Substitua * \<\> o HeaderName* pelo nome do cabeçalho que pretende definir. Se fornecer a corda vazia, o cabeçalho não está incluído na resposta.

Os valores podem referenciar as definições de aplicações, parâmetros do pedido original do cliente e parâmetros da resposta de back-end.

Uma configuração de exemplo pode parecer a seguinte:

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
> Neste exemplo, o corpo de resposta é `backendUri` definido diretamente, pelo que não é necessária nenhuma propriedade. O exemplo mostra como você pode usar proxies de funções Azure para ridicularizar APIs.

[Portal do Azure]: https://portal.azure.com
[GATILHOS HTTP]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Defina um objeto de pedidoOverrides]: #requestOverrides
[Defina um objeto de sobreposição de resposta]: #responseOverrides
[definições de aplicação]: #use-appsettings
[Utilizar variáveis]: #using-variables
[parâmetros do pedido original do cliente]: #request-parameters
[parâmetros da resposta de trás para a frente]: #response-parameters
