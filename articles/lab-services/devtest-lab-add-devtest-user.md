---
title: Adicionar proprietários e utilizadores no Azure DevTest Labs | Documentos da Microsoft
description: Adicionar proprietários e utilizadores no Azure DevTest Labs com o portal do Azure ou o PowerShell
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 4f51d9a5-2702-45f0-a2d5-a3635b58c416
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: spelluru
ms.openlocfilehash: a9426c20ae23fd3dad4cdba25590ff2eac271896
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727966"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Adicionar proprietários e utilizadores no Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Acesso no Azure DevTest Labs é controlado pelas [controlo de acesso de controlo (RBAC)](../role-based-access-control/overview.md). Utilizando o RBAC, pode separar responsabilidades dentro da sua equipa em *funções* onde pode conceder apenas a quantidade de acesso necessário aos utilizadores para desempenhar as suas funções. Três dessas funções de RBAC são *proprietário*, *utilizador de DevTest Labs*, e *contribuinte*. Neste artigo, saiba que ações podem ser executadas em cada uma das três funções RBAC principais. A partir daí, saiba como adicionar utilizadores a um laboratório - através do portal e através de um script do PowerShell e como adicionar utilizadores ao nível da subscrição.

## <a name="actions-that-can-be-performed-in-each-role"></a>Ações que podem ser executadas em cada função
Existem três funções principais que pode atribuir um utilizador:

* Proprietário
* Utilizador do DevTest Labs
* Contribuinte

A tabela seguinte ilustra as ações que podem ser realizadas por utilizadores em cada uma dessas funções:

| **Os utilizadores de ações nesta função podem realizar** | **Utilizador de DevTest Labs** | **Proprietário** | **Contribuidor** |
| --- | --- | --- | --- |
| **Tarefas de laboratório** | | | |
| Adicionar utilizadores a um laboratório |Não |Sim |Não |
| Atualizar as definições de custo |Não |Sim |Sim |
| **Tarefas de bases de VM** | | | |
| Adicionar e remover imagens personalizadas |Não |Sim |Sim |
| Adicionar, atualizar e eliminar fórmulas |Sim |Sim |Sim |
| Imagens de lista de permissões do Azure Marketplace |Não |Sim |Sim |
| **Tarefas VM** | | | |
| Criar VMs |Sim |Sim |Sim |
| Iniciar, parar e eliminar as VMs |Apenas as VMs criadas pelo utilizador |Sim |Sim |
| Atualizar as políticas de VM |Não |Sim |Sim |
| Adicionar ou remover discos de dados de/para VMs |Apenas as VMs criadas pelo utilizador |Sim |Sim |
| **Tarefas de artefacto** | | | |
| Adicionar e remover os repositórios de artefacto |Não |Sim |Sim |
| Aplicam-se de artefactos |Sim |Sim |Sim |

> [!NOTE]
> Quando um utilizador cria uma VM, esse utilizador é atribuído automaticamente para o **proprietário** função da VM criada.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Adicionar um proprietário ou o utilizador ao nível do laboratório
Os proprietários e os utilizadores podem ser adicionados ao nível do laboratório através do portal do Azure. O utilizador pode ser um utilizador externo com válido [conta Microsoft (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account).
Os seguintes passos guiá-lo pelo processo de adição de um proprietário ou o utilizador a um laboratório no Azure DevTest Labs:

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Na lista de laboratórios, selecione o laboratório pretendido.
4. No painel do laboratório, selecione **Konfigurace a zásady**. 
5. Sobre o **Konfigurace a zásady** página, selecione **controlo de acesso (IAM)** no menu à esquerda. 
6. Selecione **adicionar atribuição de função** na barra de ferramentas para adicionar um utilizador a uma função.
1. Na **adicionar permissões** janela, fazer as seguintes ações: 
    1. Selecione uma função (por exemplo: Utilizador de DevTest Labs). A secção [ações que podem ser executadas em cada função](#actions-that-can-be-performed-in-each-role) lista as várias ações que podem ser executadas pelos utilizadores nas funções de proprietário, o utilizador de DevTest e contribuinte.
    2. Selecione o utilizador a ser adicionado à função. 
    3. Selecione **Guardar**. 
11. Quando regressar para o **utilizadores** painel, o utilizador foi adicionado.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Adicionar um utilizador externo a um laboratório com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Além de adicionar os utilizadores no portal do Azure, pode adicionar um utilizador externo para seu laboratório usando um script do PowerShell. No exemplo a seguir, modificar os valores de parâmetros nos **valores mudem** comentário.
Pode recuperar os `subscriptionId`, `labResourceGroup`, e `labName` valores a partir do painel de laboratório no portal do Azure.

> [!NOTE]
> O script de exemplo parte do princípio de que o utilizador especificado foi adicionado como convidado no Active Directory e irá falhar se não for esse o caso. Para adicionar um utilizador não no Active Directory a um laboratório, utilize o portal do Azure para atribuir o utilizador a uma função, conforme ilustrado na seção, [adicionar um proprietário ou o utilizador ao nível do laboratório](#add-an-owner-or-user-at-the-lab-level).   
> 
> 

    # Add an external user in DevTest Labs user role to a lab
    # Ensure that guest users can be added to the Azure Active directory:
    # https://azure.microsoft.com/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource name here>"
    $labName = "<Enter lab name here>"
    $userDisplayName = "<Enter user's display name here>"

    # Log into your Azure account
    Connect-AzAccount

    # Select the Azure subscription that contains the lab. 
    # This step is optional if you have only one subscription.
    Select-AzSubscription -SubscriptionId $subscriptionId

    # Retrieve the user object
    $adObject = Get-AzADUser -SearchString $userDisplayName

    # Create the role assignment. 
    $labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    New-AzRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Adicionar um proprietário ou o utilizador ao nível da subscrição
Permissões do Azure são propagadas dos âmbito principal para o âmbito subordinado no Azure. Por conseguinte, os proprietários de uma subscrição do Azure que contém os laboratórios são automaticamente os proprietários desses laboratórios. Também possuem as VMs e outros recursos criados por utilizadores do laboratório e o serviço de Azure DevTest Labs. 

Pode adicionar mais proprietários a um laboratório através do painel do laboratório no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040). No entanto, o âmbito do proprietário adicionado de administração é menos abrangente que o proprietário da subscrição. Por exemplo, os proprietários adicionados não tem acesso total a alguns dos recursos que são criados pelo serviço do DevTest Labs na subscrição. 

Para adicionar um proprietário a uma subscrição do Azure, siga estes passos:

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **todos os serviços**e, em seguida, selecione **subscrições** da lista.
3. Selecione a subscrição pretendida.
4. Selecione **acesso** ícone. 
   
    ![Utilizadores de acesso](./media/devtest-lab-add-devtest-user/access-users.png)
5. Sobre o **usuários** painel, selecione **Add**.
   
    ![Adicionar utilizador](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. Sobre o **selecionar uma função** painel, selecione **proprietário**.
7. Sobre o **adicionar utilizadores** painel, introduza o endereço de e-mail ou nome do utilizador que pretende adicionar como proprietário. Se o utilizador não for encontrado, receberá uma mensagem de erro explicando o problema. Se o utilizador for encontrado, o que o utilizador está listado no **utilizador** caixa de texto.
8. Selecione o nome de utilizador localizado.
9. Selecione **selecione**.
10. Selecione **OK** para fechar a **adicionar acesso** painel.
11. Quando regressar para o **utilizadores** painel, o utilizador foi adicionado como proprietário. Este utilizador é agora um proprietário de qualquer laboratórios criado sob esta subscrição e, portanto, é capaz de executar tarefas de proprietário. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

