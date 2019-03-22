---
title: Gerir módulos na automatização do Azure
description: Este artigo descreve como gerir módulos na automatização do Azure
services: automation
ms.service: automation
ms.subservice: shared-resources
author: georgewallace
ms.author: gwallace
ms.date: 03/13/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fa7f5d3fb38eb1dbca51dec9b73dca3c998436aa
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2019
ms.locfileid: "57909163"
---
# <a name="manage-modules-in-azure-automation"></a>Gerir módulos na automatização do Azure

A automatização do Azure fornece a capacidade de importar os módulos do PowerShell para sua conta de automatização a ser utilizada pelos runbooks do PowerShell com base. Estes módulos podem ser módulos personalizados que criou, da galeria do PowerShell, ou os módulos AzureRM e Az para o Azure.

## <a name="import-modules"></a>Importar módulos

Existem várias formas que pode importar um módulo para a sua conta de automatização. As secções seguintes mostram as diferentes formas de importar um módulo.

> [!NOTE]
> O caminho máximo de um arquivo num módulo a ser utilizado na automatização do Azure é 140 caracteres. Qualquer caminho com mais de 140 carateres não poderá ser importado para a sessão do PowerShell com `Import-Module`.

### <a name="powershell"></a>PowerShell

Pode utilizar o [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) para importar um módulo para a sua conta de automatização. O cmdlet assume um url para um pacote zip do módulo.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>Portal do Azure

No portal do Azure, navegue até à sua conta de automatização e selecione **módulos** sob **recursos partilhados**. Clique em **+ adicionar um módulo**. Selecione um **. zip** ficheiro que contém seu módulo e clique em **Ok** para começar a importar o processo.

### <a name="powershell-gallery"></a>Galeria do PowerShell

Módulos da galeria do PowerShell podem ser importados a partir da [galeria do PowerShell](https://www.powershellgallery.com) diretamente ou a partir da sua conta de automatização.

Para importar um módulo da galeria do PowerShell, aceda a https://www.powershellgallery.com e procure o módulo que pretende importar. Clique em **implementar na automatização do Azure** sobre o **automatização do Azure** separador sob **opções de instalação**. Esta ação abre o portal do Azure. Sobre o **importação** página, selecione a sua conta de automatização e clique em **OK**.

![Módulo de importação da galeria do PowerShell](../media/modules/powershell-gallery.png)

Também pode importar módulos da galeria do PowerShell diretamente a partir da sua conta de automatização. Na sua conta de automatização, selecione **módulos** sob **recursos partilhados**. Na página de módulos, clique em **Galeria de procura**. Esta ação abre o **Galeria de procura** página. Pode utilizar esta página para a galeria do PowerShell para um módulo de pesquisa. Selecione o módulo que pretende importar e clique em **importar**. Sobre o **importe** página, clique em **OK** para iniciar o processo de importação.

![Importar de galeria do PowerShell do portal do Azure](../media/modules/gallery-azure-portal.png)

## <a name="internal-cmdlets"></a>Cmdlets de interno

Segue-se uma lista dos cmdlets no interno `Orchestrator.AssetManagement.Cmdlets` módulo é importado para cada conta de automatização. Estes cmdlets estão acessíveis nos seus runbooks e configurações de DSC e permitem-lhe interagir com os seus ativos na sua conta de automatização. Além disso, os cmdlets internos permitem-lhe obter segredos a partir encriptados **variável** valores, **credenciais**e criptografado **ligação** campos. Os cmdlets do PowerShell do Azure não é possível obter estes segredos. Estes cmdlets não requerem implicitamente ligar para o Azure, ao usá-los. Isso é benéfico para cenários em que tem uma ligação, como uma conta Run As que tem de utilizar para autenticar para o Azure.

|Name|Descrição|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Adicionar um tipo de ligação ao seu módulo

Pode fornecer um personalizado [tipo de ligação](../automation-connections.md) para utilização na sua conta de automatização através da adição de um ficheiro opcional para o seu módulo. Este ficheiro é um ficheiro de metadados que especifica um tipo de ligação da automatização do Azure para ser utilizado com os cmdlets do módulo na conta de automatização. Para conseguir isso primeiro tem de saber como criar um módulo do PowerShell. Para obter mais informações sobre a criação do módulo, consulte [como escrever um módulo de Script do PowerShell](/powershell/developer/module/how-to-write-a-powershell-script-module).

![Utilizar uma ligação personalizada no portal do Azure](../media/modules/connection-create-new.png)

Para adicionar um tipo de ligação da automatização do Azure, o módulo tem de conter um ficheiro com o nome `<ModuleName>-Automation.json` que especifica as propriedades de tipo de ligação. Este é um ficheiro json, que é colocado dentro da pasta do módulo do seu ficheiro. zip comprimido. Este ficheiro contém os campos de uma ligação que é necessário para ligar ao sistema ou serviço que o módulo representa. Esta configuração acaba de criar um tipo de ligação na automatização do Azure. Usando esse arquivo pode definir os nomes de campos, tipos, e se os campos devem ser encriptados ou opcionais para o tipo de ligação do módulo. O exemplo seguinte é um modelo no formato de ficheiro json que define uma propriedade de nome de utilizador e palavra-passe:

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

## <a name="module-best-practices"></a>Práticas recomendadas do módulo

Módulos do PowerShell podem ser importados para a automatização do Azure para tornar os seus cmdlets disponíveis para utilização nos runbooks e os recursos de DSC disponíveis para utilização nas configurações de DSC. Nos bastidores, a automatização do Azure armazena estes módulos e no e tempo de execução da tarefa de compilação de DSC da tarefa de runbook, carrega-os para as sandboxes de automatização do Azure onde executar runbooks e configurações de DSC compilam. Quaisquer recursos de DSC nos módulos também são automaticamente colocados no servidor de solicitação de DSC de automatização. Eles podem ser solicitados pelas máquinas quando estas são aplicadas as configurações de DSC.

Recomendamos que considere o seguinte quando cria um módulo do PowerShell para utilização na automatização do Azure:

* Inclua um resumo, uma descrição e um URI de ajuda para cada cmdlet no módulo. No PowerShell, pode definir determinadas informações de ajuda para os cmdlets para permitir ao utilizador receber ajuda sobre a utilização dos mesmos com o cmdlet **Get-Help**. O exemplo seguinte mostra como definir uma sinopse e ajudar a URI para num ficheiro. psm1 módulo:

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

  Fornecer estas informações mostra esta ajuda relativa à utilização a **Get-Help** cmdlet na consola do PowerShell. Esta descrição é também apresentada no portal do Azure.

  ![Ajuda do Módulo de Integração](../media/modules/module-activity-description.png)

* Se o módulo de ligar a um serviço externo, esta deve conter uma [tipo de ligação](#add-a-connection-type-to-your-module). Cada cmdlet no módulo deve ser capaz de considerar um objeto de ligação (uma instância desse tipo de ligação) como um parâmetro. Isso permite que os utilizadores mapear os parâmetros do recurso de ligação para parâmetros correspondentes do cmdlet sempre que chamarem um cmdlet. Com base no exemplo de runbook acima, ele usa um recurso de ligação de Contoso de exemplo chamado ContosoConnection para aceder aos recursos da Contoso e devolve os dados do serviço externo.

  No exemplo a seguir, os campos são mapeados para as propriedades de nome de utilizador e palavra-passe de um `PSCredential` de objeto e, em seguida, passado para o cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Uma forma melhor e mais fácil abordar este comportamento consiste na passagem direta do objeto de ligação para o cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Pode ativar o comportamento semelhante ao exemplo anterior para os cmdlets ao permitir que aceitem um objeto de ligação diretamente como um parâmetro, em vez de apenas os campos de ligação para os parâmetros. Normalmente, quer um parâmetro definido para cada um, para que um usuário não utilizar a automatização do Azure possa chamar os seus cmdlets sem construir uma tabela de hash para atuar como o objeto de ligação. O conjunto de parâmetros `UserAccount`, é usado para passar a ligação de propriedades de campo. `ConnectionObject` permite-lhe a passagem direta através da ligação.

* Defina o tipo de saída para todos os cmdlets no módulo. Definir um tipo de saída para um cmdlet permite que o IntelliSense, no momento da conceção, o ajude a determinar as propriedades de saída do cmdlet para uma utilização durante a criação. É especialmente útil durante a automação criação gráfica de runbooks, onde o conhecimento de tempo de design é fundamental para uma experiência de utilizador fácil com o seu módulo.

  Isso pode ser feito pela adição de `[OutputType([<MyOutputType>])]` onde MyOutputType é um tipo válido. Para saber mais sobre OutputType, veja [sobre as funções OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). O código seguinte é um exemplo da adição `OutputType` para um cmdlet:

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

  Este comportamento é semelhante a funcionalidade de "escrita antecipada" da saída de um cmdlet no ISE do PowerShell sem ter de executá-lo.

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

* Crie todos os cmdlets no módulo sem monitorização de estado. Várias tarefas de runbook podem executar em simultâneo no mesmo AppDomain e o mesmo processo e área de segurança. Se houver qualquer estado partilhado nesses níveis, podem afetar tarefas entre si. Esse comportamento pode levar a intermitente e difíceis de diagnosticar problemas.  Eis um exemplo de o que não deve fazer.

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

* O módulo deve estar totalmente incluído num pacote com xcopy. Módulos de automatização do Azure são distribuídos nas sandboxes da automatização quando os runbooks precisam executar. Os módulos tem de funcionar independentemente do anfitrião que estão a executar. Deve ser capaz de zipar e mover de um pacote do módulo e tê-lo a funcionar normalmente quando importados para o ambiente do PowerShell de outro anfitrião. Por ordem para que isso aconteça, o módulo não deve depender de quaisquer ficheiros fora da pasta do módulo. Esta pasta é a pasta que é zipada quando o módulo é importado para a automatização do Azure. O módulo também não deve depender quaisquer definições de registo única num anfitrião, como essas configurações definidas quando um produto é instalado. Todos os ficheiros no módulo devem ter um caminho de menos de 140 carateres. Caminhos com mais de 140 carateres causará problemas de importação de runbook. Se não for seguida esta melhor prática, o módulo não será utilizável na automatização do Azure.  

* Se a referência à [módulos do Azure Powershell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) no seu módulo, certifique-se de que não está a referenciar também `AzureRM`. O `Az` módulo não pode ser utilizado em conjunto com o `AzureRM` módulos. `Az` é suportado em runbooks, mas não são importados por predefinição. Para saber mais sobre o `Az` módulos e considerações a ter em conta, consulte [suporte de módulo de Az na automatização do Azure](../az-modules.md).

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre a criação de Módulos do PowerShell, veja o artigo [Escrever um Módulo do Windows PowerShell](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)