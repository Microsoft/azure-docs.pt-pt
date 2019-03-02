---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/19/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 9402c4b24c9d64b4b69d750fbd19de40cda396f3
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57218095"
---
O Azure fornece as seguintes funções incorporadas do RBAC para o acesso a dados de armazenamento:

- [Proprietário de dados de Blob de armazenamento (pré-visualização)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview): Utilize para definir a propriedade e gerir o controlo de acesso POSIX para geração 2 de armazenamento do Azure Data Lake (pré-visualização). Para obter mais informações, consulte [controlo de acesso no Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Contribuinte de dados de Blob de armazenamento (pré-visualização)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview): Utilize para conceder permissões de leitura/escrita/eliminar recursos de armazenamento de Blobs.
- [Leitor de dados de Blob de armazenamento (pré-visualização)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview): Utilize para conceder permissões só de leitura para os recursos de armazenamento de Blobs.
- [Contribuinte de dados de fila de armazenamento (pré-visualização)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview): Utilize para conceder permissões de leitura/escrita/eliminar para as filas do Azure.
- [Leitor de dados de fila de armazenamento (pré-visualização)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview): Utilize para conceder permissões só de leitura para as filas do Azure.

Para obter mais informações sobre como as funções incorporadas são definidas para o armazenamento do Azure, consulte [compreender as definições de função](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview). Para obter informações sobre como criar funções RBAC personalizadas, consulte [criar funções personalizadas para controlo de acesso](../articles/role-based-access-control/custom-roles.md). 
