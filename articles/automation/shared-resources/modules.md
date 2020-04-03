---
title: Gerir módulos em Automação Azure
description: Este artigo descreve como gerir módulos na Automação Azure.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 605d1bc72406a9aeecc9273f9bd2d7fd2b30ab11
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618699"
---
# <a name="manage-modules-in-azure-automation"></a>Gerir módulos em Automação Azure

Pode importar módulos PowerShell para a Automatização Azure para disponibilizar os seus cmdlets em livros de execução e os seus recursos DSC disponíveis nas configurações do DSC. Nos bastidores, a Azure Automation armazena estes módulos. No trabalho de runbook e no tempo de execução do trabalho de compilação dSC, a Automação coloca-os nas caixas de areia da Automação Azure onde os livros executam e as configurações dSC compilam. Quaisquer recursos DSC em módulos também são automaticamente colocados no servidor de puxar Automation DSC. As máquinas podem puxá-las quando aplicam configurações DSC.

Os módulos utilizados no Azure Automation podem ser módulos personalizados que criou, módulos da PowerShell Gallery ou dos módulos AzureRM e Az para o Azure. Quando se cria uma conta De automatização, alguns módulos são importados por padrão.

## <a name="default-modules"></a>Módulos padrão

A tabela seguinte lista módulos importados por padrão quando uma conta de Automação é criada. A automatização pode importar versões mais recentes destes módulos. No entanto, não é possível remover a versão original da sua conta Automation mesmo que apague uma versão mais recente.

|Nome do módulo|Versão|
|---|---|
| AuditoriaPolíticaDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0,2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSman.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscRecursos | 2.9.0.0 |
| Política de SegurançaDsc | 2.1.0.0 |
| Recursos Compostos estatais | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="internal-cmdlets"></a>Cmdlets internos

A tabela abaixo lista cmdlets no módulo interno `Orchestrator.AssetManagement.Cmdlets` que é importado em todas as contas da Automação. Estes cmdlets são acessíveis nos seus livros de execução e configurações DSC e permitem interagir com ativos dentro da sua conta Automation. Além disso, os cmdlets internos permitem-lhe recuperar segredos de variáveis encriptadas, credenciais e ligações encriptadas. Os cmdlets Azure PowerShell não são capazes de recuperar estes segredos. Estes cmdlets não requerem que se ligue implicitamente ao Azure quando os utiliza, como quando se utiliza uma Conta Run As para autenticar o Azure.

>[!NOTE]
>Estes cmdlets internos estão disponíveis num Trabalhador do Livro híbrido do Windows, mas não num Trabalhador de Runbook Híbrido Linux. Utilize os cmdlets de módulo [Sugóneta.Automação](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) ou [Az](../az-modules.md) correspondentes para livros de execução que funcionam diretamente no computador ou contra recursos no seu ambiente. 

|Nome|Descrição|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Início-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Trabalho de Automação de Espera|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="importing-modules"></a>Módulos de importação

Existem várias formas de importar um módulo para a sua conta Desmótica. As seguintes secções mostram as diferentes formas de importar um módulo.

> [!NOTE]
> O tamanho máximo do caminho para um ficheiro num módulo utilizado na Automatização Azure é de 140 caracteres. A automação não pode importar um ficheiro com tamanho de `Import-Module`caminho superior a 140 caracteres para a sessão PowerShell com .

### <a name="import-modules-in-azure-portal"></a>Módulos de importação no portal Azure

Para importar um módulo no portal Azure:

1. Navegue para a sua conta de Automação.
2. Selecione **Módulos** em **Recursos Partilhados**.
3. Clique **em adicionar um módulo**. 
4. Selecione o ficheiro **.zip** que contém o seu módulo.
5. Clique em **OK** para começar a importar processo.

### <a name="import-modules-using-powershell"></a>Módulos de importação usando powerShell

Pode utilizar o cmdlet [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) para importar um módulo na sua conta Automation. O cmdlet leva um URL para um pacote de zíper .zip módulo.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Também pode utilizar o mesmo cmdlet para importar um módulo da PowerShell Gallery diretamente. Certifique-se `ModuleName` de `ModuleVersion` agarrar e da [Galeria PowerShell.](https://www.powershellgallery.com)

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzureRmAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```
### <a name="import-modules-from-powershell-gallery"></a>Módulos de importação da Galeria PowerShell

Pode importar módulos [da PowerShell Gallery](https://www.powershellgallery.com) diretamente da galeria ou da sua conta Automation.

Para importar um módulo diretamente da Galeria PowerShell:

1. Vá https://www.powershellgallery.com procurar o módulo para importar.
2. Clique em **Implementar para Automatização Azure** no separador **Automação Azure** em opções de **instalação**. Esta ação abre o portal Azure. 
3. Na página Import, selecione a sua conta De automação e clique **EM OK**.

![Módulo de importação da PowerShell Gallery](../media/modules/powershell-gallery.png)

Para importar um módulo PowerShell Gallery diretamente da sua conta De automação:

1. Selecione **Módulos** em **Recursos Partilhados**. 
2. Na página Módulos, clique na **galeria Browse**e, em seguida, procure na galeria um módulo. 
3. Selecione o módulo para importar e clique **em Importar**. 
4. Na página de Importação, clique em **OK** para iniciar o processo de importação.

![PowerShell Gallery importa do portal Azure](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>Módulos de aparação

Se tiver problemas com um módulo ou precisar de voltar para uma versão anterior de um módulo, pode eliminá-lo da sua conta Automation. Não é possível eliminar as versões originais dos [módulos predefinidos](#default-modules) que são importados quando cria uma conta De automação. Se o módulo a eliminar for uma versão mais recente de um dos [módulos predefinidos,](#default-modules)volta para a versão que foi instalada com a sua conta Automation. Caso contrário, qualquer módulo que apague da sua conta Automation é removido.

### <a name="delete-modules-in-azure-portal"></a>Eliminar módulos no portal Azure

Para remover um módulo no portal Azure:

1. Navegue para a sua conta de Automação e selecione **Módulos** em **Recursos Partilhados**. 
2. Selecione o módulo que pretende remover. 
3. Na página **Módulo,** selecione **Eliminar**. Se este módulo é um dos [módulos predefinidos,](#default-modules)volta para a versão que existia quando a conta Automation foi criada.

### <a name="delete-modules-using-powershell"></a>Eliminar módulos usando powerShell

Para remover um módulo através do PowerShell, execute o seguinte comando:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```
## <a name="adding-a-connection-type-to-your-module"></a>Adicionar um tipo de ligação ao seu módulo

Pode fornecer um tipo de [ligação](../automation-connections.md) personalizado para usar na sua conta Automation adicionando um ficheiro de metadados opcional ao seu módulo. Este ficheiro especifica um tipo de ligação Azure Automation a ser utilizado com os cmdlets do módulo na sua conta Automation. Para isso, é preciso saber primeiro como ser autor de um módulo PowerShell. Ver como escrever um módulo de [script PowerShell](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

![Utilize uma ligação personalizada no portal Azure](../media/modules/connection-create-new.png)

O ficheiro que especifica as propriedades do tipo de ligação ** &lt;chama-se&gt;MóduloName -Automation.json** e encontra-se na pasta do módulo do seu ficheiro **.zip** comprimido. Este ficheiro contém os campos de uma ligação que são necessárias para ligar ao sistema ou servir o módulo que o módulo representa. A configuração permite a criação de um tipo de ligação na Automação Azure. Utilizando este ficheiro pode definir os nomes de campo, tipos e se os campos estão encriptados ou opcionais para o tipo de ligação do módulo. O exemplo seguinte é um modelo no formato de ficheiro **.json** que define o nome do utilizador e as propriedades da palavra-passe:

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

## <a name="best-practices-for-authoring-modules"></a>Boas práticas para módulos de autoria

Recomendamos que siga as considerações nesta secção quando é autor de um módulo PowerShell para utilização na Automação Azure.

### <a name="version-folder"></a>Pasta de versão

NÃO inclua uma pasta de versão dentro do pacote **.zip** para o seu módulo.  Esta questão é menos preocupante para os livros de execução, mas causa um problema com o serviço de Configuração do Estado. A Azure Automation cria automaticamente a pasta da versão quando o módulo é distribuído em nódosos geridos pelo DSC. Se existir uma pasta de versão, acaba-se por ter duas instâncias. Aqui está uma estrutura de pasta de exemplo para um módulo DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>Informação de ajuda

Inclua uma sinopse, descrição e ajude uri para cada cmdlet no seu módulo. No PowerShell, pode definir informações de ajuda `Get-Help` para cmdlets utilizando o cmdlet. O exemplo que se segue mostra como definir uma sinopse e ajudar o URI num ficheiro de módulo **.psm1:**

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

  Fornecer esta informação mostra `Get-Help` texto de ajuda através do cmdlet na consola PowerShell. Este texto também é apresentado no portal Azure.

  ![Ajuda do Módulo de Integração](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Tipo de ligação

Se o módulo se ligar a um serviço externo, defina um tipo de [ligação](#adding-a-connection-type-to-your-module). Cada cmdlet no módulo deve aceitar um objeto de ligação (uma instância desse tipo de ligação) como parâmetro. Os utilizadores mapeiam parâmetros do ativo de ligação aos parâmetros correspondentes do cmdlet cada vez que chamam cmdlet. Com base no exemplo do livro de execução `ContosoConnection` acima, utiliza um ativo de conexão Contoso chamado para aceder aos recursos da Contoso e devolver dados do serviço externo.

  No exemplo seguinte, os campos são `UserName` mapeados para as propriedades e `Password` propriedades de um `PSCredential` objeto e depois passados para o cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Uma forma mais fácil e melhor de abordar este comportamento é passar diretamente o objeto de ligação ao cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Pode ativar comportamentos semelhantes para os seus cmdlets, permitindo-lhes aceitar um objeto de ligação diretamente como parâmetro, em vez de apenas campos de ligação para parâmetros. Normalmente, pretende um parâmetro definido para cada um, para que um utilizador que não utilize a Automatização Azure possa ligar para os seus cmdlets sem construir um hashtable para agir como objeto de ligação. O conjunto `UserAccount`de parâmetros é utilizado para passar as propriedades do campo de ligação. `ConnectionObject`Permite-lhe passar a ligação diretamente.

### <a name="output-type"></a>Tipo de saída

Defina o tipo de saída para todos os cmdlets do seu módulo. Definir um tipo de saída para um cmdlet permite que o IntelliSense, no momento da conceção, o ajude a determinar as propriedades de saída do cmdlet para uma utilização durante a criação. Esta prática é especialmente útil durante a autoria gráfica do livro de execução automation, para a qual o conhecimento em tempo de design é fundamental para uma experiência fácil do utilizador com o seu módulo.

Adicione `[OutputType([<MyOutputType>])]` `MyOutputType` onde é um tipo válido. Para saber `OutputType`mais sobre , consulte [funções OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). O seguinte código é `OutputType` um exemplo de adição a um cmdlet:

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

  Este comportamento é semelhante à funcionalidade "tipo à frente" da saída de um cmdlet no PowerShell ISE sem ter de o executar.

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Estado de Cmdlet

Faça todos os cmdlets no seu módulo apátridas. Vários trabalhos de livro `AppDomain` de corridas podem simultaneamente funcionar no mesmo processo e caixa de areia. Se houver algum Estado partilhado nesses níveis, os empregos podem afetar-se mutuamente. Este comportamento pode levar a problemas intermitentes e difíceis de diagnosticar. Aqui está um exemplo do que não fazer:

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

### <a name="module-dependency"></a>Dependência do módulo

Certifique-se de que o módulo está totalmente contido num pacote xcopy-able. Os módulos Azure Automation são distribuídos nas caixas de areia da Automação quando os livros executam. Os módulos devem funcionar independentemente do hospedeiro que os executa. 

Você deve ser capaz de fechar e mover um pacote de módulos e fazê-lo funcionar normalmente quando importado para o ambiente PowerShell de outro anfitrião. Para que isso aconteça, certifique-se de que o módulo não depende de quaisquer ficheiros fora da pasta do módulo que seja fechado quando o módulo é importado para a Automação Azure. 

O seu módulo não deve depender de quaisquer configurações únicas de registo num hospedeiro. Um exemplo são as definições feitas quando um produto é instalado. 

Certifique-se de que todos os ficheiros do módulo têm caminhos com menos de 140 caracteres. Quaisquer caminhos com mais de 140 caracteres causam problemas com a importação de livros de corridas. Se não seguir esta melhor prática, o módulo não é utilizável na Automação Azure.  

### <a name="references-to-azurerm-and-az"></a>Referências ao AzureRM e ao Az

Se fizer referência ao [módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) no seu módulo, certifique-se de que também não está a fazer referência ao AzureRM. Não é possível utilizar o módulo Az em conjunto com o módulo AzureRM. Az é suportado em livros de execução, mas não é importado por padrão. Para conhecer o módulo Az e considerações a ter em conta, consulte o suporte do [módulo Az na Automação Azure.](../az-modules.md)

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a criação de módulos PowerShell, consulte [escrever um Módulo PowerShell do Windows](/powershell/scripting/developer/windows-powershell).
