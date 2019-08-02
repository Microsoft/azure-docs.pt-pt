---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4f9a828e5233c88db2106bc648c07578927e0d29
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68514874"
---
O Azure fornece as seguintes funções RBAC internas para autorizar o acesso a dados de BLOB e de fila usando o Azure AD e o OAuth:

- [Proprietário de dados do blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Use para definir a propriedade e gerenciar o controle de acesso POSIX para Azure Data Lake Storage Gen2. Para obter mais informações, consulte [controle de acesso em Azure data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Colaborador de dados do blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Use para conceder permissões de leitura/gravação/exclusão aos recursos de armazenamento de BLOBs.
- [Leitor de dados de blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Use para conceder permissões somente leitura aos recursos de armazenamento de BLOBs.
- [Colaborador de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Use para conceder permissões de leitura/gravação/exclusão às filas do Azure.
- [Leitor de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Use para conceder permissões somente leitura para filas do Azure.
- [Processador de mensagens de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Use para conceder permissões de inspeção, recuperação e exclusão a mensagens em filas do armazenamento do Azure.
- [Remetente da mensagem de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Use para conceder permissões adicionar a mensagens nas filas do armazenamento do Azure.

> [!NOTE]
> Tenha em mente que as atribuições de função do RBAC podem levar até cinco minutos para serem propagadas.
