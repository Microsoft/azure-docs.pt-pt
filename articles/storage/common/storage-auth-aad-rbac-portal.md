---
title: Utilize o portal Azure para atribuir uma função RBAC para acesso a dados
titleSuffix: Azure Storage
description: Aprenda a usar o portal Azure para atribuir permissões a um diretor de segurança do Azure Ative Directory com controlo de acesso baseado em funções (RBAC). O Azure Storage suporta funções RBAC incorporadas e personalizadas para autenticação via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ec32990513d9199c4aaccf1bcfcbf76f348f877b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75867506"
---
# <a name="use-the-azure-portal-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Utilize o portal Azure para atribuir uma função RBAC para acesso a dados de blob e fila

O Azure Ative Directory (Azure AD) autoriza os direitos de acesso a recursos garantidos através do [controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/overview.md). O Azure Storage define um conjunto de funções RBAC incorporadas que englobam conjuntos comuns de permissões usadas para aceder a dados blob ou fila.

Quando uma função RBAC é atribuída a um diretor de segurança da AD Azure, o Azure concede acesso a esses recursos para esse diretor de segurança. O acesso pode ser consultado ao nível da subscrição, do grupo de recursos, da conta de armazenamento ou de um recipiente ou fila individual. Um diretor de segurança da AD Azure pode ser um utilizador, um grupo, um diretor de serviço de aplicação ou uma [identidade gerida para os recursos Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

Este artigo descreve como usar o portal Azure para atribuir funções RBAC. O portal Azure fornece uma interface simples para atribuir funções RBAC e gerir o acesso aos seus recursos de armazenamento. Também pode atribuir funções RBAC para recursos de blob e fila utilizando ferramentas de linha de comando Azure ou as APIs de gestão de armazenamento Azure. Para obter mais informações sobre as funções RBAC para recursos de armazenamento, consulte [O acesso authenticado a blobs e filas Azure utilizando o Diretório Ativo Azure.](storage-auth-aad.md) 

## <a name="rbac-roles-for-blobs-and-queues"></a>Funções RBAC para bolhas e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinar o âmbito dos recursos 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Atribuir funções RBAC utilizando o portal Azure

Depois de ter determinado a margem adequada para uma atribuição de funções, navegue para esse recurso no portal Azure. Mostrar as definições de Controlo de **Acesso (IAM)** para o recurso e seguir estas instruções para gerir as atribuições de funções:

1. Atribuir a função de RBAC de armazenamento azure apropriada para dar acesso a um diretor de segurança da AD Azure.

1. Atribuir a função de [Leitor](../../role-based-access-control/built-in-roles.md#reader) de Recursos Azure aos utilizadores que necessitem de aceder a contentores ou filas através do portal Azure utilizando as suas credenciais De AD Azure. 

As seguintes secções descrevem cada um destes passos com mais detalhes.

> [!NOTE]
> Como proprietário da sua conta De armazenamento Azure, não lhe são atribuídas automaticamente permissões de acesso a dados. Deve atribuir-se explicitamente uma função RBAC para o Armazenamento Azure. Pode atribuí-lo ao nível da sua subscrição, grupo de recursos, conta de armazenamento ou um recipiente ou fila.
>
> Não é possível atribuir uma função a um recipiente ou fila se a sua conta de armazenamento tiver um espaço de nome hierárquico ativado.

### <a name="assign-a-built-in-rbac-role"></a>Atribuir uma função RBAC incorporada

Antes de atribuir um papel a um diretor de segurança, não se esqueça de considerar o âmbito das permissões que está a conceder. Reveja a secção de âmbito de [recurso Determine](#determine-resource-scope) para decidir o âmbito adequado.

O procedimento aqui mostrado atribui uma função a um recipiente, mas pode seguir os mesmos passos para atribuir uma função a uma fila: 

1. No [portal Azure,](https://portal.azure.com)vá à sua conta de armazenamento e exiba a **visão geral** da conta.
1. Em Serviços, selecione **Blobs**. 
1. Localize o recipiente para o qual pretende atribuir uma função e exiba as definições do recipiente. 
1. Selecione **o controlo de acesso (IAM)** para visualizar as definições de controlo de acesso para o recipiente. Selecione o separador de **atribuições de funções** para ver a lista de atribuições de papéis.

    ![Screenshot mostrando as definições de controlo de acesso do recipiente](media/storage-auth-aad-rbac-portal/portal-access-control-for-storage.png)

1. Clique no botão de atribuição de **funções Adicionar** para adicionar um novo papel.
1. Na janela de atribuição de **funções Add,** selecione a função de Armazenamento Azure que pretende atribuir. Em seguida, procure localizar o diretor de segurança a que pretende atribuir esse papel.

    ![Screenshot mostrando como atribuir uma função RBAC](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. Clique em **Guardar**. A identidade a quem atribuiu o papel aparece listada nesse papel. Por exemplo, a imagem que se segue mostra que o utilizador adicionado agora tem permissões de leitura de dados no recipiente denominado *recipiente de amostras*.

    ![Screenshot mostrando lista de utilizadores atribuídos a um papel](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

Pode seguir passos semelhantes para atribuir uma função à conta de armazenamento, grupo de recursos ou subscrição.

### <a name="assign-the-reader-role-for-portal-access"></a>Atribuir a função de Leitor para o acesso ao portal

Ao atribuir uma função incorporada ou personalizada para o Armazenamento Azure a um diretor de segurança, está a conceder permissões a esse diretor de segurança para realizar operações em dados na sua conta de armazenamento. As funções **incorporadas do Data Reader** fornecem permissões de leitura para os dados num recipiente ou fila, enquanto as funções incorporadas do Colaborador de **Dados** fornecem leitura, escrita e eliminam permissões a um recipiente ou fila. As permissões são consultadas ao recurso especificado.  
Por exemplo, se atribuir a função de Colaborador de Dados da **Blob** de Armazenamento ao utilizador Mary ao nível de um recipiente chamado **recipiente de amostras**, então a Mary recebe a leitura, a escrita e a eliminação do acesso a todas as bolhas nesse recipiente.

No entanto, se Maria quiser ver uma bolha no portal Azure, então o papel de Contribuinte de **Dados blob** de armazenamento por si só não fornecerá permissões suficientes para navegar através do portal para a bolha de forma a vê-la. São necessárias permissões adicionais da AD Azure para navegar pelo portal e ver os outros recursos que ali são visíveis.

Se os seus utilizadores precisarem de ter acesso a bolhas no portal Azure, atribua-lhes uma função Adicional rBAC, a função [Reader,](../../role-based-access-control/built-in-roles.md#reader) a esses utilizadores, ao nível da conta de armazenamento ou superior. O papel **do Leitor** é uma função de Gestor de Recursos Azure que permite aos utilizadores visualizar os recursos da conta de armazenamento, mas não modificá-los. Não fornece permissões de leitura a dados no Armazenamento Azure, mas apenas aos recursos de gestão de contas.

Siga estes passos para atribuir a função **Reader** para que um utilizador possa aceder a bolhas do portal Azure. Neste exemplo, a atribuição é remetada à conta de armazenamento:

1. No [portal Azure,](https://portal.azure.com)navegue para a sua conta de armazenamento.
1. Selecione **o controlo de acesso (IAM)** para visualizar as definições de controlo de acesso para a conta de armazenamento. Selecione o separador de **atribuições de funções** para ver a lista de atribuições de papéis.
1. Na janela de atribuição de **funções Adicionar,** selecione a função **Reader.** 
1. A partir do **acesso atribuído ao** campo, selecione Utilizador, grupo ou diretor de serviço **Azure AD**.
1. Procure localizar o diretor de segurança a que pretende atribuir o papel.
1. Salve a atribuição do papel.

A atribuição da função **Reader** só é necessária para utilizadores que necessitem de aceder a bolhas ou filas utilizando o portal Azure.

> [!IMPORTANT]
> A versão de pré-visualização do Storage Explorer no portal Azure não suporta utilizar credenciais azure ad para visualizar e modificar dados de blob ou fila. O Storage Explorer no portal Azure utiliza sempre as chaves da conta para aceder aos dados. Para utilizar o Storage Explorer no portal Azure, deve ser-lhe atribuída uma função que inclua **Microsoft.Storage/storageAccounts/listkeys/action**.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre as funções RBAC para recursos de armazenamento, consulte [O acesso authenticado a blobs e filas Azure utilizando o Diretório Ativo Azure.](storage-auth-aad.md) 
- Para saber mais sobre o RBAC, veja [O que é o controlo de acesso baseado em papéis (RBAC)?](../../role-based-access-control/overview.md)
- Para aprender a atribuir e gerir atribuições de funções RBAC com a Azure PowerShell, Azure CLI ou a REST API, consulte estes artigos:
    - [Gerir o controlo de acesso baseado em funções (RBAC) com a Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Gerir o controlo de acesso baseado em funções (RBAC) com o Azure CLI](../../role-based-access-control/role-assignments-cli.md)
    - [Gerir o controlo de acesso baseado em funções (RBAC) com a API REST](../../role-based-access-control/role-assignments-rest.md)
- Para saber como autorizar o acesso a contentores e filas a partir das suas aplicações de armazenamento, consulte [a Use Azure AD com aplicações](storage-auth-aad-app.md)de Armazenamento Azure .
