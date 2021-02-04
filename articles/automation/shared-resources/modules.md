---
title: Utilizar módulos na Automatização do Azure
description: Este artigo diz como utilizar módulos PowerShell para permitir cmdlets em runbooks e recursos DSC em configurações DSC.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/01/2021
ms.topic: conceptual
ms.openlocfilehash: a784127cfd6019629f1c2714d0f36850406c3b9d
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99548781"
---
# <a name="manage-modules-in-azure-automation"></a>Utilizar módulos na Automatização do Azure

A Azure Automation utiliza uma série de módulos PowerShell para permitir cmdlets em runbooks e recursos DSC em configurações de DSC. Os módulos suportados incluem:

* [Azure PowerShell Az.Automation](/powershell/azure/new-azureps-module-az).
* [Azure Powershell Azurerm.Automation](/powershell/module/azurerm.automation/).
* Outros módulos PowerShell.
* `Orchestrator.AssetManagement.Cmdlets`Módulo interno.
* Módulos Python 2.
* Módulos personalizados que cria.

Quando cria uma conta Automation, a Azure Automation importa alguns módulos por padrão. Ver [módulos predefinidos](#default-modules).

## <a name="sandboxes"></a>Sandboxes

Quando a Automation executa trabalhos de execução e compilação de DSC, carrega os módulos em caixas de areia onde os livros podem funcionar e as configurações do DSC podem compilar. A automatização também coloca automaticamente quaisquer recursos DSC em módulos no servidor de puxar DSC. As máquinas podem retirar os recursos quando aplicam as configurações DSC.

>[!NOTE]
>Certifique-se de importar apenas os módulos que os seus runbooks e configurações DSC requerem. Não recomendamos importar o módulo Az raiz. Inclui muitos outros módulos que pode não precisar, o que pode causar problemas de desempenho. Importar módulos individuais, como a Az.Compute, em vez disso.

Cloud sandbox suporta um máximo de 48 chamadas de sistema, e restringe todas as outras chamadas por razões de segurança. Outras funcionalidades, como a gestão credencial e algumas redes não são suportadas na caixa de areia da nuvem.

Devido ao número de módulos e cmdlets incluídos, é difícil saber previamente qual dos cmdlets fará chamadas não apoiadas. Geralmente, temos visto problemas com cmdlets que requerem acesso elevado, requerem uma credencial como parâmetro, ou cmdlets relacionados com a rede. Quaisquer cmdlets que realizem operações completas de rede de pilhas não são suportados na caixa de areia, incluindo [o Connect-AipService](/powershell/module/aipservice/connect-aipservice) do módulo AIPService PowerShell e [o Resolve-DnsName](/powershell/module/dnsclient/resolve-dnsname) do módulo DNSClient.

Estas são limitações conhecidas com a caixa de areia. A solução recomendada é implantar um [Trabalhador de Runbook Híbrido](../automation-hybrid-runbook-worker.md) ou utilizar [funções Azure](../../azure-functions/functions-overview.md).

## <a name="default-modules"></a>Módulos predefinidos

A tabela que se segue lista os módulos que a Azure Automation importa por padrão quando cria a sua conta Automation. A automatização pode importar versões mais recentes destes módulos. No entanto, não é possível remover a versão original da sua conta Automation, mesmo que elimine uma versão mais recente. Note que estes módulos predefinidos incluem vários módulos AzureRM.

Os módulos predefinidos também são conhecidos como módulos globais. No portal Azure, a propriedade **do módulo Global** será **verdadeira** ao visualizar um módulo que foi importado quando a conta foi criada.

![Screenshot da propriedade global do módulo em Azure Portal](../media/modules/automation-global-modules.png)

A automatização não importa automaticamente o módulo Raiz Az em quaisquer contas de Automação novas ou existentes. Para saber mais sobre como trabalhar com estes módulos, consulte [os módulos Migratórios para Az.](#migrate-to-az-modules)

> [!NOTE]
> Não recomendamos a alteração de módulos e runbooks em contas de Automação utilizadas para a implementação dos [VMs iniciar/parar durante o período de folga.](../automation-solution-vm-management.md)

|Nome do módulo|Versão|
|---|---|
| AuditoriaPolicyDsc | 1.1.0.0 |
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
| Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PsDscResources | 2.9.0.0 |
| SegurançaPolicyDsc | 2.1.0.0 |
| EstadoConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="az-modules"></a>Módulos AZ

Para a Az.Automation, a maioria dos cmdlets têm os mesmos nomes que os utilizados para os módulos AzureRM, exceto que o `AzureRM` prefixo foi alterado para `Az` . Para obter uma lista de módulos Az que não seguem esta convenção de nomeação, consulte a [lista de exceções.](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)

## <a name="internal-cmdlets"></a>Cmdlets internos

A Azure Automation suporta o módulo interno `Orchestrator.AssetManagement.Cmdlets` do agente Log Analytics para Windows, instalado por predefinição. A tabela a seguir define os cmdlets internos. Estes cmdlets são projetados para serem usados em vez de cmdlets Azure PowerShell para interagir com recursos partilhados. Podem obter segredos de variáveis encriptadas, credenciais e ligações encriptadas.

>[!NOTE]
>Os cmdlets internos só estão disponíveis quando estiver a executar livros no ambiente de caixa de areia Azure, ou num Trabalhador de Runbook Híbrido do Windows. 

|Nome|Descrição|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Note que os cmdlets internos diferem no nome dos cmdlets Az e AzureRM. Os nomes internos dos cmdlet não contêm palavras como `Azure` ou `Az` no substantivo, mas usam a palavra `Automation` . Recomendamos a sua utilização sobre a utilização de cmdlets Az ou AzureRM durante a execução de um livro de correr numa caixa de areia Azure ou num Trabalhador de Runbook Híbrido do Windows. Requerem menos parâmetros e são executados no contexto do seu trabalho que já está a decorrer.

Utilize cmdlets Az ou AzureRM para manipular recursos de Automação fora do contexto de um livro de bordo. 

## <a name="python-modules"></a>Módulos Python

Pode criar livros python 2 na Azure Automation. Para obter informações sobre o módulo Python, consulte [os pacotes Manage Python 2 na Azure Automation](../python-packages.md).

## <a name="custom-modules"></a>Módulos personalizados

A Azure Automation suporta módulos PowerShell personalizados que cria para utilizar com os seus runbooks e configurações DSC. Um tipo de módulo personalizado é um módulo de integração que contém opcionalmente um ficheiro de metadados para definir a funcionalidade personalizada para os cmdlets do módulo. Um exemplo da utilização de um módulo de integração é fornecido no [Adicionar um tipo de ligação](../automation-connections.md#add-a-connection-type).

A Azure Automation pode importar um módulo personalizado para disponibilizar os seus cmdlets. Nos bastidores, armazena o módulo e usa-o nas caixas de areia Azure, tal como acontece com outros módulos.

## <a name="migrate-to-az-modules"></a>Migrar para módulos Az

Esta secção diz como migrar para os módulos Az em Automação. Para obter mais informações, consulte [Migrate Azure PowerShell de AzureRM a Az](/powershell/azure/migrate-from-azurerm-to-az).

Não recomendamos a execução de módulos AzureRM e módulos Az na mesma conta Automation. Quando tem certeza de que quer migrar de AzureRM para Az, o melhor é comprometer-se totalmente com uma migração completa. A automatização reutiliza frequentemente caixas de areia dentro da conta Automation para economizar nos tempos de arranque. Se não fizer uma migração completa de módulos, poderá iniciar um trabalho que utilize apenas módulos AzureRM e, em seguida, iniciar outro trabalho que utilize apenas módulos Az. A caixa de areia logo se despenha, e recebe um erro afirmando que os módulos não são compatíveis. Esta situação resulta em acidentes aleatórios para qualquer livro de bordo ou configuração em particular.

>[!NOTE]
>Quando cria uma nova conta Automation, mesmo após a migração para os módulos Az, a Automation instala por predefinição os módulos AzureRM. Ainda pode atualizar os livros de execução tutoriais com os cmdlets AzureRM. No entanto, não devias publicar estes livros.

### <a name="test-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>Teste os seus runbooks e configurações de DSC antes da migração do módulo

Certifique-se de testar cuidadosamente todos os runbooks e configurações DSC, numa conta de Automação separada, antes de migrar para os módulos Az. 

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Parar e descascar todos os livros que usam módulos AzureRM

Para garantir que não execute nenhum livro de execuções ou configurações DSC existentes que utilizem módulos AzureRM, deve parar e descodificá-lo em todos os livros e configurações afetados. Em primeiro lugar, certifique-se de que revê cada runbook ou configuração DSC e os seus horários separadamente, para garantir que pode reagendar o item no futuro, se necessário.

Quando estiver pronto para remover os seus horários, pode utilizar o portal Azure ou o [cmdlet Remove-AzureRmAutomationSchedule.](/powershell/module/azurerm.automation/remove-azurermautomationschedule) Ver [Remover um horário](schedules.md#remove-a-schedule).

### <a name="remove-azurerm-modules"></a>Remover módulos AzureRM

É possível remover os módulos AzureRM antes de importar os módulos Az. No entanto, se o fizer, pode interromper a sincronização do controlo de origem e causar qualquer script que ainda esteja programado para falhar. Se decidir remover os módulos, consulte [o Desinstalar AzureRM](/powershell/azure/migrate-from-azurerm-to-az#uninstall-azurerm).

### <a name="import-az-modules"></a>Módulos Import Az

Importar um módulo Az na sua conta Automation não importa automaticamente o módulo para a sessão PowerShell que os livros de execução usam. Os módulos são importados para a sessão PowerShell nas seguintes situações:

* Quando um livro invoca um cmdlet de um módulo.
* Quando um livro de bordo importa o módulo explicitamente com o cmdlet [do Módulo de Importação.](/powershell/module/microsoft.powershell.core/import-module)
* Quando um livro importa outro módulo dependente.

Pode importar os módulos Az no portal Azure. Lembre-se de importar apenas os módulos Az de que necessita, não todo o módulo Az.Automation. Como [a Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) é uma dependência para os outros módulos Az, certifique-se de importar este módulo antes de qualquer outro.

1. A partir da sua conta Demômes, em **Recursos Partilhados,** selecione **Módulos.**
2. Selecione **Galeria Browse**.  
3. Na barra de pesquisa, insira o nome do módulo (por exemplo, `Az.Accounts` ).
4. Na página do Módulo PowerShell, **selecione Import** para importar o módulo na sua conta Demôm automação.

    ![Screenshot de módulos de importação na sua conta de Automação](../media/modules/import-module.png)

Você também pode fazer esta importação através da [PowerShell Gallery,](https://www.powershellgallery.com)procurando o módulo para importar. Quando encontrar o módulo, selecione-o e escolha o **separador Azure Automation.** **Selecione Implementar para a Azure Automation**.

![Screenshot de módulos de importação diretamente da PowerShell Gallery](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Teste os seus livros de execução

Depois de importar os módulos Az para a conta Automation, pode começar a editar os seus runbooks e configurações DSC para utilizar os novos módulos. Uma forma de testar a modificação de um livro de bordo para utilizar os novos cmdlets é utilizando o `Enable-AzureRmAlias -Scope Process` comando no início do livro de recortes. Ao adicionar este comando ao seu runbook, o script pode ser executado sem alterações.

## <a name="author-modules"></a>Módulos de autor

Recomendamos que siga as considerações nesta secção quando autorizar um módulo PowerShell personalizado para utilização na Azure Automation. Para preparar o seu módulo para importação, deve criar pelo menos um módulo .psd1, .psm1 ou PowerShell **.dll** ficheiro com o mesmo nome que a pasta do módulo. Em seguida, fecha a pasta do módulo para que a Azure Automation possa importá-la como um único ficheiro. A **embalagem .zip** deve ter o mesmo nome que a pasta do módulo contido.

Para saber mais sobre a autoria de um módulo PowerShell, consulte [Como Escrever um Módulo de Script PowerShell](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

### <a name="version-folder"></a>Pasta de versão

A versão do módulo PowerShell lado a lado permite-lhe utilizar mais do que uma versão de um módulo dentro do PowerShell. Isto pode ser útil se tiver scripts mais antigos que foram testados e apenas funcionar contra uma determinada versão de um módulo PowerShell, mas outros scripts requerem uma versão mais recente do mesmo módulo PowerShell.

Para construir módulos PowerShell para que contenham várias versões, crie a pasta do módulo e, em seguida, crie uma pasta dentro desta pasta de módulos para cada versão do módulo que pretende ser utilizável. No exemplo seguinte, um módulo chamado *TestModule* fornece duas versões, 1.0.0 e 2.0.0.

```dos
TestModule
   1.0.0
   2.0.0
```

Dentro de cada uma das pastas de versão, copie o módulo PowerShell .psm1, .psd1 ou PowerShell **.dll** ficheiros que compõem um módulo na respetiva pasta de versão. Feche a pasta do módulo para que a Azure Automation possa importá-la como um único ficheiro .zip. Embora a Automatização apenas mostre a versão mais alta do módulo importado, se o pacote do módulo contiver versões lado a lado do módulo, todos eles estão disponíveis para utilização nos seus runbooks ou configurações DSC.  

Embora a Automation suporte módulos que contenham versões lado a lado dentro do mesmo pacote, não suporta a utilização de várias versões de um módulo através das importações de pacotes de módulos. Por exemplo, importa **o módulo A,** que contém as versões 1 e 2 na sua conta Automation. Mais tarde atualiza o **módulo A** para incluir as versões 3 e 4, quando importa para a sua conta Automation, apenas as versões 3 e 4 são utilizáveis dentro de quaisquer runbooks ou configurações DSC. Se necessitar de todas as versões - 1, 2, 3 e 4 para estar disponível, o ficheiro .zip que a sua importação deve conter as versões 1, 2, 3 e 4.

Se vai utilizar diferentes versões do mesmo módulo entre os runbooks, deve sempre declarar a versão que pretende utilizar no seu livro de recortes utilizando o `Import-Module` cmdlet e incluir o parâmetro `-RequiredVersion <version>` . Mesmo que a versão que pretende utilizar seja a versão mais recente. Isto porque os trabalhos de runbook podem funcionar na mesma caixa de areia. Se a caixa de areia já tiver carregado explicitamente um módulo de um determinado número de versão, porque um trabalho anterior naquela caixa de areia disse para o fazer, os futuros trabalhos naquela caixa de areia não carregarão automaticamente a versão mais recente desse módulo. Isto porque alguma versão já está carregada na caixa de areia.

Para um recurso DSC, utilize o seguinte comando para especificar uma versão específica:

```powershell
Import-DscResource -ModuleName <ModuleName> -ModuleVersion <version>
```

### <a name="help-information"></a>Informação de ajuda

Inclua uma sinopse, descrição e ajude o URI para cada cmdlet do seu módulo. No PowerShell, pode definir informações de ajuda para cmdlets utilizando o `Get-Help` cmdlet. O exemplo a seguir mostra como definir uma sinopse e ajudar o URI num ficheiro de módulo **.psm1.**

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

  O fornecimento desta informação mostra o texto de ajuda através do `Get-Help` cmdlet na consola PowerShell. Este texto também é apresentado no portal Azure.

  ![Screenshot da ajuda do módulo de integração](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Tipo de ligação

Se o módulo se ligar a um serviço externo, defina um tipo de ligação utilizando um [módulo de integração personalizado.](#custom-modules) Cada cmdlet no módulo deve aceitar uma instância desse tipo de ligação (objeto de ligação) como parâmetro. Os utilizadores mapeiam os parâmetros do ativo de ligação aos parâmetros correspondentes do cmdlet cada vez que chamam um cmdlet.

![Utilize uma ligação personalizada no portal Azure](../media/modules/connection-create-new.png)

O exemplo do seguinte runbook utiliza um ativo de ligação Contoso chamado `ContosoConnection` para aceder aos recursos da Contoso e devolver dados do serviço externo. Neste exemplo, os campos são mapeados para as `UserName` propriedades e propriedades de um objeto e depois `Password` `PSCredential` passados para o cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

Uma forma mais fácil e melhor de abordar este comportamento é passando diretamente o objeto de ligação para o cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

Pode ativar um comportamento semelhante para os seus cmdlets, permitindo-lhes aceitar um objeto de ligação diretamente como parâmetro, em vez de apenas campos de ligação para parâmetros. Normalmente, pretende-se um parâmetro definido para cada um, para que um utilizador que não esteja a utilizar a Automatização possa ligar para os seus cmdlets sem construir um haxixe para funcionar como objeto de ligação. O conjunto de parâmetros `UserAccount` é utilizado para passar as propriedades do campo de ligação. `ConnectionObject` permite-lhe passar a ligação diretamente.

### <a name="output-type"></a>Tipo de saída

Defina o tipo de saída para todos os cmdlets do seu módulo. A definição de um tipo de saída para um cmdlet permite que o tempo de conceção IntelliSense ajude a determinar as propriedades de saída do cmdlet durante a autoria. Esta prática é especialmente útil durante a autoria de runbook gráfico, para o qual o conhecimento do tempo de design é fundamental para uma experiência fácil do utilizador com o seu módulo.

Adicione `[OutputType([<MyOutputType>])]` , onde é um tipo `MyOutputType` válido. Para saber mais `OutputType` sobre , consulte Sobre [funções OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). O seguinte código é um exemplo de adição `OutputType` a um cmdlet:

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

  ![Screenshot do tipo de saída de runbook gráfico](../media/modules/runbook-graphical-module-output-type.png)

  Este comportamento é semelhante à funcionalidade "tipo à frente" da saída de um cmdlet no ambiente de serviço de integração PowerShell, sem ter de o executar.

  ![Screenshot do POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Estado do Cmdlet

Faça todos os cmdlets no seu módulo apátrida. Vários trabalhos de runbook podem simultaneamente funcionar no mesmo `AppDomain` processo e no mesmo processo e caixa de areia. Se houver algum Estado partilhado nesses níveis, os empregos podem afetar-se mutuamente. Este comportamento pode levar a problemas intermitentes e difíceis de diagnosticar. Aqui está um exemplo do que não fazer:

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

Certifique-se de que o módulo está totalmente contido numa embalagem que pode ser copiada utilizando xcopia. Os módulos de automatização são distribuídos nas caixas de areia automations quando os livros de execução são executados. Os módulos devem funcionar independentemente do hospedeiro que os executa.

Você deve ser capaz de fechar e mover um pacote de módulos, e fazê-lo funcionar normalmente quando é importado para o ambiente PowerShell de outro hospedeiro. Para que isso aconteça, certifique-se de que o módulo não depende de quaisquer ficheiros fora da pasta do módulo que seja fechado quando o módulo é importado para a Automação.

O seu módulo não deve depender de configurações de registo únicas num hospedeiro. Exemplos são as definições que são feitas quando um produto é instalado.

### <a name="module-file-paths"></a>Caminhos de arquivo de módulos

Certifique-se de que todos os ficheiros do módulo têm caminhos com menos de 140 caracteres. Quaisquer caminhos com mais de 140 caracteres de comprimento causam problemas com a importação de livros. A automatização não pode importar um ficheiro com o tamanho do caminho superior a 140 caracteres na sessão PowerShell com `Import-Module` .

## <a name="import-modules"></a>Importar módulos

Esta secção define várias formas de importar um módulo para a sua conta Demôm automação.

### <a name="import-modules-in-the-azure-portal"></a>Módulos de importação no portal Azure

Para importar um módulo no portal Azure:

1. Vá à sua conta de Automação.
2. Em **Recursos Partilhados**, selecione **Módulos.**
3. **Selecione Adicione um módulo.**
4. Selecione o ficheiro **.zip** que contém o seu módulo.
5. Selecione **OK** para começar a importar processo.

### <a name="import-modules-by-using-powershell"></a>Importar módulos utilizando o PowerShell

Pode utilizar o [cmdlet New-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule) para importar um módulo na sua conta de Automação. O cmdlet leva um URL para um pacote de módulos .zip.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Também pode utilizar o mesmo cmdlet para importar diretamente um módulo da PowerShell Gallery. Certifique-se de agarrar `ModuleName` e `ModuleVersion` da [PowerShell Gallery](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>Módulos de importação da Galeria PowerShell

Pode importar módulos [PowerShell Gallery](https://www.powershellgallery.com) diretamente da Galeria ou da sua conta Automation.

Para importar um módulo diretamente da Galeria PowerShell:

1. Vá https://www.powershellgallery.com procurar o módulo para importar.
2. Nas **Opções de Instalação**, no **separador Automação Azure,** selecione **Implementar para Azure Automation**. Esta ação abre o portal Azure. 
3. Na página 'Importar', selecione a sua conta Demôm automação e selecione **OK**.

![Screenshot do módulo de importação da PowerShell Gallery](../media/modules/powershell-gallery.png)

Para importar um módulo PowerShell Gallery diretamente da sua conta Demôm automação:

1. Em **Recursos Partilhados**, selecione **Módulos.** 
2. **Selecione A galeria Browse** e, em seguida, procure na Galeria um módulo. 
3. Selecione o módulo para importar e selecione **Import.** 
4. Selecione **OK** para iniciar o processo de importação.

![Screenshot da importação de um módulo da PowerShell Gallery do portal Azure](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Eliminar módulos

Se tiver problemas com um módulo, ou precisar de voltar para uma versão anterior de um módulo, pode eliminá-lo da sua conta Demôm automação. Não é possível eliminar as versões originais dos [módulos predefinidos](#default-modules) que são importados quando cria uma conta Automation. Se o módulo a eliminar for uma versão mais recente de um dos [módulos predefinidos,](#default-modules)volta à versão que foi instalada na sua conta Automation. Caso contrário, qualquer módulo que elimine da sua conta Dem automação é removido.

### <a name="delete-modules-in-the-azure-portal"></a>Eliminar módulos no portal Azure

Para remover um módulo no portal Azure:

1. Vá à sua conta de Automação. Em **Recursos Partilhados**, selecione **Módulos.**
2. Selecione o módulo que pretende remover.
3. Na página do Módulo, selecione **Delete**. Se este módulo for um dos [módulos predefinidos,](#default-modules)volta à versão que existia quando a conta Automation foi criada.

### <a name="delete-modules-by-using-powershell"></a>Eliminar módulos utilizando o PowerShell

Para remover um módulo através do PowerShell, executar o seguinte comando:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a utilização dos módulos Azure PowerShell, consulte [Começar com a Azure PowerShell](/powershell/azure/get-started-azureps).

* Para saber mais sobre a criação de módulos PowerShell, consulte [escrever um módulo Windows PowerShell](/powershell/scripting/developer/module/writing-a-windows-powershell-module).
