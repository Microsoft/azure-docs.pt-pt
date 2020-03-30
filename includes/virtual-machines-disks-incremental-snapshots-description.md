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
ms.openlocfilehash: 4d2c0a02a48c0e04b501f136f66c28b3f532e2a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486035"
---
Os instantâneos incrementais são cópias de segurança pontual para discos geridos que, quando tirados, consistem apenas em todas as alterações desde o último instantâneo. Quando tenta descarregar ou utilizar um instantâneo incremental, o VHD completo é utilizado. Esta nova capacidade para instantâneos de disco geridos potencialmente permite que sejam mais rentáveis, uma vez que, a menos que escolha, não precisa de armazenar todo o disco com cada instantâneo individual. Tal como as imagens regulares, as imagens incrementais podem ser usadas para criar um disco gerido completo ou para fazer uma imagem instantânea regular.

Existem algumas diferenças entre um instantâneo incremental e um instantâneo regular. Os instantâneos incrementais utilizarão sempre o armazenamento padrão dos HDDs, independentemente do tipo de armazenamento do disco, enquanto os instantâneos regulares podem utilizar SSDs premium. Se estiver a utilizar instantâneos regulares no Armazenamento Premium para aumentar as implementações vm, recomendamos que utilize imagens personalizadas no armazenamento padrão na Galeria de [Imagem Partilhada.](../articles/virtual-machines/linux/shared-image-galleries.md) Irá ajudá-lo a alcançar uma escala mais massiva com um custo mais baixo. Além disso, instantâneos incrementais potencialmente oferecem uma melhor fiabilidade com [armazenamento redundante de zona](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Se o ZRS estiver disponível na região selecionada, um instantâneo incremental utilizará o ZRS automaticamente. Se o ZRS não estiver disponível na região, então o instantâneo será indefinido para [armazenamento localmente redundante](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Pode anular este comportamento e selecionar um manualmente, mas não o recomendamos.

Os instantâneos incrementais também oferecem uma capacidade diferencial, apenas disponível para discos geridos. Permitem-lhe obter as alterações entre duas imagens incrementais dos mesmos discos geridos, até ao nível do bloco. Pode utilizar esta capacidade para reduzir a sua pegada de dados ao copiar instantâneos em todas as regiões.  Por exemplo, você pode baixar o primeiro instantâneo incremental como uma bolha base em outra região. Para as imagens posteriores incrementais, só pode copiar as alterações desde o último instantâneo para a bolha base. Depois de copiar as alterações, pode tirar fotografias na bolha base que representam o seu ponto de apoio no tempo do disco noutra região. Pode restaurar o disco a partir da bolha base ou a partir de um instantâneo na bolha base em outra região.

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="Diagrama que retrata imagens incrementais copiadas em regiões. As imagens fazem várias chamadas API até eventualmente formar bolhas de página por cada instantâneo.":::