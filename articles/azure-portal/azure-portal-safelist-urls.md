---
title: As URLs de portal do Azure imsafelist | Microsoft Docs
description: Adicione essas URLs ao bypass do servidor proxy para se comunicar com o portal do Azure e seus serviços
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 09/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 9618d99495e65231de22eb719355e2ca75a17600
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71218050"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>ProSafe a portal do Azure URLs no seu firewall ou servidor proxy

Para obter um bom desempenho e conectividade entre sua rede local ou de longa distância e a nuvem do Azure, configure os dispositivos de segurança locais para ignorar as restrições de segurança para as URLs de portal do Azure. Os administradores de rede geralmente implantam servidores proxy, firewalls ou outros dispositivos para ajudar a proteger e dar controle sobre como os usuários acessam a Internet. No entanto, as regras projetadas para proteger os usuários às vezes podem bloquear ou reduzir o tráfego de Internet legítimo relacionado aos negócios, incluindo comunicações entre você e o Azure. Para otimizar a conectividade entre a rede e o portal do Azure e seus serviços, recomendamos que você adicione portal do Azure URLs à sua assafe.

## <a name="azure-portal-urls-for-proxy-bypass"></a>URLs de portal do Azure para bypass de proxy

Os pontos de extremidade de URL para a lista segura para os portal do Azure são específicos para a nuvem do Azure em que sua organização está implantada. Selecione sua nuvem e, em seguida, adicione a lista de URLs ao seu servidor proxy ou firewall para permitir que o tráfego de rede para esses pontos de extremidade ignore as restrições.

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

#### <a name="us-government-cloudtabus-government-cloud"></a>[RELATIVAS Nuvem governamental](#tab/us-government-cloud)
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
* [RELATIVAS Instituições](https://www.microsoft.com/download/details.aspx?id=57063)
* [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064)
* [China](https://www.microsoft.com/download/details.aspx?id=57062)

Outros serviços da Microsoft usam URLs e endereços IP adicionais para conectividade. Para otimizar a conectividade de rede para serviços de Microsoft 365, consulte [configurar sua rede para o Office 365](/office365/enterprise/set-up-network-for-office-365).
