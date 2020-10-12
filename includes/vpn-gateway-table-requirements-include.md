---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: dc018b5d09c9b33c10cd2d54ac6572537e05ed25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184146"
---
A tabela que se segue lista os requisitos para as portas VPN de PolicyBased e RouteBased. Esta tabela aplica-se aos modelos de implementação clássica e Resource Manager. Para o modelo clássico, os gateways VPN de Base Política são os mesmos que os gateways estáticos, e os gateways baseados em rota são os mesmos que os gateways Dinâmicos.

|  | **Gateway VPN Básico De Bases** | **Gateway VPN Básico de Rota** | **RouteBased Standard VPN Gateway** | **RouteBased High Performance VPN Gateway** |
| --- | --- | --- | --- | --- |
| **Conetividade Site a Site (S2S)** |Configuração VPN de VPN com 400 políticas |Configuração VPN de RotaBased |Configuração VPN de RotaBased |Configuração VPN de RotaBased |
| **Conetividade Site a Site (P2S**) |Não suportado |Suportado (pode coexistir com S2S) |Suportado (pode coexistir com S2S) |Suportado (pode coexistir com S2S) |
| **Método de autenticação** |Chave pré-partilhada |Chave pré-partilhada para a conetividade S2S, Certificados para a conetividade P2S |Chave pré-partilhada para a conetividade S2S, Certificados para a conetividade P2S |Chave pré-partilhada para a conetividade S2S, Certificados para a conetividade P2S |
| **Número máximo de ligações S2S** |1 |10 |10 |30 |
| **Número máximo de ligações P2S** |Não suportado |128 |128 |128 |
| **Suporte de encaminhamento ativo (BGP)** (*) |Não suportado |Não suportado |Suportado |Suportado |

  (*) O BGP não é suportado para o modelo clássico de implantação.
