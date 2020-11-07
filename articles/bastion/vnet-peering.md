---
title: VNet espreitando e arquitetura Azure Bastion
description: Neste artigo, saiba como o vNet espreita e o Azure Bastion pode ser usado em conjunto para ligar aos VMs.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: cherylmc
ms.openlocfilehash: ad3cf33dacffc8bcda9376857206784afedf7139
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94361988"
---
# <a name="vnet-peering-and-azure-bastion-preview"></a>VNet peering e Azure Bastion (Pré-visualização)

O Azure Bastion e o VNet podem ser usados em conjunto. Quando o espremiado VNet está configurado, não é preciso implantar O Bastião Azure em cada VNet espreitado. Isto significa que se tiver um hospedeiro Azure Bastion configurado numa rede virtual (VNet), pode ser utilizado para ligar aos VM implantados num VNet esprevado sem implantar um hospedeiro bastonário adicional. Para obter mais informações sobre o espremiamento do VNet, consulte [Sobre o espreitamento da rede virtual](../virtual-network/virtual-network-peering-overview.md).

Azure Bastion trabalha com os seguintes tipos de espreguiçadamento:

* **Observação de rede virtual:** Ligue redes virtuais na mesma região de Azure.
* **Observação global da rede virtual:** Ligação de redes virtuais em todas as regiões de Azure.

## <a name="architecture"></a>Arquitetura

Quando o espremiado VNet é configurado, O Bastião Azure pode ser implantado em topologias de hub-e-spoke ou de malha completa. A implementação do Azure Bastion é por rede virtual, não por subscrição/conta ou máquina virtual.

Uma vez que fornece o serviço Azure Bastion na sua rede virtual, a experiência RDP/SSH está disponível para todos os seus VMs no mesmo VNet, bem como VNets com vista. Isto significa que pode consolidar a implementação de Bastion para um único VNet e ainda chegar a VMs implantados num VNet esprevado, centralizando a implementação global.

:::image type="content" source="./media/vnet-peering/design.png" alt-text="Diagrama de Design e Arquitetura":::

Esta figura mostra a arquitetura de um destacamento de Azure Bastion num modelo de hub-and-spoke. Neste diagrama pode ver a seguinte configuração:

* O hospedeiro Bastion está implantado na rede virtual centralizada do Hub.
* O Grupo centralizado de segurança da rede (NSG) é implantado.
* Não é necessário um IP público no Azure VM.

**Passos:**

1. Ligue-se ao portal Azure utilizando qualquer navegador HTML5.
1. Selecione a máquina virtual para ligar.
1. Azure Bastion é detetado perfeitamente através do VNet esprevado.
1. Com um único clique, a sessão RDP/SSH abre no navegador. Para os limites de sessão simultânea de RDP e SSH, consulte as [sessões de RDP e SSH](bastion-faq.md#limits).

   :::image type="content" source="../../includes/media/bastion-vm-rdp/connect-vm.png" alt-text="Ligar":::

   Para obter mais informações sobre a ligação a um VM via Azure Bastion, consulte:

   * [Ligue-se a um VM - RDP](bastion-connect-vm-rdp.md).
   * [Ligue-se a um VM - SSH](bastion-connect-vm-ssh.md).

## <a name="faq"></a>FAQ

[!INCLUDE [FAQ for VNet peering](../../includes/bastion-faq-peering-include.md)]

## <a name="next-steps"></a>Passos seguintes

Leia as [FAQ do Bastião.](bastion-faq.md)