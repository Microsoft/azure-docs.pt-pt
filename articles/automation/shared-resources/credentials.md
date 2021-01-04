---
title: Gerir credenciais na Automatização do Azure
description: Este artigo diz como criar ativos credenciais e usá-los num livro de execução ou configuração DSC.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: caaeb0e40d277ef5e356c0f385a818b831326d6e
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734832"
---
# <a name="manage-credentials-in-azure-automation"></a>Gerir credenciais na Automatização do Azure

Um ativo credencial automation contém um objeto que contém credenciais de segurança, como um nome de utilizador e uma palavra-passe. Os runbooks e as configurações DSC utilizam cmdlets que aceitam um objeto [PSCredential](/dotnet/api/system.management.automation.pscredential) para autenticação. Em alternativa, podem extrair o nome de utilizador e a palavra-passe do `PSCredential` objeto para fornecer a alguma aplicação ou serviço que exija a autenticação.

>[!NOTE]
>Os ativos seguros na Azure Automation incluem credenciais, certificados, conexões e variáveis encriptadas. Estes ativos são encriptados e armazenados na Azure Automation utilizando uma chave única que é gerada para cada conta Dem automação. A Azure Automation armazena a chave no Key Vault gerido pelo sistema. Antes de armazenar um ativo seguro, a Automatização carrega a chave do Key Vault e utiliza-a para encriptar o ativo. 

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="powershell-cmdlets-used-to-access-credentials"></a>Cmdlets PowerShell utilizados para aceder a credenciais

Os cmdlets na tabela seguinte criam e gerem credenciais de Automação com PowerShell. Eles enviam como parte dos [módulos Az.](modules.md#az-modules)

| Cmdlet | Descrição |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential) |Recupera um objeto [CredencialInfo](/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo) contendo metadados sobre a credencial. O cmdlet não recupera o `PSCredential` objeto em si.  |
| [Novo-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential) |Cria uma nova credencial de Automação. |
| [Remover-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential) |Remove uma credencial de automação. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential) |Define as propriedades para uma credencial de automação existente. |

## <a name="other-cmdlets-used-to-access-credentials"></a>Outros cmdlets utilizados para aceder a credenciais

Os cmdlets da tabela seguinte são utilizados para aceder a credenciais nos seus runbooks e configurações DSC. 

| Cmdlet | Descrição |
|:--- |:--- |
| `Get-AutomationPSCredential` |Obtém um `PSCredential` objeto para usar num livro de recortes ou configuração DSC. Na maioria das vezes, deve utilizar este [cmdlet interno](modules.md#internal-cmdlets) em vez do `Get-AzAutomationCredential` cmdlet, uma vez que este último apenas recupera informações credenciais. Esta informação normalmente não é útil para passar para outro cmdlet. |
| [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) |Obtém uma credencial com um pedido de nome de utilizador e senha. Este cmdlet faz parte do módulo padrão Microsoft.PowerShell.Security. Ver [módulos predefinidos](modules.md#default-modules).|
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) | Cria um ativo credencial. Este cmdlet faz parte do módulo Azure predefinido. Ver [módulos predefinidos](modules.md#default-modules).|

Para recuperar `PSCredential` objetos no seu código, tem de importar o `Orchestrator.AssetManagement.Cmdlets` módulo. Para mais informações, consulte [Gerir os módulos na Azure Automation.](modules.md)

```powershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Deve evitar a utilização de variáveis no `Name` parâmetro de `Get-AutomationPSCredential` . A sua utilização pode complicar a descoberta de dependências entre runbooks ou configurações DSC e ativos credenciais no momento do design.

## <a name="python-functions-that-access-credentials"></a>Funções python que acedem a credenciais

A função na tabela seguinte é utilizada para aceder a credenciais num livro python 2 e 3. Os livros de 100 python estão atualmente em pré-visualização.

| Função | Descrição |
|:---|:---|
| `automationassets.get_automation_credential` | Recupera informações sobre um ativo credencial. |

> [!NOTE]
> Importe o `automationassets` módulo na parte superior do seu livro de bordo Python para aceder às funções do ativo.

## <a name="create-a-new-credential-asset"></a>Criar um novo ativo credencial

Pode criar um novo ativo credencial utilizando o portal Azure ou utilizando o Windows PowerShell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Criar um novo ativo credencial com o portal Azure

1. A partir da sua conta Demôm automação, no painel esquerdo selecione **Credenciais** em **Recursos Partilhados.**
2. Na página **Credenciais,** **selecione Adicionar uma credencial**.
3. No painel de novas credenciais, insira um nome credencial apropriado seguindo os seus padrões de nomeação.
4. Digite o seu ID de acesso no campo **nome do utilizador.**
5. Para ambos os campos de senha, insira a sua chave de acesso secreta.

    ![Criar nova credencial](../media/credentials/credential-create.png)

6. Se a caixa de autenticação multi-factor for verificada, desmarque-a.
7. Clique em **Criar** para salvar o novo ativo credencial.

> [!NOTE]
> A Azure Automation não suporta contas de utilizador que utilizem a autenticação de vários fatores.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Criar um novo ativo credencial com o Windows PowerShell

O exemplo a seguir mostra como criar um novo ativo credencial Automation. Um `PSCredential` objeto é criado primeiro com o nome e senha, e depois usado para criar o ativo credencial. Em vez disso, pode utilizar o `Get-Credential` cmdlet para solicitar ao utilizador que escreva um nome e uma palavra-passe.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="get-a-credential-asset"></a>Obtenha um ativo credencial

Um runbook ou configuração DSC recupera um ativo credencial com o `Get-AutomationPSCredential` cmdlet interno. Este cmdlet obtém um `PSCredential` objeto que pode usar com um cmdlet que requer uma credencial. Também pode recuperar as propriedades do objeto credencial para usar individualmente. O objeto tem propriedades para o nome de utilizador e a senha segura. 

> [!NOTE]
> O `Get-AzAutomationCredential` cmdlet não recupera um `PSCredential` objeto que possa ser utilizado para a autenticação. Só fornece informações sobre a credencial. Se precisar de utilizar uma credencial num livro de bordo, deve recuperá-la como `PSCredential` objeto utilizando `Get-AutomationPSCredential` .

Em alternativa, pode utilizar o método [GetNetworkCredential](/dotnet/api/system.management.automation.pscredential.getnetworkcredential) para recuperar um objeto [NetworkCredential](/dotnet/api/system.net.networkcredential) que representa uma versão não garantida da palavra-passe.

### <a name="textual-runbook-example"></a>Exemplo de livro de texto

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

O exemplo a seguir mostra como utilizar uma credencial PowerShell num livro de bordo. Recupera a credencial e atribui o seu nome de utilizador e palavra-passe a variáveis.

```powershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Também pode utilizar uma credencial para autenticar a Azure com [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Na maioria das circunstâncias, deve utilizar uma [conta Run As](../manage-runas-account.md) e recuperar a ligação com a [Get-AzAutomationConnection](../automation-connections.md).

```powershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$securePassword)

Connect-AzAccount -Credential $myPsCred
```

# <a name="python-2"></a>[Python 2](#tab/python2)

O exemplo a seguir mostra um exemplo de acesso a credenciais em livros python 2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

# <a name="python-3"></a>[Python 3](#tab/python3)

O exemplo a seguir mostra um exemplo de acesso a credenciais em livros python 3 (pré-visualização).

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print (cred["username"])
print (cred["password"])
```

---

### <a name="graphical-runbook-example"></a>Exemplo de runbook gráfico

Pode adicionar uma atividade do `Get-AutomationPSCredential` cmdlet interno a um runbook gráfico clicando à direita na credencial no painel de biblioteca do editor gráfico e selecionando **Adicionar à tela**.

![Adicione cmdlet credencial à tela](../media/credentials/credential-add-canvas.png)

A imagem a seguir mostra um exemplo de utilização de uma credencial num livro gráfico. Neste caso, a credencial fornece autenticação para um livro de bordo para recursos Azure, conforme descrito na [Utilização Azure AD em Azure Automation para autenticar a Azure](../automation-use-azure-ad.md). A primeira atividade recupera a credencial que tem acesso à subscrição do Azure. A atividade de ligação de conta utiliza então esta credencial para fornecer autenticação para quaisquer atividades que venham atrás dela. Uma [ligação de gasoduto](../automation-graphical-authoring-intro.md#use-links-for-workflow) é usada aqui, uma vez que espera um único `Get-AutomationPSCredential` objeto.  

![Fluxo de trabalho credencial com exemplo de ligação de gasoduto](../media/credentials/get-credential.png)

## <a name="use-credentials-in-a-dsc-configuration"></a>Utilize credenciais numa configuração DSC

Enquanto as configurações de DSC na Azure Automation podem funcionar com ativos credenciais `Get-AutomationPSCredential` usando, também podem passar ativos credenciais através de parâmetros. Para obter mais informações, consulte [configurações de compilação no Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os cmdlets utilizados para aceder a certificados, consulte [Gerir módulos na Azure Automation.](modules.md)
* Para obter informações gerais sobre os runbooks, consulte [a execução do Runbook na Azure Automation](../automation-runbook-execution.md).
* Para obter detalhes sobre as configurações do DSC, consulte [a visão geral da configuração do Estado da Automação Azure](../automation-dsc-overview.md).