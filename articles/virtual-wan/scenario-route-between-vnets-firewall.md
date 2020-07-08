---
title: 'Cenário: Encaminhamento personalizado Azure Firewall para WAN Virtual'
titleSuffix: Azure Virtual WAN
description: Cenários para encaminhamento - tráfego de encaminhamento entre VNets diretamente, mas use Azure Firewall para VNet ->Fluxos de tráfego de Internet/Ramo e Ramo para VNet
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: cdaa594a87d960688638591e6edd525aa3b048f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85569300"
---
# <a name="scenario-azure-firewall---custom"></a>Cenário: Azure Firewall - personalizado

Ao trabalhar com o encaminhamento virtual do hub virtual WAN, existem alguns cenários disponíveis. Neste cenário, o objetivo é direcionar o tráfego entre VNets diretamente, mas utilizar o Azure Firewall para fluxos de tráfego VNet-to-Internet/Branch e Branch-to-VNet. Para obter informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="workflow"></a>Fluxo de trabalho de cenário

Neste cenário, pretende-se passar o tráfego através do Azure Firewall para tráfego VNet-to-Internet, VNet-to-Branch ou Branch-to-VNet, mas gostaria de ir diretamente para o tráfego VNet-to-VNet. Se utilizar o Azure Firewall Manager, as definições de rota são automaticamente povoadas na **Tabela de Rotas Predefinidas**. O tráfego privado aplica-se a VNet e Branches, o tráfego de Internet aplica-se a 0.0.0.0/0.

As ligações VPN, ExpressRoute e User VPN são colectivamente chamadas De Ramos e associadas à mesma tabela de rotas (Predefinidos). Todas as ligações VPN, ExpressRoute e User VPN propagam rotas para o mesmo conjunto de tabelas de rotas. A fim de configurar este cenário, tome em consideração os seguintes passos:

1. Crie uma tabela de rota personalizada **RT_VNET**.
1. Crie uma rota para ativar VNet-to-Internet e VNet-to-Branch: 0.0.0.0/0 com o próximo hop apontando para Azure Firewall. Na secção propagação, certifique-se de que os VNets são selecionados, o que garantiria rotas mais específicas, permitindo assim o fluxo de tráfego direto VNet-to-VNet.

   * Em **Associação:** Selecione VNets que implicarão que os VNets chegarão ao destino de acordo com as rotas desta tabela de rotas.
   * Em **Propagação:** Selecione VNets que implicarão que os VNets se propagam a esta tabela de rotas; por outras palavras, rotas mais específicas propagar-se-ão a esta tabela de rotas, garantindo assim o fluxo direto de tráfego entre o VNet e o VNet.

1. Adicione uma rota estática agregada para VNets na **tabela Rota Padrão** para ativar o fluxo Branch-to-VNet através da Firewall Azure. 

   * Lembre-se, os ramos estão associados e propagam-se para a tabela de rotas predefinida.
   * Os ramos não se propagam para RT_VNET tabela de rotas. Isto garante o fluxo de tráfego VNet-to-Branch através da Firewall Azure.

Isto resultará em alterações na configuração do encaminhamento, tal como mostrado na **Figura 1**.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/between-vnets-firewall/routing.png" alt-text="Figura 1":::

## <a name="next-steps"></a>Próximos passos

* Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)
* Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).
* Para obter mais informações sobre como configurar o encaminhamento de hubs virtuais, consulte [Como configurar o encaminhamento do hub virtual](how-to-virtual-hub-routing.md).
