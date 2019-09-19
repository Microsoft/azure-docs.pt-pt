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
ms.openlocfilehash: c2b409f0eefe5efa389432cbb007cc08e0c6ae1e
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71078272"
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
> As atribuições de função RBAC podem levar até cinco minutos para serem propagadas.
>
> Somente as funções explicitamente definidas para acesso a dados permitem que uma entidade de segurança acesse dados de BLOB ou fila. Funções como **proprietário**, **colaborador**e colaborador de **conta de armazenamento** permitem que uma entidade de segurança gerencie uma conta de armazenamento, mas não forneça acesso aos dados de BLOB ou de fila nessa conta.
