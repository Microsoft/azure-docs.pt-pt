---
title: Armazenamento com redundância geográfica (GRS) para durabilidade entre regiões
titleSuffix: Azure Storage
description: O armazenamento com redundância geográfica (GRS) Replica seus dados entre duas regiões que estão a centenas de quilômetros de distância. O GRS protege contra falhas de hardware no datacenter, bem como desastres regionais.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: c44c13f268a561e3094ae76757504a86627e1f58
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895225"
---
# <a name="geo-redundant-storage-grs-cross-regional-replication-for-azure-storage"></a>Armazenamento com redundância geográfica (GRS): replicação entre regiões para o armazenamento do Azure

[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-grs.md)]

## <a name="read-access-geo-redundant-storage"></a>Armazenamento georredundante com acesso de leitura

O armazenamento com redundância geográfica com acesso de leitura (RA-GRS) maximiza a disponibilidade para sua conta de armazenamento. O RA-GRS fornece acesso somente leitura aos dados no local secundário, além da replicação geográfica em duas regiões.

Quando você habilita o acesso somente leitura aos seus dados na região secundária, seus dados ficam disponíveis em um ponto de extremidade secundário, bem como no ponto de extremidade primário da sua conta de armazenamento. O ponto de extremidade secundário é semelhante ao ponto de extremidade primário, mas acrescenta o sufixo `–secondary` ao nome da conta. Por exemplo, se o ponto de extremidade primário para o serviço blob for `myaccount.blob.core.windows.net`, o ponto de extremidade secundário será `myaccount-secondary.blob.core.windows.net`. As chaves de acesso para sua conta de armazenamento são as mesmas para os pontos de extremidade primários e secundários.

Algumas considerações para ter em mente ao usar RA-GRS:

- Seu aplicativo precisa gerenciar a qual ponto de extremidade ele está interagindo ao usar RA-GRS.
- Como a replicação assíncrona envolve um atraso, as alterações que ainda não foram replicadas para a região secundária poderão ser perdidas se os dados não puderem ser recuperados da região primária.
- Você pode verificar a hora da última sincronização da sua conta de armazenamento. A hora da última sincronização é um valor de data/hora GMT. Todas as gravações primárias antes da hora da última sincronização foram gravadas com êxito no local secundário, o que significa que elas estão disponíveis para serem lidas a partir do local secundário. As gravações primárias após a hora da última sincronização podem ou não estar disponíveis para leituras ainda. Você pode consultar esse valor usando o [portal do Azure](https://portal.azure.com/), [Azure PowerShell](storage-powershell-guide-full.md)ou de uma das bibliotecas de cliente de armazenamento do Azure.
- Se você iniciar um failover de conta (versão prévia) de uma conta GRS ou RA-GRS para a região secundária, o acesso de gravação a essa conta será restaurado após a conclusão do failover. Para obter mais informações, consulte [recuperação de desastre e failover da conta de armazenamento (versão prévia)](storage-disaster-recovery-guidance.md).
- O RA-GRS destina-se a fins de alta disponibilidade. Para obter diretrizes de escalabilidade, examine a [lista de verificação de desempenho](storage-performance-checklist.md).
- Para obter sugestões sobre como projetar para alta disponibilidade com o RA-GRS, consulte [criando aplicativos altamente disponíveis usando o armazenamento ra-grs](storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Qual é o RPO e o RTO com o GRS?

**Objetivo de ponto de recuperação (RPO):** Em GRS e RA-GRS, o serviço de armazenamento Replica geograficamente os dados do local primário para o secundário. Caso a região primária fique indisponível, você pode executar um failover de conta (versão prévia) na região secundária. Quando você inicia um failover, as alterações recentes que ainda não foram replicadas geograficamente podem ser perdidas. O número de minutos de dados potenciais perdidos é conhecido como RPO. O RPO indica o ponto no tempo em que os dados podem ser recuperados. O armazenamento do Azure normalmente tem um RPO de menos de 15 minutos, embora atualmente não haja SLA sobre o tempo que a replicação geográfica leva.

**RTO (objetivo de tempo de recuperação):** O RTO é uma medida de quanto tempo leva para executar o failover e colocar a conta de armazenamento online novamente. O tempo para executar o failover inclui as seguintes ações:

- O tempo até que o cliente inicie o failover da conta de armazenamento da região primária para a secundária.
- O tempo exigido pelo Azure para executar o failover alterando as entradas DNS primárias para apontar para o local secundário.

## <a name="paired-regions"></a>Regiões emparelhadas

Ao criar uma conta de armazenamento, você seleciona a região primária da conta. A região secundária emparelhada é determinada com base na região primária e não pode ser alterada. Para obter informações atualizadas sobre regiões com suporte do Azure, consulte [continuidade de negócios e recuperação de desastres (BCDR): regiões emparelhadas do Azure](../../best-practices-availability-paired-regions.md).

## <a name="see-also"></a>Ver também

- [Replicação do Armazenamento do Azure](storage-redundancy.md)
- [LRS (armazenamento com redundância local): redundância de dados de baixo custo para o armazenamento do Azure](storage-redundancy-lrs.md)
- [ZRS (armazenamento com redundância de zona): aplicativos de armazenamento do Azure altamente disponíveis](storage-redundancy-zrs.md)
