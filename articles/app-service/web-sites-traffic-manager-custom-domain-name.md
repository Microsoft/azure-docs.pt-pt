---
title: Configurar nomes DNS para aplicativos que usam o serviço de Azure App do Traffic Manager
description: Use um nome de domínio personalizado para um aplicativo Web no serviço Azure App que inclua o Gerenciador de tráfego para balanceamento de carga.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e6a59d519ae8bb515ab16632bd39509682959f50
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074021"
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Configurando um nome de domínio personalizado para um aplicativo Web no serviço Azure App usando o Gerenciador de tráfego
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artigo fornece instruções genéricas para usar um nome de domínio personalizado com um aplicativo do [serviço de aplicativo](overview.md) que é integrado ao [Gerenciador de tráfego](../traffic-manager/traffic-manager-overview.md) para balanceamento de carga.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>Noções básicas sobre registros DNS
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Configurar seus aplicativos Web para o modo padrão
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Adicionar um registro DNS para seu domínio personalizado
> [!NOTE]
> Se você comprou o domínio por meio de aplicativos Web do serviço Azure App, ignore as etapas a seguir e consulte a etapa final do artigo [comprar domínio para aplicativos Web](manage-custom-dns-buy-domain.md) .
> 
> 

Para associar seu domínio personalizado a um aplicativo Web no serviço Azure App, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado. Você faz isso usando as ferramentas de gerenciamento do seu provedor de domínio.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Embora as especificidades de cada provedor de domínio variem, você mapeia de seu nome *de* domínio personalizado (como **contoso.com**) *para* o nome de domínio do Traffic Manager (**contoso.trafficmanager.net**) que é integrado ao seu aplicativo Web.

> [!NOTE]
> Se um registro já estiver em uso e você precisar ligar preventivamente seus aplicativos a ele, você poderá criar um registro CNAME adicional. Por exemplo, para ligar preemptivamente **o\.contoso.com da www** ao seu aplicativo Web, crie um registro CNAME de **awverify. www** para **contoso.trafficmanager.net**. Em seguida, você pode adicionar\."www contoso.com" ao seu aplicativo Web sem alterar o registro CNAME "www". Para obter mais informações, consulte [criar registros DNS para um aplicativo Web em um domínio personalizado][CREATEDNS].

Depois de terminar de adicionar ou modificar os registros DNS no seu provedor de domínio, salve as alterações.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Habilitar o Gerenciador de tráfego
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações, consulte o [Centro para Programadores do Node.js](https://azure.microsoft.com/develop/nodejs/).

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
