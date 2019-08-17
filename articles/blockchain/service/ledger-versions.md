---
title: Versões, aplicação de patch e atualização do razão com suporte do serviço Blockchain do Azure
description: Visão geral das versões de razões com suporte no serviço Blockchain do Azure, incluindo políticas relacionadas à aplicação de patches de sistemas e atualizações gerenciadas pelo usuário e pelo sistema.
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 6cded49f14ae90f84b18c0a37059677c6c1fed99
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562782"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Versões do razão do serviço Blockchain do Azure com suporte

O serviço Blockchain do Azure usa o razão de [Quorum](https://www.goquorum.com/developers) baseado em Ethereum projetado para o processamento de transações privadas dentro de um grupo de participantes conhecidos, identificado como um consórcio no serviço Blockchain do Azure.

Atualmente, o serviço Blockchain do Azure dá suporte à [versão de quorum 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) e [Tessera Transaction Manager](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Gerenciando atualizações e upgrades

O controle de versão em quorum é feito por meio de versões principais, secundárias e patches. Por exemplo, se a versão de quorum for 2.0.1, o tipo de versão será Categorizado da seguinte maneira:

|Principais | Pequenas  | Correção  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

O serviço Blockchain do Azure atualiza automaticamente as versões de patch do quorum para os membros em execução existentes dentro de 30 dias após serem disponibilizados do quorum.

## <a name="availability-of-new-ledger-versions"></a>Disponibilidade de novas versões do razão

O serviço Blockchain do Azure fornece as últimas versões principais e secundárias do razão de quorum dentro de 60 dias de disponibilidade do fabricante do quorum. No máximo quatro versões secundárias são fornecidas para que consortia escolha ao provisionar um novo membro e consórcio. No momento, não há suporte para a atualização do para uma versão principal ou secundária.

## <a name="next-steps"></a>Passos seguintes

[Limites no serviço Blockchain do Azure](limits.md)
