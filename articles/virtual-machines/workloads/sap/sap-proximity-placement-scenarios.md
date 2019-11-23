---
title: Grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP | Microsoft Docs
description: Descreve os cenários de implantação do SAP com grupos de posicionamento de proximidade do Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: deffcb81a4f66783fedc89c3e21ea46b15ad1c64
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720003"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP
Os aplicativos SAP baseados na arquitetura SAP NetWeaver ou SAP S/4HANA são sensíveis à latência de rede entre a camada de aplicativo SAP e a camada de banco de dados SAP. Essa sensibilidade é o resultado da maior parte da lógica de negócios em execução na camada de aplicativo. Como a camada de aplicativo SAP executa a lógica de negócios, ela emite consultas para a camada de banco de dados em uma alta frequência, a uma taxa de milhares ou dezenas de milhares por segundo. Na maioria dos casos, a natureza dessas consultas é simples. Geralmente, eles podem ser executados na camada de banco de dados em 500 microssegundos ou menos.

O tempo gasto na rede para enviar tal consulta da camada de aplicativo para a camada de banco de dados e receber o retorno do conjunto de resultados tem um grande impacto no tempo necessário para executar processos de negócios. Essa sensibilidade à latência de rede é o motivo pelo qual você precisa obter a latência de rede ideal nos projetos de implantação do SAP. Consulte [SAP Note #1100926-perguntas frequentes: desempenho de rede](https://launchpad.support.sap.com/#/notes/1100926/E) para obter diretrizes sobre como classificar a latência de rede.

Em muitas regiões do Azure, o número de data centers cresceu. Esse crescimento também foi disparado pela introdução do Zonas de Disponibilidade. Ao mesmo tempo, os clientes, especialmente para sistemas SAP avançados, estão usando SKUs de VM mais especiais na família série M ou em instâncias grandes HANA. Esses tipos de máquina virtual do Azure não estão disponíveis em todos os data centers em uma região específica do Azure. Por causa desses dois as tendências, os clientes tiveram uma latência de rede que não está no intervalo ideal. Em alguns casos, essa latência resulta em um desempenho abaixo do ideal de seus sistemas SAP.

Para evitar esses problemas, o Azure oferece [grupos de posicionamento de proximidade](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). Essa nova funcionalidade já foi usada para implantar vários sistemas SAP. Para obter restrições sobre os grupos de posicionamento de proximidade, consulte o artigo mencionado no início deste parágrafo. Este artigo aborda os cenários SAP nos quais os grupos de posicionamento de proximidade do Azure podem ou devem ser usados.

## <a name="what-are-proximity-placement-groups"></a>O que são grupos de posicionamento de proximidade? 
Um grupo de posicionamento de proximidade do Azure é um constructo lógico. Quando um é definido, ele é associado a uma região do Azure e a um grupo de recursos do Azure. Quando as VMs são implantadas, um grupo de posicionamento de proximidade é referenciado por:

- A primeira VM do Azure implantada no datacenter. Você pode considerar a primeira máquina virtual como uma "VM de escopo" implantada em um datacenter com base em algoritmos de alocação do Azure que são eventualmente combinados com definições de usuário para uma zona de disponibilidade específica.
- Todas as VMs subsequentes implantadas que fazem referência ao grupo de posicionamento de proximidade, para colocar todas as VMs do Azure implantadas subsequentemente no mesmo datacenter da primeira máquina virtual.

> [!NOTE]
> Se não houver nenhum hardware de host implantado que possa executar um tipo de VM específico no datacenter em que a primeira VM foi colocada, a implantação do tipo de VM solicitado não terá êxito. Você receberá uma mensagem de falha.

Um único [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) pode ter vários grupos de posicionamento de proximidade atribuídos a ele. Mas um grupo de posicionamento de proximidade pode ser atribuído a apenas um grupo de recursos do Azure.

Ao usar grupos de posicionamento de proximidade, tenha em mente estas considerações:

- Quando você visa o desempenho ideal para seu sistema SAP e se limita a um único datacenter do Azure para o sistema usando grupos de posicionamento de proximidade, talvez não seja possível combinar todos os tipos de famílias de VMs dentro do grupo de posicionamento. Essas limitações ocorrem porque o hardware do host necessário para executar um determinado tipo de VM pode não estar presente no datacenter no qual a "VM com escopo" do grupo de posicionamento foi implantada.
- Durante o ciclo de vida de tal sistema SAP, você pode ser forçado a mover o sistema para outro datacenter. Essa mudança pode ser necessária se você decidir que a camada do DBMS do HANA de expansão deve, por exemplo, mover de quatro nós para 16 nós, e não há capacidade suficiente para obter 12 VMs adicionais do tipo usado no datacenter.
- Devido ao encerramento de hardware, a Microsoft pode criar capacidades para um tipo de VM usado em um datacenter diferente, em vez daquele que você usou inicialmente. Nesse cenário, talvez seja necessário mover todas as VMs do grupo de posicionamento de proximidade para outro datacenter.

## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Grupos de posicionamento de proximidade com sistemas SAP que usam apenas VMs do Azure
A maioria das implantações do sistema SAP NetWeaver e S/4HANA no Azure não usam [instâncias grandes do Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). Para implantações que não usam instâncias grandes do HANA, é importante fornecer um desempenho ideal entre a camada do aplicativo SAP e a camada do DBMS. Para fazer isso, defina um grupo de posicionamento de proximidade do Azure apenas para o sistema.

Na maioria das implantações de clientes, os clientes criam um único [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) para sistemas SAP. Nesse caso, há uma relação um-para-um entre, por exemplo, o grupo de recursos do sistema ERP de produção e seu grupo de posicionamento de proximidade. Em outros casos, os clientes organizam seus grupos de recursos horizontalmente e coletam todos os sistemas de produção em um único grupo de recursos. Nesse caso, você teria uma relação um-para-muitos entre o grupo de recursos para sistemas SAP de produção e vários grupos de posicionamento de proximidade para o SAP ERP de produção, SAP BW e assim por diante.

Evite agrupar vários sistemas de produção ou não produção SAP em um único grupo de posicionamento de proximidade. Quando um pequeno número de sistemas SAP ou um sistema SAP e alguns aplicativos ao redor precisam ter comunicação de rede de baixa latência, você pode considerar mover esses sistemas para um grupo de posicionamento de proximidade. Você deve evitar pacotes de sistemas porque quanto mais sistemas você agrupa em um grupo de posicionamento de proximidade, maior a probabilidade:

- Que você precisa de um tipo de VM que não pode ser executado no datacenter específico ao qual o grupo de posicionamento de proximidade foi definido como escopo.
- Esses recursos de VMs não convencionais, como VMs da série M, podem eventualmente ser não cumpridos quando você precisar de mais porque você está adicionando software a um grupo de posicionamento de proximidade ao longo do tempo.

Veja o que a configuração ideal, conforme descrito, é semelhante a:

![Grupos de posicionamento de proximidade somente com VMs do Azure](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

Nesse caso, os sistemas SAP únicos são agrupados em um grupo de recursos cada, com um grupo de posicionamento de proximidade cada. Não há nenhuma dependência de se usar as configurações de expansão ou expansão de DBMS do HANA.

## <a name="proximity-placement-groups-and-hana-large-instances"></a>Grupos de posicionamento de proximidade e instâncias grandes do HANA
Se alguns dos seus sistemas SAP dependem de [instâncias grandes Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) para a camada de aplicativo, você pode experimentar melhorias significativas na latência de rede entre a unidade de instâncias grandes do Hana e as VMs do Azure quando estiver usando as unidades de instâncias grandes Hana implantadas na [revisão 4 linhas ou carimbos](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Uma melhoria é que as unidades do SAP HANA em instâncias grandes, à medida que são implantadas, são implementadas com um grupo de posicionamento de proximidade. Você pode usar esse grupo de posicionamento de proximidade para implantar suas VMs de camada de aplicativo. Como resultado, essas VMs serão implantadas no mesmo datacenter que hospeda sua unidade de instâncias grandes do HANA.

Para determinar se a unidade do HANA em instâncias grandes está implantada em uma linha ou carimbo de revisão 4, consulte o artigo [controle de instâncias grandes do Azure Hana por meio de portal do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit). Na visão geral dos atributos de sua unidade do HANA em instâncias grandes, você também pode determinar o nome do grupo de posicionamento de proximidade porque ele foi criado quando a unidade do HANA em instâncias grandes foi implantada. O nome que aparece na visão geral de atributos é o nome do grupo de posicionamento de proximidade no qual você deve implantar suas VMs de camada de aplicativo.

Em comparação com os sistemas SAP que usam apenas máquinas virtuais do Azure, quando você usa instâncias grandes do HANA, você tem menos flexibilidade para decidir quantos [grupos de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) devem ser usados. Todas as unidades de instâncias grandes do HANA de um [locatário do Hana em instâncias grandes](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) são agrupadas em um único grupo de recursos, conforme descrito [neste artigo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal). A menos que você implante em locatários diferentes para separar, por exemplo, sistemas de produção e de não produção ou outros sistemas, todas as suas unidades de instâncias grandes do HANA serão implantadas em um locatário do HANA em instâncias grandes. Esse locatário tem uma relação um-para-um com um grupo de recursos. Mas um grupo de posicionamento de proximidade separado será definido para cada uma das unidades individuais.

Como resultado, as relações entre grupos de recursos do Azure e grupos de posicionamento de proximidade para um único locatário serão mostradas aqui:

![Grupos de posicionamento de proximidade e instâncias grandes do HANA](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>Exemplo de implantação com grupos de posicionamento de proximidade
A seguir, alguns comandos do PowerShell que você pode usar para implantar suas VMs com grupos de posicionamento de proximidade do Azure.

A primeira etapa, depois de entrar no [Azure cloud Shell](https://azure.microsoft.com/features/cloud-shell/), é verificar se você está na assinatura do Azure que deseja usar para a implantação:

<pre><code>
Get-AzureRmContext
</code></pre>

Se você precisar alterar para uma assinatura diferente, poderá fazer isso executando este comando:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Crie um novo grupo de recursos do Azure executando este comando:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

Crie o novo grupo de posicionamento de proximidade executando este comando:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

Implante sua primeira VM no grupo de posicionamento de proximidade usando um comando como este:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

O comando anterior implanta uma VM baseada no Windows. Após a implantação da VM ser realizada com sucesso, o escopo do datacenter do grupo de posicionamento de proximidade é definido na região do Azure. Todas as implantações de VM subsequentes que fazem referência ao grupo de posicionamento de proximidade, conforme mostrado no comando anterior, serão implantadas no mesmo datacenter do Azure, desde que o tipo de VM possa ser hospedado em hardware colocado no datacenter e a capacidade para esse tipo de VM seja Há.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Combinar conjuntos de disponibilidade e Zonas de Disponibilidade com grupos de posicionamento de proximidade
Uma das desvantagens de usar Zonas de Disponibilidade para implantações de sistema SAP é que você não pode implantar a camada de aplicativo SAP usando conjuntos de disponibilidade dentro da zona específica. Você deseja que a camada de aplicativo SAP seja implantada nas mesmas zonas que a camada DBMS. Não há suporte para a referência a uma zona de disponibilidade e um conjunto de disponibilidade ao implantar uma única VM. Então, anteriormente, você foi forçado a implantar sua camada de aplicativo referenciando uma zona. Você perdeu a capacidade de garantir que as VMs da camada de aplicativo estivessem espalhadas em diferentes domínios de falha e atualização.

Ao usar grupos de posicionamento de proximidade, você pode ignorar essa restrição. Esta é a sequência de implantação:

- Crie um grupo de posicionamento de proximidade.
- Implante sua VM de âncora, geralmente o servidor DBMS, referenciando uma zona de disponibilidade.
- Crie um conjunto de disponibilidade que faça referência ao grupo de proximidade do Azure. (Consulte o comando mais adiante neste artigo.)
- Implante as VMs da camada de aplicativo referenciando o conjunto de disponibilidade e o grupo de posicionamento de proximidade.

Em vez de implantar a primeira VM, conforme demonstrado na seção anterior, você faz referência a uma zona de disponibilidade e ao grupo de posicionamento de proximidade ao implantar a VM:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

Uma implantação bem-sucedida dessa máquina virtual hospedaria a instância do banco de dados do sistema SAP em uma zona de disponibilidade. O escopo do grupo de posicionamento de proximidade é fixado em um dos data centers que representam a zona de disponibilidade que você definiu.

Suponha que você implante as VMs de serviços centrais da mesma maneira que as VMs do DBMS, fazendo referência à mesma zona ou zonas e aos mesmos grupos de posicionamento de proximidade. Na próxima etapa, você precisa criar os conjuntos de disponibilidade que deseja usar para a camada de aplicativo do seu sistema SAP.

Você precisa definir e criar o grupo de posicionamento de proximidade. O comando para criar o conjunto de disponibilidade requer uma referência adicional para a ID do grupo de posicionamento de proximidade (não o nome). Você pode obter a ID do grupo de posicionamento de proximidade usando este comando:

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

Ao criar o conjunto de disponibilidade, você precisa considerar parâmetros adicionais quando estiver usando discos gerenciados (padrão, a menos que especificado de outra forma) e grupos de posicionamento de proximidade:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

O ideal é que você use três domínios de falha. Mas o número de domínios de falha com suporte pode variar de região para região. Nesse caso, o número máximo de domínios de falha possíveis para as regiões específicas é dois. Para implantar suas VMs de camada de aplicativo, você precisa adicionar uma referência ao nome do conjunto de disponibilidade e ao nome do grupo de posicionamento de proximidade, conforme mostrado aqui:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

O resultado dessa implantação é:
- Uma camada DBMS e serviços centrais para seu sistema SAP localizado em uma zona de disponibilidade específica ou Zonas de Disponibilidade.
- Uma camada de aplicativo SAP que está localizada por meio de conjuntos de disponibilidade nos mesmos data centers do Azure que a VM DBMS ou VMs.

> [!NOTE]
> Como você implanta uma VM do DBMS em uma zona e a segunda VM do DBMS em outra zona para criar uma configuração de alta disponibilidade, você precisará de um grupo de posicionamento de proximidade diferente para cada uma das zonas. O mesmo é verdadeiro para qualquer conjunto de disponibilidade que você usar.

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>Mover um sistema existente para grupos de posicionamento de proximidade
Se você já tiver sistemas SAP implantados, talvez queira otimizar a latência de rede de alguns dos seus sistemas críticos e localizar a camada de aplicativo e a camada de DBMS no mesmo datacenter. Durante a visualização pública dos grupos de posicionamento de proximidade, você precisa excluir as VMs e criar novas para mover o sistema para os grupos de posicionamento de proximidade. No momento, você não pode apenas desligar as VMs e atribuí-las aos grupos de posicionamento de proximidade.


## <a name="next-steps"></a>Passos seguintes
Confira a documentação:

- [Cargas de trabalho do SAP no Azure: lista de verificação de planejamento e implantação](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [Versão prévia: implantar VMs em grupos de posicionamento de proximidade usando CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [Versão prévia: implantar VMs em grupos de posicionamento de proximidade usando o PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [Considerações sobre a implantação de DBMS de máquinas virtuais do Azure para cargas de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

