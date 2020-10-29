---
title: Rede de solução VMware Azure e conectividade
description: Azure VMware Solution networking e descrição da conectividade.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 47b5fab8430f4e462f1e4a0e8cb6ddb69ec38c02
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92924960"
---
<!-- Used in introduction.md and concepts-networking.md -->

A Azure VMware Solution oferece um ambiente de nuvem privada acessível a partir de ambientes ou recursos baseados em Azure. Serviços como as ligações Azure ExpressRoute e VPN proporcionam a conectividade. Estes serviços requerem gamas específicas de endereços de rede e portas de firewall para permitir os serviços.

Ao implementar uma nuvem privada, são criadas redes privadas de gestão, provisionamento e vMotion. Utilize estas redes privadas para aceder ao vCenter e ao NSX-T Manager e à máquina virtual vMotion ou implementação.  O ExpressRoute Global Reach é usado para ligar nuvens privadas a ambientes no local. A ligação requer uma rede virtual com um circuito ExpressRoute na sua subscrição.

Os recursos, como servidores web e máquinas virtuais, estão acessíveis à internet através da funcionalidade IP pública Azure Virtual WAN.  Por padrão, o acesso à Internet é desativado para novas nuvens privadas. Para obter mais informações, consulte [como utilizar a funcionalidade IP pública na Solução VMware Azure.](../public-ip-usage.md)