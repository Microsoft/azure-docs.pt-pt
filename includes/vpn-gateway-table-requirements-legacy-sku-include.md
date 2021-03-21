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
ms.openlocfilehash: 5b9e036816aa532d32b1b4305ef6ae646ae05bae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "67184148"
---
A tabela que se segue lista os requisitos para as portas VPN de PolicyBased e RouteBased. Esta tabela aplica-se aos modelos de implementação clássica e Resource Manager. Para o modelo clássico, os gateways VPN de Base Política são os mesmos que os gateways estáticos, e os gateways baseados em rota são os mesmos que os gateways Dinâmicos.

|  | **Gateway VPN Básico De Bases** | **Gateway VPN Básico de Rota** | **RouteBased Standard VPN Gateway** | **RouteBased High Performance VPN Gateway** |
| --- | --- | --- | --- | --- |
| **Conetividade Site a Site (S2S)** |Configuração VPN de VPN com 400 políticas |Configuração VPN de RotaBased |Configuração VPN de RotaBased |Configuração VPN de RotaBased |
| **Conetividade Site a Site (P2S**) |Não suportado |Suportado (pode coexistir com S2S) |Suportado (pode coexistir com S2S) |Suportado (pode coexistir com S2S) |
| **Método de autenticação** |Chave pré-partilhada |Chave pré-partilhada para a conetividade S2S, Certificados para a conetividade P2S |Chave pré-partilhada para a conetividade S2S, Certificados para a conetividade P2S |Chave pré-partilhada para a conetividade S2S, Certificados para a conetividade P2S |
| **Número máximo de ligações S2S** |1 |10 |10 |30 |
| **Número máximo de ligações P2S** |Não suportado |128 |128 |128 |
| **Suporte de encaminhamento ativo (BGP)** |Não suportado |Não suportado |Suportado |Suportado |
