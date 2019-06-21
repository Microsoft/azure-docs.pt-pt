---
title: Lista segura o portal do Azure URLs | Documentos da Microsoft
description: Adicione estes URLs para a chamada direta do servidor proxy para comunicar com o portal do Azure e seus serviços
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 06/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 87ec600a2f6c4a560ec7cbb064b561fa76e2b615
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305191"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Lista segura o portal do Azure URLs em seu servidor de firewall ou proxy

Para o bom desempenho e a conectividade entre a rede de área local ou em toda a e a cloud do Azure, configurar dispositivos de segurança no local para ignorar as restrições de segurança para o portal do Azure URLs. Os administradores de rede, muitas vezes, implementam servidores de proxy, firewalls ou outros dispositivos para ajudar a proteger e dar controlo sobre a forma como os utilizadores acedem à internet. No entanto, as regras concebidas para proteger os usuários, às vezes, podem bloquear ou abrandar legítimos relacionados com a empresa tráfego de internet, incluindo comunicações entre e o Azure. Para otimizar a conectividade entre a rede e o portal do Azure e seus serviços, recomendamos que adicionar o portal do Azure URLs para a sua lista segura.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Ignorar de URLs de proxy do portal do Azure

Adicione a seguinte lista de URLs para o seu servidor proxy ou firewall para permitir o tráfego de rede para estes pontos finais para ignorar as restrições:

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
* *.wpc.azureedge.net

> [!NOTE]
> O tráfego para estes pontos finais utiliza portas não padrão de TCP para HTTP (80) e o HTTPS (443).
>
>
## <a name="next-steps"></a>Passos Seguintes

* Precisa de endereços IP da lista segura? Transferir a lista de [intervalos IP de datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* Outro Microsoft services utilize endereços IP e URLs adicionais para conectividade. Para otimizar a conectividade de rede para serviços do Microsoft 365, consulte [configurar a sua rede para o Office 365](/office365/enterprise/set-up-network-for-office-365).
