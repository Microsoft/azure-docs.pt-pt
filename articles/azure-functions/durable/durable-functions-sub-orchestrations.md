---
title: Suborquestrações para Funções Duráveis - Azure
description: Como chamar orquestrações de orquestrações na extensão de Funções Duráveis para Funções Azure.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: d4d599063f727510cbf504ea3d121bdabfe001c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76261522"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Suborquestrações em Funções Duráveis (Funções Azure)

Além de chamar funções de atividade, as funções de orquestração podem chamar outras funções de orquestrador. Por exemplo, você pode construir uma orquestração maior a partir de uma biblioteca de funções orquestradoras menores. Ou pode executar várias instâncias de uma função orquestradora em paralelo.

Uma função orquestradora pode chamar `CallSubOrchestratorAsync` outra `CallSubOrchestratorWithRetryAsync` função de orquestrador usando os ou os métodos em .NET, ou os `callSubOrchestrator` ou `callSubOrchestratorWithRetry` métodos em JavaScript. O artigo de [manipulação de erros & Compensação](durable-functions-error-handling.md#automatic-retry-on-failure) tem mais informações sobre a retentativa automática.

As funções sub-orquestradoras comportam-se como funções de atividade do ponto de vista do chamador. Podem devolver um valor, lançar uma exceção, e podem ser aguardados pela função orquestradora-mãe. 
## <a name="example"></a>Exemplo

O exemplo que se segue ilustra um cenário IoT ("Internet das Coisas") onde existem vários dispositivos que precisam de ser aprovisionados. A seguinte função representa o fluxo de trabalho de provisionamento que deve ser executado para cada dispositivo:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string deviceId = context.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await context.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await context.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await context.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceId = context.df.getInput();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    const sasUrl = yield context.df.callActivity("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    yield context.df.callActivity("SendPackageUrlToDevice", { id: deviceId, url: sasUrl });

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.df.waitForExternalEvent("DownloadCompletedAck");

    // Step 4: ...
});
```

---

Esta função de orquestrador pode ser usada como é para o fornecimento de dispositivos únicos ou pode fazer parte de uma orquestração maior. Neste último caso, a função de orquestrador-mãe pode agendar instâncias de utilização da `DeviceProvisioningOrchestration` `CallSubOrchestratorAsync` API (.NET) ou `callSubOrchestrator` (JavaScript).

Aqui está um exemplo que mostra como executar múltiplas funções orquestradoras em paralelo.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string[] deviceIds = await context.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = context.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

> [!NOTE]
> Os exemplos c# anteriores são para Funções Duráveis 2.x. Para funções duráveis 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    var id = 0;
    for (const deviceId of deviceIds) {
        const child_id = context.df.instanceId+`:${id}`;
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId, child_id);
        provisioningTasks.push(provisionTask);
        id++;
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

---

> [!NOTE]
> As suborquestrações devem ser definidas na mesma aplicação de funções que a orquestração dos pais. Se precisar de ligar e esperar por orquestrações noutra aplicação de função, considere utilizar o suporte incorporado para HTTP APIs e o padrão de consumidor de sondagens HTTP 202. Para mais informações, consulte o tópico [http Features.](durable-functions-http-features.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como definir um estatuto de orquestração personalizada](durable-functions-custom-orchestration-status.md)