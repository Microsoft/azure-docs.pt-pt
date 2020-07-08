---
title: Gerir uma execução de automação Azure Como conta
description: Este artigo diz como gerir a sua conta Run As com o PowerShell ou a partir do portal Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 06/26/2020
ms.topic: conceptual
ms.openlocfilehash: 66fb5741a5ed1af9e7edf002485c959f9f2fc82f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85507267"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Gerir uma execução de automação Azure Como conta

Executar Como as contas na Azure Automation fornecem autenticação para gestão de recursos em Azure utilizando os cmdlets Azure. Quando cria uma conta Run As, cria um novo utilizador principal de serviço no Azure Ative Directory (AD) e atribui a função contribuinte a este utilizador ao nível da subscrição.

## <a name="types-of-run-as-accounts"></a>Tipos de Run As contas

A Azure Automation utiliza dois tipos de contas Run As:

* Conta Run As do Azure
* Azure Classic Run Como conta

>[!NOTE]
>As assinaturas Azure Cloud Solution Provider (CSP) suportam apenas o modelo Azure Resource Manager. Os serviços não-Azure Resource Manager não estão disponíveis no programa. Quando está a utilizar uma subscrição CSP, a conta Azure Classic Run As não é criada, mas a conta Azure Run As é criada. Para saber mais sobre as subscrições da CSP, consulte [os serviços disponíveis em subscrições CSP.](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)

O titular do serviço para uma Execução como Conta não tem permissões para ler Azure AD por padrão. Se pretender adicionar permissões para ler ou gerir a AD Azure, terá de conceder as permissões ao principal do serviço sob **permissões da API.** Para saber mais, consulte [Adicionar permissões para aceder a APIs web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

### <a name="run-as-account"></a>Conta Run As

A conta Run As gere os recursos [do modelo de implementação do Gestor de Recursos.](../azure-resource-manager/management/deployment-models.md) Faz as seguintes tarefas.

* Cria uma aplicação do Azure AD com um certificado autoassinado, cria uma conta de principal de serviço para a aplicação no Azure AD e atribui a função Contribuidor à conta na sua subscrição atual. Pode alterar a definição de certificado para Proprietário ou qualquer outra função. Para obter mais informações, veja [Controlo de acesso baseado em funções na Automatização do Azure](automation-role-based-access-control.md).
  
* Cria um certificado de automação nomeado `AzureRunAsCertificate` na conta de Automação especificada. O certificado detém a chave privada de certificado que a aplicação Azure AD utiliza.
  
* Cria um ativo de ligação Automation named `AzureRunAsConnection` in the specific Automation account. O ativo de ligação detém o ID da aplicação, iD do inquilino, ID de assinatura e impressão digital de certificado.

### <a name="azure-classic-run-as-account"></a>Conta Run As Clássica do Azure

A conta Azure Classic Run Como gere os recursos [do modelo de implantação clássico.](../azure-resource-manager/management/deployment-models.md) Deve ser coadministrador na subscrição para criar ou renovar este tipo de conta.

A conta Azure Classic Run As executa as seguintes tarefas.

  * Cria um certificado de gestão na subscrição.

  * Cria um certificado de automação nomeado `AzureClassicRunAsCertificate` na conta de Automação especificada. O recurso do certificado contém a chave privada do certificado que o certificado de gestão utiliza.

  * Cria um ativo de ligação Automation named `AzureClassicRunAsConnection` in the specific Automation account. O ativo de ligação detém o nome de subscrição, iD de subscrição e nome do ativo do certificado.

>[!NOTE]
>A azure Classic Run Como a conta não é criada por padrão ao mesmo tempo que cria uma conta Automation. Esta conta é criada individualmente seguindo os passos descritos mais tarde neste artigo.

## <a name="obtain-run-as-account-permissions"></a><a name="permissions"></a>Obter Executar Como permissões de conta

Esta secção define permissões tanto para contas regulares como para contas Desafinada como para a Execução Clássica como para as contas.

### <a name="get-permissions-to-configure-run-as-accounts"></a>Obtenha permissões para configurar executar como contas

Para criar ou atualizar uma conta Run As, tem de ter privilégios e permissões específicas. Um administrador de aplicação no Azure Ative Directory e um Proprietário numa subscrição podem completar todas as tarefas. Numa situação em que se tem separação de deveres, o quadro a seguir apresenta uma lista das tarefas, o cmdlet equivalente, e permissões necessárias:

|Tarefa|Cmdlet  |Permissões Mínimas  |Onde define as permissões|
|---|---------|---------|---|
|Criar aplicação Azure Ad|[Aplicação New-AzAD](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication)     | Função de Desenvolvedor de Aplicações<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Home > Azure AD > Registos de Aplicações |
|Adicione uma credencial à aplicação.|[Novo-AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential)     | Administrador de Aplicação ou Administrador Global<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Home > Azure AD > Registos de Aplicações|
|Criar e obter um diretor de serviço AZure AD|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal)     | Administrador de Aplicação ou Administrador Global<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Home > Azure AD > Registos de Aplicações|
|Atribuir ou obter o papel RBAC para o principal especificado|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Administrador de acesso ao utilizador ou proprietário, ou tenha as seguintes permissões:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Subscrição](../role-based-access-control/role-assignments-portal.md)</br>Subscrições > > \<subscription name\> - Controlo de Acesso (IAM)|
|Criar ou remover um certificado de Automação|[Novo AzAutomationCertificato](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remover-AzAutomationCertificato](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate)     | Contribuinte no grupo de recursos         |Grupo de recursos de conta de automação|
|Criar ou remover uma ligação de Automação|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection)</br>[Remover-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection)|Contribuinte no grupo de recursos |Grupo de recursos de conta de automação|

<sup>1</sup> Os utilizadores não administrador do seu inquilino AZure AD podem [registar aplicações AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) se os Utilizadores do inquilino AZURE **AD puderem registar** a opção de aplicações na página de definições do Utilizador está definida como **Sim**. Se a definição de registo de pedido for **Nº,** o utilizador que executa esta ação deve ser definido nesta tabela.

Se não for membro da instância ative do Diretório da subscrição antes de ser adicionado ao papel de Administrador Global da subscrição, é adicionado como convidado. Nesta situação, recebe um `You do not have permissions to create…` aviso na página **'Adicionar Conta Dem automação'.**

Se for membro da instância Ative Directory da subscrição quando a função de Administrador Global for atribuída, também pode receber um `You do not have permissions to create…` aviso na página **'Adicionar ad automatização'.** Neste caso, pode solicitar a remoção da instância ative do Diretório da subscrição e, em seguida, solicitar a re-adição, para que se torne um utilizador completo no Ative Directory.

Para verificar se a situação que produz a mensagem de erro foi corrigida:

1. A partir do painel de diretório ativo Azure no portal Azure, selecione **Utilizadores e grupos**.
2. Selecione **Todos os utilizadores**.
3. Escolha o seu nome e, em seguida, selecione **Perfil**. 
4. Certifique-se de que o valor do atributo **do tipo Utilizador** no perfil do utilizador não está definido para o **Convidado**.

### <a name="get-permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Obtenha permissões para configurar Classic Run Como contas

Para configurar ou renovar a Classic Run As contas, tem de ter a função de Coadministrador ao nível da subscrição. Para saber mais sobre permissões clássicas de subscrição, consulte os [administradores de subscrição clássicos da Azure.](../role-based-access-control/classic-administrators.md#add-a-co-administrator)

## <a name="create-a-run-as-account-in-azure-portal"></a>Criar uma conta Run As no portal Azure

Execute os seguintes passos para atualizar a sua conta Azure Automation no portal Azure. Crie a Run As e Classic Run As Como contas individualmente. Se não precisar de gerir os recursos clássicos, pode simplesmente criar a conta Run As do Azure.

1. Inicie sessão no portal do Azure com uma conta que seja membro da função de Administradores da Subscrição e o coadministrador da subscrição.

2. Procure e selecione **Contas de Automação.**

3. Na página 'Contas de Automação', selecione a sua conta Demôm automação na lista.

4. No painel esquerdo, selecione **Executar Como Contas** na secção de definições de conta.

5. Consoante a conta que precisar, selecione **Conta Run As do Azure** ou **Conta Run As Clássica do Azure**. 

6. Dependendo da conta de juros, utilize o **Add Azure Run As** ou Add **Azure Classic Run As Account.** Depois de rever as informações de visão geral, clique em **Criar**.

7. Enquanto o Azure cria a conta Run As, pode acompanhar o progresso em **Notificações** a partir do menu. Também é exibido um banner indicando que a conta está a ser criada. O processo pode levar alguns minutos para ser concluído.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Eliminar uma conta Run As ou Run As Clássica

Esta secção descreve como eliminar uma conta Run As ou Classic Run As. Quando executar esta ação, a conta de Automatização é mantida. Depois de apagar a conta, pode reucê-la no portal Azure.

1. No portal do Azure, abra a conta de Automatização.

2. No painel esquerdo, selecione **Executar Como Contas** na secção de definições de conta.

3. Nas propriedades Contas Run As, selecione a conta Run As ou a conta Run As Clássica que quer eliminar. 

4. No painel propriedades para a conta selecionada, clique em **Eliminar**.

   ![Eliminar a conta Run As](media/manage-runas-account/automation-account-delete-runas.png)

5. Enquanto a conta estiver a ser eliminada, pode acompanhar o progresso em **Notificações** a partir do menu.

6. Assim que a conta tiver sido eliminada, pode voltar a criá-la na página de propriedades Contas Run As, ao selecionar a opção de criação **Conta Run As do Azure**.

   ![Recriar a conta Run As de Automatização](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Renovar um certificado auto-assinado

O certificado auto-assinado que criou para a conta Run As expira um ano a contar da data de criação. Em algum momento antes da sua conta run Como expira, você deve renovar o certificado. Pode renová-lo a qualquer momento antes que expire. 

Quando renova o certificado auto-assinado, o certificado válido atual é mantido para garantir que quaisquer livros que estejam em fila ou que estejam a funcionar ativamente, e que autentem com a conta Run As, não sejam afetados negativamente. O certificado permanece válido até à data de expiração.

>[!NOTE]
>Se acha que a conta Run As foi comprometida, pode eliminar e recriar o certificado auto-assinado.

>[!NOTE]
>Se configurar a sua conta Run As para utilizar um certificado emitido pela autoridade de certificados da empresa e utilizar a opção de renovar uma opção de certificado auto-assinado, o certificado da empresa é substituído por um certificado auto-assinado.

Utilize os seguintes passos para renovar o certificado auto-assinado.

1. No portal do Azure, abra a conta de Automatização.

1. Selecione **Executar Como Contas** na secção de definições de conta.

    ![Painel Propriedades da conta de automatização](media/manage-runas-account/automation-account-properties-pane.png)

1. Na página 'Executar Como Contas' propriedades, selecione a conta Run As ou a Conta Clássica como para a renovação do certificado.

1. No painel de propriedades para a conta selecionada, clique em **Renovar o certificado**.

    ![Renovar certificado da conta Run As](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Enquanto o certificado estiver a ser renovado, pode acompanhar o progresso em **Notificações** a partir do menu.

## <a name="limit-run-as-account-permissions"></a>Prazo executado como permissões de conta

Para controlar a segmentação da Automação contra recursos em Azure, pode executar o [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) script. Este script altera o seu run como principal do serviço de conta existente para criar e usar uma definição de função personalizada. O papel tem permissões para todos os recursos, exceto [o Key Vault.](https://docs.microsoft.com/azure/key-vault/)

>[!IMPORTANT]
>Depois de executar o script **Update-AutomationRunAsAccountRoleAssignments.ps1,** os livros que acedem ao Key Vault através da utilização de Run As contas já não funcionam. Antes de executar o script, deve rever os runbooks na sua conta para chamadas para Azure Key Vault. Para permitir o acesso ao Key Vault a partir de runbooks da Azure Automation, tem de [adicionar a conta Run As às permissões do Key Vault](#add-permissions-to-key-vault).

Se precisar de restringir, promover o que o responsável de serviço pode fazer, pode adicionar outros tipos de recursos ao `NotActions` elemento da definição de função personalizada. O exemplo a seguir restringe o acesso a `Microsoft.Compute/*` . Se adicionar este tipo de recurso `NotActions` para a definição de função, a função não será capaz de aceder a qualquer recurso Compute. Para saber mais sobre definições de funções, consulte [as definições de funções para os recursos da Azure.](../role-based-access-control/role-definitions.md)

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Pode determinar se o principal de serviço utilizado pela sua conta Run As está na definição de função do Contribuinte ou na definição personalizada. 

1. Aceda à sua conta Demômes automática e selecione **Executar Como Contas** na secção de definições de conta.
2. Selecione **Azure Run as Account**. 
3. Selecione **Função** para localizar a definição de função que está a ser usada.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Também pode determinar a definição de função utilizada pelas contas Run As para várias subscrições ou contas de Automação. Faça-o utilizando [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) oCheck-AutomationRunAsAccountRoleAssignments.ps1script na PowerShell Gallery.

### <a name="add-permissions-to-key-vault"></a>Adicionar permissões ao Cofre de Chaves

Pode permitir que a Azure Automation verifique se o Key Vault e o seu responsável pelo serviço de conta Run As estão a utilizar uma definição de função personalizada. Tens de o fazer:

* Conceda permissões ao Key Vault.
* Desa parte da política de acesso.

Pode utilizar [o](https://aka.ms/AA5hugb)Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1script na PowerShell Gallery para dar a sua execução como permissões de conta para Key Vault. Consulte [o acesso das aplicações Grant a um cofre chave](../key-vault/general/group-permissions-for-apps.md) para obter mais detalhes sobre a definição de permissões no Key Vault.

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Resolver problemas de configuração errada para run As accounts

Alguns itens de configuração necessários para uma execução como ou corrida clássica Como a conta pode ter sido eliminada ou criada indevidamente durante a configuração inicial. Possíveis casos de má configuração incluem:

* Recurso de certificado
* Recurso de ligação
* Executar Como conta removida da função contribuinte
* Principal de serviço ou aplicação no Azure AD

Para tais casos de configuração errada, a conta Automation deteta as alterações e apresenta um estado de *Incompleto* no painel de propriedades run As accounts para a conta.

![Estado de configuração Não concluída da conta Run As](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Quando seleciona a conta Run As, o painel de propriedades da conta apresenta a seguinte mensagem de erro:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Pode resolver rapidamente estes problemas da conta Run As, ao eliminar e recriar a conta.

## <a name="next-steps"></a>Próximos passos

* [Objetos de aplicação e objetos principais de serviço.](../active-directory/develop/app-objects-and-service-principals.md)
* [Visão geral dos certificados para serviços de nuvem Azure](../cloud-services/cloud-services-certs-create.md).
