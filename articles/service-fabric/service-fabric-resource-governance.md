---
title: Governança de recursos do Azure Service Fabric para contêineres e serviços | Microsoft Docs
description: O Service Fabric do Azure permite que você especifique os limites de recursos para os serviços em execução dentro ou fora dos contêineres.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: atsenthi
ms.openlocfilehash: aa388a688e76b0ba69231d8a11aa1bfa686f7f51
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166541"
---
# <a name="resource-governance"></a>Governação de recursos

Quando você estiver executando vários serviços no mesmo nó ou cluster, é possível que um serviço consuma mais recursos, privando outros serviços no processo. Esse problema é conhecido como o problema de "vizinho ruidosa". O Azure Service Fabric permite que o desenvolvedor especifique reservas e limites por serviço para garantir recursos e limitar o uso de recursos.

> Antes de prosseguir com este artigo, recomendamos que você se familiarize com o [modelo de aplicativo Service Fabric](service-fabric-application-model.md) e o [modelo de Hospedagem de Service Fabric](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Métricas de governança de recursos

A governança de recursos tem suporte em Service Fabric de acordo com o [pacote de serviço](service-fabric-application-model.md). Os recursos atribuídos ao pacote de serviço podem ser divididos ainda mais entre pacotes de código. Os limites de recursos especificados também significam a reserva dos recursos. O Service Fabric dá suporte à especificação de CPU e memória por pacote de serviço, com duas [métricas](service-fabric-cluster-resource-manager-metrics.md)internas:

* *CPU* (nome da métrica `servicefabric:/_CpuCores`): um núcleo lógico que está disponível no computador host. Todos os núcleos em todos os nós são ponderados da mesma.

* *Memória* (nome da métrica `servicefabric:/_MemoryInMB`): a memória é expressa em megabytes e é mapeada para a memória física que está disponível no computador.

Para essas duas métricas, o [Gerenciador de recursos de cluster](service-fabric-cluster-resource-manager-cluster-description.md) controla a capacidade total do cluster, a carga em cada nó no cluster e os recursos restantes no cluster. Essas duas métricas são equivalentes a qualquer outro usuário ou métrica personalizada. Todos os recursos existentes podem ser usados com eles:

* O cluster pode ser [balanceado](service-fabric-cluster-resource-manager-balancing.md) de acordo com essas duas métricas (comportamento padrão).
* O cluster pode ser [desfragmentado](service-fabric-cluster-resource-manager-defragmentation-metrics.md) de acordo com essas duas métricas.
* Ao [descrever um cluster, a](service-fabric-cluster-resource-manager-cluster-description.md)capacidade em buffer pode ser definida para essas duas métricas.

Não há suporte para o [relatório de carga dinâmica](service-fabric-cluster-resource-manager-metrics.md) para essas métricas, e as cargas para essas métricas são definidas no momento da criação.

## <a name="resource-governance-mechanism"></a>Mecanismo de governança de recursos

O tempo de execução de Service Fabric atualmente não fornece reserva para recursos. Quando um processo ou um contêiner é aberto, o tempo de execução define os limites de recursos para as cargas que foram definidas no momento da criação. Além disso, o tempo de execução rejeita a abertura de novos pacotes de serviço que estão disponíveis quando os recursos são excedidos. Para entender melhor como o processo funciona, vamos pegar um exemplo de um nó com dois núcleos de CPU (o mecanismo de governança de memória é equivalente):

1. Primeiro, um contêiner é colocado no nó, solicitando um núcleo de CPU. O tempo de execução abre o contêiner e define o limite de CPU para um núcleo. O contêiner não poderá usar mais de um núcleo.

2. Em seguida, uma réplica de um serviço é colocada no nó e o pacote de serviço correspondente especifica um limite de um núcleo de CPU. O tempo de execução abre o pacote de códigos e define seu limite de CPU para um núcleo.

Neste ponto, a soma dos limites é igual à capacidade do nó. Um processo e um contêiner estão sendo executados com um núcleo cada e não interferem uns com os outros. Service Fabric não coloca mais contêineres ou réplicas quando eles estão especificando o limite de CPU.

No entanto, há duas situações em que outros processos podem competir pela CPU. Nessas situações, um processo e um contêiner do nosso exemplo podem experimentar o problema de vizinho ruidosa:

* *Combinação de serviços e contêineres controlados e não controlados*: se um usuário criar um serviço sem nenhuma governança de recurso especificada, o tempo de execução o verá como não consumir nenhum recurso e poderá colocá-lo no nó em nosso exemplo. Nesse caso, esse novo processo efetivamente consome alguma CPU às custas dos serviços que já estão em execução no nó. Há duas soluções para esse problema. Não misture serviços controlados e não controlados no mesmo cluster ou use [restrições de posicionamento](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) para que esses dois tipos de serviços não acabem no mesmo conjunto de nós.

* *Quando outro processo é iniciado no nó, fora Service Fabric (por exemplo, um serviço de so)* : nessa situação, o processo fora Service Fabric também contende a CPU com os serviços existentes. A solução para esse problema é configurar corretamente as capacidades de nó para considerar a sobrecarga do sistema operacional, conforme mostrado na próxima seção.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Configuração de cluster para habilitar a governança de recursos

Quando um nó é iniciado e ingressa no cluster, Service Fabric detecta a quantidade disponível de memória e o número disponível de núcleos e, em seguida, define as capacidades dos nós para esses dois recursos.

Para deixar o espaço do buffer para o sistema operacional e para outros processos que podem estar em execução no nó, Service Fabric usa apenas 80% dos recursos disponíveis no nó. Esse percentual é configurável e pode ser alterado no manifesto do cluster.

Aqui está um exemplo de como instruir Service Fabric a usar 50% da CPU disponível e 70% da memória disponível:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Se precisar de configuração manual completa de capacidades do nó, você poderá usar o mecanismo regular para descrever os nós no cluster. Aqui está um exemplo de como configurar o nó com quatro núcleos e 2 GB de memória:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Quando a detecção automática de recursos disponíveis está habilitada e as capacidades de nó são definidas manualmente no manifesto do cluster, Service Fabric verifica se o nó tem recursos suficientes para dar suporte à capacidade definida pelo usuário:

* Se as capacidades do nó definidas no manifesto forem menores ou iguais aos recursos disponíveis no nó, Service Fabric usará as capacidades especificadas no manifesto.

* Se as capacidades de nó definidas no manifesto forem maiores que os recursos disponíveis, Service Fabric usará os recursos disponíveis como capacidades de nó.

A detecção automática de recursos disponíveis pode ser desativada se não for necessária. Para desativá-lo, altere a seguinte configuração:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Para um desempenho ideal, a configuração a seguir também deve ser ativada no manifesto do cluster:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

## <a name="specify-resource-governance"></a>Especificar governança de recursos

Os limites de governança de recursos são especificados no manifesto do aplicativo (seção ServiceManifestImport), conforme mostrado no exemplo a seguir:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

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

Neste exemplo, o pacote de serviço chamado de **Packager** Obtém um núcleo nos nós onde ele é colocado. Esse pacote de serviço contém dois pacotes de código (**CodeA1** e **CodeA2**) e ambos especificam o parâmetro `CpuShares`. A proporção do CpuShares 512:256 divide o núcleo entre os dois pacotes de código.

Portanto, neste exemplo, CodeA1 Obtém dois terços de um núcleo e CodeA2 Obtém um terço de um núcleo (e uma reserva de garantia flexível do mesmo). Se CpuShares não for especificado para pacotes de código, Service Fabric dividirá os núcleos igualmente entre eles.

Os limites de memória são absolutos, portanto, ambos os pacotes de código são limitados a 1024 MB de memória (e uma reserva de garantia flexível do mesmo). Pacotes de código (contêineres ou processos) não podem alocar mais memória do que esse limite e a tentativa de fazer isso resulta em uma exceção de memória insuficiente. Para que a imposição dos limites de recursos funcione, todos os pacotes do código dentro de um pacote de serviço devem ter limites de memória especificados.

### <a name="using-application-parameters"></a>Usando parâmetros de aplicativo

Ao especificar a governança de recursos, é possível usar [parâmetros de aplicativo](service-fabric-manage-multiple-environment-app-configuration.md) para gerenciar várias configurações de aplicativo. O exemplo a seguir mostra o uso de parâmetros de aplicativo:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

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

Neste exemplo, os valores de parâmetro padrão são definidos para o ambiente de produção, em que cada pacote de serviço obteria 4 núcleos e 2 GB de memória. É possível alterar valores padrão com arquivos de parâmetro de aplicativo. Neste exemplo, um arquivo de parâmetro pode ser usado para testar o aplicativo localmente, onde ele obteria menos recursos do que na produção:

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
> A especificação da governança de recursos com parâmetros de aplicativo está disponível a partir da versão Service Fabric 6,1.<br>
>
> Quando os parâmetros do aplicativo são usados para especificar a governança de recursos, Service Fabric não pode ser desatualizado para uma versão anterior à versão 6,1.

## <a name="other-resources-for-containers"></a>Outros recursos para contêineres

Além da CPU e da memória, é possível especificar outros limites de recursos para contêineres. Esses limites são especificados no nível do pacote de código e são aplicados quando o contêiner é iniciado. Ao contrário da CPU e da memória, o Gerenciador de recursos de cluster não está ciente desses recursos e não fará verificações de capacidade nem balanceamento de carga para eles.

* *MemorySwapInMB*: a quantidade de memória de permuta que um contêiner pode usar.
* *MemoryReservationInMB*: o limite flexível para a governança de memória que é imposta somente quando a contenção de memória é detectada no nó.
* *CpuPercent*: a porcentagem da CPU que o contêiner pode usar. Se os limites de CPU forem especificados para o pacote de serviço, esse parâmetro será efetivamente ignorado.
* *MaximumIOps*: o IOPS máximo que um contêiner pode usar (leitura e gravação).
* *MaximumIOBytesps*: o máximo de e/s (bytes por segundo) que um contêiner pode usar (leitura e gravação).
* *BlockIOWeight*: o peso de e/s de bloco para em relação a outros contêineres.

Esses recursos podem ser combinados com CPU e memória. Aqui está um exemplo de como especificar recursos adicionais para contêineres:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o Gerenciador de recursos de cluster, leia [introdução ao Gerenciador de recursos de cluster Service Fabric](service-fabric-cluster-resource-manager-introduction.md).
* Para saber mais sobre o modelo de aplicativo, pacotes de serviço e pacotes de código – e como as réplicas são mapeadas para eles, leia [modelar um aplicativo em Service Fabric](service-fabric-application-model.md).
