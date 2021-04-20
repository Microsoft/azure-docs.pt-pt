---
title: Marcas dinâmicas de nódoas dinâmicas de tecido de serviço Azure
description: O Azure Service Fabric permite-lhe adicionar e remover dinâmicamente as marcas de nó.
author: yu-supersonic
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: branim
ms.custom: devx-track-csharp
ms.openlocfilehash: 712e6422060619e5567a74d6335320eff9ed8e66
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741929"
---
# <a name="introduction-to-dynamic-node-tags"></a>Introdução a marcas dinâmicas de nóleiros
As etiquetas de nó permitem-lhe adicionar e remover etiquetas dinamicamente dos nós de modo a influenciar a colocação de serviços. A marcação de nó é muito flexível e permite alterações na colocação do serviço sem atualizações de aplicação ou cluster. As etiquetas podem ser adicionadas ou removidas dos nós a qualquer momento, e os serviços podem especificar requisitos para determinadas tags quando são criadas. Um serviço também pode ter os seus requisitos de etiqueta atualizados dinamicamente enquanto está em execução.

A marcação de nó é semelhante às [restrições de colocação](service-fabric-cluster-resource-manager-configure-services.md) e é normalmente usada para controlar o que um nó de um serviço funciona. Cada serviço de Tecido de Serviço pode ser configurado para exigir a colocação da etiqueta ou para continuar a funcionar.

A marcação de nó é suportada para todos os tipos de serviço hospedados em Tecido de Serviço (Serviços Fiáveis, Execuables de Hóspedes e Contentores). Para utilizar a marcação de nó, tem de estar a executar a versão 8.0 ou superior ao tempo de funcionamento do Tecido de Serviço.

O resto deste artigo descreve formas de ativar ou desativar a marcação de nó e dá exemplos sobre como usar esta funcionalidade.


## <a name="describing-dynamic-node-tags"></a>Descrevendo tags de nó dinâmicos
Os serviços podem especificar as etiquetas que necessitam. Existem dois tipos de etiquetas:
* **As etiquetas necessárias para a colocação** descrevem um conjunto de tags, que são necessárias apenas para a colocação de serviço. Uma vez colocada a réplica, estas etiquetas podem ser removidas sem interromper o serviço. Se alguma destas tags for removida do nó, a réplica de serviço continuará a funcionar e o Tecido de Serviço não removerá o serviço

* **As etiquetas necessárias para executar** descrevem um conjunto de tags, que são necessárias tanto para a colocação como para o funcionamento do serviço. Se alguma das etiquetas de funcionamento necessárias for removida, o Service Fabric deslocará o serviço para outro nó que tenha as etiquetas especificadas.

Exemplo: As etiquetas de colocação podem ser utilizadas quando se utiliza algum tipo de serviço de ativador de contentores, e precisa desse serviço para o seu recipiente ser colocado, e assim que o recipiente for ativado, já não precisa de ativar, podendo remover a etiqueta associada a ele, mas o contentor deve continuar a funcionar.
As etiquetas de funcionamento podem ser utilizadas quando se tem um serviço de faturação, que é útil para ser colocado com o serviço virado para o utilizador. Quando o serviço de faturação falha no nó, remove-se a etiqueta associada ao mesmo, e o serviço virado para o utilizador é transferido para outro nó, que tem serviço de faturação, e a sua etiqueta, presente.

Uma etiqueta ou conjunto de tags pode ser adicionado, atualizado ou removido de um único nó utilizando mecanismos de interface padrão do Tecido de Serviço, tais como APIs C#, APIs REST ou Comandos PowerShell.

> [!NOTE]
> O Tecido de Serviço não mantém as distribuições UD/FD quando se utilizam marcas de nó. Por favor, gerencie as etiquetas de nó adequadamente, para que não obtenha violações de FD/UD devido à má distribuição de tags em domínios.

## <a name="enabling-dynamic-node-tags"></a>Habilitando etiquetas dinâmicas de nóleiros
Para que esta funcionalidade funcione, terá de ativar a config NodeTaggingEnabled na secção placementAndLoadBalancing do cluster manifest quer utilizando XML ou JSON:

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="NodeTaggingEnabled" Value="true" />
</Section>
```

através ClusterConfig.jspara implantações autónomas ou Template.jspara aglomerados azure hospedados:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": " NodeTaggingEnabled ",
          "value": "true"
      }
    ]
  }
]
```

## <a name="setting-dynamic-node-tags"></a>Definição de marcas de nójoiais dinâmicas

### <a name="using-powershell"></a>Com o PowerShell

Adicionar etiquetas de nó ao nó:

```posh
Add-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
Este comando adicionará as etiquetas "SampleTag1" e "SampleTag2" no nó DB.1.

Retire as etiquetas dos nóis do nó:

```posh
Remove-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
Este comando removerá as etiquetas "SampleTag1" e "SampleTag2" no nó DB.1.

### <a name="using-c-apis"></a>Usando C# APIs

Adicionar etiquetas de nó ao nó:

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.AddNodeTagsAsync("DB.1", nodeTagsList);
```

Retire as etiquetas dos nóis do nó:

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.RemoveNodeTagsAsync("DB.1", nodeTagsList);
```

## <a name="setting-required-tags-for-services"></a>Definição de tags necessárias para serviços

### <a name="using-powershell"></a>Com o PowerShell

Criação de um novo serviço:

```posh
New-ServiceFabricService -ApplicationName fabric:/HelloWorld -ServiceName fabric:/HelloWorld/svc1 -ServiceTypeName HelloWorldStateful -Stateful -PartitionSchemeSingleton -TargetReplicaSetSize 5 -MinReplicaSetSize 3 -TagsRequiredToRun @("SampleTag1") - TagsRequiredToPlace @("SampleTag2")
```
Este comando cria um serviço, que requer que o "SampleTag2" esteja presente num nó para que o serviço seja colocado lá, e que o "SampleTag1" esteja presente para que o serviço continue a funcionar nesse nó.

Atualização do serviço existente:

```posh
Update-ServiceFabricService -Stateful -ServiceName fabric:/myapp/test -TagsRequiredToRun @("SampleTag1") -TagsRequiredToPlace @("SampleTag2")
```
Este comando atualiza um serviço, que requer que o "SampleTag2" esteja presente num nó para que o serviço seja colocado lá, e "SampleTag1" esteja presente para que o serviço continue a funcionar nesse nó.

### <a name="using-c-apis"></a>Usando C# APIs

Criação de um novo serviço:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceDescription.ServiceTags = serviceTags;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Atualização do serviço existente:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceUpdate.ServiceTags = serviceTags;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```

Todos estes comandos aplicam-se igualmente aos serviços apátridas.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [as restrições de colocação](service-fabric-cluster-resource-manager-configure-services.md)
