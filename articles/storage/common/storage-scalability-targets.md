---
title: Escalabilidade e metas de desempenho do armazenamento do Azure-contas de armazenamento
description: Saiba mais sobre as metas de escalabilidade e desempenho, incluindo capacidade, taxa de solicitação e largura de banda de entrada e saída, para contas de armazenamento do Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 293d0de024ad815b4755d48833b7058c29ebcd5a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513287"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>Escalabilidade e metas de desempenho do armazenamento do Azure para contas de armazenamento

Este artigo detalha as metas de escalabilidade e desempenho para contas de armazenamento do Azure. As metas de escalabilidade e desempenho listadas aqui são destinos de ponta, mas são atingíveis. Em todos os casos, a taxa de solicitação e a largura de banda obtidas pela sua conta de armazenamento dependem do tamanho dos objetos armazenados, dos padrões de acesso utilizados e do tipo de carga de trabalho que seu aplicativo executa.

Certifique-se de testar seu serviço para determinar se seu desempenho atende às suas necessidades. Se possível, evite picos repentinos na taxa de tráfego e garanta que o tráfego seja bem distribuído entre partições.

Quando seu aplicativo atinge o limite do que uma partição pode manipular para sua carga de trabalho, o armazenamento do Azure começa a retornar o código de erro 503 (servidor ocupado) ou as respostas de código de erro 500 (tempo limite de operação). Se ocorrerem erros 503, considere modificar seu aplicativo para usar uma política de retirada exponencial para novas tentativas. A retirada exponencial permite que a carga na partição diminua e reduza os picos de tráfego para essa partição.

## <a name="storage-account-scale-limits"></a>Limites de escala da conta de armazenamento

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>Limites de escala da conta de armazenamento de desempenho premium

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>Limites de escala do provedor de recursos de armazenamento

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Destinos de escala do armazenamento de BLOBs do Azure

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Destinos de escala de arquivos do Azure

Para obter mais informações sobre as metas de escala e desempenho para arquivos e Sincronização de Arquivos do Azure do Azure, consulte [metas de desempenho e escalabilidade de arquivos do Azure](../files/storage-files-scale-targets.md).

> [!IMPORTANT]
> Os limites da conta de armazenamento são aplicados a todos os compartilhamentos. A expansão para o máximo de contas de armazenamento só será atingível se houver apenas um compartilhamento por conta de armazenamento.
>
> Compartilhamentos de arquivos padrão maiores que 5 TiB têm certas limitações e restrições regionais.
> Para obter uma lista de limitações, informações regionais e instruções para habilitar esses tamanhos maiores de compartilhamento de arquivos, consulte a seção [integração a compartilhamentos de arquivos maiores (camada Standard)](../files/storage-files-planning.md#onboard-to-larger-file-shares-standard-tier) do guia de planejamento de arquivos do Azure.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>Destinos de escala de arquivos Premium

Há três categorias de limitações a serem consideradas para arquivos Premium: contas de armazenamento, compartilhamentos e arquivos.

Por exemplo: um único compartilhamento pode alcançar 100.000 IOPS e um único arquivo pode ser dimensionado para até 5.000 IOPS. Portanto, por exemplo, se você tiver três arquivos em um único compartilhamento, o máximo de IOPs que você pode obter desse compartilhamento é 15.000.

#### <a name="premium-file-share-limits"></a>Limites de compartilhamento de arquivos Premium

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Destinos de escala Sincronização de Arquivos do Azure

A Sincronização de Arquivos do Azure foi projetada com o objetivo de uso ilimitado, mas o uso ilimitado nem sempre é possível. A tabela a seguir indica os limites do teste da Microsoft e também indica quais destinos são os limites rígidos:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Destinos de escala do armazenamento de filas do Azure

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Destinos de escala do armazenamento de tabelas do Azure

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Ver também

- [Detalhes de preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/)
- [Assinatura do Azure e limites de serviço, cotas e restrições](../../azure-subscription-service-limits.md)
- [Replicação do armazenamento do Azure](../storage-redundancy.md)
- [Lista de Verificação de Desempenho e Escalabilidade do Armazenamento do Microsoft Azure](../storage-performance-checklist.md)
