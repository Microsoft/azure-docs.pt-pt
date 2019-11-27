---
title: Usar o DNS do Azure com outros serviços do Azure
description: Neste roteiro de aprendizagem, comece a usar o DNS do Azure para resolver nomes de outros serviços do Azure
services: dns
documentationcenter: na
author: asudbring
manager: kumudD
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: allensu
ms.openlocfilehash: 142157273bd24912311383785d08177abfd04398
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74211888"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Como o DNS do Azure funciona com outros serviços do Azure

O DNS do Azure é um serviço de gerenciamento de DNS hospedado e de resolução de nomes. Você pode usá-lo para criar nomes DNS públicos para outros aplicativos e serviços implantados no Azure. É simples criar um nome para um serviço do Azure em seu domínio personalizado. Basta adicionar um registro do tipo correto para seu serviço.

* Para endereços IP alocados dinamicamente, você pode criar um registro DNS CNAME que mapeia para o nome DNS criado pelo Azure para seu serviço. Os padrões de DNS impedem que você use um registro CNAME para o Apex da zona. Em vez disso, você pode usar um registro de alias. Para obter mais informações, consulte [tutorial: configurar um registro de alias para se referir a um endereço IP público do Azure](tutorial-alias-pip.md).
* Para endereços IP alocados estaticamente, você pode criar um registro de DNS A usando qualquer nome, que inclui um nome de *domínio descoberto* no Apex da zona.

A tabela a seguir descreve os tipos de registros com suporte que você pode usar para vários serviços do Azure. Como mostra a tabela, o DNS do Azure dá suporte apenas a registros DNS para recursos de rede voltados para a Internet. O DNS do Azure não pode ser usado para a resolução de nomes de endereços internos e privados.

| Serviço do Azure | Interface de rede | Descrição |
| --- | --- | --- |
| Gateway de Aplicação do Azure |[IP público de front-end](dns-custom-domain.md#public-ip-address) |Você pode criar um registro DNS A ou CNAME. |
| Azure Load Balancer |[IP público de front-end](dns-custom-domain.md#public-ip-address) |Você pode criar um registro DNS A ou CNAME. Load Balancer pode ter um endereço IP público IPv6 atribuído dinamicamente. Crie um registro CNAME para um endereço IPv6. |
| Traffic Manager do Azure |Nome público |Você pode criar um registro de alias que mapeia para o nome trafficmanager.net atribuído ao seu perfil do Gerenciador de tráfego. Para obter mais informações, consulte [tutorial: configurar um registro de alias para dar suporte a nomes de domínio Apex com o Gerenciador de tráfego](tutorial-alias-tm.md). |
| Cloud Services do Azure |[IP público](dns-custom-domain.md#public-ip-address) |Para endereços IP alocados estaticamente, você pode criar um registro A de DNS. Para endereços IP alocados dinamicamente, você deve criar um registro CNAME que é mapeado para o nome *cloudapp.net* .|
| Serviço de Aplicações do Azure | [IP externo](dns-custom-domain.md#app-service-web-apps) |Para endereços IP externos, você pode criar um registro A DNS. Caso contrário, você deve criar um registro CNAME que é mapeado para o nome do azurewebsites.net. Para obter mais informações, consulte [mapear um nome de domínio personalizado para um aplicativo do Azure](../app-service/app-service-web-tutorial-custom-domain.md). |
| Azure Resource Manager VMs |[IP público](dns-custom-domain.md#public-ip-address) |As VMs do Gerenciador de recursos podem ter endereços IP públicos. Uma VM com um endereço IP público também pode estar atrás de um balanceador de carga. Você pode criar um registro DNS A, CNAME ou alias para o endereço público. Você pode usar esse nome personalizado para ignorar o VIP no balanceador de carga. |
| VMs clássicas |[IP público](dns-custom-domain.md#public-ip-address) |As VMs clássicas criadas usando o PowerShell ou a CLI podem ser configuradas com um endereço virtual dinâmico ou estático (reservado). Você pode criar um DNS CNAME ou um registro A, respectivamente. |
