---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/17/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d73bab76860abf3e9fa442dad44e1ddb66d147a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80519552"
---
O Azure fornece as seguintes funções de RBAC incorporadopara autorizar o acesso a dados de blob e fila utilizando a Azure AD e a OAuth:

- [Armazenamento Blob Data Owner](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Utilize para definir a propriedade e gerir o controlo de acesso POSIX para o Azure Data Lake Storage Gen2. Para mais informações, consulte [o controlo de acesso em Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Contribuinte de dados blob](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Utilize para conceder permissões de leitura/escrita/exclusão aos recursos de armazenamento blob.
- [Armazenamento Blob Data Reader](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Utilize para conceder permissões apenas de leitura aos recursos de armazenamento blob.
- [Contribuinte](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)de dados da fila de armazenamento : Utilize para conceder permissões de leitura/escrita/exclusão às filas Azure.
- [Leitor de dados](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)de fila de armazenamento : Utilize para conceder permissões de leitura apenas para as filas Azure.
- [Processador de mensagens](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)de dados de fila de armazenamento: Utilize para conceder espreite, recuperar e eliminar permissões a mensagens nas filas de armazenamento do Azure.
- [Remetente](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender)de mensagens de dados de fila de armazenamento: Utilize para conceder permissões adicionais a mensagens nas filas de armazenamento do Azure.

Para obter informações detalhadas sobre as funções rBAC incorporadas para o Armazenamento Azure tanto para os serviços de dados como para o serviço de gestão, consulte a secção **de Armazenamento** em papel [integrado azure para o Azure RBAC.](../articles/role-based-access-control/built-in-roles.md#storage) Além disso, para informações sobre os diferentes tipos de funções que fornecem permissões no Azure, consulte funções de administrador de [subscrição Classic, funções Azure RBAC e Funções Azure AD.](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)

> [!NOTE]
> As atribuições de funções RBAC podem demorar até cinco minutos para se propagar.
>
> Apenas as funções explicitamente definidas para o acesso de dados permitem a um diretor de segurança aceder a dados blob ou fila. Funções como **Proprietário,** **Contribuinte**e Contribuinte de Conta de **Armazenamento** permitem a um diretor de segurança gerir uma conta de armazenamento, mas não fornecem acesso aos dados blob ou de fila dentro dessa conta.
>
> O acesso a dados blob ou de fila no portal Azure pode ser autorizado usando a sua conta AD Azure ou a chave de acesso à conta de armazenamento. Para mais informações, consulte [Utilize o portal Azure para aceder a dados de blob ou fila](../articles/storage/common/storage-access-blobs-queues-portal.md).
