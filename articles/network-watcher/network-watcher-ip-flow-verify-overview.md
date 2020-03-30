---
title: Introdução ao fluxo IP verificado no Vigilante da Rede Azure [ Microsoft Docs
description: Esta página fornece uma visão geral da capacidade de verificação de fluxo IP do Observador de Rede
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: damendo
ms.openlocfilehash: 69aca5e0901a0da8aa98fe310ac220898bf650b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845011"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Introdução ao fluxo IP verificado no Observador da Rede Azure

O fluxo IP verifica se um pacote é permitido ou negado de ou para uma máquina virtual. A informação consiste em direção, protocolo, IP local, IP remoto, porto local e porto remoto. Se o pacote for negado por um grupo de segurança, o nome da regra que negou o pacote é devolvido. Embora qualquer IP de origem ou destino possa ser escolhido, a verificação de fluxos IP ajuda os administradores a diagnosticar rapidamente problemas de conectividade a partir ou à internet e de ou para o ambiente no local.

A verificação do fluxo IP analisa as regras de todos os Grupos de Segurança da Rede (NSGs) aplicadas à interface da rede, tais como uma subnet ou uma máquina virtual NIC. O fluxo de tráfego é então verificado com base nas definições configuradas de ou para a interface da rede. A verificação do fluxo IP é útil para confirmar se uma regra de um Grupo de Segurança de Rede está bloqueando o tráfego de entrada ou de saída de uma máquina virtual.

Uma instância do Network Watcher precisa de ser criada em todas as regiões que pretende executar a verificação do fluxo IP. O Network Watcher é um serviço regional e só pode ser destituda contra recursos na mesma região. A instância utilizada não afeta os resultados da verificação do fluxo ip, uma vez que qualquer rota associada ao NIC ou subnet ainda é devolvida.

![1][1]

## <a name="next-steps"></a>Passos seguintes

Visite o seguinte artigo para saber se um pacote é permitido ou negado para uma máquina virtual específica através do portal. [Verifique se o tráfego é permitido num VM com IP Flow Verifique usando o portal](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png

