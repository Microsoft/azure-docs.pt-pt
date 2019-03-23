---
title: Utilizar o portal do Azure para gerir os direitos de acesso do Azure AD para contentores e filas com RBAC - armazenamento do Azure | Documentos da Microsoft
description: Utilize o controlo de acesso baseado em funções (RBAC) do portal do Azure para atribuir acesso a contentores e filas para entidades de segurança. O armazenamento do Azure suporta funções do RBAC incorporadas e personalizadas para a autenticação através do Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 66e6cff40d71842ef19f99d7c96219af83fc9b4e
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368243"
---
# <a name="grant-access-to-azure-containers-and-queues-with-rbac-in-the-azure-portal"></a>Conceder acesso a contentores do Azure e filas com RBAC no portal do Azure

Azure Active Directory (Azure AD) autoriza direitos de acesso a recursos protegidos por meio [controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/overview.md). O armazenamento do Azure define um conjunto de funções RBAC incorporadas que abrangem conjuntos comuns de permissões utilizados para aceder aos contentores ou filas. 

Quando uma função RBAC é atribuída a um principal de segurança do Azure AD, o Azure concede acesso a esses recursos para aquela entidade de segurança. Acesso pode ser confinado ao nível da subscrição, o grupo de recursos, a conta de armazenamento, ou um contentor individual ou fila. Um principal de segurança do Azure AD pode ser um utilizador, um grupo, um principal de serviço de aplicações, ou uma [identidade de recursos do Azure gerida](../../active-directory/managed-identities-azure-resources/overview.md).

Este artigo descreve como utilizar o portal do Azure para atribuir funções RBAC. O portal do Azure fornece uma interface simple para atribuir funções de RBAC e gerir o acesso aos seus recursos de armazenamento. Também pode atribuir funções RBAC para recursos de BLOBs e filas com ferramentas de linha de comandos do Azure ou as APIs de gestão de armazenamento do Azure. Para obter mais informações sobre as funções do RBAC para recursos de armazenamento, consulte [autenticar o acesso ao Azure os blobs e filas com o Azure Active Directory](storage-auth-aad.md). 

## <a name="rbac-roles-for-blobs-and-queues"></a>Funções do RBAC para blobs e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinar o escopo do recurso 

Antes de atribuir uma função RBAC para uma entidade de segurança, determine o âmbito de acesso que a entidade de segurança deve ter. As práticas recomendadas ditam que sempre é melhor conceder apenas o âmbito mais estreita possível.

A lista seguinte descreve os níveis em que pode definir o âmbito acesso aos recursos de BLOBs e filas do Azure, começando com o âmbito mais estreita:

- **Um contentor individual.** Este âmbito, uma entidade de segurança tem acesso a todos os blobs no contentor, bem como as propriedades do contentor e metadados.
- **Uma fila individual.** Este âmbito, uma entidade de segurança tem acesso a mensagens em fila, bem como propriedades de fila e metadados.
- **A conta de armazenamento.** Este âmbito, uma entidade de segurança tem acesso a todos os contentores e respetivos blobs ou para todas as filas e suas mensagens.
- **O grupo de recursos.** Este âmbito, uma entidade de segurança tem acesso a todos os contentores ou filas em todas as contas de armazenamento no grupo de recursos.
- **A subscrição.** Este âmbito, uma entidade de segurança tem acesso a todos os contentores ou filas em todas as contas de armazenamento em todos os grupos de recursos na subscrição.

Ao determinar o âmbito pretendido para uma atribuição de função, navegue para o recurso apropriado no portal do Azure. Apresentar o **controlo de acesso (IAM)** definições para o recurso e siga as instruções nas secções subsequentes para gerir atribuições de funções.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Atribua funções RBAC com o portal do Azure

Conceder acesso aos recursos de BLOBs e filas com credenciais do Azure AD no portal do Azure envolve os seguintes passos: 

1. Atribua a função RBAC de armazenamento do Azure adequada para conceder acesso a contentores ou filas. Acesso de leitura, atribuir os **leitor de dados de BLOBs** ou **leitor de dados de fila** função. Para acesso de leitura, escrita e eliminar, atribuir os **contribuinte de dados de BLOBs** ou **contribuinte de dados de fila** função. Também pode atribuir uma função personalizada.

1. Atribuir o Azure Resource Manager [leitor](../../role-based-access-control/built-in-roles.md#reader) função aos utilizadores que precisam de aceder os contentores ou filas através do portal do Azure com as credenciais do Azure AD. 

As secções seguintes descrevem cada uma dessas etapas mais detalhadamente.

### <a name="assign-a-built-in-rbac-role"></a>Atribuir uma função RBAC incorporada

Antes de atribuir uma função a uma entidade de segurança, certifique-se de que considere o âmbito das permissões do que se está a conceder a. Reveja os [determinar o escopo do recurso](#determine-resource-scope) secção para decidir o âmbito adequado.

O procedimento apresentado aqui atribui uma função no âmbito de um contentor, mas pode seguir os mesmos passos para atribuir uma função no âmbito de uma fila: 

1. Na [portal do Azure](https://portal.azure.com), navegue para a sua conta de armazenamento e exibir o **descrição geral** para a conta.
1. Em serviços, selecione **Blobs**. 
1. Localizar o contentor para o qual pretende atribuir uma função e apresentar as definições do contentor. 
1. Selecione **controlo de acesso (IAM)** para apresentar as definições de controlo de acesso para o contentor. Selecione o **atribuições de funções** separador para ver a lista de atribuições de funções.

    ![Captura de ecrã que mostra as definições de controlo de acesso do contentor](media/storage-auth-aad-rbac/portal-access-control-container.png)

1. Clique nas **adicionar atribuição de função** botão para adicionar uma nova função.
1. Na **adicionar atribuição de função** janela, selecione a função de armazenamento do Azure que pretende atribuir. Procure para localizar a entidade de segurança para o qual pretende atribuir essa função.

    ![Captura de ecrã que mostra como atribuir uma função RBAC](media/storage-auth-aad-rbac/add-rbac-role.png)

1. Clique em **Guardar**. A identidade a quem é atribuída a função aparece listada sob essa função. Por exemplo, a imagem seguinte mostra que o utilizador adicionado agora tem permissões de leitura aos dados no contentor com o nome *exemplo-container*.

    ![Captura de ecrã a mostrar lista de utilizadores atribuídos a uma função](media/storage-auth-aad-rbac/container-scoped-role.png)

Pode seguir passos semelhantes para atribuir uma função de âmbito para a conta de armazenamento, grupo de recursos ou subscrição.

> [!NOTE]
> Como proprietário da conta de armazenamento do Azure, não é atribuídos automaticamente permissões para aceder aos dados. Tem explicitamente de atribuir-se uma função RBAC do armazenamento do Azure. Pode atribuí-la no nível da sua subscrição, grupo de recursos, conta de armazenamento, ou um contentor ou fila.
> 
> Não é possível atribuir uma função no âmbito de um contentor ou uma fila se a sua conta de armazenamento tem um espaço de nomes hierárquico ativado.

### <a name="assign-the-reader-role-for-portal-access"></a>Atribuir a função de leitor para acesso ao portal

Quando atribui uma função interna ou personalizada do armazenamento do Azure para uma entidade de segurança, está a conceder permissões para essa entidade de segurança para executar operações nos dados na sua conta de armazenamento. O incorporado **leitor de dados** as funções fornecem as permissões de leitura para os dados de um contentor ou uma fila, enquanto o incorporado **contribuinte do Data** as funções fornecem ler, escrever e eliminar permissões para um contentor ou fila. As permissões estão no âmbito para o recurso especificado.  

Por exemplo, se atribuir o **contribuinte de dados de Blob de armazenamento** função Mary no nível de um contentor com o nome de utilizador **exemplo-container**, em seguida, Mary concedido é de leitura, escrita e eliminação acesso a todos os blobs no esse contêiner.

No entanto, se desejar ver um blob no portal do Azure, Mary, em seguida, o **contribuinte de dados de Blob de armazenamento** função por si só não irá fornecer permissões suficientes para navegar através do portal para o blob para vê-la. Mais permissões do Azure AD são necessárias para navegar através do portal e ver os outros recursos que estão visíveis.

Se os utilizadores precisam para poder aceder a blobs no portal do Azure, em seguida, atribuir-lhes uma função RBAC adicional, o [leitor](../../role-based-access-control/built-in-roles.md#reader) função, para esses usuários, no nível da conta de armazenamento ou superior. O **leitor** função é uma função do Azure Resource Manager que permite aos utilizadores ver recursos da conta de armazenamento, mas não modificá-los. Ele não fornece as permissões de leitura aos dados no armazenamento do Azure, mas apenas a recursos da conta de gestão.

Siga estes passos para atribuir a **leitor** função para que um utilizador pode aceder a blobs do portal do Azure. Neste exemplo, a atribuição tem um âmbito para a conta de armazenamento:

1. Na [portal do Azure](https://portal.azure.com), navegue até à sua conta de armazenamento.
1. Selecione **controlo de acesso (IAM)** para apresentar as definições de controlo de acesso para a conta de armazenamento. Selecione o **atribuições de funções** separador para ver a lista de atribuições de funções.
1. Na **adicionar atribuição de função** janela, selecione a **leitor** função. 
1. Partir do **atribuir acesso a** campo, selecione **utilizador, grupo ou principal de serviço do Azure AD**.
1. Procure para localizar a entidade de segurança para o qual pretende atribuir a função.
1. Guarde a atribuição de função.

> [!NOTE]
> A atribuição de função do leitor é necessária apenas para os utilizadores que precisam de aceder a blobs ou filas com o portal do Azure. 

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre o RBAC, veja [o que é o controlo de acesso baseado em funções (RBAC)?](../../role-based-access-control/overview.md).
- Para saber como atribuir e gerir atribuições de funções RBAC com o Azure PowerShell, CLI do Azure ou da API REST, veja estes artigos:
    - [Gerir o controlo de acesso baseado em funções (RBAC) com o Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Gerir o controlo de acesso baseado em funções (RBAC) com a CLI do Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Gerir o controlo de acesso baseado em funções (RBAC) com a API REST](../../role-based-access-control/role-assignments-rest.md)
- Para saber como autorizar o acesso a contentores e filas de dentro dos seus aplicativos de armazenamento, veja [utilize o Azure AD com aplicações de armazenamento do Azure](storage-auth-aad-app.md).
