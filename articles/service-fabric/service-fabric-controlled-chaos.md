---
title: Induza caos em clusters do Service Fabric | Documentos da Microsoft
description: Utilizar inserção de falhas e APIs de serviço de análise de Cluster para gerir o caos no cluster.
services: service-fabric
documentationcenter: .net
author: motanv
manager: anmola
editor: motanv
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/05/2018
ms.author: motanv
ms.openlocfilehash: 7a22b17d45218c2f78220f980605b3c211495606
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543727"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Induza Chaos controlado em clusters do Service Fabric
Sistemas distribuídos em grande escala, como infraestruturas de cloud são inerentemente pouco fiáveis. O Azure Service Fabric permite aos desenvolvedores escrever serviços distribuídos confiáveis por cima de uma infraestrutura confiável. Para escrever robustos serviços distribuídos por cima de uma infraestrutura confiável, os desenvolvedores precisam de ser capaz de testar a estabilidade dos serviços, enquanto a infraestrutura subjacente de não-confiável está a passar por transições de estado complicado devido a falhas.

O [Injeção de falha e o serviço de análise de Cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-testability-overview) (também conhecido como o serviço de análise de falhas) fornece aos desenvolvedores a capacidade de induza falhas para testar os seus serviços. Estes visados simulated falhas, como [reiniciar uma partição](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps), pode ajudar a testar as transições de estado mais comuns. No entanto falhas simuladas direcionadas estão atreladas definição e, portanto, podem perder bugs que mostram se apenas de seqüência de difícil prever, longa e complicada de transições de estado. Para um teste povoação não, pode usar o caos.

Chaos simula periódicas e intercaladas falhas (amigável e inesperadas) em todo o cluster através de longos períodos de tempo. Uma falha de anulações normal consiste num conjunto de chamadas de API de recursos de infraestrutura do serviço, por exemplo, o reinício de falhas de réplica é uma falha de anulações normal porque se trata de um fechamento seguido por um aberto numa réplica. Remover a réplica, mova a réplica primária e réplica secundária de mudança são as outras falhas anulações normais exercidas pelo caos. Falhas inesperadas são saídas de processos, como reiniciar o nó e reinicie o pacote do código. 

Assim que tiver configurado o Chaos com a taxa de e o tipo de falhas, pode começar a Chaos através de c#, o Powershell ou REST API para começar a gerar falhas no cluster e nos seus serviços. Pode configurar Chaos para ser executado por um período de tempo especificado (por exemplo, para uma hora), após o qual paradas Chaos automaticamente ou pode chamar a API de StopChaos (c#, o Powershell ou REST) para impedi-lo em qualquer altura.

> [!NOTE]
> Em sua forma atual, o Chaos induces falhas apenas seguras, que indica que na ausência de falhas externas a perda de quórum, ou perda de dados nunca ocorre de forma.
>

Enquanto Chaos está em execução, ele produz diferentes eventos que capturar o estado da execução no momento. Por exemplo, um ExecutingFaultsEvent contém todas as falhas Chaos decidiu para executar na iteração. Um ValidationFailedEvent contém os detalhes de uma falha de validação (problemas de estado de funcionamento ou estabilidade) que foram encontrados durante a validação do cluster. É possível invocar a API de GetChaosReport (c#, o Powershell ou REST) para obter o relatório de execuções de caos. Esses eventos obterem que permanecem numa [dicionário fiável](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections), que tem uma política de truncamento definida pelas duas configurações: **MaxStoredChaosEventCount** (valor predefinido é 25000) e **StoredActionCleanupIntervalInSeconds** (valor predefinido é de 3600). Cada *StoredActionCleanupIntervalInSeconds* verificações de Chaos e tudo, mas o mais recente *MaxStoredChaosEventCount* eventos, são removidos do dicionário fiável.

## <a name="faults-induced-in-chaos"></a>Falhas induzidas no Chaos
Chaos gera falhas em todo o cluster do Service Fabric e compacta falhas que são vistas em meses ou anos em algumas horas. A combinação de falhas intercaladas com a taxa de falhas elevada encontra casos específicos que caso contrário, poderão ser perdidos. Este exercício de Chaos leva a uma melhoria significativa na qualidade de código do serviço.

Chaos induces falhas nas seguintes categorias:

* Reiniciar um nó
* Reiniciar um pacote de código implementado
* Remover uma réplica
* Reiniciar uma réplica
* Mover uma réplica primária (configurável)
* Mover uma réplica secundária (configurável)

Chaos é executado em várias iterações. Cada iteração é composta por falhas e a validação de cluster para o período de tempo especificado. Pode configurar o tempo gasto para o cluster estabilizar e para a validação com êxito. Se não for encontrada uma falha na validação de cluster, o Chaos gera e persistir um ValidationFailedEvent com o carimbo de hora UTC e os detalhes da falha. Por exemplo, considere uma instância de Chaos que está configurada para execução durante uma hora, com um máximo de três falhas em simultâneo. Chaos induces três falhas e, em seguida, valida o estado de funcionamento do cluster. Ele itera através de anterior passa passo até que seja explicitamente parado por meio da API de StopChaosAsync ou uma hora. Se o cluster se torna mau estado de funcionamento em qualquer iteração (ou seja, ele não estabilizar ou não-se tornar íntegro dentro o MaxClusterStabilizationTimeout passado), Chaos gera um ValidationFailedEvent. Este evento indica que algo está errado e poderá ter de investigação adicional.

Para obter as falhas induzidos caos, pode utilizar a API de GetChaosReport (powershell, c# ou REST). A API obtém o segmento seguinte do relatório Chaos com base no token de continuação passado ou o passado-intervalo de tempo. Pode especificar o ContinuationToken para obter o segmento seguinte do relatório Chaos ou pode especificar o intervalo de tempo por meio de StartTimeUtc e EndTimeUtc, mas não é possível especificar o ContinuationToken e o intervalo de tempo na mesma chamada. Quando há mais de 100 eventos de caos, é devolvido o relatório de Chaos em segmentos em que um segmento contém mais do que 100 eventos de caos.

## <a name="important-configuration-options"></a>Opções de configuração importantes
* **TimeToRun**: Tempo total de execuções de Chaos antes que ele é concluído com êxito. Pode parar Chaos antes que tenha executado durante o período de TimeToRun por meio da API de StopChaos.

* **MaxClusterStabilizationTimeout**: A quantidade máxima de tempo de espera para o cluster para se tornar íntegros antes de produzir um ValidationFailedEvent. Este espera é reduzir a carga no cluster enquanto ele está a recuperar. As verificações realizadas são:
  * Se o estado de funcionamento do cluster está OK
  * Se o estado de funcionamento do serviço é OK
  * Se o tamanho do conjunto de réplicas de destino é obtida para a partição de serviço
  * Que existem sem réplicas InBuild
* **MaxConcurrentFaults**: O número máximo de falhas em simultâneo que são induzidas em cada iteração. É o Chaos mais agressiva, o número mais elevado e as ativações pós-falha e as combinações de transição de estado que o cluster passa por também são mais complexas. 

> [!NOTE]
> Seja como for um valor como elevado *MaxConcurrentFaults* tem Chaos garante - na ausência de falhas externas - não existe perda de quórum ou perda de dados.
>

* **EnableMoveReplicaFaults**: Ativa ou desativa as falhas que provocam as réplicas primárias ou secundárias mover. Estas falhas estão ativadas por predefinição.
* **WaitTimeBetweenIterations**: A quantidade de tempo de espera entre as iterações. Ou seja, a quantidade de tempo Chaos irá colocar em pausa após ter executado uma rodada de falhas e de ter concluído a validação correspondente do Estado de funcionamento do cluster. Quanto maior for o valor, menor é a taxa de injeção de média de falhas.
* **WaitTimeBetweenFaults**: A quantidade de tempo de espera entre duas falhas consecutivas numa única iteração. Maior o valor, do menor a simultaneidade de (ou a sobreposição entre) de falhas.
* **ClusterHealthPolicy**: Política de estado de funcionamento do cluster é utilizada para validar o estado de funcionamento do cluster entre as iterações de caos. Se o estado de funcionamento do cluster está em erro ou se ocorrer uma exceção inesperada durante a execução de falhas, o Chaos aguardará durante 30 minutos antes do Estado de funcionamento-verificação seguinte - para fornecer o cluster com algum tempo a recuperate.
* **Contexto**: Uma coleção de (cadeia, cadeia de caracteres) escreva os pares chave-valor. O mapa pode ser utilizado para registar informações sobre a execução de caos. Não pode haver mais de 100 esses pares e cada cadeia de caracteres (chave ou valor) pode ter um máximo de 4095 carateres de comprimento. Este mapa é definido pelo starter caótico executar armazenar opcionalmente o contexto sobre a execução específica.
* **ChaosTargetFilter**: Este filtro pode ser utilizado para falhas de Chaos de destino apenas a determinados tipos de nó ou apenas a determinados instâncias da aplicação. Se ChaosTargetFilter não for utilizado, de Chaos falhas de todas as entidades de cluster. Se for utilizado ChaosTargetFilter, o Chaos falhas apenas as entidades que satisfazem a especificação de ChaosTargetFilter. NodeTypeInclusionList e ApplicationInclusionList permitem apenas a semântica de União. Em outras palavras, não é possível especificar uma interseção dos NodeTypeInclusionList e ApplicationInclusionList. Por exemplo, não é possível especificar "falhas desta aplicação apenas quando se encontra com esse tipo de nó." Depois de uma entidade está incluída no NodeTypeInclusionList ou ApplicationInclusionList, essa entidade não é possível excluir ChaosTargetFilter. Mesmo se applicationX não aparece na ApplicationInclusionList, em alguns iteração Chaos applicationX pode ser com falhas porque acaba sendo num nó de nodeTypeY que está incluído no NodeTypeInclusionList. Se NodeTypeInclusionList e ApplicationInclusionList são nulo ou está vazio, será gerada uma ArgumentException.
    * **NodeTypeInclusionList**: Uma lista de tipos de nós para incluir em falhas de caos. Todos os tipos de falhas (reiniciar o nó, reinicie o codepackage, remover a réplica, reiniciar a réplica, mover primário e mover secundário) estão ativadas para os nós de um desses tipos de nó. Se um nodetype (Digamos NodeTypeX) não for apresentada a NodeTypeInclusionList, em seguida, falhas de nível de nó (como NodeRestart) nunca serão ativadas para os nós de NodeTypeX, mas as falhas de pacote e de réplica de código ainda podem ser ativadas para NodeTypeX se uma aplicação no ApplicationInclusionList acontece residam num nó de NodeTypeX. No máximo nomes de tipo de nó 100 podem ser incluídos nesta lista, para aumentar este número, uma atualização de configuração é necessária para a configuração de MaxNumberOfNodeTypesInChaosTargetFilter.
    * **ApplicationInclusionList**: Uma lista de URIs de aplicação para incluir em falhas de caos. Todas as réplicas que pertencem aos serviços desses aplicativos são propensos a falhas de réplica (réplica de reinício, remover réplica, movimentação primário e secundário de movimentação) ao caos. Chaos pode reiniciar um pacote de código apenas se o pacote do código aloja réplicas desses aplicativos apenas. Se um aplicativo não aparecer nesta lista, ele pode ainda ser com falhas em alguns iteração Chaos se o aplicativo acaba num nó de um tipo de nó que está incluído no NodeTypeInclusionList. No entanto se applicationX está associada ao nodeTypeY por meio de restrições de posicionamento e applicationX está ausente da ApplicationInclusionList e nodeTypeY está ausente da NodeTypeInclusionList, em seguida, applicationX será nunca possível com falhas. No máximo 1000 nomes de aplicativo podem ser incluídos nesta lista, para aumentar este número, uma atualização de configuração é necessária para a configuração de MaxNumberOfApplicationsInChaosTargetFilter.

## <a name="how-to-run-chaos"></a>Como executar caos

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
