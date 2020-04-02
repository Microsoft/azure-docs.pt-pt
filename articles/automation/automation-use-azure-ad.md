---
title: Use a AD Azure na Automação Azure para autenticar o Azure
description: Saiba como utilizar o Azure AD dentro da Azure Automation como fornecedor para autenticação no Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 90338a1ffa79e6c2347832cb2e74633db02ec72d
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548329"
---
# <a name="use-azure-ad-in-azure-automation-to-authenticate-to-azure"></a>Use a AD Azure na Automação Azure para autenticar o Azure

O serviço [Azure Ative Directory (AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) permite uma série de tarefas administrativas, tais como gestão de utilizadores, gestão de domínios e configuração de inscrição única. Este artigo descreve como usar a Azure AD dentro da Azure Automation como fornecedor para autenticação ao Azure. 

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

## <a name="installing-azure-ad-modules"></a>Instalação de módulos AD Azure

Pode ativar o Azure AD através dos seguintes módulos PowerShell:

* Diretório Ativo Azure PowerShell para gráfico (módulos AzureRM e Az). Naves Azure Automation com o módulo AzureRM e a sua recente atualização, o módulo Az. A funcionalidade inclui a autenticação não interativa para o Azure utilizando a autenticação baseada na credencial do utilizador Azure AD (OrgId). Ver [Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Ative Diretório para Windows PowerShell (módulo MSOnline). Este módulo permite interações com o Microsoft Online, incluindo o Office 365.

>[!NOTE]
>O PowerShell Core não suporta o módulo MSOnline. Para utilizar os cmdlets do módulo, deve executá-los a partir do Windows PowerShell. É encorajado a usar o novo Diretório Ativo Azure PowerShell para módulos gráficos em vez do módulo MSOnline. 

### <a name="preinstallation"></a>Pré-instalação

Antes de instalar os módulos Azure AD no seu computador:

* Desinstale quaisquer versões anteriores do módulo AzureRM/Az e do módulo MSOnline. 

* Desinstale o Assistente de Inscrição de Serviços Online da Microsoft para garantir o correto funcionamento dos novos módulos PowerShell.  

### <a name="install-the-azurerm-and-az-modules"></a>Instale os módulos AzureRM e Az

>[!NOTE]
>Para trabalhar com estes módulos, tem de utilizar a versão 5.1 do PowerShell ou mais tarde com uma versão de 64 bits do Windows. 

1. Instale a Estrutura de Gestão do Windows (WMF) 5.1. Consulte [instalar e configurar o WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure?view=powershell-7).

2. Instale o AzureRM e/ou Az utilizando instruções para instalar o [PowerShell Azure no Windows com o PowerShellGet](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0).

### <a name="install-the-msonline-module"></a>Instale o módulo MSOnline

>[!NOTE]
>Para instalar o módulo MSOnline, deve ser membro de uma função de administrador do Office 365. Ver [sobre papéis de administrador.](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide)

1. Certifique-se de que a função Microsoft .NET Framework 3.5.x está ativada no seu computador. É provável que o seu computador tenha uma versão mais recente instalada, mas a retrocompatibilidade com versões mais antigas do .NET Framework pode ser ativada ou desativada. 

2. Instale a versão de 64 bits do [Assistente de Inscrição de Serviços Online](https://www.microsoft.com/download/details.aspx?id=41950)da Microsoft .

3. Executar o Windows PowerShell como administrador para criar um pedido de comando Elevado Do Windows PowerShell.

4. Implementar o Diretório Ativo Azure a partir do [MSOnline 1.0](http://www.powershellgallery.com/packages/MSOnline/1.0).

5. Se for solicitado a instalação do fornecedor NuGet, escreva Y e prima ENTER.

6. Se for solicitado a instalação do módulo a partir da [PSGallery,](https://www.powershellgallery.com/)escreva Y e prima ENTER.

### <a name="install-support-for-pscredential"></a>Instale suporte para PSCredential

A Azure Automation utiliza a classe [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) para representar um ativo credencial. Os seus `PSCredential` scripts `Get-AutomationPSCredential` recuperam objetos utilizando o cmdlet. Para mais informações, consulte [os ativos credenciais na Azure Automation](shared-resources/credentials.md).

## <a name="assigning-a-subscription-administrator"></a>Atribuição de um administrador de subscrição

Deve designar um administrador para a subscrição do Azure. Esta pessoa tem o papel de Proprietário para o âmbito de subscrição. Ver [controlo de acesso baseado em funções na Automação Azure](automation-role-based-access-control.md). 

## <a name="changing-the-azure-ad-users-password"></a>Alterar a palavra-passe do utilizador da AD Azure

Para alterar a palavra-passe do utilizador da AD Azure:

1. Saia de Azure.

2. Faça o registo do administrador no Azure tal como o utilizador da AD Azure acabou de criar, utilizando o nome de utilizador completo (incluindo o domínio) e uma senha temporária. 

3. Peça ao administrador que altere a palavra-passe quando for solicitada.

## <a name="configuring-azure-automation-to-use-the-azure-ad-user-to-manage-the-azure-subscription"></a>Configurar a Automação Azure para utilizar o utilizador da AD Azure para gerir a subscrição do Azure

Para que a Azure Automation se comunique com a Azure AD, deve recuperar as credenciais associadas à ligação Azure à Azure AD. Exemplos destas credenciais são identificação do inquilino, identificação de subscrição, e similares. Para mais informações sobre a ligação entre a Azure e a Azure AD, consulte [Connect a sua organização ao Azure Ative Directory](https://docs.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops).

## <a name="creating-a-credential-asset"></a>Criar um ativo credencial

Com as credenciais Azure para AD Azure disponíveis, é hora de criar um ativo credencial azure Automation para armazenar de forma segura as credenciais Azure AD para que os scripts de execução e de Configuração do Estado do Desejo (DSC) possam aceder-lhes. Pode fazê-lo utilizando o portal Azure ou os cmdlets PowerShell.

### <a name="create-the-credential-asset-in-azure-portal"></a>Criar o ativo credencial no portal Azure

Pode utilizar o portal Azure para criar o ativo credencial. Faça esta operação a partir da sua conta de Automação utilizando **credenciais** em **recursos partilhados**. Consulte [os ativos credenciais na Automação Azure.](shared-resources/credentials.md)

### <a name="create-the-credential-asset-with-windows-powershell"></a>Criar o ativo credencial com o Windows PowerShell

Para preparar um novo ativo credencial no Windows PowerShell, o seu script cria primeiro um `PSCredential` objeto utilizando o nome de utilizador e a palavra-passe atribuídos. O script usa então este objeto para criar o ativo através de uma chamada para o cmdlet [New-AzureAutomationCredential.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) Em alternativa, o script pode ligar para o cmdlet [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) para levar o utilizador a escrever um nome e senha. Consulte [os ativos credenciais na Automação Azure.](shared-resources/credentials.md) 

## <a name="managing-azure-resources-from-an-azure-automation-runbook"></a>Gerir os recursos da Azure a partir de um livro de execução da Automação Azure

Você pode gerir os recursos Azure a partir de livros de execução Azure Automation usando o ativo credencial. Abaixo está um livro de execução da PowerShell que recolhe o ativo credencial para parar e iniciar máquinas virtuais numa subscrição Azure. Este livro de `Get-AutomationPSCredential` execução usa primeiro para recuperar a credencial para usar para autenticar o Azure. Em seguida, chama o cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) para ligar ao Azure utilizando a credencial. O script utiliza o cmdlet [Select-AzureSubscription](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0) para escolher a subscrição para trabalhar. 

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

* Pode encontrar informações sobre ativos credenciais automation em [ativos credenciais na Automação Azure.](shared-resources/credentials.md)
* Ver [Gerir módulos na Automatização Azure](shared-resources/modules.md) para saber como trabalhar com módulos de Automação.
* Para saber mais sobre os métodos que podem ser usados para iniciar um livro de corridas em Automação Azure, consulte [Iniciar um livro de corridas em Automação Azure.](automation-starting-a-runbook.md)
* Para obter mais informações sobre o PowerShell, incluindo módulos de referência linguística e aprendizagem, consulte os [Docs PowerShell](https://docs.microsoft.com/powershell/scripting/overview).