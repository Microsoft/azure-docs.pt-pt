---
title: Versões de livros de contabilidade do Serviço Blockchain Azure, patching, & upgrade
description: Visão geral das versões de livros suportados no Serviço Blockchain Azure, incluindo políticas relativas a patching de sistemas e atualizações geridas pelo sistema e geridas pelo utilizador.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 8d4a77699dd80743acfadd4d72d6d75bc1939b3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74325177"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Versões de contabilidade do Serviço Azure Blockchain suportadas

O Azure Blockchain Service utiliza o livro-razão [Quorum](https://www.goquorum.com/developers) baseado no Ethereum projetado para o processamento de transações privadas dentro de um grupo de participantes conhecidos, identificado como um consórcio no Serviço Azure Blockchain.

Atualmente, o Azure Blockchain Service suporta a [versão Quorum 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) e o gestor de [transações tessera.](https://github.com/jpmorganchase/tessera)

## <a name="managing-updates-and-upgrades"></a>Gestão de atualizações e atualizações

A versão em Quorum é feita através de um lançamento de patch maior, menor e patch. Por exemplo, se a versão Quorum for de 2.0.1, o tipo de libertação seria categorizado da seguinte forma:

|Maior | Menor  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

O Azure Blockchain Service atualiza automaticamente as versões de patch do Quorum aos membros em execução existentes no prazo de 30 dias após a disponibilização do Quorum.

## <a name="availability-of-new-ledger-versions"></a>Disponibilidade de novas versões de livros

O Azure Blockchain Service fornece as mais recentes versões principais e menores do livro quorum no prazo de 60 dias após a sua disponibilização do fabricante quorum. São previstas no máximo quatro pequenas libertações para que os consórcios possam escolher quando forem provisionados um novo membro e consórcio. Atualmente, a atualização de uma libertação importante ou menor não é suportada. Por exemplo, se estiver a executar a versão 2.x, um upgrade para a versão 3.x não é atualmente suportado. Da mesma forma, se estiver a executar a versão 2.2, não é suportado atualmente um upgrade para a versão 2.3.

## <a name="next-steps"></a>Passos seguintes

[Limites no Serviço Blockchain Azure](limits.md)
