---
title: Manipulação de erros nas políticas de Gestão da API azure [ Microsoft Docs
description: Saiba como responder às condições de erro que podem ocorrer durante o processamento de pedidos na Gestão da API Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75902446"
---
# <a name="error-handling-in-api-management-policies"></a>Erro ao processar as políticas de Gestão de API

Ao fornecer `ProxyError` um objeto, a Azure API Management permite que os editores respondam às condições de erro, o que pode ocorrer durante o processamento de pedidos. O `ProxyError` objeto é acedido através do [contexto. Propriedade LastError](api-management-policy-expressions.md#ContextVariables) e pode ser `on-error` usada por políticas na secção política. Este artigo fornece uma referência para as capacidades de manipulação de erros na Gestão da API Azure.

## <a name="error-handling-in-api-management"></a>Manipulação de erros na Gestão da API

As políticas na Gestão da `inbound`API Azure `backend`dividem-se `outbound`em secções, `on-error` como se pode ver no exemplo seguinte.

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

Durante o processamento de um pedido, são executadas etapas incorporadas, juntamente com quaisquer políticas, que estejam à margem do pedido. Se ocorrer um erro, o processamento `on-error` salta imediatamente para a secção política.
A `on-error` secção política pode ser utilizada em qualquer âmbito. Os editores da API podem configurar comportamentos personalizados, como registar o erro nos centros de eventos ou criar uma nova resposta para voltar ao chamador.

> [!NOTE]
> A `on-error` secção não está presente nas políticas por defeito. Para adicionar `on-error` a secção a uma política, navegue na política desejada no editor de política e adicione-a. Para obter mais informações sobre a configuração de políticas, consulte [Políticas na Gestão da API.](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/)
>
> Se não `on-error` houver secção, os chamadores receberão 400 ou 500 mensagens de resposta HTTP se ocorrer uma condição de erro.

### <a name="policies-allowed-in-on-error"></a>Políticas permitidas no erro

As seguintes políticas podem `on-error` ser utilizadas na secção política.

-   [escolher](api-management-advanced-policies.md#choose)
-   [set-variável](api-management-advanced-policies.md#set-variable)
-   [encontrar e substituir](api-management-transformation-policies.md#Findandreplacestringinbody)
-   [resposta de retorno](api-management-advanced-policies.md#ReturnResponse)
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)
-   [método de conjunto](api-management-advanced-policies.md#SetRequestMethod)
-   [definir-status](api-management-advanced-policies.md#SetStatus)
-   [envio-pedido](api-management-advanced-policies.md#SendRequest)
-   [enviar-um-pedido de caminho](api-management-advanced-policies.md#SendOneWayRequest)
-   [log-to-eventhub](api-management-advanced-policies.md#log-to-eventhub)
-   [json-to-xml](api-management-transformation-policies.md#ConvertJSONtoXML)
-   [xml-a-json](api-management-transformation-policies.md#ConvertXMLtoJSON)

## <a name="lasterror"></a>LastError

Quando ocorre um erro e o `on-error` controlo salta para a secção de política, o erro é armazenado em [contexto. Propriedade LastError,](api-management-policy-expressions.md#ContextVariables) que pode ser acedida por políticas na `on-error` secção. O LastError tem as seguintes propriedades.

| Nome       | Tipo   | Descrição                                                                                               | Necessário |
| ---------- | ------ | --------------------------------------------------------------------------------------------------------- | -------- |
| `Source`   | string | Nomeie o elemento onde ocorreu o erro. Pode ser uma política ou um nome de passo embutido.      | Sim      |
| `Reason`   | string | Código de erro amigável, que pode ser utilizado no manuseamento de erros.                                       | Não       |
| `Message`  | string | Descrição de erro legível pelo homem.                                                                         | Sim      |
| `Scope`    | string | Nome do âmbito onde ocorreu o erro e pode ser de "global", "produto", "api" ou "operação" | Não       |
| `Section`  | string | Nome da secção onde ocorreu o erro. Valores possíveis: "entrada", "backend", "outbound" ou "on-error".      | Não       |
| `Path`     | string | Especifica a política aninhada, por exemplo, "escolha[3]/quando[2]".                                                 | Não       |
| `PolicyId` | string | Valor do `id` atributo, se especificado pelo cliente, sobre a política em que ocorreu erro             | Não       |

> [!TIP]
> Pode aceder ao código de estado através do contexto. Resposta.StatusCode.

> [!NOTE]
> Todas as políticas `id` têm um atributo opcional que pode ser adicionado ao elemento raiz da política. Se este atributo estiver presente numa política quando ocorre uma condição de erro, `context.LastError.PolicyId` o valor do atributo pode ser recuperado utilizando a propriedade.

## <a name="predefined-errors-for-built-in-steps"></a>Erros predefinidos para passos incorporados

Os seguintes erros são predefinidos para as condições de erro que podem ocorrer durante a avaliação das etapas de processamento incorporadas.

| Origem        | Condição                                 | Razão                  | Mensagem                                                                                                                |
| ------------- | ----------------------------------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| configuração | Uri não corresponde a nenhuma API ou Operação | OperaçãoNotFound       | Incapaz de corresponder ao pedido de entrada de uma operação.                                                                      |
| autorização | Chave de subscrição não fornecida             | SubscriçãoKeyNotFound | Acesso negado devido à chave de subscrição em falta. Certifique-se de incluir a chave de subscrição ao fazer pedidos para esta API. |
| autorização | O valor-chave da subscrição é inválido         | AssinaturaKeyInválido  | Acesso negado devido a chave de subscrição inválida. Certifique-se de fornecer uma chave válida para uma subscrição ativa.            |
| múltiplo | A ligação a jusante (de um cliente a um gateway de Gestão API) foi abortada pelo cliente enquanto o pedido estava pendente | Falha de Ligação ao Cliente | múltiplo |
| múltiplo | A ligação a montante (de uma porta de entrada de gestão da API para um serviço de backend) não foi estabelecida ou foi abortada pelo backend | Falha de ligação de backend | múltiplo |
| múltiplo | A exceção do tempo de execução tinha ocorrido durante a avaliação de uma expressão particular | ExpressionValueEvaluationFailure | múltiplo |

## <a name="predefined-errors-for-policies"></a>Erros predefinidos para políticas

Os seguintes erros são predefinidos para as condições de erro que podem ocorrer durante a avaliação da política.

| Origem       | Condição                                                       | Razão                    | Mensagem                                                                                                                              |
| ------------ | --------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| limite de taxas   | Limite de taxa ultrapassado                                             | RateLimitExceeded         | O limite de taxas é ultrapassado                                                                                                               |
| quota        | Quota excedida                                                  | Quota Ultrapassada             | Volume de chamadas fora da quota. A quota será reabastecida em xx:xx:xx:xx. -ou- Fora da quota de largura de banda. A quota será reabastecida em xx:xx:xx:xx. |
| jsonp        | O valor do parâmetro de chamada é inválido (contém caracteres errados) | CallbackParameterInvalid  | O valor do parâmetro de chamada {callback-parâmetro- nome} não é um identificador JavaScript válido.                                          |
| ip-filtro    | Falha na análise do IP do chamador a pedido                          | FalhadoToParseCallerIP     | Não conseguiu estabelecer o endereço IP para o chamador. Acesso negado.                                                                        |
| ip-filtro    | IP de chamada não está na lista permitida                                | CallerIpNotAllowed        | O endereço IP do chamador {ip-address} não é permitido. Acesso negado.                                                                        |
| ip-filtro    | Ip do chamador está na lista bloqueada                                    | CallerIpBlocked           | O endereço IP do chamador está bloqueado. Acesso negado.                                                                                         |
| cabeçalho de cheque | Cabeçalho exigido não apresentado ou valor está faltando               | HeaderNotFound            | O cabeçalho {cabeçalho} não foi encontrado no pedido. Acesso negado.                                                                    |
| cabeçalho de cheque | Cabeçalho exigido não apresentado ou valor está faltando               | HeaderValueNotAllowed     | Não é permitido o valor do {header-nome} de {valor do cabeçalho} Acesso negado.                                                          |
| valida-jwt | Jwt token está desaparecido a pedido                                 | TokenNotFound             | JWT não foi encontrado no pedido. Acesso negado.                                                                                         |
| valida-jwt | Validação de assinatura falhou                                     | TokenSignatureInválido     | <mensagem da biblioteca\>jwt. Acesso negado.                                                                                          |
| valida-jwt | Audiência inválida                                                | TokenAudienceNotAllowed   | <mensagem da biblioteca\>jwt. Acesso negado.                                                                                          |
| valida-jwt | Emitente inválido                                                  | TokenIssuerNotAllowed     | <mensagem da biblioteca\>jwt. Acesso negado.                                                                                          |
| valida-jwt | O token expirou                                                   | TokenExpired              | <mensagem da biblioteca\>jwt. Acesso negado.                                                                                          |
| valida-jwt | A chave de assinatura não foi resolvida por ID                            | TokenSignatureKeyNotFound | <mensagem da biblioteca\>jwt. Acesso negado.                                                                                          |
| valida-jwt | Faltam reclamações necessárias do símbolo                          | TokenClaimNotFound        | Falta o símbolo jWT: <c1,\><\>c2, ... Acesso negado.                                                            |
| valida-jwt | Desajuste de valores de reclamação                                           | TokenClaimValueNotAllowed | Claim {claim-name} value of {claim-value} não é permitido. Acesso negado.                                                             |
| valida-jwt | Outras falhas de validação                                       | JwtInvalid                | <mensagem da biblioteca jwt\>                                                                                                          |
| pedido para a frente ou pedido de envio | O código de estado de resposta HTTP e os cabeçalhos não foram recebidos do backend dentro do tempo de paragem configurado | Tempo limite | múltiplo |

## <a name="example"></a>Exemplo

Definição de uma política da API para:

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

Para mais informações que trabalhem com políticas, consulte:

-   [Políticas em Gestão aPi](api-management-howto-policies.md)
-   [Transforme APIs](transform-api.md)
-   [Referência política](api-management-policy-reference.md) para uma lista completa de declarações políticas e suas configurações
-   [Amostras políticas](policy-samples.md)
