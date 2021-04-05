---
title: Criar uma lista segura dos URLs do portal do Azure na firewall ou no servidor proxy
description: Adicione estes URLs ao bypass do servidor proxy para comunicar com o portal Azure e os seus serviços
ms.date: 04/10/2020
ms.topic: conceptual
ms.openlocfilehash: 7d9c8222ee85c0c16ec1e1926335ac06e0389797
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745881"
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
*.azurefd.net
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
