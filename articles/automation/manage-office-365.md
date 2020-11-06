---
title: Gerir os serviços do Office 365 com a Automatização do Azure
description: Este artigo diz como utilizar a Azure Automation para gerir os serviços de subscrição do Office 365.
services: automation
ms.date: 11/05/2020
ms.topic: conceptual
ms.openlocfilehash: 70c8892969a3b13175c60a4e20e0cf9086112abe
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398050"
---
# <a name="manage-office-365-services"></a>Gerir os serviços do Office 365

Pode utilizar a Azure Automation para a gestão dos serviços de subscrição do Office 365, para produtos como o Microsoft Word e o Microsoft Outlook. As interações com o Office 365 são ativadas pelo [Azure Ative Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md). Consulte [a Utilização Azure AD na Azure Automation para autenticar até a Azure](automation-use-azure-ad.md).

## <a name="prerequisites"></a>Pré-requisitos

Precisa do seguinte para gerir os serviços de subscrição do Office 365 na Azure Automation.

* Uma subscrição do Azure. Consulte o [guia de decisão da subscrição.](/azure/cloud-adoption-framework/decision-guides/subscriptions/)
* Um objeto automation em Azure para guardar as credenciais e os livros de aplicação da conta do utilizador. Ver [Uma introdução à Azure Automation](./automation-intro.md).
* Azure AD. Consulte [a Utilização Azure AD na Azure Automation para autenticar até a Azure](automation-use-azure-ad.md).
* Um inquilino do Escritório 365, com uma conta. Consulte [o seu escritório 365 inquilino.](/sharepoint/dev/spfx/set-up-your-developer-tenant)

## <a name="install-the-msonline-and-msonlineext-modules"></a>Instale os módulos MSOnline e MSOnlineExt

A utilização do Office 365 dentro da Azure Automation requer o Microsoft Azure Ative Directory para o Windows PowerShell `MSOnline` (módulo). Você também vai precisar do módulo [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35) , que simplifica a gestão AD AD em ambientes individuais e multi-inquilinos. Instale os módulos conforme descrito na [Utilização Azure AD na Azure Automation para autenticar até a Azure](automation-use-azure-ad.md).

>[!NOTE]
>Para utilizar o MSOnline PowerShell, tem de ser membro da Azure AD. Os utilizadores convidados não podem usar o módulo.

## <a name="create-an-azure-automation-account"></a>Crie uma conta de Automatização do Azure

Para completar os passos deste artigo, precisa de uma conta na Azure Automation. Ver [Criar uma conta Azure Automation](automation-quickstart-create-account.md).
 
## <a name="add-msonline-and-msonlineext-as-assets"></a>Adicione MSOnline e MSOnlineExt como ativos

Agora adicione os módulos MSOnline e MSOnlineExt instalados para ativar a funcionalidade do Office 365. Consulte para [Gerir módulos na Azure Automation](shared-resources/modules.md).

1. No portal Azure, selecione **Contas de Automação.**
2. Escolha a sua conta Demôm automação.
3. Selecione **Galeria de Módulos** em **Recursos Partilhados.**
4. Procure por MSOnline.
5. Selecione o `MSOnline` módulo PowerShell e clique em **Importar** o módulo como ativo.
6. Repita os passos 4 e 5 para localizar e importar o `MSOnlineExt` módulo.

## <a name="create-a-credential-asset-optional"></a>Criar um ativo credencial (opcional)

É opcional criar um ativo credencial para o utilizador administrativo do Office 365 que tenha permissões para executar o seu script. Pode, no entanto, ajudar a evitar expor nomes de utilizadores e palavras-passe dentro dos scripts PowerShell. Para obter instruções, consulte [Criar um ativo credencial](automation-use-azure-ad.md#create-a-credential-asset).

## <a name="create-an-office-365-service-account"></a>Criar uma conta de serviço office 365

Para executar os serviços de subscrição do Office 365, precisa de uma conta de serviço office 365 com permissões para fazer o que quiser. Pode utilizar uma conta de administrador global, uma conta por serviço, ou ter uma função ou script para executar. Em qualquer caso, a conta de serviço requer uma senha complexa e segura. Consulte [o Escritório 365 para negócios](/microsoft-365/admin/setup/setup).

## <a name="connect-to-the-azure-ad-online-service"></a>Ligue-se ao serviço online Azure AD

>[!NOTE]
>Para utilizar os cmdlets do módulo MSOnline, tem de os executar a partir do Windows PowerShell. PowerShell Core não suporta estes cmdlets.

Pode utilizar o módulo MSOnline para ligar ao Azure AD a partir da assinatura Office 365. A ligação utiliza um nome de utilizador e palavra-passe do Office 365 ou utiliza a autenticação de vários fatores (MFA). Pode ligar-se utilizando o portal Azure ou uma pressão de comando Do Windows PowerShell (não tem de ser elevada).

Um exemplo PowerShell é mostrado abaixo. O [cmdlet Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) solicita credenciais e armazena-as na `Msolcred` variável. Em [seguida, o cmdlet Connect-MsolService](/powershell/module/msonline/connect-msolservice) utiliza as credenciais para ligar ao serviço online do diretório Azure. Se quiser ligar-se a um ambiente Azure específico, utilize o `AzureEnvironment` parâmetro.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Se não receber erros, ligou-se com sucesso. Um teste rápido é executar um Office 365 cmdlet, por exemplo, `Get-MsolUser` e ver os resultados. Se receber erros, note que um problema comum é uma palavra-passe incorreta.

>[!NOTE]
>Também pode utilizar o módulo AzureRM ou o módulo Az para ligar ao Azure AD a partir da assinatura Do Office 365. O cmdlet de ligação principal é [Connect-AzureAD](/powershell/module/azuread/connect-azuread). Este cmdlet suporta o `AzureEnvironmentName` parâmetro para ambientes específicos do Office 365.

## <a name="create-a-powershell-runbook-from-an-existing-script"></a>Crie um livro de corrida PowerShell a partir de um script existente

Acede à funcionalidade do Office 365 a partir de um script PowerShell. Aqui está um exemplo de um script para uma credencial chamada `Office-Credentials` com nome de utilizador de `admin@TenantOne.com` . `Get-AutomationPSCredential`Utiliza-se para importar a credencial do Office 365.

```powershell
$emailFromAddress = "admin@TenantOne.com"
$emailToAddress = "servicedesk@TenantOne.com"
$emailSMTPServer = "outlook.office365.com"
$emailSubject = "Office 365 License Report"

$credObject = Get-AutomationPSCredential -Name "Office-Credentials"
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body $O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="run-the-script-in-a-runbook"></a>Executar o script em um livro de corridas

Pode utilizar o seu script num livro de regras da Azure Automation. Por exemplo, usaremos o tipo de livro powerShell.

1. Crie um novo livro de execução PowerShell. Consulte para [criar um livro de bordo da Azure Automation](./automation-quickstart-create-runbook.md).
2. Na sua conta de Automação, selecione **Runbooks** em **Automação de Processos.**
3. Selecione o novo runbook e clique em **Editar.**
4. Copie o seu script e cole-o no editor textual para o livro de bordo.
5. Selecione **ATIVOS,** em seguida, **expanda as credenciais** e verifique se a credencial do Office 365 está lá.
6. Clique em **Guardar**.
7. Selecione **Test pane** e, em seguida, clique **em Iniciar** para começar a testar o seu runbook. Consulte [os runbooks da Azure Automation](./manage-runbooks.md).
8. Quando o teste estiver concluído, saia do painel de teste.

## <a name="publish-and-schedule-the-runbook"></a>Publicar e agendar o livro de corridas

Para publicar e, em seguida, agendar o seu runbook, consulte [Gerir os runbooks na Azure Automation](./manage-runbooks.md).

## <a name="next-steps"></a>Passos seguintes

* Para obter detalhes sobre a utilização credencial, consulte [Gerir as credenciais na Azure Automation.](shared-resources/credentials.md)
* Para obter informações sobre módulos, consulte [Gerir os módulos na Azure Automation.](shared-resources/modules.md)
* Se precisar de iniciar um livro de execução, consulte [Iniciar um livro de recortes na Azure Automation](start-runbooks.md).
* Para obter detalhes da PowerShell, consulte [o PowerShell Docs](/powershell/scripting/overview).
