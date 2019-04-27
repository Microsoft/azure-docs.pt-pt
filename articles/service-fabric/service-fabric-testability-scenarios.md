---
title: Criar testes de ativação pós-falha e caos para o Azure Service Fabric | Documentos da Microsoft
description: Utilizar o Service Fabric e ativação pós-falha de teste de chaos testar cenários para induza falhas e certifique-se a confiabilidade de seus serviços.
services: service-fabric
documentationcenter: .net
author: motanv
manager: rsinha
editor: toddabel
ms.assetid: 8eee7e89-404a-4605-8f00-7e4d4fb17553
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: d12c5097d4ba5e0ccfe0e2b2cbc8ccd758c32d98
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60865028"
---
# <a name="testability-scenarios"></a>Cenários do Testability
Sistemas distribuídos grandes, como infraestruturas de cloud são inerentemente pouco fiáveis. O Azure Service Fabric fornece aos desenvolvedores a capacidade de escrever serviços para serem executados por cima de infraestruturas de não-confiáveis. Para escrever os serviços de alta qualidade, os desenvolvedores precisam ser capazes de induza essa infraestrutura pouco fiável para testar a estabilidade dos serviços.

O serviço de análise de falhas fornece aos desenvolvedores a capacidade de induza ações de falha para testar os serviços na presença de falhas. No entanto, destinadas falhas simuladas fará com que apenas até agora. Para tirar o teste ainda mais, pode usar os cenários de teste no Service Fabric: um teste de chaos e um teste de ativação pós-falha. Estes cenários simular falhas intercaladas contínuas, amigável e inesperadas, em todo o cluster através de longos períodos de tempo. Depois de um teste está configurado com a taxa de e o tipo de falhas, que pode ser iniciado através de APIs c# ou do PowerShell, para gerar falhas no cluster e o seu serviço.

> [!WARNING]
> ChaosTestScenario está a ser substituído por um caos mais resiliente, com base no serviço. Veja o artigo de novos [Chaos controlado](service-fabric-controlled-chaos.md) para obter mais detalhes.
> 
> 

## <a name="chaos-test"></a>Teste de Chaos
O cenário de chaos gera falhas em todo o cluster do Service Fabric. O cenário compacta falhas costuma ser vistas em meses ou anos para algumas horas. A combinação de falhas intercaladas com a taxa de falhas elevada encontra casos específicos que caso contrário, seja perdidos. Isso nos leva a uma melhoria significativa na qualidade de código do serviço.

### <a name="faults-simulated-in-the-chaos-test"></a>Falhas simuladas no teste caos
* Reiniciar um nó
* Reiniciar um pacote de código implementado
* Remover uma réplica
* Reiniciar uma réplica
* Mover uma réplica primária (opcional)
* Mover uma réplica secundária (opcional)

O teste de chaos executa várias iterações de falhas e validações de cluster para o período de tempo especificado. O tempo gasto para o cluster estabilizar e para a validação com êxito também é configurável. O cenário de falha quando pressionar uma única falha na validação do cluster.

Por exemplo, considere um teste definido para ser executado durante uma hora, com um máximo de três falhas em simultâneo. O teste será induza três falhas e, em seguida, validar o estado de funcionamento do cluster. O teste irá iterar por meio do passo anterior, até que o cluster fica danificado ou passa de uma hora. Se o cluster ficar danificado em qualquer iteração, ou seja, não estabilizar num período de tempo configurado, o teste falhará com uma exceção. Esta exceção indica que algo está errado e tem de investigação adicional.

Em sua forma atual, o mecanismo de geração de falha no teste chaos induces apenas falhas de segurança. Isso significa que na ausência de falhas externas, uma quórum ou perda de dados nunca ocorrerá.

### <a name="important-configuration-options"></a>Opções de configuração importantes
* **TimeToRun**: Tempo total que o teste será executado antes de concluir com êxito. O teste pode concluir anteriormente em lugar de uma falha de validação.
* **MaxClusterStabilizationTimeout**: Quantidade máxima de tempo de espera para o cluster para se tornar íntegros antes da falha de teste. As verificações realizadas são se o estado de funcionamento do cluster está OK, o estado de funcionamento do serviço está OK, o tamanho de conjunto de réplicas de destino é obtido para a partição de serviço e não réplicas InBuild existem.
* **MaxConcurrentFaults**: Induzidas pelo número máximo de falhas em simultâneo em cada iteração. Quanto maior for o número, quanto mais agressivo o teste, que, por conseguinte, resultando em ativações pós-falha mais complexas e combinações de transição. O teste garante que na ausência de falhas externas não haverá uma quórum ou perda de dados, independentemente de qual esta configuração é.
* **EnableMoveReplicaFaults**: Ativa ou desativa as falhas que estão a causar a movimentação das réplicas primárias ou secundárias. Estas falhas estão desativadas por predefinição.
* **WaitTimeBetweenIterations**: Quantidade de tempo de espera entre as iterações, ou seja, após uma rodada de falhas e de validação correspondente.

### <a name="how-to-run-the-chaos-test"></a>Como executar o teste de chaos
Exemplo C#

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunChaosTestScenarioAsync(clusterConnection).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunChaosTestScenarioAsync(string clusterConnection)
    {
        TimeSpan maxClusterStabilizationTimeout = TimeSpan.FromSeconds(180);
        uint maxConcurrentFaults = 3;
        bool enableMoveReplicaFaults = true;

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        ChaosTestScenarioParameters scenarioParameters = new ChaosTestScenarioParameters(
          maxClusterStabilizationTimeout,
          maxConcurrentFaults,
          enableMoveReplicaFaults,
          timeToRun);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        ChaosTestScenario chaosScenario = new ChaosTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```

PowerShell

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## <a name="failover-test"></a>Teste de ativação pós-falha
O cenário de teste de ativação pós-falha é uma versão do cenário de teste chaos que tenha como destino uma partição de serviço específico. Ele testa o efeito de ativação pós-falha numa partição de serviço específico, deixando os outros serviços afetados. Assim que este é configurado com as informações de partição de destino e outros parâmetros, que é executado como uma ferramenta de lado do cliente que utiliza APIs do c# ou o PowerShell para gerar falhas para uma partição de serviço. O cenário itera através de uma seqüência de falhas simuladas e validação do serviço enquanto a lógica de negócios é executada no lado para fornecer uma carga de trabalho. Uma falha na validação de serviço indica um problema que necessite de investigação.

### <a name="faults-simulated-in-the-failover-test"></a>Falhas simuladas no teste de ativação pós-falha
* Reiniciar um pacote de código implementado em que a partição está alojada
* Remover uma réplica primária/secundária ou instância sem monitoração de estado
* Reiniciar uma réplica secundária primária (se um serviço persistente)
* Mover uma réplica primária
* Mover uma réplica secundária
* Reinicie a partição

O teste de ativação pós-falha induces uma falha escolhida e, em seguida, executa a validação no serviço para garantir a estabilidade. O teste de ativação pós-falha induces apenas uma falha num momento, em vez de possível várias falhas no teste caos. Se a partição de serviço não estabilizar dentro do tempo limite configurado após cada falha, o teste é reprovado. O teste induces apenas falhas de segurança. Isso significa que na ausência de falhas externas, uma quórum ou perda de dados não ocorrerá.

### <a name="important-configuration-options"></a>Opções de configuração importantes
* **PartitionSelector**: Objeto de Seletor que especifica a partição que tem de ser visada.
* **TimeToRun**: Tempo total que o teste será executado antes de terminar.
* **MaxServiceStabilizationTimeout**: Quantidade máxima de tempo de espera para o cluster para se tornar íntegros antes da falha de teste. As verificações realizadas são se o estado de funcionamento do serviço está OK, o tamanho de conjunto de réplicas de destino é obtido para todas as partições e não réplicas InBuild existem.
* **WaitTimeBetweenFaults**: Quantidade de tempo de espera entre cada ciclo de validação e de falha.

### <a name="how-to-run-the-failover-test"></a>Como executar o teste de ativação pós-falha
**C#**

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunFailoverTestScenarioAsync(clusterConnection, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunFailoverTestScenarioAsync(string clusterConnection, Uri serviceName)
    {
        TimeSpan maxServiceStabilizationTimeout = TimeSpan.FromSeconds(180);
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        FailoverTestScenarioParameters scenarioParameters = new FailoverTestScenarioParameters(
          randomPartitionSelector,
          timeToRun,
          maxServiceStabilizationTimeout);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        FailoverTestScenario failoverScenario = new FailoverTestScenario(fabricClient, scenarioParameters);

        try
        {
            await failoverScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```


**PowerShell**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```
