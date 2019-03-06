---
title: Políticas de transformação de gestão de API do Azure | Documentos da Microsoft
description: Saiba mais sobre as políticas de transformação disponíveis para utilização na gestão de API do Azure.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 4e7af92ed0ce04bb14bd49c24de4928baa4f00ec
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448072"
---
# <a name="api-management-transformation-policies"></a>Políticas de transformação de gestão de API
Este tópico fornece uma referência para as seguintes políticas de gestão de API. Para informações sobre como adicionar e configurar as políticas, consulte [políticas de gestão de API](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="TransformationPolicies"></a> Políticas de transformação

-   [Converter o JSON em XML](api-management-transformation-policies.md#ConvertJSONtoXML) - converte pedido ou corpo de resposta de JSON em XML.

-   [Converter o XML em JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) - converte pedido ou corpo de resposta do XML para JSON.

-   [Localizar e substituir a cadeia de caracteres no corpo](api-management-transformation-policies.md#Findandreplacestringinbody) – localiza uma subcadeia de solicitação ou resposta e o substitui por uma subcadeia diferente.

-   [Mascarar URLs no conteúdo](api-management-transformation-policies.md#MaskURLSContent) -ligações reescreverá (máscaras) na resposta body, de modo a que apontam para a ligação equivalente através do gateway.

-   [Defina o serviço de back-end](api-management-transformation-policies.md#SetBackendService) -altera o serviço de back-end para uma solicitação de entrada.

-   [Definir corpo](api-management-transformation-policies.md#SetBody) -define o corpo da mensagem para pedidos de entrada e saídos.

-   [Definir o cabeçalho HTTP](api-management-transformation-policies.md#SetHTTPheader) - atribui um valor a uma resposta existente e/ou o cabeçalho do pedido ou adiciona um novo cabeçalho de resposta e/ou a pedido.

-   [Defina o parâmetro de cadeia de caracteres de consulta](api-management-transformation-policies.md#SetQueryStringParameter) - adiciona, substitui o valor de ou elimina o parâmetro de cadeia de caracteres de consulta do pedido.

-   [Reescrever URL](api-management-transformation-policies.md#RewriteURL) -converte um URL do pedido de sua forma pública para o formato esperado pelo serviço web.

-   [Transformar XML usando um XSLT](api-management-transformation-policies.md#XSLTransform) -aplica-se uma transformação XSL para XML no corpo da solicitação ou resposta.

##  <a name="ConvertJSONtoXML"></a> Converter o JSON em XML
 O `json-to-xml` política converte um corpo de solicitação ou resposta JSON em XML.

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

|Name|Descrição|Necessário|
|----------|-----------------|--------------|
|json-to-xml|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

|Name|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|aplicar|O atributo tem de ser definido para um dos seguintes valores.<br /><br /> Aplique - sempre - sempre a conversão.<br />converter o - json de tipo de conteúdo - apenas se o cabeçalho de resposta Content-Type indica a presença de JSON.|Sim|N/A|
|consider-accept-header|O atributo tem de ser definido para um dos seguintes valores.<br /><br /> aplica - verdadeiro - conversão se JSON é solicitado no pedido. o cabeçalho Accept.<br />-FALSO - aplique sempre conversão.|Não|true|
|data de análise|Quando definido como `false` valores de data simplesmente são copiados durante a transformação|Não|true|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** entrada, saída, no caso de erro

-   **Âmbitos de política:** global, produto, API, operação

##  <a name="ConvertXMLtoJSON"></a> Converter o XML em JSON
 O `xml-to-json` política converte um corpo de solicitação ou resposta de XML para JSON. Esta política pode ser utilizada para modernizar as APIs com base em serviços da web de back-end só de XML.

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

|Name|Descrição|Necessário|
|----------|-----------------|--------------|
|xml-to-json|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

|Name|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|tipo|O atributo tem de ser definido para um dos seguintes valores.<br /><br /> javascript-amigável - o JSON convertido tem uma forma amigável aos desenvolvedores JavaScript.<br />-direto - o JSON convertido reflete a estrutura do documento XML original.|Sim|N/A|
|aplicar|O atributo tem de ser definido para um dos seguintes valores.<br /><br /> -sempre - converta sempre.<br />converter o-conteúdo tipo-xml - apenas se o cabeçalho de resposta Content-Type indica a presença de XML.|Sim|N/A|
|consider-accept-header|O atributo tem de ser definido para um dos seguintes valores.<br /><br /> aplica - verdadeiro - conversão se XML é solicitado no pedido. o cabeçalho Accept.<br />-FALSO - aplique sempre conversão.|Não|true|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** entrada, saída, no caso de erro

-   **Âmbitos de política:** global, produto, API, operação

##  <a name="Findandreplacestringinbody"></a> Localizar e substituir a cadeia de caracteres no corpo
 O `find-and-replace` política localiza uma subcadeia de solicitação ou resposta e o substitui por uma subcadeia diferente.

### <a name="policy-statement"></a>Declaração de política

```xml
<find-and-replace from="what to replace" to="replacement" />
```

### <a name="example"></a>Exemplo

```xml
<find-and-replace from="notebook" to="laptop" />
```

### <a name="elements"></a>Elementos

|Name|Descrição|Necessário|
|----------|-----------------|--------------|
|localizar e substituir|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

|Name|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|de|A cadeia a procurar.|Sim|N/A|
|para|A cadeia de substituição. Especifique uma zero cadeia de substituição de comprimento para remover a cadeia de procura.|Sim|N/A|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** entrada, saída, back-end, no caso de erro

-   **Âmbitos de política:** global, produto, API, operação

##  <a name="MaskURLSContent"></a> URLs de máscara no conteúdo
 O `redirect-content-urls` política reescreverá (máscaras) links no corpo da resposta, de modo a que apontam para a ligação equivalente através do gateway. Utilize a secção de saída seja para preciso reescrever links de corpo de resposta para torná-los a apontar para o gateway. Utilize a secção de entrada para um efeito oposto.

> [!NOTE]
>  Esta política não altera quaisquer valores de cabeçalho como `Location` cabeçalhos. Para alterar os valores de cabeçalho, utilize o [definição de cabeçalho](api-management-transformation-policies.md#SetHTTPheader) política.

### <a name="policy-statement"></a>Declaração de política

```xml
<redirect-content-urls />
```

### <a name="example"></a>Exemplo

```xml
<redirect-content-urls />
```

### <a name="elements"></a>Elementos

|Name|Descrição|Necessário|
|----------|-----------------|--------------|
|redirect-content-urls|Elemento raiz.|Sim|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** entrada, saída

-   **Âmbitos de política:** global, produto, API, operação

##  <a name="SetBackendService"></a> Serviço de back-end de conjunto
 Utilize o `set-backend-service` política para redirecionar uma solicitação de entrada para um back-end diferente daquela especificado nas definições de API para essa operação. Esta política altera o URL de base de serviço de back-end do pedido a receber para aquele especificado na política.

### <a name="policy-statement"></a>Declaração de política

```xml
<set-backend-service base-url="base URL of the backend service" />
```

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
Neste exemplo definir a política de serviço de back-end encaminha os pedidos com base no valor de versão transmitido na cadeia de consulta para um serviço de back-end diferente daquela especificado na API.

Inicialmente o URL base do serviço de back-end é derivado das definições de API. Portanto, o URL do pedido `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` torna-se `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` onde `http://contoso.com/api/10.4/` é o URL do serviço de back-end especificado nas definições de API.

Quando o [< escolher\> ](api-management-advanced-policies.md#choose) declaração de política é aplicada a URL base do serviço de back-end pode mudar novamente para `http://contoso.com/api/8.2` ou `http://contoso.com/api/9.1`, consoante o valor do parâmetro de consulta do pedido de versão. Por exemplo, se o valor é `"2013-15"` final pedido torna-se de URL `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`.

Se ainda mais a transformação do pedido é desejada, outros [políticas de transformação](api-management-transformation-policies.md#TransformationPolicies) pode ser utilizado. Por exemplo, para remover o parâmetro de consulta de versão, agora que o pedido está a ser encaminhado para um versão específica back-end, o [defina o parâmetro de cadeia de caracteres de consulta](api-management-transformation-policies.md#SetQueryStringParameter) política pode ser utilizada para remover o atributo de versão agora redundante.

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
Neste exemplo de política encaminha o pedido para um serviço fabric back-end, utilizar a cadeia de caracteres de consulta do ID de utilizador como a chave de partição e utilizar a réplica primária da partição.

### <a name="elements"></a>Elementos

|Name|Descrição|Necessário|
|----------|-----------------|--------------|
|set-backend-service|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

|Name|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|base-url|Novo back-end base URL do serviço.|Não|N/A|
|id de back-end|Identificador do back-end para encaminhar para.|Não|N/A|
|sf-partition-key|Apenas aplicável quando o back-end é um serviço do Service Fabric e é especificado com o "back-end-id". Utilizado para resolver uma partição específica do serviço de resolução do nome.|Não|N/A|
|sf-replica-type|Apenas aplicável quando o back-end é um serviço do Service Fabric e é especificado com o "back-end-id". Controla se o pedido deve ir para a réplica primária ou secundária de uma partição. |Não|N/A|
|sf-resolve-condition|Apenas aplicável quando o back-end é um serviço do Service Fabric. Identificando se a chamada ao back-end do Service Fabric tem de ser repetido com a nova resolução de condição.|Não|N/A|
|sf-service-instance-name|Apenas aplicável quando o back-end é um serviço do Service Fabric. Permite alterar as instâncias do serviço em tempo de execução. |Não|N/A|
|sf-listener-name|Apenas aplicável quando o back-end é um serviço do Service Fabric e é especificado com o "back-end-id". Service Fabric Reliable Services permite-lhe criar várias escutas num serviço. Este atributo é utilizado para selecionar um serviço de escuta específico, quando um serviço fiável de back-end tem mais do que um serviço de escuta. Se este atributo não for especificado, a gestão de API irá tentar utilizar um serviço de escuta sem um nome. Sem um nome de um serviço de escuta é normal Reliable Services que ter apenas um serviço de escuta. |Não|N/A|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** entrada, de back-end

-   **Âmbitos de política:** global, produto, API, operação

##  <a name="SetBody"></a> Definir corpo
 Utilize o `set-body` política para definir o corpo da mensagem para pedidos de entrada e saídos. Para acessar o corpo da mensagem que pode utilizar o `context.Request.Body` propriedade ou o `context.Response.Body`, dependendo se a política está na seção de entrada ou saída.

> [!IMPORTANT]
>  Tenha em atenção que, por predefinição, quando acessa a mensagem, body usando `context.Request.Body` ou `context.Response.Body`, o corpo da mensagem original foi perdido e tem de ser definido por meio do retorno o corpo de volta na expressão. Para preservar o conteúdo do corpo, defina o `preserveContent` parâmetro `true` ao aceder a mensagem. Se `preserveContent` está definido como `true` e um corpo de diferente é devolvido pela expressão, o corpo retornado é utilizado.
>
>  Tenha em atenção as seguintes considerações ao utilizar o `set-body` política.
>
>  -   Se estiver a utilizar o `set-body` política para retornar um corpo de novo ou atualizado, não precisa de definir `preserveContent` para `true` porque fornecidos explicitamente o novo conteúdo do corpo.
> -   Preservar o conteúdo de uma resposta no pipeline de entrada não faz sentido, porque não existe nenhuma resposta ainda.
> -   Preservar o conteúdo de um pedido no pipeline de saída não faz sentido, porque o pedido já foi enviado para o back-end neste momento.
> -   Se esta política é utilizada quando não existe nenhum corpo de mensagem, por exemplo num GET de entrada, é emitida uma exceção.

 Para obter mais informações, consulte a `context.Request.Body`, `context.Response.Body`e o `IMessage` secções o [variável de contexto](api-management-policy-expressions.md#ContextVariables) tabela.

### <a name="policy-statement"></a>Declaração de política

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Exemplos

#### <a name="literal-text-example"></a>Exemplo de texto literal

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Exemplo de acessar o corpo como uma cadeia de caracteres. Tenha em atenção são preservando o corpo do pedido original para que podemos pode acessá-lo mais tarde no pipeline.

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

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accessing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Exemplo de acessar o corpo como um JObject. Tenha em atenção que, uma vez que estamos não está a reservar o corpo do pedido original, a aceder ao mesmo mais tarde no pipeline resultará numa exceção.

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

#### <a name="filter-response-based-on-product"></a>Resposta de filtro com base no produto
 Este exemplo mostra como realizar a filtragem de conteúdo, removendo elementos de dados da resposta recebida do serviço de back-end ao utilizar o `Starter` produto. Para uma demonstração de configurar e utilizar esta política, consulte [177 do Cloud Cover episódio: Mais funcionalidades de gestão de API com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avanço rápido para 34:30. Iniciar o 31:50 ver uma descrição geral dos [a escuro céu previsão API](https://developer.forecast.io/) utilizado para esta demonstração.

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

### <a name="using-liquid-templates-with-set-body"></a>Utilizar modelos de fluidos com corpo de conjunto
O `set-body` política pode ser configurada para utilizar o [líquidos](https://shopify.github.io/liquid/basics/introduction/) linguagem de modelagem para transformar o corpo de uma solicitação ou resposta. Isso pode ser muito eficaz se de que precisa de reformatar completamente o formato da mensagem.

> [!IMPORTANT]
> A implementação de Liquid usada no `set-body` política está configurada no modo c#. Isso é particularmente importante para fazer certas coisas, como a filtragem. Por exemplo, utilizando um filtro de data requer a utilização de Pascal casing e c# data por exemplo, a formatação:
>
> {{body.foo.startDateTime| Date:"yyyyMMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> Para vincular corretamente para um corpo XML usando o modelo de fluidos, usar um `set-header` política para definir o tipo de conteúdo para a aplicação/xml, texto/xml (ou qualquer tipo que termina com + xml); para um corpo JSON, tem de ser application/json, texto/json (ou qualquer tipo que termina com + JSON).

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>Converter o JSON para SOAP através de um modelo de líquidos
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

#### <a name="transform-json-using-a-liquid-template"></a>Transformar JSON através de um modelo de líquidos
```xml
{
"order": {
    "id": "{{body.customer.purchase.identifier}}",
    "summary": "{{body.customer.purchase.orderShortDesc}}"
    }
}
```

### <a name="elements"></a>Elementos

|Name|Descrição|Necessário|
|----------|-----------------|--------------|
|Definir corpo|Elemento raiz. Contém o texto de corpo ou um expressões que retorna um corpo.|Sim|

### <a name="properties"></a>Propriedades

|Name|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|modelo|Utilizado para alterar o modo de modelos que a política de corpo de conjunto será executada no. Atualmente o único valor suportado é:<br /><br />-líquidos - definir a política de corpo irá utilizar o mecanismo de modelagem de líquidos |Não|Liquid|

Para aceder a informações sobre o pedido e resposta, o modelo de fluidos pode vincular a um objeto de contexto com as seguintes propriedades: <br />
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
 Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** entrada, saída, back-end

-   **Âmbitos de política:** global, produto, API, operação

##  <a name="SetHTTPheader"></a> Definir o cabeçalho HTTP
 O `set-header` atribui um valor a uma resposta existente e/ou o cabeçalho do pedido de política ou adiciona um novo cabeçalho de resposta e/ou a pedido.

 Insere uma lista de cabeçalhos HTTP numa mensagem de HTTP. Quando colocado num pipeline de entrada, esta política define os cabeçalhos HTTP para o pedido que está sendo passada para o serviço de destino. Quando colocado num pipeline de saída, esta política define os cabeçalhos HTTP para a resposta a ser enviados para o cliente do gateway.

### <a name="policy-statement"></a>Declaração de política

```xml
<set-header name="header name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
</set-header>
```

### <a name="examples"></a>Exemplos

#### <a name="example"></a>Exemplo

```xml
<set-header name="some header name" exists-action="override">
    <value>20</value>
</set-header>
```

#### <a name="forward-context-information-to-the-backend-service"></a>Reencaminhar as informações de contexto para o serviço de back-end
 Este exemplo mostra como aplicar a política ao nível da API para fornecer informações de contexto para o serviço de back-end. Para uma demonstração de configurar e utilizar esta política, consulte [177 do Cloud Cover episódio: Mais funcionalidades de gestão de API com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avanço rápido para 10:30. Em 12:10 é uma demonstração de chamar uma operação no portal do programador, onde pode ver a política no trabalho.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 Para obter mais informações, consulte [expressões de diretriz](api-management-policy-expressions.md) e [variável de contexto](api-management-policy-expressions.md#ContextVariables).

> [!NOTE]
> Vários valores de um cabeçalho são concatenados numa cadeia CSV, por exemplo:  
> `headerName: value1,value2,value3`
>
> As exceções incluem cabeçalhos padronizados, quais valores:
> - pode conter vírgulas (`User-Agent`, `WWW-Authenticate`, `Proxy-Authenticate`),
> - pode conter data (`Cookie`, `Set-Cookie`, `Warning`),
> - conter data (`Date`, `Expires`, `If-Modified-Since`, `If-Unmodified-Since`, `Last-Modified`, `Retry-After`).
>
> Em caso dessas exceções, vários valores de cabeçalho não vão ser concatenados numa cadeia e serão passados como cabeçalhos separados, por exemplo:  
>`User-Agent: value1`  
>`User-Agent: value2`  
>`User-Agent: value3`

### <a name="elements"></a>Elementos

|Name|Descrição|Necessário|
|----------|-----------------|--------------|
|set-header|Elemento raiz.|Sim|
|valor|Especifica o valor do cabeçalho de ser definido. Para adicionar vários cabeçalhos com o mesmo nome `value` elementos.|Sim|

### <a name="properties"></a>Propriedades

|Name|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|ação existe|Especifica a ação a tomar quando o cabeçalho já está especificado. Este atributo tem de ter um dos seguintes valores.<br /><br /> -Ignorar - substitui o valor do cabeçalho existente.<br />-skip - não substitui o valor de cabeçalho existente.<br />-Acrescentar - acrescenta o valor para o valor de cabeçalho existente.<br />-delete - remove o cabeçalho do pedido.<br /><br /> Quando definido como `override` Transaction múltiplas entradas com o mesmo nome resulta no cabeçalho que está a ser definido de acordo com todas as entradas (que serão listadas várias vezes); apenas valores listados serão definidos no resultado.|Não|substituir|
|nome|Especifica o nome do cabeçalho de ser definido.|Sim|N/A|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** entrada, saída, back-end, no caso de erro

-   **Âmbitos de política:** global, produto, API, operação

##  <a name="SetQueryStringParameter"></a> Parâmetro de cadeia de caracteres de consulta de conjunto
 O `set-query-parameter` política adiciona, substitui o valor de, ou parâmetro de cadeia de caracteres de consulta do pedido de exclusões. Pode ser usado para passar parâmetros esperado pelo serviço de back-end que são opcionais de consulta ou nunca presentes no pedido.

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

#### <a name="forward-context-information-to-the-backend-service"></a>Reencaminhar as informações de contexto para o serviço de back-end
 Este exemplo mostra como aplicar a política ao nível da API para fornecer informações de contexto para o serviço de back-end. Para uma demonstração de configurar e utilizar esta política, consulte [177 do Cloud Cover episódio: Mais funcionalidades de gestão de API com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avanço rápido para 10:30. Em 12:10 é uma demonstração de chamar uma operação no portal do programador, onde pode ver a política no trabalho.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 Para obter mais informações, consulte [expressões de diretriz](api-management-policy-expressions.md) e [variável de contexto](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementos

|Name|Descrição|Necessário|
|----------|-----------------|--------------|
|set-query-parameter|Elemento raiz.|Sim|
|valor|Especifica o valor do parâmetro de consulta seja definido. Para adicionar vários parâmetros de consulta com o mesmo nome `value` elementos.|Sim|

### <a name="properties"></a>Propriedades

|Name|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|ação existe|Especifica a ação a tomar quando o parâmetro de consulta já foi especificado. Este atributo tem de ter um dos seguintes valores.<br /><br /> -Ignorar - substitui o valor do parâmetro existente.<br />-skip - não substitui o valor de parâmetro de consulta existente.<br />-Acrescentar - acrescenta o valor para o valor de parâmetro de consulta existente.<br />-delete - remove o parâmetro de consulta no pedido.<br /><br /> Quando definido como `override` Transaction múltiplas entradas com o mesmo nome resulta no parâmetro de consulta que está a ser definido, de acordo com todas as entradas (que serão listadas várias vezes); apenas valores listados serão definidos no resultado.|Não|substituir|
|nome|Especifica o nome do parâmetro de consulta seja definido.|Sim|N/A|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** entrada, de back-end

-   **Âmbitos de política:** global, produto, API, operação

##  <a name="RewriteURL"></a> Reescrever URL
 O `rewrite-uri` política converte um URL do pedido de sua forma pública para o formato esperado pelo serviço web, conforme mostrado no exemplo a seguir.

-   URL pública- `http://api.example.com/storenumber/ordernumber`

-   URL do pedido- `http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

 Esta política pode ser utilizada quando um URL humano e/ou navegador amigável deva ser transformado em formato de URL esperado pelo serviço web. Esta política só tem de ser aplicadas ao expor um formato de URL alternativo, como URLs limpas, RESTful URLs, fácil de utilizar URLs ou URLs que permitem SEO que são puramente estruturais URLs que não contenham uma cadeia de consulta e em vez disso, contêm apenas o caminho do recurso ( após o esquema e a autoridade). Isso é geralmente feito estética, usabilidade ou mecanismo de pesquisa fins de otimização (SEO).

> [!NOTE]
>  Só é possível adicionar parâmetros de cadeia de caracteres de consulta através da política. Não é possível adicionar parâmetros de caminho de modelo extra no rewrite URL.

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

|Name|Descrição|Necessário|
|----------|-----------------|--------------|
|rewrite-uri|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

|Atributo|Descrição|Necessário|Predefinição|
|---------------|-----------------|--------------|-------------|
|modelo|O URL do serviço web reais com quaisquer parâmetros de cadeia de caracteres de consulta. Quando utilizar expressões, o valor inteiro tem de ser uma expressão.|Sim|N/A|
|copy-unmatched-params|Especifica se os parâmetros de consulta no pedido de entrada não está presente no modelo de URL original são adicionados para a URL definida pelo modelo de escrever novamente|Não|true|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** entrada

-   **Âmbitos de política:** global, produto, API, operação

##  <a name="XSLTransform"></a> Transformar XML usando um XSLT
 O `Transform XML using an XSLT` política se aplica uma transformação XSL XML no corpo da solicitação ou resposta.

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

|Name|Descrição|Necessário|
|----------|-----------------|--------------|
|xsl-transform|Elemento raiz.|Sim|
|parâmetro|Utilizado para definir variáveis usadas na transformação|Não|
|xsl:stylesheet|Elemento de folha de estilo de raiz. Todos os elementos e atributos definidos no siga o padrão [especificação XSLT](https://www.w3.org/TR/xslt)|Sim|

### <a name="usage"></a>Utilização
 Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **As secções de política:** entrada, saída

-   **Âmbitos de política:** global, produto, API, operação

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte os seguintes tópicos:

+ [Políticas de gestão de API](api-management-howto-policies.md)
+ [Referência de política](api-management-policy-reference.md) para uma lista completa das declarações de política e suas configurações
+ [Exemplos de política](policy-samples.md)
