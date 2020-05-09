---
title: Gerir credenciais na Automação Azure
description: Os ativos credenciais na Azure Automation contêm credenciais de segurança que podem ser usadas para autenticar recursos acedidos pela configuração do livro de execução ou dSC. Este artigo descreve como criar ativos credenciais e usá-los numa configuração de rumbook ou DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 16b92108bcb4e5185a1990b0ed8f1278bfe44921
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652819"
---
# <a name="manage-credentials-in-azure-automation"></a>Gerir credenciais na Automação Azure

Um ativo credencial automationis detém um objeto que contém credenciais de segurança, como um nome de utilizador e uma palavra-passe. Os livros de execução e as configurações dSC utilizam cmdlets que aceitam um objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) para autenticação. Em alternativa, podem extrair o nome `PSCredential` de utilizador e a palavra-passe do objeto para fornecer a alguma aplicação ou serviço que exija a autenticação. 

>[!NOTE]
>Os ativos seguros na Automatização Azure incluem credenciais, certificados, ligações e variáveis encriptadas. Estes ativos são encriptados e armazenados na Automatização Azure utilizando uma chave única que é gerada para cada conta De Automação. A Azure Automation armazena a chave no Cofre chave gerido pelo sistema. Antes de armazenar um ativo seguro, a Automation carrega a chave a partir do Key Vault e depois usa-a para encriptar o ativo. 

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](../automation-update-azure-modules.md).

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="powershell-cmdlets-used-to-access-credentials"></a>Cmdlets PowerShell usados para aceder a credenciais

Os cmdlets na tabela seguinte criam e gerem credenciais de Automação com a PowerShell. Eles enviam como parte dos [módulos Az.](modules.md#az-modules)

| Cmdlet | Descrição |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Recupera um objeto [CredentialInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo?view=azurerm-ps) contendo metadados sobre a credencial. O cmdlet não recupera `PSCredential` o objeto em si.  |
| [Novo AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Cria uma nova credencial de Automação. |
| [Remover-AutomaçãoCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Remove uma credencial de Automação. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Define as propriedades para uma credencial de Automação existente. |

## <a name="other-cmdlets-used-to-access-credentials"></a>Outros cmdlets usados para aceder a credenciais

Os cmdlets na tabela seguinte são utilizados para aceder a credenciais nos seus livros de execução e configurações DSC. 

| Cmdlet | Descrição |
|:--- |:--- |
| `Get-AutomationPSCredential` |Obtém `PSCredential` um objeto para usar numa configuração de livro de execução ou DSC. Na maioria das vezes, deve utilizar `Get-AzAutomationCredential` este [cmdlet interno](modules.md#internal-cmdlets) em vez do cmdlet, uma vez que este último apenas recupera informações credenciais. Esta informação normalmente não é útil para passar para outro cmdlet. |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |Obtém uma credencial com um pedido de nome de utilizador e senha. Este cmdlet faz parte do módulo padrão Microsoft.PowerShell.Security. Ver [módulos Predefinidos](modules.md#default-modules).|
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | Cria um ativo credencial. Este cmdlet faz parte do módulo Azure predefinido. Ver [módulos Predefinidos](modules.md#default-modules).|

Para `PSCredential` recuperar objetos no seu `Orchestrator.AssetManagement.Cmdlets` código, tem de importar o módulo. Para mais informações, consulte [Gerir módulos em Automação Azure.](modules.md)

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Deve evitar a utilização `Name` de `Get-AutomationPSCredential`variáveis no parâmetro de . O seu uso pode complicar a descoberta de dependências entre livros de execução ou configurações de DSC e ativos credenciais no momento do projeto.

## <a name="python-2-functions-that-access-credentials"></a>Funções Python 2 que acedem a credenciais

A função na tabela seguinte é utilizada para aceder a credenciais num livro de execução Python 2.

| Função | Descrição |
|:---|:---|
| `automationassets.get_automation_credential` | Recupera informações sobre um ativo credencial. |

> [!NOTE]
> Importe `automationassets` o módulo no topo do seu livro de execução Python para aceder às funções de ativo.

## <a name="create-a-new-credential-asset"></a>Criar um novo ativo credencial

Pode criar um novo ativo credencial utilizando o portal Azure ou utilizando o Windows PowerShell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Criar um novo ativo credencial com o portal Azure

1. Na sua conta de Automação, selecione **Credenciais** em **Recursos Partilhados.**
1. Selecione **Adicionar uma credencial**.
2. No painel New Credential, introduza um nome credencial apropriado seguindo os seus padrões de nomeação. 
3. Digite o seu ID de acesso no campo de nome do **utilizador.** 
4. Para ambos os campos de senha, introduza a sua chave de acesso secreta.

    ![Criar nova credencial](../media/credentials/credential-create.png)

5. Se a caixa de autenticação de vários fatores for verificada, desverifique-a. 
6. Clique em **Criar** para salvar o novo ativo credencial.

> [!NOTE]
> A Azure Automation não suporta contas de utilizador que utilizem a autenticação de vários fatores.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Criar um novo ativo credencial com o Windows PowerShell

O exemplo que se segue mostra como criar um novo ativo credencial automation. Um `PSCredential` objeto é criado primeiro com o nome e a senha, e depois usado para criar o ativo credencial. Em vez disso, `Get-Credential` pode utilizar o cmdlet para pedir ao utilizador que digite um nome e uma senha.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="get-a-credential-asset"></a>Obter um ativo credencial

Uma configuração de rumbook ou DSC recupera `Get-AutomationPSCredential` um ativo credencial com o cmdlet interno. Este cmdlet `PSCredential` obtém um objeto que pode usar com um cmdlet que requer uma credencial. Também pode recuperar as propriedades do objeto credencial para usar individualmente. O objeto tem propriedades para o nome de utilizador e a palavra-passe segura. 

> [!NOTE]
> O `Get-AzAutomationCredential` cmdlet não `PSCredential` recupera um objeto que possa ser utilizado para autenticação. Só fornece informações sobre a credencial. Se precisar de utilizar uma credencial num livro de rum, deve recuperá-la como objeto `PSCredential` que utiliza `Get-AutomationPSCredential`.

Em alternativa, pode utilizar o método [GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) para recuperar um objeto [NetworkCredential](/dotnet/api/system.net.networkcredential) que representa uma versão não segura da palavra-passe.

### <a name="textual-runbook-example"></a>Exemplo de livro textual

O exemplo que se segue mostra como usar uma credencial PowerShell num livro de execução. Recupera a credencial e atribui o seu nome de utilizador e senha a variáveis.


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Também pode utilizar uma credencial para autenticar o Azure com [o Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). Na maioria das circunstâncias, deve utilizar uma [conta Run As](../manage-runas-account.md) e recuperar a ligação com [get-AzAutomationConnection](../automation-connections.md).


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>Exemplo de livro de execução gráfico

Pode adicionar uma atividade `Get-AutomationPSCredential` para o cmdlet interno a um livro de execução gráfico clicando na credencial no painel da Biblioteca do editor gráfico e selecionando **Adicionar à tela**.

![Adicione credencial à tela](../media/credentials/credential-add-canvas.png)

A imagem que se segue mostra um exemplo de utilização de uma credencial num livro gráfico. Neste caso, a credencial prevê a autenticação de um livro de execução aos recursos Azure, conforme descrito na [Usa Azure AD em Azure Automation para autenticar o Azure.](../automation-use-azure-ad.md) A primeira atividade recupera a credencial que tem acesso à subscrição do Azure. A atividade de ligação à conta utiliza então esta credencial para fornecer autenticação para quaisquer atividades que venham depois dela. Uma [ligação de](../automation-graphical-authoring-intro.md#links-and-workflow) `Get-AutomationPSCredential` gasoduto é usada aqui, uma vez que espera um único objeto.  

![Adicione credencial à tela](../media/credentials/get-credential.png)

## <a name="use-credentials-in-a-dsc-configuration"></a>Use credenciais numa configuração DSC

Enquanto as configurações dSC na Automatização Azure podem funcionar com ativos credenciais usando, `Get-AutomationPSCredential`também podem passar ativos credenciais através de parâmetros. Para mais informações, consulte [Configurações de Compilação em DSC de Automação Azure](../automation-dsc-compile.md#credential-assets).

## <a name="use-credentials-in-a-python-2-runbook"></a>Use credenciais num livro python 2

O exemplo que se segue mostra um exemplo de acesso a credenciais em livros de execução Python 2.


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os cmdlets usados para aceder a credenciais, consulte [Gerir módulos em Automação Azure](modules.md).
* Para obter informações gerais sobre livros de execução, consulte a execução do Livro de [Corridas na Automação Azure.](../automation-runbook-execution.md)
* Para mais detalhes sobre as configurações do DSC, consulte a visão geral da [Configuração do Estado](../automation-dsc-overview.md).