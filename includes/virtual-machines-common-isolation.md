---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 033ec0f4011cae657fc464849aac5c5d9a850907
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83343325"
---
O Azure Compute oferece tamanhos de máquinas virtuais que são isolados a um tipo de hardware específico e dedicados a um único cliente.  Estes tamanhos de máquina virtuais são mais adequados para cargas de trabalho que requerem um alto grau de isolamento de outros clientes para cargas de trabalho envolvendo elementos como conformidade e requisitos regulamentares.  Os clientes também podem optar por subdividir ainda mais os recursos destas máquinas virtuais isoladas utilizando [o suporte Azure para máquinas virtuais aninhadas.](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)

Utilizar um tamanho isolado garante que a sua máquina virtual será a única a funcionar nessa instância específica do servidor.  As atuais ofertas de máquinas virtuais isoladas incluem:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2<sup>*</sup>
* Standard_D15_v2<sup>*</sup>
* Standard_F72s_v2

<sup>*</sup>Garantia de isolamento vai reformar-se até 15 de maio de 2020

## <a name="retiring-d15_v2ds15_v2-isolation-on-may-15-2020"></a>Reforma do D15_v2/DS15_v2 isolamento em 15 de maio de 2020
**Atualização em 10 de fevereiro de 2020: O prazo para a reforma "isolada" foi alargado para 15 de maio de 2020"**

O Azure Dedicated Host é agora GA, o que lhe permite executar as máquinas virtuais Linux e Windows da sua organização em servidores físicos de um único inquilino. Planeamos substituir totalmente os VMs Azure isolados por Azure Dedicated Host. Depois de 15 de maio de **2020,** os VMs D15_v2/DS15_v2 Azure deixarão de ser isolados de hardware.

## <a name="how-does-this-affect-me"></a>Como é que isto me afeta?
Depois de 15 de maio de 2020, deixaremos de oferecer uma garantia de isolamento para as suas máquinas virtuais D15_v2/DS15_v2 Azure. 

## <a name="what-actions-should-i-take"></a>Que ações devo tomar?
Se o isolamento de hardware não for necessário para si, não há nenhuma ação que precise de tomar. 

Se for necessário um isolamento para si, antes de 15 de maio de 2020, você precisará de:

• [Migrar](https://azure.microsoft.com/blog/introducing-azure-dedicated-host) a sua carga de trabalho para o Azure Dedicated Host.

• [Solicite acesso](https://aka.ms/D15iRequestAccess) a um D15i_v2 e DS15i_v2 Azure VM, para obter o mesmo desempenho de preço. Esta opção só está disponível para cenários de instâncias pagas e reservadas de um ano.    

• [Migrar](https://azure.microsoft.com/blog/resize-virtual-machines/) a carga de trabalho para outra máquina virtual isolada do Azure. 

Para mais detalhes veja abaixo:

## <a name="timeline"></a>Linha cronológica
| Data | Ação | 
| --- | --- |
| 18 de novembro de 2019 | Disponibilidade de D/DS15i_v2 (PAYG, 1 ano RI) |
| 14 de maio de 2020  | Último dia para comprar D/DS15i_v2 1 ano RI | 
| 15 de maio de 2020   | Garantia de isolamento D/DS15_v2 removida | 
| 15 de maio de 2021  | Aposentar-se D/DS15i_v2 (todos os clientes exceto quem comprou RI de 3 anos de D/DS15_v2 antes de 18 de novembro de 2019)| 
| 17 de novembro de 2022  | Aposentar-se D/DS15i_v2 quando 3 anos de RIs feito (para clientes que compraram RI de 3 anos de D/DS15_v2 antes de 18 de novembro de 2019) | 

## <a name="faq"></a>FAQ
### <a name="q-is-the-size-dds15_v2-going-to-get-retired"></a>P: O tamanho D/DS15_v2 vai ser reformado?
**R:** Não, só a função de "isolamento" vai ser reformada. Se não precisa de isolamento, não precisa de tomar nenhuma medida.

### <a name="q-is-the-size-dds15i_v2-going-to-get-retired"></a>P: O tamanho D/DS15i_v2 vai ser reformado?
**R**: Sim, o tamanho só está disponível até 15 de maio de 2021. Para os clientes que tenham comprado RIs de 3 anos em D/DS15_v2 antes de 18 de novembro de 2019 terão acesso a D/DS15i_v2 até 17 de novembro de 2022.

### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>P: Por que não estou a ver os novos tamanhos D/DS15i_v2 no portal?
**R**: Se você é um cliente atual D/DS15_v2 e quer usar os novos tamanhos D/DS15i_v2, preencha este [formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u)

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>P: Por que não vejo qualquer quota para os novos tamanhos D/DS15i_v2?
**R**: Se você é um cliente atual D/DS15_v2 e quer usar os novos tamanhos D/DS15i_v2, preencha este [formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u)

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>P: Quando é que os outros tamanhos isolados se vão reformar?
**R**: Forneceremos lembretes com 12 meses de antecedência do desmantelamento oficial dos tamanhos.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>P: Há algum tempo de paragem quando o meu vm aterra num hardware não isolado?
**R**: Se não precisar de isolamento, não precisa de tomar nenhuma medida e não verá nenhum tempo de inatividade.

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>P: Existem alterações de custos para se mudar para uma máquina virtual não isolada?
**A:** Não 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>P: Já comprei instância reservada de 1 ou 3 anos para D15_v2 ou Ds15_v2. Como é que o desconto será aplicado à minha utilização em VM?
**R**: RIs adquiridos antes de 18 de novembro de 2019 irá automaticamente alargar a cobertura à nova série de VM isolada. 

| RI |  Flexibilidade do tamanho do exemplo | Elegibilidade de benefícios |   
| --- | --- | --- |
|   D15_v2  |   Desativado     |   D15_v2 e D15i_v2 |    
|   D15_v2  |   Ativado  |   D15_v2 séries e D15i_v2 receberão todos o benefício ri. |    
|   D14_v2  |   Ativado  |   D15_v2 séries e D15i_v2 receberão todos o benefício ri. |    
 
Da mesma forma para a série Dsv2.
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>P: Quero comprar instâncias reservadas adicionais para o Dv2. Qual devo escolher?
**R**: Todas as RIs adquiridas após 18 de novembro de 2019, têm o seguinte comportamento. 

| RI |  Flexibilidade do tamanho do exemplo | Elegibilidade de benefícios |   
| --- | --- | --- |
| D15_v2 |  Desativado |   só D15_v2  
| D15_v2 |  Ativado |    D15_v2 série receberá o benefício ri. O novo D15i_v2 não será elegível para benefícios de RI deste tipo de RI. | 
| D15i_v2 |     Desativado | só D15i_v2 |  
| D15i_v2 |     Ativado  | só D15i_v2 | 
 
Exemplo A flexibilidade do tamanho não pode ser utilizada para se aplicar a quaisquer outros tamanhos, tais como D2_v2, D4_v2 ou D15_v2. Da mesma forma, para a série Dsv2.  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>P: Posso comprar um ri novo de 3 anos para D15i_v2 e DS15i_v2?
**R**: Infelizmente não, apenas um ano de RI está disponível para nova compra.
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>P: Posso mover a minha D15_v2/DS15_v2 Instância de Reserva existente para um tamanho isolado De Instância Reservada?
**A**: Esta ação não é necessária, uma vez que a prestação se aplicará tanto a tamanhos isolados como a tamanhos não isolados. Mas o Azure apoiará a alteração das instâncias reservadas D15_v2/DS15_v2 existentes para D15i_v2/DS15i_v2. Para todas as outras Instâncias Reservadas dv2/Dsv2, utilize a Instância Reservada existente ou compre novas Instâncias Reservadas para os tamanhos isolados.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>P: Sou um cliente de tecido de serviço Azure que conta com os níveis de durabilidade prateada ou dourada. Esta mudança tem impacto em mim?
**A:** Não. As garantias fornecidas pelos Tiers de [Durabilidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) da Service Fabric continuarão a funcionar mesmo após esta alteração. Se necessitar de isolamento físico de hardware por outras razões, poderá ainda ter de tomar uma das ações descritas acima. 
