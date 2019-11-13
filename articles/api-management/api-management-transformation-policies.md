---
title: Políticas de transformação do gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre as políticas de transformação disponíveis para uso no gerenciamento de API do Azure.
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
ms.openlocfilehash: c26cca40b0bf6d02bcec09945043f4ba854fa8e9
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012219"
---
# <a name="api-management-transformation-policies"></a>Políticas de transformação do gerenciamento de API
Este tópico fornece uma referência para as seguintes políticas de gerenciamento de API. Para obter informações sobre como adicionar e configurar políticas, consulte [políticas no gerenciamento de API](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="TransformationPolicies"></a>Políticas de transformação

-   [Converter JSON em XML](api-management-transformation-policies.md#ConvertJSONtoXML) – converte o corpo da solicitação ou da resposta de JSON em XML.

-   [Converter XML em JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) – converte o corpo da solicitação ou da resposta de XML para JSON.

-   [Localizar e substituir cadeia de caracteres no corpo](api-management-transformation-policies.md#Findandreplacestringinbody) – localiza uma subcadeia de caracteres de solicitação ou resposta e a substitui por uma subcadeia de caracteres diferente.

-   [Mascarar URLs no conteúdo](api-management-transformation-policies.md#MaskURLSContent) – reescreve (mascara) links no corpo da resposta para que eles apontem para o link equivalente por meio do gateway.

-   [Definir serviço de back-end](api-management-transformation-policies.md#SetBackendService) -altera o serviço de back-end para uma solicitação de entrada.

-   [Definir corpo](api-management-transformation-policies.md#SetBody) – define o corpo da mensagem para solicitações de entrada e saída.

-   [Definir cabeçalho http](api-management-transformation-policies.md#SetHTTPheader) – atribui um valor a um cabeçalho de resposta e/ou solicitação existente ou adiciona um novo cabeçalho de resposta e/ou solicitação.

-   [Definir parâmetro de cadeia de caracteres de consulta](api-management-transformation-policies.md#SetQueryStringParameter) -adiciona, substitui o valor ou exclui o parâmetro de cadeia de caracteres de consulta de solicitação.

-   [Reescrever URL](api-management-transformation-policies.md#RewriteURL) – converte uma URL de solicitação de seu formato público para a forma esperada pelo serviço Web.

-   [Transformar XML usando um XSLT](api-management-transformation-policies.md#XSLTransform) – aplica uma transformação XSL ao XML no corpo da solicitação ou da resposta.

##  <a name="ConvertJSONtoXML"></a>Converter JSON em XML
 A política de `json-to-xml` converte um corpo de solicitação ou resposta de JSON em XML.

### <a name="policy-statement"></a>Declaração de política

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
|json-to-xml|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|usar|O atributo deve ser definido como um dos valores a seguir.<br /><br /> -Always-sempre aplicar conversão.<br />-Content-Type-JSON-converter somente se o cabeçalho Content-Type de resposta indica a presença de JSON.|Sim|N/D|
|consider-accept-header|O atributo deve ser definido como um dos valores a seguir.<br /><br /> -true – aplicar conversão se JSON for solicitado no cabeçalho de aceitação da solicitação.<br />-false-sempre aplicar conversão.|Não|true|
|Data de análise|Quando definido como `false` valores de data são simplesmente copiados durante a transformação|Não|true|

### <a name="usage"></a>Utilização
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada, saída, em caso de erro

-   **Escopos de política:** todos os escopos

##  <a name="ConvertXMLtoJSON"></a>Converter XML em JSON
 A política de `xml-to-json` converte um corpo de solicitação ou resposta de XML para JSON. Essa política pode ser usada para modernizar APIs com base em serviços Web de back-end somente XML.

### <a name="policy-statement"></a>Declaração de política

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
|xml-to-json|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|type|O atributo deve ser definido como um dos valores a seguir.<br /><br /> -JavaScript-amigável – o JSON convertido tem um formato amigável para desenvolvedores de JavaScript.<br />-Direct – o JSON convertido reflete a estrutura do documento XML original.|Sim|N/D|
|usar|O atributo deve ser definido como um dos valores a seguir.<br /><br /> -sempre-converter sempre.<br />-Content-Type-XML-converter somente se o cabeçalho Content-Type de resposta indica a presença de XML.|Sim|N/D|
|consider-accept-header|O atributo deve ser definido como um dos valores a seguir.<br /><br /> -true – aplicar conversão se XML for solicitado no cabeçalho de aceitação da solicitação.<br />-false-sempre aplicar conversão.|Não|true|

### <a name="usage"></a>Utilização
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada, saída, em caso de erro

-   **Escopos de política:** todos os escopos

##  <a name="Findandreplacestringinbody"></a>Localizar e substituir cadeia de caracteres no corpo
 A política de `find-and-replace` localiza uma subcadeia de caracteres de solicitação ou resposta e a substitui por uma subcadeia de caracteres diferente.

### <a name="policy-statement"></a>Declaração de política

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
|Localizar e substituir|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|from|A cadeia de caracteres a ser pesquisada.|Sim|N/D|
|para|A cadeia de caracteres de substituição. Especifique uma cadeia de caracteres de substituição de comprimento zero para remover a cadeia de caracteres de pesquisa.|Sim|N/D|

### <a name="usage"></a>Utilização
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada, saída, back-end, em caso de erro

-   **Escopos de política:** todos os escopos

##  <a name="MaskURLSContent"></a>Mascarar URLs no conteúdo
 A política de `redirect-content-urls` reescreve (mascara) os links no corpo da resposta para que eles apontem para o link equivalente por meio do gateway. Use na seção de saída para reescrever links de corpo de resposta para que eles apontem para o gateway. Use na seção de entrada para um efeito oposto.

> [!NOTE]
>  Essa política não altera nenhum valor de cabeçalho como `Location` cabeçalhos. Para alterar os valores de cabeçalho, use a política [set-header](api-management-transformation-policies.md#SetHTTPheader) .

### <a name="policy-statement"></a>Declaração de política

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
|redirect-content-urls|Elemento raiz.|Sim|

### <a name="usage"></a>Utilização
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada, saída

-   **Escopos de política:** todos os escopos

##  <a name="SetBackendService"></a>Definir serviço de back-end
 Use a política de `set-backend-service` para redirecionar uma solicitação de entrada para um back-end diferente daquele especificado nas configurações de API para essa operação. Essa política altera a URL base do serviço de back-end da solicitação de entrada para aquela especificada na política.

### <a name="policy-statement"></a>Declaração de política

```xml
<set-backend-service base-url="base URL of the backend service" />
```

ou

```xml
<set-backend-service backend-id="identifier of the backend entity specifying base URL of the backend service" />
```

> [!NOTE]
> Entidades de back-end podem ser gerenciadas por meio da [API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend) de gerenciamento e do [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement).

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
Neste exemplo, a política definir serviço de back-end roteia solicitações com base no valor da versão passado na cadeia de caracteres de consulta para um serviço de back-end diferente daquele especificado na API.

Inicialmente, a URL base do serviço de back-end é derivada das configurações da API. Portanto, a URL de solicitação `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` se torna `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` em que `http://contoso.com/api/10.4/` é a URL do serviço de back-end especificada nas configurações da API.

Quando a instrução de [< escolha\>](api-management-advanced-policies.md#choose) política é aplicada, a URL base do serviço de back-end pode ser alterada novamente para `http://contoso.com/api/8.2` ou `http://contoso.com/api/9.1`, dependendo do valor do parâmetro de consulta de solicitação de versão. Por exemplo, se o valor for `"2013-15"` a URL de solicitação final se tornará `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`.

Se a transformação adicional da solicitação for desejada, outras [políticas de transformação](api-management-transformation-policies.md#TransformationPolicies) poderão ser usadas. Por exemplo, para remover o parâmetro de consulta de versão agora que a solicitação está sendo roteada para um back-end específico de versão, a política [definir parâmetro de cadeia de caracteres de consulta](api-management-transformation-policies.md#SetQueryStringParameter) pode ser usada para remover o atributo de versão agora redundante.

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
Neste exemplo, a política roteia a solicitação para um back-end do Service Fabric, usando a cadeia de caracteres de consulta userId como a chave de partição e usando a réplica primária da partição.

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|Set-back-end-Service|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|base-url|Nova URL base do serviço de back-end.|Um dos `base-url` ou `backend-id` deve estar presente.|N/D|
|ID de back-end|Identificador do back-end para o qual rotear. (As entidades de back-end são gerenciadas por meio da [API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend) e do [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement).)|Um dos `base-url` ou `backend-id` deve estar presente.|N/D|
|sf-partition-key|Aplicável somente quando o back-end é um serviço Service Fabric e é especificado usando ' backend-ID '. Usado para resolver uma partição específica do serviço de resolução de nomes.|Não|N/D|
|It-tipo de réplica|Aplicável somente quando o back-end é um serviço Service Fabric e é especificado usando ' backend-ID '. Controla se a solicitação deve ir para a réplica primária ou secundária de uma partição. |Não|N/D|
|sf-resolve-condition|Aplicável somente quando o back-end é um serviço Service Fabric. Condição que identifica se a chamada para Service Fabric back-end precisa ser repetida com a nova resolução.|Não|N/D|
|sf-service-instance-name|Aplicável somente quando o back-end é um serviço Service Fabric. Permite alterar as instâncias de serviço em tempo de execução. |Não|N/D|
|It-listener-name|Aplicável somente quando o back-end é um serviço Service Fabric e é especificado usando ' backend-ID '. Service Fabric Reliable Services permite que você crie vários ouvintes em um serviço. Esse atributo é usado para selecionar um ouvinte específico quando um Reliable Service de back-end tiver mais de um ouvinte. Se esse atributo não for especificado, o gerenciamento de API tentará usar um ouvinte sem nome. Um ouvinte sem um nome é típico para Reliable Services que têm apenas um ouvinte. |Não|N/D|

### <a name="usage"></a>Utilização
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada, back-end

-   **Escopos de política:** todos os escopos

##  <a name="SetBody"></a>Definir corpo
 Use a política de `set-body` para definir o corpo da mensagem para solicitações de entrada e saída. Para acessar o corpo da mensagem, você pode usar a propriedade `context.Request.Body` ou a `context.Response.Body`, dependendo se a política está na seção de entrada ou de saída.

> [!IMPORTANT]
>  Observe que, por padrão, quando você acessa o corpo da mensagem usando `context.Request.Body` ou `context.Response.Body`, o corpo da mensagem original é perdido e deve ser definido retornando o corpo de volta na expressão. Para preservar o conteúdo do corpo, defina o parâmetro `preserveContent` como `true` ao acessar a mensagem. Se `preserveContent` for definido como `true` e um corpo diferente for retornado pela expressão, o corpo retornado será usado.
>
>  Observe as seguintes considerações ao usar a política de `set-body`.
>
> - Se você estiver usando a política de `set-body` para retornar um corpo novo ou atualizado, não precisará definir `preserveContent` como `true` porque você está fornecendo explicitamente o novo conteúdo do corpo.
>   -   Preservar o conteúdo de uma resposta no pipeline de entrada não faz sentido porque ainda não há resposta.
>   -   A preservação do conteúdo de uma solicitação no pipeline de saída não faz sentido porque a solicitação já foi enviada para o back-end neste momento.
>   -   Se essa política for usada quando não houver corpo de mensagem, por exemplo, em um GET de entrada, uma exceção será lançada.

 Para obter mais informações, consulte as seções `context.Request.Body`, `context.Response.Body`e `IMessage` na tabela de [variáveis de contexto](api-management-policy-expressions.md#ContextVariables) .

### <a name="policy-statement"></a>Declaração de política

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Exemplos

#### <a name="literal-text-example"></a>Exemplo de texto literal

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Exemplo de acesso ao corpo como uma cadeia de caracteres. Observe que estamos preservando o corpo da solicitação original para que possamos acessá-lo mais tarde no pipeline.

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

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accessing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Exemplo de acesso ao corpo como um JObject. Observe que, como não estamos reservando o corpo da solicitação original, acessá-lo mais tarde no pipeline resultará em uma exceção.

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

#### <a name="filter-response-based-on-product"></a>Filtrar resposta com base no produto
 Este exemplo mostra como executar a filtragem de conteúdo removendo elementos de dados da resposta recebida do serviço de back-end ao usar o produto `Starter`. Para ver uma demonstração de como configurar e usar essa política, confira o [episódio 177 do Cloud Cover: mais recursos de gerenciamento de API com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avança para 34:30. Inicie em 31:50 para ver uma visão geral da [API de previsão do céu escuro](https://developer.forecast.io/) usada para esta demonstração.

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

### <a name="using-liquid-templates-with-set-body"></a>Usando modelos líquidos com o corpo do conjunto
A política de `set-body` pode ser configurada para usar a linguagem de modelagem de [líquidos](https://shopify.github.io/liquid/basics/introduction/) para transformar o corpo de uma solicitação ou resposta. Isso pode ser muito eficaz se você precisar remodelar completamente o formato da mensagem.

> [!IMPORTANT]
> A implementação de Liquid usada na política de `set-body` está configuradaC# em ' Mode '. Isso é particularmente importante ao fazer coisas como filtragem. Por exemplo, o uso de um filtro de data requer o uso da formatação C# de maiúsculas e minúsculas do Pascal, por exemplo:
>
> {{body.foo.startDateTime| Date:"yyyyMMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> Para associar corretamente a um corpo XML usando o modelo Liquid, use uma política de `set-header` para definir o tipo de conteúdo como Application/XML, text/xml (ou qualquer tipo que termina com + XML); para um corpo JSON, ele deve ser Application/JSON, Text/JSON (ou qualquer tipo que termine com + JSON).

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

#### <a name="transform-json-using-a-liquid-template"></a>Transformar JSON usando um modelo líquido
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
|definir corpo|Elemento raiz. Contém o texto do corpo ou uma expressão que retorna um corpo.|Sim|

### <a name="properties"></a>Propriedades

|Nome|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|modelo|Usado para alterar o modo de modelagem em que a política de corpo definida será executada. Atualmente, o único valor com suporte é:<br /><br />-Liquid-a política de corpo definida usará o mecanismo de modelagem de líquidos |Não||

Para acessar informações sobre a solicitação e a resposta, o modelo Liquid pode se associar a um objeto de contexto com as seguintes propriedades: <br />
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
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada, saída, back-end

-   **Escopos de política:** todos os escopos

##  <a name="SetHTTPheader"></a>Definir cabeçalho HTTP
 A política de `set-header` atribui um valor a um cabeçalho de resposta e/ou solicitação existente ou adiciona um novo cabeçalho de resposta e/ou solicitação.

 Insere uma lista de cabeçalhos HTTP em uma mensagem HTTP. Quando colocado em um pipeline de entrada, essa política define os cabeçalhos HTTP para a solicitação que está sendo passada para o serviço de destino. Quando colocado em um pipeline de saída, essa política define os cabeçalhos HTTP para a resposta que está sendo enviada para o cliente do gateway.

### <a name="policy-statement"></a>Declaração de política

```xml
<set-header name="header name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
</set-header>
```

### <a name="examples"></a>Exemplos

#### <a name="example---adding-header-override-existing"></a>Exemplo-adicionando cabeçalho, substituir existente

```xml
<set-header name="some header name" exists-action="override">
    <value>20</value>
</set-header>
```
#### <a name="example---removing-header"></a>Exemplo-removendo o cabeçalho

```xml
 <set-header name="some header name" exists-action="delete" />
```



#### <a name="forward-context-information-to-the-backend-service"></a>Encaminhar informações de contexto para o serviço de back-end
 Este exemplo mostra como aplicar a política no nível de API para fornecer informações de contexto ao serviço de back-end. Para ver uma demonstração de como configurar e usar essa política, confira o [episódio 177 do Cloud Cover: mais recursos de gerenciamento de API com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avança para 10:30. Em 12:10, há uma demonstração da chamada de uma operação no portal do desenvolvedor, onde você pode ver a política em funcionamento.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 Para obter mais informações, consulte [expressões de política](api-management-policy-expressions.md) e variável de [contexto](api-management-policy-expressions.md#ContextVariables).

> [!NOTE]
> Vários valores de um cabeçalho são concatenados a uma cadeia de caracteres CSV, por exemplo: `headerName: value1,value2,value3`
>
> As exceções incluem cabeçalhos padronizados, quais valores:
> - pode conter vírgulas (`User-Agent`, `WWW-Authenticate`, `Proxy-Authenticate`),
> - pode conter data (`Cookie`, `Set-Cookie`, `Warning`),
> - conter data (`Date`, `Expires`, `If-Modified-Since`, `If-Unmodified-Since`, `Last-Modified`, `Retry-After`).
>
> No caso dessas exceções, vários valores de cabeçalho não serão concatenados em uma cadeia de caracteres e serão passados como cabeçalhos separados, por exemplo: `User-Agent: value1`
>`User-Agent: value2`
>`User-Agent: value3`

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|definir cabeçalho|Elemento raiz.|Sim|
|valor|Especifica o valor do cabeçalho a definir. Para vários cabeçalhos com o mesmo nome, adicione elementos `value` adicionais.|Não|

### <a name="properties"></a>Propriedades

|Nome|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|Exists-ação|Especifica a ação a ser tomada quando o cabeçalho já estiver especificado. Esse atributo deve ter um dos valores a seguir.<br /><br /> -Override – substitui o valor do cabeçalho existente.<br />-Skip-não substitui o valor do cabeçalho existente.<br />-Append – acrescenta o valor ao valor de cabeçalho existente.<br />-Delete – remove o cabeçalho da solicitação.<br /><br /> Quando definido como `override` a listar várias entradas com os mesmos resultados de nome no cabeçalho que está sendo definido de acordo com todas as entradas (que serão listadas várias vezes); somente os valores listados serão definidos no resultado.|Não|Substituição|
|nome|Especifica o nome do cabeçalho a ser definido.|Sim|N/D|

### <a name="usage"></a>Utilização
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada, saída, back-end, em caso de erro

-   **Escopos de política:** todos os escopos

##  <a name="SetQueryStringParameter"></a>Definir parâmetro de cadeia de caracteres de consulta
 A política de `set-query-parameter` adiciona, substitui o valor ou exclui o parâmetro de cadeia de caracteres de consulta de solicitação. Pode ser usado para passar parâmetros de consulta esperados pelo serviço de back-end que são opcionais ou nunca estão presentes na solicitação.

### <a name="policy-statement"></a>Declaração de política

```xml
<set-query-parameter name="param name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
</set-query-parameter>
```

### <a name="examples"></a>Exemplos

#### <a name="example"></a>Exemplo

```xml

<set-query-parameter>
  <parameter name="api-key" exists-action="skip">
    <value>12345678901</value>
  </parameter>
  <!-- for multiple parameters with the same name add additional value elements -->
</set-query-parameter>

```

#### <a name="forward-context-information-to-the-backend-service"></a>Encaminhar informações de contexto para o serviço de back-end
 Este exemplo mostra como aplicar a política no nível de API para fornecer informações de contexto ao serviço de back-end. Para ver uma demonstração de como configurar e usar essa política, confira o [episódio 177 do Cloud Cover: mais recursos de gerenciamento de API com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avança para 10:30. Em 12:10, há uma demonstração da chamada de uma operação no portal do desenvolvedor, onde você pode ver a política em funcionamento.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 Para obter mais informações, consulte [expressões de política](api-management-policy-expressions.md) e variável de [contexto](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|set-query-parameter|Elemento raiz.|Sim|
|valor|Especifica o valor do parâmetro de consulta a definir. Para vários parâmetros de consulta com o mesmo nome, adicione elementos `value` adicionais.|Sim|

### <a name="properties"></a>Propriedades

|Nome|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|Exists-ação|Especifica a ação a realizar quando o parâmetro de consulta já está especificado. Esse atributo deve ter um dos valores a seguir.<br /><br /> -Override – substitui o valor do parâmetro existente.<br />-Skip-não substitui o valor do parâmetro de consulta existente.<br />-Append – acrescenta o valor ao valor do parâmetro de consulta existente.<br />-Delete – remove o parâmetro de consulta da solicitação.<br /><br /> Quando definido como `override` a listar várias entradas com os mesmos resultados de nome no parâmetro de consulta que está sendo definido de acordo com todas as entradas (que serão listadas várias vezes); somente os valores listados serão definidos no resultado.|Não|Substituição|
|nome|Especifica o nome do parâmetro de consulta a ser definido.|Sim|N/D|

### <a name="usage"></a>Utilização
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada, back-end

-   **Escopos de política:** todos os escopos

##  <a name="RewriteURL"></a>Reescrever URL
 A política de `rewrite-uri` converte uma URL de solicitação de seu formato público para o formulário esperado pelo serviço Web, conforme mostrado no exemplo a seguir.

- URL pública-`http://api.example.com/storenumber/ordernumber`

- URL da solicitação-`http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

  Essa política pode ser usada quando uma URL amigável e/ou fácil de navegador deve ser transformada no formato de URL esperado pelo serviço Web. Essa política só precisa ser aplicada ao expor um formato de URL alternativo, como URLs limpas, URLs RESTful, URLs amigáveis para o usuário ou URLs amigáveis para SEO que são URLs puramente estruturais que não contêm uma cadeia de caracteres de consulta e, em vez disso, contêm apenas o caminho do recurso ( após o esquema e a autoridade). Isso geralmente é feito para fins de estética, usabilidade ou otimização do mecanismo de pesquisa (SEO).

> [!NOTE]
>  Você só pode adicionar parâmetros de cadeia de caracteres de consulta usando a política. Você não pode adicionar parâmetros de caminho de modelo extras na URL de regravação.

### <a name="policy-statement"></a>Declaração de política

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
|rewrite-uri|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

|Atributo|Descrição|Necessário|Predefinição|
|---------------|-----------------|--------------|-------------|
|modelo|A URL do serviço Web real com qualquer parâmetro de cadeia de caracteres de consulta. Ao usar expressões, o valor inteiro deve ser uma expressão.|Sim|N/D|
|cópia-sem correspondência-params|Especifica se os parâmetros de consulta na solicitação de entrada que não estão presentes no modelo de URL original são adicionados à URL definida pelo modelo de regravação|Não|true|

### <a name="usage"></a>Utilização
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada

-   **Escopos de política:** todos os escopos

##  <a name="XSLTransform"></a>Transformar XML usando um XSLT
 A política de `Transform XML using an XSLT` aplica uma transformação XSL ao XML no corpo da solicitação ou da resposta.

### <a name="policy-statement"></a>Declaração de política

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
|xsl-transform|Elemento raiz.|Sim|
|meter|Usado para definir as variáveis usadas na transformação|Não|
|xsl: stylesheet|Elemento de folha de estilos raiz. Todos os elementos e atributos definidos no seguem a [especificação XSLT](https://www.w3.org/TR/xslt) padrão|Sim|

### <a name="usage"></a>Utilização
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada, saída

-   **Escopos de política:** todos os escopos

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte os seguintes tópicos:

+ [Políticas no gerenciamento de API](api-management-howto-policies.md)
+ [Referência de política](api-management-policy-reference.md) para uma lista completa de instruções de política e suas configurações
+ [Exemplos de política](policy-samples.md)
