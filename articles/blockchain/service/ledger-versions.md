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
ms.openlocfilehash: 63c9a8b9e266dacbb0fb6faba50fb44ac9a4b46e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027889"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Versões suportadas de contabilidade de serviço do Azure Blockchain

O serviço do Azure Blockchain usa com base Ethereum [quórum](https://github.com/jpmorganchase/quorum/wiki) contábeis concebido para o processamento de transações privadas dentro de um grupo de participantes conhecidos, identificado como um consórcio no serviço do Azure Blockchain.

Atualmente, suporta o serviço do Azure Blockchain [quórum versão 2.2.1 do buildship](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.1) e [Gestor de transações de Tessera](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Gerenciando atualizações e atualizações

Controle de versão no quórum é feito por meio de uma versão principal, a versão de ponto principal e uma versão ponto irrelevante. Por exemplo, se o quórum versão 2.0.1, 2 é uma versão principal, 0 é uma versão do ponto de principais e 1 é a versão do ponto irrelevante. O serviço automaticamente cria patches para versões de ponto do razão. Atualmente, não são suportadas versões de principais e principal ponto de atualização.

## <a name="availability-of-new-ledger-versions"></a>Disponibilidade de novas versões de contabilidade

O serviço do Azure Blockchain fornece as versões mais recentes do razão no prazo de 60 dias de indisponibilidade do fabricante contábeis. Um máximo de quatro pontos principais lançamentos são fornecidos para consortia à sua escolha quando um novo membro e um consórcio de aprovisionamento.

## <a name="next-steps"></a>Passos Seguintes

[Limites de serviço do Azure Blockchain](limits.md)
