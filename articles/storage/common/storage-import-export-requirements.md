---
title: Requisitos para o serviço importar/exportar do Azure | Documentos da Microsoft
description: Compreenda os requisitos de software e hardware para o serviço importar/exportar do Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 58997b20c01f33037a5e5e149caa59e1630373ff
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978479"
---
# <a name="azure-importexport-system-requirements"></a>Requisitos de sistema de importação/exportação do Azure

Este artigo descreve os requisitos importantes para o seu serviço importar/exportar do Azure. Recomendamos que reveja as informações cuidadosamente antes de utilizar o serviço importar/exportar e, em seguida, regressar à mesma conforme necessário durante a operação.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

Para preparar as unidades de disco rígido usando a ferramenta de WAImportExport, o seguinte procedimento **SO de 64 bits que suportam encriptação de unidade BitLocker** são suportados.


|Plataforma |Versão |
|---------|---------|
|Windows     | O Windows 7 Enterprise, Windows 7 Ultimate <br> O Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Outro software necessário para o cliente do Windows

|Plataforma |Versão |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Contas de armazenamento do Azure

O serviço importar/exportar do Azure suporta os seguintes tipos de contas de armazenamento:

- Contas de armazenamento Standard Uso Geral v2 (recomendado para a maioria dos cenários)
- Contas de Armazenamento de Blobs
- Uso Geral contas de armazenamento V1 (implantações clássicas ou Azure Resource Manager),

Para obter mais informações sobre contas de armazenamento, consulte [descrição geral de contas de armazenamento do Azure](storage-account-overview.md).

Cada tarefa pode ser utilizada para transferir dados de ou para apenas uma conta de armazenamento. Em outras palavras, uma tarefa de importação/exportação única não pode abranger várias várias contas de armazenamento. Para obter informações sobre como criar uma nova conta de armazenamento, consulte [como criar uma conta de armazenamento](storage-account-create.md).

> [!IMPORTANT]
> O serviço de exportação de importação do Azure não suporta contas de armazenamento onde o [pontos finais de serviço de rede Virtual](../../virtual-network/virtual-network-service-endpoints-overview.md) funcionalidade foi ativada. 

## <a name="supported-storage-types"></a>Tipos de armazenamento suportadas

A seguinte lista de tipos de armazenamento é suportada com o serviço importar/exportar do Azure.


|Tarefa  |Serviço de armazenamento |Suportadas  |Não suportado  |
|---------|---------|---------|---------|
|Importar     |  Armazenamento de Blobs do Azure <br><br> Armazenamento de ficheiros do Azure       | Blobs de página e Blobs de blocos suportados <br><br> Ficheiros suportados          |
|Exportar     |   Armazenamento de Blobs do Azure       | Os blobs de blocos, blobs de páginas e blobs de acréscimo suportados         | Ficheiros do Azure não suportados


## <a name="supported-hardware"></a>Hardware suportado

Para o serviço importar/exportar do Azure, terá de discos suportados para copiar dados.

### <a name="supported-disks"></a>Discos suportados

A seguinte lista de discos é suportada para utilização com o serviço de importação/exportação.


|Tipo de disco  |Tamanho  |Suportadas |
|---------|---------|---------|
|SSD    |   2,5"      |SATA III          |
|HDD     |  2,5"<br>3.5"       |SATA II, SATA III         |

Não há suporte para os seguintes tipos de disco:
- USBs.
- HDD externo com adaptador USB interno.
- Discos que estão dentro dos compartimentos de uma HDD externa.

Uma tarefa de importação/exportação única pode ter:
- Um máximo de 10 HDD/SSDs.
- Uma combinação de HDD/SSD de qualquer tamanho.

Grande número de unidades de serem distribuído por várias tarefas e não sem limites no número de tarefas que podem ser criadas. Para tarefas de importação, apenas o primeiro volume de dados na unidade é processado. O volume de dados deve ser formatado com NTFS.

Quando preparar unidades de disco rígido e copiar os dados usando a ferramenta de WAImportExport, pode utilizar adaptadores USB externas. Mais prontas para utilização USB 3.0 ou posteriores adaptadores devem funcionar.


## <a name="next-steps"></a>Passos seguintes

* [Configurar a ferramenta de WAImportExport](storage-import-export-tool-how-to.md)
* [Transferir dados com o utilitário de linha de comandos AzCopy](storage-use-azcopy.md)
* [Exemplo de importar exportar a API de REST do Azure](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
