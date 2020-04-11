---
title: Governação de recursos para contentores e serviços
description: O Tecido de Serviço Azure permite especificar limites de recursos para serviços que estão a funcionar dentro ou fora dos contentores.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: 11ca6e29829d911717a829b3e4dee0a190856a52
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115136"
---
# <a name="resource-governance"></a>Governação de recursos

Quando você está executando vários serviços no mesmo nó ou cluster, é possível que um serviço possa consumir mais recursos, morrendo de fome outros serviços no processo. Este problema é referido como o problema do "vizinho barulhento". O Tecido de Serviço Azure permite ao desenvolvedor especificar reservas e limites por serviço para garantir recursos e limitar a utilização de recursos.

> Antes de avançar com este artigo, recomendamos que se familiarize com o modelo de [aplicação Service Fabric](service-fabric-application-model.md) e o modelo de [hospedagem do Tecido de Serviço.](service-fabric-hosting-model.md)
>

## <a name="resource-governance-metrics"></a>Métricas de governação de recursos

A governação de recursos é apoiada no Tecido de Serviço de acordo com o pacote de [serviços.](service-fabric-application-model.md) Os recursos atribuídos ao pacote de serviços podem ser ainda divididos entre pacotes de código. Os limites de recursos especificados significam também a reserva dos recursos. Serviço Tecido suporta especificar CPU e memória por pacote de serviço, com duas [métricas incorporadas:](service-fabric-cluster-resource-manager-metrics.md)

* *CPU* (nome `servicefabric:/_CpuCores`métrico ): Um núcleo lógico disponível na máquina hospedeira. Todos os núcleos em todos os nós são ponderados da mesma forma.

* *Memória* (nome `servicefabric:/_MemoryInMB`métrico): A memória é expressa em megabytes, e mapeia para a memória física que está disponível na máquina.

Para estas duas métricas, cluster [Resource Manager](service-fabric-cluster-resource-manager-cluster-description.md) rastreia a capacidade total do cluster, a carga em cada nó no cluster, e os recursos restantes no cluster. Estas duas métricas são equivalentes a qualquer outro utilizador ou métrica personalizada. Todas as funcionalidades existentes podem ser utilizadas com eles:

* O cluster pode ser [equilibrado](service-fabric-cluster-resource-manager-balancing.md) de acordo com estas duas métricas (comportamento padrão).
* O cluster pode ser [desfragmentado](service-fabric-cluster-resource-manager-defragmentation-metrics.md) de acordo com estas duas métricas.
* Ao [descrever um cluster,](service-fabric-cluster-resource-manager-cluster-description.md)a capacidade tamponada pode ser definida para estas duas métricas.

> [!NOTE]
> [O relatório dinâmico da carga](service-fabric-cluster-resource-manager-metrics.md) não é suportado para estas métricas; as cargas para estas métricas são definidas no momento da criação.

## <a name="resource-governance-mechanism"></a>Mecanismo de governação de recursos

O tempo de funcionação do Tecido de Serviço atualmente não fornece reserva para recursos. Quando um processo ou um recipiente é aberto, o tempo de execução define os limites de recursos para as cargas que foram definidas no momento da criação. Além disso, o prazo de funcionamento rejeita a abertura de novos pacotes de serviços disponíveis quando os recursos são excedidos. Para entender melhor como o processo funciona, vamos dar o exemplo de um nó com dois núcleos cpu (mecanismo para a governação da memória é equivalente):

1. Primeiro, um recipiente é colocado no nó, solicitando um núcleo cpu. O tempo de execução abre o recipiente e define o limite de CPU para um núcleo. O recipiente não poderá usar mais do que um núcleo.

2. Em seguida, uma réplica de um serviço é colocada no nó, e o pacote de serviço correspondente especifica um limite de um núcleo CPU. O tempo de execução abre o pacote de código e define o seu limite de CPU para um núcleo.

Neste ponto, a soma dos limites é igual à capacidade do nó. Um processo e um recipiente estão a funcionar com um núcleo cada um e não interferem uns com os outros. O tecido de serviço não coloca mais recipientes ou réplicas quando estão a especificar o limite de CPU.

No entanto, há duas situações em que outros processos poderão ser descontinuados pela CPU. Nestas situações, um processo e um recipiente do nosso exemplo podem experimentar o problema ruidoso do vizinho:

* *Mistura de serviços e contentores governados e não governados*: Se um utilizador criar um serviço sem qualquer governação de recursos especificado, o tempo de funcionamento vê-o como não consumindo recursos, e pode colocá-lo no nó no nosso exemplo. Neste caso, este novo processo consome efetivamente algum CPU em detrimento dos serviços que já estão a funcionar no nó. Há duas soluções para este problema. Ou não misture serviços governados e não governados no mesmo cluster, ou use restrições de [colocação](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) para que estes dois tipos de serviços não acabem no mesmo conjunto de nós.

* *Quando outro processo é iniciado no nó, fora do Service Fabric (por exemplo, um serviço de SO)*: Nesta situação, o processo fora do Service Fabric também defende cpU com serviços existentes. A solução para este problema é criar capacidades de nó corretamente para contabilizar as despesas gerais do OS, como mostra a secção seguinte.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Configuração do cluster para permitir a governação dos recursos

Quando um nó começa e junta o cluster, o Service Fabric deteta a quantidade disponível de memória e o número disponível de núcleos, e depois define as capacidades do nó para esses dois recursos.

Para deixar espaço tampão para o sistema operativo, e para outros processos pode estar a funcionar no nó, o Service Fabric utiliza apenas 80% dos recursos disponíveis no nó. Esta percentagem é configurável e pode ser alterada no manifesto do cluster.

Aqui está um exemplo de como instruir o Tecido de Serviço a utilizar 50% do CPU disponível e 70% da memória disponível:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Para a maioria dos clientes e cenários, a deteção automática das capacidades do nó para o CPU e a memória é a configuração recomendada (a deteção automática é ligada por padrão). No entanto, se necessitar de configuração manual completa das capacidades do nó, pode configurá-las por tipo de nó utilizando o mecanismo para descrever os nós no cluster. Aqui está um exemplo de como configurar o tipo de nó com quatro núcleos e 2 GB de memória:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Quando a deteção automática dos recursos disponíveis está ativada, e as capacidades dos nós são definidas manualmente no manifesto do cluster, o Service Fabric verifica que o nó tem recursos suficientes para suportar a capacidade que o utilizador definiu:

* Se as capacidades do nó definidas no manifesto forem inferiores ou iguais aos recursos disponíveis no nó, então o Service Fabric utiliza as capacidades especificadas no manifesto.

* Se as capacidades do nó definidas no manifesto forem maiores do que os recursos disponíveis, o Service Fabric utiliza os recursos disponíveis como capacidades de nó.

A deteção automática dos recursos disponíveis pode ser desligada se não for necessário. Para desligá-lo, altere a seguinte definição:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Para um desempenho ótimo, a seguinte definição também deve ser ligada no manifesto do cluster:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> Começando pela versão 7.0 do Tecido de Serviço, atualizámos a regra de como as capacidades de recursos do nó são calculadas nos casos em que o utilizador fornece manualmente os valores para as capacidades de recursos do nó. Vamos considerar o seguinte cenário:
>
> * Há 10 núcleos cpu no nó
> * A SF está configurada para utilizar 80% do total de recursos para os serviços de utilizador (definição padrão), o que deixa um tampão de 20% para os outros serviços em funcionamento no nó (incl. Serviços de sistema de serviço sinuoso)
> * O utilizador decide substituir manualmente a capacidade de recurso do nó para a métrica dos núcleos cpu, e define-a para 5 núcleos
>
> Alterámos a regra sobre a forma como a capacidade disponível para os serviços de utilizador do Service Fabric é calculada da seguinte forma:
>
> * Antes do Serviço Fabric 7.0, a capacidade disponível para os serviços de utilizador seria calculada a **5 núcleos** (o tampão de capacidade de 20% é ignorado)
> * A partir do Tecido de Serviço 7.0, a capacidade disponível para os serviços de utilizador seria calculada para **4 núcleos** (o tampão de capacidade de 20% não é ignorado)

## <a name="specify-resource-governance"></a>Especificar a governação dos recursos

Os limites de governação dos recursos são especificados no manifesto de aplicação (secção ServiceManifestImport) como mostrado no seguinte exemplo:

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

Neste exemplo, o pacote de serviços chamado **ServicePackageA** recebe um núcleo nos nós onde é colocado. Este pacote de serviço contém dois pacotes de código **(CodeA1** e **CodeA2),** e ambos especificam o `CpuShares` parâmetro. A proporção de CpuShares 512:256 divide o núcleo entre os dois pacotes de código.

Assim, neste exemplo, o CodeA1 recebe dois terços de um núcleo, e o CodeA2 recebe um terço de um núcleo (e uma reserva de garantia suave do mesmo). Se o CpuShares não for especificado para pacotes de código, o Service Fabric divide os núcleos igualmente entre eles.

Os limites de memória são absolutos, pelo que ambos os pacotes de código estão limitados a 1024 MB de memória (e uma reserva de garantia suave dos mesmos). Os pacotes de código (contentores ou processos) não podem alocar mais memória do que este limite, e tentar fazê-lo resulta numa exceção fora da memória. Para que a imposição dos limites de recursos funcione, todos os pacotes do código dentro de um pacote de serviço devem ter limites de memória especificados.

### <a name="using-application-parameters"></a>Utilização de parâmetros de aplicação

Ao especificar as definições de governança de recursos, é possível utilizar parâmetros de [aplicação](service-fabric-manage-multiple-environment-app-configuration.md) para gerir várias configurações de aplicações. O exemplo que se segue mostra a utilização dos parâmetros da aplicação:

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

Neste exemplo, os valores dos parâmetros padrão são definidos para o ambiente de produção, onde cada Pacote de Serviço obteria 4 núcleos e 2 GB de memória. É possível alterar valores predefinidos com ficheiros de parâmetros de aplicação. Neste exemplo, um ficheiro de parâmetros pode ser utilizado para testar a aplicação localmente, onde obteria menos recursos do que na produção:

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
> Especificação da governação dos recursos com parâmetros de aplicação está disponível a partir da versão 6.1 do Tecido de Serviço.<br>
>
> Quando os parâmetros de aplicação são utilizados para especificar a governação dos recursos, o Tecido de Serviço não pode ser desclassificado para uma versão anterior à versão 6.1.

## <a name="enforcing-the-resource-limits-for-user-services"></a>Aplicação dos limites de recursos para os serviços de utilização

Ao aplicar a governação de recursos aos seus serviços de Fabricação de Serviço garante que esses serviços recenseados por recursos não podem exceder a sua quota de recursos, muitos utilizadores ainda precisam de executar alguns dos seus serviços de Fabricação de Serviço em modo desgovernado. Quando se utilizam serviços de tecido de serviço não governados, é possível deparar-se com situações em que os serviços "fugitivos" não governados consomem todos os recursos disponíveis nos nós do Service Fabric, o que pode levar a questões sérias como:

* Fome de recursos de outros serviços em funcionamento nos nós (incluindo serviços de sistema de tecido de serviço)
* Nódosos acabando em estado insalubre
* APIs de gestão de cluster sem resposta de tecido sem resposta

Para evitar que estas situações ocorram, o Service Fabric permite-lhe impor os limites de *recursos para todos os serviços* de utilizador do Service Fabric que funcionam no nó (governados e não governados) para garantir que os serviços de utilizador nunca utilizarão mais do que a quantidade especificada de recursos. Isto é conseguido definindo o valor para o enforceUserServiceMetricCapacities config na secção PlacementAndLoadBalanceBalance do ClusterManifest para verdade. Esta definição é desligada por defeito.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

Observações adicionais:

* A aplicação do limite `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` de recursos apenas se aplica às métricas e recursos
* A aplicação do limite de recursos só funciona se as capacidades do nó para as métricas de recursos estiverem disponíveis para o Tecido de Serviço, seja através de mecanismo de deteção automática, quer através de utilizadores que especifiquem manualmente as capacidades do nó (como explicado na configuração do Cluster para permitir a secção de governação de [recursos).](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance)Se as capacidades do nó não estiverem configuradas, a capacidade de aplicação do limite de recursos não pode ser utilizada, uma vez que o Service Fabric não pode saber quantos recursos reservar para os serviços de utilizador.O Serviço Tecido emitirá um aviso de saúde se "EnforceUserServiceMetricCapacities" for verdadeiro, mas as capacidades do nó não estão configuradas.

## <a name="other-resources-for-containers"></a>Outros recursos para contentores

Além do CPU e da memória, é possível especificar outros limites de recursos para os contentores. Estes limites são especificados ao nível da embalagem de código e são aplicados quando o recipiente é iniciado. Ao contrário do CPU e da memória, o Cluster Resource Manager não está ciente destes recursos, e não fará quaisquer verificações de capacidade ou equilíbrio de carga para eles.

* *MemorySwapInMB*: A quantidade de memória de troca que um recipiente pode utilizar.
* *MemoryReservationInMB*: O limite suave para a governação da memória que é aplicado apenas quando a contenção da memória é detetada no nó.
* *CpuPercent*: A percentagem de CPU que o recipiente pode utilizar. Se forem especificados limites de CPU para o pacote de serviços, este parâmetro é efetivamente ignorado.
* *Máximas*: O IOPS máximo que um recipiente pode utilizar (ler e escrever).
* *MaximumIOBytesps*: O IO máximo (bytes por segundo) que um recipiente pode usar (ler e escrever).
* *BlockIOWeight*: O peso do bloco IO para outros recipientes.

Estes recursos podem ser combinados com CPU e memória. Aqui está um exemplo de como especificar recursos adicionais para os contentores:

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

* Para saber mais sobre cluster Resource Manager, leia [Introdução do gestor de recursos de cluster de tecido de serviço.](service-fabric-cluster-resource-manager-introduction.md)
* Para saber mais sobre o modelo de aplicação, pacotes de serviçoe pacotes de códigos -- e como as réplicas mapeiam para eles -- leia [Model uma aplicação em Tecido de Serviço](service-fabric-application-model.md).
