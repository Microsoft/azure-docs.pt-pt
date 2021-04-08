---
title: VNet espreitando e arquitetura Azure Bastion
description: Neste artigo, saiba como o vNet espreita e o Azure Bastion pode ser usado em conjunto para ligar aos VMs.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: f72a3739fac1e7d6afdafd2676ea6fcefe847b2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101710588"
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
2. Certifique-se de que **leu** o acesso ao VM alvo e ao VNet. Além disso, verifique no IAM que leu o acesso aos seguintes recursos:
   * Papel do leitor na máquina virtual.
   * Função do leitor no NIC com IP privado da máquina virtual.
   * Papel do leitor no recurso Azure Bastion.
   * Função do leitor na Rede Virtual (Não é necessária se não houver rede virtual com olhos).
3. Para ver Bastion no menu de entrega do **Connect,** tem de selecionar os subs a que tem acesso na **subscrição > subscrição global.**
4. Selecione a máquina virtual para ligar.
5. Azure Bastion é detetado perfeitamente através do VNet esprevado.
6. Com um único clique, a sessão RDP/SSH abre no navegador. Para os limites de sessão simultânea de RDP e SSH, consulte as [sessões de RDP e SSH](bastion-faq.md#limits).

  :::image type="content" source="../../includes/media/bastion-vm-rdp/connect-vm.png" alt-text="Ligar":::

   Para obter mais informações sobre a ligação a um VM via Azure Bastion, consulte:

   * [Ligue-se a um VM - RDP](bastion-connect-vm-rdp.md).
   * [Ligue-se a um VM - SSH](bastion-connect-vm-ssh.md).

## <a name="faq"></a>FAQ

[!INCLUDE [FAQ for VNet peering](../../includes/bastion-faq-peering-include.md)]

## <a name="next-steps"></a>Passos seguintes

Leia as [FAQ do Bastião.](bastion-faq.md)
