---
title: Use o portal do Azure para gerenciar os direitos de acesso do Azure AD aos dados de BLOB e de fila com o armazenamento do Azure RBAC | Microsoft Docs
description: Use o RBAC (controle de acesso baseado em função) do portal do Azure para atribuir acesso a contêineres e filas a entidades de segurança. O armazenamento do Azure dá suporte a funções RBAC personalizadas e internas para autenticação por meio do Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ad88066ebf19bdcc9bcdb77309ce76828c09ce47
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671132"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-in-the-azure-portal"></a>Conceder acesso a dados de BLOB e de fila do Azure com RBAC no portal do Azure

O Azure Active Directory (AD do Azure) autoriza os direitos de acesso a recursos protegidos por meio [do RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md). O armazenamento do Azure define um conjunto de funções RBAC internas que abrangem conjuntos comuns de permissões usadas para acessar dados de BLOB ou de fila. 

Quando uma função RBAC é atribuída a uma entidade de segurança do Azure AD, o Azure concede acesso a esses recursos para essa entidade de segurança. O acesso pode ser definido para o nível da assinatura, o grupo de recursos, a conta de armazenamento ou um contêiner ou fila individual. Uma entidade de segurança do Azure AD pode ser um usuário, um grupo, uma entidade de serviço de aplicativo ou uma [identidade gerenciada para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Este artigo descreve como usar o portal do Azure para atribuir funções de RBAC. O portal do Azure fornece uma interface simples para atribuir funções RBAC e gerenciar o acesso aos seus recursos de armazenamento. Você também pode atribuir funções RBAC para recursos de BLOB e fila usando as ferramentas de linha de comando do Azure ou as APIs de gerenciamento de armazenamento do Azure. Para obter mais informações sobre as funções RBAC para recursos de armazenamento, consulte [autenticar o acesso a BLOBs e filas do Azure usando o Azure Active Directory](storage-auth-aad.md). 

## <a name="rbac-roles-for-blobs-and-queues"></a>Funções de RBAC para BLOBs e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinar o escopo do recurso 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Atribuir funções de RBAC usando o portal do Azure

Depois de determinar o escopo apropriado para uma atribuição de função, navegue até esse recurso na portal do Azure. Exiba as configurações de **controle de acesso (iam)** para o recurso e siga estas instruções para gerenciar as atribuições de função:

1. Atribua a função apropriada do RBAC do armazenamento do Azure para conceder acesso a uma entidade de segurança do Azure AD.

1. Atribua a função [leitor](../../role-based-access-control/built-in-roles.md#reader) de Azure Resource Manager a usuários que precisam acessar contêineres ou filas por meio do portal do Azure usando suas credenciais do Azure AD. 

As seções a seguir descrevem cada uma dessas etapas mais detalhadamente.

> [!NOTE]
> Como proprietário da conta de armazenamento do Azure, não é atribuídos automaticamente permissões para aceder aos dados. Tem explicitamente de atribuir-se uma função RBAC do armazenamento do Azure. Você pode atribuí-lo ao nível de sua assinatura, grupo de recursos, conta de armazenamento ou um contêiner ou fila.
> 
> Você não poderá atribuir uma função com escopo a um contêiner ou fila se sua conta de armazenamento tiver um namespace hierárquico habilitado.

### <a name="assign-a-built-in-rbac-role"></a>Atribuir uma função RBAC interna

Antes de atribuir uma função a uma entidade de segurança, certifique-se de considerar o escopo das permissões que você está concedendo. Examine a seção [determinar escopo do recurso](#determine-resource-scope) para decidir o escopo apropriado.

O procedimento mostrado aqui atribui uma função com escopo a um contêiner, mas você pode seguir as mesmas etapas para atribuir uma função com escopo a uma fila: 

1. Na [portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento e exiba a **visão geral** da conta.
1. Em serviços, selecione **BLOBs**. 
1. Localize o contêiner para o qual você deseja atribuir uma função e exiba as configurações do contêiner. 
1. Selecione **controle de acesso (iam)** para exibir as configurações de controle de acesso para o contêiner. Selecione a guia **atribuições de função** para ver a lista de atribuições de função.

    ![Captura de tela mostrando as configurações de controle de acesso do contêiner](media/storage-auth-aad-rbac-portal/portal-access-control-container.png)

1. Clique no botão **Adicionar atribuição de função** para adicionar uma nova função.
1. Na janela **Adicionar atribuição de função** , selecione a função de armazenamento do Azure que você deseja atribuir. Em seguida, pesquise para localizar a entidade de segurança para a qual você deseja atribuir essa função.

    ![Captura de tela mostrando como atribuir uma função de RBAC](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. Clique em **Guardar**. A identidade para a qual você atribuiu a função aparece listada sob essa função. Por exemplo, a imagem a seguir mostra que o usuário adicionado agora tem permissões de leitura para dados no contêiner denominado *Sample-container*.

    ![Captura de tela mostrando a lista de usuários atribuídos a uma função](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

Você pode seguir etapas semelhantes para atribuir uma função com escopo à conta de armazenamento, ao grupo de recursos ou à assinatura.

### <a name="assign-the-reader-role-for-portal-access"></a>Atribuir a função leitor para acesso ao portal

Ao atribuir uma função interna ou personalizada para o armazenamento do Azure para uma entidade de segurança, você está concedendo permissões a essa entidade de segurança para executar operações nos dados em sua conta de armazenamento. As funções internas do **leitor de dados** fornecem permissões de leitura para os dados em um contêiner ou fila, enquanto as funções de **colaborador de dados** internas fornecem permissões de leitura, gravação e exclusão a um contêiner ou fila. As permissões estão no escopo do recurso especificado.  

Por exemplo, se você atribuir a função de **colaborador de dados de blob de armazenamento** ao usuário Mary no nível de um contêiner denominado **Sample-container**, Mary receberá acesso de leitura, gravação e exclusão a todos os BLOBs nesse contêiner.

No entanto, se Mary quiser exibir um blob no portal do Azure, a função de **colaborador de dados do blob de armazenamento** não fornecerá permissões suficientes para navegar pelo portal para o blob a fim de exibi-lo. Permissões adicionais do Azure AD são necessárias para navegar pelo portal e exibir os outros recursos visíveis lá.

Se os usuários precisarem ser capazes de acessar BLOBs no portal do Azure, atribua a eles uma função RBAC adicional, a função [leitor](../../role-based-access-control/built-in-roles.md#reader) , a esses usuários, no nível da conta de armazenamento ou acima. A função **leitor** é uma função Azure Resource Manager que permite aos usuários exibir os recursos da conta de armazenamento, mas não modificá-los. Ele não fornece permissões de leitura para dados no armazenamento do Azure, mas apenas para recursos de gerenciamento de conta.

Siga estas etapas para atribuir a função **leitor** para que um usuário possa acessar BLOBs da portal do Azure. Neste exemplo, a atribuição tem como escopo a conta de armazenamento:

1. Na [portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento.
1. Selecione **controle de acesso (iam)** para exibir as configurações de controle de acesso para a conta de armazenamento. Selecione a guia **atribuições de função** para ver a lista de atribuições de função.
1. Na janela **Adicionar atribuição de função** , selecione a função **leitor** . 
1. No campo **atribuir acesso a** , selecione **usuário, grupo ou entidade de serviço do Azure ad**.
1. Pesquise para localizar a entidade de segurança à qual você deseja atribuir a função.
1. Salve a atribuição de função.

> [!NOTE]
> A atribuição da função leitor é necessária apenas para usuários que precisam acessar BLOBs ou filas usando o portal do Azure. 

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre as funções RBAC para recursos de armazenamento, consulte [autenticar o acesso a BLOBs e filas do Azure usando o Azure Active Directory](storage-auth-aad.md). 
- Para saber mais sobre o RBAC, consulte [o que é o RBAC (controle de acesso baseado em função)?](../../role-based-access-control/overview.md).
- Para saber como atribuir e gerenciar atribuições de função RBAC com Azure PowerShell, CLI do Azure ou a API REST, consulte estes artigos:
    - [Gerenciar RBAC (controle de acesso baseado em função) com Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Gerenciar RBAC (controle de acesso baseado em função) com CLI do Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Gerenciar o RBAC (controle de acesso baseado em função) com a API REST](../../role-based-access-control/role-assignments-rest.md)
- Para saber como autorizar o acesso a contêineres e filas de dentro de seus aplicativos de armazenamento, consulte [usar o Azure AD com aplicativos de armazenamento do Azure](storage-auth-aad-app.md).
