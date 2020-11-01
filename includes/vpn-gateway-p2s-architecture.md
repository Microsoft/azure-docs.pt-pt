---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/30/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e3ad8fcb6cd5cf68b02ac522d0e5ef5a18ba8a3c
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145005"
---
As ligações de autenticação de certificados Azure nativos ponto a local utilizam os seguintes itens, que configura neste exercício:

* Um gateway de VPN RouteBased.
* A chave pública (ficheiro .cer) de um certificado de raiz que é carregado para o Azure. Assim que o certificado estiver carregado, é considerado um certificado fidedigno e é utilizado para autenticação.
* Um certificado de cliente que é gerado a partir do certificado de raiz. O certificado de cliente instalado em cada computador cliente que vai ligar à VNet. Este certificado é utilizado para autenticação de cliente.
* Configuração do cliente VPN. O cliente VPN está configurado usando ficheiros de configuração de clientes VPN. Estes ficheiros contêm as informações necessárias para que o cliente se conecte ao VNet. Os ficheiros configuram o cliente VPN existente que é nativo ao sistema operativo. Cada cliente que estabelece ligação tem de ser configurado com as definições dos ficheiros de configuração.
