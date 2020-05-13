---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 033ec0f4011cae657fc464849aac5c5d9a850907
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83343325"
---
A Azure Compute oferece tamanhos de máquinas virtuais que estão isolados a um tipo de hardware específico e dedicados a um único cliente.  Estes tamanhos de máquinas virtuais são mais adequados para cargas de trabalho que requerem um elevado grau de isolamento de outros clientes para cargas de trabalho envolvendo elementos como conformidade e requisitos regulamentares.  Os clientes também podem optar por subdividir ainda mais os recursos destas máquinas virtuais isoladas utilizando suporte [Azure para máquinas virtuais aninhadas.](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)

A utilização de um tamanho isolado garante que a sua máquina virtual será a única a funcionar nessa instância específica do servidor.  As ofertas atuais de máquinas virtuais isoladas incluem:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2<sup>*</sup>
* Standard_D15_v2<sup>*</sup>
* Standard_F72s_v2

<sup>*</sup>Garantia de isolamento vai reformar-se até 15 de maio de 2020

## <a name="retiring-d15_v2ds15_v2-isolation-on-may-15-2020"></a>Aposentadoria D15_v2/DS15_v2 isolamento em 15 de maio de 2020
**Atualização em 10 de fevereiro de 2020: O prazo de reforma "isolado" foi alargado até 15 de maio de 2020"**

O Azure Dedicated Host é agora GA, que lhe permite executar as máquinas virtuais Linux e Windows da sua organização em servidores físicos de um único inquilino. Planeamos substituir totalmente os VMs Azure isolados por Azure Dedicado Host. Depois de 15 de maio de **2020,** os VMs D15_v2/DS15_v2 Azure deixarão de estar isolados.

## <a name="how-does-this-affect-me"></a>Como é que isto me afeta?
Depois de 15 de maio de 2020, deixaremos de fornecer uma garantia de isolamento para as suas máquinas virtuais D15_v2/DS15_v2 Azure. 

## <a name="what-actions-should-i-take"></a>Que ações devo tomar?
Se não for necessário o isolamento do hardware para si, não há nenhuma ação que precise de tomar. 

Se o isolamento for necessário para si, antes de 15 de maio de 2020, terá de o fazer:

• [Emigre](https://azure.microsoft.com/blog/introducing-azure-dedicated-host) a sua carga de trabalho para o Anfitrião Dedicado Azure.

• [Solicite acesso](https://aka.ms/D15iRequestAccess) a um D15i_v2 e DS15i_v2 Azure VM, para obter o mesmo desempenho de preço. Esta opção só está disponível para cenários de instâncias reservadas de um ano.    

• [Migrar](https://azure.microsoft.com/blog/resize-virtual-machines/) a sua carga de trabalho para outra máquina virtual isolada do Azure. 

Para mais detalhes veja abaixo:

## <a name="timeline"></a>Linha cronológica
| Date | Ação | 
| --- | --- |
| 18 de novembro de 2019 | Disponibilidade de D/DS15i_v2 (PAYG, RI de 1 ano) |
| 14 de maio de 2020  | Último dia para comprar D/DS15i_v2 RI de 1 ano | 
| 15 de maio de 2020   | D/DS15_v2 garantia de isolamento removida | 
| 15 de maio de 2021  | Aposentar-se D/DS15i_v2 (todos os clientes exceto que compraram RI de 3 anos de D/DS15_v2 antes de 18 de novembro de 2019)| 
| 17 de novembro de 2022  | Aposentar-se D/DS15i_v2 quando as RIs de 3 anos terminaram (para clientes que compraram RI de 3 anos de D/DS15_v2 antes de 18 de novembro de 2019) | 

## <a name="faq"></a>FAQ
### <a name="q-is-the-size-dds15_v2-going-to-get-retired"></a>P: O tamanho D/DS15_v2 vai reformar-se?
**R:** Não, só a característica de "isolamento" vai reformar-se. Se não precisa de isolamento, não precisa de tomar qualquer medida.

### <a name="q-is-the-size-dds15i_v2-going-to-get-retired"></a>P: O tamanho D/DS15i_v2 vai reformar-se?
**R:** Sim, o tamanho só está disponível até 15 de maio de 2021. Para os clientes que tenham comprado RIs a 3 anos em D/DS15_v2 antes de 18 de novembro de 2019 terá acesso a D/DS15i_v2 até 17 de novembro de 2022.

### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>P: Por que não vejo os novos tamanhos de D/DS15i_v2 no portal?
**R:** Se for um cliente D/DS15_v2 atual e quiser utilizar os novos tamanhos D/DS15i_v2, preencha este [formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u)

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>P: Por que não vejo nenhuma quota para os novos tamanhos de D/DS15i_v2?
**R:** Se for um cliente D/DS15_v2 atual e quiser utilizar os novos tamanhos D/DS15i_v2, preencha este [formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u)

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>P: Quando é que os outros tamanhos isolados vão reformar-se?
**R:** Forneceremos lembretes com 12 meses de antecedência do desmantelamento oficial das dimensões.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>P: Há algum tempo de inatividade quando o meu vm pousa num hardware não isolado?
**R:** Se não precisar de isolamento, não precisa de tomar qualquer medida e não verá qualquer tempo de inatividade.

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>P: Existem alterações de custos para se mudar para uma máquina virtual não isolada?
**A:** Não 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>P: Já comprei 1 ou 3 anos de instância reservada para D15_v2 ou Ds15_v2. Como é que o desconto será aplicado ao meu uso vm?
**RIs**adquiridos antes de 18 de novembro de 2019 alargarão automaticamente a cobertura à nova série VM isolada. 

| RI |  Flexibilidade de tamanho de instância | Elegibilidade de benefício |   
| --- | --- | --- |
|   D15_v2  |   Desativado     |   D15_v2 e D15i_v2 |    
|   D15_v2  |   Ativado  |   D15_v2 séries e D15i_v2 receberão todos o benefício ri. |    
|   D14_v2  |   Ativado  |   D15_v2 séries e D15i_v2 receberão todos o benefício ri. |    
 
Da mesma forma para a série Dsv2.
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>P: Quero comprar instâncias reservadas adicionais para o Dv2. Qual devo escolher?
**RIs**adquiridos após 18 de novembro de 2019, têm o seguinte comportamento. 

| RI |  Flexibilidade de tamanho de instância | Elegibilidade de benefício |   
| --- | --- | --- |
| D15_v2 |  Desativado |   D15_v2 apenas  
| D15_v2 |  Ativado |    D15_v2 série receberá o benefício ri. O novo D15i_v2 não será elegível para o benefício ri deste tipo RI. | 
| D15i_v2 |     Desativado | D15i_v2 apenas |  
| D15i_v2 |     Ativado  | D15i_v2 apenas | 
 
Por exemplo, a flexibilidade do tamanho não pode ser utilizada para se aplicar a quaisquer outros tamanhos, tais como D2_v2, D4_v2 ou D15_v2. Da mesma forma, para a série Dsv2.  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>P: Posso comprar um novo RI de 3 anos para D15i_v2 e DS15i_v2?
**R:** Infelizmente não, apenas ri de 1 ano está disponível para nova compra.
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>P: Posso mover a minha D15_v2/DS15_v2 reserva existente para uma instância reservada de tamanho isolado?
**R:** Esta ação não é necessária, uma vez que o benefício se aplicará tanto a tamanhos isolados como não isolados. Mas o Azure apoiará a alteração das instâncias reservadas D15_v2/DS15_v2 existentes para D15i_v2/DS15i_v2. Para todas as outras Instâncias Reservadas Dv2/Dsv2, utilize a Instância Reservada existente ou compre novas Instâncias Reservadas para os tamanhos isolados.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>P: Sou um Cliente de Tecido de Serviço Azure que depende dos Níveis de Durabilidade prata ou ouro. Esta mudança me afeta?
**A:** Não. As garantias fornecidas pelos Níveis de [Durabilidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) da Service Fabric continuarão a funcionar mesmo após esta alteração. Se necessitar de isolamento físico de hardware por outras razões, ainda poderá ter de tomar uma das ações acima descritas. 
