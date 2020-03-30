---
title: Criar o caos e os testes de failover para o Tecido de Serviço Azure
description: Utilizando o teste de caos do Tecido de Serviço e cenários de teste de failover para induzir falhas e verificar a fiabilidade dos seus serviços.
author: motanv
ms.topic: conceptual
ms.date: 10/1/2019
ms.author: motanv
ms.openlocfilehash: 206b02024ad052a12e87cfdf1773815027e8aec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465544"
---
# <a name="testability-scenarios"></a>Cenários de testabilidade
Grandes sistemas distribuídos como infraestruturas em nuvem não são inerentemente fiáveis. A Azure Service Fabric dá aos desenvolvedores a capacidade de escrever serviços para funcionar em cima de infraestruturas pouco fiáveis. Para escrever serviços de alta qualidade, os desenvolvedores precisam de ser capazes de induzir tais infraestruturas pouco fiáveis para testar a estabilidade dos seus serviços.

O Serviço de Análise de Falhas dá aos desenvolvedores a capacidade de induzir ações de falha para testar serviços na presença de falhas. No entanto, falhas simuladas direcionadas só o levarão até agora. Para levar mais longe os testes, pode utilizar os cenários de teste em Tecido de Serviço: um teste de caos e um teste de failover. Estes cenários simulam falhas intercaladas contínuas, graciosas e ingraciosas, ao longo de todo o cluster ao longo de longos períodos de tempo. Uma vez configurado um teste com a taxa e o tipo de falhas, pode ser iniciado através de APIs C# ou PowerShell, para gerar falhas no cluster e no seu serviço.

> [!WARNING]
> O ChaosTestScenario está a ser substituído por um caos mais resistente e baseado no serviço. Consulte o novo artigo [Controlled Chaos](service-fabric-controlled-chaos.md) para mais detalhes.
> 
> 

## <a name="chaos-test"></a>Teste do caos
O cenário de caos gera falhas em todo o cluster de Tecido de Serviço. O cenário comprime falhas geralmente vistas em meses ou anos a algumas horas. A combinação de falhas intercaladas com a elevada taxa de avaria encontra casos de canto que de outra forma são perdidos. Isto leva a uma melhoria significativa na qualidade do código do serviço.

### <a name="faults-simulated-in-the-chaos-test"></a>Falhas simuladas no teste do caos
* Reinicie um nó
* Reiniciar um pacote de código implantado
* Remover uma réplica
* Reiniciar uma réplica
* Mover uma réplica primária (opcional)
* Mover uma réplica secundária (opcional)

O teste do caos executa múltiplas iterações de falhas e validações de cluster sacar durante o período de tempo especificado. O tempo gasto para que o cluster estabilize e validação tenha sucesso também é configurável. O cenário falha quando se atinge uma única falha na validação do cluster.

Por exemplo, considere um teste definido para funcionar durante uma hora com um máximo de três falhas simultâneas. O teste induzirá três falhas e, em seguida, validará a saúde do cluster. O teste irá iterar através do passo anterior até que o cluster se torne insalubre ou passe de uma hora. Se o cluster se tornar insalubre em qualquer iteração, ou seja, não estabilizar dentro de um tempo configurado, o teste falhará com uma exceção. Esta exceção indica que algo correu mal e precisa de uma investigação mais aprofundada.

Na sua forma atual, o motor de produção de avarias no teste do caos induz apenas falhas seguras. Isto significa que, na ausência de falhas externas, nunca ocorrerá uma perda de quórum ou de dados.

### <a name="important-configuration-options"></a>Opções de configuração importantes
* **TimeToRun**: O tempo total que o teste irá decorrer antes de terminar com sucesso. O teste pode terminar mais cedo em vez de uma falha de validação.
* **MaxClusterEstabilizaçãoTimeout**: O máximo de tempo para esperar que o cluster fique saudável antes de falhar o teste. Os controlos realizados são se a saúde do cluster está ok, a saúde do serviço está OK, o tamanho definido de réplica alvo é alcançado para a partição de serviço, e não existem réplicas InBuild.
* **MaxConcurrentFaults**: Número máximo de falhas simultâneas induzidas em cada iteração. Quanto maior for o número, mais agressivo o teste, resultando em falhas mais complexas e combinações de transição. O teste garante que, na ausência de falhas externas, não haverá uma perda de quórum ou de dados, independentemente da altura desta configuração.
* **EnableMoveReplicaFaults**: Ativa ou desativa as falhas que estão a causar o movimento das réplicas primárias ou secundárias. Estas falhas são desativadas por defeito.
* **WaitTimeBetweenItas**: Tempo para esperar entre iterações, ou seja, após uma ronda de falhas e validação correspondente.

### <a name="how-to-run-the-chaos-test"></a>Como executar o teste do caos
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

O módulo Powershell de Tecido de Serviço inclui duas formas de iniciar um cenário de caos. `Invoke-ServiceFabricChaosTestScenario`é baseado no cliente, e se a máquina cliente for encerrada a meio do teste, não serão introduzidas mais falhas. Em alternativa, existe um conjunto de comandos destinados a manter o teste em funcionamento em caso de paragem da máquina. `Start-ServiceFabricChaos`utiliza um serviço de sistema imponente e fiável chamado FaultAnalysisService, garantindo que as falhas permanecerão introduzidas até que o TimeToRun esteja ligado. `Stop-ServiceFabricChaos`pode ser usado para parar manualmente o cenário, e `Get-ServiceFabricChaosReport` obter um relatório. Para mais informações consulte a [referência azure service Fabric Powershell](https://docs.microsoft.com/powershell/module/servicefabric/?view=azureservicefabricps) e [induzindo](service-fabric-controlled-chaos.md)o caos controlado nos clusters de Tecido de Serviço .

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## <a name="failover-test"></a>Teste failover
O cenário de teste failover é uma versão do cenário de teste do caos que visa uma partição específica do serviço. Testa o efeito da falha numa divisória específica do serviço, deixando os outros serviços inalterados. Uma vez configurado com a informação de partição alvo e outros parâmetros, funciona como uma ferramenta do lado do cliente que utiliza c# APIs ou PowerShell para gerar falhas para uma partição de serviço. O cenário iterates através de uma sequência de falhas simuladas e validação de serviçoenquanto a sua lógica de negócio corre de lado para fornecer uma carga de trabalho. Uma falha na validação do serviço indica um problema que precisa de mais investigação.

### <a name="faults-simulated-in-the-failover-test"></a>Falhas simuladas no teste de failover
* Reiniciar um pacote de código implantado onde a partição está hospedada
* Remover uma réplica primária/secundária ou instância apátrida
* Reiniciar uma réplica secundária primária (se um serviço persistente)
* Mover uma réplica primária
* Mova uma réplica secundária
* Reiniciar a partição

O teste failover induz uma falha escolhida e, em seguida, executa validação no serviço para garantir a sua estabilidade. O teste failover induz apenas uma falha de cada vez, em oposição a possíveis falhas múltiplas no teste do caos. Se a partição de serviço não estabilizar dentro do tempo configurado após cada falha, o teste falha. O teste induz apenas falhas seguras. Isto significa que, na ausência de falhas externas, não ocorrerá uma perda de quórum ou de dados.

### <a name="important-configuration-options"></a>Opções de configuração importantes
* **PartitionSelector**: Selecionador objeto que especifica a divisória que precisa de ser direcionada.
* **TimeToRun**: O tempo total de execução do teste será executado antes de terminar.
* **MaxServiceEstabilizaçãoTimeout**: O máximo de tempo para esperar que o cluster fique saudável antes de falhar o teste. Os controlos realizados são se a saúde do serviço está ok, o tamanho definido da réplica alvo é alcançado para todas as divisórias, e não existem réplicas InBuild.
* **WaitTimeBetweenFaults**: Tempo para esperar entre cada falha e ciclo de validação.

### <a name="how-to-run-the-failover-test"></a>Como executar o teste de failover
**C #**

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
