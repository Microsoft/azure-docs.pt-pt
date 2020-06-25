---
title: Criar uma lista segura dos URLs do portal do Azure na firewall ou no servidor proxy
description: Adicione estes URLs ao bypass do servidor proxy para comunicar com o portal Azure e os seus serviços
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 04/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 67ccf6e2f113c0ec72cfcbccedf88017e452ec4b
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85361664"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Criar uma lista segura dos URLs do portal do Azure na firewall ou no servidor proxy

Pode configurar dispositivos de segurança no local para contornar as restrições de segurança para os URLs do portal Azure. Esta configuração pode melhorar o desempenho e a conectividade entre a sua rede local ou ampla e a nuvem Azure.

Os administradores de rede frequentemente implantam servidores proxy, firewalls ou outros dispositivos. Estes dispositivos ajudam a proteger e a dar controlo sobre a forma como os utilizadores acedem à internet. As regras destinadas a proteger os utilizadores podem, por vezes, bloquear ou abrandar o tráfego legítimo de internet relacionado com o negócio. Este tráfego inclui comunicações entre si e a Azure. Para otimizar a conectividade entre a sua rede e o portal Azure e os seus serviços, recomendamos que adicione URLs do portal Azure à sua lista de segurança.

## <a name="azure-portal-urls-for-proxy-bypass"></a>URLs do portal Azure para bypass proxy

Os pontos finais do URL para a lista de segurança do portal Azure são específicos da nuvem Azure onde a sua organização é implantada. Para permitir o tráfego de rede a estes pontos finais para contornar as restrições, selecione a sua nuvem. Em seguida, adicione a lista de URLs ao seu servidor ou firewall proxy.

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

#### <a name="us-government-cloud"></a>[Nuvem do governo dos EUA](#tab/us-government-cloud)

```
*.applicationinsights.us
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
