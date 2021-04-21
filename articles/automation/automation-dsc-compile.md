---
title: Compilar configurações de DSC na Configuração do Estado da Automação Azure
description: Este artigo diz como compilar configurações de Configuração de Estado Desejada (DSC) para Azure Automation.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4e0a8bc3057bc098855eb7ff65a8feeb83b7c746
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834827"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>Compilar configurações de DSC na Configuração do Estado da Automação Azure

Pode compilar configurações de configuração de estado desejada (DSC) na Configuração do Estado da Automação Azure nas seguintes formas:

- Serviço de compilação de configuração do estado de Azure
  - Método de principiante com interface de utilizador interativa
   - Facilmente rastreia o estado de trabalho

- Windows PowerShell
  - Ligue do Windows PowerShell para a estação de trabalho local ou para construir o serviço
  - Integrar-se com o pipeline de teste de desenvolvimento
  - Fornecer valores de parâmetros complexos
  - Trabalhar com dados de nó e não-nó à escala
  - Melhoria significativa do desempenho

Também pode utilizar modelos de Gestor de Recursos Azure com a extensão de Configuração de Estado (DSC) Azure Desired para empurrar configurações para os seus VMs Azure. A extensão Azure DSC utiliza o quadro do Agente VM Azure para entregar, decretar e reportar sobre as configurações do DSC em execução em VMs Azure. Para obter detalhes de compilação utilizando modelos do Gestor de Recursos Azure, consulte a extensão de [configuração do estado desejada com os modelos do Gestor de Recursos Azure](../virtual-machines/extensions/dsc-template.md#details). 

## <a name="compile-a-dsc-configuration-in-azure-state-configuration"></a>Compilar uma configuração DSC na configuração do estado de Azure

### <a name="portal"></a>Portal

1. Na sua conta Demôm automação, clique na **configuração do Estado (DSC)**.
1. Clique no **separador Configurações** e, em seguida, clique no nome de configuração para compilar.
1. Clique **em Compile**.
1. Se a configuração não tiver parâmetros, é solicitado que confirme se pretende compilá-la. Se a configuração tiver parâmetros, a lâmina **de configuração compilar** abre-se para que possa fornecer valores de parâmetros.
1. A página De Trabalho de Compilação é aberta para que possa acompanhar o estado do trabalho de compilação. Também pode utilizar esta página para rastrear as configurações do nó (documentos de configuração MOF) colocadas no servidor de puxar por sistema de configuração do Estado da Automação Azure.

### <a name="azure-powershell"></a>Azure PowerShell

Pode utilizar [start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) para começar a compilar com o Windows PowerShell. O seguinte código de amostra começa a compilação de uma configuração DSC chamada **SampleConfig**.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob` retorna um objeto de trabalho de compilação que pode usar para rastrear o estado do trabalho. Em seguida, pode utilizar este objeto de trabalho de compilação com [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) para determinar o estado do trabalho de compilação, e [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) para visualizar os seus fluxos (saída). A amostra que se segue inicia a compilação da configuração SampleConfig, aguarda até estar concluída e, em seguida, exibe os seus fluxos.

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

A declaração de parâmetros nas configurações de DSC, incluindo os tipos de parâmetros e propriedades, funciona da mesma forma que nos livros de cálculo da Azure Automation. Consulte [Iniciar um livro de bordo na Azure Automation](./start-runbooks.md) para saber mais sobre os parâmetros do runbook.

O exemplo a seguir utiliza `FeatureName` e `IsPresent` parâmetros para determinar os valores das propriedades na configuração do nó de **amostra ParametersExample.sample,** gerada durante a compilação.

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

Pode compilar configurações de DSC que utilizem parâmetros básicos no portal de Configuração do Estado da Automação Azure ou com a Azure PowerShell.

#### <a name="portal"></a>Portal

No portal, pode introduzir valores de parâmetros depois de clicar em **Compile**.

![Configuração compilar parâmetros](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell requer parâmetros num [haxixe,](/powershell/module/microsoft.powershell.core/about/about_hash_tables)onde a chave corresponde ao nome do parâmetro e o valor é igual ao valor do parâmetro.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Para obter informações sobre a passagem de `PSCredential` objetos como parâmetros, consulte [os ativos da Credencial](#credential-assets).

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Compilar configurações que contenham recursos compósitos na Azure Automation

A funcionalidade **Recursos Compostos** permite-lhe utilizar configurações DSC como recursos aninhados dentro de uma configuração. Esta funcionalidade permite a aplicação de múltiplas configurações a um único recurso. Consulte [recursos compósitos: Utilizar uma configuração DSC como recurso](/powershell/scripting/dsc/resources/authoringresourcecomposite) para aprender mais sobre recursos compósitos.

> [!NOTE]
> De modo que as configurações que contêm recursos compósitos compilam corretamente, você deve primeiro importar para a Azure Automation quaisquer recursos DSC que os compósitos dependem. A adição de um recurso composto DSC não é diferente de adicionar qualquer módulo PowerShell à Azure Automation. Este processo está documentado em [Gerir Módulos na Azure Automation.](./shared-resources/modules.md)

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Gerir ConfigurationData ao compilar configurações na Azure Automation

`ConfigurationData` é um parâmetro DSC incorporado que permite separar a configuração estrutural de qualquer configuração específica do ambiente enquanto utiliza o DSC PowerShell. Para obter mais informações, consulte [a separação de "O quê" de "Onde" no PowerShell DSC](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/).

> [!NOTE]
> Ao compilar na Configuração do Estado da Automação Azure, pode utilizar `ConfigurationData` no Azure PowerShell, mas não no portal Azure.

A configuração DSC do exemplo a seguir `ConfigurationData` utiliza-se através das `$ConfigurationData` `$AllNodes` palavras-chave e das palavras-chave. Também precisa do [módulo xWebAdministration](https://www.powershellgallery.com/packages/xWebAdministration/) para este exemplo.

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

Pode compilar a configuração DSC anterior com o Windows PowerShell. O seguinte script adiciona duas configurações de nó ao serviço de puxar configuração do estado de automação Azure: **ConfigurationDataSample.MyVM1** e **ConfigurationDataSample.MyVM3**.

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

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Trabalhar com ativos na Azure Automation durante a compilação

As referências de ativos são as mesmas tanto na Configuração do Estado da Automação Azure como nos livros de execução. Para obter mais informações, consulte o seguinte:

- [Certificados](./shared-resources/certificates.md)
- [Ligações](automation-connections.md)
- [Credenciais](./shared-resources/credentials.md)
- [Variáveis](./shared-resources/variables.md)

#### <a name="credential-assets"></a>Ativos credenciais

As configurações de DSC na Azure Automation podem fazer referência aos ativos credenciais de automação utilizando o `Get-AutomationPSCredential` cmdlet. Se uma configuração tiver um parâmetro que especifique um `PSCredential` objeto, utilize `Get-AutomationPSCredential` passando o nome de corda de um ativo credencial Azure Automation para o cmdlet para recuperar a credencial. Em seguida, utilize esse objeto para o parâmetro que requer o `PSCredential` objeto. Nos bastidores, o ativo credencial Azure Automation com esse nome é recuperado e passado para a configuração. O exemplo abaixo mostra este cenário em ação.

Manter as credenciais seguras nas configurações dos nó (documentos de configuração MOF) requer encriptar as credenciais no ficheiro MOF de configuração de nó. Atualmente deve dar permissão ao PowerShell DSC para obter credenciais de produção em texto simples durante a geração MOF de configuração de nó. A PowerShell DSC não tem conhecimento de que a Azure Automation encripta todo o ficheiro MOF após a sua geração através de um trabalho de compilação.

Pode dizer ao PowerShell DSC que não faz mal que as credenciais sejam outputadas em texto simples na configuração do nó gerado MOFs utilizando dados de configuração. Deve passar `PSDscAllowPlainTextPassword = $true` por cada nome de bloco de nó que aparece na `ConfigurationData` configuração DSC e utiliza credenciais.

O exemplo a seguir mostra uma configuração DSC que utiliza um ativo credencial Automation.

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

Pode compilar a configuração DSC anterior com o PowerShell. O seguinte código PowerShell adiciona duas configurações de nó ao servidor de puxar o estado de azure Automation: **CredentialSample.MyVM1** e **CredentialSample.MyVM2**.

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
> Quando a compilação estiver completa, poderá receber a mensagem de erro `The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.` Pode ignorar esta mensagem com segurança.

## <a name="compile-your-dsc-configuration-in-windows-powershell"></a>Compilar a sua configuração DSC no Windows PowerShell

O processo de compilação de configurações de DSC no Windows PowerShell está incluído na documentação DSC powerShell [Write, Compile e Apply a Configuration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Pode executar este processo a partir de uma estação de trabalho de desenvolvedor ou dentro de um serviço de construção, como [Azure DevOps](https://dev.azure.com). Em seguida, pode importar os ficheiros MOF produzidos compilando a configuração para o serviço de Configuração do Estado Azure.

A compilação no Windows PowerShell também fornece a opção de assinar o conteúdo de configuração. O agente DSC verifica uma configuração de nó assinado localmente num nó gerido. A verificação garante que a configuração aplicada ao nó provém de uma fonte autorizada.

Também pode importar configurações de nó (ficheiros MOF) que tenham sido compiladas fora de Azure. A importação inclui a compilação de uma estação de trabalho do desenvolvedor ou de um serviço como [a Azure DevOps](https://dev.azure.com). Esta abordagem tem múltiplas vantagens, incluindo desempenho e fiabilidade.

> [!NOTE]
> Um ficheiro de configuração de nó não deve ser superior a 1 MB para permitir que a Azure Automation o importe.

Para obter mais informações sobre a assinatura de configurações de nó, consulte [Melhorias no WMF 5.1 - Como assinar a configuração e o módulo](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Importe uma configuração de nó no portal Azure

1. Na sua conta Demômes automatizada, clique na **configuração do Estado (DSC)** em **Gestão de Configuração.**
1. Na página de configuração do Estado (DSC), clique no **separador Configurações** e, em seguida, clique em **Adicionar**.
1. Na página 'Importar', clique no ícone da pasta ao lado do campo **'Ficheiro de Configuração** do nó' para navegar por um ficheiro MOF de configuração de nó no computador local.

   ![Procure por arquivo local](./media/automation-dsc-compile/import-browse.png)

1. Introduza um nome no campo **Nome de Configuração.** Este nome deve corresponder ao nome da configuração a partir da qual foi compilada a configuração do nó.
1. Clique em **OK**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Importe uma configuração de nó com Azure PowerShell

Pode utilizar o [cmdlet Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) para importar uma configuração de nó na sua conta de Automação.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Passos seguintes

- Para começar, consulte [Começar com a Configuração do Estado da Automação Azure](automation-dsc-getting-started.md).
- Para saber sobre a compilação de configurações de DSC para que possa atribuí-las aos nós-alvo, consulte [as configurações do Compile DSC na Configuração do Estado da Automação Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](/powershell/module/az.automation).
- Para obter informações sobre preços, consulte [os preços de configuração do Estado da Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Para um exemplo de utilização da Configuração do Estado num gasoduto de implantação contínua, consulte [Configurar uma implementação contínua com chocolate.](automation-dsc-cd-chocolatey.md)
