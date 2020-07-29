---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: c0ca96d1829a73f856de021d1286e53007b03219
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87368983"
---
A Azure fornece as seguintes funções incorporadas Azure para autorizar o acesso a dados de blob e fila utilizando Azure AD e OAuth:

- [Proprietário de dados blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Utilize para definir a propriedade e gerir o controlo de acesso POSIX para Azure Data Lake Storage Gen2. Para obter mais informações, consulte [o controlo de acesso no Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Colaborador de dados blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Utilize para conceder permissões de leitura/escrita/eliminação aos recursos de armazenamento blob.
- [Storage Blob Data Reader](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Use para conceder permissões apenas de leitura aos recursos de armazenamento Blob.
- [Armazenamento Blob Delegator](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator): Obtenha uma chave de delegação do utilizador para usar para criar uma assinatura de acesso partilhado assinada com credenciais AD AD Azure para um recipiente ou bolha.
- [Colaborador de dados da fila de](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)armazenamento : Utilize para conceder permissões de leitura/escrita/eliminação às filas do Azure.
- [Leitor de dados de fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Utilize para conceder permissões apenas de leitura às filas do Azure.
- [Processador de mensagens de dados de fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Utilize para dar espreitar, recuperar e eliminar permissões para mensagens nas filas de armazenamento do Azure.
- [Remetente de mensagens de fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Utilize para conceder permissões adicionais a mensagens nas filas de armazenamento do Azure.

Apenas as funções explicitamente definidas para o acesso aos dados permitem a um diretor de segurança aceder a dados blob ou de fila. Funções incorporadas como **Proprietário,** **Contribuinte**e **Contribuinte de Conta de Armazenamento** permitem a um responsável de segurança gerir uma conta de armazenamento, mas não fornecem acesso ao blob ou dados de fila dentro dessa conta através do Azure AD. No entanto, se uma função incluir o **Microsoft.Storage/storageAccounts/listKeys/action**, então um utilizador a quem essa função é atribuída pode aceder aos dados na conta de armazenamento através da autorização da Chave Partilhada com as teclas de acesso à conta. Para obter mais informações, consulte [o portal Azure para aceder aos dados de blob ou fila.](../articles/storage/common/storage-access-blobs-queues-portal.md)

Para obter informações detalhadas sobre as funções incorporadas da Azure para o Azure Storage tanto para os serviços de dados como para o serviço de gestão, consulte a secção **de Armazenamento** em funções incorporadas para o [Azure RBAC](../articles/role-based-access-control/built-in-roles.md#storage). Além disso, para obter informações sobre os diferentes tipos de funções que fornecem permissões em Azure, consulte [as funções de administrador de subscrição clássica, funções de RBAC e funções de AD Azure](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> As atribuições de funções da RBAC podem demorar até cinco minutos a propagar-se.
