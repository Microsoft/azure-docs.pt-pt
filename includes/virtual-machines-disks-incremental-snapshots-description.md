---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 83c391c0d92f8d4a0ed4b44bc3a90273db51b412
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81539386"
---
As imagens incrementais são backups pontuais para discos geridos que, quando tirados, consistem apenas em todas as alterações desde o último instantâneo. Quando tenta descarregar ou utilizar um instantâneo incremental, o VHD completo é utilizado. Esta nova capacidade para instantâneos de disco geridos permite potencialmente que sejam mais rentáveis, uma vez que, a menos que escolha, não tem de armazenar todo o disco com cada instantâneo individual. Tal como as imagens regulares, as imagens incrementais podem ser usadas para criar um disco gerido completo ou para fazer uma imagem regular.

Existem algumas diferenças entre um instantâneo incremental e um instantâneo regular. Os instantâneos incrementais utilizarão sempre o armazenamento padrão de HDDs, independentemente do tipo de armazenamento do disco, enquanto os instantâneos regulares podem utilizar SSDs premium. Se estiver a utilizar instantâneos regulares no Armazenamento Premium para aumentar as implementações de VM, recomendamos que utilize imagens personalizadas no armazenamento padrão na Galeria de [Imagens Partilhadas.](../articles/virtual-machines/linux/shared-image-galleries.md) Vai ajudá-lo a alcançar uma escala mais massiva com menor custo. Além disso, os instantâneos incrementais potencialmente oferecem uma melhor fiabilidade com [armazenamento redundante de zona](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Se o ZRS estiver disponível na região selecionada, um instantâneo incremental utilizará automaticamente o ZRS. Se o ZRS não estiver disponível na região, o instantâneo será por defeito para [o armazenamento localmente redundante](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Pode anular este comportamento e selecionar um manualmente, mas não recomendamos isso.

Os instantâneos incrementais também oferecem uma capacidade diferencial, apenas disponível para discos geridos. Permitem-lhe obter as alterações entre dois instantâneos incrementais dos mesmos discos geridos, até ao nível do bloco. Pode utilizar esta capacidade para reduzir a sua pegada de dados ao copiar instantâneos em regiões.  Por exemplo, você pode baixar o primeiro instantâneo incremental como uma bolha de base em outra região. Para as imagens incrementais subsequentes, só pode copiar as alterações desde a última imagem da bolha de base. Depois de copiar as alterações, pode tirar fotografias na bolha de base que representam o seu ponto de cópia de segurança no tempo do disco noutra região. Pode restaurar o seu disco a partir da bolha de base ou a partir de uma imagem na bolha de base de outra região.

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="Diagrama que retrata instantâneos incrementais copiados em regiões. As imagens fazem várias chamadas API até que, eventualmente, formem bolhas de página por cada instantâneo.":::

Pode ver o tamanho usado das suas fotos olhando para o relatório de utilização do [Azure.](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) Por exemplo, se o tamanho dos dados usados de um instantâneo for de 10 GiB, o relatório de utilização **diário** mostrará 10 GiB/((31 dias) = 0,3226 como a quantidade consumida.
