---
title: Introdução ao fluxo de IP, certifique-se no observador de rede do Azure | Documentos da Microsoft
description: Esta página fornece uma descrição geral de integração do observador de rede capacidade de verificação de fluxo
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: d352fb2d-4b4f-4ac4-9c2e-1cfccf0e7e03
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: kumud
ms.openlocfilehash: 88cb7e2cd04d13ade5c581a1ff2dc09669d89ab2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60532599"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Introdução ao fluxo de IP, certifique-se no observador de rede do Azure

A verificação de fluxo de IP verifica se um pacote é permitido ou negado para ou de uma máquina virtual. As informações consistem em direção, protocolo, local IP, remota IP, porta local e porta remota. Se o pacote for recusado por um grupo de segurança, é devolvido o nome da regra que negou o pacote. Enquanto qualquer IP de origem ou de destino pode ser escolhido, o fluxo de IP verificar ajuda a administradores diagnosticar rapidamente problemas de conectividade a partir de ou para a internet e de ou para o ambiente no local.

Verificação do fluxo de IP examina as regras para todos os grupos de segurança de rede (NSGs) aplicadas à interface de rede, por exemplo, uma NIC de sub-redes ou máquinas virtuais. Fluxo de tráfego, em seguida, é verificado com base nas configurações de ou para essa interface de rede. Verificação do fluxo de IP é útil para confirmar se uma regra num grupo de segurança de rede está a bloquear o tráfego de entrada ou saída de ou para uma máquina virtual.

Certifique-se de uma instância do observador de rede tem de ser criada em todas as regiões que pretende executar o fluxo de IP. Observador de rede é um serviço regional e apenas pode ser executado em relação aos recursos na mesma região. A instância utilizada não afeta os resultados de IP verificação do fluxo, como rotas associada ao NIC ou uma sub-rede é ainda ser devolvido.

![1][1]

## <a name="next-steps"></a>Passos Seguintes

Visite o seguinte artigo para saber se um pacote é permitido ou negado para uma máquina virtual específica através do portal. [Verificar se o tráfego é permitido numa VM com IP Flow Certifique-se com o portal](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png

