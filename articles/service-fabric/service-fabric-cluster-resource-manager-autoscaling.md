---
title: Serviços e contentores de auto escalagem de tecido de serviço Azure
description: O Azure Service Fabric permite-lhe definir políticas de escala automática para serviços e contentores.
author: radicmilos
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: miradic
ms.custom: devx-track-csharp
ms.openlocfilehash: 060bb9dcdd504846c76ab4c782b2857fdddfa394
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91354807"
---
# <a name="introduction-to-auto-scaling"></a>Introdução ao Auto Scaling
A escala automática é uma capacidade adicional do Service Fabric para escalar dinamicamente os seus serviços com base na carga que os serviços estão a reportar, ou com base no seu uso de recursos. A escala automática proporciona uma grande elasticidade e permite o fornecimento de instâncias ou divisórias adicionais do seu serviço a pedido. Todo o processo de escala automática é automatizado e transparente, e uma vez configurado as suas políticas num serviço não há necessidade de operações de escala manual ao nível do serviço. O dimensionamento automático pode ser ligado na hora da criação do serviço, ou a qualquer momento, atualizando o serviço.

Um cenário comum em que o auto-dimensionamento é útil é quando a carga num determinado serviço varia ao longo do tempo. Por exemplo, um serviço como um gateway pode escalar com base na quantidade de recursos necessários para lidar com pedidos de entrada. Vamos dar uma olhada num exemplo de como essas regras de escala podem ser:
* Se todos os casos do meu portal estiverem a usar mais de dois núcleos em média, então dimensione o serviço de gateway adicionando mais uma instância. Faça isto a cada hora, mas nunca tenha mais de sete instâncias no total.
* Se todos os casos do meu portal estiverem a usar menos de 0,5 núcleos em média, então dimensione o serviço removendo uma instância. Faça isto a cada hora, mas nunca tenha menos de três instâncias no total.

A escala automática é suportada tanto para contentores como para serviços regulares de Tecido de Serviço. Para utilizar o dimensionamento automático, é necessário estar a funcionar na versão 6.2 ou acima do tempo de funcionamento do Tecido de Serviço. 

O resto deste artigo descreve as políticas de escala, formas de permitir ou desativar o dimensionamento automático, e dá exemplos sobre como usar esta funcionalidade.

## <a name="describing-auto-scaling"></a>Descrevendo a escala de automóveis
As políticas de escala automática podem ser definidas para cada serviço num cluster de Tecido de Serviço. Cada política de escala é constituída por duas partes:
* **O gatilho de escala** descreve quando será realizado o escalonamento do serviço. As condições definidas no gatilho são verificadas periodicamente para determinar se um serviço deve ser dimensionado ou não.

* **O mecanismo de escala** descreve como o escalonamento será realizado quando é acionado. O mecanismo só é aplicado quando as condições do gatilho são satisfeitas.

Todos os gatilhos que são atualmente suportados funcionam com [métricas de carga lógicas,](service-fabric-cluster-resource-manager-metrics.md)ou com métricas físicas como CPU ou uso de memória. De qualquer forma, o Service Fabric monitorizará a carga reportada para a métrica, e avaliará o gatilho periodicamente para determinar se a escala é necessária.

Existem atualmente dois mecanismos que são suportados para a escala automática. O primeiro destina-se a serviços apátridas ou a contentores onde a escala automática é efetuada através da adição ou remoção de [instâncias](service-fabric-concepts-replica-lifecycle.md). Tanto para serviços apátridas como apátridas, a escala automática também pode ser realizada adicionando ou removendo [divisórias nomeadas](service-fabric-concepts-partitioning.md) do serviço.

> [!NOTE]
> Atualmente, existe apoio para apenas uma política de escala por serviço, e apenas um gatilho de escala por política de escala.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Gatilho médio de carga de partição com escalamento baseado em exemplo
O primeiro tipo de gatilho baseia-se na carga de casos numa divisória de serviço apátrida. As cargas métricas são primeiro suavizadas para obter a carga para cada instância de uma partição, e então estes valores são médios em todos os casos da partição. Existem três fatores que determinam quando o serviço será dimensionado:

* _O limiar de carga mais baixo_ é um valor que determina quando o serviço será **dimensionado em**. Se a carga média de todas as instâncias das divisórias for inferior a este valor, então o serviço será dimensionado.
* _O limiar de carga superior_ é um valor que determina quando o serviço será **dimensionado**. Se a carga média de todas as instâncias da partição for superior a este valor, então o serviço será dimensionado.
* _O intervalo de escala_ determina com que frequência o gatilho será verificado. Uma vez verificado o gatilho, se for necessário escalonamento, o mecanismo será aplicado. Se não for necessário escalar, não serão tomadas medidas. Em ambos os casos, o gatilho não será verificado novamente antes que o intervalo de escala expire novamente.

Este gatilho só pode ser utilizado com serviços apátridas (recipientes apátridas ou serviços de Tecido de Serviço). No caso de um serviço ter múltiplas divisórias, o gatilho é avaliado para cada partição separadamente, e cada divisória terá o mecanismo especificado aplicado a ele de forma independente. Assim, neste caso, é possível que algumas das divisórias do serviço sejam escalonadas, algumas serão dimensionadas, e algumas não serão escaladas ao mesmo tempo, com base na sua carga.

O único mecanismo que pode ser usado com este gatilho é o PartitionInstanceInstanceSsechanchanism. Existem três fatores que determinam como este mecanismo é aplicado:
* _O Incremento de Escala_ determina quantas instâncias serão adicionadas ou removidas quando o mecanismo é acionado.
* _O Conde de Instância Máxima_ define o limite superior para a escala. Se o número de casos da partição atingir este limite, o serviço não será dimensionado, independentemente da carga. É possível omitir este limite especificando o valor de -1, e nesse caso o serviço será dimensionado o máximo possível (o limite é o número de nós que estão disponíveis no cluster).
* _O Conde de Instância Mínima_ define o limite inferior para a escala. Se o número de casos da partição atingir este limite, o serviço não será dimensionado independentemente da carga.

## <a name="setting-auto-scaling-policy-for-instance-based-scaling"></a>Definição da política de escalagem automática, por exemplo, de escalamento baseado

### <a name="using-application-manifest"></a>Utilização do manifesto de aplicação
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
### <a name="using-powershell"></a>Usando Powershell
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

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Gatilho médio de carga de serviço com escalamento baseado em partição
O segundo gatilho baseia-se na carga de todas as divisórias de um serviço. As cargas métricas são primeiramente suavizadas para obter a carga para cada réplica ou instância de uma partição. Para serviços estatais, a carga da partição é considerada a carga da réplica primária, enquanto para os serviços apátridas a carga da partição é a carga média de todos os casos da partição. Estes valores são médios em todas as divisórias do serviço, e este valor é usado para desencadear a escala automática. Tal como no mecanismo anterior, existem três fatores que determinam quando o serviço será dimensionado:

* _O limiar de carga mais baixo_ é um valor que determina quando o serviço será **dimensionado em**. Se a carga média de todas as divisórias do serviço for inferior a este valor, então o serviço será dimensionado.
* _O limiar de carga superior_ é um valor que determina quando o serviço será **dimensionado**. Se a carga média de todas as divisórias do serviço for superior a este valor, então o serviço será dimensionado.
* _O intervalo de escala_ determina com que frequência o gatilho será verificado. Uma vez verificado o gatilho, se for necessário escalonamento, o mecanismo será aplicado. Se não for necessário escalar, não serão tomadas medidas. Em ambos os casos, o gatilho não será verificado novamente antes que o intervalo de escala expire novamente.

Este gatilho pode ser usado tanto com serviços apátridas como apátridas. O único mecanismo que pode ser usado com este gatilho é AddRemoveIncrementalNamedPartitionScalingMechanism. Quando o serviço é dimensionado, uma nova divisória é adicionada, e quando o serviço é dimensionado em uma das divisórias existentes é removido. Existem restrições que serão verificadas quando o serviço for criado ou atualizado e a criação/atualização do serviço falhará se estas condições não forem satisfeitas:
* O regime de partição nomeado deve ser utilizado para o serviço.
* Os nomes de partição devem ser números inteiros consecutivos, como "0", "1", ...
* O primeiro nome de partição deve ser "0".

Por exemplo, se um serviço for inicialmente criado com três divisórias, a única possibilidade válida para nomes de partição é "0", "1" e "2".

A operação de escala automática real que é realizada respeitará também este esquema de nomeação:
* Se as divisórias atuais do serviço forem denominadas "0", "1" e "2", então a partição que será adicionada para escalonamento será denominada "3".
* Se as divisórias atuais do serviço forem denominada "0", "1" e "2", então a partição que será removida para escalonamento é partição com o nome "2".

Tal como acontece com o mecanismo que utiliza a escala através da adição ou remoção de instâncias, existem três parâmetros que determinam a forma como este mecanismo é aplicado:
* _O Incremento de Escala_ determina quantas divisórias serão adicionadas ou removidas quando o mecanismo é acionado.
* _O Conde Máximo de Partição_ define o limite superior para a escala. Se o número de divisórias do serviço atingir este limite, o serviço não será dimensionado, independentemente da carga. É possível omitir este limite especificando o valor de -1, e nesse caso o serviço será dimensionado o máximo possível (o limite é a capacidade real do cluster).
* _O Conde de Instância Mínima_ define o limite inferior para a escala. Se o número de divisórias do serviço atingir este limite, o serviço não será dimensionado independentemente da carga.

> [!WARNING] 
> Quando addRemoveIncrementalNamedPartitionScalingMechanism é utilizado com serviços estatais, o Service Fabric adicionará ou removerá divisórias **sem notificação ou aviso**. A repartição dos dados não será efetuada quando o mecanismo de escalagem for acionado. Em caso de operação de escala, as novas divisórias ficarão vazias e, em caso de escala em funcionamento, **a partição será eliminada juntamente com todos os dados que contém.**

## <a name="setting-auto-scaling-policy-for-partition-based-scaling"></a>Definição da política de escalagem automática para a escala baseada em divisórias

### <a name="using-application-manifest"></a>Utilização do manifesto de aplicação
``` xml
<NamedPartition>
    <Partition Name="0" />
</NamedPartition>
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
### <a name="using-powershell"></a>Usando Powershell
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

## <a name="auto-scaling-based-on-resources"></a>Dimensionamento automático com base em recursos

De forma a permitir que o serviço de monitorização de recursos se dimensione com base em recursos reais

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Há duas métricas que representam recursos físicos reais. Um deles é o servicefabric:/_CpuCores que representam o uso real do CPU (0,5 representa metade de um núcleo) e o outro é o servicefabric:/_MemoryInMB que representa o uso da memória em MBs.
O ResourceMonitorService é responsável pelo rastreio do cpu e do uso da memória dos serviços do utilizador. Este serviço aplicará uma média móvel ponderada, a fim de dar conta de potenciais picos de curta duração. A monitorização de recursos é suportada tanto para aplicações contentorizadas como não contentorizadas no Windows e para as contentorizadas em Linux. A escala automática de recursos só está ativada para serviços ativados em [modelo de processo exclusivo.](service-fabric-hosting-model.md#exclusive-process-model)

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a escalabilidade da aplicação.](service-fabric-concepts-scalability.md)
