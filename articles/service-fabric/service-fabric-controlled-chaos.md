---
title: Induzir caos em clusters de Service Fabric
description: Usando as APIs de injeção de falha e de serviço de análise de cluster para gerenciar o caos no cluster.
author: motanv
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: motanv
ms.openlocfilehash: 37b451abd0a519dff17aba9b2d6c42b4762f30cd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463164"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Induzir caos controlado em clusters de Service Fabric
Sistemas distribuídos em larga escala, como infraestruturas de nuvem, são inerentemente não confiáveis. O Azure Service Fabric permite que os desenvolvedores gravem serviços distribuídos confiáveis sobre uma infraestrutura não confiável. Para escrever serviços distribuídos robustos sobre uma infraestrutura não confiável, os desenvolvedores precisam ser capazes de testar a estabilidade de seus serviços, enquanto a infraestrutura subjacente não confiável está passando por transições de estado complicadas devido a falhas.

A [injeção de falha e o serviço de análise de cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-testability-overview) (também conhecido como serviço de análise de falha) proporcionam aos desenvolvedores a capacidade de induzir falhas para testar seus serviços. Essas falhas simuladas direcionadas, como [reiniciar uma partição](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps), podem ajudar a exercitar as transições de estado mais comuns. No entanto, as falhas simuladas direcionadas são ajustadas por definição e, portanto, podem perder bugs que aparecem apenas em sequência de transições de estado difícil, de previsão, longa e complicada. Para um teste não polarizado, você pode usar o caos.

O caos simula as falhas intercaladas periódicas (normais e inconvenientes) em todo o cluster por longos períodos de tempo. Uma falha normal consiste em um conjunto de Service Fabric chamadas à API, por exemplo, a falha da réplica de reinicialização é uma falha normal, pois esse é um fechamento seguido por uma aberta em uma réplica. Remover a réplica, mover a réplica primária e mover a réplica secundária são as outras falhas normais exercidas pelo caos. Falhas de não-cortesia são saídas de processo, como reiniciar o nó e reiniciar o pacote de códigos. 

Depois de configurar o caos com a taxa e o tipo de falhas, você pode iniciar o caos por C#meio do, do PowerShell ou da API REST para começar a gerar falhas no cluster e em seus serviços. Você pode configurar o caos para ser executado por um período de tempo especificado (por exemplo, por uma hora), após o qual o caos é interrompido automaticamente ou podeC#chamar a API do StopChaos (, PowerShell ou REST) para interrompê-lo a qualquer momento.

> [!NOTE]
> Em sua forma atual, o caos induzi apenas a falhas seguras, o que implica que, na ausência de falhas externas, uma perda de quorum, ou a perda de dados nunca ocorra.
>

Embora o caos esteja em execução, ele produz eventos diferentes que capturam o estado da execução no momento. Por exemplo, um ExecutingFaultsEvent contém todas as falhas que o caos decidiu executar nessa iteração. Um ValidationFailedEvent contém os detalhes de uma falha de validação (problemas de integridade ou de estabilidade) que foi encontrada durante a validação do cluster. Você pode invocar a API doC#GetChaosReport (, PowerShell ou REST) para obter o relatório de execuções de caos. Esses eventos são persistidos em um [dicionário confiável](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections), que tem uma política de truncamento ditada por duas configurações: **MaxStoredChaosEventCount** (o valor padrão é 25000) e **StoredActionCleanupIntervalInSeconds** (o valor padrão é 3600). Todo *StoredActionCleanupIntervalInSeconds* caos verifica e todos, exceto os eventos de *MaxStoredChaosEventCount* mais recentes, são limpos do dicionário confiável.

## <a name="faults-induced-in-chaos"></a>Falhas induzidas no caos
O caos gera falhas em todo o cluster de Service Fabric e compacta as falhas que são vistas em meses ou anos em algumas horas. A combinação de falhas intercaladas com a alta taxa de falhas localiza os casos de canto que podem ser perdidos. Este exercício de caos leva a uma melhoria significativa na qualidade do código do serviço.

O caos induzi falhas nas seguintes categorias:

* Reiniciar um nó
* Reiniciar um pacote de códigos implantado
* Remover uma réplica
* Reiniciar uma réplica
* Mover uma réplica primária (configurável)
* Mover uma réplica secundária (configurável)

O caos é executado em várias iterações. Cada iteração consiste em falhas e na validação do cluster para o período especificado. Você pode configurar o tempo gasto para o cluster se estabilizar e para que a validação tenha sucesso. Se uma falha for encontrada na validação do cluster, o caos gerará e persiste um ValidationFailedEvent com o carimbo de data/hora UTC e os detalhes da falha. Por exemplo, considere uma instância do caos que está definida para ser executada por uma hora com um máximo de três falhas simultâneas. O caos induzi três falhas e, em seguida, valida a integridade do cluster. Ele itera na etapa anterior até que seja explicitamente interrompido por meio da API StopChaosAsync ou de uma hora. Se o cluster se tornar não íntegro em qualquer iteração (ou seja, não estabilizar ou não se tornar íntegro dentro do MaxClusterStabilizationTimeout passado), o caos gerará um ValidationFailedEvent. Esse evento indica que algo deu errado e pode precisar de mais investigação.

Para obter quais falhas o caos induziu, você pode usar a API do GetChaosReport ( C#PowerShell, ou REST). A API Obtém o próximo segmento do relatório de caos com base no token de continuação passado ou no intervalo de tempo passado. Você pode especificar o ContinuationToken para obter o próximo segmento do relatório de caos ou pode especificar o intervalo de tempo por meio de StartTimeUtc e EndTimeUtc, mas não é possível especificar o ContinuationToken e o intervalo de tempo na mesma chamada. Quando há mais de 100 eventos de caos, o relatório de caos é retornado em segmentos em que um segmento contém, no máximo, 100 eventos de caos.

## <a name="important-configuration-options"></a>Opções de configuração importantes
* **TimeToRun**: tempo total que o caos executa antes de concluir com êxito. Você pode parar o caos antes que ele seja executado pelo período de TimeToRun por meio da API do StopChaos.

* **MaxClusterStabilizationTimeout**: a quantidade máxima de tempo de espera para que o cluster se torne íntegro antes de produzir um ValidationFailedEvent. Essa espera é reduzir a carga no cluster durante a recuperação. As verificações executadas são:
  * Se a integridade do cluster estiver OK
  * Se a integridade do serviço estiver OK
  * Se o tamanho do conjunto de réplicas de destino for obtido para a partição de serviço
  * Se não existe nenhuma réplica de incompilação
* **MaxConcurrentFaults**: o número máximo de falhas simultâneas que são induzidas em cada iteração. Quanto maior o número, mais agressivo é o caos e os failovers e as combinações de transição de estado que o cluster passa também são mais complexas. 

> [!NOTE]
> Independentemente de quão alto um valor *MaxConcurrentFaults* tem, o caos garante-na ausência de falhas externas – não há perda de quórum ou perda de dados.
>

* **EnableMoveReplicaFaults**: habilita ou desabilita as falhas que fazem com que as réplicas primárias ou secundárias sejam movidas. Essas falhas estão habilitadas por padrão.
* **WaitTimeBetweenIterations**: a quantidade de tempo de espera entre as iterações. Ou seja, a quantidade de tempo que o caos pausará depois de ter executado uma rodada de falhas e concluir a validação correspondente da integridade do cluster. Quanto maior o valor, menor é a taxa média de injeção de falhas.
* **WaitTimeBetweenFaults**: a quantidade de tempo de espera entre duas falhas consecutivas em uma única iteração. Quanto maior o valor, menor a simultaneidade (ou a sobreposição entre) das falhas.
* **ClusterHealthPolicy**: a política de integridade do cluster é usada para validar a integridade do cluster entre as iterações de caos. Se a integridade do cluster estiver em erro ou se ocorrer uma exceção inesperada durante a execução da falha, o caos aguardará 30 minutos antes da próxima verificação de integridade para fornecer o cluster com algum tempo para recuperate.
* **Context**: uma coleção de pares chave-valor de tipo (cadeia de caracteres). O mapa pode ser usado para registrar informações sobre a execução do caos. Não pode haver mais de 100 pares de bytes e cada cadeia de caracteres (chave ou valor) pode ter no máximo 4095 caracteres. Esse mapa é definido pelo iniciador da execução de caos para armazenar opcionalmente o contexto sobre a execução específica.
* **ChaosTargetFilter**: esse filtro pode ser usado para direcionar as falhas de caos apenas para determinados tipos de nó ou apenas para determinadas instâncias de aplicativo. Se ChaosTargetFilter não for usado, o caos falhará em todas as entidades de cluster. Se ChaosTargetFilter for usado, o caos falhará apenas nas entidades que atendem à especificação de ChaosTargetFilter. NodeTypeInclusionList e ApplicationInclusionList permitem apenas semântica de União. Em outras palavras, não é possível especificar uma interseção de NodeTypeInclusionList e ApplicationInclusionList. Por exemplo, não é possível especificar "falha neste aplicativo somente quando ele está nesse tipo de nó". Depois que uma entidade é incluída em NodeTypeInclusionList ou ApplicationInclusionList, essa entidade não pode ser excluída usando ChaosTargetFilter. Mesmo que aplicativoX não apareça em ApplicationInclusionList, em alguma iteração de caos, aplicativoX pode ter falhado porque ele está em um nó de nodeType que está incluído no NodeTypeInclusionList. Se NodeTypeInclusionList e ApplicationInclusionList forem nulos ou vazios, um ArgumentException será gerado.
    * **NodeTypeInclusionList**: uma lista de tipos de nós a serem incluídos em falhas de caos. Todos os tipos de falhas (reiniciar o nó, reiniciar CodePackage, remover réplica, reiniciar réplica, mover primário e mover secundário) estão habilitados para os nós desses tipos de nó. Se um NodeType (digamos NodeTypeX) não aparecer no NodeTypeInclusionList, as falhas de nível de nó (como NodeRestart) nunca serão habilitadas para os nós de NodeTypeX, mas as falhas de pacote de código e de réplica ainda poderão ser habilitadas para NodeTypeX se um aplicativo no ApplicationInclusionList acontece em um nó de NodeTypeX. No máximo 100 nomes de tipo de nó podem ser incluídos nessa lista, para aumentar esse número, uma atualização de configuração é necessária para a configuração do MaxNumberOfNodeTypesInChaosTargetFilter.
    * **ApplicationInclusionList**: uma lista de URIs de aplicativo para incluir em falhas de caos. Todas as réplicas que pertencem aos serviços desses aplicativos são receptivos a falhas de réplica (reinicie a réplica, remova a réplica, mova o primário e mova o secundário) por caos. O caos poderá reiniciar um pacote de códigos somente se o pacote de códigos hospedar réplicas somente desses aplicativos. Se um aplicativo não aparecer nessa lista, ainda poderá ter falhado em alguma iteração de caos se o aplicativo terminar em um nó de um tipo de nó incluído no NodeTypeInclusionList. No entanto, se aplicativoX estiver vinculado ao nodeType por meio de restrições de posicionamento e aplicativoX estiver ausente de ApplicationInclusionList e nodeType estiver ausente de NodeTypeInclusionList, aplicativoX nunca terá falhado. No máximo 1000 nomes de aplicativos podem ser incluídos nessa lista, para aumentar esse número, uma atualização de configuração é necessária para a configuração do Maxnumberofnodetypesinchaostargetfilter.

## <a name="how-to-run-chaos"></a>Como executar o caos

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }
        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;

            // The maximum amount of time to wait for all cluster entities to become stable and healthy. 
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster
            // entities. 
            // During validation if a cluster entity is not stable and healthy within
            // MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex
            // series of states to uncover bugs.
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be
            // used to record information about the Chaos run. There cannot be more than 100 such pairs and
            // each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the
            // lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration.
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of
            // state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };

            // All types of faults, restart node, restart code package, restart replica, move primary replica,
            // and move secondary replica will happen for nodes of type 'FrontEndType'
            var nodetypeInclusionList = new List<string> { "FrontEndType"};

            // In addition to the faults included by nodetypeInclusionList,
            // restart code package, restart replica, move primary replica, move secondary replica faults will
            // happen for 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing
            // on a node which is not of type included in nodeypeInclusionList.
            var applicationInclusionList = new List<string> { "fabric:/TestApp2" };

            // List of cluster entities to target for Chaos faults.
            var chaosTargetFilter = new ChaosTargetFilter
            {
                NodeTypeInclusionList = nodetypeInclusionList,
                ApplicationInclusionList = applicationInclusionList
            };

            var parameters = new ChaosParameters(
                maxClusterStabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun,
                startContext,
                waitTimeBetweenIterations,
                waitTimeBetweenFaults,
                clusterHealthPolicy) {ChaosTargetFilter = chaosTargetFilter};

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            string continuationToken = null;

            while (true)
            {
                ChaosReport report;
                try
                {
                    report = string.IsNullOrEmpty(continuationToken)
                        ? client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult()
                        : client.TestManager.GetChaosReportAsync(continuationToken).GetAwaiter().GetResult();
                }
                catch (Exception e)
                {
                    if (e is FabricTransientException)
                    {
                        Console.WriteLine("A transient exception happened: '{0}'", e);
                    }
                    else if(e is TimeoutException)
                    {
                        Console.WriteLine("A timeout exception happened: '{0}'", e);
                    }
                    else
                    {
                        throw;
                    }

                    Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
                    continue;
                }

                continuationToken = report.ContinuationToken;

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.Add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```

```powershell
$clusterConnectionString = "localhost:19000"
$timeToRunMinute = 60

# The maximum amount of time to wait for all cluster entities to become stable and healthy.
# Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities.
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds,
# Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration.
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of
# states to uncover bugs.
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the
# fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration.
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state
# transitions that the cluster goes through.
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record
# information about the Chaos run.
# There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

#List of cluster entities to target for Chaos faults.
$chaosTargetFilter = new-object -TypeName System.Fabric.Chaos.DataStructures.ChaosTargetFilter
$chaosTargetFilter.NodeTypeInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# All types of faults, restart node, restart code package, restart replica, move primary replica, and move
# secondary replica will happen for nodes of type 'FrontEndType'
$chaosTargetFilter.NodeTypeInclusionList.AddRange( [string[]]@("FrontEndType") )
$chaosTargetFilter.ApplicationInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# In addition to the faults included by nodetypeInclusionList, 
# restart code package, restart replica, move primary replica, move secondary replica faults will happen for
# 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is
# not of type included in nodeypeInclusionList.
$chaosTargetFilter.ApplicationInclusionList.Add("fabric:/TestApp2")

Connect-ServiceFabricCluster $clusterConnectionString

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRunMinute -MaxConcurrentFaults $maxConcurrentFaults -MaxClusterStabilizationTimeoutSec $maxClusterStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ClusterHealthPolicy $clusterHealthPolicy -ChaosTargetFilter $chaosTargetFilter

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                Write-Host $e
                # When Chaos stops, a StoppedEvent is created.
                # If a StoppedEvent is found, exit the loop.
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    return
                }
            }
        }
    }

    Start-Sleep -Seconds 1
}
```
