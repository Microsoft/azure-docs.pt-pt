---
title: Requisitos de sistema de disco do Microsoft Azure Data Box | Documentos da Microsoft
description: Saiba mais sobre o software e os requisitos de rede para o seu disco do Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 02/22/2019
ms.author: alkohli
ms.openlocfilehash: 067bb6b806ddd9b83d4ea755876a980ca45d76e9
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57407634"
---
# <a name="azure-data-box-disk-system-requirements"></a>Requisitos de sistema de disco de caixa de dados do Azure

Este artigo descreve os requisitos de sistema importante para a sua solução de disco do Microsoft Azure Data Box e para os clientes a ligar o disco do Data Box. Recomendamos que reveja as informações cuidadosamente antes de implementar o disco do Data Box e, em seguida, regressar mais tarde a ela conforme necessário durante a implantação e operação subseqüente.

Os requisitos de sistema incluem as plataformas suportadas para clientes que se conectam a discos, as contas de armazenamento suportados e tipos de armazenamento.


## <a name="supported-operating-systems-for-clients"></a>Sistemas operativos suportados por clientes

Eis uma lista de sistemas operativos suportados para desbloquear o disco e a operação de cópia de dados por meio dos clientes ligados para o disco do Data Box.

| **Sistema operativo** | **Versões testadas** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04, 16.04, 18.04 <br> 8.11, 9 <br> 7.0 <br> 6.5, 6.9, 7.0, 7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Outro software necessário para clientes do Windows

Para o cliente do Windows, os seguintes também devem ser instalados.

| **Software**| **Versão** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Estrutura de gerenciamento do Windows |5.0|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Outro software necessário para clientes do Linux

Para o cliente de Linux, o conjunto de ferramentas de disco Data Box instala o software necessário seguinte:

- dislocker
- OpenSSL

## <a name="supported-connection"></a>Ligação suportados

O computador cliente que contém os dados tem de ter um USB 3.0 ou posterior porta. Ligue os discos para este cliente utilizando o cabo fornecido.

## <a name="supported-storage-accounts"></a>Contas de armazenamento suportadas

Aqui está uma lista dos tipos de armazenamento suportadas para o disco do Data Box.

| **Conta de armazenamento** | **Notas** |
| --- | --- |
| Clássica | Standard |
| Fins Gerais  |Standard; ambos o V1 e V2 são suportadas. São suportadas camadas frequentes e esporádica. |
| Conta de armazenamento de BLOBs | |

>[!NOTE]
> Contas de armazenamento Gen 2 do Azure Data Lake não são suportadas.


## <a name="supported-storage-types-for-upload"></a>Tipos de armazenamento suportadas para carregamento

Aqui está uma lista dos tipos de armazenamento suportado para carregado para o Azure com o disco Data Box.

| **Formato de ficheiro** | **Notas** |
| --- | --- |
| Blob de blocos do Azure | |
| BLOBs de páginas do Azure  | |
| Ficheiros do Azure  | |
| Managed Disks | |


## <a name="next-step"></a>Passo seguinte

* [Implementar o seu disco do Azure Data Box](data-box-disk-deploy-ordered.md)

