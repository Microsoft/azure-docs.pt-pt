---
title: Dados de blob reidratar da camada de arquivo
description: Reidratar seus BLOBs do armazenamento de arquivo para que você possa acessar os dados.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/07/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 2e7d56a1461dfd89a7309288aadb0ba245d0f885
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68958216"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Dados de blob reidratar da camada de arquivo

Embora um blob esteja na camada de acesso de arquivamento, ele é considerado offline e não pode ser lido ou modificado. Os metadados de blob permanecem online e disponíveis, permitindo que você liste o blob e suas propriedades. A leitura e a modificação de dados de blob só estão disponíveis com camadas online, como Hot ou fria. Há duas opções para recuperar e acessar os dados armazenados na camada de acesso de arquivamento.

1. [Reidratar um blob Arquivado em uma camada online](#rehydrate-an-archived-blob-to-an-online-tier) -reidratar um blob arquivado para quente ou frio alterando sua camada usando a operação [definir camada de blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) .
2. [Copiar um blob Arquivado em uma camada online](#copy-an-archived-blob-to-an-online-tier) – crie uma nova cópia de um blob arquivado usando a operação [copiar blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) . Especifique um nome de blob diferente e uma camada de destino de alta ou fria.

 Para obter mais informações sobre camadas, consulte [armazenamento de BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Reidratar um blob Arquivado em uma camada online

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Copiar um blob arquivado para uma camada online

Se você não quiser reidratar um blob, poderá escolher uma operação de [cópia de blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) . Seu blob original permanecerá sem modificações no arquivo enquanto você trabalha no novo BLOB na camada quente ou fria. Você pode definir a propriedade *x-MS-reidratar-Priority* opcional como Standard ou High (visualização) ao usar o processo de cópia.

Os blobs de arquivo só podem ser copiados para as camadas de destino online. Não há suporte para a cópia de um blob de arquivo para outro blob de arquivo.

Copiar um blob do arquivo morto leva tempo. Nos bastidores, a operação **copiar blob** rehydrates temporariamente o blob de origem do arquivo morto para criar um novo BLOB online na camada de destino. Esse novo BLOB não estará disponível até que o reidratação temporário do arquivo seja concluído e os dados sejam gravados no novo BLOB.

## <a name="pricing-and-billing"></a>Preços e faturação

Os BLOBs reidratar do arquivamento em camadas quentes ou frias são cobrados como operações de leitura e recuperação de dados. O uso de alta prioridade (versão prévia) tem custos de recuperação de dados e de operação mais altos em comparação com a prioridade padrão. Reidratação de alta prioridade aparece como um item de linha separado em sua fatura. Se uma solicitação de alta prioridade para retornar um blob de arquivo de alguns gigabytes levar mais de 5 horas, você não será cobrado pela taxa de recuperação de alta prioridade. No entanto, as taxas de recuperação padrão ainda se aplicam.

A cópia de BLOBs do arquivo em camadas quentes ou frias é cobrada como operações de leitura e recuperação de dados. Uma operação de gravação é cobrada pela criação da nova cópia. As tarifas de exclusão antecipada não se aplicam quando você copia para um blob online porque o blob de origem permanece inalterado na camada de arquivo morto. Os encargos de alta prioridade se aplicam.

Os BLOBs na camada de arquivo devem ser armazenados por um mínimo de 180 dias. Excluir ou reidratarr BLOBs arquivados antes de 180 dias incorrerá em tarifas de exclusão antecipada.

> [!NOTE]
> Para obter mais informações sobre preços para BLOBs de blocos e reidratação de dados, consulte [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). Para obter mais informações sobre encargos de transferência de dados de saída, consulte [detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="next-steps"></a>Próximos Passos

* [Saiba mais sobre as camadas de armazenamento de BLOBs](storage-blob-storage-tiers.md)
* [Verificar os preços das camadas de armazenamento frequente, esporádica e de arquivo nas contas de armazenamento de Blobs e GPv2 por região](https://azure.microsoft.com/pricing/details/storage/)
* [Gerenciar o ciclo de vida do armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md)
* [Verificar os preços das transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
