---
title: Erro ao processar em políticas de gestão de API do Azure | Documentos da Microsoft
description: Saiba como responder às condições de erro que possam ocorrer durante o processamento de pedidos na gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: apimpm
ms.openlocfilehash: 87693caa5343e359bb3ab424de489c2270bbca62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64704443"
---
# <a name="error-handling-in-api-management-policies"></a>Erro ao processar em políticas de gestão de API

Ao fornecer um `ProxyError` de objeto, gestão de API do Azure permite aos editores responder às condições de erro, que podem ocorrer durante o processamento de pedidos. O `ProxyError` objeto é acessado por meio do [contexto. LastError](api-management-policy-expressions.md#ContextVariables) propriedade e pode ser utilizado pelas políticas no `on-error` secção política. Este artigo fornece uma referência para o erro de recursos de tratamento na gestão de API do Azure.  
  
## <a name="error-handling-in-api-management"></a>Erro ao processar em gestão de API

 As políticas de gestão de API do Azure são divididas em `inbound`, `backend`, `outbound`, e `on-error` secções conforme mostrado no exemplo a seguir.  
  
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
  
Durante o processamento de um pedido, etapas incorporadas são executadas, juntamente com quaisquer políticas, as quais estão no âmbito para o pedido. Se ocorrer um erro, processamento imediatamente salta para o `on-error` secção política.  
O `on-error` secção política pode ser utilizada em qualquer âmbito. Os publicadores de APIS podem configurar o comportamento personalizado, como o log de erro para os hubs de eventos ou criar uma nova resposta para devolver ao autor da chamada.  
  
> [!NOTE]
>  O `on-error` secção não está presente nas políticas por predefinição. Para adicionar o `on-error` secção a uma política, navegue para a política pretendida no editor de políticas e adicioná-lo. Para obter mais informações sobre como configurar políticas, consulte [políticas de gestão de API](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/).  
>   
>  Se não houver nenhum `on-error` secção, os chamadores irão receber mensagens de resposta HTTP 400 ou 500 se ocorrer uma condição de erro.  
  
### <a name="policies-allowed-in-on-error"></a>Políticas permitidas no erro

 As seguintes políticas podem ser utilizadas no `on-error` secção política.  
  
-   [choose](api-management-advanced-policies.md#choose)  
-   [set-variable](api-management-advanced-policies.md#set-variable)  
-   [find-and-replace](api-management-transformation-policies.md#Findandreplacestringinbody)  
-   [return-response](api-management-advanced-policies.md#ReturnResponse)  
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)  
-   [set-method](api-management-advanced-policies.md#SetRequestMethod)  
-   [set-status](api-management-advanced-policies.md#SetStatus)  
-   [send-request](api-management-advanced-policies.md#SendRequest)  
-   [send-one-way-request](api-management-advanced-policies.md#SendOneWayRequest)  
-   [log-to-eventhub](api-management-advanced-policies.md#log-to-eventhub)  
-   [json-to-xml](api-management-transformation-policies.md#ConvertJSONtoXML)  
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON)  
  
## <a name="lasterror"></a>LastError

 Quando ocorre um erro e controle salta para o `on-error` secção de política, o erro é armazenado no [contexto. LastError](api-management-policy-expressions.md#ContextVariables) propriedade, que pode ser acessada por políticas no `on-error` secção. LastError tem as seguintes propriedades.  
  
| Name       | Tipo   | Descrição                                                                                               | Necessário |
|------------|--------|-----------------------------------------------------------------------------------------------------------|----------|
| `Source`   | string | Nomes do elemento onde ocorreu o erro. Pode ser política ou um nome de passo de pipeline interno.     | Sim      |
| `Reason`   | string | Código de erro de máquina amigável, que poderia ser usado no tratamento de erros.                                       | Não       |
| `Message`  | string | Descrição do erro legível por humanos.                                                                         | Sim      |
| `Scope`    | string | Nome do âmbito em que o erro ocorreu e pode ser um dos "global", "produto", "api" ou "operação" | Não       |
| `Section`  | string | Nome de secção onde ocorreu o erro. Valores possíveis: "entrada", "backend", "saída" ou "em erro".       | Não       |
| `Path`     | string | Especifica a política de aninhadas, por exemplo "Escolha [3] / quando [2]".                                                        | Não       |
| `PolicyId` | string | Valor da `id` de atributo, se for especificado pelo cliente, na política de onde ocorreu o erro             | Não       |

> [!TIP]
> Pode acessar o código de estado por meio do contexto. Response.StatusCode.  

> [!NOTE]
> Todas as políticas têm opcional `id` atributo que pode ser adicionado ao elemento raiz da política. Se este atributo estiver presente numa política quando ocorre uma condição de erro, o valor do atributo pode ser obtido com o `context.LastError.PolicyId` propriedade.

## <a name="predefined-errors-for-built-in-steps"></a>Erros predefinidos para obter os passos incorporados  
 Os seguintes erros estão predefinidos para condições de erro que podem ocorrer durante a avaliação de passos de processamento interno.  
  
| Source        | Condição                                 | Reason                  | Message                                                                                                                |
|---------------|-------------------------------------------|-------------------------|------------------------------------------------------------------------------------------------------------------------|
| configuração | URI não corresponde a qualquer API ou a operação | OperationNotFound       | Não é possível comparar a solicitação de entrada para uma operação de mensagens em fila.                                                                      |
| authorization | Chave de subscrição não fornecida             | SubscriptionKeyNotFound | Acesso negado devido à falta de chave de subscrição. Certifique-se incluir a chave de subscrição quando são efetuados pedidos para esta API. |
| authorization | Valor de chave de subscrição é inválido         | SubscriptionKeyInvalid  | Acesso negado devido a chave de subscrição inválido. Certifique-se fornecer uma chave válida para uma subscrição ativa.            |
  
## <a name="predefined-errors-for-policies"></a>Erros predefinidos para as políticas  
 Os seguintes erros estão predefinidos para condições de erro que podem ocorrer durante a avaliação da política.  
  
| Source       | Condição                                                       | Reason                    | Message                                                                                                                              |
|--------------|-----------------------------------------------------------------|---------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| limite de taxa   | Limite de taxa foi excedido                                             | RateLimitExceeded         | Limite de taxa foi excedido                                                                                                               |
| quota        | Quota excedida                                                  | QuotaExceeded             | Volume de chamadas fora da quota. Quota será seja reposta no xx:xx:xx. \- ou - fora da quota de largura de banda. Quota será seja reposta no xx:xx:xx. |
| jsonp        | O valor de parâmetro de retorno de chamada é inválido (contém caracteres errados) | CallbackParameterInvalid  | Valor do parâmetro de retorno de chamada {nome de parâmetro de retorno de chamada} não é um identificador de JavaScript válido.                                          |
| ip-filter    | Falha ao analisar o IP de chamador do pedido                          | FailedToParseCallerIP     | Falha ao estabelecer o endereço IP para o chamador. Acesso negado.                                                                        |
| ip-filter    | Autor da chamada IP não se encontrar no lista de permitidos                                | CallerIpNotAllowed        | O endereço IP de chamador {endereço ip} não é permitido. Acesso negado.                                                                        |
| ip-filter    | Autor da chamada IP está na lista de bloqueados                                    | CallerIpBlocked           | Endereço IP de chamador é bloqueado. Acesso negado.                                                                                         |
| check-header | Cabeçalho necessário não apresentados ou valor está em falta               | HeaderNotFound            | Cabeçalho {cabeçalho-name} não foi encontrado no pedido. Acesso negado.                                                                    |
| check-header | Cabeçalho necessário não apresentados ou valor está em falta               | HeaderValueNotAllowed     | Valor de cabeçalho {nome do cabeçalho} de {valor do cabeçalho} não é permitido. Acesso negado.                                                          |
| validate-jwt | Jwt token está em falta no pedido                                 | TokenNotFound             | JWT não foi encontrado no pedido. Acesso negado.                                                                                         |
| validate-jwt | Falha na validação de assinatura                                     | TokenSignatureInvalid     | < a mensagem da biblioteca do jwt\>. Acesso negado.                                                                                          |
| validate-jwt | Público-alvo inválido                                                | TokenAudienceNotAllowed   | < a mensagem da biblioteca do jwt\>. Acesso negado.                                                                                          |
| validate-jwt | Emissor inválido                                                  | TokenIssuerNotAllowed     | < a mensagem da biblioteca do jwt\>. Acesso negado.                                                                                          |
| validate-jwt | O token expirou                                                   | TokenExpired              | < a mensagem da biblioteca do jwt\>. Acesso negado.                                                                                          |
| validate-jwt | Chave de assinatura não foi resolvido pelo ID                            | TokenSignatureKeyNotFound | < a mensagem da biblioteca do jwt\>. Acesso negado.                                                                                          |
| validate-jwt | Declarações necessárias estão em falta do token                          | TokenClaimNotFound        | JWT token não tem as seguintes declarações: < c1\>, < c2\>,... Acesso negado.                                                            |
| validate-jwt | Erro de correspondência de valores de afirmação                                           | TokenClaimValueNotAllowed | Valor de afirmação {afirmação-name} de {valor de afirmação} não é permitida. Acesso negado.                                                             |
| validate-jwt | Outras falhas de validação                                       | JwtInvalid                | < a mensagem da biblioteca do jwt\>                                                                                                          |

## <a name="example"></a>Exemplo

Definição de uma política de API:

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

![Resposta de erro não autorizado](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações para trabalhar com políticas, consulte:

+ [Políticas de gestão de API](api-management-howto-policies.md)
+ [Transforme as APIs](transform-api.md)
+ [Referência de política](api-management-policy-reference.md) para uma lista completa das declarações de política e suas configurações
+ [Exemplos de política](policy-samples.md)   
