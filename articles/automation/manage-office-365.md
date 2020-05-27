---
title: Gerir os serviços do Office 365 com a Automatização do Azure
description: Este artigo diz como usar a Azure Automation para gerir os serviços de subscrição do Office 365.
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 322e2a3679ed29ab9ecc4cdc3c6e1fe4d0f20276
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83831177"
---
# <a name="manage-office-365-services"></a>Gerir os serviços do Office 365

Pode utilizar o Azure Automation para a gestão de serviços de subscrição do Office 365, para produtos como o Microsoft Word e o Microsoft Outlook. As interações com o Office 365 são ativadas pelo [Azure Ative Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Consulte [a Utilização de AD Azure na Automação Azure para autenticar o Azure](automation-use-azure-ad.md).

## <a name="prerequisites"></a>Pré-requisitos

Precisa do seguinte para gerir os serviços de subscrição do Office 365 na Azure Automation.

* Uma subscrição do Azure. Consulte [o guia de decisão de subscrição](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Um objeto de Automação em Azure para manter as credenciais de conta de utilizador e livros de execução. Consulte [uma introdução à Automação Azure.](https://docs.microsoft.com/azure/automation/automation-intro)
* Azure AD. Consulte [a Utilização de AD Azure na Automação Azure para autenticar o Azure](automation-use-azure-ad.md).
* Um inquilino do Escritório 365, com uma conta. Consulte [a instalação do seu Escritório 365 inquilino.](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant)

## <a name="install-the-msonline-and-msonlineext-modules"></a>Instale os módulos MSOnline e MSOnlineExt

A utilização do Office 365 no Azure Automation requer o Microsoft Azure Ative Directory para windows PowerShell `MSOnline` (módulo). Você também vai precisar do módulo , que simplifica a [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35) gestão da AD Azure em ambientes de um único e multi-inquilinos. Instale os módulos descritos no [AD Azure em Automação Azure para autenticar o Azure](automation-use-azure-ad.md).

>[!NOTE]
>Para utilizar o MSOnline PowerShell, deve ser membro da Azure AD. Os utilizadores não podem usar o módulo.

## <a name="create-an-azure-automation-account"></a>Crie uma conta de Automatização do Azure

Para completar os passos deste artigo, precisa de uma conta na Azure Automation. Ver [Criar uma conta De Automação Azure](automation-quickstart-create-account.md).
 
## <a name="add-msonline-and-msonlineext-as-assets"></a>Adicione MSOnline e MSOnlineExt como ativos

Adicione agora os módulos MSOnline e MSOnlineExt instalados para ativar a funcionalidade Office 365. Consulte a Gestão de [módulos em Automação Azure.](shared-resources/modules.md)

1. No portal Azure, selecione **Contas de Automação.**
2. Escolha a sua conta de Automação.
3. Selecione **Galeria de Módulos** em **Recursos Partilhados.**
4. Procure mSOnline.
5. Selecione o `MSOnline` módulo PowerShell e clique **em Importar** para importar o módulo como um ativo.
6. Repita os passos 4 e 5 para localizar e importar o `MSOnlineExt` módulo. 

## <a name="create-a-credential-asset-optional"></a>Criar um ativo credencial (opcional)

É opcional criar um ativo credencial para o utilizador administrativo do Office 365 que tem permissões para executar o seu script. Pode, no entanto, ajudar a evitar expor nomes de utilizadores e palavras-passe dentro dos scripts PowerShell. Para obter instruções, consulte [Criar um ativo credencial](automation-use-azure-ad.md#create-a-credential-asset).

## <a name="create-an-office-365-service-account"></a>Criar uma conta de serviço do Office 365

Para executar os serviços de subscrição do Office 365, precisa de uma conta de serviço office 365 com permissões para fazer o que quiser. Pode utilizar uma conta de administrador global, uma conta por serviço, ou ter uma função ou script para executar. Em todo o caso, a conta de serviço requer uma senha complexa e segura. Consulte a [configuração do Escritório 365 para negócios](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide). 

## <a name="connect-to-the-azure-ad-online-service"></a>Ligue-se ao serviço online Azure AD

>[!NOTE]
>Para utilizar os cmdlets do módulo MSOnline, deve executá-los a partir do Windows PowerShell. O PowerShell Core não suporta estes cmdlets.

Pode utilizar o módulo MSOnline para ligar ao Azure AD a partir da subscrição do Office 365. A ligação utiliza um nome de utilizador do Office 365 e uma palavra-passe ou utiliza a autenticação de vários fatores (MFA). Pode ligar-se utilizando o portal Azure ou um pedido de comando Windows PowerShell (não tem de ser elevado).

Um exemplo powerShell é mostrado abaixo. O [cmdlet Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) solicita credenciais e armazena-as na `Msolcred` variável. Em seguida, o cmdlet [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) utiliza as credenciais para ligar ao serviço online de diretório Azure. Se quiser ligar-se a um ambiente Azure específico, utilize o `AzureEnvironment` parâmetro.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Se não receber erros, ligou-se com sucesso. Um teste rápido é executar um Office 365 cmdlet, por exemplo, `Get-MsolUser` e ver os resultados. Se receber erros, note que um problema comum é uma senha incorreta.

>[!NOTE]
>Também pode utilizar o módulo AzureRM ou o módulo Az para ligar ao Azure AD a partir da subscrição do Office 365. O cmdlet de ligação principal é [O Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0). Este cmdlet suporta o `AzureEnvironmentName` parâmetro para ambientes específicos do Office 365.

## <a name="create-a-powershell-runbook-from-an-existing-script"></a>Crie um livro de execução PowerShell a partir de um script existente

Acede à funcionalidade office 365 a partir de um script PowerShell. Aqui está um exemplo de um script para uma credencial com `Office-Credentials` nome de utilizador de `admin@TenantOne.com` . Usa `Get-AutomationPSCredential` para importar a credencial do Office 365.

```powershell
$emailFromAddress = "admin@TenantOne.com" 
$emailToAddress = "servicedesk@TenantOne.com" 
$emailSMTPServer = "outlook.office365.com" 
$emailSubject = "Office 365 License Report" 

$credObject = Get-AutomationPSCredential -Name "Office-Credentials" 
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String 
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body 

$O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="run-the-script-in-a-runbook"></a>Executar o script em um livro de corridas

Pode usar o seu guião num livro de execução da Automação Azure. Por exemplo, usaremos o tipo de livro powerShell.

1. Crie um novo livro de execução powerShell. Consulte a Create a [Azure Automation runbook](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook).
2. Na sua conta de Automação, selecione **Runbooks** em Automação de **Processos**.
3. Selecione o novo livro de execução e clique em **Editar**.
4. Copie o seu guião e cole-o no editor textual para o livro de execução.
5. Selecione **ASSETS,** em seguida, expandir **credenciais** e verificar se a credencial do Office 365 está lá.
6. Clique em **Guardar**.
7. Selecione painel de **teste**e, em seguida, clique **em Começar** a testar o seu livro de execução. Ver [Gerir livros de execução em Automação Azure.](https://docs.microsoft.com/azure/automation/manage-runbooks)
8. Quando os testes estiverem completos, saia do painel de teste.

## <a name="publish-and-schedule-the-runbook"></a>Publicar e agendar o livro de corridas

Para publicar e, em seguida, agendar o seu livro de execução, consulte [Gerir livros de execução em Automação Azure](https://docs.microsoft.com/azure/automation/manage-runbooks).

## <a name="next-steps"></a>Passos seguintes

* Para mais detalhes sobre a utilização da credencial, consulte [Gerir credenciais em Automação Azure](shared-resources/credentials.md).
* Para obter informações sobre módulos, consulte [Gerir módulos em Automação Azure.](shared-resources/modules.md)
* Se precisar de iniciar um livro de corridas, consulte [Iniciar um livro de corridas em Automação Azure](start-runbooks.md).
* Para mais detalhes da PowerShell, consulte [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).