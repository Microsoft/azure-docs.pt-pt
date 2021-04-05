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
ms.openlocfilehash: f014ce55dc40723faf1b60f908814f9fa0428b8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99215205"
---
A Azure fornece as seguintes funções incorporadas Azure para autorizar o acesso a dados de blob e fila utilizando Azure AD e OAuth:

- [Proprietário dos Dados do Blob de Armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Utilize para definir a propriedade e gerir o controlo de acesso POSIX para o Azure Data Lake Storage Gen2. Para obter mais informações, veja [Controlo de acesso no Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Contribuidor de Dados do Blob de Armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Utilize para conceder permissões de leitura/escrita/eliminação dos recursos do armazenamento de Blobs.
- [Leitor de Dados do Blob de Armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Utilize para conceder permissões só de leitura dos recursos do armazenamento de Blobs.
- [Delegante do Blob de Armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator): Obtenha uma chave de delegação de utilizador para criar uma assinatura de acesso partilhado assinada com as credenciais do Azure Active Directory para um contentor ou blob.
- [Contribuidor de Dados da Fila de Armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Utilize para conceder permissões de leitura/escrita/eliminação das filas do Azure.
- [Leitor de Dados da Fila de Armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Utilize para conceder permissões só de leitura das filas do Azure.
- [Processador de Mensagens de Dados da Fila de Armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Utilize para conceder permissões de pré-visualização, obtenção e eliminação das mensagens nas filas do Armazenamento do Microsoft Azure.
- [Remetente de Mensagens de Dados da Fila de Armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Utilize para conceder permissões de adição de mensagens nas filas do Armazenamento do Microsoft Azure.

Apenas as funções explicitamente definidas para o acesso aos dados permitem a um diretor de segurança aceder a dados blob ou de fila. Funções incorporadas como **Proprietário,** **Contribuinte** e **Contribuinte de Conta de Armazenamento** permitem a um responsável de segurança gerir uma conta de armazenamento, mas não fornecem acesso ao blob ou dados de fila dentro dessa conta através do Azure AD. No entanto, se uma função incluir o **Microsoft.Storage/storageAccounts/listKeys/action**, então um utilizador a quem essa função é atribuída pode aceder aos dados na conta de armazenamento através da autorização da Chave Partilhada com as teclas de acesso à conta. Para obter mais informações, consulte [o portal Azure para aceder aos dados de blob ou fila.](../articles/storage/blobs/authorize-data-operations-portal.md)

Para obter informações detalhadas sobre as funções incorporadas da Azure para o Azure Storage tanto para os serviços de dados como para o serviço de gestão, consulte a secção **de Armazenamento** em funções incorporadas para o [Azure RBAC](../articles/role-based-access-control/built-in-roles.md#storage). Além disso, para obter informações sobre os diferentes tipos de funções que fornecem permissões no Azure, consulte [as funções de administrador de subscrição Clássica, funções Azure e AD AZure](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> As atribuições de funções azure podem demorar até 30 minutos a propagar-se.
