---
title: Requisitos relativos ao serviço de importação/exportação Azure | Microsoft Docs
description: Compreenda os requisitos de software e hardware para o serviço Azure Import/Export.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 0bfc09a372584a25c23060cef33d1f698e6d5ff3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712611"
---
# <a name="azure-importexport-system-requirements"></a>Requisitos do sistema do sistema Importar/Exportar do Microsoft Azure

Este artigo descreve os requisitos importantes para o seu serviço de importação/exportação Azure. Recomendamos que reveja as informações cuidadosamente antes de utilizar o serviço de Importação/Exportação e, em seguida, reencasse as informações necessárias durante a operação.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

Para preparar os discos rígidos utilizando a ferramenta WAImportExport, são suportados os **seguintes SISTEMAS de 64 bits que suportam a Encriptação bitLocker Drive.**


|Plataforma |Versão |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Outro software necessário para o cliente Windows

|Plataforma |Versão |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Contas de armazenamento do Azure

O serviço Azure Import/Export suporta os seguintes tipos de contas de armazenamento:

- Contas de armazenamento v2 de finalidade geral (recomendadas para a maioria dos cenários)
- Contas de Armazenamento de blobs
- Contas de armazenamento De Fins Gerais v1 (implementações clássicas ou azure de gestores de recursos)

> [!IMPORTANT]
> O suporte do protocolo 3.0 do Sistema de Ficheiros de Rede (NFS) 3.0 no armazenamento da Azure Blob não é suportado com Azure Import/Export.

Para obter mais informações sobre contas de armazenamento, consulte [a visão geral das contas de armazenamento do Azure.](../storage/common/storage-account-overview.md)

Cada trabalho pode ser utilizado para transferir dados de ou para uma única conta de armazenamento. Por outras palavras, uma única empresa de importação/exportação não pode abranger várias contas de armazenamento. Para obter informações sobre a criação de uma nova conta de armazenamento, consulte [Como Criar uma Conta de Armazenamento.](../storage/common/storage-account-create.md)

> [!IMPORTANT]
> Para contas de armazenamento onde a funcionalidade [Endpoints do Serviço de Rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md) foi ativada, utilize a definição de Permitir **serviços da Microsoft fidedignos...** para permitir que o serviço [de importação/exportação](../storage/common/storage-network-security.md) efetue a importação/exportação de dados de/para a Azure.

## <a name="supported-storage-types"></a>Tipos de armazenamento suportados

A seguinte lista de tipos de armazenamento é suportada com o serviço Azure Import/Export.


|Tarefa  |Serviço de Armazenamento |Suportado  |Não suportado  |
|---------|---------|---------|---------|
|Importar     |  Armazenamento de Blobs do Azure <br><br> Armazenamento de Ficheiros do Azure       | Blobs de blocos e bolhas de página suportadas <br><br> Ficheiros suportados          |
|Exportar     |   Armazenamento de Blobs do Azure       | Blobs de blocos, bolhas de página e bolhas de apêndice suportadas         | Ficheiros Azure não suportados


## <a name="supported-hardware"></a>Hardware suportado

Para o serviço Azure Import/Export, precisa de discos suportados para copiar dados.

### <a name="supported-disks"></a>Discos suportados

A seguinte lista de discos é suportada para utilização com o serviço de importação/exportação.


|Tipo de disco  |Tamanho  |Suportado |
|---------|---------|---------|
|SSD    |   2.5"      |SATA III          |
|HDD     |  2.5"<br>3.5"       |SATA II         |

Os seguintes tipos de disco não são suportados:

- USBs.
- HDD externo com adaptador USB incorporado.
- Discos que estão dentro do invólucro de um HDD externo.

Uma única empresa de importação/exportação pode ter:

- Um máximo de 10 HDD/SSDs.
- Uma mistura de HDD/SSD de qualquer tamanho.

Um grande número de unidades de unidades pode ser distribuída por vários postos de trabalho e não há limites para o número de postos de trabalho que podem ser criados. Para os trabalhos de importação, apenas é processado o primeiro volume de dados sobre a unidade. O volume de dados deve ser formatado com NTFS.

Ao preparar discos rígidos e copiar os dados utilizando a ferramenta WAImportExport, pode utilizar adaptadores USB externos. A maioria dos adaptadores USB 3.0 ou posteriormente fora da prateleira devem funcionar.

## <a name="next-steps"></a>Passos seguintes

* [Transferir dados com o utilitário de linha de comando AzCopy](../storage/common/storage-use-azcopy-v10.md)