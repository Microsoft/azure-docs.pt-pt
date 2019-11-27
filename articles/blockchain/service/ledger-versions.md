---
title: Versões do razão do serviço Blockchain do Azure, aplicação de patches, atualização &
description: Visão geral das versões de razões com suporte no serviço Blockchain do Azure, incluindo políticas relacionadas à aplicação de patches de sistemas e atualizações gerenciadas pelo usuário e pelo sistema.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 8d4a77699dd80743acfadd4d72d6d75bc1939b3b
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325177"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Versões do razão do serviço Blockchain do Azure com suporte

O serviço Blockchain do Azure usa o razão de [Quorum](https://www.goquorum.com/developers) baseado em Ethereum projetado para o processamento de transações privadas dentro de um grupo de participantes conhecidos, identificado como um consórcio no serviço Blockchain do Azure.

Atualmente, o serviço Blockchain do Azure dá suporte à [versão de quorum 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) e [Tessera Transaction Manager](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Gerenciando atualizações e upgrades

O controle de versão em quorum é feito por meio de versões principais, secundárias e de patches. Por exemplo, se a versão de quorum for 2.0.1, o tipo de versão será Categorizado da seguinte maneira:

|Principais | Pequenas  | Distribuído  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

O serviço Blockchain do Azure atualiza automaticamente as versões de patch do quorum para os membros em execução existentes dentro de 30 dias após serem disponibilizados do quorum.

## <a name="availability-of-new-ledger-versions"></a>Disponibilidade de novas versões do razão

O serviço Blockchain do Azure fornece as últimas versões principais e secundárias do razão de quorum dentro de 60 dias de disponibilidade do fabricante do quorum. No máximo quatro versões secundárias são fornecidas para que consortia escolha ao provisionar um novo membro e consórcio. No momento, não há suporte para a atualização do para uma versão principal ou secundária. Por exemplo, se você estiver executando a versão 2. x, não há suporte atualmente para a atualização para a versão 3. x. Da mesma forma, se você estiver executando a versão 2,2, não há suporte para uma atualização para a versão 2,3 no momento.

## <a name="next-steps"></a>Passos Seguintes

[Limites no serviço Blockchain do Azure](limits.md)
