---
title: Ativos de credencial na automação do Azure
description: Os ativos de credencial na automação do Azure contêm credenciais de segurança que podem ser usadas para autenticar os recursos acessados pelo runbook ou pela configuração DSC. Este artigo descreve como criar ativos de credencial e usá-los em um runbook ou configuração DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/12/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 582645919825c308fce4fe3211fa601955aaf37d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850181"
---
# <a name="credential-assets-in-azure-automation"></a>Ativos de credencial na automação do Azure

Um ativo de credencial de automação contém um objeto, que contém credenciais de segurança, como nome de usuário e senha. Os Runbooks e as configurações DSC podem usar cmdlets que aceitam um objeto PSCredential para autenticação, ou podem extrair o nome de usuário e a senha do objeto PSCredential para fornecer a um aplicativo ou serviço que exija autenticação. As propriedades de uma credencial são armazenadas com segurança na automação do Azure e podem ser acessadas no runbook ou na configuração DSC com a atividade [Get-AutomationPSCredential](#activities) .

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Os ativos seguros na automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na automação do Azure usando uma chave exclusiva que é gerada para cada conta de automação. Essa chave é armazenada em Key Vault. Antes de armazenar um ativo seguro, a chave é carregada de Key Vault e, em seguida, usada para criptografar o ativo.

## <a name="azure-classic-powershell-cmdlets"></a>Cmdlets do PowerShell clássico do Azure

Os cmdlets na tabela a seguir são usados para criar e gerenciar ativos de credenciais de automação com o Windows PowerShell.  Eles são fornecidos como parte do [módulo Azure PowerShell](/powershell/azure/overview), que está disponível para uso em Runbooks de automação e configurações DSC.

| Cmdlets | Descrição |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/servicemanagement/azure/get-azureautomationcredential) |Recupera informações sobre um ativo de credencial. Você só pode recuperar a credencial propriamente dita da atividade **Get-AutomationPSCredential** . |
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Cria uma nova credencial de automação. |
| [Remove-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Remove uma credencial de automação. |
| [Set-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Define as propriedades de uma credencial de automação existente. |

## <a name="azurerm-powershell-cmdlets"></a>Cmdlets do PowerShell do AzureRM

Para AzureRM, os cmdlets na tabela a seguir são usados para criar e gerenciar ativos de credenciais de automação com o Windows PowerShell.  Eles são fornecidos como parte do [módulo AzureRM. Automation](/powershell/azure/overview), que está disponível para uso em Runbooks de automação e configurações DSC.

| Cmdlets | Descrição |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential) |Recupera informações sobre um ativo de credencial. Isso não retorna um objeto PSCredential.  |
| [New-AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential) |Cria uma nova credencial de automação. |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential) |Remove uma credencial de automação. |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential) |Define as propriedades de uma credencial de automação existente. |

## <a name="activities"></a>Atividades

As atividades na tabela a seguir são usadas para acessar credenciais em um runbook e configurações DSC.

| Atividades | Descrição |
|:--- |:--- |
| Get-AutomationPSCredential |Obtém uma credencial a ser usada em uma configuração de runbook ou DSC. Retorna um objeto [System. Management. Automation. PSCredential](/dotnet/api/system.management.automation.pscredential) . |

> [!NOTE]
> Evite usar variáveis no parâmetro – Name de Get-AutomationPSCredential, pois isso pode complicar a descoberta de dependências entre runbooks ou configurações DSC e ativos de credencial em tempo de design.

## <a name="python2-functions"></a>Funções Python2

A função na tabela a seguir é usada para acessar credenciais em um runbook do Python2.

| Função | Descrição |
|:---|:---|
| automationassets.get_automation_credential | Recupera informações sobre um ativo de credencial. |

> [!NOTE]
> Você deve importar o módulo "automationassets" na parte superior do seu runbook do Python para acessar as funções de ativo.

## <a name="creating-a-new-credential-asset"></a>Criando um novo ativo de credencial

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Para criar um novo ativo de credencial com o portal do Azure

1. Na sua conta de automação, selecione **credenciais** em **recursos compartilhados**.
1. Clique em **+ Adicionar uma credencial**.
1. Preencha o formulário e clique em **criar** para salvar a nova credencial.

> [!NOTE]
> As contas de usuário que usam a autenticação multifator não têm suporte para uso na automação do Azure.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Para criar um novo ativo de credencial com o Windows PowerShell

Os comandos de exemplo a seguir mostram como criar uma nova credencial de automação. Um objeto PSCredential é criado primeiro com o nome e a senha e, em seguida, usado para criar o ativo de credencial. Como alternativa, você pode usar o cmdlet **Get-Credential** para ser solicitado a digitar um nome e uma senha.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Usando uma credencial do PowerShell

Você recupera um ativo de credencial em um runbook ou configuração DSC com a atividade **Get-AutomationPSCredential** . Isso retorna um [objeto PSCredential](/dotnet/api/system.management.automation.pscredential) que você pode usar com uma atividade ou um cmdlet que requer um parâmetro PSCredential. Você também pode recuperar as propriedades do objeto de credencial para usar individualmente. O objeto tem uma propriedade para o nome de usuário e a senha segura, ou você pode usar o método **GetNetworkCredential** para retornar um objeto [NetworkCredential](/dotnet/api/system.net.networkcredential) que fornecerá uma versão não segura da senha.

> [!NOTE]
> **Get-AzureRmAutomationCredential** não retorna um **PSCredential** que pode ser usado para autenticação. Ele fornece apenas informações sobre a credencial. Se você precisar usar uma credencial em um runbook, deverá usar o **Get-AutomationPSCredential** para recuperar o objeto **PSCredential** .

### <a name="textual-runbook-sample"></a>Exemplo de runbook textual

Comandos de exemplo seguintes mostram como utilizar uma credencial do PowerShell num runbook. Neste exemplo, a credencial é recuperada e seu nome de usuário e senha atribuídos a variáveis.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Você também pode usar uma credencial para autenticar no Azure com [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount). Na maioria das circunstâncias, você deve usar uma [conta Executar como](../manage-runas-account.md) e recuperá-la com [Get-AutomationConnection](../automation-connections.md).

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>Exemplo de runbook gráfico

Você adiciona uma atividade **Get-AutomationPSCredential** a um runbook gráfico clicando com o botão direito do mouse na credencial no painel Biblioteca do editor gráfico e selecionando **Adicionar à tela**.

![Adicionar credencial à tela](../media/credentials/credential-add-canvas.png)

A imagem a seguir mostra um exemplo de como usar uma credencial em um runbook gráfico.  Nesse caso, ele está sendo usado para fornecer autenticação para um runbook para recursos do Azure, conforme descrito em [autenticar Runbooks com a conta de usuário do Azure ad](../automation-create-aduser-account.md).  A primeira atividade recupera a credencial que tem acesso à assinatura do Azure.  Em seguida, a atividade **Add-AzureAccount** usa essa credencial para fornecer autenticação para todas as atividades que vierem depois dela.  Um [link de pipeline](../automation-graphical-authoring-intro.md#links-and-workflow) está aqui, pois **Get-AutomationPSCredential** está esperando um único objeto.  

![Adicionar credencial à tela](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Usando uma credencial do PowerShell em DSC

Embora as configurações de DSC na automação do Azure possam referenciar ativos de credencial usando **Get-AutomationPSCredential**, os ativos de credencial também podem ser passados por meio de parâmetros, se desejado. Para obter mais informações, consulte [compilando configurações no Azure DSC de automação](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Usando credenciais em Python2

O exemplo a seguir mostra um exemplo de acesso a credenciais em runbooks Python2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre links na criação gráfica, confira [links na criação gráfica](../automation-graphical-authoring-intro.md#links-and-workflow)
* Para entender os diferentes métodos de autenticação com a automação, consulte [segurança de automação do Azure](../automation-security-overview.md)
* Para começar com runbooks Gráficos, consulte o artigo [O meu primeiro runbook gráfico](../automation-first-runbook-graphical.md)
* Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](../automation-first-runbook-textual.md)
* Para começar a usar os runbooks do Python2, consulte [meu primeiro runbook do Python2](../automation-first-runbook-textual-python2.md) 
