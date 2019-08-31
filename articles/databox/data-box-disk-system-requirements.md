---
title: Microsoft Azure Disco do Data Box requisitos do sistema | Microsoft Docs
description: Saiba mais sobre os requisitos de software e rede para seu Disco do Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 08/29/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 20c27a3e4e9a96a19b347e5ef57ab9fb3c047140
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194441"
---
::: zone target="docs"

# <a name="azure-data-box-disk-system-requirements"></a>Disco do Azure Data Box requisitos do sistema

Este artigo descreve os requisitos de sistema importantes para sua solução Microsoft Azure Disco do Data Box e para os clientes que se conectam ao Disco do Data Box. Recomendamos que você revise as informações cuidadosamente antes de implantar sua Disco do Data Box e, em seguida, reveja-as conforme necessário durante a implantação e a operação subsequente.

Os requisitos do sistema incluem as plataformas com suporte para clientes que se conectam a discos, contas de armazenamento com suporte e tipos de armazenamento.

::: zone-end

::: zone target="chromeless"

# <a name="review-prerequisites"></a>Examinar pré-requisitos

1. Você deve ter ordenado seu Disco do Data Box usando o tutorial: Ordene seu Disco do Azure Data Box. Você recebeu seus discos e um cabo de conexão por disco.
2. Você tem um computador cliente disponível do qual você pode copiar os dados. O computador cliente tem de:

    - Execute um sistema operacional com suporte.
    - Ter outros softwares necessários instalados.

::: zone-end

## <a name="supported-operating-systems-for-clients"></a>Sistemas operacionais com suporte para clientes

Aqui está uma lista dos sistemas operacionais com suporte para a operação de desbloqueio de disco e de cópia de dados por meio dos clientes conectados à Disco do Data Box.

| **Sistema operativo** | **Versões testadas** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows (64 bits) |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04, 16.04, 18.04 <br> 8,11, 9 <br> 7.0 <br> 6,5, 6,9, 7,0, 7,5 |  

## <a name="other-required-software-for-windows-clients"></a>Outros softwares necessários para clientes Windows

Para o cliente do Windows, o a seguir também deve ser instalado.

| **Software**| **Versão** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5.0|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Outros softwares necessários para clientes Linux

Para o cliente Linux, o conjunto de ferramentas Disco do Data Box instala o seguinte software necessário:

- desbloqueio
- OpenSSL

::: zone target="docs"

## <a name="supported-connection"></a>Conexão com suporte

O computador cliente que contém os dados deve ter uma porta USB 3,0 ou posterior. Os discos se conectam a este cliente usando o cabo fornecido.

## <a name="supported-storage-accounts"></a>Contas de armazenamento suportadas

Aqui está uma lista dos tipos de armazenamento com suporte para o Disco do Data Box.

| **Conta de armazenamento** | **Notas** |
| --- | --- |
| Clássica | Standard |
| Fins Gerais  |Standardization Há suporte para v1 e v2. Há suporte para as camadas quente e fria. |
| Conta de armazenamento de BLOBs | |

>[!NOTE]
> Não há suporte para contas de Gen 2 Azure Data Lake Storage.


## <a name="supported-storage-types-for-upload"></a>Tipos de armazenamento com suporte para upload

Aqui está uma lista dos tipos de armazenamento com suporte para carregar no Azure usando Disco do Data Box.

| **Formato de arquivo** | **Notas** |
| --- | --- |
| Blob de blocos do Azure | |
| Blob de páginas do Azure  | |
| Ficheiros do Azure  | |
| Managed Disks | |


## <a name="next-step"></a>Passo seguinte

* [Implantar seu Disco do Azure Data Box](data-box-disk-deploy-ordered.md)

::: zone-end

