---
title: Criar uma conta de Automatização do Azure autónoma
description: Este artigo diz como criar uma conta autónoma da Azure Automation e uma conta Classic Run As.
services: automation
ms.subservice: process-automation
ms.date: 01/15/2019
ms.topic: conceptual
ms.openlocfilehash: c78a65c3cc2d913ba0b836947d2184b3a061a648
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91714639"
---
# <a name="create-a-standalone-azure-automation-account"></a>Criar uma conta de Automatização do Azure autónoma

Este artigo mostra-lhe como criar uma conta Azure Automation no portal Azure. Pode utilizar a conta de automação do portal para avaliar e aprender sobre automação sem utilizar funcionalidades de gestão adicionais ou integrar-se com registos do Azure Monitor. Pode adicionar funcionalidades de gestão ou integrar-se com registos do Azure Monitor para monitorização avançada de trabalhos de runbook em qualquer ponto do futuro.

Com uma conta Automation, pode autenticar livros de execução gerindo recursos em Azure Resource Manager ou no modelo clássico de implementação. Uma Conta de Automatização pode gerir os recursos entre todas as regiões e subscrições para um determinado inquilino.

Quando cria uma conta Automation no portal Azure, a conta **Executar Como** é criada automaticamente. Esta conta faz as seguintes tarefas:

* Cria um diretor de serviço em Azure Ative Directory (Azure AD).
* Cria um certificado.
* Atribui a função Colaboradora, que gere os recursos do Gestor de Recursos Azure utilizando runbooks.

Com esta conta criada para si, pode começar rapidamente a construir e a implementar runbooks para suportar as suas necessidades de automação.

## <a name="permissions-required-to-create-an-automation-account"></a>Permissões necessárias para criar uma conta de Automação

Para criar ou atualizar uma conta Demôm automação e para completar as tarefas descritas neste artigo, tem de ter os seguintes privilégios e permissões:

* Para criar uma conta Automation, a sua conta de utilizador AZURE AD deve ser adicionada a uma função com permissões equivalentes à função Proprietário para `Microsoft.Automation` recursos. Para obter mais informações, consulte [o Controlo de Acesso Baseado em Função na Azure Automation.](automation-role-based-access-control.md)
* No portal Azure, no âmbito do **Azure Ative Directory**MANAGE User , se as  >  **MANAGE**  >  **User settings** **inscrições da App** estiverem definidas para **Sim,** os utilizadores não administrador no seu inquilino Azure AD podem [registar aplicações ative Directory](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). Se **os registos da App** estiverem definidos para **Nº,** o utilizador que executa esta ação deve ter pelo menos uma função de Desenvolvedor de Aplicações em Azure AD.

Se não for membro da instância ative do Diretório da subscrição antes de ser adicionado ao papel global de Administrador/Coadministrator da subscrição, é adicionado ao Ative Directory como convidado. Neste cenário, vê esta mensagem no painel de Conta Ded automação adicionar: `You do not have permissions to create.`

Se um utilizador for adicionado primeiro à função administrador/coadministrator global, pode remover o utilizador da instância ative do Diretório da subscrição. Pode ler o utilizador para a função Utilizador no Ative Directory. Para verificar as funções do utilizador:

1. No portal Azure, vá ao painel de diretório ativo Azure.
1. Selecione **Utilizadores e grupos**.
1. Selecione **Todos os utilizadores**.
1. Depois de selecionar um utilizador específico, selecione **Profile**. O valor do atributo **do tipo Utilizador** no perfil do utilizador não deve ser o **Convidado**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Criar uma nova conta De Automação no portal Azure

Para criar uma conta Azure Automation no portal Azure, complete os seguintes passos:

1. Inscreva-se no portal Azure com uma conta que é membro da função de Administradores de Subscrição e um coadministrator da subscrição.
1. Selecione **+ Criar um recurso.**
1. Procurar **automação.** Nos resultados da pesquisa, **selecione Automation**.

   ![Procure e selecione Controlo de & de Automação no Mercado Azure](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. No ecrã seguinte, **selecione Criar novo**.

   ![Adicionar conta de Automação](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Se vir a seguinte mensagem no painel de Conta Ded automação, a sua conta não é membro da função de Administrador de Subscrição e coadministrator da subscrição.
   >
   > :::image type="content" source="media/automation-create-standalone-account/create-account-without-perms.png" alt-text="Screenshot de prompt 'You's not have permissions to create a Run As account in Azure Ative directy.":::

1. No painel 'Adicionar adm', insira um nome para a sua nova conta Dem automação no campo **Nome.** Não pode mudar este nome depois de escolhido. 

    > [!NOTE]
    > Os nomes das contas de automação são únicos por região e grupo de recursos. Os nomes das contas de Automação eliminadas podem não estar imediatamente disponíveis.

1. Se tiver mais de uma subscrição, utilize o campo **Subscrição** para especificar a subscrição a utilizar para a nova conta.
1. Para **o grupo de recursos,** insira ou selecione um grupo de recursos novo ou existente.
1. Para **localização**, selecione uma localização do datacenter Azure.
1. Para a opção **'Criar Azure Run As as'** opção de conta, certifique-se de que **sim** está selecionado e, em seguida, clique em **Criar**.

   > [!NOTE]
   > Se optar por não criar a conta Run As selecionando **No** for **Create Azure Run As account**, aparece uma mensagem no painel de Conta Ded automatização adicionar. Apesar de a conta ser criada no portal Azure, a conta não tem uma identidade de autenticação correspondente na subscrição do seu modelo de implementação clássico ou no serviço de diretório de subscrição do Azure Resource Manager. Portanto, a conta Automation não tem acesso a recursos na sua subscrição. Isto impede que quaisquer livros que refiram esta conta sejam capazes de autenticar e executar tarefas contra recursos nesses modelos de implantação.
   >
   > :::image type="content" source="media/automation-create-standalone-account/create-account-decline-create-runas-msg.png" alt-text="Screenshot de prompt 'You's not have permissions to create a Run As account in Azure Ative directy.":::
   >
   > Quando o principal de serviço não é criado, a função contribuinte não é atribuída.
   >

1. Para acompanhar o progresso da criação da conta Automation, selecione **Notificações** no menu.

Quando a conta de Automatização é criada com sucesso, são criados vários recursos automaticamente para si. Após a criação, estes livros podem ser eliminados com segurança se não os desejar ficar. O Run As Accounts, pode ser usado para autenticar a sua conta num livro de contas, e deve ser deixado a menos que crie outro ou não as exija. A tabela seguinte resume os recursos para a conta Run As.

| Recurso | Descrição |
| --- | --- |
| Runbook AzureAutomationTutorial |Um runbook gráfico de exemplo que demonstra como autenticar utilizando a conta Run As. O runbook obtém todos os recursos do Gestor de Recursos. |
| AzureAutomationTutorialScript Runbook |Um livro de exemplo powerShell que demonstra como autenticar utilizando a conta Run As. O runbook obtém todos os recursos do Gestor de Recursos. |
| Runbook AzureAutomationTutorialPython2 |Um livro de aplicação python exemplo que demonstra como autenticar utilizando a conta Run As. O livro de recortes lista todos os grupos de recursos presentes na subscrição. |
| AzureRunAsCertificate |Um ativo certificado que é automaticamente criado quando a conta Automation é criada, ou utilizando um script PowerShell para uma conta existente. O certificado autentica-se com o Azure para que possa gerir os recursos do Azure Resource Manager a partir de runbooks. Este certificado tem um tempo de vida de um ano. |
| AzureRunAsConnection |Um ativo de ligação que é criado automaticamente quando a conta Automation é criada, ou utilizando um script PowerShell para uma conta existente. |

## <a name="create-a-classic-run-as-account"></a>Criar uma conta Clássica Como

As contas Classic Run As já não são criadas por padrão quando cria uma conta Azure Automation. Se ainda necessitar de uma conta Classic Run As:

1. Na sua conta de Automação, selecione **Executar como contas** em **Definições de Conta**.
2. Selecione **Azure Classic Run como conta**.
3. Clique em **Criar** para prosseguir com a Classic Run Como criação de conta.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a autoria gráfica, consulte [os livros gráficos do Autor na Azure Automation.](automation-graphical-authoring-intro.md)
* Para começar com os livros powerShell, consulte [Tutorial: Crie um livro de execução PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Para começar com os livros de fluxo de trabalho PowerShell, consulte [Tutorial: Crie um livro de fluxo de trabalho PowerShell](learn/automation-tutorial-runbook-textual.md).
* Para começar com os runbooks Python 2, consulte [Tutorial: Crie um livro de bordo Python 2](learn/automation-tutorial-runbook-textual-python2.md).
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0&preserve-view=true#automation).
