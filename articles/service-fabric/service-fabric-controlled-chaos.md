---
title: Induzir o caos em clusters de tecido de serviço
description: Utilização de APIs do Serviço de Injeção de Falhas e De AdS do Cluster para gerir o caos no cluster.
author: motanv
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: motanv
ms.openlocfilehash: 37b451abd0a519dff17aba9b2d6c42b4762f30cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75463164"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Induzir o caos controlado em clusters de tecido de serviço
Sistemas distribuídos em larga escala, como infraestruturas em nuvem, não são inerentemente fiáveis. O Azure Service Fabric permite que os desenvolvedores escrevam serviços distribuídos fiáveis em cima de uma infraestrutura pouco fiável. Para escrever serviços distribuídos robustos em cima de uma infraestrutura pouco fiável, os desenvolvedores precisam de ser capazes de testar a estabilidade dos seus serviços, enquanto a infraestrutura subjacente não confiável está a passar por transições estatais complicadas devido a falhas.

O Serviço de [Injeção de Falhas e Análise](https://docs.microsoft.com/azure/service-fabric/service-fabric-testability-overview) de Cluster (também conhecido como Serviço de Análise de Falhas) dá aos programadores a capacidade de induzir falhas para testar os seus serviços. Estas falhas simuladas direcionadas, como [reiniciar uma partição,](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps)podem ajudar a exercer as transições de estado mais comuns. No entanto, as falhas simuladas direcionadas são tendenciosas por definição e, portanto, podem perder bugs que aparecem apenas em sequência difícil de prever, longa e complicada de transições estatais. Para um teste imparcial, podes usar o Caos.

O caos simula falhas periódicas e intercaladas (graciosas e não graciosas) em todo o cluster durante longos períodos de tempo. Uma falha graciosa consiste num conjunto de chamadas API de tecido de serviço, por exemplo, reiniciar a falha da réplica é uma falha graciosa porque esta é uma falha próxima seguida de uma abertura numa réplica. Remova a réplica, mova a réplica primária e mova a réplica secundária são as outras falhas graciosas exercidas pelo Caos. Falhas desgraciosas são saídas de processo, como reiniciar o nó e reiniciar o pacote de código. 

Depois de configurar o Caos com a taxa e o tipo de falhas, pode iniciar o Caos através de C#, Powershell ou REST API para começar a gerar falhas no cluster e nos seus serviços. Pode configurar o Caos para funcionar durante um período de tempo especificado (por exemplo, durante uma hora), após o qual o Caos para automaticamente, ou pode ligar para stopChaos API (C#, Powershell ou REST) para o parar a qualquer momento.

> [!NOTE]
> Na sua forma atual, o Caos induz apenas falhas seguras, o que implica que na ausência de falhas externas uma perda de quórum, ou perda de dados nunca ocorre.
>

Enquanto o Caos está a decorrer, produz diferentes eventos que captam o estado da corrida neste momento. Por exemplo, um ExecutingFaultsEvent contém todas as falhas que o Caos decidiu executar nessa iteração. Um ValidaçãoFailEdEvent contém os detalhes de uma falha de validação (problemas de saúde ou estabilidade) que foi encontrado durante a validação do cluster. Pode invocar o GetChaosReport API (C#, Powershell ou REST) para obter o relatório do Caos. Estes eventos são persistidos num [dicionário fiável](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections), que tem uma política de truncação ditada por duas configurações: **MaxStoredChaosEventCount** (valor predefinido é 25000) e **StoredActionCleanupIntervalInSeconds** (valor predefinido é 3600). Todos os controlos do caos *StoredActionCleanupIntervalInSeconds* e todos menos os mais recentes eventos *MaxStoredChaosEventCount,* são expurgados do dicionário fiável.

## <a name="faults-induced-in-chaos"></a>Falhas induzidas no Caos
O caos gera falhas em todo o cluster do Tecido de Serviço e comprime falhas que são vistas em meses ou anos em poucas horas. A combinação de falhas intercaladas com a elevada taxa de avaria encontra casos de canto que podem ser perdidos de outra forma. Este exercício do Caos leva a uma melhoria significativa da qualidade de código do serviço.

O caos induz falhas das seguintes categorias:

* Reinicie um nó
* Reiniciar um pacote de código implantado
* Remover uma réplica
* Reiniciar uma réplica
* Mova uma réplica primária (configurável)
* Mova uma réplica secundária (configurável)

O caos corre em múltiplas iterações. Cada iteração consiste em falhas e validação do cluster durante o período especificado. Pode configurar o tempo gasto para que o cluster estabilize e que a validação tenha sucesso. Se for encontrada uma falha na validação do cluster, o Caos gera e persiste um ValidaçãoFailedEvent com a marca utc e os detalhes da falha. Por exemplo, considere um caso de Caos que deverá funcionar durante uma hora com um máximo de três falhas simultâneas. O caos induz três falhas e, em seguida, valida a saúde do cluster. Iterates através do passo anterior até que seja explicitamente parado através da API StopChaosAsync ou passes de uma hora. Se o cluster se tornar insalubre em qualquer iteração (isto é, não estabiliza ou não se torna saudável dentro do MaxClusterStabilizationTimeout), o Caos gera um ValidaçãoFailedEvent. Este evento indica que algo correu mal e pode precisar de mais investigação.

Para obter quais falhas induzidas pelo Caos, pode utilizar a API GetChaosReport (powershell, C#, ou REST). A API obtém o próximo segmento do relatório Caos com base no token de continuação passado ou no intervalo de tempo passado. Pode especificar o ContinuationToken para obter o próximo segmento do relatório Caos ou pode especificar o intervalo de tempo através do StartTimeUtc e EndTimeUtc, mas não pode especificar tanto o ContinuationToken como o intervalo de tempo na mesma chamada. Quando há mais de 100 eventos caos, o relatório Caos é devolvido em segmentos onde um segmento não contém mais de 100 eventos caos.

## <a name="important-configuration-options"></a>Opções de configuração importantes
* **TimeToRun**: O tempo total que o Caos corre antes de terminar com sucesso. Pode parar o Caos antes de correr durante o período TimeToRun através da API StopChaos.

* **MaxClusterStabilizationTimeout**: O máximo de tempo para esperar que o cluster fique saudável antes de produzir um ValidaçãoFailedEvent. Esta espera é para reduzir a carga no cluster enquanto está a recuperar. Os controlos efetuados são:
  * Se a saúde do cluster estiver bem
  * Se a saúde do serviço estiver bem
  * Se o tamanho do conjunto de réplica-alvo for alcançado para a partição de serviço
  * Que não existem réplicas inBuild
* **MaxConcurrentFaults**: O número máximo de falhas simultâneas que são induzidas em cada iteração. Quanto maior for o número, mais agressivo é o caos e as falhas e as combinações de transição do Estado por que o cluster passa também são mais complexas. 

> [!NOTE]
> Independentemente do valor elevado que a *MaxConcurrentFaults* tem, o Chaos garante - na ausência de falhas externas - que não há perda de quórum ou perda de dados.
>

* **ActivarAsReplicaDes**: Ativa ou desativa as falhas que fazem com que as réplicas primárias ou secundárias se movam. Estas falhas são ativadas por defeito.
* **WaitTimeBetweenItas**: A quantidade de tempo para esperar entre iterações. Ou seja, o tempo que o Caos vai parar depois de ter executado uma ronda de falhas e ter terminado a correspondente validação da saúde do cluster. Quanto maior for o valor, menor é a taxa média de injeção de falhas.
* **WaitTimeBetweenFaults**: A quantidade de tempo para esperar entre duas falhas consecutivas numa única iteração. Quanto maior for o valor, menor é a conmoeda de (ou a sobreposição entre) falhas.
* **ClusterHealthPolicy**: A política de saúde do cluster é usada para validar a saúde do cluster entre iterações do Caos. Se a saúde do cluster estiver errada ou se uma exceção inesperada ocorrer durante a execução de falhas, o Caos aguardará 30 minutos antes do próximo exame de saúde - para fornecer ao cluster algum tempo para recuperar.
* **Contexto**: Uma coleção de pares de valor-chave (corda, corda) tipo. O mapa pode ser usado para gravar informações sobre a corrida do Caos. Não pode haver mais de 100 pares deste tipo e cada corda (chave ou valor) pode ter no máximo 4095 caracteres de comprimento. Este mapa é definido pelo início da corrida do Caos para armazenar opcionalmente o contexto sobre a execução específica.
* **ChaosTargetFilter**: Este filtro pode ser utilizado para direcionar falhas do Caos apenas a certos tipos de nó ou apenas a determinadas instâncias de aplicação. Se o ChaosTargetFilter não for utilizado, o Caos falha todas as entidades do cluster. Se o ChaosTargetFilter for utilizado, o Caos falha apenas as entidades que cumprem a especificação ChaosTargetFilter. NodeTypeInclusionList e ApplicationInclusionList permitem apenas a semântica sindical. Por outras palavras, não é possível especificar uma intersecção entre nodeTypeInclusionList e ApplicationInclusionList. Por exemplo, não é possível especificar "falha nesta aplicação apenas quando está nesse tipo de nó". Uma vez que uma entidade é incluída no NodeTypeInclusionList ou na ApplicationInclusionList, essa entidade não pode ser excluída usando o ChaosTargetFilter. Mesmo que o applicationX não apareça na ApplicationInclusionList, em algumas aplicações de iteração caosX pode ser defeituoso porque acontece que está num nó de nóTypeY que está incluído no NodeTypeInclusionList. Se tanto o NodeTypeInclusionList como o ApplicationInclusionList estiverem nulos ou vazios, é lançada uma Exceção de Argumento.
    * **NodeTypeInclusionList**: Uma lista de tipos de nó a incluir nas falhas do Caos. Todos os tipos de falhas (reiniciar o nó, reiniciar o código, remover a réplica, reiniciar a réplica, mover-se primário e mover-se secundário) estão ativados para os nós destes tipos de nós. Se um nótipo (digamos NodeTypeX) não aparecer na Lista de Inclusão no Nó, então as falhas de nível do nó (como nodeRestart) nunca serão ativadas para os nós do NodeTypeX, mas as falhas de código e réplica ainda podem ser ativadas para o NodeTypeX se uma aplicação na Lista de Aplicações InclusionList residir num nó de NodeTypeX. No máximo, 100 nomes de tipo de nó podem ser incluídos nesta lista, para aumentar este número, é necessária uma atualização de config para a configuração MaxNumberOfNodeTypesInChaosTargetFilter.
    * **ApplicationInclusionList**: Uma lista de URIs de aplicação a incluir em falhas do Caos. Todas as réplicas pertencentes aos serviços destas aplicações são passíveis de réplica de falhas (réplica de reinício, remoção de réplicas, movimento primário e movimento secundário) pelo Caos. O caos só pode reiniciar um pacote de código se o pacote de código supor apenas réplicas destas aplicações. Se uma aplicação não aparecer nesta lista, ainda pode ser defeituosa em alguma iteração caos se a aplicação acabar num nó de um nó que está incluído no NodeTypeInclusionList. No entanto, se o applicationX estiver ligado ao nodeTypeY através de restrições de colocação e aplicaçõesX está ausente da ApplicationInclusionList e nodeTypeY está ausente do NodeTypeInclusionList, então a aplicaçãoX nunca será defeituosa. No máximo, 1000 nomes de aplicações podem ser incluídos nesta lista, para aumentar este número, é necessária uma atualização de config para a configuração MaxNumberOfApplicationsInChaosTargetFilter.

## <a name="how-to-run-chaos"></a>Como executar o Caos

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
