---
title: Script do Azure PowerShell de exemplo - calcular o tamanho total de faturação de um contentor de BLOBs | Documentos da Microsoft
description: Calcule o tamanho total de um contentor no armazenamento de Blobs do Azure para efeitos de faturação.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: fryu
ms.openlocfilehash: 805abec84b26a6b2b9af3dfe318f877f4edb9547
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58080901"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>Calcular o tamanho total de faturação de um contentor de BLOBs

Este script calcula o tamanho de um contentor no armazenamento de Blobs do Azure com o objetivo de estimar os custos de faturação. O script os totais do tamanho dos blobs no contentor.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Este script do PowerShell calcula o tamanho de um contentor para efeitos de faturação. Se estiver a calcular o tamanho do contentor para outros fins, consulte [calcular o tamanho total de um contentor de armazenamento de BLOBs](../scripts/storage-blobs-container-calculate-size-powershell.md) para um script mais simples que fornece uma estimativa.

## <a name="determine-the-size-of-the-blob-container"></a>Determinar o tamanho do contentor de BLOBs

O tamanho total do contentor de BLOBs inclui o tamanho do contentor em si e o tamanho de todos os blobs no contentor.

As seções a seguir descreve como a capacidade de armazenamento é calculada para contentores de BLOBs e blobs. Na secção seguinte, Len(X) significa que o número de carateres na cadeia de caracteres.

### <a name="blob-containers"></a>Contentores de BLOBs

O cálculo seguinte descreve como estimar a quantidade de armazenamento é consumido por contentor de BLOBs:

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

Segue-se a análise detalhada:
* 48 bytes de overhead para cada contentor inclui a hora da última modificação, permissões, as definições de público e alguns metadados do sistema.

* O nome do contentor é armazenado como Unicode, por isso, coloque o número de carateres e multiplicar por dois.

* Para cada bloco de metadados do contentor de blob armazenado, armazenamos o comprimento do nome (ASCII), além do comprimento do valor de cadeia.

* As 512 bytes por identificador assinado inclui o nome do identificador assinado, hora de início, hora de expiração e as permissões.

### <a name="blobs"></a>Blobs

Os cálculos seguintes mostram como estimar a quantidade de armazenamento consumido por blob.

* Blob de blocos (ou instantâneo de blob de base):

   `
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   `

* Blob de página (blob de base ou instantâneo):

   `
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   `

Segue-se a análise detalhada:

* bytes 124 de sobrecarga para o blob, que inclui:
    - Hora da Última Modificação
    - Tamanho
    - Cache-Control
    - Content-Type
    - Idioma do conteúdo
    - Codificação de conteúdo
    - Content-MD5
    - Permissões
    - Informações de instantâneo
    - Concessão
    - Alguns metadados do sistema

* O nome do blob é armazenado como Unicode, por isso, coloque o número de carateres e multiplicar por dois.

* Para cada bloco de metadados armazenados, adicione o comprimento do nome (armazenado como ASCII), além do comprimento do valor de cadeia.

* Para os blobs de bloco:
  * 8 bytes para a lista de bloqueios.
  * Número de blocos vezes o tamanho do ID de bloco em bytes.
  * O tamanho dos dados em todos os blocos consolidados e não consolidados.

    >[!NOTE]
    >Quando os instantâneos são usados, este tamanho inclui apenas os dados exclusivos para este blob base ou de instantâneo. Se os blocos não consolidados não forem utilizados após uma semana, são coletado pelo lixo. Depois disso, eles não contam para faturação.

* Para blobs de página:
  * O número de intervalos de página não consecutivos com dados vezes 12 bytes. Este é o número de intervalos de página exclusiva vir ao chamar o **GetPageRanges** API.

  * O tamanho dos dados em bytes de todas as páginas armazenadas.

    >[!NOTE]
    >Quando os instantâneos são usados, este tamanho inclui somente as páginas exclusivas para o blob de base ou o blob de instantâneo que está a ser contabilizado.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Passos Seguintes

- Ver [calcular o tamanho total de um contentor de armazenamento de BLOBs](../scripts/storage-blobs-container-calculate-size-powershell.md) para um script simple que fornece uma estimativa do tamanho do contentor.

- Para obter mais informações sobre a faturação do armazenamento do Azure, consulte [Noções básicas sobre Windows Azure a cobrança de armazenamento](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

- Para obter mais informações sobre o módulo Azure PowerShell, consulte [documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

- Pode encontrar exemplos de scripts do PowerShell de armazenamento adicionais no [exemplos do PowerShell do armazenamento do Azure](../blobs/storage-samples-blobs-powershell.md).
