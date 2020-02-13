---
title: Localizações de parceiros Azure Virtual WAN Microsoft Docs
description: Este artigo contém uma lista de parceiros Azure Virtual WAN e localizações de hub.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: afa808c95c88184fb531e858fbfe6d102a6f1f08
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157164"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Parceiros virtuais wan e localizações de hub virtual

Este artigo fornece informações sobre regiões e parceiros apoiados pela WAN virtual para a conectividade no Virtual Hub.

A WAN Virtual do Azure é um serviço de rede que fornece conectividade ramo a ramo otimizada e automatizada através do Azure. A WAN Virtual permite-lhe ligar e configurar dispositivos de ramo para comunicar com o Azure. Isto pode ser feito manualmente, ou utilizando dispositivos de fornecedor através de um parceiro Virtual WAN. A utilização de dispositivos parceiros permite-lhe facilitar o uso, a simplificação da conectividade e a gestão da configuração.

A conectividade do dispositivo no local está estabelecida de forma automatizada para o Centro Virtual. Um hub virtual é uma rede virtual gerida pela Microsoft. O hub contém vários pontos finais de serviço para ativar a conectividade da rede no local (vpnsite). Só se pode ter um centro por região.

## <a name="automation"></a>Automação de parceiros de conectividade

Os dispositivos que se ligam ao Azure Virtual WAN têm automação incorporada para ligar. Isto é tipicamente configurado no UI de gestão de dispositivos (ou equivalente), que configura a conectividade e a gestão de configuração entre o dispositivo de filial VPN para um ponto final vpN hub virtual Azure (gateway VPN).

A seguinte automatização de alto nível é configurada no centro de consola/gestão do dispositivo:

* Permissões adequadas para o dispositivo aceder ao Grupo de Recursos Wan Virtual Azure
* Upload do Dispositivo branch para O WAN Virtual Azure
* Download automático de informações de conectividade Azure
* Configuração do dispositivo de ramificação no local 

Alguns parceiros de conectividade podem estender a automatização para incluir a criação do Azure Virtual Hub VNet e VPN Gateway. Se quiser saber mais sobre automação, consulte [Configure Automation – WAN Partners](virtual-wan-configure-automation-providers.md).

## <a name="partners"></a>Conectividade através de parceiros

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

Os seguintes parceiros estão programados no nosso roteiro para o futuro próximo: Arista, Aruba HPE, Cisco Systems, F5 Networks, Open Systems, Oracle SD-WAN, SharpLink e VMWare Velocloud.

## <a name="locations"></a>Localizações

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Passos seguintes

* Para mais informações sobre o WAN Virtual, consulte o [Virtual WAN FAQ](virtual-wan-faq.md).

* Para obter mais informações sobre como automatizar a conectividade com o Azure Virtual WAN, consulte [Virtual WAN Partners - Como automatizar](virtual-wan-configure-automation-providers.md).
