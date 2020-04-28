---
title: Grupos de colocação de proximidade azure para aplicações SAP Microsoft Docs
description: Descreve cenários de implantação do SAP com grupos de colocação de proximidade do Azure
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
ms.date: 01/17/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 01ce1599f86082aef3ff53d298cc53896074af66
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76277601"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Grupos de colocação de proximidade azure para a latência ideal da rede com aplicações SAP
As aplicações SAP baseadas na arquitetura SAP NetWeaver ou SAP S/4HANA são sensíveis à latência da rede entre o nível de aplicação SAP e o nível de base de dados SAP. Esta sensibilidade é o resultado da maior parte da lógica do negócio que funciona na camada de aplicação. Como a camada de aplicação SAP funciona a lógica do negócio, emite consultas ao nível de base de dados a uma alta frequência, a uma taxa de milhares ou dezenas de milhares por segundo. Na maioria dos casos, a natureza destas consultas é simples. Podem muitas vezes ser executados no nível de base de dados em 500 microsegundos ou menos.

O tempo gasto na rede para enviar essa consulta do nível de aplicação para o nível de base de dados e receber o resultado recuado tem um grande impacto no tempo que leva para executar processos de negócio. Esta sensibilidade à latência da rede é a razão pela qual precisa de alcançar a latência ideal da rede em projetos de implantação do SAP. Consulte [o SAP Note #1100926 - FAQ: Desempenho](https://launchpad.support.sap.com/#/notes/1100926/E) da rede para diretrizes sobre como classificar a latência da rede.

Em muitas regiões do Azure, o número de datacenters tem crescido. Este crescimento foi também desencadeado pela introdução de Zonas de Disponibilidade. Ao mesmo tempo, os clientes, especialmente para sistemas SAP topo de gama, estão a utilizar SKUs VM mais especiais na família Série M, ou HANA Large Instances. Estes tipos de máquinas virtuais Azure não estão disponíveis em todos os datacenters de uma região específica do Azure. Devido a estas duas tendências, os clientes experimentaram a latência da rede que não está na gama ideal. Em alguns casos, esta latência resulta num desempenho sub-óptimo dos seus sistemas SAP.

Para prevenir estes problemas, o Azure oferece grupos de [colocação de proximidade.](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) Esta nova funcionalidade já foi utilizada para implementar vários sistemas SAP. Para restrições aos grupos de colocação de proximidade, consulte o artigo referido no início deste parágrafo. Este artigo abrange os cenários SAP em que os grupos de colocação de proximidade do Azure podem ou devem ser utilizados.

## <a name="what-are-proximity-placement-groups"></a>O que são grupos de colocação de proximidade? 
Um grupo de colocação de proximidade Azure é uma construção lógica. Quando se é definido, está ligado a uma região azure e a um grupo de recursos Azure. Quando os VMs são implantados, um grupo de colocação de proximidade é referenciado por:

- O primeiro VM Azure implantado no datacenter. Pode pensar na primeira máquina virtual como um "VM de alcance" que é implantado num datacenter baseado em algoritmos de alocação azure que são eventualmente combinados com definições de utilizador para uma Zona de Disponibilidade específica.
- Todos os VMs subsequentes implantaram que referenciam o grupo de colocação de proximidade, para colocar todos os VMs Azure posteriormente implantados no mesmo datacenter que a primeira máquina virtual.

> [!NOTE]
> Se não houver nenhum hardware de anfitrião implementado que possa executar um tipo de VM específico no datacenter onde o primeiro VM foi colocado, a implementação do tipo VM solicitado não será bem sucedida. Vaireceber uma mensagem de falhanço.

Um único grupo de [recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) pode ter múltiplos grupos de colocação de proximidade atribuídos a ele. Mas um grupo de colocação de proximidade pode ser atribuído a apenas um grupo de recursos Azure.

Quando utilizar grupos de colocação de proximidade, tenha em mente estas considerações:

- Quando procura um desempenho ideal para o seu sistema SAP e se limite a um único datacenter Azure para o sistema, utilizando grupos de colocação de proximidade, poderá não ser capaz de combinar todos os tipos de famílias VM dentro do grupo de colocação. Estas limitações ocorrem porque o hardware do hospedeiro que é necessário para executar um determinado tipo de VM pode não estar presente no datacenter ao qual foi implementado o "VM de âmbito" do grupo de colocação.
- Durante o ciclo de vida de um sistema SAP deste tipo, pode ser forçado a mover o sistema para outro datacenter. Este movimento pode ser necessário se decidir que a sua camada DE DBMS HANA deve, por exemplo, passar de quatro nós para 16 nós, e não há capacidade suficiente para obter mais 12 VMs do tipo que usou no datacenter.
- Devido ao desmantelamento de hardware, a Microsoft pode acumular capacidades para um tipo VM que usou num centro de dados diferente, em vez do que inicialmente utilizou. Nesse cenário, poderá ser necessário mover todos os VMs do grupo de colocação de proximidade para outro datacenter.

## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Grupos de colocação de proximidade com sistemas SAP que usam apenas VMs Azure
A maioria das implementações do sistema SAP NetWeaver e S/4HANA no Azure não usam [grandes instâncias HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). Para implementações que não usam as Grandes Instâncias HANA, é importante proporcionar um desempenho ideal entre a camada de aplicação SAP e o nível DBMS. Para tal, defina um grupo de colocação de proximidade Azure apenas para o sistema.

Na maioria das implementações de clientes, os clientes constroem um único grupo de [recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) para sistemas SAP. Nesse caso, há uma relação um-para-um entre, por exemplo, o grupo de recursos do sistema ERP de produção e o seu grupo de colocação de proximidade. Noutros casos, os clientes organizam os seus grupos de recursos horizontalmente e recolhem todos os sistemas de produção num único grupo de recursos. Neste caso, você teria uma relação de um a muitos entre o seu grupo de recursos para a produção de sistemas SAP e vários grupos de colocação de proximidade para a sua produção SAP ERP, SAP BW, e assim por diante.

Evite agregar vários sistemas de produção sap ou não-produção num único grupo de colocação de proximidade. Quando um pequeno número de sistemas SAP ou um sistema SAP e algumas aplicações circundantes precisam de ter uma comunicação de rede de baixa latência, você pode considerar mover estes sistemas para um grupo de colocação de proximidade. Deve evitar pacotes de sistemas porque quanto mais sistemas se agrupam num grupo de colocação de proximidade, maiores são as hipóteses:

- Que você precisa de um tipo VM que não pode ser executado no datacenter específico para o qual o grupo de colocação de proximidade foi examinado.
- Que os recursos de VMs não convencionais, como VMs série M, podem eventualmente não ser cumpridos quando você precisa de mais porque você está adicionando software a um grupo de colocação de proximidade ao longo do tempo.

Aqui está o que a configuração ideal, como descrito, parece:

![Grupos de colocação de proximidade com apenas VMs Azure](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

Neste caso, os sistemas SAP únicos são agrupados num grupo de recursos cada, com um grupo de colocação de proximidade cada. Não há dependência de usar configurações de escala HANA ou DBMS.

## <a name="proximity-placement-groups-and-hana-large-instances"></a>Grupos de colocação de proximidade e grandes instâncias HANA
Se alguns dos seus sistemas SAP dependerem de [grandes instâncias HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) para a camada de aplicação, pode experimentar melhorias significativas na latência da rede entre a unidade HANA Large Instances e os VMs Azure quando estiver a utilizar unidades HANA Large Instances que são implantadas em [4 linhas ou selos de Revisão](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Uma melhoria é que as unidades HANA Large Instances, à medida que são implantadas, implantam-se com um grupo de colocação de proximidade. Pode utilizar esse grupo de colocação de proximidade para implementar os seus VMs da camada de aplicação. Como resultado, esses VMs serão implantados no mesmo datacenter que acolhe a sua unidade HANA Large Instances.

Para determinar se a sua unidade HANA Large Instances está implantada num carimbo ou linha de revisão 4, verifique o artigo Controlo de [grandes instâncias Azure HANA através](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit)do portal Azure . Na visão geral da sua unidade HANA Large Instances, também pode determinar o nome do grupo de colocação de proximidade porque foi criado quando a sua unidade HANA Large Instances foi implantada. O nome que aparece na visão geral dos atributos é o nome do grupo de colocação de proximidade em que deve implantar os seus VMs da camada de aplicação.

Em comparação com os sistemas SAP que utilizam apenas máquinas virtuais Azure, quando utiliza as Grandes Instâncias HANA, tem menos flexibilidade na decisão de [quantos grupos](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) de recursos Azure utilizar. Todas as unidades HANA Large Instances de um [inquilino HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) estão agruparadas num único grupo de recursos, como descrito [neste artigo.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal) A menos que se desloque a diferentes inquilinos para separar, por exemplo, sistemas de produção e não produção ou outros sistemas, todas as suas unidades HANA Large Instances serão implantadas num inquilino HANA Large Instances. Este inquilino tem uma relação um-para-um com um grupo de recursos. Mas um grupo de colocação de proximidade separado será definido para cada uma das unidades individuais.

Como resultado, as relações entre grupos de recursos Azure e grupos de colocação de proximidade para um único inquilino serão como mostrado aqui:

![Grupos de colocação de proximidade e grandes instâncias HANA](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>Exemplo de implantação com grupos de colocação de proximidade
Seguem-se alguns comandos PowerShell que pode utilizar para implantar os seus VMs com grupos de colocação de proximidade Azure.

O primeiro passo, depois de iniciar sessão no [Azure Cloud Shell,](https://azure.microsoft.com/features/cloud-shell/)é verificar se está na subscrição azure que pretende utilizar para a implementação:

<pre><code>
Get-AzureRmContext
</code></pre>

Se precisar de mudar para uma subscrição diferente, pode fazê-lo executando este comando:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Crie um novo grupo de recursos Azure executando este comando:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

Crie o novo grupo de colocação de proximidade executando este comando:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

Coloque o seu primeiro VM no grupo de colocação de proximidade utilizando um comando como este:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

O comando anterior implementa um VM baseado no Windows. Após esta implementação vm bem sucedida, o âmbito do datacenter do grupo de colocação de proximidade é definido dentro da região de Azure. Todas as implementações vm subsequentes que referenciam o grupo de colocação de proximidade, como mostrado no comando anterior, serão implantadas no mesmo datacenter Azure, desde que o tipo VM possa ser hospedado em hardware colocado nesse datacenter e capacidade para esse tipo VM disponível.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Combine conjuntos de disponibilidade e zonas de disponibilidade com grupos de colocação de proximidade
Uma das desvantagens em utilizar zonas de disponibilidade para implementações do sistema SAP é que não é possível implementar a camada de aplicação SAP utilizando conjuntos de disponibilidade dentro da zona específica. Pretende que a camada de aplicação SAP seja implantada nas mesmas zonas que a camada DBMS. A referenciação de uma Zona de Disponibilidade e um conjunto de disponibilidade ao implementar um único VM não é suportado. Então, anteriormente, foi forçado a implantar a sua camada de aplicação referindo-se a uma zona. Perdeu a capacidade de garantir que os VMs da camada de aplicação estavam espalhados por diferentes domínios de atualização e falha.

Ao utilizar grupos de colocação de proximidade, pode contornar esta restrição. Aqui está a sequência de implantação:

- Crie um grupo de colocação de proximidade.
- Implemente o seu VM de âncora, normalmente o servidor DBMS, referindo-se a uma Zona de Disponibilidade.
- Crie um conjunto de disponibilidade que referencia o grupo de proximidade Azure. (Consulte o comando mais tarde neste artigo.)
- Implemente os VMs da camada de aplicação, referindo o conjunto de disponibilidade e o grupo de colocação de proximidade.

Em vez de implantar o primeiro VM como demonstrado na secção anterior, refere-se a uma Zona de Disponibilidade e ao grupo de colocação de proximidade quando implementa o VM:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

Uma implementação bem sucedida desta máquina virtual alojaria a instância de base de dados do sistema SAP numa Zona de Disponibilidade. O âmbito do grupo de colocação de proximidade é fixado a um dos datacenters que representam a Zona de Disponibilidade que definiu.

Assuma que implementa os VMs dos Serviços Centrais da mesma forma que os VMs DBMS, fazendo referência à mesma zona ou zonas e aos mesmos grupos de colocação de proximidade. No próximo passo, precisa de criar os conjuntos de disponibilidade que pretende utilizar para a camada de aplicação do seu sistema SAP.

Você precisa definir e criar o grupo de colocação de proximidade. O comando para criar o conjunto de disponibilidade requer uma referência adicional ao ID do grupo de colocação de proximidade (não o nome). Você pode obter a identificação do grupo de colocação de proximidade usando este comando:

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

Quando criar o conjunto de disponibilidade, tem de considerar parâmetros adicionais quando estiver a utilizar discos geridos (predefinidos, salvo especificação em contrário) e grupos de colocação de proximidade:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

Idealmente, deve utilizar três domínios de falha. Mas o número de domínios de falhas suportados pode variar de região para região. Neste caso, o número máximo de domínios de avaria possíveis para as regiões específicas é de dois. Para implementar os Seus VMs da sua camada de aplicação, você precisa adicionar uma referência ao seu nome definido de disponibilidade e o nome do grupo de colocação de proximidade, como mostrado aqui:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

O resultado desta implantação é:
- Uma camada DBMS e Serviços Centrais para o seu sistema SAP que está localizado em uma zona de disponibilidade específica ou zonas de disponibilidade.
- Uma camada de aplicação SAP que está localizada através de conjuntos de disponibilidade nos mesmos datacenters Azure que os VM ou VMs DBMS.

> [!NOTE]
> Como você implanta um VM DBMS em uma zona e o segundo DBMS VM em outra zona para criar uma configuração de alta disponibilidade, você precisará de um grupo de colocação de proximidade diferente para cada uma das zonas. O mesmo acontece com qualquer conjunto de disponibilidade que utilize.

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>Mover um sistema existente para grupos de colocação de proximidade
Se já tiver sistemas SAP implantados, poderá querer otimizar a latência da rede de alguns dos seus sistemas críticos e localizar a camada de aplicação e a camada DBMS no mesmo datacenter. Para mover os VMs de um conjunto completo de disponibilidade azure para um grupo de colocação de proximidade existente que já está disponível, você precisa desligar todos os VMs do conjunto de disponibilidade e atribuir a disponibilidade definida para o grupo de colocação de proximidade existente através do portal Azure, PowerShell ou CLI. Se quiser mover um VM que não faça parte de uma disponibilidade definida num grupo de colocação de proximidade existente, basta encerrar o VM e atribuí-lo a um grupo de colocação de proximidade existente. 


## <a name="next-steps"></a>Passos seguintes
Confira a documentação:

- [Cargas de trabalho SAP em Azure: lista de verificação de planeamento e implementação](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [Pré-visualização: Implementar VMs para grupos de colocação de proximidade utilizando o Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [Pré-visualização: Implementar VMs para grupos de colocação de proximidade usando powerShell](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [Considerações para a implantação de DBMS de Máquinas Virtuais Azure para cargas de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

