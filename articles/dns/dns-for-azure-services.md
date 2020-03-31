---
title: Utilize o Azure DNS com outros serviços Azure
description: Neste caminho de aprendizagem, começar a usar o DNS Azure para resolver nomes para outros serviços Azure
services: dns
documentationcenter: na
author: rohinkoul
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
ms.author: rohink
ms.openlocfilehash: fa2c1ced6405c967ca33562d6215b304b8507e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937239"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Como o Azure DNS trabalha com outros serviços azure

Azure DNS é um serviço de gestão e resolução de nomes do DNS. Pode usá-lo para criar nomes públicos de DNS para outras aplicações e serviços que implementa no Azure. Criar um nome para um serviço Azure no seu domínio personalizado é simples. Basta adicionar um registo do tipo correto para o seu serviço.

* Para endereços IP dinamicamente atribuídos, pode criar um registo DNS CNAME que mapeie para o nome DNS que o Azure criou para o seu serviço. As normas DNS impedem-no de utilizar um registo CNAME para o ápice da zona. Em vez disso, podes usar um pseudónimo. Para mais informações, consulte [Tutorial: Configure um registo de pseudónimo para se referir a um endereço IP público azure](tutorial-alias-pip.md).
* Para endereços IP distribuídos estáticamente, pode criar um registo DNS A utilizando qualquer nome, que inclui um nome de *domínio nu* no ápice da zona.

A tabela seguinte descreve os tipos de registo suportados que pode utilizar para vários serviços Azure. Como mostra a tabela, o Azure DNS suporta apenas registos DNS para recursos de rede virados para a Internet. O DNS Azure não pode ser usado para resolução de nomes de endereços internos e privados.

| Serviço do Azure | Interface de rede | Descrição |
| --- | --- | --- |
| Gateway de Aplicação do Azure |[IP público frontal](dns-custom-domain.md#public-ip-address) |Pode criar um disco DNS A ou CNAME. |
| Azure Load Balancer |[IP público frontal](dns-custom-domain.md#public-ip-address) |Pode criar um disco DNS A ou CNAME. O Load Balancer pode ter um endereço IP público IPv6 que é atribuído dinamicamente. Crie um disco CNAME para um endereço IPv6. |
| Traffic Manager do Azure |Nome público |Pode criar um registo de pseudónimos que mapeie o nome trafficmanager.net atribuído ao seu perfil de Gestor de Tráfego. Para mais informações, consulte [Tutorial: Configure um registo](tutorial-alias-tm.md)de pseudónimo para apoiar nomes de domínio apex com Traffic Manager . |
| Cloud Services do Azure |[IP público](dns-custom-domain.md#public-ip-address) |Para endereços IP distribuídos estáticamente, pode criar um registo DNS A. Para endereços IP dinamicamente atribuídos, deve criar um registo CNAME que mapeie para o nome *cloudapp.net.*|
| App Service do Azure | [IP externo](dns-custom-domain.md#app-service-web-apps) |Para endereços IP externos, pode criar um registo DNS A. Caso contrário, deve criar um registo CNAME que mapeie para o nome azurewebsites.net. Para mais informações, consulte [mapeie um nome de domínio personalizado para uma aplicação Azure](../app-service/app-service-web-tutorial-custom-domain.md). |
| VMs do Gestor de Recursos Azure |[IP público](dns-custom-domain.md#public-ip-address) |Os VMs do Gestor de Recursos podem ter endereços IP públicos. Um VM com um endereço IP público também pode estar por trás de um equilibrador de carga. Pode criar um registo dNS A, CNAME ou pseudónimo para o endereço público. Pode utilizar este nome personalizado para contornar o VIP no equilibrador de carga. |
| VMs clássicas |[IP público](dns-custom-domain.md#public-ip-address) |Os VMclássicos criados através da utilização do PowerShell ou do CLI podem ser configurados com um endereço virtual dinâmico ou estático (reservado). Pode criar um DNS CNAME ou um disco A, respectivamente. |
