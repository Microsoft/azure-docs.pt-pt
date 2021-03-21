---
title: Governação de recursos para contentores e serviços
description: O Azure Service Fabric permite especificar pedidos de recursos e limites para serviços que executam como processos ou contentores.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: d760766870c8c2be0a2d2384f6d012b75bc92fbd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735663"
---
# <a name="resource-governance"></a>Gestão de recursos

Quando você está executando vários serviços no mesmo nó ou cluster, é possível que um serviço possa consumir mais recursos, esfomeando outros serviços no processo. Este problema é referido como o problema do "vizinho barulhento". O Azure Service Fabric permite ao desenvolvedor controlar este comportamento especificando pedidos e limites por serviço para limitar o uso de recursos.

> Antes de prosseguir com este artigo, recomendamos que se familiarize com o modelo de [aplicação de Tecido de Serviço][application-model-link] e o modelo de [hospedagem do Tecido de Serviço.][hosting-model-link]
>

## <a name="resource-governance-metrics"></a>Métricas de governação de recursos

A governação de recursos é suportada no Service Fabric de acordo com o [pacote de serviços.][application-model-link] Os recursos atribuídos ao pacote de serviços podem ser ainda divididos entre pacotes de código. O Service Fabric suporta a CPU e a governação da memória por pacote de serviço, com [duas métricas incorporadas:](service-fabric-cluster-resource-manager-metrics.md)

* *CPU* (nome `servicefabric:/_CpuCores` métrico): um núcleo lógico disponível na máquina hospedeira. Todos os núcleos em todos os nós são ponderados da mesma forma.

* *Memória* (nome `servicefabric:/_MemoryInMB` métrico): A memória é expressa em megabytes, e mapeia para a memória física que está disponível na máquina.

Para estas duas métricas, [o Cluster Resource Manager (CRM)][cluster-resource-manager-description-link] rastreia a capacidade total do cluster, a carga em cada nó no cluster e os restantes recursos no cluster. Estas duas métricas são equivalentes a qualquer outro utilizador ou métrica personalizada. Todas as funcionalidades existentes podem ser utilizadas com eles:

* O cluster pode ser [equilibrado](service-fabric-cluster-resource-manager-balancing.md) de acordo com estas duas métricas (comportamento padrão).
* O cluster pode ser [desfragmentado de](service-fabric-cluster-resource-manager-defragmentation-metrics.md) acordo com estas duas métricas.
* Ao [descrever um cluster, a][cluster-resource-manager-description-link]capacidade tamponada pode ser definida para estas duas métricas.

> [!NOTE]
> [O relatório dinâmico da carga](service-fabric-cluster-resource-manager-metrics.md) não é suportado para estas métricas; cargas para estas métricas são definidas no tempo de criação.

## <a name="resource-governance-mechanism"></a>Mecanismo de governação de recursos

A partir da versão 7.2, o tempo de funcionamento do Service Fabric suporta a especificação de pedidos e limites para CPU e recursos de memória.

> [!NOTE]
> As versões de execução do tecido de serviço com mais de 7.2 suportam apenas um modelo em que um único valor serve tanto como **pedido** e **limite** para um determinado recurso (CPU ou memória). Isto é descrito como a especificação **RequestsOnly** neste documento.

* *Pedidos:* Os valores de CPU e de pedido de memória representam as cargas utilizadas pelo [Cluster Resource Manager (CRM)][cluster-resource-manager-description-link] para as `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` métricas e métricas. Por outras palavras, a CRM considera que o consumo de recursos de um serviço é igual aos valores de pedido e utiliza estes valores ao tomar decisões de colocação.

* *Limites:* Os valores-limite da CPU e da Memória representam os limites reais de recursos aplicados quando um processo ou um recipiente é ativado num nó.

O Tecido de Serviço permite **pedidosOnly, LimitsOnly** e ambas as especificações **de Pedidos ELimits** para CPU e memória.
* Quando a especificação requestsonly é utilizada, o tecido de serviço também utiliza os valores de pedido como limites.
* Quando a especificação LimitsOnly é utilizada, o tecido de serviço considera os valores de pedido 0.
* Quando a especificação de Pedidos ELimits é utilizada, os valores-limite devem ser superiores ou iguais aos valores de pedido.

Para melhor compreender o mecanismo de governação de recursos, vamos olhar para um cenário de colocação de exemplo com uma especificação **RequestsOnly** para o recurso CPU (mecanismo de governação da memória é equivalente). Considere um nó com dois núcleos de CPU e dois pacotes de serviço que serão colocados nele. O primeiro pacote de serviço a ser colocado, é composto por apenas um pacote de código de contentor e especifica apenas um pedido de um núcleo de CPU. O segundo pacote de serviço a ser colocado, é composto por apenas um pacote de código baseado em processos e também especifica apenas um pedido de um núcleo de CPU. Uma vez que ambos os pacotes de serviço têm uma especificação RequestsOnly, os seus valores-limite são definidos para os seus valores de pedido.

1. Primeiro, o pacote de serviço à base de contentores que solicita um núcleo de CPU é colocado no nó. O tempo de funcionação ativa o recipiente e define o limite do CPU para um núcleo. O recipiente não poderá usar mais do que um núcleo.

2. Em seguida, o pacote de serviço baseado no processo solicitando um núcleo de CPU é colocado no nó. O tempo de funcionamento ativa o processo de serviço e define o seu limite de CPU para um núcleo.

Neste momento, a soma dos pedidos é igual à capacidade do nó. A CRM não colocará mais contentores ou processos de serviço com pedidos de CPU neste nó. No nó, um processo e um recipiente estão funcionando com um núcleo cada um e não se enfrentarão mutuamente para CPU.

Vamos agora revisitar o nosso exemplo com uma especificação **RequestsAndLimits.** Desta vez, o pacote de serviço à base de contentores especifica um pedido de um núcleo de CPU e um limite de dois núcleos de CPU. O pacote de serviço baseado em processos especifica tanto um pedido como um limite de um núcleo de CPU.
  1. Primeiro, o pacote de serviço à base de contentores é colocado no nó. O tempo de funcionação ativa o recipiente e define o limite do CPU em dois núcleos. O contentor não poderá usar mais de dois núcleos.
  2. Em seguida, o pacote de serviço baseado no processo é colocado no nó. O tempo de funcionamento ativa o processo de serviço e define o seu limite de CPU para um núcleo.

  Neste momento, a soma dos pedidos de CPU de pacotes de serviços colocados no nó é igual à capacidade da CPU do nó. A CRM não colocará mais contentores ou processos de serviço com pedidos de CPU neste nó. No entanto, no nó, a soma dos limites (dois núcleos para o recipiente + um núcleo para o processo) excede a capacidade de dois núcleos. Se o contentor e o processo rebentarem ao mesmo tempo, existe a possibilidade de contenção para o recurso CPU. Tal contenção será escamada pelo SO subjacente para a plataforma. Por exemplo, o contentor pode rebentar até dois núcleos de CPU, resultando na não garantia do pedido do processo de um núcleo de CPU.

> [!NOTE]
> Tal como ilustrado no exemplo anterior, os valores do pedido de CPU e memória **não conduzem à reserva de recursos num nó.** Estes valores representam o consumo de recursos que o Gestor de Recursos do Cluster considera ao tomar decisões de colocação. Os valores-limite representam os limites reais de recursos aplicados quando um processo ou um recipiente é ativado num nó.


Há algumas situações em que pode haver disputa pela CPU. Nestas situações, o processo e o contentor do nosso exemplo podem experimentar o problema ruidoso do vizinho:

* *Mistura de serviços e contentores governados e não governados*: Se um utilizador criar um serviço sem qualquer governação de recursos especificado, o tempo de execução vê-o como não consumindo recursos, podendo colocá-lo no nó no nosso exemplo. Neste caso, este novo processo consome efetivamente algum CPU à custa dos serviços que já estão a funcionar no nó. Há duas soluções para este problema. Ou não misture serviços governados e não governados no mesmo cluster, ou use restrições de [colocação](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) para que estes dois tipos de serviços não acabem no mesmo conjunto de nós.

* *Quando se inicia um outro processo no nó, fora do Service Fabric (por exemplo, um serviço de OS)*: Nesta situação, o processo fora do Service Fabric também defende a CPU com os serviços existentes. A solução para este problema é configurar corretamente as capacidades dos nónónios para ter em conta as despesas gerais do SO, como mostra a secção seguinte.

* *Quando os pedidos não são iguais a limites*: Como descrito no exemplo de Pedidos ELimits anteriormente, os pedidos não conduzem à reserva de recursos num nó. Quando um serviço com limites superiores aos pedidos é colocado num nó, pode consumir recursos (se disponíveis) até aos limites. Nesses casos, outros serviços no nó podem não ser capazes de consumir recursos até aos seus valores de pedido.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Configuração do cluster para permitir a governação de recursos

Quando um nó começa e se junta ao cluster, o Service Fabric deteta a quantidade disponível de memória e o número disponível de núcleos, e, em seguida, define as capacidades do nó para esses dois recursos.

Para deixar espaço tampão para o sistema operativo, e para outros processos que possam estar a ser em execução no nó, o Service Fabric utiliza apenas 80% dos recursos disponíveis no nó. Esta percentagem é configurável, e pode ser alterada no manifesto do cluster.

Aqui está um exemplo de como instruir o Service Fabric a usar 50% do CPU disponível e 70% da memória disponível:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Para a maioria dos clientes e cenários, a deteção automática de capacidades de nó para CPU e memória é a configuração recomendada (a deteção automática é ativada por predefinição). No entanto, se necessitar de uma configuração manual completa das capacidades dos nó, pode configurá-las por tipo de nó utilizando o mecanismo para descrever os nós no cluster. Aqui está um exemplo de como configurar o tipo de nó com quatro núcleos e 2 GB de memória:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Quando a deteção automática dos recursos disponíveis está ativada e as capacidades dos nóis são definidas manualmente no manifesto do cluster, o Service Fabric verifica se o nó tem recursos suficientes para suportar a capacidade que o utilizador definiu:

* Se as capacidades de nó definidas no manifesto forem inferiores ou iguais aos recursos disponíveis no nó, então o Tecido de Serviço utiliza as capacidades especificadas no manifesto.

* Se as capacidades de nó que são definidas no manifesto forem superiores aos recursos disponíveis, o Service Fabric utiliza os recursos disponíveis como capacidades de nó.

A deteção automática dos recursos disponíveis pode ser desligada se não for necessário. Para desligá-lo, altere a seguinte definição:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Para um melhor desempenho, a seguinte definição também deve ser ligada no manifesto do cluster:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> Começando pela versão 7.0 do Service Fabric, atualizámos a regra de como as capacidades de recursos de nó são calculadas nos casos em que o utilizador fornece manualmente os valores das capacidades de recursos de nó. Vamos considerar o seguinte cenário:
>
> * Há um total de 10 núcleos de CPU no nó
> * A SF está configurada para utilizar 80% do total de recursos para os serviços do utilizador (definição predefinida), o que deixa um tampão de 20% para os outros serviços em execução no nó (incl. Serviços de sistema de tecido de serviço)
> * O utilizador decide anular manualmente a capacidade de recurso do nó para a métrica dos núcleos da CPU, e define-a em 5 núcleos
>
> Alteramos a regra sobre a forma como a capacidade disponível para os serviços de utilizador do Service Fabric é calculada da seguinte forma:
>
> * Antes do Tecido de Serviço 7.0, a capacidade disponível para os serviços dos utilizadores seria calculada para **5 núcleos** (o tampão de capacidade de 20% é ignorado)
> * A partir do Service Fabric 7.0, a capacidade disponível para os serviços dos utilizadores seria calculada para **4 núcleos** (o tampão de capacidade de 20% não é ignorado)

## <a name="specify-resource-governance"></a>Especificar a governação de recursos

Os pedidos e limites de governação dos recursos são especificados no manifesto de aplicação (secção ServiceManifestImport). Vejamos alguns exemplos:

**Exemplo 1: Pedidos Especificação deond**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1024" />
    </Policies>
  </ServiceManifestImport>
```

Neste exemplo, o `CpuCores` atributo é utilizado para especificar um pedido de 1 núcleo CPU para **ServicePackageA**. Uma vez que o limite do CPU `CpuCoresLimit` (atributo) não é especificado, o Tecido de Serviço também utiliza o valor de pedido especificado de 1 núcleo como limite de CPU para o pacote de serviço.

**O ServicePackageA** só será colocado num nó onde a capacidade restante do CPU após subtrair a **soma dos pedidos de CPU de todos os pacotes de serviços colocados nesse nó** seja superior ou igual a 1 núcleo. No nó, o pacote de serviço será limitado a um núcleo. O pacote de serviço contém dois pacotes de código **(CodeA1** e **CodeA2**), e ambos especificam o `CpuShares` atributo. A proporção de CpuShares 512:256 é utilizada para calcular os limites do CPU para os pacotes de código individuais. Assim, o CodeA1 será limitado a dois terços de um núcleo, e o CodeA2 será limitado a um terço de um núcleo. Se os CpuShares não forem especificados para todos os pacotes de código, o Service Fabric divide o limite de CPU igualmente entre eles.

Embora os CpuShares especificados para pacotes de código representem a sua proporção relativa do limite global do CPU do pacote de serviços, os valores de memória dos pacotes de código são especificados em termos absolutos. Neste exemplo, o `MemoryInMB` atributo é utilizado para especificar pedidos de memória de 1024 MB tanto para o CodeA1 como para o CodeA2. Uma vez que o limite de memória `MemoryInMBLimit` (atributo) não é especificado, o Tecido de Serviço também utiliza os valores de pedido especificados como limites para as embalagens de código. O pedido de memória (e limite) para o pacote de serviço é calculado como a soma dos valores de pedido de memória (e limite) dos seus pacotes de código constitutivo. Assim, para **o ServicePackageA,** o pedido de memória e o limite são calculados como 2048 MB.

**O ServicePackageA** só será colocado num nó onde a capacidade de memória remanescente após subtrair a **soma dos pedidos de memória de todos os pacotes de serviço colocados nesse nó** seja superior ou igual a 2048 MB. No nó, ambos os pacotes de código serão limitados a 1024 MB de memória cada. As embalagens de código (contentores ou processos) não poderão alocar mais memória do que este limite, e tentar fazê-lo resultará em exceções fora da memória.

**Exemplo 2: Limitações Especificação Apenas**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCoresLimit="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMBLimit="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMBLimit="1024" />
    </Policies>
  </ServiceManifestImport>
```
Este exemplo utiliza `CpuCoresLimit` e `MemoryInMBLimit` atribui, que só estão disponíveis nas versões SF 7.2 e posteriormente. O atributo CpuCoresLimit é utilizado para especificar um limite de CPU de 1 núcleo para **ServicePackageA**. Uma vez que o pedido de CPU `CpuCores` (atributo) não é especificado, considera-se que é 0. `MemoryInMBLimit` o atributo é utilizado para especificar os limites de memória de 1024 MB para o CodeA1 e o CodeA2 e uma vez que os pedidos `MemoryInMB` (atributo) não são especificados, são considerados 0. O pedido de memória e o limite para **o ServicePackageA** são assim calculados como 0 e 2048, respectivamente. Uma vez que tanto o CPU como os pedidos de memória para **o ServicePackageA** são 0, não apresenta nenhuma carga para a CRM considerar para colocação, para as `servicefabric:/_CpuCores` métricas e para as `servicefabric:/_MemoryInMB` métricas. Assim, do ponto de vista da governação dos recursos, **o ServicePackageA** pode ser colocado em qualquer **nó, independentemente da capacidade remanescente.** Semelhante ao exemplo 1, no nó, o CodeA1 será limitado a dois terços de um núcleo e 1024 MB de memória, e o CodeA2 será limitado a um terço de um núcleo e 1024 MB de memória.

**Exemplo 3: Pedidos ELimits especificação**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1" CpuCoresLimit="2"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" MemoryInMBLimit="3072" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="2048" MemoryInMBLimit="4096" />
    </Policies>
  </ServiceManifestImport>
```
Com base nos dois primeiros exemplos, este exemplo demonstra especificar tanto os pedidos como os limites para a CPU e a memória. **O ServicePackageA** tem CPU e pedidos de memória de 1 core e 3072 (1024 + 2048) MB, respectivamente. Só pode ser colocado num nó que tenha pelo menos 1 núcleo (e 3072 MB) de capacidade deixada após subtrair a soma de todos os pedidos de CPU (e memória) de todos os pacotes de serviço que são colocados no nó a partir da capacidade total do CPU (e memória) do nó. No nó, o CodeA1 será limitado a dois terços de 2 núcleos e 3072 MB de memória, enquanto o CodeA2 será limitado a um terço de 2 núcleos e 4096 MB de memória.

### <a name="using-application-parameters"></a>Usando parâmetros de aplicação

Ao especificar as definições de governação de recursos, é possível utilizar [parâmetros de aplicação](service-fabric-manage-multiple-environment-app-configuration.md) para gerir várias configurações de aplicações. O exemplo a seguir mostra a utilização dos parâmetros de aplicação:

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

Neste exemplo, os parâmetros padrão são definidos para o ambiente de produção, onde cada Pacote de Serviço obteria 4 núcleos e 2 GB de memória. É possível alterar valores predefinidos com ficheiros de parâmetros de aplicação. Neste exemplo, um ficheiro de parâmetro pode ser utilizado para testar a aplicação localmente, onde obteria menos recursos do que na produção:

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
> Especificar a governação dos recursos com os parâmetros de aplicação está disponível a partir da versão 6.1 do Service Fabric.<br>
>
> Quando os parâmetros de aplicação são utilizados para especificar a governação dos recursos, o Tecido de Serviço não pode ser desclassificado para uma versão antes da versão 6.1.

## <a name="enforcing-the-resource-limits-for-user-services"></a>Aplicação dos limites de recursos para os serviços dos utilizadores

Ao aplicar a governação de recursos aos seus serviços de Service Fabric garante que esses serviços governados por recursos não podem exceder a sua quota de recursos, muitos utilizadores ainda precisam de executar alguns dos seus serviços de Service Fabric em modo não governado. Ao utilizar serviços de tecido de serviço não governados, é possível encontrar situações em que serviços "fugitivos" não governados consomem todos os recursos disponíveis nos nós do Tecido de Serviço, o que pode levar a questões sérias como:

* Fome de recursos de outros serviços em execução nos nós (incluindo serviços de sistema de tecido de serviço)
* Nódes acabando num estado insalubre
* APIs de gestão de clusters de tecido de serviço sem resposta

Para evitar que estas situações ocorram, o Service Fabric permite-lhe impor os limites de *recursos para todos os serviços de utilizador do Service Fabric que executam no nó* (governado e não governado) para garantir que os serviços de utilizador nunca utilizarão mais do que a quantidade especificada de recursos. Isto é conseguido definindo o valor para as CapacidadesMétricas do Serviço De Execução config na secção PlacementAndLoadBalancing do ClusterManifest para ser verdadeiro. Esta definição é desligada por defeito.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

Observações adicionais:

* A aplicação do limite de recursos só se aplica às `servicefabric:/_CpuCores` métricas e aos `servicefabric:/_MemoryInMB` recursos
* A aplicação do limite de recursos só funciona se as capacidades de nó para as métricas de recursos estiverem disponíveis para o Tecido de Serviço, seja através de mecanismos de deteção automática, quer através de utilizadores especificando manualmente as capacidades do nó (conforme explicado na configuração do Cluster para permitir a secção [de governação dos recursos).](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance) Se as capacidades do nó não forem configuradas, a capacidade de aplicação do limite de recursos não pode ser utilizada, uma vez que o Service Fabric não pode saber quanto recursos reserva para os serviços do utilizador. O Service Fabric emitirá um aviso sanitário se "Executar as CapacidadesMétricas do Serviço De Serviço" for verdadeiro, mas as capacidades dos nóns não estiverem configuradas.

## <a name="other-resources-for-containers"></a>Outros recursos para contentores

Além do CPU e da memória, é possível especificar outros limites de recursos para os contentores. Estes limites são especificados ao nível da embalagem de código e são aplicados quando o recipiente é iniciado. Ao contrário do CPU e da memória, o Cluster Resource Manager não está ciente destes recursos, e não fará nenhuma verificação de capacidade ou carregamento de equilíbrio para eles.

* *MemorySwapInMB*: A quantidade de memória de troca que um recipiente pode utilizar.
* *MemoryReservationInMB*: O limite suave para a governação da memória que é aplicado apenas quando a contenção da memória é detetada no nó.
* *CpuPercent*: A percentagem de CPU que o recipiente pode utilizar. Se os pedidos ou limites da CPU forem especificados para o pacote de serviços, este parâmetro é efetivamente ignorado.
* *MáximoIOps*: O IOPS máximo que um recipiente pode usar (ler e escrever).
* *MáximoIOBytesps*: O IO máximo (bytes por segundo) que um recipiente pode usar (ler e escrever).
* *BlockIOWeight*: O peso do bloco IO para relativamente a outros recipientes.

Estes recursos podem ser combinados com CPU e memória. Aqui está um exemplo de como especificar recursos adicionais para contentores:

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

* Para saber mais sobre o Cluster Resource Manager, leia [Introdução ao gestor de recursos do cluster de tecido de serviço.](service-fabric-cluster-resource-manager-introduction.md)
* Para saber mais sobre o modelo de aplicação, pacotes de serviço e pacotes de código -- e como as réplicas mapeam para eles -- leia [modelo uma aplicação em Tecido de Serviço][application-model-link].

<!-- Links -->
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[cluster-resource-manager-description-link]: service-fabric-cluster-resource-manager-cluster-description.md
