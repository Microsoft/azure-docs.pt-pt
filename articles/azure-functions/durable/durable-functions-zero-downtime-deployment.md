---
title: Implementação de tempo de inatividade zero para funções duradouras
description: Saiba como ativar a orquestração de Funções Duráveis para implementações de tempo zero.
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.custom: fasttrack-edit
ms.openlocfilehash: 707d624c47c536e00e98910a8902772703733515
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558768"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Implementação de tempo de inatividade zero para funções duradouras

O [modelo de execução fiável](./durable-functions-orchestrations.md) de Funções Duradouras requer que as orquestrações sejam determinísticas, o que cria um desafio adicional a considerar quando implementa atualizações. Quando uma implantação contém alterações nas assinaturas de funções de atividade ou lógica de orquestrador, as instâncias de orquestração a bordo falham. Esta situação é especialmente um problema para exemplos de orquestrações de longa duração, que podem representar horas ou dias de trabalho.

Para evitar que estas falhas aconteçam, tem duas opções: 
- Atrase a sua colocação até que todas as instâncias de orquestração em execução tenham terminado.
- Certifique-se de que quaisquer instâncias de orquestração em execução utilizam as versões existentes das suas funções. 

O gráfico a seguir compara as três principais estratégias para alcançar uma implementação de tempo de inatividade zero para funções duradouras: 

| Estratégia |  Quando utilizar | Vantagens | Desvantagens |
| -------- | ------------ | ---- | ---- |
| [Controlo de versões](#versioning) |  Aplicações que não experimentam [mudanças frequentes de quebra.](durable-functions-versioning.md) | Simples de implementar. |  Aumento do tamanho da aplicação da função na memória e número de funções.<br/>Duplicação de códigos. |
| [Verificação de estado com ranhura](#status-check-with-slot) | Um sistema que não tem orquestrações de longa duração que duram mais de 24 horas ou frequentemente sobrepostas orquestrações. | Base de código simples.<br/>Não requer gestão adicional de aplicativos de função. | Requer uma conta de armazenamento adicional ou gestão de centros de tarefas.<br/>Requer períodos de tempo em que não há orquestrações. |
| [Encaminhamento de aplicações](#application-routing) | Um sistema que não tem períodos de tempo em que as orquestrações não estão a funcionar, como os períodos de tempo com orquestrações que duram mais de 24 horas ou com orquestrações frequentemente sobrepostas. | Lida com novas versões de sistemas com orquestrações em execução contínua que têm mudanças de rutura. | Requer um router de aplicação inteligente.<br/>Poderia aumentar o número de aplicações de função permitidas pela sua subscrição. A predefinição é 100. |

## <a name="versioning"></a>Controlo de versões

Defina novas versões das suas funções e deixe as versões antigas na sua aplicação de função. Como pode ver no diagrama, a versão de uma função torna-se parte do seu nome. Como as versões anteriores das funções são preservadas, as instâncias de orquestração a bordo podem continuar a referenciar-las. Entretanto, os pedidos de novas instâncias de orquestração exigem a versão mais recente, que a função do cliente de orquestração pode fazer referência a partir de uma configuração de aplicações.

![Estratégia de versão](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

Nesta estratégia, todas as funções devem ser copiadas e as suas referências a outras funções devem ser atualizadas. Pode facilitar a escrita de um guião. Aqui está um [projeto com](https://github.com/TsuyoshiUshio/DurableVersioning) um guião de migração.

>[!NOTE]
>Esta estratégia utiliza slots de implementação para evitar tempo de inatividade durante a implementação. Para obter informações mais detalhadas sobre como criar e utilizar novas ranhuras de implantação, consulte [as ranhuras de implementação do Azure Functions](../functions-deployment-slots.md).

## <a name="status-check-with-slot"></a>Verificação de estado com ranhura

Enquanto a versão atual da sua aplicação de função está a funcionar na sua ranhura de produção, implemente a nova versão da sua aplicação de função para a sua ranhura de preparação. Antes de trocar as suas faixas de produção e de encenação, verifique se existem casos de orquestração em execução. Depois de todas as instâncias de orquestração estarem completas, pode fazer a troca. Esta estratégia funciona quando se tem períodos previsíveis em que não há casos de orquestração em voo. Esta é a melhor abordagem quando as suas orquestrações não são longas e quando as suas execuções de orquestração não se sobrepõem frequentemente.

### <a name="function-app-configuration"></a>Configuração de aplicativo de função

Utilize o seguinte procedimento para configurar este cenário.

1. [Adicione slots de implementação](../functions-deployment-slots.md#add-a-slot) à sua aplicação de função para a realização e produção.

1. Para cada slot, defina a definição da [aplicação AzureWebJobsStorage](../functions-app-settings.md#azurewebjobsstorage) para a cadeia de ligação de uma conta de armazenamento partilhada. Esta cadeia de ligação à conta de armazenamento é utilizada pelo tempo de funcionamento das Funções Azure. Esta conta é utilizada pelo tempo de funcionamento das Funções Azure e gere as teclas da função.

1. Para cada slot, crie uma nova configuração de aplicações, por exemplo, `DurableManagementStorage` . Desa couuser o seu valor à cadeia de ligação de diferentes contas de armazenamento. Estas contas de armazenamento são utilizadas pela extensão funções duráveis para [uma execução fiável](./durable-functions-orchestrations.md). Utilize uma conta de armazenamento separada para cada ranhura. Não marque esta definição como uma definição de ranhura de implantação.

1. Nahost.jsda sua aplicação de função [ na secção duraableTask do ficheiro,](durable-functions-bindings.md#hostjson-settings)especifique `connectionStringName` (Durable 2.x) ou `azureStorageConnectionStringName` (Durable 1.x) como o nome da definição de aplicação que criou no passo 3.

O diagrama seguinte mostra a configuração descrita de slots de implantação e contas de armazenamento. Neste cenário de pré-desafectação potencial, a versão 2 de uma aplicação de função encontra-se em execução na ranhura de produção, enquanto a versão 1 permanece na ranhura de preparação.

![Slots de implantação e contas de armazenamento](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>host.jsem exemplos

Os seguintes fragmentos JSON são exemplos da definição da cadeia de ligação no *host.jsficheiro.*

#### <a name="functions-20"></a>Funções 2.0

```json
{
  "version": 2.0,
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub",
      "storageProvider": {
        "connectionStringName": "DurableManagementStorage"
      }
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

Configure o seu pipeline CI/CD para ser implantado apenas quando a sua aplicação de função não tiver instâncias de orquestração pendentes ou em execução. Quando estiver a utilizar os Gasodutos Azure, pode criar uma função que verifica estas condições, como no exemplo seguinte:

```csharp
[FunctionName("StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var result = await client.ListInstancesAsync(new OrchestrationStatusQueryCondition() { RuntimeStatus = runtimeStatus }, CancellationToken.None);
    return (ActionResult)new OkObjectResult(new { HasRunning = result.DurableOrchestrationState.Any() });
}
```

Em seguida, configuure o portão de preparação para esperar até que não estejam a decorrer orquestrações. Para obter mais informações, consulte [o controlo de implementação de desbloqueio utilizando portões](/azure/devops/pipelines/release/approvals/gates)

![Porta de implementação](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

A Azure Pipelines verifica a sua aplicação de função para executar instâncias de orquestração antes do início da sua implantação.

![Portão de implantação (em funcionamento)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

Agora, a nova versão da sua aplicação de função deve ser implementada para a ranhura de preparação.

![Slot de encenação](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

Finalmente, trocar slots. 

As definições de aplicação que não estão marcadas como configurações de slot de implementação também são trocadas, pelo que a aplicação da versão 2 mantém a sua referência à conta de armazenamento A. Como o estado de orquestração é rastreado na conta de armazenamento, quaisquer orquestrações em execução na aplicação da versão 2 continuam a funcionar na nova ranhura sem interrupção.

![Ranhura de implementação](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

Para utilizar a mesma conta de armazenamento para ambas as ranhuras, pode alterar os nomes dos seus centros de tarefa. Neste caso, precisa de gerir o estado das suas slots e as definições do HubName da sua aplicação. Para saber mais, consulte [os centros de tarefa em Funções Duradouras.](durable-functions-task-hubs.md)

## <a name="application-routing"></a>Encaminhamento de aplicações

Esta estratégia é a mais complexa. No entanto, pode ser usado para apps de função que não têm tempo entre executar orquestrações.

Para esta estratégia, deve criar um router de *aplicações* em frente às suas Funções Duráveis. Este router pode ser implementado com Funções Duradouras. O router tem a responsabilidade de:

* Implemente a aplicação de função.
* Gerir a versão de Funções Duradouras. 
* Encaminhar pedidos de orquestração para funcionar apps.

Na primeira vez que um pedido de orquestração é recebido, o router faz as seguintes tarefas:

1. Cria uma nova aplicação de função em Azure.
2. Implementa o código da sua aplicação de função para a nova aplicação de função em Azure.
3. Remete o pedido de orquestração para a nova app.

O router gere o estado de que versão do código da sua aplicação é implementada para qual aplicação de função em Azure.

![Encaminhamento de aplicações (primeira vez)](media/durable-functions-zero-downtime-deployment/application-routing.png)

O router direciona pedidos de implantação e orquestração para a aplicação de função apropriada com base na versão enviada com o pedido. Ignora a versão do patch.

Quando implementar uma nova versão da sua aplicação sem uma alteração de rutura, pode incrementar a versão patch. O router implementa a sua app de função existente e envia pedidos para as versões antigas e novas do código, que são encaminhada para a mesma aplicação de função.

![Encaminhamento de aplicações (sem alteração de rutura)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

Quando implementar uma nova versão da sua aplicação com uma alteração de rutura, pode incrementar a versão principal ou menor. Em seguida, o router de aplicações cria uma nova aplicação de função no Azure, implementa-se na sua e encaminha os pedidos para a nova versão da sua app para a própria. No diagrama seguinte, as orquestrações em execução na versão 1.0.1 da app continuam em execução, mas os pedidos para a versão 1.1.0 são encaminhados para a nova aplicação de funções.

![Encaminhamento de aplicações (alteração de rutura)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

O router monitoriza o estado das orquestrações na versão 1.0.1 e remove as aplicações depois de todas as orquestrações estarem terminadas. 

### <a name="tracking-store-settings"></a>Definições de loja de rastreio

Cada aplicação de função deve usar filas de agendamento separadas, possivelmente em contas de armazenamento separadas. Se quiser consultar todas as instâncias de orquestração em todas as versões da sua aplicação, pode partilhar instâncias e tabelas de história através das suas aplicações de função. Pode partilhar tabelas configurando as `trackingStoreConnectionStringName` definições e `trackingStoreNamePrefix` as definições no ficheirohost.jsno ficheiro [ de definições](durable-functions-bindings.md#host-json) para que todas utilizem os mesmos valores.

Para obter mais informações, consulte [Gerir casos em Funções Duradouras em Azure](durable-functions-instance-management.md).

![Definições de loja de rastreio](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Versão Funções Duradouras](durable-functions-versioning.md)
