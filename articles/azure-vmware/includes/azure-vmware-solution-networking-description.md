---
title: Rede e conectividade Azure VMWare Solution
description: Azure VMWare Solution networking e descrição da conectividade.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 1d43a8a49fc56a6a5b534a89fd1dfc2f5ae2d478
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91574466"
---
<!-- Used in introduction.md and concepts-networking.md -->

A Azure VMware Solution oferece um ambiente de nuvem privada acessível a partir de ambientes ou recursos baseados em Azure. Serviços como as ligações Azure ExpressRoute e VPN proporcionam a conectividade. Estes serviços requerem gamas específicas de endereços de rede e portas de firewall para permitir os serviços.

Ao implementar uma nuvem privada, são criadas redes privadas de gestão, provisionamento e vMotion. Utilize estas redes privadas para aceder ao vCenter e ao NSX-T Manager e à máquina virtual vMotion ou implementação.  O ExpressRoute Global Reach é usado para ligar nuvens privadas a ambientes no local. A ligação requer uma rede virtual com um circuito ExpressRoute na sua subscrição.



>[!NOTE]
>O acesso à internet e aos serviços Azure é fornecido e fornecido para consumir VMs em redes de produção ao implementar uma nuvem privada.  Por predefinição, o acesso à Internet é desativado para novas nuvens privadas e, a qualquer momento, pode ser ativado ou desativado.