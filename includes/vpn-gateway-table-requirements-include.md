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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184146"
---
A tabela seguinte lista os requisitos para gateways VPN baseados em políticas e routebased. Esta tabela aplica-se aos modelos de implementação clássica e Resource Manager. Para o modelo clássico, os gateways VPN baseados em políticas são os mesmos que gateways estáticos, e gateways baseados em rota são os mesmos que gateways Dinâmicos.

|  | **Gateway base VPN baseado em políticas** | **Gateway VPN Básico baseado em rotas** | **Gateway VPN Padrão baseado em rotas** | **Gateway VPN de alto desempenho baseado em rota** |
| --- | --- | --- | --- | --- |
| **Conetividade Site a Site (S2S)** |Configuração VPN baseada em políticas |Configuração VPN baseada em rotas |Configuração VPN baseada em rotas |Configuração VPN baseada em rotas |
| **Conetividade Site a Site (P2S**) |Não suportado |Suportado (pode coexistir com S2S) |Suportado (pode coexistir com S2S) |Suportado (pode coexistir com S2S) |
| **Método de autenticação** |Chave pré-partilhada |Chave pré-partilhada para a conetividade S2S, Certificados para a conetividade P2S |Chave pré-partilhada para a conetividade S2S, Certificados para a conetividade P2S |Chave pré-partilhada para a conetividade S2S, Certificados para a conetividade P2S |
| **Número máximo de ligações S2S** |1 |10 |10 |30 |
| **Número máximo de ligações P2S** |Não suportado |128 |128 |128 |
| **Suporte ativo de encaminhamento (BGP)** (*) |Não suportado |Não suportado |Suportado |Suportado |

  (*) O BGP não é suportado para o modelo clássico de implantação.
