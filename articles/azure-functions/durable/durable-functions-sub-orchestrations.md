---
title: Orquestrações secundária para funções duráveis - Azure
description: Como chamar orquestrações de orquestrações na extensão de funções duráveis para as funções do Azure.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 1ab9a5714a7ef24b51957bd48b1b67240cf13adb
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607671"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Orquestrações secundárias nas funções duráveis (funções do Azure)

Para além de chamar funções de atividade, as funções do orchestrator podem chamar outras funções do orchestrator. Por exemplo, pode criar uma orquestração maior fora de uma biblioteca de funções do orchestrator. Em alternativa, pode executar várias instâncias de uma função de orquestrador em paralelo.

Uma função de orquestrador pode chamar a função de orquestrador de outro ao chamar o [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) ou o [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) métodos no .NET, ou o `callSubOrchestrator` ou `callSubOrchestratorWithRetry` métodos em JavaScript. O [tratamento de erro & compensação](durable-functions-error-handling.md#automatic-retry-on-failure) artigo tem mais informações sobre a repetição automática.

As funções do orchestrator secundárias se comportam como funções de atividade do ponto de vista do chamador. Eles podem retornar um valor, lance uma exceção e podem ser colocadas em espera pela função de orquestrador principal.

## <a name="example"></a>Exemplo

O exemplo a seguir ilustra um cenário de IoT ("Internet das coisas") em que existem vários dispositivos que têm de ser aprovisionado. Há uma orquestração específica que precisa para ocorrer para cada um dos dispositivos, que podem ser semelhante ao seguinte:

### <a name="c"></a>C#

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

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

Esta função de orquestrador pode ser usada como-é para aprovisionamento de dispositivos pontuais ou ele pode fazer parte de uma orquestração maior. No último caso, a função de orquestrador principal pode agendar a instâncias de `DeviceProvisioningOrchestration` utilizando o `CallSubOrchestratorAsync` (C#) ou `callSubOrchestrator` (JavaScript) API.

Eis um exemplo que mostra como executar várias funções do orchestrator em paralelo.

### <a name="c"></a>C#

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    for (const deviceId of deviceIds) {
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.push(provisionTask);
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba quais são os hubs de tarefas e como configurá-las](durable-functions-task-hubs.md)
