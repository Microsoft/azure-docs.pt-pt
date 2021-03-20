---
title: Grupos de colocação de proximidade Azure para aplicações SAP | Microsoft Docs
description: Descreve cenários de implantação do SAP com grupos de colocação de proximidade do Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 924fdef475c43023c69c3006db19cd9a5aa15349
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669660"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Grupos de colocação de proximidade azul para a latência ideal da rede com aplicações SAP
As aplicações SAP baseadas na arquitetura SAP NetWeaver ou SAP S/4HANA são sensíveis à latência da rede entre o nível de aplicação SAP e o nível de base de dados SAP. Esta sensibilidade é o resultado da maior parte da lógica de negócio que funciona na camada de aplicação. Como a camada de aplicação SAP gere a lógica do negócio, emite consultas para o nível da base de dados a uma frequência elevada, a uma taxa de milhares ou dezenas de milhares por segundo. Na maioria dos casos, a natureza destas consultas é simples. Podem ser executados na base de dados em 500 microsegundos ou menos.

O tempo gasto na rede para enviar essa consulta do nível de aplicação para o nível de base de dados e receber o resultado recuado tem um grande impacto no tempo que leva para executar processos de negócio. Esta sensibilidade à latência da rede é a razão pela qual você pode querer alcançar certas latências máximas de rede em projetos de implantação SAP. Consulte [a #1100926 DE NOTAS SAP - FAQ: Desempenho da rede](https://launchpad.support.sap.com/#/notes/1100926/E) para diretrizes sobre como classificar a latência da rede.

Em muitas regiões do Azure, o número de centros de dados tem crescido. Ao mesmo tempo, os clientes, especialmente para sistemas SAP de gama alta, estão a utilizar SKUs VM mais especiais da família M ou Mv2, ou HANA Large Instances. Estes tipos de máquinas virtuais Azure nem sempre estão disponíveis em todos os datacenters que complementam uma região do Azure. Estes factos podem criar oportunidades para otimizar a latência da rede entre a camada de aplicação SAP e a camada SAP DBMS.

Para lhe dar a possibilidade de otimizar a latência da rede, o Azure oferece [grupos de colocação de proximidade.](../../co-location.md) Os grupos de colocação de proximidade podem ser usados para forçar o agrupamento de diferentes tipos de VM num único datacenter Azure para otimizar a latência da rede entre estes diferentes tipos de VM da melhor forma possível. No processo de implantação do primeiro VM num grupo de colocação de proximidade, o VM fica ligado a um datacenter específico. Por mais apelativo que pareça esta perspetiva, a utilização da construção introduz também algumas restrições:

- Não pode assumir que todos os tipos de VM do Azure estão disponíveis em todos os centros de dados Azure. Como resultado, a combinação de diferentes tipos de VM dentro de um grupo de colocação de proximidade pode ser restringida. Estas restrições ocorrem porque o hardware do anfitrião que é necessário para executar um determinado tipo VM pode não estar presente no datacenter para o qual o grupo de colocação foi implementado
- Ao redimensionar partes dos VMs que estão dentro de um grupo de colocação de proximidade, não pode assumir automaticamente que em todos os casos o novo tipo VM está disponível no mesmo datacenter que os outros VMs que fazem parte do grupo de colocação de proximidade
- À medida que o Azure desativa o hardware, pode forçar certos VMs de um grupo de colocação de proximidade em outro datacenter Azure. Para mais detalhes sobre este caso, leia o documento [Co-localizar recursos para uma maior latência](../../co-location.md#planned-maintenance-and-proximity-placement-groups)  

> [!IMPORTANT]
> Como resultado das potenciais restrições, devem ser utilizados grupos de colocação de proximidade:
>
> - Só quando necessário
> - Apenas na granularidade de um único sistema SAP e não para toda uma paisagem do sistema ou uma paisagem SAP completa
> - De forma a manter os diferentes tipos de VM e o número de VMs dentro de um grupo de colocação de proximidade ao mínimo

Assuma que se implementar VMs especificando Zonas de Disponibilidade e selecionar as mesmas Zonas de Disponibilidade, a latência da rede entre estes VMs deve ser suficiente para operar sistemas SAP NetWeaver e S/4HANA com desempenho e produção satisfatórios. Esta suposição é independente do facto de uma determinada zona ser construída a partir de um datacenter ou de vários centros de dados. A única razão para utilizar grupos de colocação de proximidade em implementações zonais é o caso em que pretende atribuir VMs de disponibilidade Azure implantados juntamente com VMs implantados zonal.


## <a name="what-are-proximity-placement-groups"></a>O que são grupos de colocação de proximidade? 
Um grupo de colocação de proximidade Azure é uma construção lógica. Quando um grupo de colocação de proximidade é definido, está ligado a uma região de Azure e a um grupo de recursos Azure. Quando os VMs são implantados, um grupo de colocação de proximidade é referenciado por:

- O primeiro Azure VM implantado no datacenter. Pode pensar na primeira máquina virtual como um "VM de âmbito" que é implantado num datacenter baseado em algoritmos de alocação Azure que são eventualmente combinados com definições de utilizador para uma zona de disponibilidade específica.
- Todos os VMs subsequentes implantaram que referenciam o grupo de colocação de proximidade, para colocar todos os VMs Azure subsequentemente implantados no mesmo datacenter que a primeira máquina virtual.

> [!NOTE]
> Se não houver hardware de hospedeiro implantado que possa executar um tipo VM específico no datacenter onde o primeiro VM foi colocado, a implementação do tipo VM solicitado não será bem sucedida. Receberá uma mensagem de fracasso.

Um único [grupo de recursos Azure](../../../azure-resource-manager/management/manage-resources-portal.md) pode ter vários grupos de colocação de proximidade atribuídos a ele. Mas um grupo de colocação de proximidade pode ser atribuído a apenas um grupo de recursos Azure.


## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Grupos de colocação de proximidade com sistemas SAP que utilizam apenas VMs Azure
A maioria das implementações do sistema SAP NetWeaver e S/4HANA em Azure não utilizam [grandes instâncias HANA](./hana-overview-architecture.md). Para implementações que não usam HANA Large Instances, é importante proporcionar um desempenho ideal entre a camada de aplicação SAP e o nível DBMS. Para tal, defina um grupo de colocação de proximidade Azure apenas para o sistema.

Na maioria das implementações de clientes, os clientes constroem um único [grupo de recursos Azure](../../../azure-resource-manager/management/manage-resources-portal.md) para sistemas SAP. Nesse caso, existe uma relação um-para-um entre, por exemplo, o grupo de recursos do sistema ERP de produção e o seu grupo de colocação de proximidade. Noutros casos, os clientes organizam os seus grupos de recursos horizontalmente e recolhem todos os sistemas de produção num único grupo de recursos. Neste caso, você teria uma relação de um a muitos entre o seu grupo de recursos para a produção de sistemas SAP e vários grupos de colocação de proximidade para a sua produção SAP ERP, SAP BW, e assim por diante.

Evite agregar vários sistemas de produção sap ou não-produção num único grupo de colocação de proximidade. Quando um pequeno número de sistemas SAP ou um sistema SAP e algumas aplicações circundantes precisam de ter uma comunicação de rede de baixa latência, você pode considerar mover estes sistemas para um grupo de colocação de proximidade. Evite pacotes de sistemas porque quanto mais sistemas agrupar num grupo de colocação de proximidade, maiores são as hipóteses:

- Que você precisa de um tipo VM que não pode ser executado no datacenter específico no qual o grupo de colocação de proximidade foi examinado.
- Que os recursos de VMs não convencionais, como M-Series VMs, poderiam eventualmente ser cumpridos quando você precisa de mais porque você está adicionando software a um grupo de colocação de proximidade ao longo do tempo.

Aqui está o que a configuração ideal, como descrito, parece:

![Grupos de colocação de proximidade com apenas VMs Azure](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

Neste caso, os sistemas SAP únicos são agrupados num único grupo de recursos cada, com um grupo de colocação de proximidade cada. Não há dependência de usar configurações de escala HANA ou DBMS.

## <a name="proximity-placement-groups-and-hana-large-instances"></a>Grupos de colocação de proximidade e HANA Grandes Instâncias
Se alguns dos seus sistemas SAP dependerem de [HANA Large Instances](./hana-overview-architecture.md) para a camada de aplicação, pode experimentar melhorias significativas na latência da rede entre a unidade HANA Large Instances e os VMs Azure quando estiver a utilizar unidades HANA Large Instances que são implantadas na [Revisão 4 linhas ou selos](./hana-network-architecture.md#networking-architecture-for-hana-large-instance). Uma melhoria é que as unidades HANA Large Instances, à medida que são implantadas, implantam-se com um grupo de colocação de proximidade. Pode utilizar esse grupo de colocação de proximidade para implantar os VMs da sua camada de aplicação. Como resultado, esses VMs serão implantados no mesmo centro de dados que hospeda a sua unidade HANA Large Instances.

Para determinar se a sua unidade HANA Large Instances está implantada num selo ou linha de Revisão 4, consulte o artigo [controlo Azure HANA Large Instances através do portal Azure](./hana-li-portal.md#look-at-attributes-of-single-hli-unit). Na visão geral dos atributos da sua unidade HANA Large Instances, também pode determinar o nome do grupo de colocação de proximidade porque foi criado quando a sua unidade HANA Large Instances foi implantada. O nome que aparece na visão geral dos atributos é o nome do grupo de colocação de proximidade em que deve implementar os VMs da sua camada de aplicação.

Em comparação com os sistemas SAP que utilizam apenas máquinas virtuais Azure, quando utiliza HANA Large Instances, você tem menos flexibilidade na decisão de quantos [grupos de recursos Azure](../../../azure-resource-manager/management/manage-resources-portal.md) devem usar. Todas as unidades HANA Large Instances de um [inquilino HANA Large Instances](./hana-know-terms.md) estão agrupadas num único grupo de recursos, como descrito [neste artigo.](./hana-li-portal.md#display-of-hana-large-instance-units-in-the-azure-portal) A menos que você se insiram em diferentes inquilinos para separar, por exemplo, sistemas de produção e não-produção ou outros sistemas, todas as suas unidades HANA Large Instances serão implantadas em um inquilino HANA Large Instances. Este inquilino tem uma relação de um para um com um grupo de recursos. Mas um grupo de colocação de proximidade separado será definido para cada uma das unidades individuais.

Como resultado, as relações entre grupos de recursos Azure e grupos de colocação de proximidade para um único inquilino serão como mostrado aqui:

![Grupos de colocação de proximidade e HANA Grandes Instâncias](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>Exemplo de implantação com grupos de colocação de proximidade
Seguem-se alguns comandos PowerShell que pode utilizar para implantar os seus VMs com grupos de colocação de proximidade Azure.

O primeiro passo, depois de iniciar seduca na [Azure Cloud Shell,](https://azure.microsoft.com/features/cloud-shell/)é verificar se está na subscrição do Azure que pretende utilizar para a implementação:

<pre><code>
Get-AzureRmContext
</code></pre>

Se precisar de alterar para uma subscrição diferente, pode fazê-lo executando este comando:

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

O comando anterior implementa um VM baseado no Windows. Após esta implantação de VM ter sucesso, o âmbito do datacenter do grupo de colocação de proximidade é definido na região de Azure. Todas as implementações VM subsequentes que referem o grupo de colocação de proximidade, como mostrado no comando anterior, serão implantadas no mesmo datacenter Azure, desde que o tipo VM possa ser hospedado no hardware colocado nesse datacenter, e a capacidade para esse tipo VM está disponível.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Combine conjuntos de disponibilidade e zonas de disponibilidade com grupos de colocação de proximidade
Uma das desvantagens para a utilização de Zonas de Disponibilidade para implementações do sistema SAP é que não é possível implantar a camada de aplicação SAP utilizando conjuntos de disponibilidade dentro da zona específica. Pretende que a camada de aplicação SAP seja implantada nas mesmas zonas que a camada DBMS. Fazer referência a uma Zona de Disponibilidade e a um conjunto de disponibilidade ao implementar um único VM não é suportado. Então, anteriormente, foi forçado a implantar a sua camada de aplicação referindo-se a uma zona. Perdeu a capacidade de garantir que os VMs da camada de aplicação estavam espalhados por diferentes domínios de atualização e falha.

Ao utilizar grupos de colocação de proximidade, pode contornar esta restrição. Aqui está a sequência de implantação:

- Criar um grupo de colocação de proximidade.
- Implemente o seu VM de âncora, normalmente o servidor DBMS, fazendo referência a uma Zona de Disponibilidade.
- Crie um conjunto de disponibilidade que refira o grupo de proximidade Azure. (Ver o comando mais tarde neste artigo.)
- Implemente os VMs da camada de aplicação referindo o conjunto de disponibilidade e o grupo de colocação de proximidade.

Em vez de implantar o primeiro VM como demonstrado na secção anterior, refere uma Zona de Disponibilidade e o grupo de colocação de proximidade quando implementa o VM:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

Uma implementação bem sucedida desta máquina virtual acolheria a caixa de dados do sistema SAP numa zona de disponibilidade. O âmbito do grupo de colocação de proximidade é fixado a um dos datacenters que representam a Zona de Disponibilidade definida.

Assuma que implementa os VMs dos Serviços Centrais da mesma forma que os VMs DBMS, referindo-se à mesma zona ou zonas e aos mesmos grupos de colocação de proximidade. No passo seguinte, precisa de criar os conjuntos de disponibilidade que pretende utilizar para a camada de aplicação do seu sistema SAP.

Definir e criar o grupo de colocação de proximidade. O comando para a criação do conjunto de disponibilidade requer uma referência adicional ao ID do grupo de colocação de proximidade (não o nome). Pode obter a ID do grupo de colocação de proximidade utilizando este comando:

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

Quando criar o conjunto de disponibilidade, tem de considerar parâmetros adicionais quando estiver a utilizar discos geridos (padrão, salvo especificação em contrário) e grupos de colocação de proximidade:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

Idealmente, deve utilizar três domínios de avaria. Mas o número de domínios de avaria suportados pode variar de região para região. Neste caso, o número máximo de domínios de avaria possível para as regiões específicas é de dois. Para implementar os VMs da sua camada de aplicação, precisa de adicionar uma referência ao nome do conjunto de disponibilidade e ao nome do grupo de colocação de proximidade, como mostrado aqui:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

O resultado desta implantação é:
- Uma camada DBMS e serviços centrais para o seu sistema SAP que está localizado numa zona de disponibilidade específica ou zonas de disponibilidade.
- Uma camada de aplicação SAP que está localizada através de conjuntos de disponibilidade nos mesmos centros de dados Azure como os VM ou VMs DBMS.

> [!NOTE]
> Como coloca um DBMS VM numa zona e o segundo DBMS VM em outra zona para criar uma configuração de alta disponibilidade, precisará de um grupo de colocação de proximidade diferente para cada uma das zonas. O mesmo se aplica a qualquer conjunto de disponibilidade que utilize.

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>Mover um sistema existente para grupos de colocação de proximidade
Se já tiver sistemas SAP implantados, talvez queira otimizar a latência da rede de alguns dos seus sistemas críticos e localizar a camada de aplicação e a camada DBMS no mesmo datacenter. Para mover os VMs de uma disponibilidade completa de Azure definida para um grupo de colocação de proximidade existente que já está traçado, você precisa desligar todos os VMs do conjunto de disponibilidade e atribuir o conjunto de disponibilidade definido para o grupo de colocação de proximidade existente através do portal Azure, PowerShell ou CLI. Se quiser mover um VM que não faça parte de uma disponibilidade definida para um grupo de colocação de proximidade existente, basta desligar o VM e atribuí-lo a um grupo de colocação de proximidade existente. 


## <a name="next-steps"></a>Passos seguintes
Confira a documentação:

- [Cargas de trabalho sap em Azure: lista de verificação de planeamento e implantação](./sap-deployment-checklist.md)
- [Pré-visualização: Implementar VMs para grupos de colocação de proximidade usando O Azure CLI](../../linux/proximity-placement-groups.md)
- [Pré-visualização: Implementar VMs para grupos de colocação de proximidade usando PowerShell](../../windows/proximity-placement-groups.md)
- [Considerações para a implantação de DBMS de máquinas virtuais Azure para cargas de trabalho SAP](./dbms_guide_general.md)