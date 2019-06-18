---
title: Recursos de infraestrutura do serviço do Azure automaticamente Dimensionar contentores e serviços | Documentos da Microsoft
description: O Azure Service Fabric permite-lhe definir políticas para contentores e serviços de dimensionamento automático.
services: service-fabric
documentationcenter: .net
author: radicmilos
manager: ''
editor: nipuzovi
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2018
ms.author: miradic
ms.openlocfilehash: 8e57c071c9fd93a8581d574aeec2b23b38b3ab95
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60844028"
---
# <a name="introduction-to-auto-scaling"></a>Introdução ao dimensionamento automático
Dimensionamento automático é uma capacidade adicional do Service Fabric para dimensionar dinamicamente os seus serviços com base na carga que serviços estão a enviar relatórios ou com base na respetiva utilização de recursos. Dimensionamento automático oferece excelente elasticidade e permite o aprovisionamento de instâncias adicionais ou partições do seu serviço a pedido. O processo de dimensionamento de automático todo é automatizada e transparente e, depois de configurar as políticas num serviço não é necessário para operações de dimensionamento manuais no nível de serviço. Dimensionamento automático pode ser ativado no momento da criação de serviço ou em qualquer altura ao atualizar o serviço.

Um cenário comum em que o dimensionamento automático é útil é quando a carga num determinado serviço varia ao longo do tempo. Por exemplo, um serviço como um gateway pode ser dimensionado com base na quantidade de recursos necessários para processar pedidos de entrada. Vamos dar uma olhada num exemplo de como o que essas regras de dimensionamento deve ser semelhante ao:
* Se todas as instâncias do meu gateway estiver a utilizar mais de dois núcleos em média, em seguida, dimensione o serviço de gateway horizontalmente ao adicionar uma instância mais. Fazer isso cada hora, mas nunca ter mais de sete instâncias no total.
* Se todas as instâncias do meu gateway estiver a utilizar inferior a 0,5 núcleos em média, em seguida, dimensione o serviço ao remover uma instância. Fazer isso cada hora, mas nunca tiver menos de três instâncias no total.

Dimensionamento automático é suportado para contentores e serviços do Service Fabric regulares. Para utilizar o dimensionamento automático, terá de estar em execução na versão 6.2 ou acima do runtime do Service Fabric. 

O resto deste artigo descreve as políticas de formas para ativar ou desativar o dimensionamento automático, dimensionamento e fornece exemplos sobre como utilizar esta funcionalidade.

## <a name="describing-auto-scaling"></a>Descrevendo o dimensionamento automático
Políticas de dimensionamento automático pode ser definido para cada serviço no cluster do Service Fabric. Cada política de dimensionamento consiste em duas partes:
* **Dimensionamento acionador** descreve quando será executada dimensionamento do serviço. Condições definidas no acionador são verificadas periodicamente para determinar se um serviço deverão ser aumentado ou não.

* **Mecanismo de dimensionamento** descreve como dimensionar será efetuada quando for acionado. Mecanismo só se aplica quando estiverem reunidas as condições do acionador.

Todos os acionadores que são atualmente suportados funcionará com [métricas de carga lógico](service-fabric-cluster-resource-manager-metrics.md), ou com a métrica física, como a utilização da CPU ou memória. De qualquer forma, o Service Fabric irá monitorizar a carga comunicada para a métrica e avaliará o acionador periodicamente para determinar se a opção dimensionamento é necessário.

Há dois mecanismos que são atualmente suportados para o dimensionamento automático. Primeiro destina-se para serviços sem estado ou para contentores, dimensionamento automático onde é feita adicionando ou removendo [instâncias](service-fabric-concepts-replica-lifecycle.md). Para os serviços com e sem estado, dimensionamento automático pode também ser executada ao adicionar ou remover nomeados [partições](service-fabric-concepts-partitioning.md) do serviço.

> [!NOTE]
> Atualmente, existe suporte para apenas uma política de dimensionamento por serviço e apenas um acionador de dimensionamento por política de dimensionamento.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Acionador de carga de partição média com a instância de dimensionamento com base em
O primeiro tipo de Acionador baseia-se a carga de instâncias de uma partição de serviço sem estado. Métrica cargas são primeiro suavizadas para obter a carga para cada instância de uma partição e, em seguida, estes valores são transformadas em médias de todas as instâncias da partição. Existem três fatores que determinam quando o serviço será dimensionado:

* _Limiar de carregamento inferior_ é um valor que determina quando o serviço será **reduzido horizontalmente**. Se a carga média de todas as instâncias das partições for inferior este valor, em seguida, o serviço irá ser reduzido horizontalmente.
* _Limiar de carregamento superior_ é um valor que determina quando o serviço será **aumentados horizontalmente**. Se a carga média de todas as instâncias da partição é maior do que este valor, em seguida, o serviço irá ser dimensionado.
* _Intervalo de dimensionamento_ determina a frequência com que o acionador será verificado. Assim que o acionador for selecionado, se o dimensionamento é necessário o mecanismo será aplicado. Se não for necessário dimensionamento, irá ser efetuada nenhuma ação. Em ambos os casos, acionador não estará marcado novamente antes de intervalo de dimensionamento novamente.

Este acionador pode ser utilizado apenas com serviços sem estado (contentores sem monitoração de estado ou serviços do Service Fabric). No caso de quando um serviço tem várias partições, o acionador é avaliado separadamente para cada partição, e cada partição terão o mecanismo especificado aplicado a ele de forma independente. Portanto, neste caso, é possível que algumas das partições do serviço irão ser aumentadas horizontalmente, alguns irão ser reduzido horizontalmente e alguns não ser dimensionados em todos os ao mesmo tempo, com base na carga.

O mecanismo único que pode ser utilizado com este acionador é PartitionInstanceCountScaleMechanism. Existem três fatores que determinam a forma como esse mecanismo é aplicado:
* _Dimensionar o incremento_ determina quantas instâncias serão adicionadas ou removidas quando o mecanismo é acionado.
* _Número máximo de instâncias_ define o limite superior para dimensionamento. Se o número de instâncias da partição atingir este limite, em seguida, o serviço será não ser aumentado horizontalmente, independentemente da carga. É possível omitir este limite, especificando o valor de -1 e, nesse caso, o serviço será dimensionado fora da melhor forma possível (o limite é o número de nós que estão disponíveis no cluster).
* _Contagem de instâncias mínima_ define o limite inferior para dimensionamento. Se o número de instâncias da partição atingir este limite, em seguida, serviço será não ser reduzido horizontalmente, independentemente da carga.

## <a name="setting-auto-scaling-policy"></a>A definição de política de dimensionamento automático

### <a name="using-application-manifest"></a>Usando o manifesto da aplicação
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
### <a name="using-c-apis"></a>Com APIs do c#
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
### <a name="using-powershell"></a>Com o Powershell
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

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Acionador de carga média do serviço com a partição de dimensionamento com base em
O acionador segundo baseia-se a carga de todas as partições de um serviço. Métrica cargas são primeiro suavizadas para obter a carga para cada réplica ou de uma instância de uma partição. Para serviços com estado, a carga da partição é considerada a carga da réplica primária, enquanto para serviços sem estado, a carga da partição se a carga média de todas as instâncias da partição. Estes valores são uma média de todas as partições do serviço e este valor é utilizado para acionar o dimensionamento automático. Mesmo do mecanismo anterior, existem três fatores que determinam quando o serviço será dimensionado:

* _Limiar de carregamento inferior_ é um valor que determina quando o serviço será **reduzido horizontalmente**. Se a carga média de todas as partições do serviço for inferior este valor, em seguida, o serviço irá ser reduzido horizontalmente.
* _Limiar de carregamento superior_ é um valor que determina quando o serviço será **aumentados horizontalmente**. Se a carga média de todas as partições do serviço for superior a este valor, em seguida, o serviço irá ser dimensionado.
* _Intervalo de dimensionamento_ determina a frequência com que o acionador será verificado. Assim que o acionador for selecionado, se o dimensionamento é necessário o mecanismo será aplicado. Se não for necessário dimensionamento, irá ser efetuada nenhuma ação. Em ambos os casos, acionador não estará marcado novamente antes de intervalo de dimensionamento novamente.

Este acionador pode ser utilizado com serviços com e sem estado. O mecanismo único que pode ser utilizado com este acionador é AddRemoveIncrementalNamedPartitionScalingMechanism. Quando o serviço é aumentado horizontalmente, em seguida, é adicionada uma nova partição, e quando o serviço é dimensionado de uma das partições existentes são removidos. Existem restrições que serão verificadas quando o serviço é criado ou atualizado e o serviço de criação/atualização falhará se estas condições não forem cumpridas:
* Esquema de partição com o nome tem de ser utilizada para o serviço.
* Nomes de partição tem de ser números integer consecutivos, como "0", "1",...
* Nome da primeira partição tem de ser "0".

Por exemplo, se um serviço é criado inicialmente com três partições, a possibilidade de só é válida para nomes de partição é "0", "1" e "2".

O operação é realizada de dimensionamento de automático real irá respeitar este esquema de nomenclatura:
* Se as partições atuais do serviço são denominadas "0", "1" e "2", em seguida, a partição que será adicionada para aumentar horizontalmente será nomeada "3".
* Se as partições atuais do serviço são o nome "0", "1" e "2", a partição que será removida para reduzir horizontalmente é a partição com o nome "2".

Mesmo assim como acontece com o mecanismo que utiliza o dimensionamento adicionando ou removendo instâncias, há três parâmetros que determinam a forma como esse mecanismo é aplicado:
* _Dimensionar o incremento_ determina quantas partições serão adicionadas ou removidas quando o mecanismo é acionado.
* _Número máximo de partição_ define o limite superior para dimensionamento. Se o número de partições do serviço de atingir este limite, em seguida, o serviço será não ser aumentado horizontalmente, independentemente da carga. É possível omitir este limite, especificando o valor de -1 e, nesse caso, o serviço será dimensionado fora da melhor forma possível (o limite é a capacidade real do cluster).
* _Contagem de instâncias mínima_ define o limite inferior para dimensionamento. Se o número de partições do serviço de atingir este limite, em seguida, serviço será não ser reduzido horizontalmente, independentemente da carga.

> [!WARNING] 
> Quando AddRemoveIncrementalNamedPartitionScalingMechanism é utilizado com serviços com estado, o Service Fabric irá adicionar ou remover partições **sem notificação ou aviso**. Repartição de dados não será executada quando o mecanismo de dimensionamento é acionado. No caso de operação de aumento vertical, novas partições estará vazias e, em caso de operação de dimensionamento **partição será eliminada juntamente com todos os dados que contém**.

## <a name="setting-auto-scaling-policy"></a>A definição de política de dimensionamento automático

### <a name="using-application-manifest"></a>Usando o manifesto da aplicação
``` xml
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedPartitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Com APIs do c#
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
### <a name="using-powershell"></a>Com o Powershell
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

## <a name="auto-scaling-based-on-resources"></a>Dimensionamento automático com base nos recursos

Para ativar o serviço de monitor de recursos dimensionar com base nos recursos reais

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Existem duas métricas que representam os recursos físicos reais. Uma delas é servicefabric: / _CpuCores que representam a utilização de cpu real (para que 0,5 representa metade um núcleo) e outra sendo servicefabric: / _MemoryInMB que representa o uso de memória em MB.
ResourceMonitorService é responsável por controlar a utilização de cpu e memória dos serviços de utilizador. Este serviço irá aplicar uma média móvel ponderada para potenciais picos de curta duração em conta. Monitorização de recursos é suportada para aplicações em contentores e não em contentores no Windows e para aqueles em contentores no Linux. Dimensionamento em recursos automático só está ativado para os serviços ativados [modelo de processo exclusivo](service-fabric-hosting-model.md#exclusive-process-model).

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [escalabilidade do aplicativo](service-fabric-concepts-scalability.md).
