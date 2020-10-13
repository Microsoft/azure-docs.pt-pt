---
title: Gerir uma execução de automação Azure Como conta
description: Este artigo diz como gerir a sua conta Run As com o PowerShell ou a partir do portal Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 09/28/2020
ms.topic: conceptual
ms.openlocfilehash: 3357cb40ff476a3cc0bce259930068aeccf2c10c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91767451"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Gerir uma execução de automação Azure Como conta

Executar Como contas na Azure Automation fornecem autenticação para gerir recursos no Azure Resource Manager ou no modelo de implementação Azure Classic utilizando runbooks automation e outras funcionalidades de Automação. Este artigo fornece orientações sobre como gerir uma conta Run As ou Classic Run As.

Para saber mais sobre a autenticação da conta Azure Automation e orientação relacionada com cenários de automatização de processos, consulte [a visão geral da autenticação da Conta de Automação](automation-security-overview.md).

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Executar como permissões de conta

Esta secção define permissões tanto para contas regulares como para contas Desafinada como para a Execução Clássica como para as contas.

Para criar ou atualizar uma conta Run As, tem de ter privilégios e permissões específicas. Um administrador de aplicação no Azure Ative Directory e um Proprietário numa subscrição podem completar todas as tarefas. Numa situação em que se tem separação de deveres, o quadro a seguir apresenta uma lista das tarefas, o cmdlet equivalente, e permissões necessárias:

|Tarefa|Cmdlet  |Permissões Mínimas  |Onde define as permissões|
|---|---------|---------|---|
|Criar aplicação Azure Ad|[Aplicação New-AzAD](/powershell/module/az.resources/new-azadapplication)     | Função de Desenvolvedor de Aplicações<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Home > Azure AD > Registos de Aplicações |
|Adicione uma credencial à aplicação.|[Novo-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Administrador de Aplicação ou Administrador Global<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Home > Azure AD > Registos de Aplicações|
|Criar e obter um diretor de serviço AZure AD|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Administrador de Aplicação ou Administrador Global<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Home > Azure AD > Registos de Aplicações|
|Atribuir ou obter o papel de Azure para o principal especificado|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Administrador de acesso ao utilizador ou proprietário, ou tenha as seguintes permissões:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Subscrição](../role-based-access-control/role-assignments-portal.md)</br>Subscrições > > \<subscription name\> - Controlo de Acesso (IAM)|
|Criar ou remover um certificado de Automação|[Novo AzAutomationCertificato](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remover-AzAutomationCertificato](/powershell/module/az.automation/remove-azautomationcertificate)     | Contribuinte no grupo de recursos         |Grupo de recursos de conta de automação|
|Criar ou remover uma ligação de Automação|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remover-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Contribuinte no grupo de recursos |Grupo de recursos de conta de automação|

<sup>1</sup> Os utilizadores não administrador do seu inquilino AZure AD podem [registar aplicações AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) se os Utilizadores do inquilino AZURE **AD puderem registar** a opção de aplicações na página de definições do Utilizador está definida como **Sim**. Se a definição de registo de pedido for **Nº,** o utilizador que executa esta ação deve ser definido nesta tabela.

Se não for membro da instância ative do Diretório da subscrição antes de ser adicionado ao papel de Administrador Global da subscrição, é adicionado como convidado. Nesta situação, recebe um `You do not have permissions to create…` aviso na página **'Adicionar Conta Dem automação'.**

Se for membro da instância Ative Directory da subscrição em que a função de Administrador Global é atribuída, também pode receber um `You do not have permissions to create…` aviso na página **'Adicionar ad automatização'.** Neste caso, pode solicitar a remoção da instância ative do Diretório da subscrição e, em seguida, solicitar a re-adição, para que se torne um utilizador completo no Ative Directory.

Para verificar se a situação que produz a mensagem de erro foi corrigida:

1. A partir do painel de diretório ativo Azure no portal Azure, selecione **Utilizadores e grupos**.
2. Selecione **Todos os utilizadores**.
3. Escolha o seu nome e, em seguida, selecione **Perfil**.
4. Certifique-se de que o valor do atributo **do tipo Utilizador** no perfil do utilizador não está definido para o **Convidado**.

### <a name="permissions-required-to-create-or-manage-classic-run-as-accounts"></a><a name="permissions-classic"></a>Permissões necessárias para criar ou gerir o Classic Run Como contas

Para configurar ou renovar a Classic Run As contas, tem de ter a função de Coadministrador ao nível da subscrição. Para saber mais sobre permissões clássicas de subscrição, consulte os [administradores de subscrição clássicos da Azure.](../role-based-access-control/classic-administrators.md#add-a-co-administrator)

## <a name="create-a-run-as-account-in-azure-portal"></a>Criar uma conta Run As no portal Azure

Execute os seguintes passos para atualizar a sua conta Azure Automation no portal Azure. Crie a Run As e Classic Run As Como contas individualmente. Se não precisar de gerir os recursos clássicos, pode simplesmente criar a conta Run As do Azure.

1. Inicie sessão no portal do Azure com uma conta que seja membro da função de Administradores da Subscrição e o coadministrador da subscrição.

2. Procure e selecione **Contas de Automação.**

3. Na página 'Contas de Automação', selecione a sua conta Demôm automação na lista.

4. No painel esquerdo, selecione **Executar como contas** na secção **Definições de Conta.**

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Selecione a opção 'Executar como conta'.":::

5. Dependendo da conta que necessita, utilize o **painel + Azure Run As Account** ou + **Azure Classic Run As Account.** Depois de rever as informações de visão geral, clique em **Criar**.

    :::image type="content" source="media/manage-runas-account/automation-account-create-runas.png" alt-text="Selecione a opção 'Executar como conta'.":::

6. Enquanto o Azure cria a conta Run As, pode acompanhar o progresso em **Notificações** a partir do menu. Também é exibido um banner indicando que a conta está a ser criada. O processo pode levar alguns minutos para ser concluído.

## <a name="create-a-run-as-account-using-powershell"></a>Criar uma conta Run As usando PowerShell

A lista a seguir fornece os requisitos para criar uma conta Run As no PowerShell utilizando um script fornecido. Estes requisitos aplicam-se a ambos os tipos de contas Run As.

* Windows 10 ou Windows Server 2016 com módulos Azure Resource Manager 3.4.1 e posterior. O script PowerShell não suporta versões anteriores do Windows.
* Azure PowerShell PowerShell 6.2.4 ou mais tarde. Para obter informações, consulte [como instalar e configurar a Azure PowerShell](/powershell/azure/install-az-ps).
* Uma conta de Automação, que é referenciada como o valor para os `AutomationAccountName` parâmetros e `ApplicationDisplayName` parâmetros.
* Permissões equivalentes às listadas nas [permissões necessárias para configurar run As contas](#permissions).

Para obter os valores para `AutomationAccountName` , e , que são `SubscriptionId` `ResourceGroupName` necessários parâmetros para o script PowerShell, completar os seguintes passos.

1. No portal Azure, selecione **Contas de Automação.**

1. Na página 'Contas de Automação', selecione a sua conta Demôm automação.

1. Na secção de definições de conta, selecione **Propriedades**.

1. Note os valores **de Nome,** **ID de assinatura**e Grupo de **Recursos** na página **Propriedades.**

   ![Página de propriedades de conta de automação](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Script PowerShell para criar uma conta Run As

O script PowerShell inclui suporte para várias configurações.

* Utilizar um certificado autoassinado para criar uma conta Run As.
* Utilizar um certificado autoassinado para criar uma conta Run As e uma conta Run As Clássica.
* Criar uma conta Run As e uma conta Run As Clássica mediante a utilização de um certificado emitido pela sua autoridade de certificação empresarial (AC).
* Utilizar um certificado autoassinado na cloud do Azure Government para criar uma conta Run As e uma conta Run As Clássica.

1. Faça o download e guarde o script para uma pasta local utilizando o seguinte comando.

    ```powershell
    wget https://raw.githubusercontent.com/azureautomation/runbooks/master/Utility/AzRunAs/Create-RunAsAccount.ps1 -outfile Create-RunAsAccount.ps1
    ```

2. Inicie o PowerShell com direitos de utilizador elevados e navegue para a pasta que contém o script.

3. Executar um dos seguintes comandos para criar uma conta Run As e/ou Classic Run As com base nos seus requisitos.

    * Criar uma conta Run As utilizando um certificado auto-assinado.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
        ```

    * Utilizar um certificado autoassinado para criar uma conta Run As e uma conta Run As Clássica.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
        ```

    * Utilizar um certificado empresarial para criar uma conta Run As e uma conta Run As Clássica.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
        ```

        Se criou uma conta Classic Run As com um certificado público da empresa (.arquivo cer), use este certificado. O script cria e guarda-o na pasta de ficheiros temporários do seu computador, sob o perfil de utilizador `%USERPROFILE%\AppData\Local\Temp` utilizado para executar a sessão PowerShell. Consulte [o upload de um certificado de API de gestão para o portal Azure](../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

    * Crie uma conta Run As e uma conta Classic Run As usando um certificado auto-assinado na nuvem do Governo Azure

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnvironmentName AzureUSGovernment
        ```

4. Depois do guião ter sido executado, é-lhe pedido que autentica com o Azure. Inscreva-se com uma conta que é membro da função de administrador de subscrição. Se estiver a criar uma conta Classic Run As, a sua conta deve ser coadministradora da subscrição.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Eliminar uma conta Run As ou Run As Clássica

Esta secção descreve como eliminar uma conta Run As ou Classic Run As. Quando executar esta ação, a conta de Automatização é mantida. Depois de eliminar a conta Run As, pode recodê-la no portal Azure ou com o script PowerShell fornecido.

1. No portal do Azure, abra a conta de Automatização.

2. No painel esquerdo, selecione **Executar Como Contas** na secção de definições de conta.

3. Nas propriedades Contas Run As, selecione a conta Run As ou a conta Run As Clássica que quer eliminar.

4. No painel propriedades para a conta selecionada, clique em **Eliminar**.

   ![Eliminar a conta Run As](media/manage-runas-account/automation-account-delete-runas.png)

5. Enquanto a conta estiver a ser eliminada, pode acompanhar o progresso em **Notificações** a partir do menu.

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

Para controlar a segmentação da Automação contra recursos em Azure, pode executar o [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) script. Este script altera o seu run como principal do serviço de conta existente para criar e usar uma definição de função personalizada. O papel tem permissões para todos os recursos, exceto [o Key Vault.](../key-vault/index.yml)

>[!IMPORTANT]
>Depois de executar o script **Update-AutomationRunAsAccountRoleAssignments.ps1, ** os livros que acedem ao Key Vault através da utilização de Run As contas já não funcionam. Antes de executar o script, deve rever os runbooks na sua conta para chamadas para Azure Key Vault. Para permitir o acesso ao Key Vault a partir de runbooks da Azure Automation, tem de [adicionar a conta Run As às permissões do Key Vault](#add-permissions-to-key-vault).

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

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Selecione a opção 'Executar como conta'." lightbox="media/manage-runas-account/verify-role-expanded.png":::

Também pode determinar a definição de função utilizada pelas contas Run As para várias subscrições ou contas de Automação. Faça-o utilizando [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) oCheck-AutomationRunAsAccountRoleAssignments.ps1script na PowerShell Gallery.

### <a name="add-permissions-to-key-vault"></a>Adicionar permissões ao Cofre de Chaves

Pode permitir que a Azure Automation verifique se o Key Vault e o seu responsável pelo serviço de conta Run As estão a utilizar uma definição de função personalizada. Tens de o fazer:

* Conceda permissões ao Key Vault.
* Desa parte da política de acesso.

Pode utilizar [ o ](https://aka.ms/AA5hugb)Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1script na PowerShell Gallery para dar a sua execução como permissões de conta para Key Vault. Consulte [a política de acesso do Cofre de Chaves](/azure/key-vault/general/assign-access-policy-powershell) para obter mais detalhes sobre a definição de permissões no Key Vault.

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Resolver problemas de configuração errada para run As accounts

Alguns itens de configuração necessários para uma execução como ou corrida clássica Como a conta pode ter sido eliminada ou criada indevidamente durante a configuração inicial. Possíveis casos de má configuração incluem:

* Recurso de certificado
* Recurso de ligação
* Executar Como conta removida da função contribuinte
* Principal de serviço ou aplicação no Azure AD

Para tais casos de configuração errada, a conta Automation deteta as alterações e apresenta um estado de *Incompleto* no painel de propriedades run As accounts para a conta.

![Estado de configuração Não concluída da conta Run As](media/manage-runas-account/automation-account-runas-config-incomplete.png)

Quando seleciona a conta Run As, o painel de propriedades da conta apresenta a seguinte mensagem de erro:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Pode resolver rapidamente estes problemas de conta run como eliminando e recriando a conta Run As.

## <a name="next-steps"></a>Passos seguintes

* [Objetos de aplicação e objetos principais de serviço.](../active-directory/develop/app-objects-and-service-principals.md)
* [Visão geral dos certificados para serviços de nuvem Azure](../cloud-services/cloud-services-certs-create.md).
