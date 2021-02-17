---
title: Crie o caos e os testes de failover para a Azure Service Fabric
description: Utilizando os cenários de teste de caos do Tecido de Serviço e de teste de failover para induzir falhas e verificar a fiabilidade dos seus serviços.
author: georgewallace
ms.topic: conceptual
ms.date: 10/1/2019
ms.author: gwallace
ms.custom: devx-track-csharp
ms.openlocfilehash: 78e613e9d86c44ad7daf8778b35e6af49abb7d38
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546368"
---
# <a name="testability-scenarios"></a>Cenários de testabilidade
Grandes sistemas distribuídos como infraestruturas em nuvem são inerentemente pouco fiáveis. A Azure Service Fabric dá aos desenvolvedores a capacidade de escrever serviços para funcionar em cima de infraestruturas não confiáveis. Para poderem escrever serviços de alta qualidade, os desenvolvedores precisam de ser capazes de induzir tais infraestruturas pouco fiáveis para testar a estabilidade dos seus serviços.

O Serviço de Análise de Falhas dá aos desenvolvedores a capacidade de induzir ações de falha para testar serviços na presença de falhas. No entanto, falhas simuladas direcionadas só o levarão até agora. Para levar o teste mais longe, pode utilizar os cenários de teste no Tecido de Serviço: um teste de caos e um teste de falha. Estes cenários simulam falhas intercaladas contínuas, graciosas e ingraciais, ao longo do cluster durante longos períodos de tempo. Uma vez que um teste é configurado com a taxa e o tipo de falhas, pode ser iniciado através de APIs C# ou PowerShell, para gerar falhas no cluster e no seu serviço.

> [!WARNING]
> O ChaosTestScenario está a ser substituído por um caos mais resistente e baseado em serviços. Consulte o novo artigo [Controlled Chaos](service-fabric-controlled-chaos.md) para mais detalhes.
> 
> 

## <a name="chaos-test"></a>Teste do caos
O cenário de caos gera falhas em todo o cluster de Tecido de Serviço. O cenário comprime falhas geralmente vistas em meses ou anos a algumas horas. A combinação de falhas intercaladas com a alta taxa de avaria encontra casos de canto que de outra forma são perdidos. Isto leva a uma melhoria significativa na qualidade do código do serviço.

### <a name="faults-simulated-in-the-chaos-test"></a>Falhas simuladas no teste do caos
* Reiniciar um nó
* Reiniciar um pacote de código implantado
* Remover uma réplica
* Reiniciar uma réplica
* Mover uma réplica primária (opcional)
* Mover uma réplica secundária (opcional)

O teste de caos executa várias iterações de falhas e validações de clusters durante o período de tempo especificado. O tempo gasto para o cluster estabilizar e para validação ter sucesso também é configurável. O cenário falha quando se atinge uma única falha na validação do cluster.

Por exemplo, considere um teste definido para funcionar durante uma hora com um máximo de três falhas simultâneas. O teste irá induzir três falhas e, em seguida, validar a saúde do cluster. O teste irá iterar através do passo anterior até que o cluster fique insalubre ou uma hora passe. Se o cluster não for saudável em qualquer iteração, ou seja, não estabilizar dentro de um tempo configurado, o teste falhará com uma exceção. Esta exceção indica que algo correu mal e precisa de uma investigação mais aprofundada.

Na sua forma atual, o motor de geração de falhas no teste do caos induz apenas falhas seguras. Isto significa que, na ausência de falhas externas, nunca ocorrerá um quórum ou perda de dados.

### <a name="important-configuration-options"></a>Opções de configuração importantes
* **TimeToRun**: Tempo total que o teste irá ser executado antes de terminar com sucesso. O teste pode terminar mais cedo em vez de uma falha de validação.
* **MaxClusterStabilizationTimeout**: Tempo máximo para esperar que o cluster fique saudável antes de falhar no teste. As verificações realizadas são se a saúde do cluster está OK, a saúde do serviço está OK, o tamanho definido da réplica alvo é alcançado para a partição de serviço, e não existem réplicas inBuild.
* **MaxConcurrentFaults**: Número máximo de falhas simultâneas induzidas em cada iteração. Quanto maior for o número, mais agressivo será o teste, resultando assim em falhas mais complexas e combinações de transição. O teste garante que, na ausência de falhas externas, não haverá um quórum ou perda de dados, independentemente da altura desta configuração.
* **ActivarMoveReplicaFaults**: Ativa ou desativa as falhas que estão a causar o movimento das réplicas primárias ou secundárias. Estas falhas são desativadas por defeito.
* **WaitTimeBetweenIterations**: Quantidade de tempo para esperar entre iterações, isto é, após uma ronda de falhas e validação correspondente.

### <a name="how-to-run-the-chaos-test"></a>Como fazer o teste do caos
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

O módulo Powershell de Tecido de Serviço inclui duas formas de iniciar um cenário de caos. `Invoke-ServiceFabricChaosTestScenario` é baseado no cliente, e se a máquina cliente estiver fechada a meio do teste, não serão introduzidas mais falhas. Em alternativa, existe um conjunto de comandos destinados a manter o teste em funcionamento em caso de paragem da máquina. `Start-ServiceFabricChaos` utiliza um serviço de sistema imponente e fiável chamado FaultAnalysisService, garantindo que as falhas permanecerão introduzidas até que o TimeToRun esteja em cima. `Stop-ServiceFabricChaos` pode ser usado para parar manualmente o cenário, e `Get-ServiceFabricChaosReport` obterá um relatório. Para obter mais informações consulte a [referência Azure Service Fabric Powershell](/powershell/module/servicefabric/) e [induzir o caos controlado nos clusters de Tecidos de Serviço](service-fabric-controlled-chaos.md).

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
O cenário de teste de failover é uma versão do cenário de teste do caos que visa uma partição de serviço específica. Testa o efeito da falha numa partição de serviço específica, deixando os outros serviços inalterados. Uma vez configurado com as informações de partição do alvo e outros parâmetros, funciona como uma ferramenta do lado do cliente que utiliza APIs C# ou PowerShell para gerar falhas para uma partição de serviço. O cenário imita através de uma sequência de falhas simuladas e validação de serviço enquanto a sua lógica de negócio corre ao lado para fornecer uma carga de trabalho. Uma falha na validação do serviço indica um problema que precisa de uma investigação mais aprofundada.

### <a name="faults-simulated-in-the-failover-test"></a>Falhas simuladas no teste de failover
* Reinicie um pacote de código implantado onde a partição é hospedada
* Remover uma réplica primária/secundária ou instância apátrida
* Reiniciar uma réplica secundária primária (se um serviço persistido)
* Mover uma réplica primária
* Mover uma réplica secundária
* Reiniciar a partição

O teste de failover induz uma falha escolhida e, em seguida, executa a validação no serviço para garantir a sua estabilidade. O teste de failover induz apenas uma falha de cada vez, em oposição a possíveis falhas múltiplas no teste de caos. Se a divisória de serviço não estabilizar dentro do intervalo de tempo configurado após cada avaria, o teste falha. O teste só induz falhas seguras. Isto significa que, na ausência de falhas externas, não ocorrerá um quórum ou perda de dados.

### <a name="important-configuration-options"></a>Opções de configuração importantes
* **PartitionSelector**: Seletor que especifica a partição que precisa de ser direcionada.
* **TimeToRun**: Tempo total que o teste irá ser executado antes de terminar.
* **MaxServiceStabilizationTimeout**: Tempo máximo para esperar que o cluster fique saudável antes de falhar no teste. As verificações realizadas são se a saúde do serviço é OK, o tamanho do conjunto de réplicas alvo é alcançado para todas as divisórias, e não existem réplicas inBuild.
* **WaitTimeBetweenFaults**: Tempo de espera entre cada falha e ciclo de validação.

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
