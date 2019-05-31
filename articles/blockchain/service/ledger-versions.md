---
title: O serviço do Azure Blockchain suportada de versões de contabilidade, aplicação de patches e a atualização
description: Descrição geral das versões de livros razão suportados no serviço de Blockchain do Azure, incluindo políticas em relação a sistemas gerenciados pelo sistema e de aplicação de patches e atualizações geridas pelo utilizador.
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 53f65ec91a1e0f1e5a6322f0125bf83cd3e400b2
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399110"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Versões suportadas de contabilidade de serviço do Azure Blockchain

O serviço do Azure Blockchain usa com base Ethereum [quórum](https://www.goquorum.com/developers) contábeis concebido para o processamento de transações privadas dentro de um grupo de participantes conhecidos, identificado como um consórcio no serviço do Azure Blockchain.

Atualmente, suporta o serviço do Azure Blockchain [quórum versão 2.2.1 do buildship](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.1) e [Gestor de transações de Tessera](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Gerenciando atualizações e atualizações

Versões de patch e controle de versão no quórum é feito por meio de um principal, secundária. Por exemplo, se a versão de quórum 2.0.1, tipo de versão seria ser classificado da seguinte forma:

|Principais | Pequenas  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

O serviço do Azure Blockchain atualiza automaticamente as versões de patch do quórum para membros em execução existentes em 30 dias a ser disponibilizada de quórum.

## <a name="availability-of-new-ledger-versions"></a>Disponibilidade de novas versões de contabilidade

O serviço do Azure Blockchain fornece o mais recente versão principal e secundária da razão de quórum no prazo de 60 dias de indisponibilidade do fabricante de quórum. Um máximo de quatro versões secundárias são fornecidos para consortia à sua escolha quando um novo membro e um consórcio de aprovisionamento. Versão de atualização para uma versão principal ou secundária não é atualmente suportada.

## <a name="next-steps"></a>Passos Seguintes

[Limites de serviço do Azure Blockchain](limits.md)
