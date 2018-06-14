---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5e7e365a73c9ba9cfd91d4f93759e7af55a0ad48
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2018
ms.locfileid: "30929330"
---
A tabela seguinte lista os requisitos para gateways PolicyBased e de RouteBased VPN. Esta tabela aplica-se aos modelos de implementação clássica e Resource Manager. Para o modelo clássico, os gateways de PolicyBased VPN são os mesmos que os gateways estáticos e os gateways baseados na rota são os mesmos que os gateways dinâmicos.

|  | **Gateway de VPN PolicyBased básico** | **Gateway de VPN RouteBased básico** | **Gateway de VPN RouteBased padrão** | **Gateway de VPN RouteBased elevado desempenho** |
| --- | --- | --- | --- | --- |
| **Conetividade site a Site (S2S)** |Configuração de PolicyBased VPN |Configuração de RouteBased VPN |Configuração de RouteBased VPN |Configuração de RouteBased VPN |
| **Conetividade Site a Site (P2S**) |Não suportado |Suportado (pode coexistir com S2S) |Suportado (pode coexistir com S2S) |Suportado (pode coexistir com S2S) |
| **Método de autenticação** |Chave pré-partilhada |Chave pré-partilhada para a conetividade S2S, Certificados para a conetividade P2S |Chave pré-partilhada para a conetividade S2S, Certificados para a conetividade P2S |Chave pré-partilhada para a conetividade S2S, Certificados para a conetividade P2S |
| **Número máximo de ligações S2S** |1 |10 |10 |30 |
| **Número máximo de ligações P2S** |Não suportado |128 |128 |128 |
| **Suporte de encaminhamento ativo (BGP)** (*) |Não suportado |Não suportado |Suportadas |Suportadas |

  (*) BGP não é suportado para o modelo de implementação clássica.
