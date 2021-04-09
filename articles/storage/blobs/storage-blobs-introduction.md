---
title: Introdução ao armazenamento blob (objeto)
titleSuffix: Azure Storage
description: Utilize o armazenamento Azure Blob para armazenar quantidades massivas de dados de objetos não estruturados, tais como texto ou dados binários. O Armazenamento de blobs do Azure é altamente dimensionável e disponível.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6d71fc7931db77f0cf19a4e7ea70731ba19d1f6d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937316"
---
# <a name="introduction-to-azure-blob-storage"></a>Introdução ao Armazenamento de blobs do Azure

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>Recursos de armazenamento de bolhas

O armazenamento blob oferece três tipos de recursos:

- A **conta de armazenamento**
- Um **recipiente** na conta de armazenamento
- Uma **bolha** em um recipiente

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama mostrando a relação entre uma conta de armazenamento, contentores e bolhas](./media/storage-blobs-introduction/blob1.png)

### <a name="storage-accounts"></a>Contas de armazenamento

Uma conta de armazenamento fornece um espaço de nome único em Azure para os seus dados. Cada objeto que armazena no Azure Storage tem um endereço que inclui o nome da sua conta única. A combinação do nome da conta e do ponto final da bolha de armazenamento Azure forma o endereço base para os objetos na sua conta de armazenamento.

Por exemplo, se a sua conta de armazenamento for nomeada *mystorageaccount*, então o ponto final padrão para armazenamento Blob é:

```
http://mystorageaccount.blob.core.windows.net
```

Para criar uma conta de armazenamento, consulte [Criar uma conta de armazenamento.](../common/storage-account-create.md) Para saber mais sobre as contas de armazenamento, consulte [a visão geral da conta de armazenamento Azure](../common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="containers"></a>Contentores

Um contentor organiza um conjunto de bolhas, semelhantes a um diretório num sistema de ficheiros. Uma conta de armazenamento pode incluir um número ilimitado de contentores, e um contentor pode armazenar um número ilimitado de blobs.

> [!NOTE]
> O nome do contentor tem de ser em minúsculas. Para obter mais informações sobre o nome de recipientes, consulte [Naming e Referenciar Contentores, Bolhas e Metadados.](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)

### <a name="blobs"></a>Blobs

O Azure Storage suporta três tipos de bolhas:

- **As bolhas de bloco armazenam** texto e dados binários. Os blobs de blocos são constituídos por blocos de dados que podem ser geridos individualmente. As bolhas de blocos podem armazenar até cerca de 190,7 TiB.
- **As bolhas de apêndice** são compostas por blocos como bolhas de bloco, mas são otimizadas para operações de apêndice. Os blobs de acréscimo são ideais para cenários como o registo de dados a partir de máquinas virtuais.
- **As bolhas de página** armazenam ficheiros de acesso aleatório até 8 TiB de tamanho. As bolhas de página armazenam ficheiros de disco rígido virtual (VHD) e servem como discos para máquinas virtuais Azure. Para obter mais informações sobre as bolhas de página, consulte [a visão geral das bolhas da página de Azure](storage-blob-pageblob-overview.md)

Para obter mais informações sobre os diferentes tipos de bolhas, consulte [Understanding Block Blobs, Append Blobs e Page Blobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="move-data-to-blob-storage"></a>Move data to Blob storage (Mover dados para o armazenamento de Blobs)

Existem várias soluções para a migração dos dados existentes para o armazenamento da Blob:

- **O AzCopy** é uma ferramenta de linha de comando fácil de usar para Windows e Linux que copia dados de e para o armazenamento blob, através de contentores ou através de contas de armazenamento. Para obter mais informações sobre a AzCopy, consulte [os dados de transferência com o AzCopy v10](../common/storage-use-azcopy-v10.md).
- A **biblioteca Azure Storage Data Movement** é uma biblioteca .NET para transferir dados entre os serviços de armazenamento Azure. O utilitário AzCopy é construído com a biblioteca Do Movimento de Dados. Para mais informações, consulte a [documentação de referência](/dotnet/api/microsoft.azure.storage.datamovement) para a biblioteca do Movimento de Dados.
- **A Azure Data Factory** suporta a cópia de dados de e para o armazenamento blob usando a chave de conta, uma assinatura de acesso partilhado, um principal serviço ou identidades geridas para recursos Azure. Para obter mais informações, consulte [copiar dados para ou a partir do armazenamento da Azure Blob utilizando a Azure Data Factory](../../data-factory/connector-azure-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
- **Blobfuse** é um controlador de sistema de ficheiros virtual para o armazenamento Azure Blob. Pode utilizar o blobfuse para aceder aos dados de blob existentes na sua conta de Armazenamento através do sistema de ficheiros Linux. Para obter mais informações, consulte [Como montar o armazenamento Blob como um sistema de ficheiros com blobfuse](storage-how-to-mount-container-linux.md).
- O serviço **Azure Data Box** está disponível para transferir dados no local para armazenamento Blob quando grandes conjuntos de dados ou restrições de rede tornam os dados de upload sobre o fio irrealistas. Dependendo do tamanho dos dados, pode solicitar [a Azure Data Box Disk,](../../databox/data-box-disk-overview.md) [Azure Data Box](../../databox/data-box-overview.md)ou [Azure Data Box Dispositivos pesados](../../databox/data-box-heavy-overview.md) da Microsoft. Em seguida, pode copiar os seus dados para esses dispositivos e enviá-los de volta para a Microsoft para serem enviados para o armazenamento blob.
- O **serviço Azure Import/Export** fornece uma forma de importar ou exportar grandes quantidades de dados de e para a sua conta de armazenamento utilizando discos rígidos que fornece. Para obter mais informações, consulte [o serviço Microsoft Azure Import/Export para transferir dados para o armazenamento blob](../../import-export/storage-import-export-service.md).

## <a name="next-steps"></a>Passos seguintes

- [Criar uma conta de armazenamento](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Metas de escalabilidade e desempenho para armazenamento blob](scalability-targets.md)
