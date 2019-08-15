---
title: Locais de parceiros de WAN virtual do Azure | Microsoft Docs
description: Este artigo contém uma lista de parceiros de WAN virtual do Azure e locais de Hub.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: d9f0bb7d3817b943b71715eb080dd0b060efa025
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990337"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Parceiros de WAN virtual e locais de Hub virtual

Este artigo fornece informações sobre regiões e parceiros virtuais com suporte da WAN para conectividade com o Hub virtual.

A WAN Virtual do Azure é um serviço de rede que fornece conectividade ramo a ramo otimizada e automatizada através do Azure. A WAN Virtual permite-lhe ligar e configurar dispositivos de ramo para comunicar com o Azure. Isso pode ser feito manualmente ou usando dispositivos de provedor por meio de um parceiro de WAN virtual. O uso de dispositivos de parceiros permite facilitar o uso, simplificar a conectividade e o gerenciamento de configuração.

A conectividade do dispositivo local é estabelecida de maneira automatizada para o Hub virtual. Um hub virtual é uma rede virtual gerenciada pela Microsoft. O hub contém vários pontos finais de serviço para ativar a conectividade da rede no local (vpnsite). Você só pode ter um hub por região.

## <a name="automation"></a>Automação de parceiros de conectividade

Dispositivos que se conectam à WAN virtual do Azure têm automação interna para se conectar. Normalmente, isso é configurado na interface do usuário de gerenciamento de dispositivo (ou equivalente), que configura o gerenciamento de conectividade e configuração entre o dispositivo de ramificação VPN para um ponto de extremidade de VPN de Hub virtual do Azure (gateway de VPN).

A seguinte automação de alto nível é configurada no console do dispositivo/centro de gerenciamento:

* Permissões apropriadas para o dispositivo acessar o grupo de recursos de WAN virtual do Azure
* Carregando o dispositivo de ramificação na WAN virtual do Azure
* Download automático de informações de conectividade do Azure
* Configuração do dispositivo de ramificação local 

Alguns parceiros de conectividade podem estender a automação para incluir a criação da VNet do Hub virtual do Azure e do gateway de VPN. Se você quiser saber mais sobre a automação, consulte [Configurar automação – parceiros de WAN](virtual-wan-configure-automation-providers.md).

## <a name="partners"></a>Conectividade por meio de parceiros

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

Os parceiros a seguir são candidatos a nosso roteiro para o futuro próximo: Arista, F5 Networks, Fortinet, prata-pico, Velocloud, Versaa, Nuage Nokia.

## <a name="locations"></a>Locais

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre WAN virtual, consulte as [perguntas frequentes sobre Wan virtual](virtual-wan-faq.md).

* Para obter mais informações sobre como automatizar a conectividade com a WAN virtual do Azure, consulte [parceiros de WAN virtual-como automatizar](virtual-wan-configure-automation-providers.md).
