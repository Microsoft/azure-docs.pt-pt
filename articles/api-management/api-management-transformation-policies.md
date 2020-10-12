---
title: Políticas de transformação da AZure API Management Microsoft Docs
description: Conheça as políticas de transformação disponíveis para uso na Azure API Management.
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
ms.openlocfilehash: 0182c3aa9095ad6f7bf3d8d86f115517e9efb020
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86249608"
---
# <a name="api-management-transformation-policies"></a>Políticas de transformação da Gestão de API
Este tópico fornece uma referência para as seguintes políticas de Gestão da API. Para obter informações sobre políticas de adição e configuração, consulte [Políticas em Gestão de API.](https://go.microsoft.com/fwlink/?LinkID=398186)

##  <a name="transformation-policies"></a><a name="TransformationPolicies"></a> Políticas de transformação

-   [Converter JSON em XML](api-management-transformation-policies.md#ConvertJSONtoXML) - Converte o pedido ou o corpo de resposta de JSON para XML.

-   [Converter XML em JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) - Converte o pedido ou o corpo de resposta de XML para JSON.

-   [Encontrar e substituir a corda no corpo](api-management-transformation-policies.md#Findandreplacestringinbody) - Encontra um pedido ou sublagem de resposta e substitui-o por um sublamamento diferente.

-   [Mascarar URLs em conteúdo](api-management-transformation-policies.md#MaskURLSContent) - Re-escreve links (máscaras) no corpo de resposta de modo a que eles apaguem para o link equivalente através do gateway.

-   [Serviço de backend definido](api-management-transformation-policies.md#SetBackendService) - Altera o serviço de backend para um pedido de entrada.

-   [Conjunto do corpo](api-management-transformation-policies.md#SetBody) - Define o corpo da mensagem para pedidos de entrada e saída.

-   [Definir cabeçalho HTTP](api-management-transformation-policies.md#SetHTTPheader) - Atribui um valor a uma resposta existente e/ou solicitar cabeçalho ou adiciona uma nova resposta e/ou cabeçalho de pedido.

-   [Definir parâmetro de cadeia de consulta](api-management-transformation-policies.md#SetQueryStringParameter) - Adiciona, substitui valor ou elimina parâmetro de cadeia de pedido.

-   [Reescrever URL](api-management-transformation-policies.md#RewriteURL) - Converte um URL de pedido do seu formulário público para o formulário esperado pelo serviço web.

-   [Transforme o XML utilizando um XSLT](api-management-transformation-policies.md#XSLTransform) - Aplica uma transformação XSL a XML no corpo de pedido ou resposta.

##  <a name="convert-json-to-xml"></a><a name="ConvertJSONtoXML"></a> Converter JSON em XML
 A `json-to-xml` apólice converte um órgão de pedido ou resposta de JSON para XML.

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

|Nome|Descrição|Obrigatório|
|----------|-----------------|--------------|
|json-to-xml|Elemento de raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Obrigatório|Predefinição|
|----------|-----------------|--------------|-------------|
|apply|O atributo deve ser definido para um dos seguintes valores.<br /><br /> - sempre - aplicar sempre a conversão.<br />- tipo de conteúdo-json - converta-se apenas se o cabeçalho do tipo de conteúdo de resposta indicar a presença de JSON.|Sim|N/D|
|considerar-aceitar cabeçalho|O atributo deve ser definido para um dos seguintes valores.<br /><br /> - verdadeiro - aplique a conversão se XML for solicitado no pedido Aceite cabeçalho.<br />- falso - aplicar sempre a conversão.|Não|true|
|data de parse|Quando definidos até à `false` data os valores são simplesmente copiados durante a transformação|Não|true|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada, saída, erro

-   **Âmbitos de política:** todos os âmbitos

##  <a name="convert-xml-to-json"></a><a name="ConvertXMLtoJSON"></a> Converter XML em JSON
 A `xml-to-json` apólice converte um órgão de pedido ou resposta de XML para JSON. Esta política pode ser usada para modernizar APIs com base em serviços web de backend apenas XML.

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

|Nome|Descrição|Obrigatório|
|----------|-----------------|--------------|
|xml-para-json|Elemento de raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Obrigatório|Predefinição|
|----------|-----------------|--------------|-------------|
|tipo|O atributo deve ser definido para um dos seguintes valores.<br /><br /> - javascript-friendly - o JSON convertido tem um formulário amigável para os desenvolvedores JavaScript.<br />- direto - o JSON convertido reflete a estrutura original do documento XML.|Sim|N/D|
|apply|O atributo deve ser definido para um dos seguintes valores.<br /><br /> - sempre - converter sempre.<br />- tipo de conteúdo-xml - converta-se apenas se o cabeçalho do tipo de conteúdo de resposta indicar a presença de XML.|Sim|N/D|
|considerar-aceitar cabeçalho|O atributo deve ser definido para um dos seguintes valores.<br /><br /> - verdadeiro - aplique a conversão se jSON for solicitado no pedido Aceite cabeçalho.<br />- falso - aplicar sempre a conversão.|Não|true|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada, saída, erro

-   **Âmbitos de política:** todos os âmbitos

##  <a name="find-and-replace-string-in-body"></a><a name="Findandreplacestringinbody"></a> Encontre e substitua a corda no corpo
 A `find-and-replace` apólice encontra um pedido ou sub-fundamento de resposta e substitui-o por um sub-cordão diferente.

### <a name="policy-statement"></a>Declaração política

```xml
<find-and-replace from="what to replace" to="replacement" />
```

### <a name="example"></a>Exemplo

```xml
<find-and-replace from="notebook" to="laptop" />
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Obrigatório|
|----------|-----------------|--------------|
|encontrar e substituir|Elemento de raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Obrigatório|Predefinição|
|----------|-----------------|--------------|-------------|
|De|A cadeia a procurar.|Sim|N/D|
|para|A cadeia de substituição. Especifique uma cadeia de substituição de comprimento zero para remover o fio de pesquisa.|Sim|N/D|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada, saída, backend, erro

-   **Âmbitos de política:** todos os âmbitos

##  <a name="mask-urls-in-content"></a><a name="MaskURLSContent"></a> URLs de máscara em conteúdo
 A `redirect-content-urls` política reescreve (máscaras) ligações no organismo de resposta para que apaguem para o link equivalente através do gateway. Utilize na secção de saída para reescrever ligações do corpo de resposta para os fazer apontar para o gateway. Utilize na secção de entrada para obter um efeito oposto.

> [!NOTE]
>  Esta política não altera quaisquer valores de cabeçalho, tais como `Location` cabeçalhos. Para alterar os valores dos cabeçalhos, utilize a política [de set-header.](api-management-transformation-policies.md#SetHTTPheader)

### <a name="policy-statement"></a>Declaração política

```xml
<redirect-content-urls />
```

### <a name="example"></a>Exemplo

```xml
<redirect-content-urls />
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Obrigatório|
|----------|-----------------|--------------|
|redireccionamento-conteúdo-urls|Elemento de raiz.|Sim|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada, saída

-   **Âmbitos de política:** todos os âmbitos

##  <a name="set-backend-service"></a><a name="SetBackendService"></a> Serviço de backend definido
 Utilize a `set-backend-service` política para redirecionar um pedido de entrada para um backend diferente do especificado nas definições de API para essa operação. Esta política altera o URL base de serviço de backend do pedido de entrada para o especificado na apólice.

### <a name="policy-statement"></a>Declaração política

```xml
<set-backend-service base-url="base URL of the backend service" />
```

ou

```xml
<set-backend-service backend-id="identifier of the backend entity specifying base URL of the backend service" />
```

> [!NOTE]
> As entidades backend podem ser geridas através da [gestão API](/rest/api/apimanagement/2019-12-01/backend) e [PowerShell.](https://www.powershellgallery.com/packages?q=apimanagement)

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
Neste exemplo, as rotas de política de serviço de backend definidas com base no valor da versão passaram na cadeia de consulta para um serviço de backend diferente do especificado na API.

Inicialmente, o URL de base de serviço de backend é derivado das definições de API. Assim, o URL de pedido `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` torna-se onde está o URL de `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` serviço de `http://contoso.com/api/10.4/` backend especificado nas definições de API.

Quando a [<escolher \> ](api-management-advanced-policies.md#choose) a declaração de política é aplicada, o URL de base de serviço de backend pode mudar novamente `http://contoso.com/api/8.2` para `http://contoso.com/api/9.1` ou, dependendo do valor do parâmetro de consulta de pedido de versão. Por exemplo, se o valor for `"2013-15"` o URL de pedido final torna-se `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef` .

Se for desejada uma nova transformação do pedido, podem ser utilizadas outras [políticas de transformação.](api-management-transformation-policies.md#TransformationPolicies) Por exemplo, para remover o parâmetro de consulta da versão agora que o pedido está a ser encaminhado para uma versão específica backend, a política de  [parâmetros de linha de consulta set](api-management-transformation-policies.md#SetQueryStringParameter) pode ser usada para remover o atributo de versão agora redundante.

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
Neste exemplo, a política encaminha o pedido para um backend de tecido de serviço, utilizando a cadeia de consulta userId como chave de partição e utilizando a réplica primária da partição.

### <a name="elements"></a>Elementos

|Nome|Descrição|Obrigatório|
|----------|-----------------|--------------|
|set-backend-serviço|Elemento de raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Obrigatório|Predefinição|
|----------|-----------------|--------------|-------------|
|base-url|URL de base de serviço de backend novo.|Um dos `base-url` ou `backend-id` deve estar presente.|N/D|
|backend-id|Identificador do backend para. (As entidades backend são geridas através da [API](/rest/api/apimanagement/2019-12-01/backend) e [da PowerShell](https://www.powershellgallery.com/packages?q=apimanagement).)|Um dos `base-url` ou `backend-id` deve estar presente.|N/D|
|sf-partition-key|Só aplicável quando o backend é um serviço de Tecido de Serviço e é especificado com 'backend-id'. Usado para resolver uma divisão específica do serviço de resolução de nomes.|Não|N/D|
|sf-replica-type|Só aplicável quando o backend é um serviço de Tecido de Serviço e é especificado com 'backend-id'. Controla se o pedido deve ir para a réplica primária ou secundária de uma partição. |Não|N/D|
|sf-resolve-condição|Só se aplica quando o backend é um serviço de Tecido de Serviço. A condição de identificar se a chamada para o Service Fabric tem de ser repetida com nova resolução.|Não|N/D|
|sf-serviço-instância-nome|Só se aplica quando o backend é um serviço de Tecido de Serviço. Permite alterar as instâncias de serviço em tempo de execução. |Não|N/D|
|sf-ouvinte-nome|Só aplicável quando o backend é um serviço de Tecido de Serviço e é especificado com 'backend-id'. O Service Fabric Reliable Services permite-lhe criar vários ouvintes num serviço. Este atributo é utilizado para selecionar um ouvinte específico quando um Backend Reliable Service tem mais de um ouvinte. Se este atributo não for especificado, a API Management tentará utilizar um ouvinte sem nome. Um ouvinte sem nome é típico dos Serviços Fidedigdos que têm apenas um ouvinte. |Não|N/D|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada, backend

-   **Âmbitos de política:** todos os âmbitos

##  <a name="set-body"></a><a name="SetBody"></a> Conjunto de corpo
 Utilize a `set-body` política para definir o corpo de mensagens para pedidos de entrada e saída. Para aceder ao corpo de mensagens pode utilizar a `context.Request.Body` propriedade ou `context.Response.Body` a, dependendo se a apólice está na secção de entrada ou saída.

> [!IMPORTANT]
>  Tenha em atenção que, por padrão, quando acede ao corpo da mensagem utilizando `context.Request.Body` `context.Response.Body` ou, o corpo da mensagem original perde-se e deve ser definido devolvendo o corpo à expressão. Para preservar o conteúdo do corpo, desapedaça o `preserveContent` parâmetro para o acesso à `true` mensagem. Se `preserveContent` for definido e um corpo diferente for devolvido pela `true` expressão, o corpo devolvido é usado.
>
>  Por favor, note as seguintes considerações ao utilizar a `set-body` apólice.
>
> - Se estiver a usar a `set-body` política para devolver um corpo novo ou atualizado, não precisa de definir porque está a fornecer `preserveContent` `true` explicitamente o novo conteúdo do corpo.
>   -   Preservar o conteúdo de uma resposta no oleoduto de entrada não faz sentido porque ainda não há resposta.
>   -   Preservar o conteúdo de um pedido no gasoduto de saída não faz sentido porque o pedido já foi enviado para o backend neste momento.
>   -   Se esta política for utilizada quando não existe um organismo de mensagens, por exemplo num GET de entrada, é lançada uma exceção.

 Para mais informações, consulte o `context.Request.Body` `context.Response.Body` , e as `IMessage` secções na tabela [variável Contexto.](api-management-policy-expressions.md#ContextVariables)

### <a name="policy-statement"></a>Declaração política

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Exemplos

#### <a name="literal-text-example"></a>Exemplo de texto literal

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Exemplo de acesso ao corpo como uma corda. Note que estamos preservando o corpo de pedido original para que possamos acessá-lo mais tarde no oleoduto.

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

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accessing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Exemplo de acesso ao corpo como um JObject. Note que uma vez que não estamos reservando o corpo de pedido original, aceder-lhe mais tarde no oleoduto resultará numa exceção.

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

#### <a name="filter-response-based-on-product"></a>Resposta do filtro baseada no produto
 Este exemplo mostra como executar a filtragem de conteúdos removendo elementos de dados da resposta recebida do serviço de backend ao utilizar o `Starter` produto. Para uma demonstração de configuração e utilização desta política, consulte [cloud cover episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e fast-forward to 34:30. Comece às 31:50 para ver uma visão geral da [API](https://developer.forecast.io/) de Previsão do Céu Escuro usada para esta demonstração.

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
A `set-body` política pode ser configurada para usar a linguagem templária [líquida](https://shopify.github.io/liquid/basics/introduction/) para transformar o corpo de um pedido ou resposta. Isto pode ser muito eficaz se precisar de remodelar completamente o formato da sua mensagem.

> [!IMPORTANT]
> A implementação do Líquido utilizado na `set-body` apólice está configurada no modo C#. Isto é particularmente importante quando se faz coisas como a filtragem. Como exemplo, a utilização de um filtro de data requer a utilização do invólucro Pascal e da formatação de data C#, por exemplo:
>
> {{body.foo.startDateTime Data:"yyyMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> Para se ligar corretamente a um corpo XML utilizando o modelo Liquid, utilize uma `set-header` política para definir o Tipo de Conteúdo para aplicação/xml, texto/xml (ou qualquer tipo de final com +xml); para um corpo JSON, deve ser aplicação/json, texto/json (ou qualquer tipo que termine com +json).

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>Converter JSON em SOAP usando um modelo líquido
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

#### <a name="transform-json-using-a-liquid-template"></a>Transforme json usando um modelo líquido
```xml
{
"order": {
    "id": "{{body.customer.purchase.identifier}}",
    "summary": "{{body.customer.purchase.orderShortDesc}}"
    }
}
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Obrigatório|
|----------|-----------------|--------------|
|conjunto de corpo|Elemento de raiz. Contém o texto do corpo ou uma expressão que devolve um corpo.|Sim|

### <a name="properties"></a>Propriedades

|Nome|Descrição|Obrigatório|Predefinição|
|----------|-----------------|--------------|-------------|
|modelo|Usado para alterar o modo de templário em que a política do corpo definido irá funcionar. Atualmente, o único valor suportado é:<br /><br />- líquido - a política do corpo definido utilizará o motor templário líquido |Não||

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
 Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada, saída, backend

-   **Âmbitos de política:** todos os âmbitos

##  <a name="set-http-header"></a><a name="SetHTTPheader"></a> Definir cabeçalho HTTP
 A `set-header` apólice atribui um valor a uma resposta existente e/ou um cabeçalho de pedido ou adiciona uma nova resposta e/ou cabeçalho de pedido.

 Insere uma lista de cabeçalhos HTTP numa mensagem HTTP. Quando colocado num gasoduto de entrada, esta política define os cabeçalhos HTTP para o pedido ser transmitido para o serviço alvo. Quando colocado num oleoduto de saída, esta política define os cabeçalhos HTTP para a resposta enviada ao cliente do gateway.

### <a name="policy-statement"></a>Declaração política

```xml
<set-header name="header name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
</set-header>
```

### <a name="examples"></a>Exemplos

#### <a name="example---adding-header-override-existing"></a>Exemplo - adicionar cabeçalho, sobrepor-se

```xml
<set-header name="some header name" exists-action="override">
    <value>20</value>
</set-header>
```
#### <a name="example---removing-header"></a>Exemplo - remoção do cabeçalho

```xml
 <set-header name="some header name" exists-action="delete" />
```



#### <a name="forward-context-information-to-the-backend-service"></a>Informação de contexto encaminha para o serviço backend
 Este exemplo mostra como aplicar a política ao nível da API para fornecer informações de contexto ao serviço de backend. Para uma demonstração de configuração e utilização desta política, consulte [cloud cover episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e fast-forward to 10:30. Às 12:10 há uma demonstração de convocação de uma operação no portal do desenvolvedor onde se pode ver a política em funcionamento.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 Para obter mais informações, consulte [as expressões de política](api-management-policy-expressions.md) e a [variável Context.](api-management-policy-expressions.md#ContextVariables)

> [!NOTE]
> Os múltiplos valores de um cabeçalho são concatenados a uma cadeia de CSV, por exemplo: `headerName: value1,value2,value3`
>
> As exceções incluem cabeçalhos padronizados, que valorizam:
> - podem conter vírgulas , `User-Agent` `WWW-Authenticate` , `Proxy-Authenticate`
> - pode conter data ( `Cookie` , `Set-Cookie` , `Warning` ),
> - conter a data `Date` `Expires` (, `If-Modified-Since` , , , , , . `If-Unmodified-Since` `Last-Modified` `Retry-After` .
>
> Em caso de exceções, os valores múltiplos dos cabeçalhos não serão concatenados numa única corda e serão passados como cabeçalhos separados, por exemplo: `User-Agent: value1`
>`User-Agent: value2`
>`User-Agent: value3`

### <a name="elements"></a>Elementos

|Nome|Descrição|Obrigatório|
|----------|-----------------|--------------|
|set-header|Elemento de raiz.|Sim|
|valor|Especifica o valor do cabeçalho a definir. Para vários cabeçalhos com o mesmo nome adicione `value` elementos adicionais.|Não|

### <a name="properties"></a>Propriedades

|Nome|Descrição|Obrigatório|Predefinição|
|----------|-----------------|--------------|-------------|
|existe-ação|Especifica que medidas tomar quando o cabeçalho já está especificado. Este atributo deve ter um dos seguintes valores.<br /><br /> - sobreposição - substitui o valor do cabeçalho existente.<br />- saltar - não substitui o valor do cabeçalho existente.<br />- apêndice - anexa o valor ao valor do cabeçalho existente.<br />- excluir - retire o cabeçalho do pedido.<br /><br /> Quando definido para `override` a inscrição de várias entradas com o mesmo nome resulta na configuração do cabeçalho de acordo com todas as entradas (que serão listadas várias vezes); apenas os valores listados serão definidos no resultado.|Não|sobreposição|
|name|Especifica o nome do cabeçalho a definir.|Sim|N/D|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada, saída, backend, erro

-   **Âmbitos de política:** todos os âmbitos

##  <a name="set-query-string-parameter"></a><a name="SetQueryStringParameter"></a> Definir parâmetro de cadeia de consulta
 A `set-query-parameter` apólice adiciona, substitui o valor ou elimina o parâmetro de cadeia de consulta de pedido. Pode ser usado para passar parâmetros de consulta esperados pelo serviço de backend que são opcionais ou nunca presentes no pedido.

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

#### <a name="forward-context-information-to-the-backend-service"></a>Informação de contexto encaminha para o serviço backend
 Este exemplo mostra como aplicar a política ao nível da API para fornecer informações de contexto ao serviço de backend. Para uma demonstração de configuração e utilização desta política, consulte [cloud cover episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e fast-forward to 10:30. Às 12:10 há uma demonstração de convocação de uma operação no portal do desenvolvedor onde se pode ver a política em funcionamento.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 Para obter mais informações, consulte [as expressões de política](api-management-policy-expressions.md) e a [variável Context.](api-management-policy-expressions.md#ContextVariables)

### <a name="elements"></a>Elementos

|Nome|Descrição|Obrigatório|
|----------|-----------------|--------------|
|set-consulta-parâmetro|Elemento de raiz.|Sim|
|valor|Especifica o valor do parâmetro de consulta a definir. Para parâmetros de consulta múltiplas com o mesmo nome adicione `value` elementos adicionais.|Sim|

### <a name="properties"></a>Propriedades

|Nome|Descrição|Obrigatório|Predefinição|
|----------|-----------------|--------------|-------------|
|existe-ação|Especifica a ação a realizar quando o parâmetro de consulta já está especificado. Este atributo deve ter um dos seguintes valores.<br /><br /> - sobreposição - substitui o valor do parâmetro existente.<br />- saltar - não substitui o valor do parâmetro de consulta existente.<br />- apêndice - anexa o valor ao valor do parâmetro de consulta existente.<br />- eliminar - remove o parâmetro de consulta do pedido.<br /><br /> Quando definido para `override` recrutar várias entradas com o mesmo nome resulta no parâmetro de consulta que está a ser definido de acordo com todas as entradas (que serão listadas várias vezes); apenas os valores listados serão definidos no resultado.|Não|sobreposição|
|name|Especifica o nome do parâmetro de consulta a definir.|Sim|N/D|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada, backend

-   **Âmbitos de política:** todos os âmbitos

##  <a name="rewrite-url"></a><a name="RewriteURL"></a> Reescrever URL
 A `rewrite-uri` política converte um URL de pedido do seu formulário público para o formulário esperado pelo serviço web, como mostrado no exemplo seguinte.

- URL público - `http://api.example.com/storenumber/ordernumber`

- Solicitação URL - `http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

  Esta política pode ser usada quando um URL humano e/ou amigo do navegador deve ser transformado no formato URL esperado pelo serviço web. Esta política só deve ser aplicada quando expor um formato DE URL alternativo, como URLs limpos, URLs RESTful, URLs fáceis de utilizar ou URLs fáceis de seo que são URLs puramente estruturais que não contêm uma cadeia de consulta e contêm apenas o caminho do recurso (após o esquema e a autoridade). Isto é muitas vezes feito para fins estéticos, usabilidade ou otimização do motor de busca (SEO).

> [!NOTE]
>  Só é possível adicionar parâmetros de cadeia de consulta utilizando a apólice. Não é possível adicionar parâmetros de trajetória extra no URL de reescrita.

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

|Nome|Descrição|Obrigatório|
|----------|-----------------|--------------|
|reescrever-uri|Elemento de raiz.|Sim|

### <a name="attributes"></a>Atributos

|Atributo|Descrição|Obrigatório|Predefinição|
|---------------|-----------------|--------------|-------------|
|modelo|O URL de serviço web real com quaisquer parâmetros de cadeia de consulta. Ao utilizar expressões, todo o valor deve ser uma expressão.|Sim|N/D|
|copy-incomparável-params|Especifica se os parâmetros de consulta no pedido de entrada não presentes no modelo URL original são adicionados ao URL definido pelo modelo de re-escrita|Não|true|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada

-   **Âmbitos de política:** todos os âmbitos

##  <a name="transform-xml-using-an-xslt"></a><a name="XSLTransform"></a> Transformar XML usando um XSLT
 A `Transform XML using an XSLT` política aplica uma transformação de XSL a XML no órgão de pedido ou resposta.

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

|Nome|Descrição|Obrigatório|
|----------|-----------------|--------------|
|xsl-transform|Elemento de raiz.|Sim|
|parameter|Usado para definir variáveis usadas na transformação|Não|
|xsl:folha de estilo|Elemento de folha de estilo de raiz. Todos os elementos e atributos definidos dentro seguem a [especificação padrão XSLT](https://www.w3.org/TR/xslt)|Sim|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada, saída

-   **Âmbitos de política:** todos os âmbitos

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte os seguintes tópicos:

+ [Políticas em Gestão de API](api-management-howto-policies.md)
+ [Referência política](./api-management-policies.md) para uma lista completa de declarações políticas e suas definições
+ [Amostras de política](policy-samples.md)
