---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 026717dff2b6883eb643497dec91226e4afe8133
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60150222"
---
O Azure oferece as seguintes funções incorporadas do RBAC para autorizar o acesso a dados de BLOBs e filas com o Azure AD e OAuth:

- [Proprietário de dados de Blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Utilize para definir a propriedade e gerir o controlo de acesso POSIX para geração 2 de armazenamento do Azure Data Lake (pré-visualização). Para obter mais informações, consulte [controlo de acesso no Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Contribuinte de dados de Blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Utilize para conceder permissões de leitura/escrita/eliminar recursos de armazenamento de Blobs.
- [Leitor de dados de Blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Utilize para conceder permissões só de leitura para os recursos de armazenamento de Blobs.
- [Contribuinte de dados de fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Utilize para conceder permissões de leitura/escrita/eliminar para as filas do Azure.
- [Leitor de dados de fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Utilize para conceder permissões só de leitura para as filas do Azure.
- [Processador de mensagens de dados de fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Utilizar a pré-visualização de concessão, obter e permissões de eliminação de mensagens em filas de armazenamento do Azure.
- [Remetente da mensagem de dados de fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Utilize para conceder adicionar permissões para as mensagens em filas de armazenamento do Azure.

> [!NOTE]
> Tenha em atenção que as atribuições de funções do RBAC podem demorar até cinco minutos para propagar.
