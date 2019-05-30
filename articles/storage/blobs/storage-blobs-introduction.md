---
title: Introdução ao Armazenamento de blobs – Armazenamento de objetos no Azure
description: O Armazenamento de blobs do Azure armazena grandes quantidades de dados de objetos não estruturados, como dados de texto ou binários. O Armazenamento de blobs do Azure é altamente dimensionável e disponível. Os clientes podem aceder a objetos de dados no Armazenamento de blobs do PowerShell ou da CLI do Azure, de forma programática através de bibliotecas de cliente do Armazenamento do Microsoft Azure ou através de REST.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 05/24/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e8a68ca87307e1265c200f2e05464ff87983c1e6
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258412"
---
# <a name="introduction-to-azure-blob-storage"></a>Introdução ao Armazenamento de blobs do Azure

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>Recursos de armazenamento de BLOBs

Armazenamento de BLOBs oferece três tipos de recursos:

- O **conta de armazenamento**. 
- R **contentor** na conta de armazenamento
- R **BLOBs** num contentor 

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama da arquitetura de armazenamento de BLOBs](./media/storage-blob-introduction/blob1.png)

### <a name="storage-accounts"></a>Contas de armazenamento

Uma conta de armazenamento fornece um espaço de nomes exclusivo no Azure para os seus dados. Cada objeto que armazena no armazenamento do Azure tem um endereço que inclui o seu nome de conta exclusivo. A combinação do nome da conta e o ponto de final do blob de armazenamento do Azure constitui o endereço base para os objetos na conta de armazenamento.

Por exemplo, se a sua conta de armazenamento com o nome *mystorageaccount*, em seguida, o ponto final predefinido para o armazenamento de BLOBs é:

```
http://mystorageaccount.blob.core.windows.net 
```

Para criar uma conta de armazenamento, veja [criar uma conta de armazenamento](../common/storage-quickstart-create-account.md). Para saber mais sobre as contas de armazenamento, veja [descrição geral da conta de armazenamento do Azure](../common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="containers"></a>Contentores

Um contentor organiza um conjunto de blobs, semelhantes a um diretório num sistema de ficheiros. Uma conta de armazenamento pode incluir um número ilimitado de contentores, e um contentor pode armazenar um número ilimitado de blobs. 

  > [!NOTE]
  > O nome do contentor tem de ser em minúsculas. Para obter mais informações sobre a nomenclatura de contentores, consulte [nomenclatura e referenciação de contentores, Blobs e metadados](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).

### <a name="blobs"></a>Blobs
 
O armazenamento do Azure suporta três tipos de blobs:

* **Blobs de blocos** armazenar dados de texto e binário, até 4,7 TB. Os blobs de blocos são constituídos por blocos de dados que podem ser geridos individualmente.
* **Blobs de acréscimo** são constituídos por blocos, como blobs de blocos, mas estão otimizados para operações de acréscimo. Os blobs de acréscimo são ideais para cenários como os dados de registo a partir de máquinas virtuais.
* **Blobs de páginas** acesso aleatório de armazenamento de ficheiros até 8 TB de tamanho. Os blobs de páginas arquivo que os ficheiros de disco rígido virtual (VHD) servem como discos de máquinas virtuais do Azure. Para obter mais informações sobre blobs de páginas, consulte [blobs de páginas de descrição geral do Azure](storage-blob-pageblob-overview.md)

Para obter mais informações sobre os diferentes tipos de blobs, veja [Noções básicas sobre Blobs de blocos, Blobs de acréscimo e Blobs de páginas](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="move-data-to-blob-storage"></a>Mover dados para o armazenamento de BLOBs

Existe um número de soluções para migrar dados existentes para o armazenamento de BLOBs:

- **AzCopy** é uma ferramenta de linha de comandos fácil de usar para Windows e Linux que copia dados de e para armazenamento de BLOBs, em contentores, ou em várias contas de armazenamento. Para obter mais informações sobre o AzCopy, veja [transferir dados com v10 o AzCopy (pré-visualização)](../common/storage-use-azcopy-v10.md). 
- O **biblioteca de movimento de dados de armazenamento do Azure** é uma biblioteca .NET para mover dados entre os serviços de armazenamento do Azure. O utilitário AzCopy baseia-se com a biblioteca de movimento de dados. Para obter mais informações, consulte a [documentação de referência](/dotnet/api/microsoft.azure.storage.datamovement) para a biblioteca de movimento de dados. 
- **O Azure Data Factory** suporta copiar dados de e para armazenamento de BLOBs por meio da chave de conta, a assinatura de acesso partilhado, service identidades principal ou gerenciadas para autenticações de recursos do Azure. Para obter mais informações, consulte [copiar dados de ou para armazenamento de Blobs do Azure com o Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 
- **Blobfuse** é um driver de sistema de arquivos virtual para o armazenamento de Blobs do Azure. Pode usar blobfuse para aceder aos dados de BLOBs de bloco existente na sua conta de armazenamento através do sistema de ficheiros do Linux. Para obter mais informações, consulte [como armazenamento de BLOBs de montagem como um sistema de ficheiros com blobfuse](storage-how-to-mount-container-linux.md).
- **O Azure Data Box** serviço está disponível para transferência de dados no local para o armazenamento de BLOBs quando grandes conjuntos de dados ou as restrições de rede fazem carregamento de dados durante a transmissão irreal. Dependendo do tamanho dos dados, pode pedir [disco do Azure Data Box](../../databox/data-box-disk-overview.md), [do Azure Data Box](../../databox/data-box-overview.md), ou [pesadas de caixa de dados do Azure](../../databox/data-box-heavy-overview.md) dispositivos da Microsoft. Em seguida, pode copiar os dados para esses dispositivos e envie-os à Microsoft para ser carregado para o armazenamento de Blobs.
- O **serviço importar/exportar do Azure** fornece uma forma de importar ou exportar grandes quantidades de dados de e para sua conta de armazenamento com discos rígidos que fornece. Para obter mais informações, consulte [utilizar o serviço de importação/exportação do Microsoft Azure para transferir dados para o armazenamento de BLOBs](../common/storage-import-export-service.md).

## <a name="next-steps"></a>Passos Seguintes

* [Criar uma conta de armazenamento](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure destinos de escalabilidade e desempenho de armazenamento](../common/storage-scalability-targets.md)
