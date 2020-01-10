---
title: Governação de recursos para contentores e serviços
description: O Service Fabric do Azure permite que você especifique os limites de recursos para os serviços em execução dentro ou fora dos contêineres.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: 85520876d7f0c89450b572d28dee6cb66ed2231d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772385"
---
# <a name="resource-governance"></a>Governação de recursos

Quando você estiver executando vários serviços no mesmo nó ou cluster, é possível que um serviço consuma mais recursos, privando outros serviços no processo. Esse problema é conhecido como o problema de "vizinho ruidosa". O Azure Service Fabric permite que o desenvolvedor especifique reservas e limites por serviço para garantir recursos e limitar o uso de recursos.

> Antes de prosseguir com este artigo, recomendamos que você se familiarize com o [modelo de aplicativo Service Fabric](service-fabric-application-model.md) e o [modelo de Hospedagem de Service Fabric](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Métricas de governança de recursos

A governança de recursos tem suporte em Service Fabric de acordo com o [pacote de serviço](service-fabric-application-model.md). Os recursos atribuídos ao pacote de serviço podem ser divididos ainda mais entre pacotes de código. Os limites de recursos especificados também significam a reserva dos recursos. O Service Fabric dá suporte à especificação de CPU e memória por pacote de serviço, com duas [métricas](service-fabric-cluster-resource-manager-metrics.md)internas:

* *CPU* (nome da métrica `servicefabric:/_CpuCores`): um núcleo lógico que está disponível no computador host. Todos os núcleos em todos os nós são ponderados da mesma.

* *Memória* (nome da métrica `servicefabric:/_MemoryInMB`): a memória é expressa em megabytes e é mapeada para a memória física disponível no computador.

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

Para a maioria dos clientes e cenários, a detecção automática de capacidades de nó para CPU e memória é a configuração recomendada (a detecção automática é ativada por padrão). No entanto, se precisar de configuração manual completa de capacidades do nó, você poderá configurá-las por tipo de nó usando o mecanismo para descrever os nós no cluster. Aqui está um exemplo de como configurar o tipo de nó com quatro núcleos e 2 GB de memória:

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

> [!IMPORTANT]
> A partir do Service Fabric versão 7,0, atualizamos a regra para como as capacidades de recursos de nó são calculadas nos casos em que o usuário fornece manualmente os valores para as capacidades de recurso do nó. Vamos considerar o seguinte cenário:
>
> * Há 10 núcleos de CPU total no nó
> * O it está configurado para usar 80% do total de recursos para os serviços de usuário (configuração padrão), que deixa um buffer de 20% para os outros serviços em execução no nó (incluindo Service Fabric serviços do sistema)
> * O usuário decide substituir manualmente a capacidade do recurso de nó para a métrica de núcleos de CPU e a define como 5 núcleos
>
> Alteramos a regra sobre como a capacidade disponível para serviços de usuário Service Fabric é calculada da seguinte maneira:
>
> * Antes de Service Fabric 7,0, a capacidade disponível para serviços de usuário seria calculada para **5 núcleos** (o buffer de capacidade de 20% é ignorado)
> * A partir do Service Fabric 7,0, a capacidade disponível para serviços de usuário seria calculada para **4 núcleos** (o buffer de capacidade de 20% não é ignorado)

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

Ao especificar as configurações de governança de recursos, é possível usar [parâmetros de aplicativo](service-fabric-manage-multiple-environment-app-configuration.md) para gerenciar várias configurações de aplicativo. O exemplo a seguir mostra o uso de parâmetros de aplicativo:

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

## <a name="enforcing-the-resource-limits-for-user-services"></a>Impondo os limites de recursos para serviços de usuário

Ao aplicar a governança de recursos aos seus serviços de Service Fabric garante que esses serviços controlados por recursos não possam exceder sua cota de recursos, muitos usuários ainda precisam executar alguns dos serviços Service Fabric no modo não controlado. Ao usar serviços de Service Fabric não governados, é possível executar situações em que serviços não governados "descontrolados" consomem todos os recursos disponíveis nos nós de Service Fabric, o que pode levar a problemas sérios, como:

* Consumo de recursos de outros serviços em execução nos nós (incluindo Service Fabric serviços do sistema)
* Nós terminando em um estado não íntegro
* APIs de gerenciamento de Cluster Service Fabric sem resposta

Para evitar que essas situações ocorram, Service Fabric permite que você *aplique os limites de recursos para todos os Service Fabric serviços de usuário em execução no nó* (governados e não controlados) para garantir que os serviços de usuário nunca usarão mais do que a quantidade especificada de recursos. Isso é feito definindo o valor para a configuração EnforceUserServiceMetricCapacities na seção PlacementAndLoadBalancing de ClusterManifest como true. Essa configuração é desativada por padrão.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

Comentários adicionais:

* A imposição de limite de recursos se aplica somente às métricas de recurso de `servicefabric:/_CpuCores` e `servicefabric:/_MemoryInMB`
* A imposição de limite de recursos só funciona se as capacidades de nó para as métricas de recurso estiverem disponíveis para Service Fabric, seja por meio do mecanismo de detecção automática ou por usuários especificando manualmente as capacidades do nó (conforme explicado na seção [configuração do cluster para habilitar a governança de recursos](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance) ). Se as capacidades do nó não estiverem configuradas, o recurso de imposição de limite de recursos não poderá ser usado, pois Service Fabric não poderá saber a quantidade de recursos reservados para os serviços do usuário. Service Fabric emitirá um aviso de integridade se "EnforceUserServiceMetricCapacities" for true, mas as capacidades do nó não estiverem configuradas.

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
