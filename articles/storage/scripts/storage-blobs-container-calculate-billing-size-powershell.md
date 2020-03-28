---
title: Amostra de script Azure PowerShell - Calcular o tamanho total da faturação de um recipiente de bolha / Microsoft Docs
description: Calcular o tamanho total de um recipiente no armazenamento de Azure Blob para efeitos de faturação.
services: storage
author: fhryo-msft
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: fryu
ms.openlocfilehash: 12b32256c91dfcf93ca55eeb348cc78613ba860e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067110"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>Calcular o tamanho total da faturação de um recipiente de bolha

Este script calcula o tamanho de um recipiente no armazenamento de Azure Blob com o propósito de estimar os custos de faturação. O guião totaliza o tamanho das bolhas no recipiente.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Este script PowerShell calcula o tamanho de um recipiente para efeitos de faturação. Se estiver a calcular o tamanho do recipiente para outros fins, consulte [Calcular o tamanho total de um recipiente de armazenamento Blob](../scripts/storage-blobs-container-calculate-size-powershell.md) para um script mais simples que forneça uma estimativa.

## <a name="determine-the-size-of-the-blob-container"></a>Determinar o tamanho do recipiente de bolhas

O tamanho total do recipiente de bolhas inclui o tamanho do próprio recipiente e o tamanho de todas as bolhas debaixo do recipiente.

As seguintes secções descrevem como a capacidade de armazenamento é calculada para recipientes e bolhas.Na secção seguinte, Len (X) significa o número de caracteres na cadeia.

### <a name="blob-containers"></a>Recipientes blob

O seguinte cálculo descreve como estimar a quantidade de armazenamento que é consumida por recipiente de bolhas:

```
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
```

Segue-se a avaria:
* 48 bytes de despesas gerais para cada recipiente incluem o Último Tempo Modificado, Permissões, Definições Públicas e alguns metadados do sistema.

* O nome do recipiente é armazenado como Unicode, por isso, pegue o número de caracteres e multiplique por dois.

* Para cada bloco de metadados de contentores blob que são armazenados, armazenamos o comprimento do nome (ASCII), mais o comprimento do valor de cadeia.

* Os 512 bytes por Identificador Assinado incluem o nome do identificador assinado, hora de início, tempo de validade e permissões.

### <a name="blobs"></a>Blobs

Os seguintes cálculos mostram como estimar a quantidade de armazenamento consumida por bolha.

* Bloco blob (bolha de base ou instantâneo):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   ```

* Blob de página (bolha de base ou instantâneo):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   ```

Segue-se a avaria:

* 124 bytes de despesas gerais para a bolha, que inclui:
    - Hora da Última Modificação
    - Tamanho
    - Cache-Control
    - Content-Type
    - Linguagem de conteúdo
    - Codificação de conteúdos
    - Conteúdo-MD5
    - Permissões
    - Informação instantânea
    - Arrendamento
    - Alguns metadados do sistema

* O nome blob é armazenado como Unicode, por isso pegue o número de caracteres e multiplique por dois.

* Para cada bloco de metadados armazenados, adicione o comprimento do nome (armazenado como ASCII), mais o comprimento do valor de cadeia.

* Para as bolhas de bloco:
  * 8 bytes para a lista de blocos.
  * Número de blocos vezes o tamanho de identificação do bloco em bytes.
  * O tamanho dos dados em todos os blocos comprometidos e não comprometidos.

    >[!NOTE]
    >Quando são utilizados instantâneos, este tamanho inclui apenas os dados únicos para esta base ou bolha instantânea. Se os blocos não comprometidos não forem usados após uma semana, são recolhidos pelo lixo. Depois disso, não contam para a faturação.

* Para bolhas de página:
  * O número de páginas não consecutivas varia com data times 12 bytes. Este é o número de gamas únicas de páginas que você vê ao ligar para a **API GetPageRanges.**

  * O tamanho dos dados em bytes de todas as páginas armazenadas.

    >[!NOTE]
    >Quando as imagens são usadas, este tamanho inclui apenas as páginas únicas para a bolha base ou a bolha instantânea que está sendo contada.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Passos seguintes

- Consulte [Calcular o tamanho total de um recipiente de armazenamento Blob](../scripts/storage-blobs-container-calculate-size-powershell.md) para um script simples que forneça uma estimativa do tamanho do recipiente.

- Para obter mais informações sobre a faturação de armazenamento do Azure, consulte [Understanding Windows Azure Storage Billing](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

- Para obter mais informações sobre o módulo Azure PowerShell, consulte a [documentação Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

- Pode encontrar amostras adicionais de script PowerShell em [amostras powerShell para armazenamento azure](../blobs/storage-samples-blobs-powershell.md).
