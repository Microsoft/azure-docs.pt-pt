---
title: Ativos credenciais na Automação Azure
description: Os ativos credenciais na Azure Automation contêm credenciais de segurança que podem ser usadas para autenticar recursos acedidos pela configuração do livro de execução ou dSC. Este artigo descreve como criar ativos credenciais e usá-los numa configuração de rumbook ou DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 767c1fddbc3d1f46d4341a70c990c2b57ad40e54
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373539"
---
# <a name="credential-assets-in-azure-automation"></a>Ativos credenciais na Automação Azure

Um ativo credencial automationis detém um objeto, que contém credenciais de segurança como um nome de utilizador e uma senha. Os livros de execução e as configurações dSC podem utilizar cmdlets que aceitem um objeto PSCredential para autenticação, ou podem extrair o nome de utilizador e a palavra-passe do objeto PSCredential para fornecer a alguma aplicação ou serviço que exija a autenticação. As propriedades para uma credencial são armazenadas de forma segura na Automação Azure e podem ser acedidas na configuração do livro de execução ou DSC com a atividade [Get-AutomationPSCredential.](#activities)

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Os ativos seguros na Automatização Azure incluem credenciais, certificados, ligações e variáveis encriptadas. Estes ativos são encriptados e armazenados na Automatização Azure utilizando uma chave única que é gerada para cada conta de automação. Esta chave está armazenada no Cofre chave. Antes de armazenar um ativo seguro, a chave é carregada a partir do Cofre chave e depois usada para encriptar o ativo.

## <a name="azure-powershell-az-cmdlets"></a>Cmdlets Azure PowerShell Az

Para o módulo Azure PowerShell Az, os cmdlets na tabela seguinte são utilizados para criar e gerir ativos credenciais de automação com o Windows PowerShell. Eles enviam como parte do [módulo De automação AzureAz.Automation,](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)que está disponível para uso em livros de automação e configurações DSC.

| Cmdlets | Descrição |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Recupera informações sobre um ativo credencial. Isto não devolve um objeto PSCredential.  |
| [Novo AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Cria uma nova credencial de Automação. |
| [Remover-AutomaçãoCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Remove uma credencial de Automação. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Define as propriedades para uma credencial de Automação existente. |

## <a name="activities"></a>Atividades

As atividades na tabela seguinte são usadas para aceder a credenciais em configurações de livro de execução e DSC.

| Atividades | Descrição |
|:--- |:--- |
| Get-AutomationPSCredential |Obtém uma credencial para usar numa configuração de rumbook ou DSC. Devolve um [objeto System.Management.Automation.PSCredential.](/dotnet/api/system.management.automation.pscredential) |

> [!NOTE]
> Deve evitar a utilização de variáveis no parâmetro "Nome do Get-AutomationPSCredential", uma vez que isso pode complicar a descoberta de dependências entre livros de execução ou configurações dSC e ativos credenciais no momento do design.

## <a name="python2-functions"></a>Funções Python2

A função na tabela seguinte é utilizada para aceder a credenciais num livro python2.

| Função | Descrição |
|:---|:---|
| automationassets.get_automation_credential | Recupera informações sobre um ativo credencial. |

> [!NOTE]
> Deve importar o módulo de "automação" no topo do seu livro de execução Python para aceder às funções do ativo.

## <a name="creating-a-new-credential-asset"></a>Criar um novo ativo credencial

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Para criar um novo ativo credencial com o portal Azure

1. Na sua conta de automação, selecione **Credenciais** em **Recursos Partilhados.**
1. Selecione **Adicionar uma credencial**.
1. Complete o formulário e selecione **Criar** para salvar a nova credencial.

> [!NOTE]
> As contas de utilizador que utilizam a autenticação de vários fatores não são suportadas para utilização na Automação Azure.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Para criar um novo ativo credencial com o Windows PowerShell

Os comandos de amostra seguem mostram como criar uma nova credencial de automatização. Um objeto PSCredential é criado primeiro com o nome e senha e depois usado para criar o ativo credencial. Em alternativa, pode utilizar o cmdlet **Get-Credential** para ser solicitado a escrever um nome e uma senha.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Usando uma credencial PowerShell

Você recupera um ativo credencial em um livro de execução ou configuração DSC com a atividade **Get-AutomationPSCredential.** Isto devolve um [objeto PSCredential](/dotnet/api/system.management.automation.pscredential) que pode utilizar com uma atividade ou cmdlet que requer um parâmetro PSCredential. Também pode recuperar as propriedades do objeto credencial para usar individualmente. O objeto tem uma propriedade para o nome de utilizador e a palavra-passe segura, ou pode usar o método **GetNetworkCredential** para devolver um objeto [NetworkCredential](/dotnet/api/system.net.networkcredential) que fornecerá uma versão não segura da palavra-passe.

> [!NOTE]
> **Get-AzAutomationCredential** não devolve um **PSCredential** que pode ser usado para autenticação. Só fornece informações sobre a credencial. Se precisar de utilizar uma credencial num livro de execução, deve utilizar o **Get-AutomationPSCredential** para recuperar o objeto **PSCredential.**

### <a name="textual-runbook-sample"></a>Amostra de livro textual

Comandos de exemplo seguintes mostram como utilizar uma credencial do PowerShell num runbook. Neste exemplo, a credencial é recuperada e o seu nome de utilizador e senha atribuídos a variáveis.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Também pode utilizar uma credencial para autenticar o Azure com [o Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). Na maioria das circunstâncias, deve utilizar uma [conta Run As](../manage-runas-account.md) e recuperá-la com [Get-AzAutomationConnection](../automation-connections.md).

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>Amostra de livro gráfico

Adicione uma atividade **Get-AutomationPSCredential** a um livro de execução gráfico clicando na credencial no painel da Biblioteca do editor gráfico e selecionando **Adicionar à tela**.

![Adicione credencial à tela](../media/credentials/credential-add-canvas.png)

A imagem que se segue mostra um exemplo de utilização de uma credencial num livro gráfico. Neste caso, está a ser utilizado para fornecer autenticação para um livro de execução aos recursos azure, conforme descrito em [Recortes autenticados com conta de Utilizador AD Azure](../automation-create-aduser-account.md). A primeira atividade recupera a credencial que tem acesso à subscrição do Azure. A atividade **Connect-AzureRmAccount** utiliza então esta credencial para fornecer autenticação para quaisquer atividades que venham depois dela. Uma [ligação de pipeline](../automation-graphical-authoring-intro.md#links-and-workflow) está aqui, uma vez que **get-AutomationPSCredential** está à espera de um único objeto.  

![Adicione credencial à tela](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Usando uma credencial PowerShell em DSC

Enquanto as configurações dSC na Automatização Azure podem referenciar ativos credenciais usando **Get-AutomationPSCredential,** os ativos credenciais também podem ser passados através de parâmetros, se necessário. Para mais informações, consulte [Configurações de Compilação em DSC de Automação Azure](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Usando credenciais em Python2

A amostra que se segue mostra um exemplo de acesso a credenciais em livros de execução Python2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre links na autoria gráfica, consulte [Links na autoria gráfica](../automation-graphical-authoring-intro.md#links-and-workflow)
* Para compreender os diferentes métodos de autenticação com automação, consulte [Azure Automation Security](../automation-security-overview.md)
* Para começar com runbooks Gráficos, consulte o artigo [O meu primeiro runbook gráfico](../automation-first-runbook-graphical.md)
* Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](../automation-first-runbook-textual.md)
* Para começar com os livros python2, veja [o meu primeiro livro python2](../automation-first-runbook-textual-python2.md) 
