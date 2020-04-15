---
title: Criar uma conta de Automatização do Azure autónoma
description: Este artigo percorre-o através dos passos de criação, teste e utilização de um exemplo de autenticação principal de segurança na Automação Azure.
services: automation
ms.subservice: process-automation
ms.date: 01/15/2019
ms.topic: conceptual
ms.openlocfilehash: 3a885f071c89ff6d9bb79d908b19c9451b4ed735
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383307"
---
# <a name="create-a-standalone-azure-automation-account"></a>Criar uma conta de Automatização do Azure autónoma

Este artigo mostra-lhe como criar uma conta Azure Automation no portal Azure. Pode utilizar a conta portal Automation para avaliar e aprender sobre automação sem utilizar soluções de gestão adicionais ou integração com registos do Monitor Azure. Pode adicionar essas soluções de gestão ou integrar-se com os registos do Azure Monitor para monitorização avançada de trabalhos de livros de recorridos em qualquer momento do futuro.

Com uma conta Automation, pode autenticar livros de execução gerindo recursos em Azure Resource Manager ou no modelo clássico de implementação. Uma Conta de Automatização pode gerir os recursos entre todas as regiões e subscrições para um determinado inquilino.

Quando se cria uma conta De automação no portal Azure, estas contas são automaticamente criadas:

* **Executar Como conta.** Esta conta faz as seguintes tarefas:
  * Cria um diretor de serviço no Azure Ative Directory (Azure AD).
  * Cria um certificado.
  * Atribui o Controlo de Acesso Baseado em Funções (RBAC), que gere os recursos do Gestor de Recursos Azure utilizando livros de execução.

Com estas contas criadas para si, pode rapidamente começar a construir e a implementar livros de execução para suportar as suas necessidades de automação.

## <a name="permissions-required-to-create-an-automation-account"></a>Permissões necessárias para criar uma conta de Automação

Para criar ou atualizar uma conta de Automação, e para completar as tarefas descritas neste artigo, deve ter os seguintes privilégios e permissões:

* Para criar uma conta De automação, a sua conta de utilizador Azure AD deve ser adicionada a uma função com permissões equivalentes à função Proprietário da **Microsoft. Recursos de automação.** Para mais informações, consulte [O Controlo de Acesso baseado em Funções na Automação Azure](automation-role-based-access-control.md).
* No portal Azure, sob as**definições**de**Utilizador de Manage** > do **Diretório** > Ativo do Azure, se as inscrições da App forem **definidas** para **Sim,** os utilizadores não administradores no seu inquilino Azure AD podem [registar aplicações de Diretório Ativo.](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions) Se **as inscrições** da App estiverem definidas para **Nº,** o utilizador que executa esta ação deve ser um administrador global no Azure AD.

Se não for membro da instância de Diretório Ativo da subscrição antes de ser adicionado ao papel de administrador/coadministrador global da subscrição, é adicionado ao Ative Directory como convidado. Neste cenário, vê esta mensagem na página **da Conta Add Automation:** "Não tem permissões para criar."

Se um utilizador for adicionado primeiro ao papel de administrador/coadministrador global, pode removê-los da instância de Diretório Ativo da subscrição e, em seguida, lê-los para o papel completo do Utilizador no Diretório Ativo.

Para verificar as funções do utilizador:

1. No portal Azure, vá ao painel de **Diretório Ativo Azure.**
1. Selecione **Utilizadores e grupos**.
1. Selecione **todos os utilizadores**.
1. Depois de selecionar um utilizador específico, selecione **Perfil**. O valor do **atributo** do tipo Utilizador no perfil do utilizador não deve ser **o Convidado**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Criar uma nova conta De Automação no portal Azure

Para criar uma conta Azure Automation no portal Azure, complete os seguintes passos:

1. Inscreva-se no portal Azure com uma conta que seja membro do papel de Administradores de Subscrição e coadministrador da subscrição.
1. Selecione **+ Criar um Recurso**.
1. Pesquisa por **Automação**. Nos resultados da pesquisa, selecione **Automation**.

   ![Procure e selecione Automation & Control no Mercado Azure](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. No ecrã seguinte selecione **Criar**.

   ![Adicionar conta de Automação](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Se vir a seguinte mensagem no painel da **Conta Add Automation,** a sua conta não é membro da função de Administradores de Subscrição e coadministrador da subscrição.
   >
   > ![Adicionar aviso de conta de automação](media/automation-create-standalone-account/create-account-without-perms.png)

1. No painel **'Adicionar A automatização',** na caixa **nome,** introduza um nome para a sua nova conta Automation. Este nome não pode ser alterado depois de escolhido. *Os nomes da Conta de Automação são únicos por região e grupo de recursos. Os nomes das Contas de Automação que foram eliminadas podem não estar imediatamente disponíveis.*
1. Se tiver mais de uma subscrição, na caixa **de subscrição,** especifique a subscrição que pretende utilizar para a nova conta.
1. Para **o grupo Recursos,** insira ou selecione um novo ou existente grupo de recursos.
1. Para **localização**, selecione uma localização do datacenter Azure.
1. Para a **escolha Create Azure Run Como** opção conta, **certifique-se** de que Sim é selecionado e, em seguida, selecione **Criar**.

   > [!NOTE]
   > Se optar por não criar o Run As conta selecionando **No** for **Create Azure Run As account**, aparece uma mensagem no painel da Conta Add **Automation.** Apesar de a conta ser criada no portal Azure, a conta não tem uma identidade de autenticação correspondente na subscrição do modelo de implementação clássico ou no serviço de diretório de subscrição do Gestor de Recursos do Azure. Portanto, a conta Automation não tem acesso a recursos na sua subscrição. Isto impede que quaisquer livros que refiram esta conta sejam capazes de autenticar e executar tarefas contra recursos nesses modelos de implantação.
   >
   > ![Adicionar aviso de conta de automação](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > Quando o diretor de serviço não é criado, a função Contributiva não é atribuída.
   >

1. Para acompanhar o progresso da criação da conta Automation, no menu, selecione **Notificações.**

### <a name="resources-included"></a>Recursos incluídos

Quando a conta de Automatização é criada com sucesso, são criados vários recursos automaticamente para si. Após a criação, estes livros podem ser eliminados com segurança se não desejar mantê-los. O Run As Accounts pode ser usado para autenticar a sua conta num livro de execução, e deve ser deixado a menos que crie outro ou não as exija. A tabela seguinte resume os recursos para a conta Run As.

| Recurso | Descrição |
| --- | --- |
| Runbook AzureAutomationTutorial |Um livro gráfico de exemplo que demonstra como autenticar usando a conta Run As. O livro de corridas obtém todos os recursos do Gestor de Recursos. |
| AzureAutomationTutorialScript Runbook |Um livro de execução da PowerShell que demonstra como autenticar utilizando a conta Run As. O livro de corridas obtém todos os recursos do Gestor de Recursos. |
| Runbook AzureAutomationTutorialPython2 |Um livro de execução python exemplo que demonstra como autenticar usando a conta Run As. O livro de execução lista todos os grupos de recursos presentes na subscrição. |
| AzureRunAsCertificate |Um ativo de certificado que é criado automaticamente quando a conta Automation é criada, ou usando um script PowerShell para uma conta existente. O certificado autentica com o Azure para que possa gerir os recursos do Gestor de Recursos Azure a partir de livros de execução. Este certificado tem um tempo de vida de um ano. |
| AzureRunAsConnection |Um ativo de ligação que é criado automaticamente quando a conta Automation é criada, ou usando um script PowerShell para uma conta existente. |

## <a name="create-a-classic-run-as-account"></a>Criar uma corrida clássica Como conta

Corrida clássica Como as contas já não são criadas por padrão quando cria uma conta De Automação Azure. Se ainda necessitar de uma Corrida Clássica Como conta:

1. Na sua conta De automação, selecione **Executar Como Contas** em **Definições de Conta**.
2. Selecione **Azure Classic Run As Account**.
3. Clique em **Criar** para prosseguir com a Classic Run Como criação de conta.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre autoria gráfica, consulte [a autoria gráfica em Automação Azure.](automation-graphical-authoring-intro.md)
* Para começar com os livros de execução da PowerShell, veja o meu primeiro livro de [corridas PowerShell.](automation-first-runbook-textual-powershell.md)
* Para começar com os livros de fluxo de trabalho powerShell, consulte o meu primeiro livro de execução de fluxo de [trabalho PowerShell.](automation-first-runbook-textual.md)
* Para começar com os livros python 2, veja [o meu primeiro livro python2.](automation-first-runbook-textual-python2.md)
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
