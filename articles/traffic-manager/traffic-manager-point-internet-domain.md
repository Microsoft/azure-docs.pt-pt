---
title: Apontar um domínio de Internet para o Gerenciador de tráfego-Gerenciador de tráfego do Azure
description: Este artigo ajuda-o a apontar o nome de domínio da sua empresa para um nome de domínio do Traffic Manager.
services: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: allensu
ms.openlocfilehash: eb656e5a8185951d203ed10ed819ff5ef11e8071
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038055"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Apontar um domínio de Internet da empresa para um domínio do Gestor de Tráfego do Azure

Quando cria um perfil do Gestor de Tráfego, o Azure atribui automaticamente um nome DNS a esse perfil. Para utilizar um nome da sua zona DNS, crie um registo DNS CNAME que mapeia para o nome de domínio do seu perfil do Gestor de Tráfego. Pode encontrar o nome de domínio do Gestor de Tráfego na secção **Geral** na página Configuração do perfil do Gestor de Tráfego.

Por exemplo, para apontar o nome `www.contoso.com` para o nome de DNS do Gestor de Tráfego `contoso.trafficmanager.net`, cria o registo de recursos de DNS seguinte:

    www.contoso.com IN CNAME contoso.trafficmanager.net

Todas as solicitações de tráfego para *www\.contoso.com* são direcionadas para *contoso.trafficmanager.net*.

> [!IMPORTANT]
> Não é possível apontar um domínio de segundo nível, como *contoso.com*, para o domínio do Traffic Manager. Os padrões de protocolo DNS não permitem registos CNAME para nomes de domínio de segundo nível.

## <a name="next-steps"></a>Passos seguintes

* [Métodos de encaminhamento do Gestor de Tráfego](traffic-manager-routing-methods.md)
* [Gestor de Tráfego – Desativar, ativar ou eliminar um perfil](disable-enable-or-delete-a-profile.md)
* [Gestor de Tráfego – Desativar ou ativar um ponto final](disable-or-enable-an-endpoint.md)
