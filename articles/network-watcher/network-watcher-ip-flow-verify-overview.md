---
title: Introdução ao fluxo IP verificar em Azure Network Watcher | Microsoft Docs
description: Esta página fornece uma visão geral da capacidade de verificação do fluxo IP do Observador de Rede
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: d6f6fe81c121f547f79fa34be77aab1fb6c0875a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899359"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Introdução ao fluxo IP verificar no Observador de Redes Azure

O fluxo IP verifique se um pacote é permitido ou negado a ou a partir de uma máquina virtual. A informação consiste em direção, protocolo, IP local, IP remoto, porto local e porta remota. Se o pacote for negado por um grupo de segurança, o nome da regra que negou o pacote é devolvido. Embora qualquer IP de origem ou destino possa ser escolhido, o fluxo IP verifique ajuda os administradores a diagnosticar rapidamente problemas de conectividade de ou para a internet e de ou para o ambiente no local.

Verificação do fluxo IP as regras de todos os Grupos de Segurança da Rede (NSGs) aplicadas à interface de rede, tais como uma sub-rede ou uma máquina virtual NIC. O fluxo de tráfego é verificado com base nas definições configuradas de ou para essa interface de rede. A verificação do fluxo IP é útil para confirmar se uma regra de um Grupo de Segurança de Rede está bloqueando o tráfego de entrada ou saída de uma máquina virtual.

Um caso de Network Watcher precisa de ser criado em todas as regiões que planeia executar a verificação do fluxo IP. O Network Watcher é um serviço regional e só pode ser utilizado contra recursos na mesma região. A instância utilizada não afeta os resultados do fluxo IP verificar, uma vez que qualquer rota associada ao NIC ou sub-rede ainda é devolvida.

![1][1]

## <a name="next-steps"></a>Passos seguintes

Visite o seguinte artigo para saber se um pacote é permitido ou negado para uma máquina virtual específica através do portal. [Verifique se o tráfego é permitido num VM com fluxo IP Verifique usando o portal](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png

