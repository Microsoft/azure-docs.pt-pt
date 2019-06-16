---
title: Armazenamento georredundante (GRS) de durabilidade de inter-regional no armazenamento do Azure | Documentos da Microsoft
description: Armazenamento georredundante (GRS) replica os dados entre duas regiões que distam centenas de quilómetros de distância. GRS protege contra falhas de hardware no Centro de dados, bem como de desastres regionais.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/20/2018
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 09b553f3ca64d8f5217f023c776ec848215366f9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65150996"
---
# <a name="geo-redundant-storage-grs-cross-regional-replication-for-azure-storage"></a>Armazenamento georredundante (GRS): Replicação de inter-regional do armazenamento do Azure
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-grs.md)]

## <a name="read-access-geo-redundant-storage"></a>Armazenamento georredundante com acesso de leitura
Armazenamento georredundante com acesso de leitura (RA-GRS) maximiza a disponibilidade para a sua conta de armazenamento. RA-GRS fornece acesso só de leitura aos dados na localização secundária, além de georreplicação entre duas regiões.

Quando ativar o acesso só de leitura aos seus dados na região secundária, os dados estão disponíveis num ponto final secundário, bem como no ponto final primário para a sua conta de armazenamento. O ponto final secundário é semelhante para o ponto final primário, mas acrescenta o sufixo `–secondary` ao nome da conta. Por exemplo, se o seu ponto final primário para o serviço Blob `myaccount.blob.core.windows.net`, em seguida, o ponto final secundário é `myaccount-secondary.blob.core.windows.net`. As chaves de acesso para a sua conta de armazenamento são os mesmos para os pontos de extremidade primários e secundários.

Algumas considerações a ter em conta ao utilizar o RA-GRS:

* A aplicação tem de gerir o ponto final está interagindo com ao utilizar o RA-GRS.
* Uma vez que a replicação assíncrona envolve um atraso, as alterações que ainda não tiverem sido replicadas para a região secundária podem ser perdidas se não não possível recuperar os dados da região primária.
* Pode verificar a hora da última sincronização da sua conta de armazenamento. Hora da última sincronização é um valor de data/hora GMT. Todas as escritas primárias antes da hora da última sincronização foi escritas com êxito para a localização secundária, que significa que eles estão disponíveis a serem lidos a partir da localização secundária. Primário escreve após a hora da última sincronização pode ou não estar disponível para leituras ainda. Pode consultar este valor usando o [portal do Azure](https://portal.azure.com/), [Azure PowerShell](storage-powershell-guide-full.md), ou a partir de uma das bibliotecas de cliente do armazenamento do Azure.
* Se iniciar um failover de conta (pré-visualização) de uma conta GRS ou RA-GRS para a região secundária, o acesso de escrita para essa conta é restaurado após a ativação pós-falha foi concluída. Para obter mais informações, consulte [após desastre de armazenamento e recuperação de failover de conta (pré-visualização)](storage-disaster-recovery-guidance.md).
* Destina-se o RA-GRS para fins de elevada disponibilidade. Para obter orientações de escalabilidade, reveja os [lista de verificação de desempenho](storage-performance-checklist.md).
* Para obter sugestões sobre como estruturar para elevada disponibilidade com RA-GRS, consulte [criação altamente disponíveis de aplicativos com o armazenamento RA-GRS](storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>O que é o RPO e RTO com GRS?

**Objetivo de ponto de recuperação (RPO):** No GRS e RA-GRS, o armazenamento do serviço de forma assíncrona geo-replica os dados dos principais para a localização secundária. No caso da região primária fica indisponível, pode efetuar uma ativação pós-falha de conta (pré-visualização) de para a região secundária. Quando iniciar uma ativação pós-falha, as alterações recentes que ainda não tiverem sido georreplicado poderão perder-se. O número de minutos de dados possíveis que perdeu é conhecido como o RPO. O RPO indica o ponto no tempo para que os dados podem ser recuperados. Normalmente, o armazenamento do Azure tem um RPO de menos de 15 minutos, embora não existe atualmente nenhum SLA no georreplicação quanto tempo demora.

**Objetivo de tempo de recuperação (RTO):** O RTO é uma medida de tempo que demora para efetuar a ativação pós-falha e obter a conta de armazenamento online. A hora a realizar a ativação pós-falha inclui as seguintes ações:

   * O tempo até que o cliente inicia a ativação pós-falha da conta de armazenamento dos principais para a região secundária.
   * O tempo necessário pelo Azure para efetuar a ativação pós-falha, alterando as entradas de DNS primárias para apontar para a localização secundária.

## <a name="paired-regions"></a>Regiões emparelhadas 
Quando criar uma conta de armazenamento, selecione a região primária para a conta. Região secundária associada é determinado com base na região principal e não pode ser alterado. Para obter informações atualizadas sobre as regiões suportadas pelo Azure, consulte [Business continuidade e recuperação após desastre (BCDR): Regiões emparelhadas do Azure](../../best-practices-availability-paired-regions.md).

## <a name="see-also"></a>Consulte também
- [Replicação do Armazenamento do Azure](storage-redundancy.md)
- [Armazenamento localmente redundante (LRS): Redundância de dados de baixo custo do armazenamento do Azure](storage-redundancy-lrs.md)
- [Armazenamento com redundância de zona (ZRS): Aplicações de armazenamento do Azure de elevada disponibilidade](storage-redundancy-zrs.md)
