---
title: Crie testes de caos e de failover para o Azure Service Fabric
description: Usando os cenários de teste de Service Fabric e teste de caos para induzir falhas e verificar a confiabilidade de seus serviços.
author: motanv
ms.topic: conceptual
ms.date: 10/1/2019
ms.author: motanv
ms.openlocfilehash: 206b02024ad052a12e87cfdf1773815027e8aec4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465544"
---
# <a name="testability-scenarios"></a>Cenários de possibilidade de teste
Sistemas distribuídos grandes, como infraestruturas de nuvem, são inerentemente não confiáveis. O Azure Service Fabric oferece aos desenvolvedores a capacidade de escrever serviços para serem executados sobre infraestruturas não confiáveis. Para escrever serviços de alta qualidade, os desenvolvedores precisam ser capazes de induzir essa infraestrutura não confiável para testar a estabilidade de seus serviços.

O serviço de análise de falha fornece aos desenvolvedores a capacidade de induzir ações de falha para testar serviços na presença de falhas. No entanto, as falhas simuladas direcionadas o levarão até agora. Para fazer o teste mais detalhadamente, você pode usar os cenários de teste no Service Fabric: um teste de caos e um teste de failover. Esses cenários simulam falhas intercaladas contínuas, tanto normais quanto inconvenientes, em todo o cluster por longos períodos de tempo. Quando um teste é configurado com a taxa e o tipo de falhas, ele pode ser iniciado por meio C# de APIs ou do PowerShell, para gerar falhas no cluster e no serviço.

> [!WARNING]
> O ChaosTestScenario está sendo substituído por um caos mais resiliente baseado em serviços. Confira o artigo novo [caos controlado](service-fabric-controlled-chaos.md) para obter mais detalhes.
> 
> 

## <a name="chaos-test"></a>Teste de caos
O cenário de caos gera falhas em todo o cluster de Service Fabric. O cenário compacta as falhas geralmente vistas em meses ou anos a algumas horas. A combinação de falhas intercaladas com a alta taxa de falhas localiza casos de canto que, de outra forma, são perdidos. Isso leva a uma melhoria significativa na qualidade do código do serviço.

### <a name="faults-simulated-in-the-chaos-test"></a>Falhas simuladas no teste de caos
* Reiniciar um nó
* Reiniciar um pacote de códigos implantado
* Remover uma réplica
* Reiniciar uma réplica
* Mover uma réplica primária (opcional)
* Mover uma réplica secundária (opcional)

O teste de caos executa várias iterações de falhas e validações de cluster para o período de tempo especificado. O tempo gasto para o cluster se estabilizar e para que a validação tenha sucesso também é configurável. O cenário falha quando você clica em uma única falha na validação do cluster.

Por exemplo, considere um conjunto de teste para executar por uma hora com um máximo de três falhas simultâneas. O teste induzirá três falhas e validará a integridade do cluster. O teste irá iterar na etapa anterior até que o cluster se torne não íntegro ou uma hora passe. Se o cluster se tornar não íntegro em qualquer iteração, ou seja, não se estabilizar dentro de um tempo configurado, o teste falhará com uma exceção. Essa exceção indica que algo deu errado e precisa de mais investigação.

Em sua forma atual, o mecanismo de geração de falha no teste de caos induzi apenas a falhas seguras. Isso significa que, na ausência de falhas externas, um quorum ou perda de dados nunca ocorrerá.

### <a name="important-configuration-options"></a>Opções de configuração importantes
* **TimeToRun**: tempo total que o teste será executado antes de concluir com êxito. O teste pode ser concluído anteriormente no lugar de uma falha de validação.
* **MaxClusterStabilizationTimeout**: a quantidade máxima de tempo de espera para o cluster se tornar íntegro antes de falhar no teste. As verificações executadas são se a integridade do cluster está OK, se a integridade do serviço está OK, se o tamanho do conjunto de réplicas de destino é obtido para a partição de serviço e se não existem réplicas de incompilação.
* **MaxConcurrentFaults**: número máximo de falhas simultâneas induzidas em cada iteração. Quanto maior o número, mais agressivo o teste, resultando em failovers e combinações de transição mais complexos. O teste garante que, na ausência de falhas externas, não haverá quorum ou perda de dados, independentemente de quão alta essa configuração é.
* **EnableMoveReplicaFaults**: habilita ou desabilita as falhas que estão causando a movimentação das réplicas primárias ou secundárias. Essas falhas estão desabilitadas por padrão.
* **WaitTimeBetweenIterations**: a quantidade de tempo de espera entre as iterações, ou seja, após uma rodada de falhas e a validação correspondente.

### <a name="how-to-run-the-chaos-test"></a>Como executar o teste de caos
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

O módulo Service Fabric PowerShell inclui duas maneiras de iniciar um cenário de caos. `Invoke-ServiceFabricChaosTestScenario` é baseado no cliente e, se o computador cliente for desligado no meio do teste, nenhuma outra falha será introduzida. Como alternativa, há um conjunto de comandos destinadas a manter o teste em execução no caso de um desligamento da máquina. o `Start-ServiceFabricChaos` usa um serviço de sistema confiável e com estado chamado FaultAnalysisService, garantindo que as falhas permanecerão introduzidas até que o TimeToRun esteja ativo. `Stop-ServiceFabricChaos` pode ser usado para interromper manualmente o cenário e `Get-ServiceFabricChaosReport` obterá um relatório. Para obter mais informações, consulte a [referência do Azure Service Fabric PowerShell](https://docs.microsoft.com/powershell/module/servicefabric/?view=azureservicefabricps) e o [caos controlado pelo induzir em clusters Service Fabric](service-fabric-controlled-chaos.md).

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## <a name="failover-test"></a>Teste de failover
O cenário de teste de failover é uma versão do cenário de teste de caos que tem como alvo uma partição de serviço específica. Ele testa o efeito de failover em uma partição de serviço específica, deixando os outros serviços não afetados. Uma vez configurado com as informações da partição de destino e outros parâmetros, ele é executado como uma ferramenta do lado do cliente C# que usa APIs ou o PowerShell para gerar falhas para uma partição de serviço. O cenário itera por meio de uma sequência de falhas simuladas e validação de serviço enquanto sua lógica de negócios é executada no lado para fornecer uma carga de trabalho. Uma falha na validação do serviço indica um problema que precisa de mais investigação.

### <a name="faults-simulated-in-the-failover-test"></a>Falhas simuladas no teste de failover
* Reiniciar um pacote de códigos implantado onde a partição está hospedada
* Remover uma réplica primária/secundária ou uma instância sem estado
* Reiniciar uma réplica secundária primária (se houver um serviço persistente)
* Mover uma réplica primária
* Mover uma réplica secundária
* Reiniciar a partição

O teste de failover induzi uma falha escolhida e, em seguida, executa a validação no serviço para garantir sua estabilidade. O teste de failover induzi apenas uma falha por vez, em oposição às possíveis várias falhas no teste de caos. Se a partição de serviço não se estabilizar dentro do tempo limite configurado após cada falha, o teste falhará. O teste induzi somente a falhas seguras. Isso significa que, na ausência de falhas externas, uma perda de quórum ou de dados não ocorrerá.

### <a name="important-configuration-options"></a>Opções de configuração importantes
* **PartitionSelector**: o objeto selector que especifica a partição que precisa ser direcionada.
* **TimeToRun**: tempo total que o teste será executado antes de concluir.
* **MaxServiceStabilizationTimeout**: a quantidade máxima de tempo de espera para o cluster se tornar íntegro antes de falhar no teste. As verificações realizadas são se a integridade do serviço está OK, se o tamanho do conjunto de réplicas de destino é obtido para todas as partições e se não existem réplicas de incompilação.
* **WaitTimeBetweenFaults**: a quantidade de tempo de espera entre cada ciclo de falha e validação.

### <a name="how-to-run-the-failover-test"></a>Como executar o teste de failover
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
