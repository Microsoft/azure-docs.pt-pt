---
title: Utilizar o Azure AD na Automatização do Azure para autenticar para o Azure
description: Este artigo diz como utilizar a Azure AD dentro da Azure Automation como fornecedor de autenticação para a Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: c17c9cdc02c87037a39b8d6029bc4506afa8ad28
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87064383"
---
# <a name="use-azure-ad-to-authenticate-to-azure"></a>Utilizar o Azure AD para autenticar no Azure

O serviço [Azure Ative Directory (AD)](../active-directory/fundamentals/active-directory-whatis.md) permite uma série de tarefas administrativas, tais como gestão de utilizadores, gestão de domínios e configuração única de inscrição. Este artigo descreve como utilizar o Azure AD dentro da Azure Automation como fornecedor de autenticação ao Azure. 

## <a name="install-azure-ad-modules"></a>Instalar módulos AD AZure

Pode ativar o AZure AD através dos seguintes módulos PowerShell:

* Azure Ative Directory PowerShell para Graph (módulos AzureRM e Az). A Azure Automation envia com o módulo AzureRM e a sua recente atualização, o módulo Az. A funcionalidade inclui a autenticação não interativa para o Azure utilizando a autenticação baseada em credenciais do utilizador Azure AD (OrgId). Ver [Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Ative Directory for Windows PowerShell (módulo MSOnline). Este módulo permite interações com o Microsoft Online, incluindo o Office 365.

>[!NOTE]
>O PowerShell Core não suporta o módulo MSOnline. Para utilizar os cmdlets do módulo, tem de os executar a partir do Windows PowerShell. Você é encorajado a usar o mais recente Azure Ative Directory PowerShell para módulos Graph em vez do módulo MSOnline. 

### <a name="preinstallation"></a>Pré-instalação

Antes de instalar os módulos AD AZure no seu computador:

* Desinstale quaisquer versões anteriores do módulo AzureRM/Az e do módulo MSOnline. 

* Desinstale o Assistente de Inscrição dos Serviços Online da Microsoft para garantir o correto funcionamento dos novos módulos PowerShell.  

### <a name="install-the-azurerm-and-az-modules"></a>Instalar os módulos AzureRM e Az

>[!NOTE]
>Para trabalhar com estes módulos, tem de utilizar a versão 5.1 do PowerShell ou posteriormente com uma versão de 64 bits do Windows. 

1. Instalar o Quadro de Gestão do Windows (WMF) 5.1. Consulte [a Instalação e Configurar o WMF 5.1](/powershell/scripting/wmf/setup/install-configure?view=powershell-7).

2. Instale o AzureRM e/ou Az utilizando instruções no [Install Azure PowerShell no Windows com o PowerShellGet](/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0).

### <a name="install-the-msonline-module"></a>Instale o módulo MSOnline

>[!NOTE]
>Para instalar o módulo MSOnline, tem de ser membro de uma função de administração do Office 365. Ver [Sobre papéis de administração.](/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide)

1. Certifique-se de que a função Microsoft .NET Framework 3.5.x está ativada no seu computador. É provável que o seu computador tenha uma versão mais recente instalada, mas a compatibilidade para trás com versões mais antigas do Quadro .NET pode ser ativada ou desativada. 

2. Instale a versão de 64 bits do [Assistente de Serção de Serviços Online da Microsoft](https://www.microsoft.com/download/details.aspx?id=41950).

3. Executar o Windows PowerShell como administrador para criar uma solicitação de comando do Windows PowerShell elevada.

4. Implementar o Azure Ative Directory a partir de [MSOnline 1.0](http://www.powershellgallery.com/packages/MSOnline/1.0).

5. Se for solicitado para instalar o fornecedor NuGet, escreva Y e prima ENTER.

6. Se tiver sido solicitado para instalar o módulo da [PSGallery,](https://www.powershellgallery.com/)escreva Y e prima ENTER.

### <a name="install-support-for-pscredential"></a>Instalar suporte para PSCredential

A Azure Automation utiliza a classe [PSCredential](/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) para representar um ativo credencial. Os seus scripts recuperam `PSCredential` objetos utilizando o `Get-AutomationPSCredential` cmdlet. Para mais informações, consulte [os ativos da Credencial na Azure Automation.](shared-resources/credentials.md)

## <a name="assign-a-subscription-administrator"></a>Atribuir um administrador de subscrição

Tem de atribuir um administrador para a assinatura Azure. Esta pessoa tem o papel de Proprietário para o âmbito de subscrição. Consulte [o controlo de acesso baseado em funções na Azure Automation](automation-role-based-access-control.md). 

## <a name="change-the-azure-ad-users-password"></a>Alterar a senha do utilizador AZure AD

Para alterar a palavra-passe do utilizador Azure AD:

1. Sai de Azure.

2. Faça o administrador iniciar sessão no Azure como o utilizador AZure AD acaba de ser criado, utilizando o nome de utilizador completo (incluindo o domínio) e uma senha temporária. 

3. Peça ao administrador para alterar a palavra-passe quando solicitado.

## <a name="configure-azure-automation-to-manage-the-azure-subscription"></a>Configure Azure Automation para gerir a subscrição do Azure

Para que a Azure Automation comunique com a Azure AD, deve recuperar as credenciais associadas à ligação Azure a AD. Exemplos destas credenciais são iD de inquilino, ID de assinatura, e similares. Para mais informações sobre a ligação entre a Azure e a Azure AD, consulte [Connect your organization to Azure Ative Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops).

## <a name="create-a-credential-asset"></a>Criar um ativo credencial

Com as credenciais Azure para Azure AD disponíveis, é hora de criar um ativo credencial Azure Automation para armazenar de forma segura as credenciais AD AZure para que os scripts de runbooks e Desire State Configuration (DSC) possam aceder-lhes. Pode fazê-lo utilizando o portal Azure ou os cmdlets PowerShell.

### <a name="create-the-credential-asset-in-azure-portal"></a>Criar o ativo credencial no portal Azure

Pode utilizar o portal Azure para criar o ativo credencial. Faça esta operação a partir da sua conta de Automação utilizando **credenciais** em **Recursos Partilhados.** Consulte [os ativos credenciais na Azure Automation](shared-resources/credentials.md).

### <a name="create-the-credential-asset-with-windows-powershell"></a>Crie o ativo credencial com o Windows PowerShell

Para preparar um novo ativo credencial no Windows PowerShell, o seu script cria primeiro um `PSCredential` objeto utilizando o nome de utilizador e a palavra-passe atribuídos. O script usa então este objeto para criar o ativo através de uma chamada para o cmdlet [New-AzureAutomationCredential.](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential?view=azuresmps-4.0.0) Em alternativa, o script pode chamar o [cmdlet Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) para levar o utilizador a escrever um nome e uma palavra-passe. Consulte [os ativos credenciais na Azure Automation](shared-resources/credentials.md). 

## <a name="manage-azure-resources-from-an-azure-automation-runbook"></a>Gerir os recursos da Azure a partir de um runbook da Azure Automation

Pode gerir os recursos da Azure Automation através do ativo credencial. Abaixo está um livro de aplicação powerShell que recolhe o ativo credencial a utilizar para parar e iniciar máquinas virtuais numa subscrição do Azure. Este livro de bordo utiliza pela primeira vez `Get-AutomationPSCredential` para recuperar a credencial para utilizar para autenticar a Azure. Em seguida, chama o [cmdlet Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) para ligar ao Azure usando a credencial. O script utiliza o [cmdlet Select-AzureSubscription](/powershell/module/servicemanagement/azure.service/select-azuresubscription?view=azuresmps-4.0.0) para escolher a subscrição para trabalhar. 

```azurepowershell
Workflow Stop-Start-AzureVM 
{ 
    Param 
    (    
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureSubscriptionId, 
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureVMList="All", 
        [Parameter(Mandatory=$true)][ValidateSet("Start","Stop")] 
        [String] 
        $Action 
    ) 
     
    $credential = Get-AutomationPSCredential -Name 'AzureCredential' 
    Connect-AzAccount -Credential $credential 
    Select-AzureSubscription -SubscriptionId $AzureSubscriptionId 
 
    if($AzureVMList -ne "All") 
    { 
        $AzureVMs = $AzureVMList.Split(",") 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
    else 
    { 
        $AzureVMs = (Get-AzVM).Name 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
 
    } 
 
    foreach($AzureVM in $AzureVMsToHandle) 
    { 
        if(!(Get-AzVM | ? {$_.Name -eq $AzureVM})) 
        { 
            throw " AzureVM : [$AzureVM] - Does not exist! - Check your inputs " 
        } 
    } 
 
    if($Action -eq "Stop") 
    { 
        Write-Output "Stopping VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Stop-AzVM -Force 
        } 
    } 
    else 
    { 
        Write-Output "Starting VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Start-AzVM 
        } 
    } 
}
```  

## <a name="next-steps"></a>Passos seguintes

* Para obter detalhes sobre a utilização credencial, consulte [Gerir as credenciais na Azure Automation.](shared-resources/credentials.md)
* Para obter informações sobre módulos, consulte [Gerir os módulos na Azure Automation.](shared-resources/modules.md)
* Se precisar de iniciar um livro de execução, consulte [Iniciar um livro de recortes na Azure Automation](start-runbooks.md).
* Para obter detalhes da PowerShell, consulte [o PowerShell Docs](/powershell/scripting/overview).
