---
title: Amostra de script Azure PowerShell - Calcular o tamanho total da faturação de um recipiente blob / Microsoft Docs
description: Calcular o tamanho total de um recipiente no armazenamento Azure Blob para efeitos de faturação.
services: storage
author: fhryo-msft
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: fryu
ms.openlocfilehash: f8bd47a8fde700382f9789ab1c77ff35e9b1f4f5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "86999535"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>Calcular o tamanho total da faturação de um recipiente de bolhas

Este script calcula o tamanho de um recipiente no armazenamento da Azure Blob com o propósito de estimar os custos de faturação. O guião totaliza o tamanho das bolhas no recipiente.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Este script PowerShell calcula o tamanho de um recipiente para efeitos de faturação. Se estiver a calcular o tamanho do contentor para outros fins, consulte [Calcular o tamanho total de um recipiente de armazenamento Blob](../scripts/storage-blobs-container-calculate-size-powershell.md) para um script mais simples que forneça uma estimativa.

## <a name="determine-the-size-of-the-blob-container"></a>Determinar o tamanho do recipiente blob

O tamanho total do recipiente blob inclui o tamanho do próprio recipiente e o tamanho de todas as bolhas debaixo do recipiente.

As seguintes secções descrevem como a capacidade de armazenamento é calculada para recipientes e bolhas.Na secção seguinte, Len(X) o número de caracteres na cadeia.

### <a name="blob-containers"></a>Contentores de blobs

O seguinte cálculo descreve como estimar a quantidade de armazenamento consumida por recipiente de bolhas:

```
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
```

Segue-se a avaria:
* 48 bytes de sobrecarga para cada recipiente inclui o último tempo modificado, permissões, configurações públicas e alguns metadados do sistema.

* O nome do recipiente é armazenado como Unicode, por isso pegue no número de caracteres e multiplique por dois.

* Para cada bloco de metadados de recipiente blob que é armazenado, armazenamos o comprimento do nome (ASCII), mais o comprimento do valor da cadeia.

* Os 512 bytes por Identificador Assinado incluem nome de identificador assinado, hora de início, prazo de validade e permissões.

### <a name="blobs"></a>Blobs

Os seguintes cálculos mostram como estimar a quantidade de armazenamento consumida por bolha.

* Bolha de bloco (bolha de base ou instantâneo):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   ```

* Bolha de página (bolha de base ou instantâneo):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   ```

Segue-se a avaria:

* 124 bytes de sobrecarga para bolha, que inclui:
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

* O nome blob é armazenado como Unicode, por isso pegue no número de caracteres e multiplique por dois.

* Para cada bloco de metadados armazenados, adicione o comprimento do nome (armazenado como ASCII), mais o comprimento do valor da cadeia.

* Para as bolhas de bloco:
  * 8 bytes para a lista de blocos.
  * Número de blocos vezes o tamanho do bloco de identificação em bytes.
  * O tamanho dos dados em todos os blocos comprometidos e não comprometidos.

    >[!NOTE]
    >Quando as imagens são usadas, este tamanho inclui apenas os dados únicos para esta base ou bolha instantânea. Se os blocos não autorizados não forem utilizados após uma semana, são recolhidos lixo. Depois disso, não contam para a faturação.

* Para bolhas de página:
  * O número de páginas não consagradas varia com datas vezes 12 bytes. Este é o número de gamas de páginas únicas que você vê ao ligar para a API **GetPageRanges.**

  * O tamanho dos dados em bytes de todas as páginas armazenadas.

    >[!NOTE]
    >Quando as imagens são usadas, este tamanho inclui apenas as páginas únicas para a bolha de base ou a bolha instantânea que está sendo contada.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Passos seguintes

- Ver [Calcular o tamanho total de um recipiente de armazenamento Blob](../scripts/storage-blobs-container-calculate-size-powershell.md) para obter um simples script que forneça uma estimativa do tamanho do recipiente.

- Para obter mais informações sobre a faturação do Armazenamento Azure, consulte [a Conta de Armazenamento do Windows Azure](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

- Para obter mais informações sobre o módulo Azure PowerShell, consulte [a documentação da Azure PowerShell](https://docs.microsoft.com/powershell/azure/).

- Pode encontrar amostras adicionais de script PowerShell em [amostras powerShell para armazenamento Azure](../blobs/storage-samples-blobs-powershell.md).
