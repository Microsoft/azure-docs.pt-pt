---
title: Utilizar o serviço de gestão de API para gerar pedidos de HTTP
description: Saiba como utilizar políticas de pedido e resposta na gestão de API para chamar serviços externos a partir de sua API
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
ms.openlocfilehash: adb7329249570750002f04fb72465698f869afdc
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58792489"
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Utilizar serviços externos do serviço de gestão de API do Azure
As políticas disponíveis no serviço de gestão de API do Azure podem fazer uma grande variedade de trabalho úteis com base exclusivamente na solicitação de entrada, a resposta de saída e informações básicas de configuração. No entanto, a capacidade de interagir com serviços externos da gestão de API políticas abre muitas oportunidades de mais.

Viu anteriormente como interagir com o [serviço do Hub de eventos do Azure para o registo, monitorização e análise](api-management-log-to-eventhub-sample.md). Este artigo demonstra as políticas que permitem-lhe interagir com qualquer serviço de acesso baseado em HTTP externo. Estas políticas podem ser utilizadas para acionar eventos remotos ou para recuperação das informações que são utilizadas para manipular a solicitação original e a resposta de alguma forma.

## <a name="send-one-way-request"></a>Forma-pedido de envio-um
Possivelmente a interação externa mais simples é o estilo de disparar e esquecer de mensagens em fila de pedido que permite que um serviço externo ser notificado de algum tipo de evento importante. A política de controlo de fluxo `choose` pode ser utilizado para detetar qualquer tipo de condição que está interessado.  Se a condição for satisfeita, pode fazer um externo através de pedido HTTP a [forma-pedido de envio-um](/azure/api-management/api-management-advanced-policies#SendOneWayRequest) política. Isto pode ser um pedido para um sistema de mensagens, como o Hipchat ou Slack ou um API de correio como o SendGrid ou o MailChimp, ou para incidentes de suporte críticos algo como o PagerDuty. Todos estes sistemas de mensagens têm as APIs de HTTP simples que podem ser invocadas.

### <a name="alerting-with-slack"></a>Alertas com Slack
O exemplo seguinte demonstra como enviar uma mensagem para uma sala de bate-papo Slack se o código de estado de resposta HTTP é maior que ou igual a 500. Um erro de intervalo de 500 indica um problema com o back-end API que o cliente da API não é possível resolver si próprios. Normalmente requer algum tipo de intervenção da parte da gestão de API.  

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

Slack tem a noção de webhooks de entrada. Quando configurar um hook de web de entrada, Slack gera uma URL especial, o que permite-lhe para fazer um pedido POST simple e passar uma mensagem para o canal Slack. O corpo JSON que criar se baseia num formato definido pelo Slack.

![Hook de Slack Web](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>É disparar e esquecer suficientemente bem?
Existem determinadas vantagens e desvantagens em usar um estilo de disparar e esquecer de mensagens em fila de pedido. Se, por algum motivo, o pedido falhar, em seguida, a falha não é será possível reportar. Nesta situação específica, a complexidade de ter uma falha secundária reporting system e o custo de desempenho adicionais de aguardar a resposta não é garantida. Para cenários em que é essencial para verificar a resposta, o [pedido de envio](/azure/api-management/api-management-advanced-policies#SendRequest) política é uma opção melhor.

## <a name="send-request"></a>Pedido de envio
O `send-request` ativa de política com um serviço externo para realizar funções de processamento complexo e retornar dados para a gestão de API de serviço que pode ser utilizada para processamento adicional da política.

### <a name="authorizing-reference-tokens"></a>Autorizar os tokens de referência
Uma função importante da gestão de API está a proteger os recursos de back-end. Se o servidor de autorização utilizado pela sua API cria [JWT tokens](https://jwt.io/) como parte do seu fluxo de OAuth2, como [Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md) faz, em seguida, pode utilizar o `validate-jwt` política para verificar a validade do token. Alguns servidores de autorização criar os chamados [referenciar tokens](https://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) que não é possível verificar sem fazer uma chamada de retorno para o servidor de autorização.

### <a name="standardized-introspection"></a>Introspeção padronizada
No passado, não houve nenhuma forma padronizada de verificar um token de referência com um servidor de autorização. No entanto um padrão proposto recentemente [RFC 7662](https://tools.ietf.org/html/rfc7662) foi publicado pelo IETF que define como um servidor de recurso pode verificar a validade de um token.

### <a name="extracting-the-token"></a>Extrair o token
O primeiro passo é extrair o token do cabeçalho de autorização. O valor de cabeçalho deve ser formatado com o `Bearer` esquema de autorização, um único espaço e, em seguida, o token de autorização, de acordo [RFC 6750](https://tools.ietf.org/html/rfc6750#section-2.1). Infelizmente há casos em que o esquema de autorização é omitido. Para justificar isso durante a análise, gestão de API divide o valor do cabeçalho num espaço e seleciona a cadeia de caracteres última matriz de cadeias de caracteres retornada. Isso fornece uma solução alternativa para cabeçalhos de autorização incorretamente formatado.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>A efetuar o pedido de validação
Assim que a gestão de API tem o token de autorização, gestão de API pode fazer o pedido para validar o token. RFC 7662 chama introspeção este processo e requer que `POST` um formulário HTML para o recurso de introspeção. O formulário HTML tem de conter, pelo menos, um par chave/valor com a chave `token`. Este pedido para o servidor de autorização tem também de ser autenticado, para garantir que os clientes maliciosos não é possível ir trawling para tokens válidos.

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

### <a name="checking-the-response"></a>A verificar a resposta
O `response-variable-name` atributo é utilizado para conceder acesso a resposta retornada. Nome definido nesta propriedade pode ser utilizado como uma chave para o `context.Variables` dicionário para acessar o `IResponse` objeto.

Do objeto de resposta, pode recuperar o corpo e RFC 7622 informa sobre a gestão de API que a resposta tem de ser um objeto JSON e tem de conter, pelo menos, uma propriedade chamada `active` isto é um valor booleano. Quando `active` é verdade, em seguida, o token é considerado válido.

### <a name="reporting-failure"></a>Falha de geração de relatórios
Pode utilizar um `<choose>` política para detetar se o token é inválido e nesse caso, retornar uma resposta 401.

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

De acordo [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) que descreve como `bearer` tokens devem ser usados, gestão de API também devolve um `WWW-Authenticate` cabeçalho com a resposta 401. O WWW-Authenticate destina-se para instruir um cliente sobre como construir um pedido devidamente autorizado. Devido à grande variedade de abordagens possíveis com o framework de OAuth2, é difícil comunicar todas as informações necessárias. Felizmente, existem os esforços em curso para o ajudar a [clientes descobrir como autorizar corretamente os pedidos para um servidor de recurso](https://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Solução final
No final, obterá a seguinte política:

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

Esse é apenas um dos muitos exemplos de como o `send-request` política pode ser utilizada para integrar serviços externos útil ao processo de pedidos e respostas que flui através do serviço de gestão de API.

## <a name="response-composition"></a>Composição de resposta
O `send-request` política pode ser utilizada para melhorar a um pedido principal para um sistema de back-end, como viu no exemplo anterior, ou pode ser usado como uma substituição completa para a chamada de back-end. Usando essa técnica pode facilmente criar recursos compostos, que são agregados de vários sistemas diferentes.

### <a name="building-a-dashboard"></a>Criar um dashboard
Às vezes, quer poder expor informações que existe em vários sistemas de back-end, por exemplo, para orientar um dashboard. Os KPIs provenientes de todas as diferentes back-ends, mas prefere não fornecer acesso direto aos mesmos e seria bom se foi possível obter a todas as informações numa única solicitação. Talvez algumas das informações do back-end tem alguns dividir e repartir e um pouco a correção primeiro! A capacidade de colocar em cache desse recurso composto seria útil reduzir a carga de back-end, como sabe que os utilizadores têm um hábito de insistir a tecla F5 para ver se as suas métricas com baixo desempenho poderá ser alterado.    

### <a name="faking-the-resource"></a>Simular o recurso
O primeiro passo para criar o recurso de dashboard é configurar uma operação de novo no portal do Azure. Esta é uma operação de marcador de posição utilizada para configurar uma política de composição para criar o recurso dinâmico.

![Operação de dashboard](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Tornando os pedidos
Quando a operação tiver sido criada, pode configurar uma política especificamente para essa operação. 

![Operação de dashboard](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

O primeiro passo é extrair quaisquer parâmetros de consulta da solicitação recebida, para que possa reencaminhá-los para o back-end. Neste exemplo, o dashboard está mostrando informações com base num período de tempo e, portanto, tem um `fromDate` e `toDate` parâmetro. Pode utilizar o `set-variable` política para extrair as informações da URL do pedido.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

Assim que tiver estas informações, pode fazer pedidos para todos os sistemas de back-end. Cada solicitação constrói um novo URL com as informações de parâmetro e chama o respetivo servidor e armazena a resposta numa variável de contexto.

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
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>
```

Estes pedidos são executadas em seqüência, o que não é ideal. 

### <a name="responding"></a>A responder
Para construir a resposta composta, pode utilizar o [resposta de retorno](/azure/api-management/api-management-advanced-policies#ReturnResponse) política. O `set-body` elemento pode utilizar uma expressão para construir um novo `JObject` com todas as representações de componente incorporadas como propriedades.

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

A política completa será semelhante ao seguinte:

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
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
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

Na configuração da operação de marcador de posição, pode configurar o recurso de dashboard sejam armazenados em cache durante, pelo menos, uma hora. 

## <a name="summary"></a>Resumo
O serviço de gestão de API do Azure fornece diretivas flexíveis que podem ser aplicadas seletivamente para tráfego HTTP e permita a composição dos serviços de back-end. Se deseja melhorar o seu gateway de API com as funções, verificação, capacidades de validação de alertas ou criar novos recursos compostos com base em vários serviços de back-end, o `send-request` e políticas relacionadas abrem um mundo de possibilidades.

