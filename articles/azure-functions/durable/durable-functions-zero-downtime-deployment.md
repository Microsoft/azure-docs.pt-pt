---
title: Implantação sem tempo de inatividade para Durable Functions
description: Saiba como habilitar sua orquestração de Durable Functions para implantações sem tempo de inatividade.
services: functions
author: tsushi
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.openlocfilehash: b47604f2c8703ba587e98d68dc30552e5944f562
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614492"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Implantação sem tempo de inatividade para Durable Functions

O [modelo de execução confiável](durable-functions-checkpointing-and-replay.md) do Durable Functions exige que as orquestrações sejam determinísticas, o que cria um desafio adicional a ser considerado ao implantar atualizações. Quando uma implantação contém alterações nas assinaturas da função de atividade ou na lógica do Orchestrator, as instâncias de orquestração em andamento falham. Essa situação é especialmente um problema para instâncias de orquestrações de longa execução, que podem representar horas ou dias de trabalho.

Para evitar que essas falhas ocorram, você deve atrasar a implantação até que todas as instâncias de orquestração em execução tenham sido concluídas ou certificar-se de que todas as instâncias de orquestração em execução usem as versões existentes de suas funções. Para obter mais informações sobre o controle de versão, consulte [controle de versão em Durable Functions](durable-functions-versioning.md).

> [!NOTE]
> Este artigo fornece diretrizes para aplicativos de funções direcionados a Durable Functions 1. x. Ele ainda não foi atualizado para considerar as alterações introduzidas no Durable Functions 2. x. Para obter mais informações sobre as diferenças entre as versões de extensão, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

O gráfico a seguir compara as três estratégias principais para obter uma implantação de tempo de inatividade zero para Durable Functions: 

| Estratégia |  Quando utilizar | Prós | Contras |
| -------- | ------------ | ---- | ---- |
| **[Versão](#versioning)** |  Aplicativos que não experimentam [alterações significativas](durable-functions-versioning.md) frequentes. | Simples de implementar. |  Maior tamanho do aplicativo de funções na memória e no número de funções.<br/>Duplicação de código. |
| **[Verificação de status com slot](#status-check-with-slot)** | Um sistema que não tem orquestrações de longa duração que têm mais de 24 horas ou orquestrações sobrepostas com frequência. | Base de código simples.<br/>Não requer gerenciamento de aplicativo de funções adicional. | Requer gerenciamento de Hub de tarefas ou conta de armazenamento adicional.<br/>Requer períodos de tempo em que nenhuma orquestração está em execução. |
| **[Roteamento de aplicativos](#application-routing)** | Um sistema que não tem períodos de tempo em que as orquestrações não estão em execução, como aquelas com orquestrações duradouras mais de 24 horas ou com orquestrações com sobreposição frequentes. | Lida com novas versões de sistemas com orquestrações continuamente em execução que têm alterações significativas. | Requer um roteador de aplicativo inteligente.<br/>Pode ser o número máximo de aplicativos de funções permitidos pela sua assinatura (padrão 100). |

## <a name="versioning"></a>Controlo de versões

Defina novas versões de suas funções e deixe as versões antigas em seu aplicativo de funções. Como você pode ver no diagrama, a versão da função se torna parte do seu nome. Como as versões anteriores das funções são preservadas, as instâncias de orquestração em andamento podem continuar a referenciá-las. Enquanto isso, as solicitações para novas instâncias de orquestração chamam a versão mais recente, à qual sua função de cliente de orquestração pode fazer referência a partir de uma configuração de aplicativo.

![Estratégia de controle de versão](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

Nessa estratégia, cada função deve ser copiada e suas referências a outras funções são atualizadas. Você pode torná-lo mais fácil escrevendo um script. Aqui está um [projeto de exemplo](https://github.com/TsuyoshiUshio/DurableVersioning) com um script de migração.

>[!NOTE]
>Essa estratégia usa slots de implantação para evitar o tempo de inatividade durante a implantação. Para obter informações mais detalhadas sobre como criar e usar novos slots de implantação, consulte [Azure Functions slots de implantação](../functions-deployment-slots.md).

## <a name="status-check-with-slot"></a>Verificação de status com slot

Embora a versão atual do seu aplicativo de funções esteja em execução no slot de produção, implante a nova versão do seu aplicativo de funções em seu slot de preparo. Antes de trocar os slots de produção e de preparo, verifique se há instâncias de orquestração em execução. Depois que todas as instâncias de orquestração forem concluídas, você poderá fazer a permuta. Essa estratégia funciona quando você tem períodos previsíveis quando não há instâncias de orquestração em andamento. Essa é a melhor abordagem quando suas orquestrações não são de execução longa e quando suas execuções de orquestração não se sobrepõem com frequência.

### <a name="function-app-configuration"></a>Configuração do aplicativo de funções

Você pode usar o procedimento a seguir para configurar este cenário:

1. [Adicione slots de implantação](../functions-deployment-slots.md#add-a-slot) ao seu aplicativo de funções para preparo e produção.

1. Para cada slot, defina a [configuração de aplicativo AzureWebJobsStorage](../functions-app-settings.md#azurewebjobsstorage) como a cadeia de conexão de uma conta de armazenamento compartilhada. Isso será usado pelo Azure Functions Runtime. Essa conta será usada pelo Azure Functions Runtime e gerenciará as chaves da função.

1. Para cada slot, crie uma nova configuração de aplicativo (por exemplo, DurableManagementStorage) e defina seu valor como a cadeia de conexão de diferentes contas de armazenamento. Essas contas de armazenamento serão usadas pela extensão de Durable Functions para [execução confiável](durable-functions-checkpointing-and-replay.md). Use uma conta de armazenamento separada para cada slot. Não marque essa configuração como uma configuração de slot de implantação.

1. Na [seção durableTask do arquivo host. JSON](durable-functions-bindings.md#hostjson-settings)do seu aplicativo de funções, especifique azureStorageConnectionStringName como o nome da configuração do aplicativo que você criou na etapa 3.

O diagrama a seguir mostra ilustra a configuração descrita de Slots de implantação e contas de armazenamento. Nesse cenário potencial de pré-implantação, a versão 2 de um aplicativo de funções está em execução no slot de produção, enquanto a versão 1 permanece no slot de preparo.

![Ranhura de implementação](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>exemplos de host. JSON

Os fragmentos JSON a seguir são exemplos da configuração da cadeia de conexão no arquivo host. JSON.

#### <a name="functions-20"></a>Funções 2,0

```json
{
  "version": 2.0,
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "DurableManagementStorage"
    }
  }
}
```

#### <a name="functions-1x"></a>Funções 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "DurableManagementStorage"
  }
}
```

### <a name="cicd-pipeline-configuration"></a>Configuração de pipeline de CI/CD

Configure seu pipeline de CI/CD para implantar somente quando seu aplicativo de funções não tiver instâncias de orquestração pendentes ou em execução. Quando você estiver usando Azure Pipelines, poderá criar uma função que verifica essas condições, como no exemplo a seguir:

```csharp
[FunctionName("StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var status = await client.GetStatusAsync(new DateTime(2015,10,10), null, runtimeStatus);
    return (ActionResult) new OkObjectResult(new Status() {HasRunning = (status.Count != 0)});
}
```

Em seguida, configure o portão de preparo para aguardar até que nenhuma orquestração esteja em execução. Para obter mais informações, consulte [liberar controle de implantação usando Gates](/azure/devops/pipelines/release/approvals/gates?view=azure-devops)

![Portão de implantação](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure Pipelines verifica seu aplicativo de funções para executar instâncias de orquestração antes do início da implantação.

![Portão de implantação (em execução)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

Agora, a nova versão do seu aplicativo de funções deve ser implantada no slot de preparo.

![Ranhura de implementação](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

Por fim, slots de permuta. 

As configurações do aplicativo que não são marcadas como configurações do slot de implantação também são trocadas, portanto, o aplicativo da versão 2 mantém sua referência à conta de armazenamento A. Como o estado de orquestração é acompanhado na conta de armazenamento, todas as orquestrações em execução no aplicativo da versão 2 continuam a ser executadas no novo slot sem interrupção.

![Ranhura de implementação](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

Para usar a mesma conta de armazenamento para ambos os slots, você pode alterar os nomes dos hubs de tarefas. Nesse caso, você precisa gerenciar o estado de seus slots e suas configurações de HubName de aplicativos. Para saber mais, confira [hubs de tarefas em Durable Functions](durable-functions-task-hubs.md).

## <a name="application-routing"></a>Roteamento de aplicativos

Essa estratégia é a mais complexa. No entanto, ele pode ser usado para aplicativos de funções que não têm tempo entre orquestrações em execução.

Para essa estratégia, você deve criar um *roteador de aplicativo* na frente de seu Durable functions. Esse roteador pode ser implementado com Durable Functions e tem as seguintes responsabilidades:

* Implantando o aplicativo de funções.
* Gerenciando a versão do Durable Functions. 
* Roteamento de solicitações de orquestração para aplicativos de funções.

Na primeira vez que uma solicitação de orquestração é recebida, o roteador realiza as tarefas:

1. Cria um novo aplicativo de funções no Azure.
2. Implanta o código do aplicativo de funções no novo aplicativo de funções no Azure.
3. Encaminha a solicitação de orquestração para o novo aplicativo.

O roteador gerencia o estado de qual versão do código do aplicativo é implantada em qual aplicativo de funções no Azure.

![Roteamento de aplicativos (primeira vez)](media/durable-functions-zero-downtime-deployment/application-routing.png)

O roteador direciona as solicitações de implantação e orquestração para o aplicativo de funções apropriado com base no `version` enviado com a solicitação, ignorando a versão do patch.

Ao implantar uma nova versão do seu aplicativo *sem* uma alteração significativa, você pode incrementar a versão do patch. O roteador é implantado em seu aplicativo de funções existente e envia solicitações para as versões antigas e novas do código são roteadas para o mesmo aplicativo de funções.

![Roteamento de aplicativos (sem alteração significativa)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

Ao implantar uma nova versão do seu aplicativo com uma alteração significativa, você pode incrementar a versão principal ou secundária. Em seguida, o roteador de aplicativo cria um novo aplicativo de funções no Azure, implanta-o e roteia solicitações para a nova versão do seu aplicativo para ele. No diagrama a seguir, a execução de orquestrações na versão 1.0.1 do aplicativo continua em execução, mas as solicitações para a versão 1.1.0 são roteadas para o novo aplicativo de funções.

![Roteamento de aplicativos (alteração significativa)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

O roteador monitora o status de orquestrações na versão 1.0.1 e remove os aplicativos após a conclusão de todas as orquestrações.  

### <a name="tracking-store-settings"></a>Configurações do repositório de rastreamento

Cada aplicativo de funções deve usar filas de agendamento separadas, possivelmente em contas de armazenamento separadas. No entanto, se você quiser consultar todas as instâncias de orquestrações em todas as versões do seu aplicativo, poderá compartilhar as tabelas de instâncias e de histórico em seus aplicativos de funções. Você pode compartilhar tabelas Configurando o `trackingStoreConnectionStringName` e `trackingStoreNamePrefix` no arquivo de [configurações do host. JSON](durable-functions-bindings.md#host-json) para que todos usem os mesmos valores.

Para obter mais detalhes, [gerencie instâncias no Durable Functions no Azure](durable-functions-instance-management.md).

![Configurações do repositório de rastreamento](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Durable Functions de controle de versão](durable-functions-versioning.md)

