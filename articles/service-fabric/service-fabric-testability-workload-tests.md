---
title: Simular falhas em aplicações de tecido de serviço Azure
description: Saiba como endurecer os seus serviços de Tecido de Serviço Azure contra falhas graciosas e vergonhosas.
author: anmolah
ms.topic: conceptual
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: d3d9f6478336c59adb875bf21438d5ffa457b1d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75645995"
---
# <a name="simulate-failures-during-service-workloads"></a>Simular falhas durante as cargas de trabalho do serviço
Os cenários de testabilidade no Tecido de Serviço Azure permitem aos desenvolvedores não se preocuparem em lidar com falhas individuais. Existem, no entanto, cenários em que pode ser necessária uma interdição explícita da carga de trabalho dos clientes e falhas. A interparada da carga de trabalho do cliente e das falhas garante que o serviço está realmente a realizar alguma ação quando o falha acontece. Dado o nível de controlo que a testabilidade proporciona, estes podem estar em pontos precisos da execução da carga de trabalho. Esta indução de falhas em diferentes estados da aplicação pode encontrar bugs e melhorar a qualidade.

## <a name="sample-custom-scenario"></a>Cenário personalizado da amostra
Este teste mostra um cenário que interliga a carga de trabalho do negócio com [falhas graciosas e vergonhosas.](service-fabric-testability-actions.md#graceful-vs-ungraceful-fault-actions) As falhas devem ser induzidas no meio de operações de serviço ou calcular os melhores resultados.

Passemos por um exemplo de um serviço que expõe quatro cargas de trabalho: A, B, C e D. Cada um corresponde a um conjunto de fluxos de trabalho e pode ser computação, armazenamento ou uma mistura. Por uma questão de simplicidade, vamos abstrair as cargas de trabalho no nosso exemplo. As diferentes falhas executadas neste exemplo são:

* Reiniciar O nó: Falha desgraciosa para simular o reinício de uma máquina.
* ReiniciarCódigoEmbalado: Falha desgraciosa para simular falhas no processo do hospedeiro de serviço.
* Remover Replicação: Falha graciosa para simular a remoção da réplica.
* MovePrimary: Falha graciosa para simular movimentos de réplica desencadeados pelo equilíbrio de carga de tecido de serviço.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        // Replace these strings with the actual version for your cluster and application.
        string clusterConnection = "localhost:19000";
        Uri applicationName = new Uri("fabric:/samples/PersistentToDoListApp");
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Workload Test...");
        try
        {
            RunTestAsync(clusterConnection, applicationName, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Workload Test failed: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Workload Test completed successfully.");
        return 0;
    }

    public enum ServiceWorkloads
    {
        A,
        B,
        C,
        D
    }

    public enum ServiceFabricFaults
    {
        RestartNode,
        RestartCodePackage,
        RemoveReplica,
        MovePrimary,
    }

    public static async Task RunTestAsync(string clusterConnection, Uri applicationName, Uri serviceName)
    {
        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);
        // Maximum time to wait for a service to stabilize.
        TimeSpan maxServiceStabilizationTime = TimeSpan.FromSeconds(120);

        // How many loops of faults you want to execute.
        uint testLoopCount = 20;
        Random random = new Random();

        for (var i = 0; i < testLoopCount; ++i)
        {
            var workload = SelectRandomValue<ServiceWorkloads>(random);
            // Start the workload.
            var workloadTask = RunWorkloadAsync(workload);

            // While the task is running, induce faults into the service. They can be ungraceful faults like
            // RestartNode and RestartDeployedCodePackage or graceful faults like RemoveReplica or MovePrimary.
            var fault = SelectRandomValue<ServiceFabricFaults>(random);

            // Create a replica selector, which will select a primary replica from the given service to test.
            var replicaSelector = ReplicaSelector.PrimaryOf(PartitionSelector.RandomOf(serviceName));
            // Run the selected random fault.
            await RunFaultAsync(applicationName, fault, replicaSelector, fabricClient);
            // Validate the health and stability of the service.
            await fabricClient.TestManager.ValidateServiceAsync(serviceName, maxServiceStabilizationTime);

            // Wait for the workload to finish successfully.
            await workloadTask;
        }
    }

    private static async Task RunFaultAsync(Uri applicationName, ServiceFabricFaults fault, ReplicaSelector selector, FabricClient client)
    {
        switch (fault)
        {
            case ServiceFabricFaults.RestartNode:
                await client.FaultManager.RestartNodeAsync(selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RestartCodePackage:
                await client.FaultManager.RestartDeployedCodePackageAsync(applicationName, selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RemoveReplica:
                await client.FaultManager.RemoveReplicaAsync(selector, CompletionMode.Verify, false);
                break;
            case ServiceFabricFaults.MovePrimary:
                await client.FaultManager.MovePrimaryAsync(selector.PartitionSelector);
                break;
        }
    }

    private static Task RunWorkloadAsync(ServiceWorkloads workload)
    {
        throw new NotImplementedException();
        // This is where you trigger and complete your service workload.
        // Note that the faults induced while your service workload is running will
        // fault the primary service. Hence, you will need to reconnect to complete or check
        // the status of the workload.
    }

    private static T SelectRandomValue<T>(Random random)
    {
        Array values = Enum.GetValues(typeof(T));
        T workload = (T)values.GetValue(random.Next(values.Length));
        return workload;
    }
}
```
