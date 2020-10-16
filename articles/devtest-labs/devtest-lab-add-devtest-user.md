---
title: Adicione proprietários e utilizadores em Azure DevTest Labs Microsoft Docs
description: Adicione proprietários e utilizadores em Azure DevTest Labs usando o portal Azure ou PowerShell
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 40173589c4798a8b00d940bca2a71f4d61f2c199
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87533330"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Adicionar proprietários e utilizadores em Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

O acesso em Azure DevTest Labs é controlado pelo [controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md). Utilizando o RBAC, pode segregar os deveres dentro da sua equipa em *funções* em que concede apenas a quantidade de acesso necessário aos utilizadores para desempenharem os seus trabalhos. Três destas funções da Azure são *Proprietário,* *Utilizador de DevTest Labs*e *Colaborador.* Neste artigo, você aprende que ações podem ser realizadas em cada uma das três principais funções de Azure. A partir daí, aprende-se a adicionar utilizadores a um laboratório - tanto através do portal como através de um script PowerShell, e como adicionar utilizadores ao nível da subscrição.

## <a name="actions-that-can-be-performed-in-each-role"></a>Ações que podem ser realizadas em cada papel
Existem três funções principais que pode atribuir a um utilizador:

* Proprietário
* Utilizador de Laboratórios DevTest
* Contribuinte

A tabela a seguir ilustra as ações que podem ser executadas pelos utilizadores em cada uma destas funções:

| **Ações que os utilizadores neste papel podem desempenhar** | **Utilizador de Laboratórios DevTest** | **Proprietário** | **Contribuinte** |
| --- | --- | --- | --- |
| **Tarefas de laboratório** | | | |
| Adicionar utilizadores a um laboratório |Não |Sim |Não |
| Atualizar definições de custos |Não |Sim |Sim |
| **Tarefas de base VM** | | | |
| Adicione e remova imagens personalizadas |Não |Sim |Sim |
| Adicione, atualize e elimine fórmulas |Sim |Sim |Sim |
| Whitelist Azure Marketplace imagens |Não |Sim |Sim |
| **Tarefas VM** | | | |
| Criar VMs |Sim |Sim |Sim |
| Iniciar, parar e apagar VMs |Apenas VMs criados pelo utilizador |Sim |Sim |
| Atualizar as políticas de VM |Não |Sim |Sim |
| Adicionar/remover discos de dados de/para VMs |Apenas VMs criados pelo utilizador |Sim |Sim |
| **Tarefas de artefacto** | | | |
| Adicione e remova repositórios de artefactos |Não |Sim |Sim |
| Aplicar artefactos |Sim |Sim |Sim |

> [!NOTE]
> Quando um utilizador cria um VM, esse utilizador é automaticamente atribuído à função **Proprietário** do VM criado.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Adicione um proprietário ou utilizador ao nível do laboratório
Proprietários e utilizadores podem ser adicionados ao nível do laboratório através do portal Azure. Um utilizador pode ser um utilizador externo com uma conta Microsoft válida [(MSA)](devtest-lab-faq.md#what-is-a-microsoft-account).
Os seguintes passos guiam-no através do processo de adição de um proprietário ou utilizador a um laboratório em Azure DevTest Labs:

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Da lista de laboratórios, selecione o laboratório desejado.
4. Na lâmina do laboratório, selecione **Configuração e políticas**. 
5. Na página **de Configuração e políticas,** selecione **Access control (IAM)** a partir do menu à esquerda. 
6. **Selecione Adicionar a atribuição de funções** na barra de ferramentas para adicionar um utilizador a uma função.
1. Na janela **de permissões Adicionar,** faça as seguintes ações: 
    1. Selecione uma função (por exemplo: Utilizador de DevTest Labs). A secção [As ações que podem ser executadas em cada função](#actions-that-can-be-performed-in-each-role) lista as várias ações que podem ser realizadas pelos utilizadores nas funções Proprietário, Utilizador DevTest e Colaborador.
    2. Selecione o utilizador a adicionar à função. 
    3. Selecione **Guardar**. 
11. Quando voltar à lâmina do **Utilizador,** o utilizador foi adicionado.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Adicione um utilizador externo a um laboratório usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Além de adicionar utilizadores no portal Azure, pode adicionar um utilizador externo ao seu laboratório utilizando um script PowerShell. No exemplo seguinte, modifique os valores dos parâmetros nos **Valores para alterar** comentários.
Pode recuperar o `subscriptionId` `labResourceGroup` , e `labName` valores da lâmina de laboratório no portal Azure.

> [!NOTE]
> O script da amostra pressupõe que o utilizador especificado foi adicionado como convidado ao Ative Directory, e falhará se não for esse o caso. Para adicionar um utilizador que não está no Ative Directory a um laboratório, utilize o portal Azure para atribuir o utilizador a uma função ilustrada na secção, [Adicione um proprietário ou utilizador ao nível do laboratório](#add-an-owner-or-user-at-the-lab-level).   
> 
> 

```azurepowershell
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
```

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Adicionar um proprietário ou utilizador ao nível de subscrição
As permissões azure são propagadas do âmbito dos pais para o âmbito da criança em Azure. Portanto, os proprietários de uma assinatura Azure que contém laboratórios são automaticamente proprietários desses laboratórios. Também possuem os VMs e outros recursos criados pelos utilizadores do laboratório, e o serviço Azure DevTest Labs. 

Pode adicionar mais proprietários a um laboratório através da lâmina do laboratório no [portal Azure.](https://go.microsoft.com/fwlink/p/?LinkID=525040) No entanto, o âmbito de administração do proprietário adicional é mais estreito do que o âmbito de aplicação do proprietário da subscrição. Por exemplo, os proprietários adicionados não têm acesso total a alguns dos recursos que são criados na subscrição pelo serviço DevTest Labs. 

Para adicionar um proprietário a uma subscrição da Azure, siga estes passos:

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Todos os Serviços**e, em seguida, selecione **Subscrições** da lista.
3. Selecione a subscrição desejada.
4. Selecione o ícone **de acesso.** 
   
    ![Utilizadores de acesso](./media/devtest-lab-add-devtest-user/access-users.png)
5. Na lâmina do **Utilizadores,** **selecione Adicionar**.
   
    ![Adicionar utilizador](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. No **Selecionar uma lâmina de função,** selecione **Proprietário**.
7. Na lâmina **do Utilizador Adicionar,** insira o endereço de e-mail ou o nome do utilizador que pretende adicionar como proprietário. Se o utilizador não puder ser encontrado, obtém-se uma mensagem de erro explicando o problema. Se o utilizador for encontrado, esse utilizador está listado na caixa de texto do **Utilizador.**
8. Selecione o nome de utilizador localizado.
9. Selecione **Selecionar**.
10. Selecione **OK** para fechar a lâmina **de acesso Adicionar.**
11. Quando regressar à lâmina do **Utilizador,** o utilizador foi adicionado como proprietário. Este utilizador é agora proprietário de quaisquer laboratórios criados ao abrigo desta subscrição, e assim é capaz de executar tarefas do proprietário. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
