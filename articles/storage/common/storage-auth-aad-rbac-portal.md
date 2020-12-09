---
title: Utilize o portal Azure para atribuir uma função Azure para o acesso a dados
titleSuffix: Azure Storage
description: Saiba como usar o portal Azure para atribuir permissões a um diretor de segurança do Azure Ative Directory com controlo de acesso baseado em funções Azure (Azure RBAC). O Azure Storage suporta funções personalizadas incorporadas e Azure para autenticação via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 40003db9c3bd2c736f9cedd73b8b7a31a77f625f
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854585"
---
# <a name="use-the-azure-portal-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Utilize o portal Azure para atribuir uma função Azure para acesso a dados de bolhas e filas

O Azure Ative Directory (Azure AD) autoriza os direitos de acesso a recursos seguros através do [controlo de acesso baseado em funções da Azure (Azure RBAC)](../../role-based-access-control/overview.md). O Azure Storage define um conjunto de funções incorporadas do Azure que englobam conjuntos comuns de permissões usadas para aceder a dados de bolhas ou filas.

Quando um papel de Azure é atribuído a um diretor de segurança da AZure, a Azure concede acesso a esses recursos para esse diretor de segurança. O acesso pode ser alargado ao nível da subscrição, do grupo de recursos, da conta de armazenamento ou de um contentor ou fila individual. Um diretor de segurança Azure AD pode ser um utilizador, um grupo, um diretor de serviço de aplicação ou uma [identidade gerida para os recursos da Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

Este artigo descreve como usar o portal Azure para atribuir funções Azure. O portal Azure fornece uma interface simples para atribuir funções Azure e gerir o acesso aos seus recursos de armazenamento. Também pode atribuir funções Azure para recursos blob e fila usando ferramentas de linha de comando Azure ou as APIs de gestão de armazenamento Azure. Para obter mais informações sobre as funções Azure para recursos de armazenamento, consulte [o acesso autenticado a blobs e filas Azure usando o Azure Ative Directory](storage-auth-aad.md).

## <a name="azure-roles-for-blobs-and-queues"></a>Papéis de azul para bolhas e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinar o âmbito de recursos

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-azure-roles-using-the-azure-portal"></a>Atribuir funções Azure usando o portal Azure

Depois de ter determinado o âmbito adequado para uma atribuição de funções, navegue para esse recurso no portal Azure. Mostrar as definições **do Controlo de Acesso (IAM)** para o recurso e seguir estas instruções para gerir as atribuições de funções:

1. Atribua a função azure de armazenamento adequada para conceder acesso a um diretor de segurança Azure AD.

1. Atribua a função de [Leitor](../../role-based-access-control/built-in-roles.md#reader) de Gestor de Recursos Azure aos utilizadores que necessitem de aceder a contentores ou filas através do portal Azure utilizando as suas credenciais Azure AD.

As seguintes secções descrevem cada um destes passos com mais detalhes.

> [!NOTE]
> Quando cria uma conta de Armazenamento Azure, não lhe são atribuídas automaticamente permissões de acesso aos dados através do Azure AD. Deve atribuir-se explicitamente um papel Azure para o Azure Storage. Pode atribuí-lo ao nível da sua subscrição, grupo de recursos, conta de armazenamento ou contentor ou fila.
>
> Antes de atribuir uma função para o acesso aos dados, poderá aceder aos dados na sua conta de armazenamento através do portal Azure, uma vez que o portal Azure também pode utilizar a chave de conta para o acesso aos dados. Para mais informações, consulte [Escolha como autorizar o acesso aos dados blob no portal Azure.](../blobs/authorize-data-operations-portal.md)

### <a name="assign-an-azure-built-in-role"></a>Atribuir um papel embutido Azure

Antes de atribuir um papel a um diretor de segurança, não se esqueça de considerar o âmbito das permissões que está a conceder. Reveja a secção [de âmbito de recursos determine](#determine-resource-scope) para decidir o âmbito adequado.

O procedimento aqui indicado atribui uma função a um contentor, mas pode seguir os mesmos passos para atribuir uma função a uma fila:

1. No [portal Azure,](https://portal.azure.com)aceda à sua conta de armazenamento e apresente a **Visão Geral** da conta.
1. Em Serviços, selecione **Blobs**.
1. Localize o recipiente para o qual pretende atribuir uma função e apresente as definições do recipiente.
1. Selecione **o controlo de acesso (IAM)** para exibir as definições de controlo de acesso para o recipiente. Selecione o **separador funções** para ver a lista de atribuições de funções.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/portal-access-control-container.png" alt-text="Screenshot mostrando definições de controlo de acesso ao contentor":::

1. Clique no botão **de atribuição de funções Adicionar** para adicionar um novo papel.
1. Na janela **de atribuição de funções Adicionar,** selecione a função de Armazenamento Azure que pretende atribuir. Em seguida, procure para localizar o principal de segurança ao qual quer atribuir esse papel.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/add-rbac-role.png" alt-text="Screenshot mostrando como atribuir um papel de Azure":::

1. Clique em **Guardar**. A identidade a quem atribuiu o papel aparece listada nessa função. Por exemplo, a seguinte imagem mostra que o utilizador adicionado agora leu permissões aos dados no contentor denominado *amostra-contentor*.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/container-scoped-role.png" alt-text="Screenshot mostrando lista de utilizadores atribuídos a uma função":::

Pode seguir passos semelhantes para atribuir uma função a uma conta de armazenamento, grupo de recursos ou subscrição.

### <a name="assign-the-reader-role-for-portal-access"></a>Atribuir a função Reader para acesso ao portal

Quando atribui um papel incorporado ou personalizado para o Azure Storage a um responsável de segurança, está a conceder permissões a esse chefe de segurança para realizar operações em dados na sua conta de armazenamento. As funções de Leitor de **Dados** incorporados fornecem permissões de leitura para os dados num recipiente ou fila, enquanto as funções de Contribuinte de **Dados** incorporado fornecem permissões de leitura, escrita e eliminação para um recipiente ou fila. As permissões são procuradas no recurso especificado.  
Por exemplo, se atribuir a função **de Contribuinte de Dados blob de armazenamento** ao utilizador Mary ao nível de um recipiente denominado recipiente de **amostras,** então a Mary tem direito a ler, escrever e apagar o acesso a todas as bolhas desse recipiente.

No entanto, se Mary quiser ver uma bolha no portal Azure, então o papel **de Colaborador de Dados de Armazenamento blob** por si só não fornecerá permissões suficientes para navegar através do portal para a bolha para vê-lo. As permissões AD adicionais do AZure são necessárias para navegar através do portal e ver os outros recursos que são visíveis lá.

Se os seus utilizadores precisarem de ter acesso a blobs no portal Azure, atribua-lhes uma função adicional de Azure, a função [Reader,](../../role-based-access-control/built-in-roles.md#reader) a esses utilizadores, ao nível da conta de armazenamento ou acima. A função **Reader** é uma função de Gestor de Recursos Azure que permite aos utilizadores visualizar os recursos da conta de armazenamento, mas não modificá-los. Não fornece permissões de leitura aos dados no Azure Storage, mas apenas aos recursos de gestão de conta.

Siga estes passos para atribuir a função **Reader** para que um utilizador possa aceder a bolhas a partir do portal Azure. Neste exemplo, a atribuição é traçada na conta de armazenamento:

1. No [portal do Azure](https://portal.azure.com), navegue para a sua conta de armazenamento.
1. Selecione **o controlo de acesso (IAM)** para exibir as definições de controlo de acesso para a conta de armazenamento. Selecione o **separador funções** para ver a lista de atribuições de funções.
1. Na janela **de atribuição de funções Adicionar,** selecione a função **Reader.** 
1. A partir do **acesso do Access a** Campo, selecione **utilizador, grupo ou principal de serviço Azure.**
1. Procure localizar o principal de segurança ao qual pretende atribuir o papel.
1. Guarde a tarefa de função.

A atribuição da função **Reader** é necessária apenas para os utilizadores que necessitem de aceder a bolhas ou filas utilizando o portal Azure.

> [!IMPORTANT]
> A versão de pré-visualização do Storage Explorer no portal Azure não suporta a utilização de credenciais AZure AD para visualizar e modificar dados de blob ou fila. O Explorador de Armazenamento no portal Azure utiliza sempre as chaves da conta para aceder aos dados. Para utilizar o Storage Explorer no portal Azure, tem de ser atribuída uma função que inclua **Microsoft.Storage/storageAccounts/listkeys/action**.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre as funções Azure para recursos de armazenamento, consulte [o acesso autenticado a blobs e filas Azure usando o Azure Ative Directory](storage-auth-aad.md). 
- Para saber mais sobre o Azure RBAC, veja [o que é o controlo de acesso baseado em funções do Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
- Para aprender a atribuir e gerir atribuições de funções da Azure PowerShell, Azure CLI ou da REST API, consulte estes artigos:
    - [Adicione ou remova atribuições de funções Azure utilizando o módulo Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Adicione ou remova atribuições de funções Azure usando o CLI Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Utilizar a API REST para adicionar ou remover atribuições de funções do Azure](../../role-based-access-control/role-assignments-rest.md)
- Para saber como autorizar o acesso a contentores e filas a partir das suas aplicações de armazenamento, consulte [Use Azure AD com aplicações de Armazenamento Azure](storage-auth-aad-app.md).
