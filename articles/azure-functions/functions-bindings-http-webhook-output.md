---
title: Ligações de saída do Azure Functions HTTP
description: Saiba como devolver respostas http em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: a25658677e436edf4d001599bb4981f527016596
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277625"
---
# <a name="azure-functions-http-output-bindings"></a>Ligações de saída do Azure Functions HTTP

Utilize a ligação de saída HTTP para responder ao remetente de pedidos HTTP. Esta ligação requer um gatilho HTTP e permite-lhe personalizar a resposta associada ao pedido do gatilho.

O valor de devolução predefinido para uma função desencadeada pelo HTTP é:

- `HTTP 204 No Content` com um corpo vazio nas Funções 2.x e superior
- `HTTP 200 OK` com um corpo vazio nas Funções 1.x

## <a name="configuration"></a>Configuração

A tabela que se segue explica as propriedades de configuração de ligação que definiu no ficheiro *função.json.* Para C# bibliotecas de classes, não existem propriedades de atributos que correspondam a estas *propriedades funções.json.*

|Propriedade  |Descrição  |
|---------|---------|
| **tipo** |Deve ser definido para `http`. |
| **direção** | Deve ser definido para `out`. |
| **nome** | O nome variável utilizado no código de função para a resposta, ou `$return` utilizar o valor de devolução. |

## <a name="usage"></a>Utilização

Para enviar uma resposta HTTP, utilize os padrões de resposta padrão de linguagem. Dentro C# C# ou no script, faça o tipo de retorno da função `IActionResult` ou `Task<IActionResult>`. Não C#é necessário um atributo de valor de retorno.

Por exemplo, respostas, consulte o exemplo do [gatilho](./functions-bindings-http-webhook-trigger.md#example).

## <a name="hostjson-settings"></a>definições de Host. JSON

Esta secção descreve as definições de configuração global disponíveis para esta ligação nas versões 2.x e superiores. O ficheiro exemplo host.json abaixo contém apenas as definições da versão 2.x+ para esta ligação. Para obter mais informações sobre as configurações globais de configuração nas versões 2.x e além, consulte a [referência host.json para funções azure](functions-host-json.md).

> [!NOTE]
> Para uma referência do host.json nas funções 1.x, consulte a [referência host.json para as funções Azure 1.x](functions-host-json-v1.md#http).

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

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
| personalizadoHeaders|nenhuma|Permite-lhe definir cabeçalhos personalizados na resposta HTTP. O exemplo anterior adiciona o cabeçalho `X-Content-Type-Options` à resposta para evitar cheirar o tipo de conteúdo. |
|dynamicThrottlesEnabled|verdadeiro<sup>\*</sup>|Quando ativada, esta definição faz com que o gasoduto de processamento de pedidos verifique periodicamente os contadores de desempenho do sistema, como `connections/threads/processes/memory/cpu/etc` e se algum desses contadores tiver um limiar elevado incorporado (80%), os pedidos serão rejeitados com uma resposta `429 "Too Busy"` até que o contador(s) volte aos níveis normais.<br/><sup>\*</sup> O padrão num plano de consumo é `true`. O padrão num plano dedicado é `false`.|
|hsts|não habilitado|Quando `isEnabled` está definida para `true`, o [comportamento http strict transport security (HSTS) de .NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) é aplicado, conforme definido na classe [`HstsOptions`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0). O exemplo acima também define a [propriedade`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) para 10 dias. As propriedades suportadas de `hsts` são: <table><tr><th>Propriedade</th><th>Descrição</th></tr><tr><td>anfitriões excluídos</td><td>Uma série de nomes de anfitriões para os quais o cabeçalho HSTS não é adicionado.</td></tr><tr><td>incluirSubDomínios</td><td>Valor booleano que indica se o parâmetro SubDomain incluído do cabeçalho De Segurança Estrita-Transporte está ativado.</td></tr><tr><td>maxAge</td><td>Corda que define o parâmetro de idade máxima do cabeçalho De Segurança De Transporte Rigoroso.</td></tr><tr><td>pré-carga</td><td>Boolean que indica se o parâmetro de pré-carga do cabeçalho de segurança de transporte rigoroso está ativado.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|O número máximo de funções HTTP que são executadas em paralelo. Este valor permite-lhe controlar a conmoeda, o que pode ajudar a gerir a utilização de recursos. Por exemplo, pode ter uma função HTTP que utiliza um grande número de recursos do sistema (memória/cpu/tomadas) de tal forma que causa problemas quando a moeda é demasiado elevada. Ou pode ter uma função que faz pedidos de saída para um serviço de terceiros, e essas chamadas precisam de ser limitadas. Nestes casos, aplicar um acelerador aqui pode ajudar. <br/><sup>*</sup> O padrão para um plano de consumo é 100. O padrão para um plano dedicado é ilimitado (`-1`).|
|maxOutstandingRequests|200<sup>\*</sup>|O número máximo de pedidos pendentes que são realizados a qualquer momento. Este limite inclui pedidos que estão em fila mas que não começaram a executar, bem como quaisquer execuções em curso. Quaisquer pedidos de entrada acima deste limite são rejeitados com uma resposta 429 "Demasiado Ocupada". Isso permite que os chamadores empreguem estratégias de retry baseadas no tempo, e também o ajuda a controlar as lateências máximas de pedido. Isto apenas controla a fila que ocorre dentro do caminho de execução do hospedeiro script. Outras filas, como a fila de pedidos de ASP.NET, continuarão em vigor e não serão afetadas por este cenário. <br/><sup>\*</sup> O padrão para um plano de consumo é de 200. O padrão para um plano dedicado é ilimitado (`-1`).|
|routePrefix|api|O prefixo da rota que se aplica a todas as rotas. Utilize uma corda vazia para remover o prefixo predefinido. |

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função a partir de um pedido HTTP](./functions-bindings-http-webhook-trigger.md)