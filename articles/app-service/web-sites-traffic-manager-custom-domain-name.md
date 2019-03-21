---
title: Configurar nomes DNS para aplicações que utilizam o Gestor de tráfego - serviço de aplicações do Azure
description: Utilizar um nome de domínio personalizado para um uma aplicação web no serviço de aplicações do Azure, que inclui o Gestor de tráfego para balanceamento de carga.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b207d9e3123245e5cb7dddb625f7488886591ae5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58089867"
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Configurar um nome de domínio personalizado para uma aplicação web no serviço de aplicações do Azure utilizando o Gestor de tráfego
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artigo fornece instruções genéricas para utilizar um nome de domínio personalizado com uma [serviço de aplicações](overview.md) aplicação integrada com [Gestor de tráfego](../traffic-manager/traffic-manager-overview.md) para balanceamento de carga.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>Compreender os registos DNS
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Configurar as suas aplicações web para o modo padrão
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Adicionar um registo DNS para o seu domínio personalizado
> [!NOTE]
> Se tiver adquirido o domínio através de aplicações de Web do serviço de aplicações do Azure, em seguida, ignore os passos seguintes e consulte o passo final da [comprar o domínio para aplicações Web](manage-custom-dns-buy-domain.md) artigo.
> 
> 

Para associar o seu domínio personalizado a uma aplicação web no App Service do Azure, tem de adicionar uma nova entrada na tabela de DNS para o seu domínio personalizado. Pode fazê-lo usando as ferramentas de gestão do fornecedor de domínio.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Enquanto as especificidades de cada fornecedor de domínio podem variar, mapear *partir* seu nome de domínio personalizado (tal como **contoso.com**) *para* o nome de domínio do Gestor de tráfego ( **contoso.trafficmanager.NET**) que está integrada com a sua aplicação web.

> [!NOTE]
> Se um registo já está em utilização e precisa vincular preventivamente as suas aplicações, pode criar um registo CNAME adicional. Por exemplo, para vincular preventivamente **www\.contoso.com** à sua aplicação web, criar um registo CNAME de **awverify.www** para **contoso.trafficmanager.net**. Em seguida, pode adicionar "www\.contoso.com" à sua aplicação Web sem alterar o registo CNAME "www". Para obter mais informações, consulte [registos de DNS de criar para uma aplicação web num domínio personalizado][CREATEDNS].

Quando terminar de adicionar ou modificar registos DNS no seu fornecedor de domínios, guarde as alterações.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Ativar o Gestor de tráfego
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações, consulte o [Centro para Programadores do Node.js](https://azure.microsoft.com/develop/nodejs/).

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
