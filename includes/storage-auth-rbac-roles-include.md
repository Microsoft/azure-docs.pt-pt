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
ms.openlocfilehash: a4a2034d44d98de9696b435c68c97a8a54a1c351
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026884"
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

Apenas as funções explicitamente definidas para o acesso aos dados permitem a um diretor de segurança aceder a dados blob ou de fila. Funções incorporadas como **Proprietário,** **Contribuinte** e **Contribuinte de Conta de Armazenamento** permitem a um responsável de segurança gerir uma conta de armazenamento, mas não fornecem acesso ao blob ou dados de fila dentro dessa conta através do Azure AD. No entanto, se uma função incluir o **Microsoft.Storage/storageAccounts/listKeys/action**, então um utilizador a quem essa função é atribuída pode aceder aos dados na conta de armazenamento através da autorização da Chave Partilhada com as teclas de acesso à conta. Para obter mais informações, consulte [o portal Azure para aceder aos dados de blob ou fila.](../articles/storage/blobs/authorize-data-operations-portal.md)

Para obter informações detalhadas sobre as funções incorporadas da Azure para o Azure Storage tanto para os serviços de dados como para o serviço de gestão, consulte a secção **de Armazenamento** em funções incorporadas para o [Azure RBAC](../articles/role-based-access-control/built-in-roles.md#storage). Além disso, para obter informações sobre os diferentes tipos de funções que fornecem permissões no Azure, consulte [as funções de administrador de subscrição Clássica, funções Azure e AD AZure](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> As atribuições de funções azure podem demorar até cinco minutos a propagar-se.