---
title: Funções Azure HTTP Vinculações de saída
description: Saiba como devolver respostas HTTP em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 6caf3847971c2dd39f1eea0a921891e4440373a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727472"
---
# <a name="azure-functions-http-output-bindings"></a>Funções Azure HTTP Vinculações de saída

Utilize a ligação de saída HTTP para responder ao remetente de pedidos HTTP. Esse enlace requer um acionador HTTP e permite-lhe personalizar a resposta associada ao pedido do acionador.

O valor de retorno predefinido para uma função desencadeada por HTTP é:

- `HTTP 204 No Content` com um corpo vazio em Funções 2.x e superior
- `HTTP 200 OK` com um corpo vazio em Funções 1.x

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsficheiro.* Para bibliotecas de classe C# não existem propriedades de atributos que correspondam a estas *function.jssobre* propriedades.

|Propriedade  |Descrição  |
|---------|---------|
| **tipo** |Deve ser definido para `http` . |
| **direção** | Deve ser definido para `out` . |
| **nome** | O nome variável usado no código de função para a resposta, ou `$return` para usar o valor de retorno. |

## <a name="usage"></a>Utilização

Para enviar uma resposta HTTP, utilize os padrões de resposta padrão da linguagem. No script C# ou C# faça o tipo de retorno da função `IActionResult` ou `Task<IActionResult>` . Em C#, um atributo de valor de retorno não é necessário.

Por exemplo, as respostas, consulte o exemplo do [gatilho](./functions-bindings-http-webhook-trigger.md#example).

## <a name="hostjson-settings"></a>host.jsnas definições

Esta secção descreve as definições de configuração global disponíveis para esta ligação nas versões 2.x ou superiores. O exemplo host.jsno ficheiro abaixo contém apenas as definições da versão 2.x+ para esta ligação. Para obter mais informações sobre as configurações globais nas versões 2.x e não só, consulte [host.jsem referência para Funções Azure](functions-host-json.md).

> [!NOTE]
> Para obter uma referência de host.jsem Funções 1.x, consulte [host.jsem referência para as Funções Azure 1.x](functions-host-json-v1.md#http).

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|Propriedade  |Predefinição | Description |
|---------|---------|---------| 
| personalizadosHeaders|nenhum|Permite-lhe definir cabeçalhos personalizados na resposta HTTP. O exemplo anterior adiciona o `X-Content-Type-Options` cabeçalho à resposta para evitar o cheiro do tipo de conteúdo. |
|dynamicThrottlesEnabled|verdade<sup>\*</sup>|Quando ativado, esta definição faz com que o gasoduto de processamento de pedidos verifique periodicamente os contadores de desempenho do sistema `connections/threads/processes/memory/cpu/etc` e, se algum desses contadores estiver acima de um limiar elevado incorporado (80%), os pedidos serão rejeitados com uma `429 "Too Busy"` resposta até que o contador(s) volte aos níveis normais.<br/><sup>\*</sup>O padrão num plano de consumo é `true` . O padrão num plano dedicado é `false` .|
|hsts|não habilitado|Quando `isEnabled` definido para , o comportamento HTTP Strict Transport Security `true` [(HSTS) de .NET Core](/aspnet/core/security/enforcing-ssl?tabs=visual-studio#hsts) é aplicado, conforme definido na [ `HstsOptions` classe](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions). O exemplo acima também define a [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) propriedade para 10 dias. Propriedades suportadas `hsts` são: <table><tr><th>Propriedade</th><th>Descrição</th></tr><tr><td>excluídosHosts</td><td>Uma série de nomes de anfitriões para os quais o cabeçalho HSTS não é adicionado.</td></tr><tr><td>incluiMus</td><td>Valor booleano que indica se o parâmetro de SubDomain do cabeçalho De Segurança Estrita-Transporte está ativado.</td></tr><tr><td>maxAge</td><td>Corda que define o parâmetro da idade máxima do cabeçalho De Segurança Estrita-Transporte.</td></tr><tr><td>pré-carregamento</td><td>Boolean que indica se o parâmetro de pré-carga do cabeçalho De Segurança Estrita-Transporte está ativado.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|O número máximo de funções HTTP que são executadas em paralelo. Este valor permite-lhe controlar a concordância, o que pode ajudar a gerir a utilização de recursos. Por exemplo, pode ter uma função HTTP que utiliza um grande número de recursos do sistema (memória/cpu/tomadas) de modo a causar problemas quando a conuscência é demasiado elevada. Ou pode ter uma função que faz pedidos de saída para um serviço de terceiros, e essas chamadas precisam de ser limitadas. Nestes casos, aplicar um acelerador aqui pode ajudar. <br/><sup>*</sup>O padrão para um plano de consumo é de 100. O padrão para um plano dedicado não é limitado `-1` ().|
|maxOutstandingRequests|200<sup>\*</sup>|O número máximo de pedidos pendentes que são realizados a qualquer momento. Este limite inclui pedidos que estão em fila mas que não começaram a executar, bem como quaisquer execuções em curso. Quaisquer pedidos de entrada acima deste limite são rejeitados com uma resposta 429 "Demasiado Ocupado". Isso permite que os chamadores utilizem estratégias de relíndi baseadas no tempo, e também ajuda-o a controlar o máximo de atrasos de pedido. Isto apenas controla a fila que ocorre dentro do caminho de execução do anfitrião do script. Outras filas, como a fila de pedidos ASP.NET ainda estarão em vigor e não serão afetadas por esta definição. <br/><sup>\*</sup>O padrão para um plano de consumo é de 200. O padrão para um plano dedicado não é limitado `-1` ().|
|rotaPrefix|api|O prefixo da rota que se aplica a todas as rotas. Utilize uma corda vazia para remover o prefixo predefinido. |

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função a partir de um pedido HTTP](./functions-bindings-http-webhook-trigger.md)