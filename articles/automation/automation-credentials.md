---
title: Ativos de credencial na automatização do Azure
description: Ativos de credencial na automatização do Azure contêm as credenciais de segurança que podem ser utilizadas para autenticar em recursos acedidos pelo runbook ou configuração DSC. Este artigo descreve como criar recursos de credencial e usá-los num runbook ou configuração DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a842c0807a3cfbad78a43bcffa896c83bceedfb9
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959294"
---
# <a name="credential-assets-in-azure-automation"></a>Ativos de credencial na automatização do Azure

Um recurso de credencial da automatização contém um objeto, que contém credenciais de segurança, como um nome de utilizador e palavra-passe. Configurações de Runbooks e DSC podem utilizar os cmdlets que aceitem um objeto PSCredential para autenticação, ou eles podem extrair o nome de utilizador e palavra-passe do objeto PSCredential para fornecer a alguma aplicação ou serviço que requer autenticação. As propriedades das credenciais são armazenadas em segurança na automatização do Azure e podem ser acedidas no runbook ou configuração DSC com o [Get-AutomationPSCredential](https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) atividade.

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Os elementos seguros na automatização do Azure incluem as credenciais, certificados, ligações e variáveis encriptadas. Esses ativos são encriptados e armazenados na automatização do Azure com uma chave exclusiva que é gerada para cada conta de automatização. Esta chave é armazenada no Key Vault. Antes de armazenar um recurso seguro, a chave é carregada a partir do Key Vault e, em seguida, utilizada para encriptar o elemento.

## <a name="azure-classic-powershell-cmdlets"></a>Cmdlets do Azure PowerShell clássico

Os cmdlets na tabela seguinte são utilizados para criar e gerir recursos de credencial da automatização com o Windows PowerShell.  Eles são fornecidos como parte do [módulo do Azure PowerShell](/powershell/azure/overview), que está disponível para utilização nos runbooks de automatização e configurações de DSC.

| Cmdlets | Descrição |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/servicemanagement/azure/get-azureautomationcredential?view=azuresmps-3.7.0) |Obtém informação sobre um recurso de credencial. Só é possível obter a credencial em si partir **Get-AutomationPSCredential** atividade. |
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Cria uma nova credencial da automatização. |
| [Remove- AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Remove uma credencial da automatização. |
| [Set- AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Define as propriedades de uma credencial da automatização existente. |

## <a name="azurerm-powershell-cmdlets"></a>Cmdlets do AzureRM PowerShell

Para AzureRM, os cmdlets na tabela seguinte são utilizados para criar e gerir recursos de credencial da automatização com o Windows PowerShell.  Eles são fornecidos como parte do [módulo do Azurerm](/powershell/azure/overview), que está disponível para utilização nos runbooks de automatização e configurações de DSC.

| Cmdlets | Descrição |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential?view=azurermps-4.4.0) |Obtém informação sobre um recurso de credencial.  |
| [New-AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential?view=azurermps-4.4.0) |Cria uma nova credencial da automatização. |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential?view=azurermps-4.4.0) |Remove uma credencial da automatização. |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential?view=azurermps-4.4.0) |Define as propriedades de uma credencial da automatização existente. |

## <a name="activities"></a>Atividades

As atividades na tabela seguinte são utilizadas para aceder a credenciais num runbook e configurações de DSC.

| Atividades | Descrição |
|:--- |:--- |
| Get-AutomationPSCredential |Obtém uma credencial que deve utilizar num runbook ou configuração DSC. Devolve um [System.Management.Automation.PSCredential](https://msdn.microsoft.com/library/system.management.automation.pscredential) objeto. |

> [!NOTE]
> Deve evitar utilizar variáveis no parâmetro – Name de Get-AutomationPSCredential, uma vez que isso pode dificultar a deteção de dependências entre runbooks ou configurações de DSC e recursos de credencial em tempo de design.

## <a name="python2-functions"></a>Funções do Python2

A função na tabela seguinte é utilizada para aceder a credenciais num Python2 runbook.

| Função | Descrição |
|:---|:---|
| automationassets.get_automation_credential | Obtém informação sobre um recurso de credencial. |

> [!NOTE]
> Tem de importar o módulo "automationassets" na parte superior do seu runbook de Python para acessar as funções de recurso.

## <a name="creating-a-new-credential-asset"></a>Criar um novo recurso de credencial

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Para criar um novo recurso de credencial com o portal do Azure

1. A partir da sua conta de automatização, selecione **credenciais** sob **recursos partilhados**.
1. Clique em **+ adicionar uma credencial**.
1. Preencha o formulário e clique em **criar** para guardar a nova credencial.

> [!NOTE]
> Contas de utilizador que utilizam a autenticação multifator não são suportadas para utilização na automatização do Azure.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Para criar um novo recurso de credencial com o Windows PowerShell

Os comandos de exemplo seguintes mostram como criar uma nova credencial da automatização. Um objeto PSCredential é criado pela primeira vez com o nome e a palavra-passe e, em seguida, utilizado para criar o recurso de credencial. Em alternativa, pode utilizar o **Get-Credential** cmdlet para ser solicitado a digitar um nome e a palavra-passe.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Utilizar uma credencial do PowerShell

Recupera um recurso de credencial num runbook ou configuração DSC com o **Get-AutomationPSCredential** atividade. Isso retorna um [objeto PSCredential](https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) que pode utilizar com uma atividade ou um cmdlet que requer um parâmetro de PSCredential. Também pode obter as propriedades do objeto de credencial a utilizar individualmente. O objeto tem uma propriedade para o nome de utilizador e a palavra-passe segura, ou pode utilizar o **GetNetworkCredential** método para retornar um [NetworkCredential](https://msdn.microsoft.com/library/system.net.networkcredential.aspx) objeto que fornecerá uma versão não segura dos palavra-passe.

### <a name="textual-runbook-sample"></a>Exemplo de textual runbook

Comandos de exemplo seguintes mostram como utilizar uma credencial do PowerShell num runbook. Neste exemplo, a credencial é recuperada e seu nome de utilizador e palavra-passe atribuídas a variáveis.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Também pode utilizar uma credencial para autenticar no Azure com o [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount). Na maioria das circunstâncias, deve usar uma [conta Run As](manage-runas-account.md) e recuperá-la com [Get-AutomationConnection](automation-connections.md).

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential`
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>Exemplo de runbook gráfico

Adicionar uma **Get-AutomationPSCredential** atividade para um runbook gráfico clicando na credencial no painel de biblioteca do editor gráfico e selecionando **adicionar à tela**.

![Adicionar credencial à tela](media/automation-credentials/credential-add-canvas.png)

A imagem seguinte mostra um exemplo de como utilizar uma credencial num runbook gráfico.  Neste caso, está a ser utilizado para fornecer autenticação para um runbook para recursos do Azure, conforme descrito em [autenticar Runbooks com a conta de utilizador do Azure AD](automation-create-aduser-account.md).  A primeira atividade obtém a credencial que tem acesso à subscrição do Azure.  O **Add-AzureAccount** atividade, em seguida, utiliza esta credencial para fornecer autenticação para qualquer atividade que vir depois dela.  R [ligação de pipeline](automation-graphical-authoring-intro.md#links-and-workflow) chegou desde **Get-AutomationPSCredential** está esperando um único objeto.  

![Adicionar credencial à tela](media/automation-credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Utilizar uma credencial do PowerShell no DSC

Enquanto as configurações de DSC na automatização do Azure podem fazer referência a ativos de credencial usando **Get-AutomationPSCredential**, ativos de credencial podem também ser transmitidos através de parâmetros, se quisesse. Para obter mais informações, consulte [compilar configurações no DSC de automatização do Azure](automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Com credenciais no Python2

O exemplo a seguir mostra um exemplo de aceder a credenciais em runbooks do Python2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre as ligações na criação de gráficos, veja [Links na criação de gráficos](automation-graphical-authoring-intro.md#links-and-workflow)
* Para compreender os métodos de autenticação diferentes com a automatização, veja [segurança da automatização do Azure](automation-security-overview.md)
* Para começar com runbooks Gráficos, consulte o artigo [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md) 
* Para começar com runbooks do Python2, veja [meu primeiro runbook Python2](automation-first-runbook-textual-python2.md) 