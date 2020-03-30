---
title: Utilização do serviço de gestão da API para gerar pedidos HTTP
description: Aprenda a usar políticas de pedido e resposta na Gestão aPI para chamar serviços externos da sua API
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 4539c0fa-21ef-4b1c-a1d4-d89a38c242fa
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 1c86570850894a47f57a2d3587811411cc9a76eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190002"
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Utilização de serviços externos do serviço de Gestão API Azure
As políticas disponíveis no serviço de Gestão API Azure podem fazer um vasto leque de trabalhos úteis baseados exclusivamente no pedido de entrada, na resposta de saída e na informação básica de configuração. No entanto, poder interagir com os serviços externos a partir de políticas de Gestão API abre muitas mais oportunidades.

Já viu anteriormente como interagir com o [serviço Azure Event Hub para a exploração madeireira, monitorização e análise.](api-management-log-to-eventhub-sample.md) Este artigo demonstra políticas que lhe permitem interagir com qualquer serviço externo baseado em HTTP. Estas políticas podem ser usadas para desencadear eventos remotos ou para recuperar informações que são usadas para manipular o pedido e resposta original de alguma forma.

## <a name="send-one-way-request"></a>Pedido de envio de um ida
Possivelmente a interação externa mais simples é o estilo de pedido de fogo e esquecimento que permite que um serviço externo seja notificado de algum tipo de evento importante. A política `choose` de fluxo de controlo pode ser usada para detetar qualquer tipo de condição em que esteja interessado.  Se a condição estiver satisfeita, pode fazer um pedido http externo utilizando a política de pedido de envio de [um caminho.](/azure/api-management/api-management-advanced-policies#SendOneWayRequest) Isto pode ser um pedido para um sistema de mensagens como Hipchat ou Slack, ou um API de correio como SendGrid ou MailChimp, ou para incidentes de suporte crítico algo como PagerDuty. Todos estes sistemas de mensagens têm APIs http simples que podem ser invocados.

### <a name="alerting-with-slack"></a>Alertando com Slack
O exemplo que se segue demonstra como enviar uma mensagem para uma sala de chat Slack se o código de estado de resposta HTTP for maior ou igual a 500. Um erro de 500 alcanceindica um problema com a API de backend que o cliente da API não consegue resolver-se sozinho. Normalmente requer algum tipo de intervenção na parte da API Management.  

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>
```

Slack tem a noção de ganchos de teia de entrada. Ao configurar um gancho web de entrada, o Slack gera um URL especial, que lhe permite fazer um simples pedido de POST e passar uma mensagem para o canal Slack. O corpo JSON que cria baseia-se num formato definido pela Slack.

![Gancho de teia de slack](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>O fogo e o esquecimento são bons o suficiente?
Há certas trocas quando se usa um estilo de pedido de fogo e esquecimento. Se, por alguma razão, o pedido falhar, então a falha não será reportada. Nesta situação específica, a complexidade de ter um sistema secundário de reporte de falhas e o custo adicional de desempenho da espera da resposta não se justifica. Para cenários em que é essencial verificar a resposta, então a política [de envio de pedidos](/azure/api-management/api-management-advanced-policies#SendRequest) é uma melhor opção.

## <a name="send-request"></a>Pedido de envio
A `send-request` política permite que a utilização de um serviço externo execute funções de processamento complexas e devolva dados ao serviço de gestão da API que possam ser utilizados para um tratamento de políticas posterior.

### <a name="authorizing-reference-tokens"></a>Autorizar fichas de referência
Uma grande função da API Management é proteger os recursos de backend. Se o servidor de autorização utilizado pela sua API criar [fichas JWT](https://jwt.io/) como parte do seu fluxo OAuth2, como faz o [Azure Ative Directory,](../active-directory/hybrid/whatis-hybrid-identity.md) então pode utilizar a `validate-jwt` política para verificar a validade do símbolo. Alguns servidores de autorização criam os [chamados tokens](https://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) de referência que não podem ser verificados sem fazer uma chamada de volta para o servidor de autorização.

### <a name="standardized-introspection"></a>Introspeção padronizada
No passado, não houve uma forma padronizada de verificar um símbolo de referência com um servidor de autorização. No entanto, um [RFC 7662](https://tools.ietf.org/html/rfc7662) padrão recentemente proposto foi publicado pela IETF que define como um servidor de recursos pode verificar a validade de um símbolo.

### <a name="extracting-the-token"></a>Extraindo o símbolo
O primeiro passo é extrair o símbolo do cabeçalho de autorização. O valor cabeçalho deve `Bearer` ser formatado com o regime de autorização, um único espaço e, em seguida, o símbolo de autorização de acordo com [o RFC 6750](https://tools.ietf.org/html/rfc6750#section-2.1). Infelizmente, há casos em que o regime de autorização é omitido. Para ter em conta isso ao analisar, a API Management divide o valor do cabeçalho num espaço e seleciona a última corda da gama de cordas devolvida. Isto fornece uma salção de suposições para cabeçalhos de autorização mal formatados.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>Fazendo o pedido de validação
Assim que a API Management tiver o sinal de autorização, a API Management pode fazer o pedido de validação do token. O RFC 7662 chama a este `POST` processo de introspeção e requer um formulário HTML para o recurso de introspeção. O formulário HTML deve pelo menos conter um `token`par chave/valor com a tecla . Este pedido ao servidor de autorização também deve ser autenticado, para garantir que clientes maliciosos não podem ir à pesca por fichas válidas.

```xml
<send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
  <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
  <set-method>POST</set-method>
  <set-header name="Authorization" exists-action="override">
    <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
  </set-header>
  <set-header name="Content-Type" exists-action="override">
    <value>application/x-www-form-urlencoded</value>
  </set-header>
  <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
</send-request>
```

### <a name="checking-the-response"></a>Verificação da resposta
O `response-variable-name` atributo é usado para dar acesso à resposta devolvida. O nome definido nesta propriedade pode ser `context.Variables` usado como uma `IResponse` chave para o dicionário para aceder ao objeto.

A partir do objeto de resposta, você pode recuperar o corpo e RFC 7622 diz à API `active` Management que a resposta deve ser um objeto JSON e deve conter pelo menos uma propriedade chamada que é um valor booleano. Quando `active` é verdade, então o símbolo é considerado válido.

Alternativamente, se o servidor de autorização não incluir o campo "ativo" para indicar se o token é válido, utilize uma ferramenta como o Carteiro para determinar quais as propriedades definidas num símbolo válido. Por exemplo, se uma resposta simbólica válida contiver uma propriedade chamada "expires_in", verifique se este nome de propriedade existe na resposta do servidor de autorização desta forma:

<quando condição="@((iResponse)contexto. Variáveis["tokenstate"]). Body.As<JObject>(). Propriedade("expires_in") == nulo)">

### <a name="reporting-failure"></a>Falha de reporte
Pode utilizar `<choose>` uma política para detetar se o token é inválido e, em caso afirmativo, devolver uma resposta 401.

```xml
<choose>
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
    <return-response response-variable-name="existing response variable">
      <set-status code="401" reason="Unauthorized" />
      <set-header name="WWW-Authenticate" exists-action="override">
        <value>Bearer error="invalid_token"</value>
      </set-header>
    </return-response>
  </when>
</choose>
```

De acordo com o [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) que descreve como `bearer` devem `WWW-Authenticate` ser utilizados tokens, a API Management também devolve um cabeçalho com a resposta 401. O WWW-Authenticate destina-se a instruir um cliente sobre como construir um pedido devidamente autorizado. Devido à grande variedade de abordagens possíveis com o quadro OAuth2, é difícil comunicar todas as informações necessárias. Felizmente, estão em curso esforços para ajudar [os clientes a descobrir como autorizar adequadamente os pedidos a um servidor](https://tools.ietf.org/html/draft-jones-oauth-discovery-00)de recursos.

### <a name="final-solution"></a>Solução final
No final, obtém-se a seguinte política:

```xml
<inbound>
  <!-- Extract Token from Authorization header parameter -->
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

  <!-- Send request to Token Server to validate token (see RFC 7662) -->
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
    <set-method>POST</set-method>
    <set-header name="Authorization" exists-action="override">
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
    </set-header>
    <set-header name="Content-Type" exists-action="override">
      <value>application/x-www-form-urlencoded</value>
    </set-header>
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
  </send-request>

  <choose>
          <!-- Check active property in response -->
          <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
              <!-- Return 401 Unauthorized with http-problem payload -->
              <return-response response-variable-name="existing response variable">
                  <set-status code="401" reason="Unauthorized" />
                  <set-header name="WWW-Authenticate" exists-action="override">
                      <value>Bearer error="invalid_token"</value>
                  </set-header>
              </return-response>
          </when>
      </choose>
  <base />
</inbound>
```

Este é apenas um dos `send-request` muitos exemplos de como a política pode ser usada para integrar serviços externos úteis no processo de pedidos e respostas que fluem através do serviço de Gestão da API.

## <a name="response-composition"></a>Composição de resposta
A `send-request` política pode ser usada para melhorar um pedido primário para um sistema de backend, como viu no exemplo anterior, ou pode ser usado como uma substituição completa para a chamada de backend. Utilizando esta técnica, pode facilmente criar recursos compósitos que são agregados a partir de vários sistemas diferentes.

### <a name="building-a-dashboard"></a>Construção de um painel de instrumentos
Por vezes, queremos ser capazes de expor informações que existem em múltiplos sistemas de backend, por exemplo, para conduzir um dashboard. Os KPIs vêm de todos os back-ends, mas prefere não fornecer acesso direto aos mesmos e seria bom que toda a informação pudesse ser recuperada num único pedido. Talvez algumas informações de backend precisam de alguns cortes e dicing e um pouco de sanitizing primeiro! Ser capaz de cache esse recurso composto seria útil para reduzir a carga de backend, uma vez que sabe que os utilizadores têm o hábito de martelar a chave F5 para ver se as suas métricas de baixo desempenho podem mudar.    

### <a name="faking-the-resource"></a>Fingindo o recurso
O primeiro passo para a construção do recurso do painel de instrumentos é configurar uma nova operação no portal Azure. Esta é uma operação de espaço reservado usada para configurar uma política de composição para construir o recurso dinâmico.

![Operação do painel de instrumentos](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Fazer os pedidos
Uma vez criada a operação, pode configurar uma política especificamente para essa operação. 

![Operação do painel de instrumentos](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

O primeiro passo é extrair quaisquer parâmetros de consulta do pedido de entrada, para que possa encaminhar para o backend. Neste exemplo, o painel de instrumentos está a mostrar `fromDate` informações com base num período de tempo e, portanto, tem um e `toDate` um parâmetro. Pode utilizar `set-variable` a política para extrair a informação do URL de pedido.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

Assim que tiver esta informação, pode fazer pedidos a todos os sistemas de backend. Cada pedido constrói um novo URL com a informação do parâmetro e chama o respetivo servidor e armazena a resposta numa variável de contexto.

```xml
<send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
  <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
  <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>
```

Estes pedidos executam em sequência, o que não é o ideal. 

### <a name="responding"></a>Resposta
Para construir a resposta composta, pode utilizar a política de resposta ao [retorno.](/azure/api-management/api-management-advanced-policies#ReturnResponse) O `set-body` elemento pode usar uma `JObject` expressão para construir um novo com todas as representações do componente incorporadas como propriedades.

```xml
<return-response response-variable-name="existing response variable">
  <set-status code="200" reason="OK" />
  <set-header name="Content-Type" exists-action="override">
    <value>application/json</value>
  </set-header>
  <set-body>
    @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                  new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                  new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                  new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                  ).ToString())
  </set-body>
</return-response>
```

A política completa é a seguinte:

```xml
<policies>
    <inbound>

  <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
  <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
</policies>
```

Na configuração do funcionamento do espaço reservado, pode configurar o recurso do painel de instrumentos a ser colocado durante pelo menos uma hora. 

## <a name="summary"></a>Resumo
O serviço azure API Management fornece políticas flexíveis que podem ser aplicadas seletivamente ao tráfego HTTP e permite a composição dos serviços de backend. Quer queira melhorar o seu portal de api com funções de alerta, verificação, capacidades `send-request` de validação ou criar novos recursos compósitos baseados em múltiplos serviços de backend, as políticas e políticas relacionadas abrem um mundo de possibilidades.

