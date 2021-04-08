---
title: incluir ficheiro
description: incluir ficheiro
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 11/05/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 57b0bb9ab8ceb34021a38db0d0abf2c9c919e808
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94356657"
---
### <a name="can-i-still-deploy-multiple-bastion-hosts-across-peered-virtual-networks"></a>Ainda posso implementar vários anfitriões de Bastião através de redes virtuais?

Sim. Por predefinição, um utilizador vê o hospedeiro Bastion que é implantado na mesma rede virtual em que o VM reside. No entanto, no menu **Connect,** um utilizador pode ver vários anfitriões de Bastião detetados através de redes espreitadas. Podem selecionar o anfitrião Bastion que preferem utilizar para se ligarem ao VM implantado na rede virtual.

### <a name="if-my-peered-vnets-are-deployed-in-different-subscriptions-will-connectivity-via-bastion-work"></a>Se os meus VNets espreitados forem implantados em diferentes subscrições, a conectividade através do Bastion funcionará?

Sim, a conectividade via Bastion continuará a trabalhar para VNets espreitados através de diferentes subscrições para um único Inquilino. As assinaturas em dois inquilinos diferentes não são apoiadas. Para ver Bastion no menu de entrega do **Connect,** o utilizador deve selecionar os subs a que tem acesso na **subscrição > subscrição global.**

:::image type="content" source="./media/bastion-faq-peering-include/global-subscriptions.png" alt-text="Filtro global de subscrições" lightbox="./media/bastion-faq-peering-include/global-subscriptions.png":::

### <a name="i-have-access-to-the-peered-vnet-but-i-cant-see-the-vm-deployed-there"></a>Tenho acesso ao VNet, mas não consigo ver o VM lá implantado.

Certifique-se de que o utilizador **leu** o acesso ao VM e ao VNet. Além disso, verifique no IAM que o utilizador **leu** o acesso aos seguintes recursos:

* Papel do leitor na máquina virtual.
* Função do leitor no NIC com IP privado da máquina virtual.
* Papel do leitor no recurso Azure Bastion.
* Função do leitor na Rede Virtual (Não é necessária se não houver rede virtual com olhos).

|Permissões|Description|Tipo de permissão|
|---|---| ---|
|Microsoft.Network/bastionHosts/read |Recebe um bastonário anfitrião|Ação|
|Microsoft.Network/virtualNetworks/BastionHosts/action |Obtém referências do Bastion Host numa Rede Virtual.|Ação|
|Microsoft.Network/virtualNetworks/bastionHosts/predefinição/ação|Obtém referências do Bastion Host numa Rede Virtual.|Ação|
|Microsoft.Network/networkInterfaces/read|Obtém uma definição de interface de rede.|Ação|
|Microsoft.Network/networkInterfaces/ipconfigurations/read|Obtém uma definição de configuração IP de interface de rede.|Ação|
|Microsoft.Network/virtualNetworks/read|Obtenha a definição de rede virtual|Ação|
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read|Obtém referências a todas as máquinas virtuais numa sub-rede de rede virtual|Ação|
|Microsoft.Network/virtualNetworks/virtualMachines/read|Obtém referências a todas as máquinas virtuais numa rede virtual|Ação|