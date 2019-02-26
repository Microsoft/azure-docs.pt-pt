---
title: Criar uma conta de Automatização do Azure autónoma
description: Este artigo orienta-o pelos passos de criação, teste e, usando uma autenticação de principal de segurança de exemplo na automatização do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 197d7529a52b400cd078ead79bbb6db5f5849ee6
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821375"
---
# <a name="create-a-standalone-azure-automation-account"></a>Criar uma conta de Automatização do Azure autónoma

Este artigo mostra-lhe como criar uma conta de automatização do Azure no portal do Azure. Pode utilizar a conta de automatização de portal para avaliar e saiba mais sobre a automatização sem utilizar soluções de gestão adicionais ou integração com os registos do Azure Monitor. Pode adicionar essas soluções de gestão ou integrar com os registos do Azure Monitor para monitorização avançada de tarefas de runbook em qualquer momento no futuro.

Com uma conta de automatização, pode autenticar runbooks ao gerir recursos no Azure Resource Manager ou o modelo de implementação clássica. Uma Conta de Automatização pode gerir os recursos entre todas as regiões e subscrições para um determinado inquilino.

Quando cria uma conta de automatização no portal do Azure, estas contas são criadas automaticamente:

* **Conta Run As**. Esta conta faz as seguintes tarefas:
  * Cria um principal de serviço no Azure Active Directory (Azure AD).
  * Cria um certificado.
  * Atribui o Contributor Role-Based controlo de acesso (RBAC), que gere os recursos do Azure Resource Manager através de runbooks.
* **Conta Run As clássica**. Esta conta carrega um certificado de gestão. O certificado gerencia recursos clássicos através de runbooks.

Com essas contas criadas por si, pode começar rapidamente a criar e implementar runbooks para suportar as suas necessidades de automatização.

## <a name="permissions-required-to-create-an-automation-account"></a>Permissões necessárias para criar uma conta de automatização

Para criar ou atualizar uma conta de automatização e concluir as tarefas descritas neste artigo, tem de ter os privilégios e permissões seguintes:

* Para criar uma conta de automatização, sua conta de utilizador do Azure AD tem de ser adicionada a uma função com permissões equivalentes à função de proprietário para **Microsoft. Automatização** recursos. Para obter mais informações, consulte [controlo de acesso baseado em funções na automatização do Azure](automation-role-based-access-control.md).
* No portal do Azure, em **do Azure Active Directory** > **GERIR** > **registos das aplicações**, se **registos das aplicações**  está definido como **Sim**, os utilizadores de não-administrador no inquilino do Azure AD podem [registar aplicações do Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). Se **registos de aplicações** está definida como **não**, o utilizador que executa esta ação tem de ser um administrador global no Azure AD.

Se não é um membro de instância do Active Directory da subscrição antes de ser adicionado à função de administrador global/coadministrador da subscrição, é adicionado ao Active Directory como convidado. Neste cenário, verá esta mensagem sobre a **adicionar conta de automatização** página: "Não tem permissões para criar."

Se um utilizador é adicionado à função de administrador global/coadministrador em primeiro lugar, pode removê-los da instância do Active Directory da subscrição e, em seguida, readd-los à função de utilizador completa no Active Directory.

Para verificar as funções de utilizador:

1. No portal do Azure, vá para o **do Azure Active Directory** painel.
1. Selecionar **Utilizadores e grupos**.
1. Selecione **todos os utilizadores**.
1. Depois de selecionar um utilizador específico, selecione **perfil**. O valor do **tipo de utilizador** atributo sob o perfil do usuário não deve ser **convidado**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Criar uma nova conta de automatização no portal do Azure

Para criar uma conta de automatização do Azure no portal do Azure, conclua os seguintes passos:

1. Inicie sessão no portal do Azure com uma conta que seja membro da função de administradores da subscrição e um coadministrador da subscrição.
1. Selecione **+ criar um recurso**.
1. Procure **automatização**. Nos resultados da pesquisa, selecione **automatização**.

   ![Procure e selecione automatização & controlo no Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. No ecrã seguinte, selecione **criar**.

  ![Adicionar conta de automatização](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

  > [!NOTE]
  > Se vir a mensagem seguinte no **adicionar conta de automatização** painel, a sua conta não é um membro da função de administradores da subscrição e um coadministrador da subscrição.
  >
  > ![Adicionar aviso de conta de automatização](media/automation-create-standalone-account/create-account-without-perms.png)

1. Na **adicionar conta de automatização** painel, na **nome** , introduza um nome para a sua nova conta de automatização. Este nome não pode ser alterado depois de este será escolhido. *Nomes de conta de automatização são exclusivos por grupo de recursos e região. Os nomes de contas de automatização que foram eliminados poderão não estar imediatamente disponíveis.*
1. Se tiver mais de uma assinatura, além da **subscrição** caixa, especifique a subscrição que pretende utilizar para a nova conta.
1. Para **grupo de recursos**, introduza ou selecione um grupo de recursos novo ou existente.
1. Para **localização**, selecione uma localização do datacenter do Azure.
1. Para o **criar conta Run do Azure** opção, certifique-se de que **Sim** está selecionado e, em seguida, selecione **criar**.

  > [!NOTE]
  > Se optar por não criar a conta Run As, selecionando **não** para **criar do Azure conta Run As**, é apresentada uma mensagem no **adicionar conta de automatização** painel. Apesar da conta é criada no portal do Azure, a conta não tem uma identidade de autenticação correspondente na sua subscrição do modelo de implementação clássica ou no serviço de diretório de subscrição do Azure Resource Manager. Por conseguinte, a conta de automatização não tem acesso a recursos na sua subscrição. Isto impede que todos os runbooks que referenciam esta conta de conseguir autenticar e executar tarefas relativamente aos recursos nesses modelos de implementação.
  >
  > ![Adicionar aviso de conta de automatização](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
  >
  > Se o principal de serviço não for criado, a função Contribuidor não é atribuída.
  >

1. Para acompanhar o progresso da criação de conta de automatização, no menu, selecione **notificações**.

### <a name="resources-included"></a>Recursos incluídos

Quando a conta de Automatização é criada com sucesso, são criados vários recursos automaticamente para si. Após a criação, estes runbooks pode ser eliminados com segurança se não pretender mantê-los. As contas Run as, pode ser utilizado para autenticar a sua conta de um runbook e deve ser deixado, a menos que crie outro ou não requerem-los. A tabela seguinte resume os recursos para a conta Run As.

| Recurso | Descrição |
| --- | --- |
| Runbook AzureAutomationTutorial |Um runbook gráfico de exemplo que demonstra como a autenticação com a conta Run As. O runbook obtém todos os recursos do Resource Manager. |
| AzureAutomationTutorialScript Runbook |Um runbook do PowerShell de exemplo que demonstra como a autenticação com a conta Run As. O runbook obtém todos os recursos do Resource Manager. |
| Runbook AzureAutomationTutorialPython2 |Um runbook de Python de exemplo que demonstra como a autenticação com a conta Run As. O runbook apresenta uma lista de todos os grupos de recursos presentes na subscrição. |
| AzureRunAsCertificate |Um recurso de certificado que foi criado automaticamente quando é criada a conta de automatização ou utilizando um script do PowerShell para uma conta existente. O certificado é autenticado com o Azure, para que possa gerir recursos do Azure Resource Manager a partir dos runbooks. Este certificado tem um tempo de vida de um ano. |
| AzureRunAsConnection |Um recurso de ligação criado automaticamente quando é criada a conta de automatização ou utilizando um script do PowerShell para uma conta existente. |

A tabela seguinte resume os recursos da conta Run As clássica.

| Recurso | Descrição |
| --- | --- |
| AzureClassicAutomationTutorial Runbook |Um runbook gráfico de exemplo. O runbook obtém todas as VMs clássicas numa subscrição utilizando a ser executado como conta clássica (certificado). Em seguida, ele exibe os nomes VM e o estado. |
| AzureClassicAutomationTutorial Script Runbook |Um runbook do PowerShell de exemplo. O runbook obtém todas as VMs clássicas numa subscrição utilizando a ser executado como conta clássica (certificado). Em seguida, ele exibe os nomes VM e o estado. |
| AzureClassicRunAsCertificate |Um recurso de certificado que foi criado automaticamente. O certificado é autenticado com o Azure, para que possa gerir recursos clássicos do Azure a partir dos runbooks. Este certificado tem um tempo de vida de um ano. |
| AzureClassicRunAsConnection |Um recurso de ligação criado automaticamente. O elemento é autenticado com o Azure, para que possa gerir recursos clássicos do Azure a partir dos runbooks. |

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre a criação de gráficos, veja [criação de gráficos na automatização do Azure](automation-graphical-authoring-intro.md).
* Para começar com runbooks do PowerShell, consulte [My first PowerShell runbook (O meu primeiro runbook do PowerShell)](automation-first-runbook-textual-powershell.md).
* Para começar com runbooks do fluxo de trabalho do PowerShell, veja [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md).
* Para obter uma introdução aos runbooks Python2, veja [My first Python2 runbook](automation-first-runbook-textual-python2.md) (O meu primeiro runbook Python2).

