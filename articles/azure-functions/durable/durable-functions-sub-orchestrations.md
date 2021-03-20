---
title: Sub-orquestrações para Funções Duradouras - Azure
description: Como chamar orquestrações de orquestrações na extensão de Funções Duradouras para Funções Azure.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 5625bc2ddfa4b6f527ca16f19f33d257a1834d4b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85340821"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Sub-orquestrações em Funções Duradouras (Funções Azure)

Além de chamar funções de atividade, as funções de orquestrador podem chamar outras funções de orquestrador. Por exemplo, pode construir uma orquestração maior a partir de uma biblioteca de funções orquestradoras menores. Ou pode executar vários casos de uma função orquestradora em paralelo.

Uma função orquestradora pode chamar outra função orquestradora utilizando os `CallSubOrchestratorAsync` `CallSubOrchestratorWithRetryAsync` métodos ou métodos em .NET, ou os `callSubOrchestrator` métodos ou `callSubOrchestratorWithRetry` métodos em JavaScript. O artigo [de tratamento de erros & Compensation](durable-functions-error-handling.md#automatic-retry-on-failure) tem mais informações sobre a repetição automática.

As funções de sub-orquestrador comportam-se tal como as funções de atividade do ponto de vista do chamador. Podem devolver um valor, lançar uma exceção, e podem ser aguardados pela função de orquestrador-mãe. 

> [!NOTE]
> As sub-orquestrações são atualmente suportadas em .NET e JavaScript.

## <a name="example"></a>Exemplo

O exemplo a seguir ilustra um cenário IoT ("Internet das Coisas") onde há vários dispositivos que precisam de ser a provisionados. A seguinte função representa o fluxo de trabalho de provisionamento que precisa de ser executado para cada dispositivo:

# <a name="c"></a>[C#](#tab/csharp)

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

Esta função orquestradora pode ser usada como-é para o provisionamento de dispositivos one-off ou pode fazer parte de uma orquestração maior. Neste último caso, a função de orquestrador-mãe pode agendar instâncias de `DeviceProvisioningOrchestration` utilização da `CallSubOrchestratorAsync` API (.NET) ou `callSubOrchestrator` (JavaScript).

Aqui está um exemplo que mostra como executar várias funções orquestradoras em paralelo.

# <a name="c"></a>[C#](#tab/csharp)

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
> Os exemplos C# anteriores são para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

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
> As sub-orquestrações devem ser definidas na mesma aplicação de função que a orquestração dos pais. Se precisar de ligar e aguardar orquestrações noutra aplicação de funções, considere usar o suporte incorporado para ASP HTTP e o padrão de consumidor de sondagens HTTP 202. Para mais informações, consulte o tópico [HTTP Features.](durable-functions-http-features.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como definir um estado de orquestração personalizado](durable-functions-custom-orchestration-status.md)
