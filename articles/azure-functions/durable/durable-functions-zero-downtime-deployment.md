---
title: Implantação de tempo zero para funções duráveis
description: Aprenda a permitir a sua orquestração de Funções Duráveis para implementações de tempo zero.
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.openlocfilehash: 8e12d58c0077084c181d111b0b017665b74b9157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74231262"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Implantação de tempo zero para funções duráveis

O [modelo de execução fiável](durable-functions-checkpointing-and-replay.md) das Funções Duráveis requer que as orquestrações sejam determinísticas, o que cria um desafio adicional a considerar quando implementa atualizações. Quando uma implantação contém alterações nas assinaturas da função de atividade ou lógica orquestradora, as instâncias de orquestração a bordo falham. Esta situação é especialmente um problema para os casos de orquestrações de longa duração, que podem representar horas ou dias de trabalho.

Para evitar que estas falhas aconteçam, tem duas opções: 
- Atrase a sua implantação até que todos os casos de orquestração em execução tenham terminado.
- Certifique-se de que quaisquer instâncias de orquestração em execução utilizam as versões existentes das suas funções. 

> [!NOTE]
> Este artigo fornece orientação para aplicações de funções que visam funções duráveis 1.x. Não foi atualizado para ter em conta as alterações introduzidas nas Funções Duráveis 2.x. Para obter mais informações sobre as diferenças entre versões de extensão, consulte [versões De Funções Duráveis](durable-functions-versions.md).

O gráfico seguinte compara as três principais estratégias para alcançar uma implementação de tempo zero para funções duráveis: 

| Estratégia |  Quando utilizar | Vantagens | Contras |
| -------- | ------------ | ---- | ---- |
| [Controlo de versões](#versioning) |  Aplicações que não experimentam mudanças frequentes [de quebra.](durable-functions-versioning.md) | Simples de implementar. |  Aumento do tamanho da aplicação de função na memória e número de funções.<br/>Duplicação de código. |
| [Verificação de estado com ranhura](#status-check-with-slot) | Um sistema que não tem orquestrações de longa duração que duram mais de 24 horas ou frequentemente sobrepõem orquestrações. | Base de código simples.<br/>Não requer gestão adicional de aplicações de função. | Requer uma conta de armazenamento adicional ou uma gestão do centro de tarefas.<br/>Requer períodos de tempo em que não há orquestrações a decorrer. |
| [Encaminhamento de aplicações](#application-routing) | Um sistema que não tem períodos de tempo em que as orquestrações não estão a funcionar, como os períodos de tempo com orquestrações que duram mais de 24 horas ou com orquestrações frequentemente sobrepostas. | Lida com novas versões de sistemas com orquestrações contínuas que têm mudanças de rutura. | Requer um router de aplicação inteligente.<br/>Poderia atingir o número de aplicações de função permitidas pela sua subscrição. A predefinição é 100. |

## <a name="versioning"></a>Controlo de versões

Defina novas versões das suas funções e deixe as versões antigas na sua aplicação de funções. Como pode ver no diagrama, a versão de uma função torna-se parte do seu nome. Como as versões anteriores das funções são preservadas, as instâncias de orquestração a bordo podem continuar a referi-las. Entretanto, os pedidos de novas instâncias de orquestração exigem a versão mais recente, que a função do cliente da orquestração pode fazer referência a partir de uma configuração de aplicação.

![Estratégia de versão](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

Nesta estratégia, todas as funções devem ser copiadas e as suas referências a outras funções devem ser atualizadas. Pode facilitar a escrita de um guião. Aqui está um [projeto de amostra](https://github.com/TsuyoshiUshio/DurableVersioning) com um roteiro de migração.

>[!NOTE]
>Esta estratégia utiliza ranhuras de implantação para evitar o tempo de inatividade durante a implantação. Para obter informações mais detalhadas sobre como criar e utilizar novas ranhuras de implantação, consulte as ranhuras de implantação das [Funções Azure](../functions-deployment-slots.md).

## <a name="status-check-with-slot"></a>Verificação de estado com ranhura

Enquanto a versão atual da sua aplicação de funções estiver a funcionar na sua ranhura de produção, implemente a nova versão da sua aplicação de função para o seu slot de encenação. Antes de trocar as suas ranhuras de produção e encenação, verifique se existem casos de orquestração em execução. Depois de todos os casos de orquestração estarem completos, pode fazer a troca. Esta estratégia funciona quando se tem períodos previsíveis quando não há casos de orquestração em voo. Esta é a melhor abordagem quando as suas orquestrações não são longas e quando as suas execuções de orquestração não se sobrepõem frequentemente.

### <a name="function-app-configuration"></a>Configuração de aplicativo de função

Utilize o seguinte procedimento para configurar este cenário.

1. [Adicione ranhuras](../functions-deployment-slots.md#add-a-slot) de implementação à sua aplicação de funções para encenação e produção.

1. Para cada ranhura, defina a definição da [aplicação AzureWebJobsStorage](../functions-app-settings.md#azurewebjobsstorage) na cadeia de ligação de uma conta de armazenamento partilhada. Esta cadeia de ligação à conta de armazenamento é utilizada pelo tempo de funcionamento das Funções Azure. Esta conta é utilizada pelo tempo de funcionamento das Funções Azure e gere as teclas da função.

1. Para cada ranhura, crie uma nova `DurableManagementStorage`configuração de aplicações, por exemplo, . Delineie o seu valor para a cadeia de ligação de diferentes contas de armazenamento. Estas contas de armazenamento são utilizadas pela extensão das Funções Duráveis para [uma execução fiável](durable-functions-checkpointing-and-replay.md). Utilize uma conta de armazenamento separada para cada ranhura. Não marque esta definição como uma definição de ranhura de implantação.

1. Na secção de [tarefas duradouras do ficheiro host.json](durable-functions-bindings.md#hostjson-settings)da sua aplicação de função, especifique `azureStorageConnectionStringName` como o nome da definição da aplicação que criou no passo 3.

O diagrama seguinte mostra a configuração descrita de ranhuras de implantação e contas de armazenamento. Neste potencial cenário de pré-implantação, a versão 2 de uma aplicação de função está a funcionar na ranhura de produção, enquanto a versão 1 permanece na ranhura de encenação.

![Slots de implantação e contas de armazenamento](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>exemplos host.json

Os seguintes fragmentos JSON são exemplos da definição de cadeia de ligação no ficheiro *host.json.*

#### <a name="functions-20"></a>Funções 2.0

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

### <a name="cicd-pipeline-configuration"></a>Configuração do gasoduto CI/CD

Configure o seu pipeline CI/CD para implantar apenas quando a sua aplicação de funções não tiver instâncias de orquestração pendentes ou em execução. Quando estiver a utilizar os Gasodutos Azure, pode criar uma função que verifica estas condições, como no seguinte exemplo:

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

Em seguida, configure o portão de preparação para esperar até que nenhuma orquestração esteja em execução. Para mais informações, consulte o controlo de implementação de [lançamento utilizando portões](/azure/devops/pipelines/release/approvals/gates?view=azure-devops)

![Porta de implementação](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

A Azure Pipelines verifica a sua aplicação de funções para executar instâncias de orquestração antes do início da sua implementação.

![Portão de implantação (em execução)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

Agora, a nova versão da sua aplicação de funções deve ser implementada para a ranhura de encenação.

![Ranhura de encenação](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

Finalmente, troque as ranhuras. 

As definições de aplicação que não estão marcadas como definições de slot de implementação também são trocadas, pelo que a aplicação versão 2 mantém a sua referência à conta de armazenamento A. Como o estado de orquestração é rastreado na conta de armazenamento, quaisquer orquestrações em execução na aplicação da versão 2 continuam a funcionar na nova ranhura sem interrupção.

![Ranhura de implementação](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

Para utilizar a mesma conta de armazenamento para ambas as ranhuras, pode alterar os nomes dos seus centros de tarefas. Neste caso, precisa de gerir o estado das suas faixas horárias e as definições do HubName da sua aplicação. Para saber mais, consulte [os centros de tarefas em Funções Duráveis.](durable-functions-task-hubs.md)

## <a name="application-routing"></a>Encaminhamento de aplicações

Esta estratégia é a mais complexa. No entanto, pode ser usado para aplicações de funções que não têm tempo entre orquestrações de execução.

Para esta estratégia, deve criar um router de *aplicação* em frente às suas Funções Duráveis. Este router pode ser implementado com Funções Duráveis. O router tem a responsabilidade de:

* Implemente a aplicação de função.
* Gerir a versão das Funções Duráveis. 
* Encaminhe pedidos de orquestração para funcionar apps.

A primeira vez que um pedido de orquestração é recebido, o router faz as seguintes tarefas:

1. Cria uma nova aplicação de funções em Azure.
2. Implementa o código da sua aplicação de funções para a nova aplicação de funções no Azure.
3. Reencaminha o pedido de orquestração para a nova app.

O router gere o estado de qual versão do código da sua aplicação é implementada para a aplicação de funções em Azure.

![Encaminhamento de aplicações (primeira vez)](media/durable-functions-zero-downtime-deployment/application-routing.png)

O router direciona pedidos de implantação e orquestração para a aplicação de função apropriada com base na versão enviada com o pedido. Ignora a versão do patch.

Quando implementar uma nova versão da sua aplicação sem uma alteração de rutura, pode incrementar a versão patch. O router implementa para a sua aplicação de função existente e envia pedidos para as versões antigas e novas do código, que são encaminhados para a mesma aplicação de funções.

![Encaminhamento de aplicações (sem alteração de rutura)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

Quando implementar uma nova versão da sua aplicação com uma mudança de rutura, pode incrementar a versão principal ou menor. Em seguida, o router de aplicação cria uma nova aplicação de função no Azure, implanta-se para o mesmo, e encaminha os pedidos para a nova versão da sua aplicação para o mesmo. No diagrama seguinte, as orquestrações de execução na versão 1.0.1 da aplicação continuam a funcionar, mas os pedidos para a versão 1.1.0 são encaminhados para a nova aplicação de funções.

![Encaminhamento de aplicações (alteração de rutura)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

O router monitoriza o estado das orquestrações na versão 1.0.1 e remove as aplicações depois de todas as orquestrações estarem terminadas. 

### <a name="tracking-store-settings"></a>Definições de loja de rastreio

Cada aplicação de função deve utilizar filas de agendamento separadas, possivelmente em contas de armazenamento separadas. Se quiser consultar todas as instâncias de orquestração em todas as versões da sua aplicação, pode partilhar instâncias e tabelas de história nas suas aplicações de funções. Pode partilhar tabelas configurando as `trackingStoreConnectionStringName` definições e `trackingStoreNamePrefix` definições no ficheiro de [definições host.json](durable-functions-bindings.md#host-json) para que todos utilizem os mesmos valores.

Para mais informações, consulte [Gerir instâncias em Funções Duráveis em Azure](durable-functions-instance-management.md).

![Definições de loja de rastreio](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Veredura Funções Duráveis](durable-functions-versioning.md)

