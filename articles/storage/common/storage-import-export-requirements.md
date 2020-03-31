---
title: Requisitos para o serviço de importação/exportação azure [ Microsoft Docs
description: Compreenda os requisitos de software e hardware para o serviço De Importação/Exportação Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 58997b20c01f33037a5e5e149caa59e1630373ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255317"
---
# <a name="azure-importexport-system-requirements"></a>Requisitos do sistema do sistema Importar/Exportar do Microsoft Azure

Este artigo descreve os requisitos importantes para o seu serviço de importação/exportação Azure. Recomendamos que reveja cuidadosamente as informações antes de utilizar o serviço de importação/exportação e, em seguida, remeta-a conforme necessário durante a operação.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

Para preparar os discos rígidos utilizando a ferramenta WAImportExport, são suportados os seguintes **64 bits OS que suportam a encriptação bitLocker Drive.**


|Plataforma |Versão |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Outro software necessário para cliente Windows

|Plataforma |Versão |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Contas de armazenamento do Azure

O serviço azure import/exportação suporta os seguintes tipos de contas de armazenamento:

- Contas de armazenamento Padrão General Purpose v2 (recomendadas para a maioria dos cenários)
- Contas de Armazenamento de blobs
- Contas de armazenamento V1 de Propósito Geral (ambas implantações de Classic ou Azure Resource Manager),

Para obter mais informações sobre contas de armazenamento, consulte a visão geral das contas de [armazenamento do Azure.](storage-account-overview.md)

Cada trabalho pode ser utilizado para transferir dados para ou a partir de apenas uma conta de armazenamento. Por outras palavras, um único trabalho de importação/exportação não pode abranger várias contas de armazenamento. Para obter informações sobre a criação de uma nova conta de armazenamento, consulte [Como Criar uma Conta de Armazenamento](storage-account-create.md).

> [!IMPORTANT]
> O serviço de exportação de importações azure não suporta contas de armazenamento onde a funcionalidade [Final points](../../virtual-network/virtual-network-service-endpoints-overview.md) do Serviço de Rede Virtual foi ativada. 

## <a name="supported-storage-types"></a>Tipos de armazenamento suportados

A seguinte lista de tipos de armazenamento é suportada com o serviço azure import/exportação.


|Tarefa  |Serviço de Armazenamento |Suportado  |Não suportado  |
|---------|---------|---------|---------|
|Importar     |  Armazenamento de Blobs do Azure <br><br> Armazenamento de Ficheiros do Azure       | Bloco blobs e blocos de página suportados <br><br> Ficheiros suportados          |
|Exportar     |   Armazenamento de Blobs do Azure       | Blocos de bolhas, bolhas de página e bolhas de apêndice suportados         | Ficheiros Azure não suportados


## <a name="supported-hardware"></a>Hardware suportado

Para o serviço azure import/exportação, precisa de discos suportados para copiar dados.

### <a name="supported-disks"></a>Discos suportados

A seguinte lista de discos é suportada para utilização com o serviço de importação/exportação.


|Tipo de disco  |Tamanho  |Suportado |
|---------|---------|---------|
|SSD    |   2.5"      |SATA III          |
|HDD     |  2.5"<br>3.5"       |SATA II, SATA III         |

Os seguintes tipos de disco não são suportados:
- USBs.
- HDD externo com adaptador USB incorporado.
- Discos que estão dentro do invólucro de um HDD externo.

Um único trabalho de importação/exportação pode ter:
- Um máximo de 10 HDD/SSDs.
- Uma mistura de HDD/SSD de qualquer tamanho.

Um grande número de unidades pode ser distribuída por vários postos de trabalho e não há limites para o número de postos de trabalho que podem ser criados. No que diz matéria de empregos importados, apenas o primeiro volume de dados da unidade é tratado. O volume de dados deve ser formatado com NTFS.

Ao preparar discos rígidos e copiar os dados utilizando a ferramenta WAImportExport, pode utilizar adaptadores USB externos. A maioria dos adaptadores USB 3.0 ou posteriores devem funcionar.


## <a name="next-steps"></a>Passos seguintes

* [Configurar a ferramenta WAImportExport](storage-import-export-tool-how-to.md)
* [Transferir dados com o utilitário da linha de comando AzCopy](storage-use-azcopy.md)
* [Amostra de API de exportação de importação de azure](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
