---
title: Sobre suportes do Azure Route Server (Preview) para ExpressRoute e Azure VPN
description: Saiba como o Azure Route Server interage com as portas ExpressRoute e Azure VPN.
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 6e588c7c0381c6825bcf75cbbe28a1dd6b865940
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101680534"
---
# <a name="about-azure-route-server-preview-support-for-expressroute-and-azure-vpn"></a>Sobre o suporte do Azure Route Server (Preview) para ExpressRoute e Azure VPN

O Azure Route Server suporta não só aparelhos virtuais de rede de terceiros (NVA) em funcionamento no Azure, mas também se integra perfeitamente com gateways ExpressRoute e Azure VPN. Não precisa de configurar ou gerir o perspível BGP entre o gateway e o Azure Route Server. Pode ativar a troca de rotas entre o Gateway e o Azure Route Server com uma simples [alteração de configuração](quickstart-configure-route-server-powershell.md#route-exchange).

> [!IMPORTANT]
> O Azure Route Server (Preview) encontra-se atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-it-work"></a>Como funciona?

Quando implementa um Azure Route Server juntamente com um gateway ExpressRoute e um NVA numa rede virtual por padrão Azure Route Server não propaga as rotas que recebe do gateway NVA e ExpressRoute entre si. Assim que ativar a troca de rotas, o ExpressRoute e a NVA aprenderão as rotas uns dos outros.

Por exemplo, no seguinte diagrama:

* O aparelho SDWAN receberá do Azure Route Server a rota de "On-prem 2", que está ligada ao ExpressRoute, juntamente com a rota de rede virtual.

* O gateway ExpressRoute receberá a rota de "On-prem 1", que está ligada ao aparelho SDWAN, juntamente com a rota de rede virtual do Azure Route Server.

    ![Diagrama mostrando ExpressRoute configurado com O Servidor de Rota.](./media/expressroute-vpn-support/expressroute-with-route-server.png)

Também pode substituir o aparelho SDWAN por gateway Azure VPN. Uma vez que o gateway Azure VPN e o ExpressRoute estão totalmente geridos, basta permitir que a troca de rotas para as duas redes no local falem entre si.

> [!IMPORTANT] 
> O gateway Azure VPN deve ser configurado em modo [**ativo.**](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md)
>

![Diagrama mostrando porta expressRoute e VPN configurado com Route Server.](./media/expressroute-vpn-support/expressroute-and-vpn-with-route-server.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Azure Route Server](route-server-faq.md).
- Saiba como [configurar o Azure Route Server](quickstart-configure-route-server-powershell.md).
- Saiba mais sobre [a coexistência Azure ExpressRoute e Azure VPN.](../expressroute/expressroute-howto-coexist-resource-manager.md)
