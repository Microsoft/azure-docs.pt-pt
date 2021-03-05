---
title: Criar uma execução de automação Azure Como conta
description: Este artigo diz como criar uma conta Run As com PowerShell ou a partir do portal Azure.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: ef6afff30da48b79b42e5fb4b3c72c3500f22dd1
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172308"
---
# <a name="how-to-create-an-azure-automation-run-as-account"></a>Como criar uma Azure Automation Run As account

Executar Como contas na Azure Automation fornecem autenticação para gerir recursos no Azure Resource Manager ou no modelo de implementação Azure Classic utilizando runbooks automation e outras funcionalidades de Automação. Este artigo descreve como criar uma conta Run As ou Classic Run Como a partir do portal Azure ou Azure PowerShell.

## <a name="create-account-in-azure-portal"></a>Criar conta no portal Azure

Execute os seguintes passos para atualizar a sua conta Azure Automation no portal Azure. As contas Run As e Classic Run As são criadas separadamente. Se não precisar de gerir os recursos clássicos, pode simplesmente criar a conta Run As do Azure.

1. Inicie sessão no portal do Azure com uma conta que seja membro da função Administradores da Subscrição e coadministrador da subscrição.

2. Procure e selecione **Contas de Automação.**

3. Na página **'Contas de Automação',** selecione a sua conta Demôm automação na lista.

4. No painel esquerdo, selecione **Executar como contas** na secção **Definições de Conta.**

    :::image type="content" source="media/create-run-as-account/automation-account-properties-pane.png" alt-text="Selecione a opção 'Executar como conta'.":::

5. Dependendo da conta que necessita, utilize o **painel + Azure Run As Account** ou + **Azure Classic Run As Account.** Depois de rever as informações de visão geral, clique em **Criar**.

    :::image type="content" source="media/create-run-as-account/automation-account-create-run-as.png" alt-text="Selecione a opção de criar uma Conta Como Executo":::

6. Enquanto o Azure cria a conta Run As, pode acompanhar o progresso em **Notificações** a partir do menu. Também é exibido um banner indicando que a conta está a ser criada. O processo pode levar alguns minutos para ser concluído.

## <a name="create-account-using-powershell"></a>Criar conta usando PowerShell

A lista a seguir fornece os requisitos para criar uma conta Run As no PowerShell utilizando um script fornecido. Estes requisitos aplicam-se a ambos os tipos de contas Run As.

* Windows 10 ou Windows Server 2016 com módulos Azure Resource Manager 3.4.1 e posterior. O script PowerShell não suporta versões anteriores do Windows.
* Azure PowerShell PowerShell 6.2.4 ou mais tarde. Para obter informações, consulte [como instalar e configurar a Azure PowerShell](/powershell/azure/install-az-ps).
* Uma conta de Automação, que é referenciada como o valor para os `AutomationAccountName` parâmetros e `ApplicationDisplayName` parâmetros.
* Permissões equivalentes às listadas nas [permissões necessárias para configurar run As contas](automation-security-overview.md#permissions).

Para obter os valores para `AutomationAccountName` , e , que são `SubscriptionId` `ResourceGroupName` necessários parâmetros para o script PowerShell, completar os seguintes passos.

1. Inicie sessão no Portal do Azure.

1. Procure e selecione **Contas de Automação.**

1. Na página 'Contas de Automação', selecione a sua conta Demôm automação na lista.

1. No painel esquerdo, selecione **Propriedades.**

1. Note os valores **de Nome,** **ID de assinatura** e Grupo de **Recursos** na página **Propriedades.**

   ![Página de propriedades de conta de automação](media/create-run-as-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Script PowerShell para criar uma conta Run As

O script PowerShell inclui suporte para várias configurações.

* Utilizar um certificado autoassinado para criar uma conta Run As.
* Crie uma conta Run As e/ou uma conta Classic Run As utilizando um certificado auto-assinado.
* Crie uma conta Run As e/ou uma conta Classic Run As utilizando um certificado emitido pela sua autoridade de certificação empresarial (CA).
* Crie uma conta Run As e/ou uma conta Classic Run As utilizando um certificado auto-assinado na nuvem do Governo Azure.

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

        Se criou uma conta Classic Run As com um certificado público da empresa (.cer ficheiro), utilize este certificado. O script cria e guarda-o na pasta de ficheiros temporários do seu computador, sob o perfil de utilizador `%USERPROFILE%\AppData\Local\Temp` utilizado para executar a sessão PowerShell. Consulte [o upload de um certificado de API de gestão para o portal Azure](../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

    * Crie uma conta Run As e uma conta Classic Run As usando um certificado auto-assinado na nuvem do Governo Azure

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnvironmentName AzureUSGovernment
        ```

4. Depois do guião ter sido executado, é-lhe pedido que autentica com o Azure. Inscreva-se com uma conta que é membro da função de administrador de subscrição. Se estiver a criar uma conta Classic Run As, a sua conta deve ser coadministradora da subscrição.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a autoria gráfica, consulte [os livros gráficos do Autor na Azure Automation.](automation-graphical-authoring-intro.md)
* Para começar com os livros powerShell, consulte [Tutorial: Crie um livro de execução PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Para começar com um livro de bordo Python 3, consulte [Tutorial: Crie um livro de bordo Python 3](learn/automation-tutorial-runbook-textual-python-3.md).
