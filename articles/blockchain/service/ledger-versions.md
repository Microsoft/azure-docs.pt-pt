---
title: Versões de contabilidade do Azure Blockchain Service, patching, & atualização
description: Visão geral das versões de livros suportados no Azure Blockchain Service, incluindo políticas relativas a patching de sistemas e atualizações geridas pelo sistema e geridas pelo utilizador.
ms.date: 06/02/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: da7907c2c324932ba43863c1074cdff7d54d3827
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84430757"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Versões suportadas do Azure Blockchain Service

O Azure Blockchain Service utiliza o contabilidade [quórum](https://www.goquorum.com/developers) baseado no Ethereum projetado para o processamento de transações privadas dentro de um grupo de participantes conhecidos, identificado como um consórcio no Azure Blockchain Service.

Atualmente, o Azure Blockchain Service suporta a [versão 2.5.0](https://github.com/jpmorganchase/quorum/releases/tag/v2.5.0) do Quorum e [o gestor de transações tessera.](https://github.com/jpmorganchase/tessera)

## <a name="managing-updates-and-upgrades"></a>Gerir atualizações e atualizações

A versão em Quorum é feita através de lançamentos importantes, menores e de patch. Por exemplo, se a versão Qurum for 2.0.1, o tipo de libertação será classificado da seguinte forma:

|Maior | Menor  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

O Azure Blockchain Service atualiza automaticamente as versões de patch do Quorum para os membros de execução existentes no prazo de 30 dias após a disponibilização do Quorum.

## <a name="availability-of-new-ledger-versions"></a>Disponibilidade de novas versões de contabilidade

O Azure Blockchain Service fornece as versões principais e menores do livro de contabilidade Do Quorum no prazo de 60 dias após a sua disponibilização pelo fabricante Quorum. Um máximo de quatro lançamentos menores são fornecidos para que os consórcios escolham quando a provisionar um novo membro e um consórcio. Atualmente, não é suportada a atualização de uma libertação maior ou menor. Por exemplo, se estiver a executar a versão 2.x, um upgrade para a versão 3.x não está suportado. Da mesma forma, se estiver a executar a versão 2.2, atualmente não é suportada uma atualização para a versão 2.3.

## <a name="next-steps"></a>Passos seguintes

[Limites no Serviço Azure Blockchain](limits.md)
