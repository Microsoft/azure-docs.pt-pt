---
title: Políticas de transformação da API Da Azure Microsoft Docs
description: Conheça as políticas de transformação disponíveis para utilização na Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: 27bb6abb7ae8eae46bc4dea3708270ecb4b731a6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81260909"
---
# <a name="api-management-transformation-policies"></a>Políticas de transformação da Gestão de API
Este tópico fornece uma referência para as seguintes políticas de Gestão da API. Para obter informações sobre a adição e configuração de políticas, consulte [Políticas na Gestão da API](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="transformation-policies"></a><a name="TransformationPolicies"></a>Políticas de transformação

-   [Converter JSON para XML](api-management-transformation-policies.md#ConvertJSONtoXML) - Converte o corpo de pedido ou resposta da JSON para xML.

-   [Converter XML para JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) - Converte o corpo de pedido ou resposta de XML para JSON.

-   [Encontre e substitua](api-management-transformation-policies.md#Findandreplacestringinbody) a cadeia no corpo - Encontra um substring de pedido ou resposta e substitui-a por uma subcadeia diferente.

-   [Mask URLs em conteúdo](api-management-transformation-policies.md#MaskURLSContent) - Re-escreve (máscaras) ligações no corpo de resposta de modo a que apontem para o link equivalente através do gateway.

-   [Despfique](api-management-transformation-policies.md#SetBackendService) o serviço de backend - Altera o serviço de backend para um pedido de entrada.

-   [Definir o corpo](api-management-transformation-policies.md#SetBody) - Define o corpo da mensagem para pedidos de entrada e saída.

-   [set HTTP header](api-management-transformation-policies.md#SetHTTPheader) - Atribui um valor a uma resposta existente e/ou cabeçalho de pedido ou adiciona uma nova resposta e/ou cabeçalho de pedido.

-   [Definir parâmetro](api-management-transformation-policies.md#SetQueryStringParameter) de corda de consulta - Adiciona, substitui o valor de ou elimina o parâmetro de corda de pedido de consulta.

-   [Reescrever URL](api-management-transformation-policies.md#RewriteURL) - Converte um URL de pedido do seu formulário público para o formulário esperado pelo serviço web.

-   [Transforme o XML utilizando um XSLT](api-management-transformation-policies.md#XSLTransform) - Aplica uma transformação XSL ao XML no corpo de pedido ou resposta.

##  <a name="convert-json-to-xml"></a><a name="ConvertJSONtoXML"></a>Converter JSON em XML
 A `json-to-xml` política converte um órgão de pedido ou resposta da JSON para o XML.

### <a name="policy-statement"></a>Declaração política

```xml
<json-to-xml apply="always | content-type-json" consider-accept-header="true | false" parse-date="true | false"/>
```

### <a name="example"></a>Exemplo

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <json-to-xml apply="always" consider-accept-header="false" parse-date="false"/>
    </outbound>
</policies>
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|json-to-xml|Elemento de raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|apply|O atributo deve ser fixado num dos seguintes valores.<br /><br /> - sempre - aplicar sempre conversão.<br />- conteúdo-tipo-json - converta-se apenas se o cabeçalho do Tipo conteúdo da resposta indicar a presença de JSON.|Sim|N/D|
|considerar-aceitar-cabeçalho|O atributo deve ser fixado num dos seguintes valores.<br /><br /> - verdadeiro - aplicar conversão se xML for solicitado a pedido Aceite cabeçalho.<br />- falso - aplicar sempre conversão.|Não|true|
|data-parse|Quando definidos até à `false` data os valores são simplesmente copiados durante a transformação|Não|true|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada, saída, erro

-   **Âmbitos de política:** todos os âmbitos

##  <a name="convert-xml-to-json"></a><a name="ConvertXMLtoJSON"></a>Converter XML em JSON
 A `xml-to-json` política converte um órgão de pedido ou resposta de XML para JSON. Esta política pode ser usada para modernizar APIs com base em serviços web de backend xml.

### <a name="policy-statement"></a>Declaração política

```xml
<xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>
```

### <a name="example"></a>Exemplo

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|xml-a-json|Elemento de raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|tipo|O atributo deve ser fixado num dos seguintes valores.<br /><br /> - javascript-friendly - o JSON convertido tem um formulário amigável para os desenvolvedores JavaScript.<br />- direto - o JSON convertido reflete a estrutura original do documento XML.|Sim|N/D|
|apply|O atributo deve ser fixado num dos seguintes valores.<br /><br /> - sempre - converter sempre.<br />- conteúdo-tipo-xml - converta-se apenas se o cabeçalho do tipo conteúdo indicar a presença de XML.|Sim|N/D|
|considerar-aceitar-cabeçalho|O atributo deve ser fixado num dos seguintes valores.<br /><br /> - verdadeiro - aplicar conversão se jSON for solicitado a pedido Aceite cabeçalho.<br />- falso - aplicar sempre conversão.|Não|true|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada, saída, erro

-   **Âmbitos de política:** todos os âmbitos

##  <a name="find-and-replace-string-in-body"></a><a name="Findandreplacestringinbody"></a>Encontre e substitua a corda no corpo
 A `find-and-replace` política encontra um substring de pedido ou resposta e substitui-o por um substring diferente.

### <a name="policy-statement"></a>Declaração política

```xml
<find-and-replace from="what to replace" to="replacement" />
```

### <a name="example"></a>Exemplo

```xml
<find-and-replace from="notebook" to="laptop" />
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|encontrar e substituir|Elemento de raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|De|A cadeia a procurar.|Sim|N/D|
|para|A cadeia de substituição. Especifique uma cadeia de substituição de comprimento zero para remover a cadeia de pesquisa.|Sim|N/D|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada, saída, backend, erro

-   **Âmbitos de política:** todos os âmbitos

##  <a name="mask-urls-in-content"></a><a name="MaskURLSContent"></a>Máscara URLs em conteúdo
 A `redirect-content-urls` política reescreve (máscaras) liga-se no corpo de resposta para que apontem para a ligação equivalente através do portal. Utilize na secção de saída para reescrever as ligações do corpo de resposta para que apontem para o portal. Utilize na secção de entrada para um efeito oposto.

> [!NOTE]
>  Esta política não altera valores `Location` de cabeçalho, como cabeçalhos. Para alterar os valores do cabeçalho, utilize a política [de cabeçalho.](api-management-transformation-policies.md#SetHTTPheader)

### <a name="policy-statement"></a>Declaração política

```xml
<redirect-content-urls />
```

### <a name="example"></a>Exemplo

```xml
<redirect-content-urls />
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|redirecionamento-conteúdo-urls|Elemento de raiz.|Sim|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada, saída

-   **Âmbitos de política:** todos os âmbitos

##  <a name="set-backend-service"></a><a name="SetBackendService"></a>Definir serviço de backend
 Utilize `set-backend-service` a política para redirecionar um pedido de entrada para um backend diferente do especificado nas definições da API para essa operação. Esta política altera o URL base de serviço backend do pedido de entrada para o especificado na política.

### <a name="policy-statement"></a>Declaração política

```xml
<set-backend-service base-url="base URL of the backend service" />
```

ou

```xml
<set-backend-service backend-id="identifier of the backend entity specifying base URL of the backend service" />
```

> [!NOTE]
> As entidades backend podem ser geridas através da gestão [API](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/backend) e [PowerShell.](https://www.powershellgallery.com/packages?q=apimanagement)

### <a name="example"></a>Exemplo

```xml
<policies>
    <inbound>
        <choose>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2013-05")">
                <set-backend-service base-url="http://contoso.com/api/8.2/" />
            </when>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2014-03")">
                <set-backend-service base-url="http://contoso.com/api/9.1/" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
Neste exemplo, as rotas de política de serviços de backend definidas solicitam com base no valor da versão passado na cadeia de consulta para um serviço de backend diferente do especificado na API.

Inicialmente, o URL base de serviço backend é derivado das definições da API. Assim, o `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` URL `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` `http://contoso.com/api/10.4/` de pedido torna-se onde está o URL do serviço de backend especificado nas definições da API.

Quando o [<escolher\> ](api-management-advanced-policies.md#choose) a declaração de política é aplicada, `http://contoso.com/api/9.1`o URL base de serviço backend pode mudar novamente para `http://contoso.com/api/8.2` ou , dependendo do valor do parâmetro de consulta de pedido de versão. Por exemplo, se `"2013-15"` o valor for `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`o URL de pedido final torna-se .

Se for desejada uma nova transformação do pedido, outras políticas de [Transformação](api-management-transformation-policies.md#TransformationPolicies) podem ser utilizadas. Por exemplo, para remover o parâmetro de consulta da versão agora que o pedido está a ser encaminhado para uma versão específica de backend, a política de [parâmetros](api-management-transformation-policies.md#SetQueryStringParameter) de corda de consulta set pode ser usada para remover o atributo da versão agora redundante.

### <a name="example"></a>Exemplo

```xml
<policies>
    <inbound>
        <set-backend-service backend-id="my-sf-service" sf-partition-key="@(context.Request.Url.Query.GetValueOrDefault("userId","")" sf-replica-type="primary" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
Neste exemplo, a política encaminha o pedido para um backend de tecido de serviço, utilizando a corda de consulta userId como chave de partição e utilizando a réplica primária da divisória.

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|serviço de back-end|Elemento de raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|base-url|Url de base de serviço de backend novo.|Um `base-url` dos `backend-id` ou deve estar presente.|N/D|
|backend-id|Identificador do backend para. (As entidades backend são geridas via [API](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/backend) e [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement).)|Um `base-url` dos `backend-id` ou deve estar presente.|N/D|
|chave de partição sf|Só aplicável quando o backend é um serviço de tecido de serviço e é especificado usando 'backend-id'. Usado para resolver uma partição específica do serviço de resolução de nomes.|Não|N/D|
|sf-replica-tipo|Só aplicável quando o backend é um serviço de tecido de serviço e é especificado usando 'backend-id'. Controle se o pedido deve ir para a réplica primária ou secundária de uma divisória. |Não|N/D|
|sf-resolver-condição|Só aplicável quando o backend é um serviço de Tecido de Serviço. Condição identificando se a chamada para o backend do Tecido de Serviço tem que ser repetida com nova resolução.|Não|N/D|
|sf-service-exemplo-nome|Só aplicável quando o backend é um serviço de Tecido de Serviço. Permite alterar as instâncias de serviço no tempo de execução. |Não|N/D|
|sf-listener-nome|Só aplicável quando o backend é um serviço de tecido de serviço e é especificado usando 'backend-id'. Serviço Fabric Reliable Services permite-lhe criar vários ouvintes num serviço. Este atributo é usado para selecionar um ouvinte específico quando um Serviço Fiável de Backend tem mais do que um ouvinte. Se este atributo não for especificado, a API Management tentará utilizar um ouvinte sem nome. Um ouvinte sem nome é típico de Serviços Fiáveis que têm apenas um ouvinte. |Não|N/D|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada, backend

-   **Âmbitos de política:** todos os âmbitos

##  <a name="set-body"></a><a name="SetBody"></a>Definir corpo
 Utilize `set-body` a política para definir o organismo de mensagens para pedidos de entrada e saída. Para aceder ao corpo de `context.Request.Body` mensagens `context.Response.Body`pode utilizar a propriedade ou a, dependendo se a apólice está na secção de entrada ou saída.

> [!IMPORTANT]
>  Note que, por padrão, ao `context.Request.Body` `context.Response.Body`aceder ao corpo da mensagem utilizando ou , o corpo original da mensagem está perdido e deve ser definido devolvendo o corpo de volta na expressão. Para preservar o conteúdo `preserveContent` do corpo, defina o parâmetro para quando `true` aceder à mensagem. Se `preserveContent` for `true` definido e um corpo diferente for devolvido pela expressão, o corpo devolvido é usado.
>
>  Tenha em atenção as seguintes considerações ao utilizar a `set-body` apólice.
>
> - Se estiver a `set-body` usar a política para devolver um corpo `preserveContent` novo `true` ou atualizado, não precisa de definir porque está a fornecer explicitamente o conteúdo do novo corpo.
>   -   Preservar o conteúdo de uma resposta no gasoduto de entrada não faz sentido porque ainda não há resposta.
>   -   Preservar o conteúdo de um pedido no oleoduto de saída não faz sentido porque o pedido já foi enviado para o backend neste momento.
>   -   Se esta política for utilizada quando não existe um organismo de mensagens, por exemplo, num GET de entrada, é lançada uma exceção.

 Para mais informações, `context.Response.Body`consulte `IMessage` as `context.Request.Body`secções da [tabela variável Context.](api-management-policy-expressions.md#ContextVariables)

### <a name="policy-statement"></a>Declaração política

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Exemplos

#### <a name="literal-text-example"></a>Exemplo de texto literal

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Exemplo de aceder ao corpo como uma corda. Note que estamos preservando o corpo de pedido original para que possamos acessá-lo mais tarde no oleoduto.

```xml
<set-body>
@{ 
    string inBody = context.Request.Body.As<string>(preserveContent: true); 
    if (inBody[0] =='c') { 
        inBody[0] = 'm'; 
    } 
    return inBody; 
}
</set-body>
```

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accessing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Exemplo de acesso ao corpo como Um JObject. Note-se que, uma vez que não reservamos o organismo de pedido original, acedendo-o mais tarde no gasoduto resultará numa exceção.

```xml
<set-body> 
@{ 
    JObject inBody = context.Request.Body.As<JObject>(); 
    if (inBody.attribute == <tag>) { 
        inBody[0] = 'm'; 
    } 
    return inBody.ToString(); 
} 
</set-body>

```

#### <a name="filter-response-based-on-product"></a>Resposta do filtro com base no produto
 Este exemplo mostra como executar a filtragem de conteúdo removendo elementos de `Starter` dados da resposta recebida do serviço backend ao utilizar o produto. Para uma demonstração de configuração e utilização desta política, consulte [cloud cover Episódio 177: Mais Funcionalidades de Gestão aPI com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avance para as 34:30. Comece às 31:50 para ver uma visão geral da [API](https://developer.forecast.io/) de Previsão do Céu Escuro usada para esta demonstração.

```xml
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->
<choose>
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">
    <set-body>@{
        var response = context.Response.Body.As<JObject>();
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {
          response.Property (key).Remove ();
        }
        return response.ToString();
      }
    </set-body>
  </when>
</choose>
```

### <a name="using-liquid-templates-with-set-body"></a>Usando modelos líquidos com corpo definido
A `set-body` política pode ser configurada para usar a linguagem [templating liquidal](https://shopify.github.io/liquid/basics/introduction/) para transformar o corpo de um pedido ou resposta. Isto pode ser muito eficaz se precisar de remodelar completamente o formato da sua mensagem.

> [!IMPORTANT]
> A implementação do `set-body` Líquido utilizado na política está configurada em 'Modo C#'. Isto é particularmente importante quando se faz coisas como a filtragem. Como exemplo, a utilização de um filtro de data requer a utilização de invólucro Pascal e formatação de data C# por exemplo:
>
> {{body.foo.startDateTime! Data:"yyyMMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> Para se ligar corretamente a um corpo XML `set-header` utilizando o modelo Liquid, utilize uma política para definir o Tipo de Conteúdo para qualquer aplicação/xml, texto/xml (ou qualquer tipo que termine com +xml); para um corpo JSON, deve ser aplicação/json, texto/json (ou qualquer tipo que termine com +json).

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>Converter JSON em SABÃO usando um modelo líquido
```xml
<set-body template="liquid">
    <soap:Envelope xmlns="http://tempuri.org/" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
        <soap:Body>
            <GetOpenOrders>
                <cust>{{body.getOpenOrders.cust}}</cust>
            </GetOpenOrders>
        </soap:Body>
    </soap:Envelope>
</set-body>
```

#### <a name="transform-json-using-a-liquid-template"></a>Transforme jSON usando um modelo líquido
```xml
{
"order": {
    "id": "{{body.customer.purchase.identifier}}",
    "summary": "{{body.customer.purchase.orderShortDesc}}"
    }
}
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|set-body|Elemento de raiz. Contém o texto do corpo ou uma expressão que devolve um corpo.|Sim|

### <a name="properties"></a>Propriedades

|Nome|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|modelo|Usado para alterar o modo de templating em que a política do corpo definido vai funcionar. Atualmente, o único valor suportado é:<br /><br />- líquido - a política do corpo definido utilizará o motor de templating líquido |Não||

Para aceder a informações sobre o pedido e resposta, o modelo Liquid pode ligar-se a um objeto de contexto com as seguintes propriedades: <br />
<pre>context.
    Request.
        Url
        Method
        OriginalMethod
        OriginalUrl
        IpAddress
        MatchedParameters
        HasBody
        ClientCertificates
        Headers

    Response.
        StatusCode
        Method
        Headers
Url.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString

OriginalUrl.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString
</pre>



### <a name="usage"></a>Utilização
 Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada, saída, backend

-   **Âmbitos de política:** todos os âmbitos

##  <a name="set-http-header"></a><a name="SetHTTPheader"></a>Definir cabeçalho HTTP
 A `set-header` política atribui um valor a uma resposta existente e/ou cabeçalho de pedido ou adiciona uma nova resposta e/ou cabeçalho de pedido.

 Insere uma lista de cabeçalhos HTTP numa mensagem HTTP. Quando colocada num gasoduto de entrada, esta política define os cabeçalhos HTTP para o pedido que é passado para o serviço alvo. Quando colocada num oleoduto de saída, esta política define os cabeçalhos HTTP para a resposta enviada ao cliente do gateway.

### <a name="policy-statement"></a>Declaração política

```xml
<set-header name="header name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
</set-header>
```

### <a name="examples"></a>Exemplos

#### <a name="example---adding-header-override-existing"></a>Exemplo - adicionar cabeçalho, anular o existente

```xml
<set-header name="some header name" exists-action="override">
    <value>20</value>
</set-header>
```
#### <a name="example---removing-header"></a>Exemplo - remoção do cabeçalho

```xml
 <set-header name="some header name" exists-action="delete" />
```



#### <a name="forward-context-information-to-the-backend-service"></a>Informações de contexto para a frente para o serviço backend
 Este exemplo mostra como aplicar a política a nível da API para fornecer informações de contexto ao serviço backend. Para uma demonstração de configuração e utilização desta política, consulte [cloud cover Episódio 177: Mais Funcionalidades de Gestão aPI com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avançar rapidamente para as 10:30. Às 12:10 há uma demonstração de convocar uma operação no portal de desenvolvimento onde se pode ver a política a funcionar.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 Para mais informações, consulte [expressões políticas](api-management-policy-expressions.md) e [variável de contexto.](api-management-policy-expressions.md#ContextVariables)

> [!NOTE]
> Os múltiplos valores de um cabeçalho são concatenados a uma corda CSV, por exemplo:`headerName: value1,value2,value3`
>
> As exceções incluem cabeçalhos padronizados, que valores:
> - pode conter vírgulas ,`User-Agent`, `Proxy-Authenticate` `WWW-Authenticate`
> - pode conter`Cookie`a `Set-Cookie` `Warning`data ( , , ),
> - conter a`Date` `Expires`data `If-Modified-Since` `If-Unmodified-Since`( `Last-Modified` `Retry-After`, , , , , ).
>
> Em caso dessas exceções, os múltiplos valores do cabeçalho não serão concatenados numa corda e serão passados como cabeçalhos separados, por exemplo:`User-Agent: value1`
>`User-Agent: value2`
>`User-Agent: value3`

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|set-header|Elemento de raiz.|Sim|
|valor|Especifica o valor do cabeçalho a definir. Para vários cabeçalhos com `value` o mesmo nome adicione elementos adicionais.|Não|

### <a name="properties"></a>Propriedades

|Nome|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|existe-ação|Especifica que medidas tomar quando o cabeçalho já estiver especificado. Este atributo deve ter um dos seguintes valores.<br /><br /> - substituição - substitui o valor do cabeçalho existente.<br />- skip - não substitui o valor do cabeçalho existente.<br />- apêndice - anexa o valor ao valor do cabeçalho existente.<br />- apagar - retira o cabeçalho do pedido.<br /><br /> Quando definido `override` para recrutar várias entradas com o mesmo nome resulta no cabeçalho sendo definido de acordo com todas as entradas (que serão listadas várias vezes); apenas os valores listados serão definidos no resultado.|Não|anular|
|nome|Especifica o nome do cabeçalho a definir.|Sim|N/D|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada, saída, backend, erro

-   **Âmbitos de política:** todos os âmbitos

##  <a name="set-query-string-parameter"></a><a name="SetQueryStringParameter"></a>Definir parâmetro de corda de consulta
 A `set-query-parameter` política adiciona, substitui o valor ou elimina o parâmetro de cadeia de pedidos de consulta. Pode ser usado para passar parâmetros de consulta esperados pelo serviço backend que são opcionais ou nunca estão presentes no pedido.

### <a name="policy-statement"></a>Declaração política

```xml
<set-query-parameter name="param name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
</set-query-parameter>
```

#### <a name="example"></a>Exemplo

```xml

<set-query-parameter name="api-key" exists-action="skip">
  <value>12345678901</value>
</set-query-parameter>

```

#### <a name="forward-context-information-to-the-backend-service"></a>Informações de contexto para a frente para o serviço backend
 Este exemplo mostra como aplicar a política a nível da API para fornecer informações de contexto ao serviço backend. Para uma demonstração de configuração e utilização desta política, consulte [cloud cover Episódio 177: Mais Funcionalidades de Gestão aPI com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avançar rapidamente para as 10:30. Às 12:10 há uma demonstração de convocar uma operação no portal de desenvolvimento onde se pode ver a política a funcionar.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 Para mais informações, consulte [expressões políticas](api-management-policy-expressions.md) e [variável de contexto.](api-management-policy-expressions.md#ContextVariables)

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|set-consulta-parâmetro|Elemento de raiz.|Sim|
|valor|Especifica o valor do parâmetro de consulta a definir. Para vários parâmetros de consulta `value` com o mesmo nome adicione elementos adicionais.|Sim|

### <a name="properties"></a>Propriedades

|Nome|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|existe-ação|Especifica a ação a realizar quando o parâmetro de consulta já está especificado. Este atributo deve ter um dos seguintes valores.<br /><br /> - substituição - substitui o valor do parâmetro existente.<br />- skip - não substitui o valor do parâmetro de consulta existente.<br />- apêndice - anexa o valor ao valor do parâmetro de consulta existente.<br />- excluir - remove o parâmetro de consulta do pedido.<br /><br /> Quando definido `override` para recrutar várias entradas com o mesmo nome resulta no parâmetro de consulta sendo definido de acordo com todas as entradas (que serão listadas várias vezes); apenas os valores listados serão definidos no resultado.|Não|anular|
|nome|Especifica o nome do parâmetro de consulta a definir.|Sim|N/D|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada, backend

-   **Âmbitos de política:** todos os âmbitos

##  <a name="rewrite-url"></a><a name="RewriteURL"></a>Reescrever URL
 A `rewrite-uri` política converte um URL de pedido da sua forma pública para o formulário esperado pelo serviço web, como mostra o seguinte exemplo.

- URL público -`http://api.example.com/storenumber/ordernumber`

- Solicitar URL -`http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

  Esta política pode ser usada quando um URL humano e/ou amigo do navegador deve ser transformado no formato URL esperado pelo serviço web. Esta política só precisa de ser aplicada quando se expõe um formato DEURL alternativo, como URLs limpos, URLs RESTful, URLs fáceis de utilizar ou URLs que são URLs puramente estruturais que não contêm uma corda de consulta e que contêm apenas o caminho do recurso (após o esquema e a autoridade). Isto é feito frequentemente para fins estéticos, usabilidade supor ou otimização de motores de busca (SEO).

> [!NOTE]
>  Só é possível adicionar parâmetros de cordas de consulta utilizando a política. Não é possível adicionar parâmetros extra de trajetória de modelo no URL de reescrita.

### <a name="policy-statement"></a>Declaração política

```xml
<rewrite-uri template="uri template" copy-unmatched-params="true | false" />
```

### <a name="example"></a>Exemplo

```xml
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put?c=d -->
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" copy-unmatched-params="false" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put -->
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|reescrever-uri|Elemento de raiz.|Sim|

### <a name="attributes"></a>Atributos

|Atributo|Descrição|Necessário|Predefinição|
|---------------|-----------------|--------------|-------------|
|modelo|O URL do serviço web real com quaisquer parâmetros de corda de consulta. Ao utilizar expressões, todo o valor deve ser uma expressão.|Sim|N/D|
|copy-inmatched-params|Especifica se os parâmetros de consulta no pedido de entrada não presentes no modelo original de URL são adicionados ao URL definido pelo modelo de reescrita|Não|true|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada

-   **Âmbitos de política:** todos os âmbitos

##  <a name="transform-xml-using-an-xslt"></a><a name="XSLTransform"></a>Transforme xML usando um XSLT
 A `Transform XML using an XSLT` política aplica uma transformação XSL à XML no órgão de pedido ou resposta.

### <a name="policy-statement"></a>Declaração política

```xml
<xsl-transform>
    <parameter name="User-Agent">@(context.Request.Headers.GetValueOrDefault("User-Agent","non-specified"))</parameter>
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
        <xsl:output method="xml" indent="yes" />
        <xsl:param name="User-Agent" />
        <xsl:template match="* | @* | node()">
            <xsl:copy>
                <xsl:if test="self::* and not(parent::*)">
                    <xsl:attribute name="User-Agent">
                        <xsl:value-of select="$User-Agent" />
                    </xsl:attribute>
                </xsl:if>
                <xsl:apply-templates select="* | @* | node()" />
            </xsl:copy>
        </xsl:template>
    </xsl:stylesheet>
  </xsl-transform>
```

### <a name="example"></a>Exemplo

```xml
<policies>
  <inbound>
      <base />
  </inbound>
  <outbound>
      <base />
      <xsl-transform>
        <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
            <xsl:output omit-xml-declaration="yes" method="xml" indent="yes" />
            <!-- Copy all nodes directly-->
            <xsl:template match="node()| @*|*">
                <xsl:copy>
                    <xsl:apply-templates select="@* | node()|*" />
                </xsl:copy>
            </xsl:template>
        </xsl:stylesheet>
    </xsl-transform>
  </outbound>
</policies>
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|xsl-transformar|Elemento de raiz.|Sim|
|parameter|Usado para definir variáveis usadas na transformação|Não|
|xsl:stylesheet|Elemento de folha de estilo de raiz. Todos os elementos e atributos definidos no seguimento da [especificação padrão XSLT](https://www.w3.org/TR/xslt)|Sim|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada, saída

-   **Âmbitos de política:** todos os âmbitos

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte os seguintes tópicos:

+ [Políticas em Gestão aPi](api-management-howto-policies.md)
+ [Referência política](api-management-policy-reference.md) para uma lista completa de declarações políticas e suas configurações
+ [Amostras políticas](policy-samples.md)
