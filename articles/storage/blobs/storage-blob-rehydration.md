---
title: Rehidratar os dados blob do nível de arquivo
description: Reidrate as suas bolhas a partir do armazenamento de arquivo para que possa aceder aos dados.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 1265d018997f9540e14e83ab15a44e78f4f86fb1
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402668"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Rehidratar os dados blob do nível de arquivo

Enquanto uma bolha está no nível de acesso ao arquivo, é considerada offline e não pode ser lida ou modificada. Os metadados blob permanecem on-line e disponíveis, permitindo-lhe listar a bolha e as suas propriedades. A leitura e modificação dos dados blob só está disponível com níveis online como quente ou fresco. Existem duas opções para recuperar e aceder aos dados armazenados no nível de acesso ao arquivo.

1. [Reidratar uma bolha arquivada para um nível on-line](#rehydrate-an-archived-blob-to-an-online-tier) - Reidratar uma bolha de arquivo para quente ou fresco, alterando o seu nível utilizando a operação [set Blob Tier.](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)
2. [Copie uma bolha arquivada para um nível online](#copy-an-archived-blob-to-an-online-tier) - Crie uma nova cópia de uma bolha de arquivo utilizando a operação [Copy Blob.](https://docs.microsoft.com/rest/api/storageservices/copy-blob) Especifique um nome blob diferente e um nível de destino quente ou fresco.

 Para obter mais informações sobre os níveis, consulte o [armazenamento do Azure Blob: hot, cool e archive access tiers](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Reidratar uma bolha arquivada para um nível online

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Copiar um blob arquivado para uma camada online

Se não quiser reidratar a sua bolha de arquivo, pode optar por fazer uma operação [Copy Blob.](https://docs.microsoft.com/rest/api/storageservices/copy-blob) A sua bolha original permanecerá inalterada no arquivo enquanto uma nova bolha é criada no nível quente ou fresco online para que você trabalhe. Na operação Copy Blob, também pode definir a propriedade prioritária *x-ms-rehidratada* opcional para Standard ou High para especificar a prioridade a que pretende que a sua cópia blob seja criada.

Copiar uma bolha do arquivo pode levar horas a concluir dependendo da prioridade de reidratação selecionada. Nos bastidores, a operação **Copy Blob** lê a sua bolha de fonte de arquivo para criar uma nova bolha online no nível de destino selecionado. A nova bolha pode ser visível quando lista bolhas, mas os dados não estão disponíveis até que a leitura da bolha de arquivo de origem esteja completa e os dados sejam escritos para a nova bolha de destino online. A nova bolha é como uma cópia independente e qualquer modificação ou eliminação não afeta a bolha de arquivo de origem.

> [!IMPORTANT]
> Não elimine a bolha de origem até que a cópia esteja concluída com sucesso no destino. Se a bolha de origem for eliminada, a bolha de destino pode não completar a cópia e ficará vazia. Pode verificar o *estado da cópia x-ms* para determinar o estado da operação de cópia.

As bolhas de arquivo só podem ser copiadas para níveis de destino on-line dentro da mesma conta de armazenamento. Não é suportada a cópia de uma bolha de arquivo para outra bolha de arquivo. A tabela que se segue indica as capacidades do CopyBlob.

|                                           | **Fonte de nível quente**   | **Fonte de nível legal** | **Fonte de nível de arquivo**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **Destino de nível quente**                  | Suportado             | Suportado            | Suportado na mesma conta; pendente rehidratar               |
| **Destino de nível legal**                 | Suportado             | Suportado            | Suportado na mesma conta; pendente rehidratar               |
| **Destino de nível de arquivo**              | Suportado             | Suportado            | Não suportado         |

## <a name="pricing-and-billing"></a>Preços e faturação

As bolhas hidratantes fora do arquivo em níveis quentes ou frescos são carregadas como operações de leitura e recuperação de dados. A utilização de alta prioridade tem custos de operação e recuperação de dados mais elevados em comparação com a prioridade padrão. A reidratação de alta prioridade aparece como um item de linha separado na sua conta. Se um pedido de alta prioridade para devolver uma bolha de arquivo de alguns gigabytes demorar mais de 5 horas, não lhe será cobrada a alta taxa de recuperação prioritária. No entanto, as taxas de recuperação padrão continuam a aplicar-se à medida que a reidratação foi priorizada em relação a outros pedidos.

A cópia das bolhas do arquivo para níveis quentes ou frescos é cobrada como operações de leitura e recuperação de dados. É cobrada uma operação de escrita para a criação da nova cópia blob. As taxas de eliminação antecipada não se aplicam quando copia para uma bolha online porque a bolha de origem permanece inalterada no nível de arquivo. Os encargos de recuperação de alta prioridade aplicam-se se forem selecionados.

As bolhas no nível de arquivo devem ser armazenadas durante um período mínimo de 180 dias. A eliminação ou rehidratação de bolhas arquivadas antes de 180 dias incorrerá em taxas de eliminação antecipadas.

> [!NOTE]
> Para obter mais informações sobre preços para blocos e reidratação de dados, consulte O [Preço de Armazenamento De Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). Para obter mais informações sobre os encargos de transferência de dados de saída, consulte dados de preços de [transferências](https://azure.microsoft.com/pricing/details/data-transfers/)de dados .

## <a name="quickstart-scenarios"></a>Cenários de início rápido

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>Reidratar uma bolha de arquivo para um nível online
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No portal Azure, procure e selecione **Todos os Recursos.**

1. Selecione a sua conta de armazenamento.

1. Selecione o seu recipiente e, em seguida, selecione a sua bolha.

1. Nas **propriedades Blob,** selecione **Nível De alteração**.

1. Selecione o nível de acesso **Hot** ou **Cool.** 

1. Selecione uma Prioridade de Rehidratação de **Padrão** ou **Alta**.

1. Selecione **Guardar** na parte inferior.

![Alterar o nível da conta de armazenamento Verifique o estado do ](media/storage-tiers/blob-access-tier.png)
 ![ rehidratado](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
O seguinte script PowerShell pode ser usado para alterar o nível de blob de uma bolha de arquivo. A `$rgName` variável deve ser inicializada com o nome do seu grupo de recursos. A `$accountName` variável deve ser inicializada com o nome da sua conta de armazenamento. A `$containerName` variável deve ser inicializada com o nome do recipiente. A `$blobName` variável deve ser inicializada com o seu nome blob. 
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
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", “Standard”)
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>Copie uma bolha de arquivo para uma nova bolha com um nível on-line
O seguinte script PowerShell pode ser usado para copiar uma bolha de arquivo para uma nova bolha dentro da mesma conta de armazenamento. A `$rgName` variável deve ser inicializada com o nome do seu grupo de recursos. A `$accountName` variável deve ser inicializada com o nome da sua conta de armazenamento. As `$srcContainerName` `$destContainerName` variáveis e variáveis devem ser inicializadas com os nomes dos seus recipientes. As `$srcBlobName` `$destBlobName` variáveis e variáveis devem ser inicializadas com os vossos nomes blob. 
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

* [Saiba mais sobre blob Storage Tiers](storage-blob-storage-tiers.md)
* [Verifique preços quentes, frescos e de arquivo no armazenamento blob e contas GPv2 por região](https://azure.microsoft.com/pricing/details/storage/)
* [Gerir o ciclo de vida do Armazenamento de Blobs do Azure](storage-lifecycle-management-concepts.md)
* [Verificar os preços das transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
