---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: edec192009b9cc9b71114287e705c161183273dc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95992926"
---
As imagens incrementais são backups pontuais para discos geridos que, quando tirados, consistem apenas nas alterações desde o último instantâneo. Quando restaura um disco a partir de uma imagem incremental, o sistema reconstrói o disco completo, o que representa o ponto de cópia de segurança do disco quando o instantâneo incremental foi tirado. Esta nova capacidade para instantâneos de disco geridos permite potencialmente que sejam mais rentáveis, uma vez que, a menos que escolha, não tem de armazenar todo o disco com cada instantâneo individual. Tal como os instantâneos completos, os instantâneos incrementais podem ser usados para criar um disco gerido completo ou um instantâneo completo.

Há algumas diferenças entre um instantâneo incremental e um instantâneo completo. Os instantâneos incrementais utilizarão sempre o armazenamento padrão de HDDs, independentemente do tipo de armazenamento do disco, enquanto os instantâneos completos podem utilizar SSDs premium. Se estiver a utilizar instantâneos completos no Armazenamento Premium para aumentar as implementações de VM, recomendamos que utilize imagens personalizadas no armazenamento padrão na Galeria de [Imagens Partilhadas.](../articles/virtual-machines/linux/shared-image-galleries.md) Vai ajudá-lo a alcançar uma escala mais massiva com menor custo. Além disso, os instantâneos incrementais potencialmente oferecem uma melhor fiabilidade com [armazenamento redundante de zona](../articles/storage/common/storage-redundancy.md) (ZRS). Se o ZRS estiver disponível na região selecionada, um instantâneo incremental utilizará automaticamente o ZRS. Se o ZRS não estiver disponível na região, o instantâneo será por defeito para [o armazenamento localmente redundante](../articles/storage/common/storage-redundancy.md) (LRS). Pode anular este comportamento e selecionar um manualmente, mas não recomendamos isso.

Os instantâneos incrementais também oferecem uma capacidade diferencial, apenas disponível para discos geridos. Permitem-lhe obter as alterações entre dois instantâneos incrementais dos mesmos discos geridos, até ao nível do bloco. Pode utilizar esta capacidade para reduzir a sua pegada de dados ao copiar instantâneos em regiões.  Por exemplo, você pode baixar o primeiro instantâneo incremental como uma bolha de base em outra região. Para as imagens incrementais subsequentes, só pode copiar as alterações desde a última imagem da bolha de base. Depois de copiar as alterações, pode tirar fotografias na bolha de base que representam o seu ponto de cópia de segurança no tempo do disco noutra região. Pode restaurar o seu disco a partir da bolha de base ou a partir de uma imagem na bolha de base de outra região.

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="Diagrama que retrata instantâneos incrementais copiados em regiões. As imagens fazem várias chamadas API até que, eventualmente, formem bolhas de página por cada instantâneo.":::

As imagens incrementais são faturadas apenas para o tamanho utilizado. Pode encontrar o tamanho usado das suas fotos olhando para o relatório de utilização do [Azure.](../articles/cost-management-billing/understand/review-individual-bill.md) Por exemplo, se o tamanho dos dados usados de um instantâneo for de 10 GiB, o relatório de utilização **diário** mostrará 10 GiB/((31 dias) = 0,3226 como a quantidade consumida.