---
title: As URLs de portal do Azure imsafelist | Microsoft Docs
description: Adicione essas URLs ao bypass do servidor proxy para se comunicar com o portal do Azure e seus serviços
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3747ca7504e1a8a6bbeb6237c1b3cb2e5e4afb5b
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667478"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>ProSafe a portal do Azure URLs no seu firewall ou servidor proxy

Para obter um bom desempenho e conectividade entre sua rede local ou de longa distância e a nuvem do Azure, configure os dispositivos de segurança locais para ignorar as restrições de segurança para as URLs de portal do Azure. Os administradores de rede geralmente implantam servidores proxy, firewalls ou outros dispositivos para ajudar a proteger e dar controle sobre como os usuários acessam a Internet. No entanto, as regras projetadas para proteger os usuários às vezes podem bloquear ou reduzir o tráfego de Internet legítimo relacionado aos negócios, incluindo comunicações entre você e o Azure. Para otimizar a conectividade entre a rede e o portal do Azure e seus serviços, recomendamos que você adicione portal do Azure URLs à sua assafe.

## <a name="azure-portal-urls-for-proxy-bypass"></a>URLs de portal do Azure para bypass de proxy

Adicione a seguinte lista de URLs ao seu servidor proxy ou firewall para permitir que o tráfego de rede para esses pontos de extremidade ignore as restrições:

* *.aadcdn.microsoftonline-p.com
* *.aimon.applicationinsights.io
* *.azure.com
* *.azuredatalakestore.net
* *.azureedge.net
* *.exp.azure.com
* *.ext.azure.com
* *.gfx.ms
* *.account.microsoft.com
* *.hosting.portal.azure.net
* *.marketplaceapi.microsoft.com
* *.microsoftonline.com
* *.msauth.net
* *.msftauth.net
* *.portal.azure.com
* *.portalext.visualstudio.com
* *.sts.microsoft.com
* *.vortex.data.microsoft.com
* *.vscommerce.visualstudio.com
* *.vssps.visualstudio.com
* \*.windows.net
* *.wpc.azureedge.net

> [!NOTE]
> O tráfego para esses pontos de extremidade usa portas TCP padrão para HTTP (80) e HTTPS (443).
>
>
## <a name="next-steps"></a>Passos seguintes

* Precisa de endereços IP de resafelist? Baixe a lista de [intervalos de IP do datacenter Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* Outros serviços da Microsoft usam URLs e endereços IP adicionais para conectividade. Para otimizar a conectividade de rede para serviços de Microsoft 365, consulte [configurar sua rede para o Office 365](/office365/enterprise/set-up-network-for-office-365).
