---
title: Parceiros e localizações Azure Virtual WAN Microsoft Docs
description: Este artigo contém uma lista de parceiros Azure Virtual WAN e localizações do hub.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: 2f3641bf540b188ce3d2333ccd6bcf4a926ba130
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565112"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Parceiros virtuais wan e localizações de hub virtual

Este artigo fornece informações sobre regiões e parceiros suportados por WAN virtual para a conectividade no Virtual Hub.

A WAN Virtual do Azure é um serviço de rede que fornece conectividade ramo a ramo otimizada e automatizada através do Azure. A WAN Virtual permite-lhe ligar e configurar dispositivos de ramo para comunicar com o Azure. Isto pode ser feito manualmente, ou utilizando dispositivos de fornecedor através de um parceiro Virtual WAN. A utilização de dispositivos parceiros permite-lhe facilitar a utilização, a simplificação da conectividade e a gestão da configuração.

A conectividade a partir do dispositivo no local é estabelecida de forma automatizada para o Centro Virtual. Um hub virtual é uma rede virtual gerida pela Microsoft. O hub contém vários pontos finais de serviço para ativar a conectividade da rede no local (vpnsite). Só se pode ter um centro por região.

## <a name="automation-from-connectivity-partners"></a><a name="automation"></a>Automação de parceiros de conectividade

Os dispositivos que se ligam ao Azure Virtual WAN têm automatização incorporada para se conectarem. Isto é tipicamente configurado na UI de gestão de dispositivos (ou equivalente), que configura a conectividade e a gestão de configuração entre o dispositivo de filial VPN para um ponto final Azure Virtual Hub VPN (gateway VPN).

A seguinte automatização de alto nível é criada no centro de consola/gestão do dispositivo:

* Permissões adequadas para o dispositivo aceder ao Grupo de Recursos Azure Virtual WAN
* Upload do dispositivo branch para Azure Virtual WAN
* Download automático de informações de conectividade Azure
* Configuração do dispositivo de filial no local 

Alguns parceiros de conectividade podem estender a automatização para incluir a criação do Azure Virtual Hub VNet e VPN Gateway. Se quiser saber mais sobre automação, consulte [as diretrizes de Automação para parceiros VIRTUAIS WAN.](virtual-wan-configure-automation-providers.md)

## <a name="connectivity-through-partners"></a><a name="partners"></a>Conectividade através de parceiros

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

Os seguintes parceiros estão no nosso roteiro para o futuro próximo: 128 Tecnologias, Arista, Aruba HPE, Cisco Systems (Viptela), F5 Networks, Open Systems, Oracle SD-WAN e SharpLink.

## <a name="locations"></a><a name="locations"></a>Localizações

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Próximos passos

* Para obter mais informações sobre o VIRTUAL WAN, consulte o [VIRTUAL WAN FAQ](virtual-wan-faq.md).

* Para obter mais informações sobre como automatizar a conectividade com a Azure Virtual WAN, consulte [as diretrizes de Automação para parceiros VIRTUAIS WAN](virtual-wan-configure-automation-providers.md).
