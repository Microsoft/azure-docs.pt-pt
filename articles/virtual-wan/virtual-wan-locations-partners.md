---
title: WAN Virtual do Azure para parceiros locais | Documentos da Microsoft
description: Este artigo contém uma lista de parceiros de WAN Virtual do Azure e locais de hub.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: f38cd0565b2e90fe0803d8e815c622e22e954a18
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57409709"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Parceiros WAN virtual e locais de virtual hub

Este artigo fornece informações sobre o Virtual WAN suportada regiões e parceiros para conectividade para o Virtual Hub.

A WAN Virtual do Azure é um serviço de rede que fornece conectividade ramo a ramo otimizada e automatizada através do Azure. A WAN Virtual permite-lhe ligar e configurar dispositivos de ramo para comunicar com o Azure. Isso pode ser feito manualmente ou através de dispositivos de fornecedor através de um parceiro de Virtual WAN. Utilizar dispositivos de parceiros, permite que a facilidade de uso, a simplificação da conectividade e gestão de configuração.

De maneira automatizada para o Hub Virtual, é estabelecer a conectividade do dispositivo no local. Um hub virtual é uma rede virtual gerida pela Microsoft. O hub contém vários pontos finais de serviço para ativar a conectividade da rede no local (vpnsite). Só pode ter um hub por região.

## <a name="automation"></a>Automatização de parceiros de conectividade

Os dispositivos que se ligam a WAN Virtual do Azure têm automatização incorporada para se ligar. Isso normalmente é definido se na gestão de dispositivos da interface do Usuário (ou equivalente), que configura o gerenciamento de configuração e de conectividade entre o dispositivo de ramo VPN para um ponto de extremidade de VPN de Hub Virtual do Azure (gateway de VPN).

A automação de alto nível seguinte é configurada no Centro de consola/Gestão de dispositivos:

* Permissões adequadas para o dispositivo aceder ao grupo de recursos de WAN Virtual de Azure
* Carregando-a de dispositivo do ramo no WAN Virtual do Azure
* Transferência automática de informações de conectividade do Azure
* Configuração do dispositivo de ramo no local 

Alguns parceiros de conectividade podem expandir a automatização para incluir a criação da VNet do Hub Virtual do Azure e o Gateway de VPN. Se quiser saber mais sobre a automatização, veja [configurar a automação – parceiros WAN](virtual-wan-configure-automation-providers.md).

## <a name="partners"></a>Conectividade através de parceiros

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

## <a name="locations"></a>localizações

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre o Virtual WAN, consulte a [FAQ de WAN Virtual](virtual-wan-faq.md).

* Para obter mais informações sobre como automatizar a conectividade WAN Virtual do Azure, consulte [parceiros WAN Virtual - como automatizar](virtual-wan-configure-automation-providers.md).
