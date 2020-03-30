---
title: Gerir módulos em Automação Azure
description: Este artigo descreve como gerir módulos na Automação Azure
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e300bc0f29808215673407d21b65fe329e50ad45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278340"
---
# <a name="manage-modules-in-azure-automation"></a>Gerir módulos em Automação Azure

A Azure Automation fornece a capacidade de importar módulos PowerShell para a sua Conta de Automação para serem utilizados pelos livros de execução baseados na PowerShell. Estes módulos podem ser módulos personalizados que criou, a partir da PowerShell Gallery, ou dos módulos AzureRM e Az para o Azure. Quando se cria uma Conta de Automação, alguns módulos são importados por padrão.

## <a name="import-modules"></a>Importar módulos

Existem várias formas de importar um módulo para a sua Conta de Automação. As seguintes secções mostram as diferentes formas de importar um módulo.

> [!NOTE]
> O caminho máximo de um ficheiro num módulo a utilizar na Automatização Azure é de 140 caracteres. Qualquer caminho com mais de 140 caracteres não poderá ser `Import-Module`importado para a sessão powerShell com .

### <a name="powershell"></a>PowerShell

Pode utilizar o [Módulo De Automação New-AzureRm](/powershell/module/azurerm.automation/new-azurermautomationmodule) para importar um módulo para a sua Conta de Automação. O cmdlet leva uma url para um pacote zip módulo.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Também pode utilizar o mesmo cmdlet para importar um módulo da PowerShell Gallery diretamente. Certifique-se de que agarra o **MóduloNome** e **O MóduloVersão** da [Galeria PowerShell](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Portal do Azure

No portal Azure, navegue para a sua Conta de Automação e selecione **Módulos** em **Recursos Partilhados.** Clique **+ Adicione um módulo**. Selecione um ficheiro **.zip** que contenha o seu módulo e clique em **Ok** para começar a importar o processo.

### <a name="powershell-gallery"></a>Galeria do PowerShell

Os módulos da galeria PowerShell podem ser importados diretamente da [PowerShell Gallery](https://www.powershellgallery.com) ou da sua Conta de Automação.

Para importar um módulo da Galeria https://www.powershellgallery.com PowerShell, vá procurar o módulo que pretende importar. Clique em **Implementar para Automatização Azure** no separador **Automação Azure** em opções de **instalação**. Esta ação abre o portal Azure. Na página **Import,** selecione a sua Conta de Automação e clique **EM OK**.

![Módulo de importação da PowerShell Gallery](../media/modules/powershell-gallery.png)

Também pode importar módulos a partir da PowerShell Gallery diretamente da sua Conta de Automação. Na sua Conta de Automação, selecione **Módulos** em **Recursos Partilhados.** Na página dos módulos clique na **galeria Browse**e, em seguida, procure um módulo na PowerShell Gallery. Selecione o módulo que pretende importar e clique **em Importar**. Na página **de Importação,** clique em **OK** para iniciar o processo de importação.

![PowerShell Gallery importa do portal Azure](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Eliminar módulos

Se tiver problemas com um módulo ou precisar de voltar para uma versão anterior de um módulo, pode eliminá-lo da sua Conta de Automação. Não é possível eliminar a versão original dos [módulos predefinidos](#default-modules) que são importados quando cria uma Conta de Automação. Se o módulo que pretende eliminar for uma versão mais recente de um dos [módulos predefinidos instalados,](#default-modules) irá voltar para a versão que foi instalada com a sua Conta de Automação. Caso contrário, qualquer módulo que apague da sua Conta de Automação será removido.

### <a name="azure-portal"></a>Portal do Azure

No portal Azure, navegue para a sua Conta de Automação e selecione **Módulos** em **Recursos Partilhados.** Selecione o módulo que pretende remover. Na página **Módulo,** selecione **Eliminar**. Se este módulo for um dos [módulos predefinidos,](#default-modules)será revirado para a versão que estava presente quando a Conta de Automação foi criada.

### <a name="powershell"></a>PowerShell

Para remover um módulo através do PowerShell, execute o seguinte comando:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Cmdlets internos

Segue-se uma listagem de cmdlets no módulo interno `Orchestrator.AssetManagement.Cmdlets` que é importado em todas as Conta sugóticas. Estes cmdlets são acessíveis nos seus livros de execução e configurações DSC e permitem interagir com os seus ativos dentro da sua Conta de Automação. Além disso, os cmdlets internos permitem-lhe recuperar segredos de valores **variáveis** encriptados, **credenciais**e campos de **Ligação** encriptados. Os cmdlets Azure PowerShell não são capazes de recuperar estes segredos. Estes cmdlets não requerem que se ligue implicitamente ao Azure ao utilizá-los, como por exemplo, utilizar uma Conta Run As para autenticar o Azure.

>[!NOTE]
>Estes cmdlets internos estão disponíveis num Windows Hybrid Runbook Worker, não estão disponíveis num Trabalhador de Runbook Híbrido Linux. Utilize os módulos [AzureRM.Automation](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) ou [Az correspondentes](../az-modules.md) para livros de execução que funcionam diretamente no computador ou contra recursos no seu ambiente. 
>

|Nome|Descrição|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Início-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Trabalho de Automação de Espera|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Adicione um tipo de ligação ao seu módulo

Pode fornecer um tipo de [ligação](../automation-connections.md) personalizado para utilizar na sua Conta de Automação adicionando um ficheiro opcional ao seu módulo. Este ficheiro é um ficheiro de metadados que especifica um tipo de ligação À Automatização Azure a ser utilizado com os cmdlets do módulo na sua Conta de Automação. Para isso, é preciso saber primeiro como ser autor de um módulo PowerShell. Para obter mais informações sobre a autoria de módulos, consulte Como Escrever um Módulo de [Script PowerShell](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

![Utilize uma ligação personalizada no portal Azure](../media/modules/connection-create-new.png)

Para adicionar um tipo de ligação de automação `<ModuleName>-Automation.json` Azure, o seu módulo deve conter um ficheiro com o nome que especifica as propriedades do tipo de ligação. O ficheiro json é colocado dentro da pasta do módulo do seu ficheiro .zip comprimido. Este ficheiro contém os campos de uma ligação que é necessária para ligar ao sistema ou servir o módulo que o módulo representa. A configuração acaba por criar um tipo de ligação na Automação Azure. Utilizando este ficheiro pode definir os nomes de campo, tipos e se os campos devem ser encriptados ou opcionais, para o tipo de ligação do módulo. O exemplo seguinte é um modelo no formato de ficheiro json que define um nome de utilizador e propriedade de senha:

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

## <a name="module-best-practices"></a>As melhores práticas do módulo

Os módulos PowerShell podem ser importados para a Automatização Azure para disponibilizar os seus cmdlets para utilização dentro de livros de execução e os seus recursos DSC disponíveis para utilização dentro das configurações dSC. Nos bastidores, a Azure Automation armazena estes módulos, e no trabalho de execução de trabalho de compilação dSC, os carrega nas caixas de areia Da Automatização Azure onde os livros executam e as configurações dSC compilam. Quaisquer recursos DSC em módulos também são automaticamente colocados no servidor de puxar Automation DSC. Podem ser puxados por máquinas quando aplicam configurações DSC.

Recomendamos que considere o seguinte quando autor de um módulo PowerShell para utilização na Automação Azure:

* NÃO inclua uma pasta de versão dentro do pacote .zip.  Esta questão é menos preocupante para os livros de execução, mas causará um problema com o serviço de Configuração do Estado.  O Azure Automation criará automaticamente a pasta da versão quando o módulo for distribuído para nós geridos pelo DSC, e se existir uma pasta de versão, acabará por ter duas instâncias.  Por exemplo, estrutura de pastas para um módulo DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Inclua um resumo, uma descrição e um URI de ajuda para cada cmdlet no módulo. No PowerShell, pode definir determinadas informações de ajuda para os cmdlets para permitir ao utilizador receber ajuda sobre a utilização dos mesmos com o cmdlet **Get-Help**. O exemplo que se segue mostra como definir uma sinopse e ajudar o URI num ficheiro de módulo .psm1:

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

  Fornecer esta informação mostra esta ajuda usando o cmdlet **Get-Help** na consola PowerShell. Esta descrição também está exposta no portal Azure.

  ![Ajuda do Módulo de Integração](../media/modules/module-activity-description.png)

* Se o módulo se ligar a um serviço externo, deve conter um tipo de [ligação](#add-a-connection-type-to-your-module). Cada cmdlet no módulo deve ser capaz de considerar um objeto de ligação (uma instância desse tipo de ligação) como um parâmetro. Os utilizadores mapeiam parâmetros do ativo de ligação aos parâmetros correspondentes do cmdlet cada vez que chamam cmdlet. Com base no exemplo do livro de executantes acima, utiliza um ativo de conexão Contoso chamado ContosoConnection para aceder aos recursos contoso e devolver dados do serviço externo.

  No exemplo seguinte, os campos são mapeados para `PSCredential` as propriedades userName e Password de um objeto e depois passados para o cmdlet.

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

  Pode ativar comportamentos como o exemplo anterior para os seus cmdlets, permitindo-lhes aceitar um objeto de ligação diretamente como parâmetro, em vez de apenas campos de ligação para parâmetros. Normalmente, pretende um parâmetro definido para cada um, para que um utilizador que não utilize a Automatização Azure possa ligar para os seus cmdlets sem construir um hashtable para agir como objeto de ligação. O conjunto `UserAccount`de parâmetros é utilizado para passar as propriedades do campo de ligação. `ConnectionObject`Permite-lhe passar a ligação diretamente.

* Defina o tipo de saída para todos os cmdlets do módulo. Definir um tipo de saída para um cmdlet permite que o IntelliSense, no momento da conceção, o ajude a determinar as propriedades de saída do cmdlet para uma utilização durante a criação. É especialmente útil durante a autoria gráfica do livro de execução automation, onde o conhecimento do tempo de design é fundamental para uma experiência fácil do utilizador com o seu módulo.

Adicione `[OutputType([<MyOutputType>])]` onde o MyOutputType é um tipo válido. Para saber mais sobre o OutputType, consulte [sobre funções OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). O seguinte código é `OutputType` um exemplo de adição a um cmdlet:

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

* Crie todos os cmdlets no módulo sem monitorização de estado. Vários trabalhos de runbook podem simultaneamente funcionar no mesmo AppDomain e no mesmo processo e caixa de areia. Se houver algum Estado partilhado nesses níveis, os empregos podem afetar-se mutuamente. Este comportamento pode levar a problemas intermitentes e difíceis de diagnosticar.  Eis um exemplo de o que não deve fazer.

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

* O módulo deve estar totalmente contido num pacote xcopy-able. Os módulos Azure Automation são distribuídos nas caixas de areia da Automação quando os livros de execução precisam de ser executados. Os módulos precisam de funcionar independentemente do hospedeiro em que estão a funcionar. Você deve ser capaz de fechar e mover um pacote de módulos e fazê-lo funcionar normalmente quando importado para o ambiente PowerShell de outro anfitrião. Para que isso aconteça, o módulo não deve depender de quaisquer ficheiros fora da pasta do módulo. Esta pasta é a pasta que fica fechada quando o módulo é importado para a Automação Azure. O módulo também não deve depender de quaisquer configurações únicas de registo num hospedeiro, como as configurações definidas quando um produto é instalado. Todos os ficheiros do módulo devem ter um caminho inferior a 140 caracteres. Quaisquer caminhos com mais de 140 caracteres causarão problemas importando o seu livro de corridas. Se esta melhor prática não for seguida, o módulo não será utilizável na Automação Azure.  

* Se fizer referência aos [módulos Azure Powershell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) no `AzureRM`seu módulo, certifique-se de que também não está a fazer referências . O `Az` módulo não pode ser utilizado `AzureRM` em conjunto com os módulos. `Az`é suportado em livros de execução, mas não são importados por defeito. Para conhecer `Az` os módulos e considerações a ter em conta, consulte o suporte do [módulo Az na Automação Azure.](../az-modules.md)

## <a name="default-modules"></a>Módulos padrão

A tabela seguinte lista os módulos importados por padrão quando é criada uma Conta de Automação. Os módulos listados abaixo podem ter versões mais recentes dos mesmos importados, mas a versão original não pode ser removida da sua Conta de Automação mesmo que apague uma versão mais recente dos mesmos.

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

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a criação de Módulos do PowerShell, veja o artigo [Escrever um Módulo do Windows PowerShell](/powershell/scripting/developer/windows-powershell)
