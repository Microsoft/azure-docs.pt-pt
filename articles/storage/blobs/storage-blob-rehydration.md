---
title: Reidratar dados blob do nível de arquivo
description: Reidrate as suas bolhas do armazenamento de arquivo para que possa aceder aos dados do blob. Copie uma bolha arquivada para um nível online.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: a416c22c5b8e09104b20a17bc5042302fa56d8ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88035149"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Reidratar dados blob do nível de arquivo

Enquanto uma bolha está no nível de acesso ao arquivo, é considerada offline e não pode ser lida ou modificada. Os metadados blob permanecem on-line e disponíveis, permitindo-lhe listar a bolha e as suas propriedades. Ler e modificar dados blob só está disponível com níveis on-line, como quentes ou frescos. Existem duas opções para recuperar e aceder aos dados armazenados no nível de acesso ao arquivo.

1. [Reidratar uma bolha arquivada para um nível on-line](#rehydrate-an-archived-blob-to-an-online-tier) - Reidratar uma bolha de arquivo para quente ou fresco alterando a sua camada utilizando a operação [set Blob Tier.](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)
2. [Copie uma bolha arquivada para um nível on-line](#copy-an-archived-blob-to-an-online-tier) - Crie uma nova cópia de uma bolha de arquivo utilizando a operação [Copy Blob.](https://docs.microsoft.com/rest/api/storageservices/copy-blob) Especifique um nome de bolha diferente e um nível de destino de quente ou fresco.

 Para obter mais informações sobre os níveis, consulte [o armazenamento Azure Blob: níveis de acesso quentes, frescos e de arquivo.](storage-blob-storage-tiers.md)

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Reidratar uma bolha arquivada para um nível online

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Copiar um blob arquivado para uma camada online

Se não quiser reidratar a sua bolha de arquivo, pode optar por fazer uma operação [Copy Blob.](https://docs.microsoft.com/rest/api/storageservices/copy-blob) A sua bolha original permanecerá desmodificado no arquivo enquanto uma nova bolha é criada no nível quente ou fresco on-line para que possa trabalhar. Na operação Copy Blob, também pode definir a propriedade opcional *x-ms-rehydrate-prioridade* para Standard ou High para especificar a prioridade em que deseja que a sua cópia blob seja criada.

Copiar uma bolha do arquivo pode demorar horas a ser completada dependendo da prioridade rehidratada selecionada. Nos bastidores, a operação **Copy Blob** lê a sua mancha de origem de arquivo para criar uma nova bolha on-line no nível de destino selecionado. O novo blob pode estar visível quando lista as bolhas, mas os dados não estão disponíveis até que a leitura da bolha do arquivo de origem esteja completa e os dados sejam escritos para a nova bolha de destino online. A nova bolha é como uma cópia independente e qualquer modificação ou supressão não afeta a bolha de arquivo de origem.

> [!IMPORTANT]
> Não elimine a bolha de origem até que a cópia esteja concluída com sucesso no destino. Se a bolha de origem for eliminada, a bolha de destino pode não completar a cópia e ficará vazia. Pode verificar o *estado da cópia x-ms* para determinar o estado da operação da cópia.

As bolhas de arquivo só podem ser copiadas para níveis de destino on-line dentro da mesma conta de armazenamento. A cópia de uma bolha de arquivo para outra bolha de arquivo não é suportada. A tabela seguinte indica as capacidades do CopyBlob.

|                                           | **Fonte de nível quente**   | **Fonte de nível fresco** | **Fonte de nível de arquivo**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **Destino de nível quente**                  | Suportado             | Suportado            | Suportado na mesma conta; pendente rehidrata               |
| **Destino de nível legal**                 | Suportado             | Suportado            | Suportado na mesma conta; pendente rehidrata               |
| **Destino de nível de arquivo**              | Suportado             | Suportado            | Não suportado         |

## <a name="pricing-and-billing"></a>Preços e faturação

As bolhas rehidratantes fora do arquivo em camadas quentes ou frias são carregadas como operações de leitura e recuperação de dados. A utilização de alta prioridade tem custos de operação e recuperação de dados mais elevados em comparação com a prioridade padrão. A reidratação de alta prioridade aparece como um item de linha separada na sua conta. Se um pedido de alta prioridade para devolver uma bolha de arquivo de alguns gigabytes demorar mais de 5 horas, não será cobrado a alta taxa de recuperação de prioridade. No entanto, as taxas de recuperação padrão ainda se aplicam à medida que a reidratação foi prioritária em relação a outros pedidos.

A cópia de bolhas do arquivo para níveis quentes ou frescos é carregada como operações de leitura e recuperação de dados. É cobrada uma operação de escrita para a criação da nova cópia blob. As taxas de eliminação antecipada não se aplicam quando copia para uma bolha online porque a bolha de origem permanece não modificada no nível de arquivo. Os encargos de recuperação de alta prioridade aplicam-se se forem selecionados.

As bolhas no nível de arquivo devem ser armazenadas durante um período mínimo de 180 dias. A eliminação ou a reidratação de bolhas arquivadas antes de 180 dias incorrerão em taxas de eliminação antecipadas.

> [!NOTE]
> Para obter mais informações sobre preços para bolhas de blocos e reidratação de dados, consulte [o preço de armazenamento Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). Para obter mais informações sobre os encargos de transferência de dados de saída, consulte [detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quickstart-scenarios"></a>Cenários de início rápido

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>Reidratar uma bolha de arquivo para um nível on-line
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No portal Azure, procure e selecione **Todos os Recursos.**

1. Selecione a sua conta de armazenamento.

1. Selecione o seu recipiente e, em seguida, selecione a sua bolha.

1. Nas **propriedades Blob**, selecione **Alterar o nível**.

1. Selecione o nível de acesso **Quente** ou **Fresco.** 

1. Selecione uma Prioridade Rehidrata de **Standard** ou **Alta**.

1. **Selecione Guarde** na parte inferior.

![Alterar nível de conta de armazenamento ](media/storage-tiers/blob-access-tier.png)
 ![ Verifique o estado do rehidrato](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
O seguinte script PowerShell pode ser usado para alterar o nível de bolha de uma bolha de arquivo. A `$rgName` variável deve ser inicializada com o nome do seu grupo de recursos. A `$accountName` variável deve ser inicializada com o nome da sua conta de armazenamento. A `$containerName` variável deve ser inicializada com o nome do seu recipiente. A `$blobName` variável deve ser inicializada com o seu nome blob. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", "Standard")
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>Copie uma bolha de arquivo para uma nova bolha com um nível on-line
O seguinte script PowerShell pode ser usado para copiar uma bolha de arquivo para uma nova bolha dentro da mesma conta de armazenamento. A `$rgName` variável deve ser inicializada com o nome do seu grupo de recursos. A `$accountName` variável deve ser inicializada com o nome da sua conta de armazenamento. As `$srcContainerName` `$destContainerName` variáveis e variáveis devem ser rubricadas com os nomes dos seus recipientes. As `$srcBlobName` `$destBlobName` variáveis e variáveis devem ser inicializadas com os seus nomes blob. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$srcContainerName = ""
$destContainerName = ""
$srcBlobName = ""
$destBlobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Copy source blob to a new destination blob with access tier hot using standard rehydrate priority
Start-AzStorageBlobCopy -SrcContainer $srcContainerName -SrcBlob $srcBlobName -DestContainer $destContainerName -DestBlob $destBlobName -StandardBlobTier Hot -RehydratePriority Standard -Context $ctx
```

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre os níveis de armazenamento blob](storage-blob-storage-tiers.md)
* [Verifique preços quentes, frescos e de arquivo nas contas blob e GPv2 por região](https://azure.microsoft.com/pricing/details/storage/)
* [Gerir o ciclo de vida do Armazenamento de Blobs do Azure](storage-lifecycle-management-concepts.md)
* [Verificar os preços das transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
