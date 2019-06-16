---
title: Utilizar o DNS do Azure com outros serviços do Azure | Documentos da Microsoft
description: Compreender como utilizar o DNS do Azure para resolver nomes dos outros serviços do Azure
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: victorh
ms.openlocfilehash: dcf209d2036d2686bea0b51380db3cd2473d04a6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61293197"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Como funciona o DNS do Azure com outros serviços do Azure

O DNS do Azure é um DNS gestão e o nome resolução serviço hospedado. Pode usá-lo a criar nomes DNS públicos para outras aplicações e serviços que implementar no Azure. A criação de um nome para um serviço do Azure no seu domínio personalizado é simple. Basta adicionar um registo do tipo correto para o seu serviço.

* Para endereços IP alocados dinamicamente, pode criar um registo CNAME de DNS que mapeia para o nome DNS que o Azure criou para o seu serviço. Normas DNS o impede de utilizar um registo CNAME para o vértice da zona. Em vez disso, pode usar um registo de alias. Para obter mais informações, consulte [Tutorial: Configurar um registo de alias para fazer referência a um endereço IP público do Azure](tutorial-alias-pip.md).
* Para endereços IP atribuídos estaticamente, pode criar um registo DNS ao utilizar qualquer nome, que inclui um *domínio sem "www"* nome no vértice da zona.

A tabela seguinte descreve os tipos de registo suportados, que pode utilizar para vários serviços do Azure. Como mostra a tabela, o DNS do Azure suporta apenas os registos DNS para recursos de rede com acesso à Internet. Não é possível utilizar o DNS do Azure para resolução de nomes de endereços internas, privadas.

| Serviço do Azure | Interface de rede | Descrição |
| --- | --- | --- |
| Gateway de Aplicação do Azure |[IP público de front-end](dns-custom-domain.md#public-ip-address) |Pode criar um registo de DNS A ou CNAME. |
| Azure Load Balancer |[IP público de front-end](dns-custom-domain.md#public-ip-address) |Pode criar um registo de DNS A ou CNAME. Balanceador de carga pode ter um IPv6 endereço IP público que é dinamicamente atribuído. Crie um registo CNAME para um endereço IPv6. |
| Traffic Manager do Azure |Nome público |Pode criar um registo de alias que mapeia para o nome de trafficmanager.net atribuído ao perfil do Traffic Manager. Para obter mais informações, consulte [Tutorial: Configurar um registo de alias para oferecer suporte a nomes de domínio com o Gestor de tráfego do vértice](tutorial-alias-tm.md). |
| Cloud Services do Azure |[IP público](dns-custom-domain.md#public-ip-address) |Para endereços IP atribuídos estaticamente, pode criar um registo DNS. Para endereços IP alocados dinamicamente, tem de criar um registo CNAME que mapeia para o *cloudapp.net* nome.|
| Serviço de Aplicações do Azure | [IP externo](dns-custom-domain.md#app-service-web-apps) |Para endereços IP externos, pode criar um registo DNS. Caso contrário, tem de criar um registo CNAME que mapeia para o nome de azurewebsites.net. Para obter mais informações, consulte [mapear um nome de domínio personalizado para uma aplicação do Azure](../app-service/app-service-web-tutorial-custom-domain.md). |
| VMs do Azure Resource Manager |[IP público](dns-custom-domain.md#public-ip-address) |VMs do Resource Manager pode ter endereços IP públicos. Uma VM com um endereço IP público também pode estar atrás de um balanceador de carga. Pode criar um DNS A, CNAME ou registo de alias para o endereço público. Pode utilizar este nome personalizado para ignorar o VIP no balanceador de carga. |
| VMs clássicas |[IP público](dns-custom-domain.md#public-ip-address) |As VMs clássicas criadas com o PowerShell ou a CLI podem ser configuradas com um endereço de virtual (reservado) dinâmico ou estático. Pode criar um CNAME DNS ou um registo, respetivamente. |
