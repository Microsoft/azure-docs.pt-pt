---
title: Manipulação de erros nas políticas de Gestão da API da Azure ! Microsoft Docs
description: Saiba como responder às condições de erro que possam ocorrer durante o processamento de pedidos na Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: a3b6f90d0aa26b478c0f2fcefac55dcd509da437
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070950"
---
# <a name="error-handling-in-api-management-policies"></a>Erro ao processar as políticas de Gestão de API

Ao fornecer um `ProxyError` objeto, a Azure API Management permite que os editores respondam às condições de erro, que podem ocorrer durante o processamento de pedidos. O `ProxyError` objeto é acedido através do [contexto. Propriedade LastError](api-management-policy-expressions.md#ContextVariables) e pode ser usado por políticas na `on-error` secção política. Este artigo fornece uma referência para as capacidades de tratamento de erros na Azure API Management.

## <a name="error-handling-in-api-management"></a>Manipulação de erros na Gestão da API

As políticas em Gestão API Azure dividem-se `inbound` `backend` em, `outbound` e `on-error` secções como mostrado no exemplo seguinte.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is
         forwarded to the backend service go here -->
    </backend>
    <outbound>
      <!-- statements to be applied to the response go here -->
    </outbound>
    <on-error>
        <!-- statements to be applied if there is an error
             condition go here -->
  </on-error>
</policies>
```

Durante o processamento de um pedido, são executadas etapas incorporadas juntamente com quaisquer políticas, que estejam em âmbito de aplicação do pedido. Se ocorrer um erro, o processamento salta imediatamente para a `on-error` secção de política.
A `on-error` secção de política pode ser utilizada em qualquer âmbito. Os editores da API podem configurar comportamentos personalizados, tais como registar o erro nos centros de eventos ou criar uma nova resposta para o retorno ao chamador.

> [!NOTE]
> A `on-error` secção não está presente nas políticas por defeito. Para adicionar a `on-error` secção a uma política, consulte a política desejada no editor de política e adicione-a. Para obter mais informações sobre políticas de configuração, consulte [Políticas em Gestão de API.](./api-management-howto-policies.md)
>
> Se não houver `on-error` secção, os chamadores receberão 400 ou 500 mensagens de resposta HTTP se ocorrer uma condição de erro.

### <a name="policies-allowed-in-on-error"></a>Políticas permitidas no erro

As seguintes políticas podem ser utilizadas na `on-error` secção política.

-   [escolher](api-management-advanced-policies.md#choose)
-   [conjunto-variável](api-management-advanced-policies.md#set-variable)
-   [encontrar e substituir](api-management-transformation-policies.md#Findandreplacestringinbody)
-   [resposta de retorno](api-management-advanced-policies.md#ReturnResponse)
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)
-   [método de conjunto](api-management-advanced-policies.md#SetRequestMethod)
-   [estado de conjunto](api-management-advanced-policies.md#SetStatus)
-   [envio-pedido](api-management-advanced-policies.md#SendRequest)
-   [enviar-um-pedido de ida](api-management-advanced-policies.md#SendOneWayRequest)
-   [log-to-eventhub](api-management-advanced-policies.md#log-to-eventhub)
-   [json-to-xml](api-management-transformation-policies.md#ConvertJSONtoXML)
-   [xml-para-json](api-management-transformation-policies.md#ConvertXMLtoJSON)
-   [limitação da concordância](api-management-advanced-policies.md#LimitConcurrency)
-   [mock-response](api-management-advanced-policies.md#mock-response)
-   [retry](api-management-advanced-policies.md#Retry)
-   [traço](api-management-advanced-policies.md#Trace)

## <a name="lasterror"></a>LastError

Quando ocorre um erro e o controlo salta para a `on-error` secção de política, o erro é armazenado em [contexto. Propriedade LastError,](api-management-policy-expressions.md#ContextVariables) que pode ser acedida por políticas na `on-error` secção. A LastError tem as seguintes propriedades.

| Nome       | Tipo   | Descrição                                                                                               | Obrigatório |
| ---------- | ------ | --------------------------------------------------------------------------------------------------------- | -------- |
| `Source`   | cadeia | Nomeia o elemento onde ocorreu o erro. Pode ser política ou um nome de passo de gasoduto embutido.      | Yes      |
| `Reason`   | cadeia | Código de erro amigável à máquina, que pode ser utilizado no manuseamento de erros.                                       | No       |
| `Message`  | cadeia | Descrição de erros legíveis pelo homem.                                                                         | Yes      |
| `Scope`    | cadeia | Nome do âmbito onde ocorreu o erro e pode ser de "global", "produto", "api" ou "operação" | No       |
| `Section`  | cadeia | Nome da secção onde ocorreu erro. Valores possíveis: "entrada", "backend", "outbound" ou "on-error".      | No       |
| `Path`     | cadeia | Especifica a política aninhada, por exemplo "escolher[3]/quando[2]".                                                 | No       |
| `PolicyId` | cadeia | Valor do `id` atributo, se especificado pelo cliente, na política onde ocorreu erro             | No       |

> [!TIP]
> Pode aceder ao código de estado através do contexto. Resposta.Código de Estado.

> [!NOTE]
> Todas as políticas têm um atributo opcional `id` que pode ser adicionado ao elemento raiz da política. Se este atributo estiver presente numa política quando ocorre uma condição de erro, o valor do atributo pode ser recuperado usando o `context.LastError.PolicyId` imóvel.

## <a name="predefined-errors-for-built-in-steps"></a>Erros predefinidos para passos incorporados

Os seguintes erros são predefinidos para as condições de erro que podem ocorrer durante a avaliação das etapas de processamento incorporadas.

| Origem        | Condição                                 | Razão                  | Mensagem                                                                                                                |
| ------------- | ----------------------------------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| configuração | Uri não corresponde a qualquer API ou Operação | OperaçãoNotFound       | Incapaz de corresponder ao pedido de entrada para uma operação.                                                                      |
| autorização | Chave de subscrição não fornecida             | SubscriçãoKeyNotFound | Acesso negado devido à falta da chave de subscrição. Certifique-se de incluir a chave de subscrição ao fazer pedidos a esta API. |
| autorização | O valor chave da subscrição é inválido         | SubscriçãoKeyInvalid  | Acesso negado devido à chave de subscrição inválida. Certifique-se de fornecer uma chave válida para uma subscrição ativa.            |
| múltiplo | A ligação a jusante (de um cliente a um gateway de gestão da API) foi abortada pelo cliente enquanto o pedido estava pendente | ClientConnectionFailure | múltiplo |
| múltiplo | A ligação a montante (de uma porta de entrada de gestão da API para um serviço de backend) não foi estabelecida ou foi abortada pelo backend | BackendConnectionFailure | múltiplo |
| múltiplo | Exceção do tempo de execução ocorreu durante a avaliação de uma expressão particular | ExpressionValueEvaluationFailure | múltiplo |

## <a name="predefined-errors-for-policies"></a>Erros predefinidos para políticas

Os seguintes erros são predefinidos para as condições de erro que podem ocorrer durante a avaliação da política.

| Origem       | Condição                                                       | Razão                    | Mensagem                                                                                                                              |
| ------------ | --------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| limite de taxa   | Limite de taxa ultrapassado                                             | TaxaLimitExceed         | Limite de taxa é ultrapassado                                                                                                               |
| quota        | Quota excedida                                                  | QuotaExceeded             | Volume de chamadas fora da quota. A quota será reabastecida em xx:xx:xx:xx. - ou- Fora da quota de largura de banda. A quota será reabastecida em xx:xx:xx:xx. |
| jsonp        | O valor do parâmetro de chamada é inválido (contém caracteres errados) | CallbackParameterInvalid  | O valor do parâmetro de retorno {callback-nome de parâmetro} não é um identificador JavaScript válido.                                          |
| ip-filtro    | Não analisou o IP do pedido de pedido                          | FailedToParseCallerIP     | Não conseguiu estabelecer o endereço IP para o chamador. Acesso negado.                                                                        |
| ip-filtro    | O IP do chamador não está na lista permitida                                | ChamadaIpNotAllowed        | O endereço IP do autor da chamada {endereço ip} não é permitido. Acesso negado.                                                                        |
| ip-filtro    | O IP do chamador está na lista bloqueada                                    | CallerIpBlocked           | O endereço IP do chamador está bloqueado. Acesso negado.                                                                                         |
| cabeçalho de verificação | Cabeçalho necessário não apresentado ou valor falta               | HeaderNotFound            | Cabeçalho {nome de cabeçalho} não foi encontrado no pedido. Acesso negado.                                                                    |
| cabeçalho de verificação | Cabeçalho necessário não apresentado ou valor falta               | HeaderValueNotAllowed     | Não é permitido o valor do cabeçalho {header}} de {header-value} Acesso negado.                                                          |
| validar-jwt | Jwt token está desaparecido a pedido                                 | TokenNotFound             | JWT não foi encontrado no pedido. Acesso negado.                                                                                         |
| validar-jwt | Validação de assinatura falhou                                     | TokenSignatureInvalid     | <mensagem da biblioteca \> JWT. Acesso negado.                                                                                          |
| validar-jwt | Público inválido                                                | TokenaudienceNotAllowed   | <mensagem da biblioteca \> JWT. Acesso negado.                                                                                          |
| validar-jwt | Emitente inválido                                                  | TokenIssuerNotAllowed     | <mensagem da biblioteca \> JWT. Acesso negado.                                                                                          |
| validar-jwt | O token expirou                                                   | TokenExpired              | <mensagem da biblioteca \> JWT. Acesso negado.                                                                                          |
| validar-jwt | A chave de assinatura não foi resolvida por ID                            | TokenSignatureKeyNotFound | <mensagem da biblioteca \> JWT. Acesso negado.                                                                                          |
| validar-jwt | Faltam reclamações necessárias do token                          | TokenClaimNotFound        | JWT token está faltando as seguintes alegações: <\> c1, <c2 \> , ... Acesso negado.                                                            |
| validar-jwt | Valores de reclamação desajustados                                           | TokenClaimValueNotAllowed | Não é permitido o valor de reivindicação {claim-name} de {claim-value} . Acesso negado.                                                             |
| validar-jwt | Outras falhas de validação                                       | JwtInvalid                | <mensagem da biblioteca JWT\>                                                                                                          |
| pedido de envio ou envio | O código de estado de resposta HTTP e os cabeçalhos não foram recebidos a partir do backend dentro do tempo limite configurado | Tempo Limite | múltiplo |

## <a name="example"></a>Exemplo

Definição de uma política de API para:

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <set-header name="ErrorSource" exists-action="override">
            <value>@(context.LastError.Source)</value>
        </set-header>
        <set-header name="ErrorReason" exists-action="override">
            <value>@(context.LastError.Reason)</value>
        </set-header>
        <set-header name="ErrorMessage" exists-action="override">
            <value>@(context.LastError.Message)</value>
        </set-header>
        <set-header name="ErrorScope" exists-action="override">
            <value>@(context.LastError.Scope)</value>
        </set-header>
        <set-header name="ErrorSection" exists-action="override">
            <value>@(context.LastError.Section)</value>
        </set-header>
        <set-header name="ErrorPath" exists-action="override">
            <value>@(context.LastError.Path)</value>
        </set-header>
        <set-header name="ErrorPolicyId" exists-action="override">
            <value>@(context.LastError.PolicyId)</value>
        </set-header>
        <set-header name="ErrorStatusCode" exists-action="override">
            <value>@(context.Response.StatusCode.ToString())</value>
        </set-header>
        <base />
    </on-error>
</policies>
```

e o envio de um pedido não autorizado resultará na seguinte resposta:

![Resposta de erro não autorizada](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as políticas, consulte:

-   [Políticas em Gestão de API](api-management-howto-policies.md)
-   [Transformar APIs](transform-api.md)
-   [Referência política](./api-management-policies.md) para uma lista completa de declarações políticas e suas definições
-   [Amostras de política](./policy-reference.md)