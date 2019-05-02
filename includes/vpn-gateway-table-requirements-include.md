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
ms.openlocfilehash: dc018b5d09c9b33c10cd2d54ac6572537e05ed25
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "64859070"
---
A tabela seguinte lista os requisitos para gateways PolicyBased e RouteBased VPN. Esta tabela aplica-se aos modelos de implementação clássica e Resource Manager. Para o modelo clássico, os gateways de PolicyBased VPN são os mesmos que os gateways estáticos e os gateways baseados na rota são os mesmos que os gateways dinâmicos.

|  | **Gateway de VPN PolicyBased básico** | **Gateway de VPN RouteBased básico** | **Gateway de VPN RouteBased Standard** | **Gateway de VPN RouteBased alto desempenho** |
| --- | --- | --- | --- | --- |
| **Conetividade site a Site (S2S)** |Configuração de PolicyBased VPN |Configuração de RouteBased VPN |Configuração de RouteBased VPN |Configuração de RouteBased VPN |
| **Conetividade Site a Site (P2S**) |Não suportado |Suportado (pode coexistir com S2S) |Suportado (pode coexistir com S2S) |Suportado (pode coexistir com S2S) |
| **Método de autenticação** |Chave pré-partilhada |Chave pré-partilhada para a conetividade S2S, Certificados para a conetividade P2S |Chave pré-partilhada para a conetividade S2S, Certificados para a conetividade P2S |Chave pré-partilhada para a conetividade S2S, Certificados para a conetividade P2S |
| **Número máximo de ligações S2S** |1 |10 |10 |30 |
| **Número máximo de ligações de P2S** |Não suportado |128 |128 |128 |
| **Suporte de encaminhamento ativo (BGP)** (*) |Não suportado |Não suportado |Suportadas |Suportadas |

  (*) O BGP não é suportado para o modelo de implementação clássica.
