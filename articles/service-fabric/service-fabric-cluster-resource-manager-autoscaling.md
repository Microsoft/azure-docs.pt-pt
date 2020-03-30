---
title: Serviço azure serviços de escala automática de tecido e recipientes
description: O Azure Service Fabric permite-lhe definir políticas de escala automática para serviços e contentores.
author: radicmilos
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: miradic
ms.openlocfilehash: 3660ece7add8f279292340aae9ab445b682fe045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452089"
---
# <a name="introduction-to-auto-scaling"></a>Introdução ao Auto Scaling
A escala automática é uma capacidade adicional do Service Fabric para escalar dinamicamente os seus serviços com base na carga que os serviços estão a reportar, ou com base no seu uso de recursos. A escala automática proporciona uma grande elasticidade e permite o fornecimento de instâncias adicionais ou divisórias do seu serviço a pedido. Todo o processo de escala automática é automatizado e transparente, e uma vez configurado as suas políticas num serviço, não há necessidade de operações de escala manual ao nível do serviço. A escala automática pode ser ligada quer no momento da criação do serviço, quer a qualquer momento, atualizando o serviço.

Um cenário comum em que a escala automática é útil é quando a carga num determinado serviço varia ao longo do tempo. Por exemplo, um serviço como um gateway pode escalar com base na quantidade de recursos necessários para lidar com os pedidos de entrada. Vamos dar uma olhada num exemplo de como essas regras de escala podem parecer:
* Se todos os casos do meu portal estiverem a usar mais de dois núcleos em média, então esforce o serviço de gateway adicionando mais um caso. Faça isto a cada hora, mas nunca tenha mais de sete casos no total.
* Se todos os casos do meu portal estiverem a usar menos de 0,5 núcleos em média, então escala o serviço removendo uma instância. Faça isto a cada hora, mas nunca tenha menos de três casos no total.

A escala automática é suportada tanto para contentores como para serviços regulares de fabricação de serviço. Para utilizar a escala automática, tem de estar a funcionar na versão 6.2 ou superior ao tempo de funcionamento do Tecido de Serviço. 

O resto deste artigo descreve as políticas de escala, formas de ativar ou desativar a escala automática, e dá exemplos sobre como usar esta funcionalidade.

## <a name="describing-auto-scaling"></a>Descrevendo a escala automática
As políticas de escala automática podem ser definidas para cada serviço num cluster de Tecido de Serviço. Cada política de escala é constituída por duas partes:
* **O gatilho** de escala descreve quando a escala ção do serviço será executada. As condições definidas no gatilho são verificadas periodicamente para determinar se um serviço deve ser escalado ou não.

* **O mecanismo** de escala descreve como a escala será realizada quando é desencadeada. O mecanismo só é aplicado quando as condições do gatilho forem satisfeitas.

Todos os gatilhos que são atualmente suportados funcionam com [métricas de carga lógica,](service-fabric-cluster-resource-manager-metrics.md)ou com métricas físicas como CPU ou uso de memória. De qualquer forma, o Tecido de Serviço monitorizará a carga reportada para a métrica, e avaliará o gatilho periodicamente para determinar se é necessário dimensionamento.

Existem dois mecanismos que são atualmente suportados para a escala automática. O primeiro destina-se a serviços apátridas ou a contentores em que a escala automática é efetuada adicionando ou removendo [instâncias](service-fabric-concepts-replica-lifecycle.md). Tanto para serviços apátridas como apátridas, a escalação automática também pode ser realizada adicionando ou removendo [divisórias nomeadas](service-fabric-concepts-partitioning.md) do serviço.

> [!NOTE]
> Atualmente, existe apoio para apenas uma política de escala por serviço, e apenas um gatilho de escala por política de escala.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Gatilho médio de carga de partição com escala baseada em instância
O primeiro tipo de gatilho baseia-se na carga de instâncias numa partição de serviço apátrida. As cargas métricas são primeiramente suavizadas para obter a carga para cada instância de uma partição, e então estes valores são mediados em todos os casos da partição. Existem três fatores que determinam quando o serviço será dimensionado:

* _O limiar de carga mais baixo_ é um valor que determina quando o serviço será **escalado**. Se a carga média de todas as instâncias das divisórias for inferior a este valor, então o serviço será escalado.
* _O limiar de carga superior_ é um valor que determina quando o serviço será **dimensionado**. Se a carga média de todos os casos da partição for superior a este valor, então o serviço será dimensionado.
* _O intervalo_ de escala determina a frequência com que o gatilho será verificado. Uma vez verificado o gatilho, se for necessário escalonamento, o mecanismo será aplicado. Se não for necessário escalar, então não serão tomadas medidas. Em ambos os casos, o gatilho não será verificado novamente antes de expirar novamente o intervalo de escala.

Este gatilho só pode ser utilizado com serviços apátridas (recipientes apátridas ou serviços de fabricação de serviço). No caso de um serviço ter múltiplas divisórias, o gatilho é avaliado para cada partição separadamente, e cada partição terá o mecanismo especificado aplicado independentemente. Assim, neste caso, é possível que algumas das divisórias do serviço sejam dimensionadas, algumas serão dimensionadas, e algumas não serão dimensionadas ao mesmo tempo, com base na sua carga.

O único mecanismo que pode ser usado com este gatilho é partitionCountScaleMechanism. Existem três fatores que determinam como este mecanismo é aplicado:
* _O incremento_ de escala determina quantas instâncias serão adicionadas ou removidas quando o mecanismo for acionado.
* _Contagem de instâncias máximas_ define o limite superior para a escala. Se o número de instâncias da partição atingir este limite, então o serviço não será dimensionado, independentemente da carga. É possível omitir este limite especificando o valor de -1, e nesse caso o serviço será escalado o máximo possível (o limite é o número de nós disponíveis no cluster).
* _Contagem de instâncias mínimas_ define o limite inferior para a escala. Se o número de instâncias da partição atingir este limite, então o serviço não será escalado independentemente da carga.

## <a name="setting-auto-scaling-policy"></a>Definição da política de escala automática

### <a name="using-application-manifest"></a>Usando o manifesto de aplicação
``` xml
<LoadMetrics>
<LoadMetric Name="MetricB" Weight="High"/>
</LoadMetrics>
<ServiceScalingPolicies>
<ScalingPolicy>
    <AveragePartitionLoadScalingTrigger MetricName="MetricB" LowerLoadThreshold="1" UpperLoadThreshold="2" ScaleIntervalInSeconds="100"/>
    <InstanceCountScalingMechanism MinInstanceCount="3" MaxInstanceCount="4" ScaleIncrement="1"/>
</ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Usando C# APIs
```csharp
FabricClient fabricClient = new FabricClient();
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//set up the rest of the ServiceDescription
AveragePartitionLoadScalingTrigger trigger = new AveragePartitionLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new PartitionInstanceCountScaleMechanism();
mechanism.MaxInstanceCount = 3;
mechanism.MinInstanceCount = 1;
mechanism.ScaleIncrement = 1;
trigger.MetricName = "servicefabric:/_CpuCores";
trigger.ScaleInterval = TimeSpan.FromMinutes(20);
trigger.LowerLoadThreshold = 1.0;
trigger.UpperLoadThreshold = 2.0;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceDescription.ScalingPolicies.Add(policy);
//as we are using scaling on a resource this must be exclusive service
//also resource monitor service needs to be enabled
serviceDescription.ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```
### <a name="using-powershell"></a>Usando powershell
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.PartitionInstanceCountScaleMechanism
$mechanism.MinInstanceCount = 1
$mechanism.MaxInstanceCount = 6
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AveragePartitionLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_CpuCores"
$trigger.LowerLoadThreshold = 0.3
$trigger.UpperLoadThreshold = 0.8
$trigger.ScaleInterval = New-TimeSpan -Minutes 10
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
Update-ServiceFabricService -Stateless -ServiceName "fabric:/AppName/ServiceName" -ScalingPolicies $scalingpolicies
```

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Gatilho médio de carga de serviço com escala à base de divisória
O segundo gatilho baseia-se na carga de todas as divisórias de um serviço. As cargas métricas são primeiramente suavizadas para obter a carga para cada réplica ou instância de uma partição. Para serviços audais, a carga da divisória é considerada a carga da réplica primária, enquanto para os serviços apátridas a carga da divisória é a carga média de todos os casos da partição. Estes valores são mediados em todas as divisórias do serviço, e este valor é usado para desencadear a escala automática. O mesmo que no mecanismo anterior, existem três fatores que determinam quando o serviço será dimensionado:

* _O limiar de carga mais baixo_ é um valor que determina quando o serviço será **escalado**. Se a carga média de todas as divisórias do serviço for inferior a este valor, então o serviço será escalado.
* _O limiar de carga superior_ é um valor que determina quando o serviço será **dimensionado**. Se a carga média de todas as divisórias do serviço for superior a este valor, então o serviço será dimensionado.
* _O intervalo_ de escala determina a frequência com que o gatilho será verificado. Uma vez verificado o gatilho, se for necessário escalonamento, o mecanismo será aplicado. Se não for necessário escalar, então não serão tomadas medidas. Em ambos os casos, o gatilho não será verificado novamente antes de expirar novamente o intervalo de escala.

Este gatilho pode ser usado tanto com serviços apátridas como apátridas. O único mecanismo que pode ser usado com este gatilho é addRemoveIncrementalNamedPartitionScalingMechanism. Quando o serviço é escalado, uma nova divisória é adicionada, e quando o serviço é escalado em uma das divisórias existentes é removido. Existem restrições que serão verificadas quando o serviço for criado ou atualizado e a criação/atualização do serviço falhará se estas condições não forem satisfeitas:
* O regime de partição denominado deve ser utilizado para o serviço.
* Os nomes da partição devem ser números inteiros consecutivos, como "0", "1", ...
* O primeiro nome da partição deve ser "0".

Por exemplo, se um serviço for inicialmente criado com três divisórias, a única possibilidade válida para os nomes de partição é "0", "1" e "2".

A operação real de escala automática realizada respeitará também este esquema de nomeação:
* Se as partições atuais do serviço forem denominadas "0", "1" e "2", então a partição que será adicionada para a escala ção será denominada "3".
* Se as partições atuais do serviço forem denominadas "0", "1" e "2", então a partição que será removida para escala ré partição com o nome "2".

Tal como com o mecanismo que utiliza a escala, adicionando ou removendo instâncias, existem três parâmetros que determinam como este mecanismo é aplicado:
* _O incremento_ de escala determina quantas divisórias serão adicionadas ou removidas quando o mecanismo for acionado.
* _Contagem máxima de partição_ define o limite superior para a escala. Se o número de divisórias do serviço atingir este limite, então o serviço não será dimensionado, independentemente da carga. É possível omitir este limite especificando o valor de -1, e nesse caso o serviço será escalado o máximo possível (o limite é a capacidade real do cluster).
* _Contagem de instâncias mínimas_ define o limite inferior para a escala. Se o número de divisórias do serviço atingir este limite, então o serviço não será escalado independentemente da carga.

> [!WARNING] 
> Quando o AddRemoveIncrementalNamedPartitionScalingMechanism for utilizado com serviços estatais, o Tecido de Serviço adicionará ou removerá divisórias **sem notificação ou aviso**. A repartição dos dados não será efetuada quando for acionado o mecanismo de escala. Em caso de funcionamento de escala, novas divisórias ficarão vazias e, em caso de funcionamento de escala, a **partição será eliminada juntamente com todos os dados que contém**.

## <a name="setting-auto-scaling-policy"></a>Definição da política de escala automática

### <a name="using-application-manifest"></a>Usando o manifesto de aplicação
``` xml
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedPartitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Usando C# APIs
```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
AveragePartitionLoadScalingTrigger trigger = new AverageServiceLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new AddRemoveIncrementalNamedPartitionScalingMechanism();
mechanism.MaxPartitionCount = 4;
mechanism.MinPartitionCount = 1;
mechanism.ScaleIncrement = 1;
//expecting that the service already has metric NumberOfConnections
trigger.MetricName = "NumberOfConnections";
trigger.ScaleInterval = TimeSpan.FromMinutes(15);
trigger.LowerLoadThreshold = 10000;
trigger.UpperLoadThreshold = 20000;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceUpdate.ScalingPolicies = new List<ScalingPolicyDescription>;
serviceUpdate.ScalingPolicies.Add(policy);
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```
### <a name="using-powershell"></a>Usando powershell
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.AddRemoveIncrementalNamedPartitionScalingMechanism
$mechanism.MinPartitionCount = 1
$mechanism.MaxPartitionCount = 3
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AverageServiceLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_MemoryInMB"
$trigger.LowerLoadThreshold = 5000
$trigger.UpperLoadThreshold = 10000
$trigger.ScaleInterval = New-TimeSpan -Minutes 25
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -TargetReplicaSetSize 3 -MinReplicaSetSize 2 -HasPersistedState true -PartitionNames @("0","1") -ServicePackageActivationMode ExclusiveProcess -ScalingPolicies $scalingpolicies
```

## <a name="auto-scaling-based-on-resources"></a>Escala automática com base em recursos

A fim de permitir que o serviço de monitorização de recursos escalacom base em recursos reais

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Há duas métricas que representam recursos físicos reais. Um deles é o serviço:/_CpuCores que representam a utilização real do CPU (portanto 0,5 representa meio núcleo) e o outro é o serviço tecido:/_MemoryInMB que representa o uso da memória em MBs.
O ResourceMonitorService é responsável pelo rastreio do CPU e pela utilização da memória dos serviços do utilizador. Este serviço aplicará uma média móvel ponderada de forma a ter em conta potenciais picos de curta duração. A monitorização de recursos é suportada tanto para aplicações contentorizadas como não contentorizadas no Windows e para as contentorizadas no Linux. A escala automática de recursos só está ativada para serviços ativados em [modelo de processo exclusivo.](service-fabric-hosting-model.md#exclusive-process-model)

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [escalabilidade da aplicação.](service-fabric-concepts-scalability.md)
