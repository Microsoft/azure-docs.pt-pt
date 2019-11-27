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
ms.openlocfilehash: 2251725597e44a15e421f33cc315be3fb1c7846f
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485431"
---
A Computação do Azure oferece tamanhos de máquinas virtuais que são Isolados para um tipo de hardware específico e dedicados a um único cliente.  Estes tamanhos de máquinas virtuais são mais adequados para cargas de trabalho que exigem um elevado grau de isolamento de outros clientes para cargas de trabalho que envolvem elementos como os requisitos de conformidade e regulamentares.  Os clientes também podem optar por subdividir ainda mais os recursos dessas máquinas virtuais isoladas usando o [suporte do Azure para máquinas virtuais aninhadas](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

A utilização de um tamanho isolado garante que sua máquina virtual será a única em execução nessa instância de servidor específica.  As ofertas atuais de máquinas virtuais isoladas incluem:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

Você pode saber mais sobre cada tamanho isolado disponível [aqui](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory).

## <a name="retiring-d15_v2ds15_v2-isolation-on-february-15-2020"></a>Desativar o isolamento de D15_v2/DS15_v2 em 15 de fevereiro de 2020
Anunciamos recentemente a versão prévia do host dedicado do Azure, que permite que você execute as máquinas virtuais Linux e Windows de sua organização em servidores físicos de locatário único. Planejamos substituir totalmente VMs do Azure isoladas pelo host dedicado do Azure. Após **15 de fevereiro de 2020,** as VMs do Azure D15_v2/DS15_v2 podem não estar mais isoladas em hardware.

## <a name="how-does-this-affect-me"></a>Como é que isto me afeta?
Após 15 de fevereiro de 2020, não forneceremos mais uma garantia de isolamento para suas máquinas virtuais do Azure D15_v2/DS15_v2. 

## <a name="what-actions-should-i-take"></a>Quais ações devo tomar?
Se o isolamento de hardware não for necessário para você, não haverá nenhuma ação que você precise tomar. 

Se o isolamento for necessário para você, antes de 15 de fevereiro de 2020, você precisaria:

• [Migre](https://azure.microsoft.com/blog/introducing-azure-dedicated-host) sua carga de trabalho para a versão prévia do host dedicado do Azure

• [Solicitar acesso](https://aka.ms/D15iRequestAccess) a uma D15i_v2 e DS15i_v2 VM do Azure para obter o mesmo desempenho de preço. Essa opção só está disponível para cenários de instância reservada pago pelo uso e de um ano.    

• [Migre](https://azure.microsoft.com/blog/resize-virtual-machines/) sua carga de trabalho para outra máquina virtual isolada do Azure. 

Para obter detalhes, veja abaixo:

## <a name="timeline"></a>Linha cronológica
| Date | Ação | 
| --- | --- |
| 18 de novembro de 2019  | Disponibilidade de D/DS15i_v2 (PAYG, RI de 1 ano) |
| 14 de fevereiro de 2020  | Último dia para comprar D/DS15i_v2 RI de 1 ano | 
| 15 de fevereiro de 2020   | Garantia de isolamento D/DS15_v2 removida | 
| 15 de maio de 2021  | Desativar D/DS15i_v2 (todos os clientes exceto quem comprou a RI de 3 anos de D/DS15_v2 antes de 18 de novembro de 2019)| 
| 17 de novembro de 2022   | Desativar D/DS15i_v2 quando o RIs de 3 anos for concluído (para clientes que compraram uma RI de 3 anos de D/DS15_v2 antes de 18 de novembro de 2019) | 

## <a name="faq"></a>FAQ
### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>P: por que não vejo os novos tamanhos D/DS15i_v2 no portal?
**R**: se você for um cliente d/DS15_v2 atual e quiser usar os novos tamanhos d/DS15i_v2, preencha este [formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u)

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>P: por que não estou vendo nenhuma cota para os novos tamanhos D/DS15i_v2?
**R**: se você for um cliente d/DS15_v2 atual e quiser usar os novos tamanhos d/DS15i_v2, preencha este [formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u)

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>P: quando os outros tamanhos isolados serão desativados?
**R**: forneceremos lembretes 12 meses de antecedência sobre o encerramento oficial dos tamanhos.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>P: há um tempo de inatividade quando minha VM chega em um hardware não isolado?
**R**: se você não precisar de isolamento, não precisará executar nenhuma ação e não verá nenhum tempo de inatividade.

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>P: há alguma alteração de custo para mudar para uma máquina virtual não isolada?
**R**: não 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>P: já comprei uma instância reservada de 1 ou 3 anos para D15_v2 ou Ds15_v2. Como o desconto será aplicado ao meu uso da VM?
**R: o**RIS adquirido antes de 18 de novembro de 2019 irá estender automaticamente a cobertura para a nova série de VMs isoladas. 

| REFERENCIA |  Flexibilidade do tamanho da instância | Elegibilidade do benefício |   
| --- | --- | --- |
|   D15_v2  |   Desativado     |   D15_v2 e D15i_v2 |    
|   D15_v2  |   Ativado  |   D15_v2 série e D15i_v2 receberão o benefício RI. |    
|   D14_v2  |   Ativado  |   D15_v2 série e D15i_v2 receberão o benefício RI. |    
 
Da mesma forma para a série Dsv2.
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>P: desejo adquirir instâncias reservadas adicionais para Dv2. Qual devo escolher?
**R**: todos os RIS adquiridos após 18 de novembro de 2019 têm o seguinte comportamento. 

| REFERENCIA |  Flexibilidade do tamanho da instância | Elegibilidade do benefício |   
| --- | --- | --- |
| D15_v2 |  Desativado |   Somente D15_v2  
| D15_v2 |  Ativado |    A série D15_v2 receberá o benefício de RI. O novo D15i_v2 não será elegível para o benefício de RI desse tipo de RI. | 
| D15i_v2 |     Desativado | Somente D15i_v2 |  
| D15i_v2 |     Ativado  | Somente D15i_v2 | 
 
A flexibilidade do tamanho da instância não pode ser usada para se aplicar a outros tamanhos, como D2_v2, D4_v2 ou D15_v2. Da mesma forma, para a série Dsv2.  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>P: posso comprar uma nova RI de 3 anos para D15i_v2 e DS15i_v2?
**R**: Infelizmente não, apenas uma ri de 1 ano está disponível para nova compra.
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>P: posso mover minha instância de reserva de D15_v2/DS15_v2 existente para uma instância reservada de tamanho isolado?
**R**: isso não é necessário, pois o benefício será aplicado a tamanhos isolados e não isolados. Mas o Azure dará suporte à alteração de instâncias reservadas de D15_v2/DS15_v2 existentes para D15i_v2/DS15i_v2. Para todas as outras instâncias reservadas Dv2/Dsv2, use a instância reservada existente ou compre novas instâncias reservadas para os tamanhos isolados.

### <a name="q-im-a-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>P: sou um cliente do Azure Service Fabric contando com as camadas de durabilidade prata ou ouro. Essa alteração me afeta?
**R**: não. As garantias fornecidas pelas camadas de [durabilidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) de Service Fabric continuarão a funcionar mesmo após essa alteração. Se você precisar de isolamento de hardware físico por outros motivos, talvez ainda precise executar uma das ações descritas acima. 
