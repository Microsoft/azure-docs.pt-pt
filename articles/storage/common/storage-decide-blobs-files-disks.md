---
title: Decidir quando usar Blobs Azure, Ficheiros Azure ou Discos Azure
description: Saiba mais sobre as diferentes formas de armazenar e aceder a dados no Azure para ajudá-lo a decidir que tecnologia usar.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4b1a42e25a6d8c7b4a3c24dffcb858ffe63dd10b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71671036"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Decidir quando usar Blobs Azure, Ficheiros Azure ou Discos Azure

O Microsoft Azure fornece várias funcionalidades no Armazenamento Azure para armazenar e aceder aos seus dados na nuvem. Este artigo abrange Ficheiros Azure, Blobs e Discos, e foi concebido para ajudá-lo a escolher entre estas funcionalidades.

## <a name="scenarios"></a>Cenários

A tabela seguinte compara Ficheiros, Blobs e Discos, e mostra cenários de exemplo adequados para cada um.

| Funcionalidade | Descrição | Quando utilizar |
|--------------|-------------|-------------|
| **Ficheiros do Azure** | Fornece uma interface SMB, bibliotecas de clientes e uma [interface REST](/rest/api/storageservices/file-service-rest-api) que permite o acesso de qualquer lugar a ficheiros armazenados. | Pretende "levantar e deslocar" uma aplicação para a nuvem que já utiliza o sistema de ficheiros nativo APIs para partilhar dados entre ele e outras aplicações em funcionamento no Azure.<br/><br/>Você quer armazenar ferramentas de desenvolvimento e depuração que precisam ser acedidas a partir de muitas máquinas virtuais. |
| **Blobs do Azure** | Fornece bibliotecas de clientes e uma [interface REST](/rest/api/storageservices/blob-service-rest-api) que permite armazenar e aceder a dados não estruturados em larga escala em blocos de bolhas.<br/><br/>Também suporta [o Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) para soluções de análise de big data da empresa. | Deseja que a sua aplicação suporte o streaming e os cenários de acesso aleatório.<br/><br/>Pretende aceder aos dados da aplicação a partir de qualquer lugar.<br/><br/>Você quer construir um lago de dados da empresa em Azure e realizar análise de big data. |
| **Discos do Azure** | Fornece bibliotecas de clientes e uma [interface REST](/rest/api/compute/manageddisks/disks/disks-rest-api) que permite que os dados sejam armazenados e acedidos persistentemente a partir de um disco rígido virtual anexo. | Pretende levantar e deslocar aplicações que utilizam APIs do sistema de ficheiros nativopara ler e escrever dados para discos persistentes.<br/><br/>Pretende armazenar dados que não são necessários para serem acedidos de fora da máquina virtual a que o disco está ligado. |


## <a name="next-steps"></a>Passos seguintes

Ao tomar decisões sobre como os seus dados são armazenados e acedidos, deve também considerar os custos envolvidos. Para mais informações, consulte o [Preço de Armazenamento Azure](https://azure.microsoft.com/pricing/details/storage/).
  
Algumas funcionalidades SMB não são aplicáveis à nuvem. Para mais informações, consulte [Funcionalidades não suportadas pelo serviço De Ficheiros Azure](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
 
Para mais informações sobre o Azure Blobs, consulte o nosso artigo, O que é o [armazenamento de Azure Blob?](../blobs/storage-blobs-overview.md)

Para mais informações sobre o Armazenamento de Discos, consulte a nossa [Introdução para discos geridos](../../virtual-machines/windows/managed-disks-overview.md).

Para mais informações sobre o Azure Files, consulte o nosso artigo, [Planning for a Azure Files implementação](../files/storage-files-planning.md).