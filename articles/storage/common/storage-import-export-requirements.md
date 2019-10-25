---
title: Requisitos para o serviço de importação/exportação do Azure | Microsoft Docs
description: Compreenda os requisitos de software e hardware para o serviço de importação/exportação do Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: b6dffb46d8c0fd7201079de3e8509ece516d2f8f
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821407"
---
# <a name="azure-importexport-system-requirements"></a>Requisitos do sistema de importação/exportação do Azure

Este artigo descreve os requisitos importantes para o serviço de importação/exportação do Azure. É recomendável revisar as informações cuidadosamente antes de usar o serviço de importação/exportação e, em seguida, referir-se a ela conforme necessário durante a operação.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

Para preparar os discos rígidos usando a ferramenta WAImportExport, há suporte para o seguinte **sistema operacional de 64 bits que oferece suporte a criptografia de unidade de disco BitLocker** .


|Plataforma |Versão |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Outros softwares necessários para o Windows Client

|Plataforma |Versão |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Contas de armazenamento do Azure

O serviço de importação/exportação do Azure dá suporte aos seguintes tipos de contas de armazenamento:

- Contas de armazenamento Standard Uso Geral v2 (recomendado para a maioria dos cenários)
- Contas de Armazenamento de Blobs
- Uso Geral contas de armazenamento V1 (implantações clássicas ou Azure Resource Manager), 

Para obter mais informações sobre contas de armazenamento, consulte [visão geral das contas de armazenamento do Azure](storage-account-overview.md).

Cada trabalho pode ser usado para transferir dados de ou para apenas uma conta de armazenamento. Em outras palavras, um único trabalho de importação/exportação não pode se estender por várias contas de armazenamento. Para obter informações sobre como criar uma nova conta de armazenamento, consulte [como criar uma conta de armazenamento](storage-quickstart-create-account.md).

> [!IMPORTANT] 
> O serviço de exportação de importação do Azure não dá suporte a contas de armazenamento em que os [pontos de extremidade de serviço de rede Virtual](../../virtual-network/virtual-network-service-endpoints-overview.md) recurso foi habilitado. 

## <a name="supported-storage-types"></a>Tipos de armazenamento com suporte

A lista de tipos de armazenamento a seguir tem suporte com o serviço de importação/exportação do Azure.


|Tarefa  |Serviço de armazenamento |Suportadas  |Não suportado  |
|---------|---------|---------|---------|
|Importar     |  Armazenamento de Blobs do Azure <br><br> Armazenamento de arquivos do Azure       | Blobs de blocos e blobs de páginas com suporte <br><br> Arquivos com suporte          |
|Exportar     |   Armazenamento de Blobs do Azure       | Blobs de blocos, blobs de páginas e blobs de acréscimo com suporte         | Arquivos do Azure sem suporte


## <a name="supported-hardware"></a>Hardware com suporte 

Para o serviço de importação/exportação do Azure, você precisa de discos com suporte para copiar dados.

### <a name="supported-disks"></a>Discos com suporte

A lista de discos a seguir tem suporte para uso com o serviço de importação/exportação.


|Tipo de disco  |Tamanho  |Suportadas |
|---------|---------|---------|
|SSD    |   2,5 "      |SATA III          | 
|HDD     |  2,5 "<br>3,5 "       |SATA II, SATA III         |

Não há suporte para os seguintes tipos de disco:
- USBs.
- HDD externo com adaptador USB interno.
- Discos que estão dentro dos compartimentos de uma HDD externa.

Um único trabalho de importação/exportação pode ter:
- Um máximo de 10 HDD/SSDs.
- Uma mistura de HDD/SSD de qualquer tamanho.

Um grande número de unidades pode ser distribuído em vários trabalhos e não há limites no número de trabalhos que podem ser criados. Para trabalhos de importação, somente o primeiro volume de dados na unidade é processado. O volume de dados deve ser formatado com NTFS.

Ao preparar discos rígidos e copiar os dados usando a ferramenta WAImportExport, você pode usar adaptadores USB externos. A maioria dos adaptadores USB 3,0 ou posteriores disponíveis no mercado devem funcionar. 


## <a name="next-steps"></a>Passos seguintes

* [Configurar a ferramenta WAImportExport](storage-import-export-tool-how-to.md)
* [Transferir dados com o utilitário de linha de comandos AzCopy](storage-use-azcopy.md)
* [Exemplo de API REST de importação e exportação do Azure](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)

