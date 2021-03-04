---
title: Requisitos de sistema do Microsoft Azure Data Box Disk| Microsoft Docs
description: Saiba mais sobre o software e os requisitos de rede para o Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 02/22/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 750ed8f65db04199ea284e69693bced65a1dc8d9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703138"
---
::: zone target="docs"

# <a name="azure-data-box-disk-system-requirements"></a>Requisitos de sistema do Azure Data Box Disk

Este artigo descreve os requisitos de sistema importantes para a sua solução Microsoft Azure Data Box Disk e para os clientes que se ligam ao Data Box Disk. Recomendamos que reveja cuidadosamente as informações antes de implementar o Data Box Disk e, em seguida, reveja-as conforme necessário durante a implementação e a operação subsequente.

Os requisitos de sistema incluem as plataformas suportadas para os clientes que se ligam aos discos, contas de armazenamento suportadas e tipos de armazenamento.

::: zone-end

::: zone target="chromeless"

## <a name="review-prerequisites"></a>Rever pré-requisitos

1. Terá de ter encomendado o Data Box Disk através do [Tutorial: Encomende o Azure Data Box Disk](data-box-disk-deploy-ordered.md). Recebeu os seus discos e um cabo de ligação por disco.
2. Tem um computador cliente disponível a partir do qual pode copiar os dados. O computador cliente tem de:

    - Executar um sistema operativo suportado.
    - Ter outro software necessário instalado.

::: zone-end

## <a name="supported-operating-systems-for-clients"></a>Sistemas operativos suportados para clientes

Aqui está uma lista dos sistemas operativos suportados para a operação de desbloqueio de disco e de cópia de dados por meio dos clientes ligados ao Data Box Disk.

| **Sistema operativo** | **Versões testadas** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows (64 bits) |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04, 16.04, 18.04 <br> 8.11, 9 <br> 7.0 <br> 6.5, 6.9, 7.0, 7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Outro software necessário para clientes Windows

Para clientes Windows, também deve ser instalado o seguinte.

| **Software**| **Versão** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5.1|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Outro software necessário para clientes Linux

Para clientes Linux, o conjunto de ferramentas do Data Box Disk instala o seguinte software necessário:

- dislocker
- OpenSSL

## <a name="supported-connection"></a>Ligação suportada

O computador cliente que contém os dados tem de ter uma porta USB 3.0 ou posterior. Os discos ligam-se a este cliente através do cabo disponibilizado.

## <a name="supported-storage-accounts"></a>Contas de armazenamento do Azure

Aqui está uma lista dos tipos de armazenamento suportados para o Data Box Disk.

| **Conta de armazenamento** | **Notas** |
| --- | --- |
| Clássica | Standard |
| Fins Gerais  |Standard; quer a V1 como a V2 são suportadas. São suportados tanto os escalões de acesso frequente, como os de acesso esporádico. |
| Conta de armazenamento de blobs | |

> [!IMPORTANT]
> O suporte do protocolo 3.0 do Sistema de Ficheiros de Rede (NFS) 3.0 no armazenamento do Azure Blob não é suportado com o Disco de Caixa de Dados.

## <a name="supported-storage-types-for-upload"></a>Tipos de armazenamento suportados para carregamento

Aqui está uma lista dos tipos de armazenamento suportados para carregamento no Azure através do Data Box Disk.

| **Formato do ficheiro** | **Notas** |
| --- | --- |
| Blob de blocos do Azure | |
| Blob de páginas do Azure  | |
| Ficheiros do Azure  | |
| Managed Disks | |

::: zone target="docs"

## <a name="next-step"></a>Passo seguinte

* [Implementar o Azure Data Box Disk](data-box-disk-deploy-ordered.md)

::: zone-end

