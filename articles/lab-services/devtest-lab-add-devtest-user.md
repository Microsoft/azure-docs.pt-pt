---
title: Adicione proprietários e utilizadores em Azure DevTest Labs. Microsoft Docs
description: Adicione proprietários e utilizadores em Azure DevTest Labs usando o portal Azure ou PowerShell
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284281"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Adicione proprietários e utilizadores em Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

O acesso em Azure DevTest Labs é controlado pelo [Azure Role-Based Access Control (RBAC)](../role-based-access-control/overview.md). Utilizando o RBAC, pode segregar os deveres dentro da sua equipa em *funções* onde concede apenas a quantidade de acesso necessário aos utilizadores para desempenharem os seus trabalhos. Três destas funções rBAC são *Proprietário,* *DevTest Labs User*, e *Contributor*. Neste artigo, você aprende quais as ações que podem ser realizadas em cada uma das três principais funções rBAC. A partir daí, aprende-se a adicionar utilizadores a um laboratório - tanto através do portal como através de um script PowerShell, como como adicionar utilizadores ao nível da subscrição.

## <a name="actions-that-can-be-performed-in-each-role"></a>Ações que podem ser realizadas em cada papel
Existem três funções principais que pode atribuir a um utilizador:

* Proprietário
* Utilizador de Laboratórios DevTest
* Contribuinte

A tabela que se segue ilustra as ações que podem ser executadas pelos utilizadores em cada uma destas funções:

| **Ações que os utilizadores nesta função podem executar** | **Utilizador de Laboratórios DevTest** | **Proprietário** | **Contribuinte** |
| --- | --- | --- | --- |
| **Tarefas de laboratório** | | | |
| Adicione utilizadores a um laboratório |Não |Sim |Não |
| Atualizar definições de custos |Não |Sim |Sim |
| **Tarefas base VM** | | | |
| Adicione e remova imagens personalizadas |Não |Sim |Sim |
| Adicionar, atualizar e eliminar fórmulas |Sim |Sim |Sim |
| Whitelist Azure Marketplace imagens |Não |Sim |Sim |
| **Tarefas VM** | | | |
| Criar VMs |Sim |Sim |Sim |
| Iniciar, parar e eliminar VMs |Apenas VMs criados pelo utilizador |Sim |Sim |
| Atualizar as políticas vm |Não |Sim |Sim |
| Adicionar/remover discos de dados de/para VMs |Apenas VMs criados pelo utilizador |Sim |Sim |
| **Tarefas de artefactos** | | | |
| Adicionar e remover repositórios de artefactos |Não |Sim |Sim |
| Aplicar artefactos |Sim |Sim |Sim |

> [!NOTE]
> Quando um utilizador cria um VM, esse utilizador é automaticamente atribuído à função **Proprietário** do VM criado.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Adicione um proprietário ou utilizador ao nível do laboratório
Proprietários e utilizadores podem ser adicionados ao nível do laboratório através do portal Azure. Um utilizador pode ser um utilizador externo com uma conta Microsoft válida [(MSA)](devtest-lab-faq.md#what-is-a-microsoft-account).
Os seguintes passos guiam-no através do processo de adição de um proprietário ou utilizador a um laboratório em Azure DevTest Labs:

1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Da lista de laboratórios, selecione o laboratório desejado.
4. Na lâmina do laboratório, selecione **Configuração e políticas**. 
5. Na página **de Configuração e políticas,** selecione controlo de **acesso (IAM)** do menu à esquerda. 
6. Selecione Adicionar a atribuição de **funções** na barra de ferramentas para adicionar um utilizador a uma função.
1. Na janela **adicionar permissões,** faça as seguintes ações: 
    1. Selecione uma função (por exemplo: DevTest Labs User). A secção [Ações que podem ser realizadas em cada função](#actions-that-can-be-performed-in-each-role) lista as várias ações que podem ser realizadas pelos utilizadores nas funções Proprietário, Utilizador DevTest e Colaborador.
    2. Selecione o utilizador a adicionar à função. 
    3. Selecione **Guardar**. 
11. Quando volta à lâmina **dos Utilizadores,** o utilizador foi adicionado.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Adicione um utilizador externo a um laboratório usando powerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Além de adicionar utilizadores no portal Azure, pode adicionar um utilizador externo ao seu laboratório usando um script PowerShell. No exemplo seguinte, modifique os valores do parâmetro sob os **Valores para alterar** comentários.
Você pode `subscriptionId`recuperar `labResourceGroup`o `labName` , e valores da lâmina de laboratório no portal Azure.

> [!NOTE]
> O script da amostra pressupõe que o utilizador especificado foi adicionado como convidado ao Diretório Ativo, e falhará se não for esse o caso. Para adicionar um utilizador que não se incomode no Diretório Ativo a um laboratório, utilize o portal Azure para atribuir ao utilizador uma função ilustrada na secção, [Adicionar um proprietário ou utilizador ao nível do laboratório](#add-an-owner-or-user-at-the-lab-level).   
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

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Adicione um proprietário ou utilizador ao nível de subscrição
As permissões azure são propagadas do âmbito dos pais ao âmbito infantil em Azure. Portanto, os proprietários de uma subscrição Azure que contém laboratórios são automaticamente proprietários desses laboratórios. Também possuem os VMs e outros recursos criados pelos utilizadores do laboratório, e o serviço Azure DevTest Labs. 

Você pode adicionar proprietários adicionais a um laboratório através da lâmina do laboratório no [portal Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040). No entanto, o âmbito de administração do proprietário adicionado é mais restrito do que o âmbito do proprietário da subscrição. Por exemplo, os proprietários adicionados não têm acesso total a alguns dos recursos que são criados na subscrição pelo serviço DevTest Labs. 

Para adicionar um proprietário a uma subscrição Azure, siga estes passos:

1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Todos os Serviços**e, em seguida, selecione **Subscrições** da lista.
3. Selecione a subscrição desejada.
4. Selecione ícone **de acesso.** 
   
    ![Utilizadores de acesso](./media/devtest-lab-add-devtest-user/access-users.png)
5. Na lâmina **utilizadores,** selecione **Adicionar**.
   
    ![Adicionar utilizador](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. Na **lâmina Selecione uma** lâmina de função, selecione **Proprietário**.
7. Na lâmina adicionar **utilizadores,** introduza o endereço de e-mail ou o nome do utilizador que pretende adicionar como proprietário. Se o utilizador não puder ser encontrado, obtém-se uma mensagem de erro explicando o problema. Se o utilizador for encontrado, esse utilizador está listado na caixa de texto **do Utilizador.**
8. Selecione o nome de utilizador localizado.
9. Selecione **Selecionar**.
10. Selecione **OK** para fechar a lâmina **de acesso Adicionar.**
11. Quando regressa à lâmina **dos Utilizadores,** o utilizador foi adicionado como proprietário. Este utilizador é agora proprietário de quaisquer laboratórios criados ao abrigo desta subscrição, e assim é capaz de executar tarefas do proprietário. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

