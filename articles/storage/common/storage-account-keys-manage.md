---
title: Gerir chaves de acesso à conta
titleSuffix: Azure Storage
description: Aprenda a visualizar, gerir e rodar as chaves de acesso à sua conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2020
ms.author: tamram
ms.openlocfilehash: 50c0980800bbc9b2951bf9107114c1a4d9265558
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454667"
---
# <a name="manage-storage-account-access-keys"></a>Gerir as chaves de acesso à conta de armazenamento

Ao criar uma conta de armazenamento, o Azure gera duas chaves de acesso à conta de armazenamento de 512 bits. Estas chaves podem ser utilizadas para autorizar o acesso aos dados na sua conta de armazenamento através de autorização de Chave Partilhada.

A Microsoft recomenda que utilize o Cofre de Chaves Azure para gerir as suas teclas de acesso e que rode e regenera regularmente as suas teclas. A utilização do Cofre de Chaves Azure facilita a rotação das chaves sem interrupção das suas aplicações. Também pode rodar manualmente as chaves.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-access-keys-and-connection-string"></a>Ver chaves de acesso e fio de ligação

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Utilize o Cofre de Chaves Azure para gerir as suas chaves de acesso

A Microsoft recomenda a utilização do Cofre de Chaves Azure para gerir e rodar as suas teclas de acesso. A sua aplicação pode aceder de forma segura às suas chaves no Cofre de Chaves, para que possa evitar armazená-las com o seu código de aplicação. Para obter mais informações sobre a utilização do Key Vault para gestão de chaves, consulte os seguintes artigos:

- [Gerir as chaves da conta de armazenamento com o Cofre de Chaves Azure e a PowerShell](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Gerir as chaves da conta de armazenamento com o Cofre de Chaves Azure e o Azure CLI](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Manualmente, roda manuais chaves de acesso

A Microsoft recomenda que rode periodicamente as suas chaves de acesso para ajudar a manter a sua conta de armazenamento segura. Se possível, utilize o Cofre de Chaves Azure para gerir as suas chaves de acesso. Se não estiver a utilizar o Cofre de Chaves, terá de rodar manualmente as chaves.

Duas chaves de acesso são atribuídas para que possa rodar as chaves. Ter duas chaves garante que a sua aplicação mantém o acesso ao Armazenamento Azure durante todo o processo.

> [!WARNING]
> Regenerar as chaves de acesso pode afetar quaisquer aplicações ou serviços Azure que estejam dependentes da chave da conta de armazenamento. Todos os clientes que utilizem a chave da conta para aceder à conta de armazenamento devem ser atualizados para utilizar a nova chave, incluindo serviços de mídia, aplicações de nuvem, ambiente de trabalho e mobile, e aplicações gráficas de interface de utilizador para o Armazenamento Azure, como [o Azure Storage Explorer.](https://azure.microsoft.com/features/storage-explorer/)

Siga este processo para rodar as chaves da sua conta de armazenamento:

1. Atualize as cordas de ligação no seu código de aplicação para utilizar a tecla secundária.
2. Volte a gerar a chave de acesso primária para a sua conta do Storage. Na lâmina de **Chaves** de Acesso no portal Azure, clique em **Regenerar Key1**, e depois clique **em Sim** para confirmar que pretende gerar uma nova tecla.
3. Atualize as cadeias de ligação no código para fazer referência à nova chave de acesso primária.
4. Volte a gerar a chave de acesso secundária da mesma forma.

> [!NOTE]
> A Microsoft recomenda a utilização de apenas uma das chaves em todas as suas aplicações ao mesmo tempo. Se utilizar a Chave 1 em alguns locais e a Chave 2 noutros, não poderá rodar as chaves sem que alguma aplicação perca acesso.

Para rodar as chaves de acesso de uma conta, o utilizador deve ser um Administrador de Serviço, ou deve ser atribuído uma função RBAC que inclua a **Microsoft.Storage/storageAccounts/regeneratekey/action**. Algumas funções de RBAC incorporadas que incluem esta ação são as funções **de Proprietário,** **Contribuinte**e Serviço de Serviço chave de conta de **armazenamento.** Para obter mais informações sobre o papel de Administrador de Serviço, consulte funções de administrador de [subscrição Classic, funções Azure RBAC e funções Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md). Para obter informações detalhadas sobre as funções rBAC incorporadas para o Armazenamento Azure, consulte a secção de **armazenamento** em [papel azure incorporado para o Azure RBAC.](../../role-based-access-control/built-in-roles.md#storage)

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da conta de armazenamento azure](storage-account-overview.md)
- [Criar uma conta de armazenamento](storage-account-create.md)
