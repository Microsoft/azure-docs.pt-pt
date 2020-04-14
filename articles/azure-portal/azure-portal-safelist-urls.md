---
title: Lista de cofres os URLs do portal Azure no seu firewall ou servidor proxy
description: Adicione estes URLs ao bypass do servidor proxy para comunicar com o portal Azure e seus serviços
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 04/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 399c64c88e78079432fcf7c09dafd199da83358b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255054"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Lista de cofres os URLs do portal Azure no seu firewall ou servidor proxy

Pode configurar dispositivos de segurança no local para contornar as restrições de segurança para os URLs do portal Azure. Esta configuração pode melhorar o desempenho e a conectividade entre a sua rede local ou ampla e a nuvem Azure.

Os administradores de rede frequentemente implementam servidores proxy, firewalls ou outros dispositivos. Estes dispositivos ajudam a proteger e a dar controlo sobre a forma como os utilizadores acedem à internet. As regras destinadas a proteger os utilizadores podem, por vezes, bloquear ou abrandar o tráfego legítimo de internet relacionado com o negócio. Este tráfego inclui comunicações entre si e azure. Para otimizar a conectividade entre a sua rede e o portal Azure e os seus serviços, recomendamos que adicione URLs do portal Azure à sua lista de cofres.

## <a name="azure-portal-urls-for-proxy-bypass"></a>URLs do portal Azure para bypass proxy

Os pontos finais do URL para a lista de segurança para o portal Azure são específicos da nuvem Azure onde a sua organização está implantada. Para permitir que o tráfego de rede para estes pontos finais contorne as restrições, selecione a sua nuvem. Em seguida, adicione a lista de URLs ao seu servidor de procuração ou firewall.

#### <a name="public-cloud"></a>[Nuvem Pública](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azureafd.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloud"></a>[Nuvem do Governo dos E.U.A.](#tab/us-government-cloud)

```
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[Nuvem do Governo da China](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> O tráfego para estes pontos finais utiliza portas TCP padrão para HTTP (80) e HTTPS (443).
>
>
