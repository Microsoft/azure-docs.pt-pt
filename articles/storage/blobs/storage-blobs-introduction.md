---
title: Introdução ao armazenamento de BLOB (objeto)-armazenamento do Azure
description: O Armazenamento de blobs do Azure armazena grandes quantidades de dados de objetos não estruturados, como dados de texto ou binários. O Armazenamento de blobs do Azure é altamente dimensionável e disponível. Os clientes podem aceder a objetos de dados no Armazenamento de blobs do PowerShell ou da CLI do Azure, de forma programática através de bibliotecas de cliente do Armazenamento do Microsoft Azure ou através de REST.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 11/04/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 8071fe12b925384465c1e570465ebaa0100c53e9
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749801"
---
# <a name="introduction-to-azure-blob-storage"></a>Introdução ao Armazenamento de blobs do Azure

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>Recursos de armazenamento de BLOBs

O armazenamento de BLOBs oferece três tipos de recursos:

- A **conta de armazenamento**. 
- Um **contêiner** na conta de armazenamento
- Um **blob** em um contêiner 

O diagrama seguinte mostra a relação entre estes recursos.

![relação entre blob de conta e recurso de contêiner](./media/storage-blob-introduction/blob1.png)

### <a name="storage-accounts"></a>Contas de armazenamento

Uma conta de armazenamento fornece um namespace exclusivo no Azure para seus dados. Cada objeto que você armazena no armazenamento do Azure tem um endereço que inclui seu nome de conta exclusivo. A combinação do nome da conta e do ponto de extremidade do blob de armazenamento do Azure forma o endereço base para os objetos em sua conta de armazenamento.

Por exemplo, se sua conta de armazenamento for denominada *mystorageaccount*, o ponto de extremidade padrão para o armazenamento de BLOBs será:

```
http://mystorageaccount.blob.core.windows.net 
```

Para criar uma conta de armazenamento, consulte [criar uma conta de armazenamento](../common/storage-quickstart-create-account.md). Para saber mais sobre contas de armazenamento, confira [visão geral da conta de armazenamento do Azure](../common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="containers"></a>Contentores

Um contêiner organiza um conjunto de BLOBs, semelhante a um diretório em um sistema de arquivos. Uma conta de armazenamento pode incluir um número ilimitado de contentores, e um contentor pode armazenar um número ilimitado de blobs. 

  > [!NOTE]
  > O nome do contentor tem de ser em minúsculas. Para obter mais informações sobre como nomear contêineres, consulte [nomenclatura e referência de contêineres, BLOBs e metadados](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).

### <a name="blobs"></a>Blobs
 
O armazenamento do Azure dá suporte a três tipos de BLOBs:

* **Blobs de blocos** armazenam texto e dados binários, até cerca de 4,7 TB. Os blobs de blocos são constituídos por blocos de dados que podem ser geridos individualmente.
* Os **blobs de acréscimo** são compostos de blocos como BLOBs de blocos, mas são otimizados para operações de acréscimo. Os blobs de acréscimo são ideais para cenários como os dados de registo a partir de máquinas virtuais.
* **Blobs de páginas** armazenam arquivos de acesso aleatório de até 8 TB de tamanho. Os blobs de páginas armazenam arquivos de disco rígido virtual (VHD) e servem como discos para máquinas virtuais do Azure. Para obter mais informações sobre blobs de página, consulte [visão geral dos BLOBs de páginas do Azure](storage-blob-pageblob-overview.md)

Para obter mais informações sobre os diferentes tipos de BLOBs, consulte [noções básicas sobre blobs de blocos, blobs de acréscimo e blobs de páginas](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="move-data-to-blob-storage"></a>Mover dados para o armazenamento de BLOBs

Existem várias soluções para migrar dados existentes para o armazenamento de BLOBs:

- O **AzCopy** é uma ferramenta de linha de comando fácil de usar para Windows e Linux que copia dados de e para o armazenamento de BLOBs, entre contêineres ou entre contas de armazenamento. Para obter mais informações sobre AzCopy, consulte [transferir dados com o AzCopy v10 (versão prévia)](../common/storage-use-azcopy-v10.md). 
- A **biblioteca de movimentação de dados do armazenamento do Azure** é uma biblioteca .net para mover dados entre os serviços de armazenamento do Azure. O utilitário AzCopy é criado com a biblioteca de movimentação de dados. Para obter mais informações, consulte a [documentação de referência](/dotnet/api/microsoft.azure.storage.datamovement) para a biblioteca de movimentação de dados. 
- O **Azure data Factory** dá suporte à cópia de dados de e para o armazenamento de BLOBs usando a chave de conta, uma assinatura de acesso compartilhado, uma entidade de serviço ou identidades gerenciadas para recursos do Azure. Para obter mais informações, consulte [copiar dados de ou para o armazenamento de BLOBs do Azure usando Azure data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 
- **Blobfuse** é um driver de sistema de arquivos virtual para o armazenamento de BLOBs do Azure. Você pode usar o blobfuse para acessar seus dados de blob de blocos existentes em sua conta de armazenamento por meio do sistema de arquivos do Linux. Para obter mais informações, consulte [como montar o armazenamento de BLOBs como um sistema de arquivos com o blobfuse](storage-how-to-mount-container-linux.md).
- O serviço de **Azure data Box** está disponível para transferir dados locais para o armazenamento de BLOBs quando grandes conjuntos de dados ou restrições de rede fazem o upload do dado pela conexão não real. Dependendo do tamanho dos dados, você pode solicitar [disco do Azure data Box](../../databox/data-box-disk-overview.md), [Azure data Box](../../databox/data-box-overview.md)ou [Azure data Box Heavy](../../databox/data-box-heavy-overview.md) dispositivos da Microsoft. Você pode copiar seus dados para esses dispositivos e enviá-los de volta à Microsoft para serem carregados no armazenamento de BLOBs.
- O **serviço de importação/exportação do Azure** fornece uma maneira de importar ou exportar grandes quantidades de dados de e para sua conta de armazenamento usando discos rígidos que você fornecer. Para obter mais informações, consulte [usar o serviço de importação/exportação do Microsoft Azure para transferir dados para o armazenamento de BLOBs](../common/storage-import-export-service.md).

## <a name="next-steps"></a>Passos seguintes

- [Criar uma conta de armazenamento](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Escalabilidade e metas de desempenho para o armazenamento de BLOBs](scalability-targets.md)
