---
title: Introdução ao armazenamento blob (objeto) - Armazenamento Azure
description: O Armazenamento de blobs do Azure armazena grandes quantidades de dados de objetos não estruturados, como dados de texto ou binários. O Armazenamento de blobs do Azure é altamente dimensionável e disponível. Os clientes podem aceder a objetos de dados no Armazenamento de blobs do PowerShell ou da CLI do Azure, de forma programática através de bibliotecas de cliente do Armazenamento do Microsoft Azure ou através de REST.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 03/04/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: aeb6971d37601367e62b8f883ccbc576e9534cdb
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357570"
---
# <a name="introduction-to-azure-blob-storage"></a>Introdução ao Armazenamento de blobs do Azure

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>Recursos de armazenamento blob

O armazenamento blob oferece três tipos de recursos:

- A **conta de armazenamento**
- Um **recipiente** na conta de armazenamento
- Uma **bolha** em um recipiente

O diagrama seguinte mostra a relação entre estes recursos.

![relação entre o recurso De conta Blob e Container](./media/storage-blobs-introduction/blob1.png)

### <a name="storage-accounts"></a>Contas de armazenamento

Uma conta de armazenamento fornece um espaço de nome único em Azure para os seus dados. Cada objeto que armazena no Azure Storage tem um endereço que inclui o nome único da conta. A combinação do nome da conta e do ponto final do ponto final do depósito azure forma o endereço base para os objetos na sua conta de armazenamento.

Por exemplo, se a sua conta de armazenamento for nomeada minha conta de *armazenamento,* então o ponto final padrão para o armazenamento blob é:

```
http://mystorageaccount.blob.core.windows.net 
```

Para criar uma conta de armazenamento, consulte [Criar uma conta](../common/storage-account-create.md)de armazenamento . Para saber mais sobre as contas de armazenamento, consulte a visão geral da conta de [armazenamento do Azure.](../common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="containers"></a>Contentores

Um recipiente organiza um conjunto de bolhas, semelhantes a um diretório num sistema de ficheiros. Uma conta de armazenamento pode incluir um número ilimitado de contentores, e um contentor pode armazenar um número ilimitado de blobs. 

  > [!NOTE]
  > O nome do contentor tem de ser em minúsculas. Para obter mais informações sobre o nome de contentores, consulte [Contentores de Nomeação e Referência, Blobs e Metadados](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).

### <a name="blobs"></a>Blobs
 
O Armazenamento Azure suporta três tipos de bolhas:

- **Bloqueie blobs** armazenar texto e dados binários, até cerca de 4,7 TB. Os blobs de blocos são constituídos por blocos de dados que podem ser geridos individualmente.
- As bolhas de apêndice são **compostas** por blocos como blocos de blocos, mas são otimizadas para operações de apêndice. Os blobs de acréscimo são ideais para cenários como os dados de registo a partir de máquinas virtuais.
- **As bolhas de página** armazenam ficheiros de acesso aleatório até 8 TB de tamanho. As bolhas de página armazenam ficheiros de disco rígido virtual (VHD) e servem como discos para máquinas virtuais Azure. Para mais informações sobre as bolhas de página, consulte a [visão geral das bolhas de página azure](storage-blob-pageblob-overview.md)

Para obter mais informações sobre os diferentes tipos de bolhas, consulte [Understanding Block Blobs, Append Blobs e Page Blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="move-data-to-blob-storage"></a>Mova dados para o armazenamento blob

Existem várias soluções para a migração dos dados existentes para o armazenamento da Blob:

- **O AzCopy** é uma ferramenta de linha de comando fácil de usar para Windows e Linux que copia dados de e para o armazenamento blob, através de contentores ou através de contas de armazenamento. Para mais informações sobre o AzCopy, consulte [os dados de transferência com o AzCopy v10 (Pré-visualização)](../common/storage-use-azcopy-v10.md). 
- A **biblioteca Azure Storage Data Movement** é uma biblioteca .NET para movimentar dados entre os serviços de Armazenamento Azure. O utilitário AzCopy é construído com a biblioteca Data Movement. Para mais informações, consulte a [documentação de referência](/dotnet/api/microsoft.azure.storage.datamovement) para a biblioteca do Movimento de Dados. 
- A **Azure Data Factory** suporta a cópia de dados de e para o armazenamento da Blob utilizando a chave de conta, uma assinatura de acesso partilhado, um diretor de serviço ou identidades geridas para os recursos Do Azure. Para mais informações, consulte [os dados da Cópia de ou para o armazenamento da Blob Azure utilizando](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)a Azure Data Factory . 
- **Blobfuse** é um controlador virtual do sistema de ficheiros para o armazenamento de Azure Blob. Pode utilizar blobfuse para aceder aos dados de blob de bloco existentes na sua conta de Armazenamento através do sistema de ficheiros Linux. Para mais informações, consulte como montar o armazenamento blob como um sistema de [ficheiros com blobfuse](storage-how-to-mount-container-linux.md).
- O serviço **Azure Data Box** está disponível para transferir dados no local para o armazenamento blob quando grandes conjuntos de dados ou restrições de rede tornam o upload de dados através do fio irrealista. Dependendo do tamanho dos dados, pode solicitar o Disco de Caixa de [Dados Azure,](../../databox/data-box-disk-overview.md)a Caixa de [Dados Azure](../../databox/data-box-overview.md)ou os dispositivos Heavy Da Caixa de [Dados Azure](../../databox/data-box-heavy-overview.md) da Microsoft. Em seguida, pode copiar os seus dados para esses dispositivos e enviá-los de volta para a Microsoft para serem enviados para o armazenamento blob.
- O **serviço azure import/exportação** fornece uma forma de importar ou exportar grandes quantidades de dados de e para a sua conta de armazenamento utilizando discos rígidos que fornece. Para mais informações, consulte [Utilize o serviço de importação/exportação do Microsoft Azure para transferir dados para o armazenamento blob](../common/storage-import-export-service.md).

## <a name="next-steps"></a>Passos seguintes

- [Criar uma conta de armazenamento](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Metas de escalabilidade e desempenho para armazenamento blob](scalability-targets.md)
