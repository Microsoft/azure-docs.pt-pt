---
title: Utilizar módulos na Automatização do Azure
description: Este artigo descreve como gerir módulos na Automação Azure.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2a567b156c3a7e0c48e2c2f8db2d268e0eb3307c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770947"
---
# <a name="manage-modules-in-azure-automation"></a>Utilizar módulos na Automatização do Azure

A Azure Automation permite-lhe importar módulos PowerShell para ativar os cmdlets em livros de execução e recursos DSC em configurações DSC. Os módulos utilizados na Automação Azure incluem:

* [Azure PowerShell Az.Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)
* [Azure PowerShell AzureRM.Automation](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)
* Módulo `Orchestrator.AssetManagement.Cmdlets` interno para o agente Log Analytics para Windows
* [Kit de Ferramentas de Autoria AzureAutomation](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)
* Outros módulos PowerShell
* Módulos personalizados que cria 

Ao criar uma conta de Automação, a Azure Automation importa alguns módulos por padrão. Ver [módulos Predefinidos](#default-modules).

Quando a Azure Automation executa trabalhos de compilação de runbook e DSC, ele carrega os módulos em caixas de areia onde os livros de execução podem ser executados e as configurações dSC podem compilar. A automatização também coloca automaticamente quaisquer recursos DSC em módulos no servidor de puxar DSC. As máquinas podem puxar os recursos quando aplicam as configurações do DSC.

>[!NOTE]
>Certifique-se de importar apenas os módulos de que os seus livros de execução e configurações dSC realmente precisam. Não importe o módulo de rollup, por exemplo, Az.Automation, em qualquer caso.

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](../automation-update-azure-modules.md).

## <a name="default-modules"></a>Módulos padrão

A tabela seguinte lista módulos que a Azure Automation importa por padrão quando cria a sua conta De automação. A automatização pode importar versões mais recentes destes módulos. No entanto, não é possível remover a versão original da sua conta Automation mesmo que apague uma versão mais recente. Note que estes módulos predefinidos incluem vários módulos AzureRM. 

> [!NOTE]
> Não recomendamos alterar módulos e livros de execução em contas de Automação que contenham quaisquer soluções. 

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


Os módulos de automação são preferidos nos seus livros de execução e configurações DSC. No entanto, a Azure Automation não importa automaticamente o módulo Rollup Az em quaisquer contas de Automação novas ou existentes. Para mais informações sobre o trabalho com estes módulos, consulte [os módulos Migrating para Az](#migrating-to-az-modules).

## <a name="internal-cmdlets"></a>Cmdlets internos

A tabela a seguir define os cmdlets internos suportados pelo `Orchestrator.AssetManagement.Cmdlets` módulo. Utilize-os nos seus livros de execução e configurações dSC para interagir com os ativos da Automação dentro da conta Automation. Os cmdlets são projetados para recuperar segredos de variáveis encriptadas, credenciais e conexões encriptadas. 

> [!NOTE]
> Os cmdlets Azure PowerShell não conseguem obter os segredos de ativos que os cmdlets internos podem recuperar. 

|Nome|Descrição|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Início-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Trabalho de Automação de Espera|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Estes cmdlets internos estão disponíveis num Trabalhador do Livro híbrido do Windows, mas não num Trabalhador de Runbook Híbrido Linux. São executados a partir de uma caixa de areia Orquestradora, que é usada pelo trabalhador híbrido.  O seu uso não requer uma ligação implícita ao Azure, como quando se utiliza um Run As conta para a autenticação.

Em vez de utilizar os cmdlets internos, utilize os cmdlets Az ou AzureRM para livros de execução e configurações que funcionam diretamente no computador ou contra recursos no seu ambiente. Nestes casos, deve ligar-se implicitamente ao Azure quando utilizar os cmdlets, como quando utilizar uma conta Run As para autenticar o Azure. 

## <a name="modules-supporting-get-automationpscredential"></a>Módulos de suporte get-AutomationPSCredential

Para o desenvolvimento local utilizando o Kit `Get-AutomationPSCredential` de Ferramentas de Autor de Automação Azure, o cmdlet faz parte do conjunto [AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9). Para o Azure trabalhar com o contexto `Orchestrator.AssetManagement.Cmdlets`Automation, o cmdlet está em . Para saber mais sobre o uso de credenciais na Automação Azure, consulte [os ativos credenciais na Automação Azure.](credentials.md)

## <a name="migrating-to-az-modules"></a>Migrar para módulos Az

Há várias coisas a ter em conta ao utilizar os módulos Az na Automação Azure:

* Soluções de nível superior na sua conta Automation podem usar livros e módulos. Por isso, editar livros de execução ou atualizar módulos pode potencialmente causar problemas com as suas soluções. Deve testar cuidadosamente todos os livros e soluções numa conta de Automação separada antes de importar os módulos Az. 

* Quaisquer modificações nos módulos podem afetar negativamente os [VMs de início/paragem durante a solução fora](../automation-solution-vm-management.md)de horas . 

* Importar um módulo Az para a sua conta Automation não importa automaticamente o módulo na sessão powerShell que os livros de execução usam. Os módulos são importados para a sessão PowerShell nas seguintes situações:

    * Quando um livro de corridas invoca um cmdlet de um módulo
    * Quando um livro de resta importa `Import-Module` o módulo explicitamente com o cmdlet
    * Quando um livro importa outro módulo dependente

Depois de ter concluído a migração dos seus módulos, não tente iniciar os livros de execução utilizando módulos AzureRM na conta Automation. Também é recomendado não importar ou atualizar módulos AzureRM na conta. Considere a conta migrada para Az.Automation, e opere apenas com módulos Az. 

>[!IMPORTANT]
>Quando cria uma nova conta Automation, a Azure Automation instala os módulos AzureRM por padrão. Ainda pode atualizar os livros de execução tutoriais com cmdlets AzureRM. No entanto, não deve executar estes livros de execução.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Parar e desmarcar todos os livros que usam módulos AzureRM

Para garantir que não executa quaisquer livros de execução existentes que utilizem módulos AzureRM, pare e desmarque todos os livros de execução afetados. Pode ver quais os horários existentes e quais os horários a remover executando código semelhante a este exemplo:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

É importante rever cada horário separadamente para garantir que pode reagendar no futuro para os seus livros, se necessário.

### <a name="import-the-az-modules"></a>Importar os módulos Az

Esta secção diz como importar os módulos Az no portal Azure. Lembre-se de importar apenas os módulos Az de que precisa, e não todo o módulo Az.Automation. Uma vez que [a Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) é uma dependência dos outros módulos Az, certifique-se de importar este módulo antes de qualquer outro.

1. Na sua conta de Automação, selecione **Módulos** em **Recursos Partilhados**. 
2. Clique na **Galeria Browse** para abrir a página da Galeria De Navegação.  
3. Na barra de pesquisa, introduza o `Az.Accounts`nome do módulo, por exemplo, . 
4. Na página do Módulo PowerShell, clique **em Importar** para importar o módulo para a sua conta Deautomação.

    ![Importar módulos para a conta de Automação](../media/modules/import-module.png)

Este processo de importação também pode ser feito através da [PowerShell Gallery,](https://www.powershellgallery.com) procurando o módulo para importar. Assim que encontrar o módulo, selecione-o, escolha o separador **De automação Azure** e clique em **Implementar para automação Azure**.

![Importar módulos diretamente da galeria](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Teste os seus livros de execução

Depois de importar os módulos Az para a conta Automation, pode começar a editar os livros de execução para utilizar os novos módulos. A maioria dos cmdlets tem os mesmos nomes que para os módulos AzureRM, exceto que o prefixo AzureRM (ou AzureRm) foi alterado para Az. Para obter uma lista de módulos que não seguem esta convenção de nomeação, consulte [a lista de exceções](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Uma forma de testar a modificação de um livro de `Enable-AzureRmAlias -Scope Process` execução para utilizar os novos cmdlets é utilizando no início do livro de execução. Ao adicionar este comando ao seu livro de execução, o script pode ser executado sem alterações.

## <a name="authoring-modules"></a>Módulos de autoria

Recomendamos que siga as considerações nesta secção quando é autor de um módulo PowerShell para utilização na Automação Azure.

### <a name="references-to-azurerm-and-az"></a>Referências ao AzureRM e ao Az

Se fizer referência aos módulos Az no seu módulo, certifique-se de que também não está a fazer referência aos módulos AzureRM. Não pode usar os dois conjuntos de módulos ao mesmo tempo. 

### <a name="version-folder"></a>Pasta de versão

NÃO inclua uma pasta de versão no pacote **.zip** para o seu módulo.  Esta questão é menos preocupante para os livros de execução, mas causa um problema com o serviço de Configuração do Estado (DSC). A Azure Automation cria automaticamente a pasta da versão quando o módulo é distribuído em nódosos geridos pelo DSC. Se existir uma pasta de versão, acaba-se por ter duas instâncias. Aqui está uma estrutura de pasta de exemplo para um módulo DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>Informação de ajuda

Inclua uma sinopse, descrição e ajude uri para cada cmdlet no seu módulo. No PowerShell, pode definir informações de ajuda `Get-Help` para cmdlets utilizando o cmdlet. O exemplo que se segue mostra como definir uma sinopse e ajudar o URI num ficheiro de módulo **.psm1.**

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

Se o módulo se ligar a um serviço externo, defina um tipo de [ligação](#adding-a-connection-type-to-your-module). Cada cmdlet no módulo deve aceitar um objeto de ligação (uma instância desse tipo de ligação) como parâmetro. Os utilizadores mapeiam parâmetros do ativo de ligação aos parâmetros correspondentes do cmdlet cada vez que chamam cmdlet. O exemplo do livro de corridas seguinte, com base no `ContosoConnection` exemplo na secção anterior, utiliza um ativo de ligação Contoso chamado para aceder aos recursos da Contoso e devolver dados do serviço externo. Neste exemplo, os campos são mapeados para as `UserName` propriedades e `Password` propriedades de um `PSCredential` objeto e depois passados para o cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Uma forma mais fácil e melhor de abordar este comportamento é passando diretamente o objeto de ligação ao cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Pode ativar comportamentos semelhantes para os seus cmdlets, permitindo-lhes aceitar um objeto de ligação diretamente como parâmetro, em vez de apenas campos de ligação para parâmetros. Normalmente, pretende um parâmetro definido para cada um, para que um utilizador que não utilize a Automatização Azure possa ligar para os seus cmdlets sem construir um hashtable para agir como objeto de ligação. O conjunto `UserAccount` de parâmetros é utilizado para passar as propriedades do campo de ligação. `ConnectionObject`Permite-lhe passar a ligação diretamente.

### <a name="output-type"></a>Tipo de saída

Defina o tipo de saída para todos os cmdlets do seu módulo. Definir um tipo de saída para um cmdlet permite que o Tempo de Design IntelliSense ajude a determinar as propriedades de saída do cmdlet durante a autoria. Esta prática é especialmente útil durante a autoria de um livro gráfico, para o qual o conhecimento em tempo de design é fundamental para uma experiência fácil do utilizador com o seu módulo.

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

Certifique-se de que o módulo está totalmente contido numa embalagem que pode ser copiada com xcopy. Os módulos Azure Automation são distribuídos nas caixas de areia da Automação quando os livros executam. Os módulos devem funcionar independentemente do hospedeiro que os executa. 

Você deve ser capaz de fechar e mover um pacote de módulos e fazê-lo funcionar normalmente quando importado para o ambiente PowerShell de outro anfitrião. Para que isso aconteça, certifique-se de que o módulo não depende de quaisquer ficheiros fora da pasta do módulo que seja fechado quando o módulo é importado para a Automação Azure. 

O seu módulo não deve depender de quaisquer configurações únicas de registo num hospedeiro. Exemplos são as definições que são feitas quando um produto é instalado. 

### <a name="module-file-paths"></a>Caminhos de arquivo de módulos

Certifique-se de que todos os ficheiros do módulo têm caminhos com menos de 140 caracteres. Quaisquer caminhos com mais de 140 caracteres de comprimento causam problemas com a importação de livros de execução. A Azure Automation não pode importar um ficheiro com o tamanho `Import-Module`do caminho superior a 140 caracteres na sessão powerShell com .

## <a name="importing-modules"></a>Módulos de importação

Esta secção define várias formas de importar um módulo para a sua conta Desmótica. 

### <a name="import-modules-in-azure-portal"></a>Módulos de importação no portal Azure

Para importar um módulo no portal Azure:

1. Navegue para a sua conta de Automação.
2. Selecione **Módulos** em **Recursos Partilhados**.
3. Clique **em adicionar um módulo**. 
4. Selecione o ficheiro **.zip** que contém o seu módulo.
5. Clique em **OK** para começar a importar processo.

### <a name="import-modules-using-powershell"></a>Módulos de importação usando powerShell

Pode utilizar o cmdlet [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) para importar um módulo na sua conta Automation. O cmdlet leva um URL para um pacote de zíper .zip módulo.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Também pode utilizar o mesmo cmdlet para importar um módulo da PowerShell Gallery diretamente. Certifique-se `ModuleName` de `ModuleVersion` agarrar e da [Galeria PowerShell.](https://www.powershellgallery.com)

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
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
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="adding-a-connection-type-to-your-module"></a>Adicionar um tipo de ligação ao seu módulo

Pode fornecer um tipo de [ligação](../automation-connections.md) personalizado para usar na sua conta Automation adicionando um ficheiro de metadados opcional ao seu módulo. Este ficheiro especifica um tipo de ligação Azure Automation a ser utilizado com os cmdlets do módulo na sua conta Automation. Para saber mais sobre a autoria de um módulo PowerShell, consulte [Como Escrever um Módulo de Script PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7).

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

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a utilização de módulos Azure PowerShell, consulte [Start start with Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0).
* Para saber mais sobre a criação de módulos PowerShell, consulte [escrever um Módulo PowerShell do Windows](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7).
