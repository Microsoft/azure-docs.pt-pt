---
title: Compilação de configurações na Configuração do Estado da Automação Azure
description: Este artigo descreve como compilar configurações de Configuração de Estado Desejado (DSC) para a Automação Azure.
services: automation
ms.subservice: dsc
ms.date: 09/10/2018
ms.topic: conceptual
ms.openlocfilehash: 5462d0fae44217f6217d5be1b321df53c4706aaa
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430572"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Compilação de configurações dSC na configuração do Estado da Automação Azure

Pode compilar as configurações de Configuração do Estado Desejado (DSC) de duas formas com a Configuração do Estado da Automação Azure: em Azure e no Windows PowerShell. A tabela seguinte ajuda-o a determinar quando utilizar o método baseado nas características de cada um:

- Serviço de compilação de configuração do Estado azure
  - Método de principiante com interface interativa de utilizador
   - Facilmente rastrear o estado de trabalho

- Windows PowerShell
  - Chamada do Windows PowerShell sobre estação de trabalho local ou serviço de construção
  - Integrar com o gasoduto de teste de desenvolvimento
  - Fornecer valores complexos de parâmetros
  - Trabalhe com dados de nó e não-nó em escala
  - Melhoria significativa do desempenho

Para obter detalhes de compilação, consulte a extensão de [Configuração do Estado Desejado com modelos](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details)de Gestor de Recursos Azure .

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Compilação de uma configuração DSC na configuração do estado de Azure

### <a name="portal"></a>Portal

1. Na sua conta de Automação, clique na **configuração do Estado (DSC)** .
1. Clique no separador **Configurações** e, em seguida, clique no nome de configuração para compilar.
1. Clique em **Compile**.
1. Se a configuração não tiver parâmetros, é-lhe pedido que confirme se pretende compilar. Se a configuração tiver parâmetros, a lâmina **de configuração de compilação** abre-se para que possa fornecer valores de parâmetros.
1. A página De trabalho de **compilação** é aberta para que possa rastrear o estado do trabalho de compilação, e as configurações do nó (documentos de configuração MOF) que causou para ser colocada no Servidor de Pull De Configuração do Estado da Automatização Azure.

### <a name="azure-powershell"></a>Azure PowerShell

Pode utilizar [`Start-AzAutomationDscCompilationJob`](/powershell/module/az.automation/start-azautomationdsccompilationjob) para começar a compilar com o Windows PowerShell. O seguinte código de amostra inicia a compilação de uma configuração DSC chamada **SampleConfig**.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob` devolve um objeto de trabalho de compilação que pode usar para rastrear o seu estado. Em seguida, pode utilizar este objeto de trabalho de compilação com [`Get-AzAutomationDscCompilationJob`](/powershell/module/az.automation/get-azautomationdsccompilationjob)
para determinar o estado do trabalho de compilação, e [`Get-AzAutomationDscCompilationJobOutput`](/powershell/module/az.automation/get-azautomationdscconfiguration)
para ver os seus fluxos (saída). O seguinte código de amostra inicia a compilação da configuração **SampleConfig,** aguarda até que esteja concluída e, em seguida, exibe os seus fluxos.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>Declarar parâmetros básicos

A declaração de parâmetros nas configurações do DSC, incluindo tipos e propriedades de parâmetros, funciona da mesma forma que nos livros de execução da Automação Azure. Consulte Iniciar um livro de [corridas na Azure Automation](automation-starting-a-runbook.md) para saber mais sobre parâmetros de livro.

O exemplo seguinte utiliza dois parâmetros chamados **FeatureName** e **IsPresent,** para determinar os valores das propriedades na configuração do nó de **amostra ParametersExample.sample,** gerado durante a compilação.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

Pode compilar configurações DSC que utilizam parâmetros básicos no portal de configuração do Estado da Automatização Azure ou com o Azure PowerShell:

#### <a name="portal"></a>Portal

No portal, pode introduzir valores de parâmetros depois de clicar em **Compile**.

![Configuração compilam parâmetros](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell requer parâmetros num [hashtable](/powershell/module/microsoft.powershell.core/about/about_hash_tables) onde a chave corresponde ao nome do parâmetro, e o valor é igual ao valor do parâmetro.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Para obter informações sobre a passagem de PSCredencias como parâmetros, consulte [Os Ativos Credenciais](#credential-assets) abaixo.

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Compilar configurações que contenham recursos compósitos na Automação Azure

A função **Recursos Compósitos** permite-lhe utilizar as configurações dSC como recursos aninhados dentro de uma configuração. Isto permite-lhe aplicar múltiplas configurações num único recurso. Ver [recursos compósitos: Utilizando uma configuração DSC como recurso](/powershell/scripting/dsc/resources/authoringresourcecomposite) para saber mais sobre recursos compósitos.

> [!NOTE]
> Para que as configurações que contenham **Recursos Compósitos** compilem corretamente, deve primeiro garantir que quaisquer Recursos DSC em que o composto depende sejam importados pela primeira vez para a Automação Azure.

Adicionar um **Recurso Compósito** DSC não é diferente de adicionar qualquer módulo PowerShell à Automação Azure.
A instrução passo a passo para este processo está documentada no artigo [Gerir módulos em Automação Azure.](/azure/automation/shared-resources/modules)

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Gerir o ConfigurationData ao compilar configurações na Automação Azure

**A ConfiguraçãoData** permite-lhe separar a configuração estrutural de qualquer configuração específica do ambiente enquanto utiliza o PowerShell DSC. Consulte [a separação "O quê" de "Onde" no PowerShell DSC](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) para saber mais sobre **o ConfigurationData**.

> [!NOTE]
> Pode utilizar o **ConfigurationData** quando estiver a compilar na Configuração do Estado da Automação Azure utilizando o Azure PowerShell, mas não no portal Azure.

O exemplo seguinte A configuração DSC utiliza **o ConfigurationData** através do **$ConfigurationData** e **$AllNodes** palavras-chave. Também precisa do módulo [ **xWebAdministration** ](https://www.powershellgallery.com/packages/xWebAdministration/) para este exemplo:

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

Pode compilar a configuração DSC anterior com o Windows PowerShell. O seguinte script adiciona duas configurações de nó ao Serviço de Pull de Configuração do Estado da Automatização Do Azure: **ConfiguraçãoDataSample.MyVM1** e **ConfiguraçãoDataSample.MyVM3**:

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Trabalho com ativos na Automação Azure durante compilação

As referências de ativos são as mesmas na Configuração e nos livros de execução do Estado da Automação Azure. Para mais informações, consulte o seguinte:

- [Certificados](automation-certificates.md)
- [Ligações](automation-connections.md)
- [Credenciais](automation-credentials.md)
- [Variáveis](automation-variables.md)

#### <a name="credential-assets"></a>Ativos credenciais

As configurações dSC na Automatização Azure podem referenciar ativos credenciais de automatização utilizando o `Get-AutomationPSCredential` cmdlet. Se uma configuração tiver um parâmetro que tenha um tipo **PSCredential,** então pode utilizar o `Get-AutomationPSCredential` cmdlet passando o nome de cadeia de um ativo credencial de automação Azure ao cmdlet para recuperar a credencial. Em seguida, pode utilizar esse objeto para o parâmetro que requer o objeto **PSCredential.** Nos bastidores, o ativo credencial azure Automation com esse nome é recuperado e passado para a configuração. O exemplo abaixo mostra isto em ação.

Manter as credenciais seguras nas configurações do nó (documentos de configuração MOF) requer encriptar as credenciais no ficheiro MOF de configuração do nó. No entanto, atualmente deve dizer à PowerShell DSC que não há problema em as credenciais serem saídas em texto simples durante a geração MOF de configuração do nó, porque o PowerShell DSC não sabe que a Automação Azure estará a encriptar todo o ficheiro MOF após a sua geração através de um trabalho de compilação.

Pode dizer ao PowerShell DSC que não faz mal que as credenciais sejam produzidas em texto simples nos MOFs de configuração do nó gerado utilizando dados de configuração. Deve passar `PSDscAllowPlainTextPassword = $true` através do **ConfigurationData** para o nome de cada bloco de nó que aparece na configuração DSC e utiliza credenciais.

O exemplo seguinte mostra uma configuração DSC que utiliza um ativo credencial automation.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

Pode compilar a configuração DSC anterior com a PowerShell. O seguinte PowerShell adiciona duas configurações de nó ao Servidor de Pull de Configuração do Estado de Automação Azure: **CredentialSample.MyVM1** e **CredentialSample.MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> Quando a compilação estiver concluída, poderá receber um erro que indique: **O módulo 'Microsoft.PowerShell.Management' não foi importado porque o snap-in 'Microsoft.PowerShell.Management' já estava importado.** Este aviso pode ser ignorado com segurança.

## <a name="compiling-configurations-in-windows-powershell"></a>Compilação de configurações no Windows PowerShell

Também pode importar configurações de nó (MOFs) que tenham sido compiladas fora de Azure.
Isto inclui a compilação a partir de uma estação de trabalho de desenvolvedores ou num serviço como [o Azure DevOps](https://dev.azure.com).
Existem múltiplas vantagens nesta abordagem, incluindo desempenho e fiabilidade.
A compilação no Windows PowerShell também fornece a opção de assinar conteúdo de configuração.
Uma configuração de nó assinado é verificada localmente num nó gerido pelo agente DSC, garantindo que a configuração que está sendo aplicada ao nó vem de uma fonte autorizada.

> [!NOTE]
> Um ficheiro de configuração do nó não deve ser superior a 1 MB para permitir a sua importação para a Automação Azure.

Para obter mais informações sobre como assinar configurações de nó, consulte [Melhorias em WMF 5.1 - Como assinar configuração e módulo](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="compile-a-configuration-in-windows-powershell"></a>Compilar uma configuração no Windows PowerShell

O processo para compilar configurações de DSC no Windows PowerShell está incluído na documentação PowerShell DSC [Escrever, Compilar e Aplicar uma Configuração](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Isto pode ser executado a partir de uma estação de trabalho de desenvolvedor ou dentro de um serviço de construção como [O Azure DevOps](https://dev.azure.com).

O ficheiro ou ficheiros MOF produzidos através da compilação da configuração pode então ser importado diretamente para o serviço de Configuração do Estado Do Estado do Azure.

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Importar uma configuração de nó no portal Azure

1. Na sua conta de Automação, clique na **configuração do Estado (DSC)** em Gestão de **Configuração**.
1. Na página **de configuração do Estado (DSC),** clique no separador **Configurações** e, em seguida, clique **+ Adicionar**.
1. Na página **Import,** clique no ícone da pasta ao lado da caixa de texto do Ficheiro de **Configuração** do Nó para procurar um ficheiro de configuração de nó (MOF) no seu computador local.

   ![Procurar arquivo local](./media/automation-dsc-compile/import-browse.png)

1. Introduza um nome na caixa de texto **'Nome configuração'.** Este nome deve coincidir com o nome da configuração a partir da qual a configuração do nó foi compilada.
1. Clique em **OK**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Importar uma configuração de nó com Azure PowerShell

Pode utilizar o [cmdlet Import-AzAutomationDscNodeConfiguração](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) para importar uma configuração de nó na sua conta de automação.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Passos seguintes

- Para começar, veja [Começar com a Configuração do Estado da Automação Azure](automação-dsc-getting-started.md.
- Para aprender sobre a compilação de configurações de DSC para que possa atribuí-las a nós-alvo, consulte [configurações de compilação na Configuração do Estado da Automação Azure](automation-dsc-compile.md).
- Para referência de cmdlet PowerShell, consulte [os cmdlets](/powershell/module/az.automation)de configuração do Estado da Automatização Do Azure .
- Para obter informações sobre preços, consulte os preços de configuração do Estado da [Automatização do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para ver um exemplo de utilização da Configuração do Estado da Automação Azure num gasoduto de implantação contínua, consulte a implantação contínua utilizando a Configuração do Estado de [Automação Azure e o Chocolatey](automation-dsc-cd-chocolatey.md).
