---
title: Gerenciar módulos na automação do Azure
description: Este artigo descreve como gerenciar módulos na automação do Azure
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 65759b32889f9a99b0322823bb8a4924788e8c09
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786474"
---
# <a name="manage-modules-in-azure-automation"></a>Gerenciar módulos na automação do Azure

A automação do Azure fornece a capacidade de importar módulos do PowerShell para sua conta de automação a ser usada pelos runbooks baseados no PowerShell. Esses módulos podem ser módulos personalizados que você criou, do Galeria do PowerShell, ou os módulos AzureRM e AZ para o Azure. Quando você cria uma conta de automação, alguns módulos são importados por padrão.

## <a name="import-modules"></a>Importar módulos

Há várias maneiras pelas quais você pode importar um módulo para sua conta de automação. As seções a seguir mostram as diferentes maneiras de importar um módulo.

> [!NOTE]
> O caminho máximo de um arquivo em um módulo a ser usado na automação do Azure é de 140 caracteres. Qualquer caminho acima de 140 caracteres não será capaz de ser importado para a sessão do PowerShell com `Import-Module`.

### <a name="powershell"></a>PowerShell

Você pode usar o [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) para importar um módulo para sua conta de automação. O cmdlet usa uma URL para um pacote zip de módulo.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Você também pode usar o mesmo cmdlet para importar um módulo de Galeria do PowerShell diretamente. Certifique-se de pegar **ModuleName** e **ModuleVersion** de [Galeria do PowerShell](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Portal do Azure

Na portal do Azure, navegue até sua conta de automação e selecione **módulos** em **recursos compartilhados**. Clique em **+ Adicionar um módulo**. Selecione um arquivo **. zip** que contenha seu módulo e clique em **OK** para iniciar a importação do processo.

### <a name="powershell-gallery"></a>Galeria do PowerShell

Os módulos da galeria do PowerShell podem ser importados do [Galeria do PowerShell](https://www.powershellgallery.com) diretamente ou da sua conta de automação.

Para importar um módulo do Galeria do PowerShell, acesse https://www.powershellgallery.com e procure o módulo que você deseja importar. Clique em **implantar na automação do Azure** na guia **automação do Azure** em **Opções de instalação**. Essa ação abre a portal do Azure. Na página **importar** , selecione sua conta de automação e clique em **OK**.

![Módulo de importação de Galeria do PowerShell](../media/modules/powershell-gallery.png)

Você também pode importar módulos do Galeria do PowerShell diretamente da sua conta de automação. Em sua conta de automação, selecione **módulos** em **recursos compartilhados**. Na página módulos, clique em **procurar na Galeria**e pesquise o Galeria do PowerShell de um módulo. Selecione o módulo que você deseja importar e clique em **importar**. Na página **importar** , clique em **OK** para iniciar o processo de importação.

![Galeria do PowerShell importação do portal do Azure](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Excluir módulos

Se você tiver problemas com um módulo ou precisar reverter para uma versão anterior de um módulo, poderá excluí-lo da sua conta de automação. Não é possível excluir a versão original dos [módulos padrão](#default-modules) que são importados quando você cria uma conta de automação. Se o módulo que você deseja excluir for uma versão mais recente de um dos [módulos padrão](#default-modules) instalados, ele será revertido para a versão que foi instalada com sua conta de automação. Caso contrário, qualquer módulo que você excluir da sua conta de automação será removido.

### <a name="azure-portal"></a>Portal do Azure

Na portal do Azure, navegue até sua conta de automação e selecione **módulos** em **recursos compartilhados**. Selecione o módulo que você deseja remover. Na página do **módulo** , clcick **delete**. Se esse módulo for um dos [módulos padrão](#default-modules), ele será revertido para a versão que estava presente quando a conta de automação foi criada.

### <a name="powershell"></a>PowerShell

Para remover um módulo por meio do PowerShell, execute o seguinte comando:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Cmdlets internos

Veja a seguir uma lista de cmdlets no módulo `Orchestrator.AssetManagement.Cmdlets` interno que é importado para cada conta de automação. Esses cmdlets podem ser acessados em seus runbooks e configurações DSC e permitem que você interaja com seus ativos na sua conta de automação. Além disso, os cmdlets internos permitem que você recupere segredos de valores de **variáveis** criptografados, **credenciais**e campos de **conexão** criptografados. Os cmdlets Azure PowerShell não podem recuperar esses segredos. Esses cmdlets não exigem que você se conecte implicitamente ao Azure ao usá-los, como usar uma conta Executar como para autenticar no Azure.

>[!NOTE]
>Esses cmdlets internos não estão disponíveis em um Hybrid Runbook Worker, eles só podem ser acessados de runbooks em execução no Azure. Use os módulos [AzureRM. Automation](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) ou [AZ](../az-modules.md) correspondentes para runbooks em execução diretamente no computador ou em relação a recursos em seu ambiente. 
>

|Nome|Descrição|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Adicionar um tipo de conexão ao seu módulo

Você pode fornecer um [tipo de conexão](../automation-connections.md) personalizada para usar em sua conta de automação adicionando um arquivo opcional ao seu módulo. Esse arquivo é um arquivo de metadados que especifica um tipo de conexão de automação do Azure a ser usado com os cmdlets do módulo em sua conta de automação. Para conseguir isso, você deve primeiro saber como criar um módulo do PowerShell. Para obter mais informações sobre a criação de módulo, consulte [como gravar um módulo de script do PowerShell](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

![Usar uma conexão personalizada no portal do Azure](../media/modules/connection-create-new.png)

Para adicionar um tipo de conexão de automação do Azure, seu módulo deve conter um arquivo com o nome `<ModuleName>-Automation.json` que especifica as propriedades do tipo de conexão. O arquivo JSON é colocado dentro da pasta do módulo do arquivo. zip compactado. Esse arquivo contém os campos de uma conexão que é necessária para se conectar ao sistema ou serviço que o módulo representa. A configuração acaba criando um tipo de conexão na automação do Azure. Usando esse arquivo, você pode definir os nomes de campo, tipos e se os campos devem ser criptografados ou opcionais, para o tipo de conexão do módulo. O exemplo a seguir é um modelo no formato de arquivo JSON que define uma propriedade de nome de usuário e senha:

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="module-best-practices"></a>Práticas recomendadas de módulo

Os módulos do PowerShell podem ser importados para a automação do Azure para disponibilizar seus cmdlets para uso em runbooks e seus recursos de DSC disponíveis para uso nas configurações de DSC. Nos bastidores, a automação do Azure armazena esses módulos e, no trabalho de runbook e no tempo de execução do trabalho de compilação DSC, carrega-os nas áreas restritas de automação do Azure onde os runbooks são executados e as configurações de DSC são compiladas. Todos os recursos de DSC em módulos também são colocados automaticamente no servidor de pull DSC de Automação. Eles podem ser extraídos por computadores quando aplicam configurações de DSC.

Recomendamos que você considere o seguinte ao criar um módulo do PowerShell para uso na automação do Azure:

* Não inclua uma pasta de versão dentro do pacote. zip.  Esse problema é uma preocupação menor para runbooks, mas causará um problema com o serviço de configuração de estado.  A automação do Azure criará a pasta de versão automaticamente quando o módulo for distribuído para nós gerenciados pela DSC e, se uma pasta de versão existir, você acabará com duas instâncias.  Exemplo de estrutura de pastas para um módulo DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Inclua um resumo, uma descrição e um URI de ajuda para cada cmdlet no módulo. No PowerShell, pode definir determinadas informações de ajuda para os cmdlets para permitir ao utilizador receber ajuda sobre a utilização dos mesmos com o cmdlet **Get-Help**. O exemplo a seguir mostra como definir um URI de Sinopse e ajuda para em um arquivo de módulo. psm1:

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Fornecer essas informações mostra essa ajuda usando o cmdlet **Get-Help** no console do PowerShell. Essa descrição também é exibida no portal do Azure.

  ![Ajuda do Módulo de Integração](../media/modules/module-activity-description.png)

* Se o módulo se conectar a um serviço externo, ele deverá conter um [tipo de conexão](#add-a-connection-type-to-your-module). Cada cmdlet no módulo deve ser capaz de considerar um objeto de ligação (uma instância desse tipo de ligação) como um parâmetro. Os usuários mapeiam parâmetros do ativo de conexão para os parâmetros correspondentes do cmdlet cada vez que chamam um cmdlet. Com base no exemplo de runbook acima, ele usa um ativo de conexão contoso de exemplo chamado ContosoConnection para acessar os recursos da Contoso e retornar dados do serviço externo.

  No exemplo a seguir, os campos são mapeados para as propriedades UserName e password de um objeto `PSCredential` e, em seguida, passados para o cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Uma maneira mais fácil e melhor de abordar esse comportamento é passando diretamente o objeto de conexão para o cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Você pode habilitar o comportamento como o exemplo anterior para seus cmdlets, permitindo que eles aceitem um objeto de conexão diretamente como um parâmetro, em vez de apenas campos de conexão para parâmetros. Normalmente, você deseja um conjunto de parâmetros para cada um, de modo que um usuário que não usa a automação do Azure possa chamar seus cmdlets sem construir uma tabela de hash para atuar como o objeto de conexão. O conjunto de parâmetros `UserAccount`, é usado para passar as propriedades do campo de conexão. `ConnectionObject` permite que você passe a conexão diretamente.

* Defina o tipo de saída para todos os cmdlets no módulo. Definir um tipo de saída para um cmdlet permite que o IntelliSense, no momento da conceção, o ajude a determinar as propriedades de saída do cmdlet para uma utilização durante a criação. Ele é especialmente útil durante a criação gráfica do runbook de automação, em que o conhecimento do tempo de design é fundamental para uma experiência de usuário fácil com seu módulo.

Adicione `[OutputType([<MyOutputType>])]` em que myoutputtype é um tipo válido. Para saber mais sobre OutputType, consulte [sobre o Functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). O código a seguir é um exemplo de adição de `OutputType` a um cmdlet:

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![Tipo de Saída Gráfica do Runbook](../media/modules/runbook-graphical-module-output-type.png)

  Esse comportamento é semelhante à funcionalidade de "tipo antecipado" da saída de um cmdlet no ISE do PowerShell sem precisar executá-lo.

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

* Crie todos os cmdlets no módulo sem monitorização de estado. Vários trabalhos de runbook podem ser executados simultaneamente no mesmo AppDomain e no mesmo processo e área restrita. Se houver qualquer estado compartilhado nesses níveis, os trabalhos poderão afetar os outros. Esse comportamento pode levar a problemas intermitentes e difíceis de diagnosticar.  Eis um exemplo de o que não deve fazer.

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

* O módulo deve estar totalmente contido em um pacote habilitado para xcopy. Os módulos de automação do Azure são distribuídos para as áreas restritas de automação quando os runbooks precisam ser executados. Os módulos precisam trabalhar independentemente do host no qual estão sendo executados. Você deve ser capaz de compactar e mover um pacote de módulo e fazer com que ele funcione normalmente quando importado para o ambiente do PowerShell de outro host. Para que isso aconteça, o módulo não deve depender de nenhum arquivo fora da pasta do módulo. Essa pasta é a pasta que é compactada quando o módulo é importado para a automação do Azure. O módulo também não deve depender de nenhuma configuração de registro exclusiva em um host, como as configurações definidas quando um produto é instalado. Todos os arquivos no módulo devem ter um caminho inferior a 140 caracteres. Todos os caminhos acima de 140 caracteres causarão problemas ao importar o runbook. Se essa prática recomendada não for seguida, o módulo não poderá ser usado na automação do Azure.  

* Se fizer referência aos [módulos AZ do Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) em seu módulo, verifique se você também não está referenciando `AzureRM`. O módulo `Az` não pode ser usado em conjunto com os módulos `AzureRM`. o `Az` tem suporte em runbooks, mas não é importado por padrão. Para saber mais sobre os módulos `Az` e as considerações a serem levadas em conta, consulte [AZ module support in Azure Automation](../az-modules.md).

## <a name="default-modules"></a>Módulos padrão

A tabela a seguir lista os módulos que são importados por padrão quando uma conta de automação é criada. Os módulos listados abaixo podem ter versões mais recentes importadas, mas a versão original não pode ser removida da sua conta de automação mesmo que você exclua uma versão mais recente delas.

|Nome do módulo|Versão|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0,2 |
| Microsoft. PowerShell. Core | 0 |
| Microsoft. PowerShell. Diagnostics |  |
| Microsoft. PowerShell. Management |  |
| Microsoft. PowerShell. Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft. WSMan. Management |  |
| Orchestrator. AssetManagement. cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a criação de Módulos do PowerShell, veja o artigo [Escrever um Módulo do Windows PowerShell](/powershell/scripting/developer/windows-powershell)
