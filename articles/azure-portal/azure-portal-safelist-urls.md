---
title: ProSafe a portal do Azure URLs no seu firewall ou servidor proxy
description: Adicione essas URLs ao bypass do servidor proxy para se comunicar com o portal do Azure e seus serviços
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5bba7296a05cfbb72698a991ece1ef298689bd1
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900655"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>ProSafe a portal do Azure URLs no seu firewall ou servidor proxy

Pode configurar dispositivos de segurança no local para contornar as restrições de segurança para os URLs do portal Azure. Esta configuração pode melhorar o desempenho e a conectividade entre a sua rede local ou ampla e a nuvem Azure.

Os administradores de rede frequentemente implementam servidores proxy, firewalls ou outros dispositivos. Estes dispositivos ajudam a proteger e a dar controlo sobre a forma como os utilizadores acedem à internet. As regras destinadas a proteger os utilizadores podem, por vezes, bloquear ou abrandar o tráfego legítimo de internet relacionado com o negócio. Este tráfego inclui comunicações entre si e azure. Para otimizar a conectividade entre a rede e o portal do Azure e seus serviços, recomendamos que você adicione portal do Azure URLs à sua assafe.

## <a name="azure-portal-urls-for-proxy-bypass"></a>URLs de portal do Azure para bypass de proxy

Os pontos de extremidade de URL para a lista segura para os portal do Azure são específicos para a nuvem do Azure em que sua organização está implantada. Para permitir que o tráfego de rede para estes pontos finais contorne as restrições, selecione a sua nuvem. Em seguida, adicione a lista de URLs ao seu servidor de procuração ou firewall.

#### <a name="public-cloudtabpublic-cloud"></a>[Nuvem pública](#tab/public-cloud)

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

#### <a name="us-government-cloudtabus-government-cloud"></a>[Nuvem do governo dos EUA](#tab/us-government-cloud)

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

#### <a name="china-government-cloudtabchina-government-cloud"></a>[Nuvem do governo da China](#tab/china-government-cloud)

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
> O tráfego para esses pontos de extremidade usa portas TCP padrão para HTTP (80) e HTTPS (443).
>
>
## <a name="next-steps"></a>Passos seguintes

Precisa de endereços IP de resafelist? Baixe a lista de intervalos de IP do datacenter Microsoft Azure para sua nuvem:

* [Pelo](https://www.microsoft.com/download/details.aspx?id=56519)
* [Governo dos EUA](https://www.microsoft.com/download/details.aspx?id=57063)
* [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064)
* [China](https://www.microsoft.com/download/details.aspx?id=57062)

Outros serviços da Microsoft usam URLs e endereços IP adicionais para conectividade. Para otimizar a conectividade de rede para serviços de Microsoft 365, consulte [configurar sua rede para o Office 365](/office365/enterprise/set-up-network-for-office-365).
