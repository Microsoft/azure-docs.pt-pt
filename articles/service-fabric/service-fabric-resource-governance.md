---
title: Governação de recursos do Service Fabric do Azure para contentores e serviços | Documentos da Microsoft
description: O Azure Service Fabric permite-lhe especificar os limites de recursos para serviços em execução no interior ou exteriores contentores.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: aljo, subramar
ms.openlocfilehash: 1a9d9e0b6a82bd4bb3312df5288c04d0e52af3a6
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805677"
---
# <a name="resource-governance"></a>Governação de recursos

Quando estiver a executar vários serviços no mesmo nó ou cluster, é possível que um serviço pode consumir mais recursos, privação outros serviços no processo. Esse problema é referido como o problema de "vizinho ruidoso". O Azure Service Fabric permite que o desenvolvedor especificar as reservas e os limites por serviço para garantir a recursos e limitar a utilização de recursos.

> Antes de continuar com este artigo, recomendamos que se familiarizar com o [modelo de aplicação do Service Fabric](service-fabric-application-model.md) e o [modelo de hospedagem do Service Fabric](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Métricas de governação de recursos

Governação de recursos é suportada nos recursos de infraestrutura do serviço de acordo com o [pacote de serviço](service-fabric-application-model.md). Os recursos que são atribuídos ao pacote de serviço podem ser mais divididos entre pacotes do código. Os limites de recursos que são especificados também significa que a reserva dos recursos. Service Fabric suporta a especificação de CPU e memória por pacote de serviço, com dois interna [métricas](service-fabric-cluster-resource-manager-metrics.md):

* *CPU* (nome da métrica `servicefabric:/_CpuCores`): Um núcleo de lógico que está disponível na máquina host. Todos os núcleos em todos os nós são ponderados o mesmo.

* *Memória* (nome da métrica `servicefabric:/_MemoryInMB`): Memória é expressa em megabytes, e ele mapeia para a memória física disponível na máquina.

Para essas duas medições [Resource Manager de Cluster](service-fabric-cluster-resource-manager-cluster-description.md) controla a capacidade total do cluster, a carga em cada nó do cluster e os restantes recursos no cluster. Essas duas métricas são equivalentes a qualquer utilizador ou métrica personalizada. Todas as funcionalidades existentes podem ser utilizadas com os mesmos:

* O cluster pode ser [balanceada](service-fabric-cluster-resource-manager-balancing.md) , de acordo com essas duas métricas (comportamento predefinido).
* O cluster pode ser [desfragmentados](service-fabric-cluster-resource-manager-defragmentation-metrics.md) , de acordo com essas duas métricas.
* Quando [descrever um cluster](service-fabric-cluster-resource-manager-cluster-description.md), em buffer capacidade pode ser definida para essas duas métricas.

[Relatórios de carga dinâmica](service-fabric-cluster-resource-manager-metrics.md) não é suportada para estas métricas e carrega para estas métricas são definidas no momento da criação.

## <a name="resource-governance-mechanism"></a>Mecanismo de governação de recursos

O tempo de execução do Service Fabric atualmente não fornecer uma reserva de recursos. Quando um processo ou contentor é aberto, o tempo de execução define os limites de recursos para as cargas que foram definidas no momento da criação. Além disso, o tempo de execução rejeita a abertura de novos pacotes de serviço que estão disponíveis quando os recursos são foi excedidos. Para compreender melhor como funciona o processo, vamos dar um exemplo de um nó com dois núcleos de CPU (mecanismo de governação de memória é equivalente a maiúsculas e minúsculas):

1. Em primeiro lugar, um contêiner é colocado no nó, solicitando um núcleo de CPU. O tempo de execução é aberto o contentor e define o limite de CPU para um núcleo. O contentor não poderá utilizar mais de um núcleo.

2. Em seguida, uma réplica de um serviço é colocada no nó e o pacote de serviço correspondente Especifica um limite de núcleo de CPU. O tempo de execução é aberto o pacote do código e define o limite de CPU para um núcleo.

Neste momento, a soma dos limites de é igual à capacidade do nó. Um processo e um contentor em execução com um núcleo e não esteja a interferir uns com os outros. Service Fabric não coloca mais contentores ou réplicas quando está a especificar o limite de CPU.

No entanto, existem duas situações em que outros processos podem disputar da CPU. Nestas situações, um processo e um contentor a partir do nosso exemplo poderão ocorrer o problema de vizinhos ruidosos:

* *Combinar serviços governados e não sejam regidos e contentores*: Se um utilizador cria um serviço sem qualquer governação de recursos especificada, o tempo de execução o vê como consumir sem recursos e pode colocá-lo no nó no nosso exemplo. Neste caso, este novo processo consome efetivamente alguns CPU às custas dos serviços que já estão em execução no nó. Existem dois solução para esse problema. Um não misturar governados e não sejam regidos serviços no mesmo cluster ou utilize [restrições de posicionamento](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) para que estes dois tipos de serviços não acabam no mesmo conjunto de nós.

* *Quando o outro processo é iniciado no nó, fora do Service Fabric (por exemplo, um serviço de sistema operacional)*: Nesta situação, o processo de fora do Service Fabric também suporta para a CPU com os serviços existentes. A solução para esse problema é configurar as capacidades de nós corretamente a conta para a sobrecarga de SO, conforme mostrado na próxima seção.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Configuração do cluster para ativar a governação de recursos

Quando um nó é iniciado e adere ao cluster, o Service Fabric Deteta a quantidade disponível de memória e o número de núcleos disponível e, em seguida, define as capacidades de nó para esses dois recursos.

Para deixar espaço de memória intermédia para o sistema operativo e para outros processos pode estar em execução no nó, o Service Fabric utiliza apenas a 80% dos recursos disponíveis no nó. Essa porcentagem é configurável e pode ser alterada no manifesto do cluster.

Eis um exemplo de como instruir o Service Fabric para utilizar a 50% de CPU disponível e 70% de memória disponível:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Se precisar de uma configuração completa manual das capacidades de nós, pode utilizar o mecanismo regular para descrever os nós do cluster. Eis um exemplo de como configurar o nó com quatro núcleos e 2 GB de memória:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Quando a deteção automática de recursos disponíveis é ativada e as capacidades de nós manualmente são definidas no manifesto do cluster, o Service Fabric verifica que o nó tem recursos suficientes para suportar a capacidade de que o utilizador tiver definido:

* Se as capacidades de nós que estão definidas no manifesto do menor ou igual aos recursos disponíveis no nó, em seguida, o Service Fabric utiliza as capacidades que são especificadas no manifesto.

* Se as capacidades de nós que estão definidas no manifesto são maiores que recursos disponíveis, o Service Fabric utiliza os recursos disponíveis como as capacidades de nós.

Deteção automática de recursos disponíveis pode ser desativada se não for necessário. Para desativá-la, altere a definição seguinte:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Para um desempenho ideal, a definição seguinte deve também ser ativada no manifesto do cluster:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

## <a name="specify-resource-governance"></a>Especifique a governação de recursos

Limites de governação de recursos são especificados no manifesto do aplicativo (ServiceManifestImport secção), conforme mostrado no exemplo a seguir:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>

  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric sums the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```

Neste exemplo, o pacote de serviço chamado **ServicePackageA** obtém um núcleo em nós de onde está colocado. Este pacote de serviço contém dois pacotes de código (**CodeA1** e **CodeA2**), e ambos especificar o `CpuShares` parâmetro. A proporção de CpuShares 512:256 divide o núcleo entre os pacotes de código de dois.

Portanto, neste exemplo, CodeA1 obtém dois terços de um núcleo e CodeA2 obtém um terço de um núcleo (e uma reserva de garantia das mesmas). Se CpuShares não forem especificadas para pacotes de código, o Service Fabric divide os núcleos igualmente entre eles.

Limites de memória são absolutos, pelo que ambos os pacotes de código estão limitados a 1024 MB de memória (e uma reserva de garantia das mesmas). Pacotes de código (contentores ou processos) não é possível alocar mais memória do que este limite e tentar fazê-lo resulta numa exceção de memória esgotada. Para que a imposição dos limites de recursos funcione, todos os pacotes do código dentro de um pacote de serviço devem ter limites de memória especificados.

### <a name="using-application-parameters"></a>O uso de parâmetros de aplicação

Quando especificar a governação de recursos é possível usar [parâmetros de aplicação](service-fabric-manage-multiple-environment-app-configuration.md) para gerir várias configurações de aplicação. O exemplo seguinte mostra a utilização de parâmetros de aplicação:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

Neste exemplo, valores de parâmetro predefinidos estão definidos para o ambiente de produção, onde cada pacote de serviço obteria 4 núcleos e 2 GB de memória. É possível alterar os valores predefinidos com ficheiros de parâmetro de aplicação. Neste exemplo, um ficheiro de parâmetro pode ser utilizado para testar a aplicação localmente, onde ele obter menos recursos do que na produção:

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT]
> A especificação de governação de recursos com parâmetros de aplicação está disponível a partir com a versão 6.1 do Service Fabric.<br>
>
> Quando os parâmetros de aplicação são utilizados para especificar a governação de recursos, o Service Fabric não pode ser desatualizado para uma versão anterior à versão 6.1.

## <a name="other-resources-for-containers"></a>Outros recursos para contentores

Além da CPU e memória, é possível especificar outros limites de recursos para contentores. Estes limites são especificados no nível de pacote do código e são aplicados quando o contentor é iniciado. Ao contrário com CPU e memória, o Gestor de recursos de Cluster não conhece estes recursos e não nenhuma verificação de capacidade ou balanceamento de carga para eles.

* *MemorySwapInMB*: A quantidade de memória de comutação que pode utilizar um contentor.
* *MemoryReservationInMB*: O limite não restritivo de governação de memória que é aplicado apenas quando a contenção de memória é detetada no nó.
* *CpuPercent*: A percentagem de CPU que pode utilizar o contentor. Se os limites de CPU são especificados para o pacote de serviço, este parâmetro é efetivamente ignorado.
* *MaximumIOps*: O IOPS máximo que pode utilizar um contentor (leitura e escrita).
* *MaximumIOBytesps*: O máximo e/s (bytes por segundo) que pode utilizar um contentor (leitura e escrita).
* *BlockIOWeight*: O bloco de peso de e/s para em relação a outros contentores.

Estes recursos podem ser combinados com a CPU e memória. Eis um exemplo de como especificar recursos adicionais para contentores:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre o Gestor de recursos do Cluster, leia [apresentando o Gestor de recursos de cluster do Service Fabric](service-fabric-cluster-resource-manager-introduction.md).
* Para saber mais sobre o modelo de aplicativo, pacotes de serviço e os pacotes do código – e como réplicas mapeiam para – Leia [modelar uma aplicação no Service Fabric](service-fabric-application-model.md).
