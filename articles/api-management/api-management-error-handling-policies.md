---
title: Tratamento de erros nas políticas de gerenciamento de API do Azure | Microsoft Docs
description: Saiba como responder às condições de erro que podem ocorrer durante o processamento de solicitações no gerenciamento de API do Azure.
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
ms.openlocfilehash: 2c021a6d10c95b58ac444de8ea895ca01371a2b0
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75902446"
---
# <a name="error-handling-in-api-management-policies"></a>Tratamento de erro em políticas de gerenciamento de API

Ao fornecer um objeto `ProxyError`, o gerenciamento de API do Azure permite que os editores respondam às condições de erro, o que pode ocorrer durante o processamento de solicitações. O objeto `ProxyError` é acessado por meio do [contexto. ](api-management-policy-expressions.md#ContextVariables)A propriedade LastError e pode ser usada por políticas na seção política de `on-error`. Este artigo fornece uma referência para os recursos de tratamento de erros no gerenciamento de API do Azure.

## <a name="error-handling-in-api-management"></a>Tratamento de erros no gerenciamento de API

As políticas no gerenciamento de API do Azure são divididas em seções `inbound`, `backend`, `outbound`e `on-error`, conforme mostrado no exemplo a seguir.

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

Durante o processamento de uma solicitação, as etapas internas são executadas junto com todas as políticas, que estão no escopo da solicitação. Se ocorrer um erro, o processamento saltará imediatamente para a seção de política de `on-error`.
A seção de política de `on-error` pode ser usada em qualquer escopo. Os editores de API podem configurar o comportamento personalizado, como registrar o erro nos hubs de eventos ou criar uma nova resposta para retornar ao chamador.

> [!NOTE]
> A seção `on-error` não está presente nas políticas por padrão. Para adicionar a seção `on-error` a uma política, navegue até a política desejada no editor de políticas e adicione-a. Para obter mais informações sobre como configurar políticas, consulte [políticas no gerenciamento de API](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/).
>
> Se não houver `on-error` seção, os chamadores receberão mensagens de resposta HTTP 400 ou 500 se ocorrer uma condição de erro.

### <a name="policies-allowed-in-on-error"></a>Políticas permitidas em erro

As políticas a seguir podem ser usadas na seção política de `on-error`.

-   [choose](api-management-advanced-policies.md#choose)
-   [set-variable](api-management-advanced-policies.md#set-variable)
-   [find-and-replace](api-management-transformation-policies.md#Findandreplacestringinbody)
-   [return-response](api-management-advanced-policies.md#ReturnResponse)
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)
-   [Set-Method](api-management-advanced-policies.md#SetRequestMethod)
-   [set-status](api-management-advanced-policies.md#SetStatus)
-   [send-request](api-management-advanced-policies.md#SendRequest)
-   [send-one-way-request](api-management-advanced-policies.md#SendOneWayRequest)
-   [log-to-eventhub](api-management-advanced-policies.md#log-to-eventhub)
-   [json-to-xml](api-management-transformation-policies.md#ConvertJSONtoXML)
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON)

## <a name="lasterror"></a>LastError

Quando ocorre um erro e o controle salta para a seção de política de `on-error`, o erro é armazenado no [contexto. ](api-management-policy-expressions.md#ContextVariables)A propriedade LastError, que pode ser acessada por políticas na seção `on-error`. LastError tem as seguintes propriedades.

| Nome       | Tipo   | Descrição                                                                                               | Obrigatório |
| ---------- | ------ | --------------------------------------------------------------------------------------------------------- | -------- |
| `Source`   | string | Nomeia o elemento em que ocorreu o erro. Pode ser a política ou um nome de etapa de pipeline interno.      | Sim      |
| `Reason`   | string | Código de erro amigável à máquina, que poderia ser usado no tratamento de erros.                                       | Não       |
| `Message`  | string | Descrição do erro legível por humanos.                                                                         | Sim      |
| `Scope`    | string | Nome do escopo em que ocorreu o erro e pode ser um de "global", "produto", "API" ou "operação" | Não       |
| `Section`  | string | Nome da seção em que ocorreu o erro. Valores possíveis: "entrada", "back-end", "Outbound" ou "On-Error".      | Não       |
| `Path`     | string | Especifica a política aninhada, por exemplo, "Choose [3]/When [2]".                                                 | Não       |
| `PolicyId` | string | Valor do atributo `id`, se especificado pelo cliente, na política em que ocorreu o erro             | Não       |

> [!TIP]
> Você pode acessar o código de status por meio de contexto. Response. StatusCode.

> [!NOTE]
> Todas as políticas têm um atributo `id` opcional que pode ser adicionado ao elemento raiz da política. Se esse atributo estiver presente em uma política quando ocorrer uma condição de erro, o valor do atributo poderá ser recuperado usando a propriedade `context.LastError.PolicyId`.

## <a name="predefined-errors-for-built-in-steps"></a>Erros predefinidos para etapas internas

Os erros a seguir são predefinidos para condições de erro que podem ocorrer durante a avaliação de etapas de processamento internas.

| Origem        | Condição                                 | Razão                  | Mensagem                                                                                                                |
| ------------- | ----------------------------------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| configuração | O URI não corresponde a nenhuma API ou operação | OperationNotFound       | Não é possível fazer a correspondência da solicitação de entrada para uma operação.                                                                      |
| authorization | Chave de assinatura não fornecida             | SubscriptionKeyNotFound | Acesso negado devido à falta de chave de assinatura. Certifique-se de incluir a chave de assinatura ao fazer solicitações para essa API. |
| authorization | O valor da chave de assinatura é inválido         | SubscriptionKeyInvalid  | Acesso negado devido à chave de assinatura inválida. Certifique-se de fornecer uma chave válida para uma assinatura ativa.            |
| múltiplo | A conexão downstream (de um cliente para um gateway de gerenciamento de API) foi anulada pelo cliente enquanto a solicitação estava pendente | ClientConnectionFailure | múltiplo |
| múltiplo | A conexão upstream (de um gateway de gerenciamento de API para um serviço de back-end) não foi estabelecida ou foi anulada pelo back-end | BackendConnectionFailure | múltiplo |
| múltiplo | Ocorreu uma exceção de tempo de execução durante a avaliação de uma expressão específica | ExpressionValueEvaluationFailure | múltiplo |

## <a name="predefined-errors-for-policies"></a>Erros predefinidos para políticas

Os erros a seguir são predefinidos para condições de erro que podem ocorrer durante a avaliação da política.

| Origem       | Condição                                                       | Razão                    | Mensagem                                                                                                                              |
| ------------ | --------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| taxa-limite   | Limite de taxa excedido                                             | RateLimitExceeded         | O limite de taxa foi excedido                                                                                                               |
| quota        | Cota excedida                                                  | QuotaExceeded             | Volume de chamadas fora da quota. A cota será reabastecida em XX: XX: XX. -ou-fora da cota de largura de banda. A cota será reabastecida em XX: XX: XX. |
| JSONP        | O valor do parâmetro de retorno de chamada é inválido (contém caracteres errados) | CallbackParameterInvalid  | O valor do parâmetro de retorno de chamada {callback-Parameter-Name} não é um identificador JavaScript válido.                                          |
| ip-filter    | Falha ao analisar o IP do chamador da solicitação                          | FailedToParseCallerIP     | Falha ao estabelecer o endereço IP para o chamador. Acesso negado.                                                                        |
| ip-filter    | O IP do chamador não está na lista de permissões                                | CallerIpNotAllowed        | O endereço IP do chamador {IP-address} não é permitido. Acesso negado.                                                                        |
| ip-filter    | O IP do chamador está na lista bloqueada                                    | CallerIpBlocked           | O endereço IP do chamador está bloqueado. Acesso negado.                                                                                         |
| cabeçalho de verificação | Cabeçalho necessário não apresentado ou valor ausente               | HeaderNotFound            | O cabeçalho {Header-Name} não foi encontrado na solicitação. Acesso negado.                                                                    |
| cabeçalho de verificação | Cabeçalho necessário não apresentado ou valor ausente               | HeaderValueNotAllowed     | O valor do cabeçalho {Header-Name} de {header-value} não é permitido. Acesso negado.                                                          |
| validate-jwt | O token JWT está ausente na solicitação                                 | TokenNotFound             | JWT não encontrado na solicitação. Acesso negado.                                                                                         |
| validate-jwt | Falha na validação da assinatura                                     | TokenSignatureInvalid     | < mensagem da biblioteca JWT\>. Acesso negado.                                                                                          |
| validate-jwt | Público-alvo inválido                                                | TokenAudienceNotAllowed   | < mensagem da biblioteca JWT\>. Acesso negado.                                                                                          |
| validate-jwt | Emissor inválido                                                  | TokenIssuerNotAllowed     | < mensagem da biblioteca JWT\>. Acesso negado.                                                                                          |
| validate-jwt | O token expirou                                                   | TokenExpired              | < mensagem da biblioteca JWT\>. Acesso negado.                                                                                          |
| validate-jwt | A chave de assinatura não foi resolvida pela ID                            | TokenSignatureKeyNotFound | < mensagem da biblioteca JWT\>. Acesso negado.                                                                                          |
| validate-jwt | Declarações necessárias estão ausentes do token                          | TokenClaimNotFound        | O token JWT não tem as seguintes declarações: < C1\>, < C2\>,... Acesso negado.                                                            |
| validate-jwt | Incompatibilidade de valores de declaração                                           | TokenClaimValueNotAllowed | O valor da declaração {Claim-Name} de {Claim-value} não é permitido. Acesso negado.                                                             |
| validate-jwt | Outras falhas de validação                                       | JwtInvalid                | < mensagem da biblioteca JWT\>                                                                                                          |
| solicitação de encaminhamento ou envio-solicitação | O código e os cabeçalhos de status de resposta HTTP não foram recebidos do back-end dentro do tempo limite configurado | Tempo limite | múltiplo |

## <a name="example"></a>Exemplo

Definindo uma política de API para:

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

e o envio de uma solicitação não autorizada resultará na seguinte resposta:

![Resposta de erro não autorizada](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como trabalhar com políticas, consulte:

-   [Políticas no gerenciamento de API](api-management-howto-policies.md)
-   [APIs de transformação](transform-api.md)
-   [Referência de política](api-management-policy-reference.md) para uma lista completa de instruções de política e suas configurações
-   [Exemplos de política](policy-samples.md)
