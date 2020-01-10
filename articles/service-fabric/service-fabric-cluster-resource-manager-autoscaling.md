---
title: Contêineres e serviços de dimensionamento automático do Azure Service Fabric
description: O Service Fabric do Azure permite que você defina políticas de dimensionamento automático para serviços e contêineres.
author: radicmilos
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: miradic
ms.openlocfilehash: 3660ece7add8f279292340aae9ab445b682fe045
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452089"
---
# <a name="introduction-to-auto-scaling"></a>Introdução ao dimensionamento automático
O dimensionamento automático é um recurso adicional de Service Fabric para dimensionar dinamicamente seus serviços com base na carga que os serviços estão relatando ou com base no uso de recursos. O dimensionamento automático oferece grande elasticidade e permite o provisionamento de instâncias adicionais ou partições de seu serviço sob demanda. Todo o processo de dimensionamento automático é automatizado e transparente e, depois que você configura suas políticas em um serviço, não há necessidade de operações de dimensionamento manual no nível de serviço. O dimensionamento automático pode ser ativado no momento da criação do serviço ou a qualquer momento, atualizando o serviço.

Um cenário comum em que o dimensionamento automático é útil é quando a carga em um serviço específico varia ao longo do tempo. Por exemplo, um serviço como um gateway pode ser dimensionado com base na quantidade de recursos necessários para lidar com solicitações de entrada. Vamos dar uma olhada em um exemplo de como essas regras de dimensionamento poderiam ser:
* Se todas as instâncias do meu gateway estiverem usando mais de dois núcleos em média, expanda o serviço do gateway adicionando mais uma instância. Faça isso a cada hora, mas nunca tenha mais de sete instâncias no total.
* Se todas as instâncias do meu gateway estiverem usando menos de 0,5 núcleos em média, dimensione o serviço no removendo uma instância. Faça isso a cada hora, mas nunca tenha menos de três instâncias no total.

O dimensionamento automático tem suporte para contêineres e serviços de Service Fabric regulares. Para usar o dimensionamento automático, você precisa estar em execução na versão 6,2 ou superior do tempo de execução de Service Fabric. 

O restante deste artigo descreve as políticas de dimensionamento, maneiras de habilitar ou desabilitar o dimensionamento automático e fornece exemplos de como usar esse recurso.

## <a name="describing-auto-scaling"></a>Descrevendo o dimensionamento automático
As políticas de dimensionamento automático podem ser definidas para cada serviço em um Cluster Service Fabric. Cada política de dimensionamento consiste em duas partes:
* O **gatilho de dimensionamento** descreve quando o dimensionamento do serviço será executado. As condições definidas no gatilho são verificadas periodicamente para determinar se um serviço deve ser dimensionado ou não.

* O **mecanismo de dimensionamento** descreve como o dimensionamento será executado quando for disparado. O mecanismo só é aplicado quando as condições do gatilho são atendidas.

Todos os gatilhos com suporte no momento funcionam com [métricas de carga lógica](service-fabric-cluster-resource-manager-metrics.md)ou com métricas físicas, como uso de CPU ou memória. De qualquer forma, Service Fabric monitorará a carga relatada para a métrica e avaliará o gatilho periodicamente para determinar se o dimensionamento é necessário.

Há dois mecanismos que atualmente têm suporte para dimensionamento automático. O primeiro é destinado a serviços sem estado ou a contêineres em que o dimensionamento automático é executado adicionando ou removendo [instâncias](service-fabric-concepts-replica-lifecycle.md). Para serviços com e sem estado, o dimensionamento automático também pode ser executado adicionando ou removendo [partições](service-fabric-concepts-partitioning.md) nomeadas do serviço.

> [!NOTE]
> Atualmente, há suporte para apenas uma política de dimensionamento por serviço e apenas um gatilho de dimensionamento por política de dimensionamento.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Gatilho de carga de partição médio com dimensionamento baseado em instância
O primeiro tipo de gatilho é baseado na carga de instâncias em uma partição de serviço sem estado. As cargas de métricas são primeiro suavizadas para obter a carga de cada instância de uma partição e, em seguida, esses valores são calculados em todas as instâncias da partição. Há três fatores que determinam quando o serviço será dimensionado:

* _Limite de carga inferior_ é um valor que determina quando o serviço será **dimensionado horizontalmente**. Se a carga média de todas as instâncias das partições for menor que esse valor, o serviço será dimensionado no.
* _Limite de carga superior_ é um valor que determina quando o serviço será **escalado horizontalmente**. Se a carga média de todas as instâncias da partição for maior que esse valor, o serviço será escalado horizontalmente.
* O _intervalo de dimensionamento_ determina a frequência com que o gatilho será verificado. Quando o gatilho for verificado, se for necessário dimensionar, o mecanismo será aplicado. Se o dimensionamento não for necessário, nenhuma ação será executada. Em ambos os casos, o gatilho não será verificado novamente antes de o intervalo de dimensionamento expirar novamente.

Esse gatilho pode ser usado somente com serviços sem estado (contêineres sem monitoração de estado ou serviços de Service Fabric). No caso de um serviço ter várias partições, o gatilho é avaliado para cada partição separadamente, e cada partição terá o mecanismo especificado aplicado de forma independente. Portanto, nesse caso, é possível que algumas das partições do serviço sejam escaladas horizontalmente, algumas serão escaladas horizontalmente e outras não serão dimensionadas ao mesmo tempo, com base em sua carga.

O único mecanismo que pode ser usado com esse gatilho é PartitionInstanceCountScaleMechanism. Há três fatores que determinam como esse mecanismo é aplicado:
* O _incremento de escala_ determina quantas instâncias serão adicionadas ou removidas quando o mecanismo for disparado.
* _Contagem máxima de instâncias_ define o limite superior para o dimensionamento. Se o número de instâncias da partição atingir esse limite, o serviço não será escalado horizontalmente, independentemente da carga. É possível omitir esse limite especificando o valor de-1 e, nesse caso, o serviço será escalado horizontalmente o máximo possível (o limite é o número de nós disponíveis no cluster).
* A _contagem mínima de instâncias_ define o limite inferior para o dimensionamento. Se o número de instâncias da partição atingir esse limite, o serviço não será dimensionado, independentemente da carga.

## <a name="setting-auto-scaling-policy"></a>Definindo a política de dimensionamento automático

### <a name="using-application-manifest"></a>Usando o manifesto do aplicativo
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
### <a name="using-powershell"></a>Usando o PowerShell
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

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Gatilho de carga de serviço médio com dimensionamento baseado em partição
O segundo gatilho é baseado na carga de todas as partições de um serviço. As cargas de métricas são primeiro suavizadas para obter a carga de cada réplica ou instância de uma partição. Para serviços com estado, a carga da partição é considerada como a carga da réplica primária, enquanto para serviços sem estado a carga da partição é a carga média de todas as instâncias da partição. Esses valores são calculados com média em todas as partições do serviço, e esse valor é usado para disparar o dimensionamento automático. O mesmo que no mecanismo anterior, há três fatores que determinam quando o serviço será dimensionado:

* _Limite de carga inferior_ é um valor que determina quando o serviço será **dimensionado horizontalmente**. Se a carga média de todas as partições do serviço for menor do que esse valor, o serviço será dimensionado no.
* _Limite de carga superior_ é um valor que determina quando o serviço será **escalado horizontalmente**. Se a carga média de todas as partições do serviço for maior que esse valor, o serviço será escalado horizontalmente.
* O _intervalo de dimensionamento_ determina a frequência com que o gatilho será verificado. Quando o gatilho for verificado, se for necessário dimensionar, o mecanismo será aplicado. Se o dimensionamento não for necessário, nenhuma ação será executada. Em ambos os casos, o gatilho não será verificado novamente antes de o intervalo de dimensionamento expirar novamente.

Esse gatilho pode ser usado com serviços com e sem estado. O único mecanismo que pode ser usado com esse gatilho é AddRemoveIncrementalNamedPartitionScalingMechanism. Quando o serviço é escalado horizontalmente, uma nova partição é adicionada e quando o serviço é dimensionado em uma das partições existentes é removido. Há restrições que serão verificadas quando o serviço for criado ou atualizado e a criação/atualização de serviço falhará se essas condições não forem atendidas:
* O esquema de partição nomeado deve ser usado para o serviço.
* Os nomes de partição devem ser números inteiros consecutivos, como "0", "1",...
* O nome da primeira partição deve ser "0".

Por exemplo, se um serviço for criado inicialmente com três partições, a única possibilidade válida para os nomes de partição é "0", "1" e "2".

A operação de dimensionamento automático real que é executada também respeitará esse esquema de nomenclatura:
* Se as partições atuais do serviço forem nomeadas como "0", "1" e "2", a partição que será adicionada para expansão será denominada "3".
* Se as partições atuais do serviço forem nomeadas como "0", "1" e "2", a partição que será removida para o dimensionamento será a partição com o nome "2".

Mesmo que o mecanismo que usa o dimensionamento adicionando ou removendo instâncias, há três parâmetros que determinam como esse mecanismo é aplicado:
* O _incremento de escala_ determina quantas partições serão adicionadas ou removidas quando o mecanismo for disparado.
* A _contagem máxima de partições_ define o limite superior para o dimensionamento. Se o número de partições do serviço atingir esse limite, o serviço não será escalado horizontalmente, independentemente da carga. É possível omitir esse limite especificando o valor de-1 e, nesse caso, o serviço será escalado horizontalmente o máximo possível (o limite é a capacidade real do cluster).
* A _contagem mínima de instâncias_ define o limite inferior para o dimensionamento. Se o número de partições do serviço atingir esse limite, o serviço não será dimensionado, independentemente da carga.

> [!WARNING] 
> Quando AddRemoveIncrementalNamedPartitionScalingMechanism é usado com serviços com estado, Service Fabric irá adicionar ou remover partições **sem notificação ou aviso**. O reparticionamento de dados não será executado quando o mecanismo de dimensionamento for disparado. No caso da operação de expansão, as novas partições estarão vazias e, no caso da operação de reduzir verticalmente, **a partição será excluída junto com todos os dados que ela contém**.

## <a name="setting-auto-scaling-policy"></a>Definindo a política de dimensionamento automático

### <a name="using-application-manifest"></a>Usando o manifesto do aplicativo
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
### <a name="using-powershell"></a>Usando o PowerShell
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

Para habilitar o serviço de monitor de recursos para dimensionar com base em recursos reais

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Há duas métricas que representam os recursos físicos reais. Um deles é o infabric:/_CpuCores que representa o uso real da CPU (portanto, 0,5 representa metade de um núcleo) e o outro é o infabric:/_MemoryInMB que representa o uso de memória em MBs.
ResourceMonitorService é responsável por controlar o uso de CPU e memória dos serviços do usuário. Esse serviço aplicará a média móvel ponderada para considerar possíveis picos de curta duração. O monitoramento de recursos tem suporte para aplicativos em contêineres e não-contêineres no Windows e para aqueles em contêineres no Linux. O dimensionamento automático em recursos só é habilitado para serviços ativados no [modelo de processo exclusivo](service-fabric-hosting-model.md#exclusive-process-model).

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a [escalabilidade do aplicativo](service-fabric-concepts-scalability.md).
