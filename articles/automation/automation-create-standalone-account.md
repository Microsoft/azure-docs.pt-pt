---
title: Criar uma conta de Automatização do Azure autónoma
description: Este artigo orienta você pelas etapas de criação, teste e uso de uma autenticação de entidade de segurança de exemplo na automação do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 01/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1cdea358daa3bd0f9e738a0454613ea774a0e6dc
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146651"
---
# <a name="create-a-standalone-azure-automation-account"></a>Criar uma conta de Automatização do Azure autónoma

Este artigo mostra como criar uma conta de automação do Azure no portal do Azure. Você pode usar a conta de automação do portal para avaliar e saber mais sobre a automação sem usar soluções de gerenciamento adicionais ou integração com logs de Azure Monitor. Você pode adicionar essas soluções de gerenciamento ou integrá-las a logs de Azure Monitor para monitoramento avançado de trabalhos de runbook em qualquer ponto no futuro.

Com uma conta de automação, você pode autenticar runbooks Gerenciando recursos em Azure Resource Manager ou no modelo de implantação clássico. Uma Conta de Automatização pode gerir os recursos entre todas as regiões e subscrições para um determinado inquilino.

Quando você cria uma conta de automação no portal do Azure, essas contas são criadas automaticamente:

* **Conta Executar como**. Essa conta executa as seguintes tarefas:
  * Cria uma entidade de serviço no Azure Active Directory (Azure AD).
  * Cria um certificado.
  * Atribui o RBAC (controle de acesso baseado em função) de colaborador, que gerencia recursos de Azure Resource Manager usando runbooks.

Com essas contas criadas para você, você pode começar rapidamente a criar e implantar runbooks para dar suporte às suas necessidades de automação.

## <a name="permissions-required-to-create-an-automation-account"></a>Permissões necessárias para criar uma conta de automação

Para criar ou atualizar uma conta de automação e para concluir as tarefas descritas neste artigo, você deve ter os seguintes privilégios e permissões:

* Para criar uma conta de automação, sua conta de usuário do Azure ad deve ser adicionada a uma função com permissões equivalentes à **função de proprietário da Microsoft. Recursos** de automação. Para obter mais informações, consulte [controle de acesso baseado em função na automação do Azure](automation-role-based-access-control.md).
* Na portal do Azure, em **Azure Active Directory** > **gerenciar** > **configurações de usuário**, se **registros de aplicativo** estiver definido como **Sim**, os usuários não administradores em seu locatário do Azure ad poderão [registrar ativos Aplicativos de diretório](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). Se **registros de aplicativo** for definido como **não**, o usuário que executa essa ação deverá ser um administrador global no Azure AD.

Se você não for um membro da instância de Active Directory da assinatura antes de ser adicionado à função Administrador Global/coadministrador da assinatura, será adicionado ao Active Directory como convidado. Nesse cenário, você verá esta mensagem na página **adicionar conta de automação** : "Você não tem permissões para criar."

Se um usuário for adicionado à função de administrador global/coadministrador primeiro, você poderá removê-los da instância de Active Directory da assinatura e, em seguida, lê-los para a função de usuário completo no Active Directory.

Para verificar as funções de usuário:

1. Na portal do Azure, vá para o painel de **Azure Active Directory** .
1. Selecionar **Utilizadores e grupos**.
1. Selecione **todos os usuários**.
1. Depois de selecionar um usuário específico, selecione **perfil**. O valor do atributo de **tipo de usuário** no perfil do usuário não deve ser **convidado**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Criar uma nova conta de automação no portal do Azure

Para criar uma conta de automação do Azure no portal do Azure, conclua as seguintes etapas:

1. Entre no portal do Azure com uma conta que seja membro da função Administradores de assinatura e um coadministrador da assinatura.
1. Selecione **+ criar um recurso**.
1. Pesquise **automação**. Nos resultados da pesquisa, selecione **automação**.

   ![Pesquisar e selecionar o controle de & de automação no Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. Na próxima tela, selecione **criar**.

   ![Adicionar conta de automação](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Se você vir a seguinte mensagem no painel **adicionar conta de automação** , sua conta não será membro da função Administradores de assinatura e um coadministrador da assinatura.
   >
   > ![Aviso de adição de conta de automação](media/automation-create-standalone-account/create-account-without-perms.png)

1. No painel **adicionar conta de automação** , na caixa **nome** , insira um nome para a nova conta de automação. Esse nome não pode ser alterado depois de escolhido. *Os nomes de conta de automação são exclusivos por região e grupo de recursos. Os nomes das contas de automação que foram excluídas podem não estar imediatamente disponíveis.*
1. Se você tiver mais de uma assinatura, na caixa **assinatura** , especifique a assinatura que deseja usar para a nova conta.
1. Para **grupo de recursos**, insira ou selecione um grupo de recursos novo ou existente.
1. Para **local**, selecione um local de datacenter do Azure.
1. Para a opção **criar conta Executar como do Azure** , verifique se **Sim** está selecionado e, em seguida, selecione **criar**.

   > [!NOTE]
   > Se você optar por não criar a conta Executar como selecionando **não** para **criar conta Executar como do Azure**, uma mensagem será exibida no painel **adicionar conta de automação** . Embora a conta seja criada no portal do Azure, a conta não tem uma identidade de autenticação correspondente em sua assinatura do modelo de implantação clássica ou no serviço de diretório Azure Resource Manager assinatura. Portanto, a conta de automação não tem acesso aos recursos em sua assinatura. Isso impede que todos os runbooks que fazem referência a essa conta sejam capazes de autenticar e executar tarefas em relação aos recursos nesses modelos de implantação.
   >
   > ![Aviso de adição de conta de automação](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > Quando a entidade de serviço não é criada, a função colaborador não é atribuída.
   >

1. Para acompanhar o progresso da criação da conta de automação, no menu, selecione **notificações**.

### <a name="resources-included"></a>Recursos incluídos

Quando a conta de Automatização é criada com sucesso, são criados vários recursos automaticamente para si. Após a criação, esses runbooks poderão ser excluídos com segurança se você não quiser mantê-los. As contas Executar como podem ser usadas para autenticar sua conta em um runbook e devem ser deixadas a menos que você crie outra ou não as exija. A tabela seguinte resume os recursos para a conta Run As.

| Resource | Descrição |
| --- | --- |
| Runbook AzureAutomationTutorial |Um runbook gráfico de exemplo que demonstra como autenticar usando a conta Executar como. O runbook Obtém todos os recursos do Resource Manager. |
| AzureAutomationTutorialScript Runbook |Um runbook do PowerShell de exemplo que demonstra como autenticar usando a conta Executar como. O runbook Obtém todos os recursos do Resource Manager. |
| Runbook AzureAutomationTutorialPython2 |Um runbook de exemplo do Python que demonstra como autenticar usando a conta Executar como. O runbook lista todos os grupos de recursos presentes na assinatura. |
| AzureRunAsCertificate |Um ativo de certificado criado automaticamente quando a conta de automação é criada ou usando um script do PowerShell para uma conta existente. O certificado é autenticado com o Azure para que você possa gerenciar Azure Resource Manager recursos de runbooks. Este certificado tem um tempo de vida de um ano. |
| AzureRunAsConnection |Um ativo de conexão que é criado automaticamente quando a conta de automação é criada ou usando um script do PowerShell para uma conta existente. |

## <a name="classic-run-as-accounts"></a>Contas Executar como clássicas

As contas Executar como clássicas não são mais criadas, por padrão, quando você cria uma conta de automação do Azure. Se você ainda precisar de uma conta Executar como clássica, execute as etapas a seguir.

1. Na página da sua **conta de automação** , selecione **contas Executar como** em **configurações da conta**.
2. Selecione **conta Executar como clássica do Azure**.
3. Clique em **criar** para prosseguir com a criação da conta Executar como clássica.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a criação gráfica, consulte [criação gráfica na automação do Azure](automation-graphical-authoring-intro.md).
* Para começar com runbooks do PowerShell, consulte [My first PowerShell runbook (O meu primeiro runbook do PowerShell)](automation-first-runbook-textual-powershell.md).
* Para começar com runbooks do fluxo de trabalho do PowerShell, veja [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md).
* Para obter uma introdução aos runbooks Python2, veja [My first Python2 runbook](automation-first-runbook-textual-python2.md) (O meu primeiro runbook Python2).

