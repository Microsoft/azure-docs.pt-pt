---
title: Compilando configurações na configuração de estado da automação do Azure
description: Este artigo descreve como compilar configurações de DSC (configuração de estado desejado) para a automação do Azure.
services: automation
ms.subservice: dsc
ms.date: 09/10/2018
ms.topic: conceptual
ms.openlocfilehash: d7f22e5042f301d7c16573318b6ddd1585f1e350
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770004"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Compilando configurações DSC na configuração de estado da automação do Azure

Você pode compilar configurações de DSC (configuração de estado desejado) de duas maneiras com a configuração de estado da automação do Azure: no Azure e no Windows PowerShell. A tabela a seguir ajuda a determinar quando usar o método com base nas características de cada um:

- Serviço de compilação de configuração de estado do Azure
  - Método para iniciantes com interface do usuário interativa
   - Rastreie facilmente o estado do trabalho

- Windows PowerShell
  - Chamada do Windows PowerShell na estação de trabalho local ou serviço de compilação
  - Integrar com pipeline de teste de desenvolvimento
  - Fornecer valores de parâmetro complexos
  - Trabalhar com dados de nó e não nó em escala
  - Melhoria significativa no desempenho

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Compilando uma configuração DSC na configuração de estado do Azure

### <a name="portal"></a>Portal

1. Na sua conta de automação, clique em **configuração de estado (DSC)** .
1. Clique na guia **configurações** e, em seguida, clique no nome da configuração para compilar.
1. Clique em **Compilar**.
1. Se a configuração não tiver parâmetros, será solicitado que você confirme se deseja compilá-lo. Se a configuração tiver parâmetros, a folha de **configuração de compilação** será aberta para que você possa fornecer valores de parâmetro. Consulte a seção de [**parâmetros básicos**](#basic-parameters) a seguir para obter mais detalhes sobre os parâmetros.
1. A página **trabalho de compilação** é aberta para que você possa acompanhar o status do trabalho de compilação e as configurações do nó (documentos de configuração do MOF) que ele fez para ser colocado no servidor de pull da configuração do estado de automação do Azure.

### <a name="azure-powershell"></a>Azure PowerShell

Você pode usar [`Start-AzAutomationDscCompilationJob`](/powershell/module/az.automation/start-azautomationdsccompilationjob) para iniciar a compilação com o Windows PowerShell. O código de exemplo a seguir inicia a compilação de uma configuração DSC chamada **SampleConfig**.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob` retorna um objeto de trabalho de compilação que você pode usar para controlar seu status. Você pode usar esse objeto de trabalho de compilação com [`Get-AzAutomationDscCompilationJob`](/powershell/module/az.automation/get-azautomationdsccompilationjob)
para determinar o status do trabalho de compilação e [`Get-AzAutomationDscCompilationJobOutput`](/powershell/module/az.automation/get-azautomationdscconfiguration)
para exibir seus fluxos (saída). O código de exemplo a seguir inicia a compilação da configuração **SampleConfig** , aguarda até que ela seja concluída e, em seguida, exibe seus fluxos.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

###  <a name="basic-parameters"></a>Parâmetros básicos

A declaração de parâmetro em configurações DSC, incluindo tipos de parâmetro e propriedades, funciona da mesma forma que nos runbooks de automação do Azure. Consulte [iniciando um runbook na automação do Azure](automation-starting-a-runbook.md) para saber mais sobre parâmetros de runbook.

O exemplo a seguir usa dois parâmetros chamados **FeatureName** e **ispresente**, para determinar os valores das propriedades na configuração do nó **ParametersExample. Sample** , gerada durante a compilação.

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

Você pode compilar configurações DSC que usam parâmetros básicos no portal de configuração de estado da automação do Azure ou com Azure PowerShell:

#### <a name="portal"></a>Portal

No portal, você pode inserir valores de parâmetro depois de clicar em **Compilar**.

![Parâmetros de compilação de configuração](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

O PowerShell requer parâmetros em uma [Hashtable](/powershell/module/microsoft.powershell.core/about/about_hash_tables) em que a chave corresponde ao nome do parâmetro e o valor é igual ao valor do parâmetro.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Para obter informações sobre como passar PSCredentials como parâmetros, consulte [ativos de credencial](#credential-assets) abaixo.

### <a name="compiling-configurations-in-azure-automation-that-contain-composite-resources"></a>Compilando configurações na automação do Azure que contêm recursos de composição

Os **recursos de composição** permitem que você use configurações de DSC como recursos aninhados dentro de uma configuração. Isso permite que você aplique várias configurações a um único recurso. Consulte [recursos de composição: usando uma configuração DSC como um recurso](/powershell/scripting/dsc/resources/authoringresourcecomposite) para saber mais sobre os **recursos de composição**.

> [!NOTE]
> Para que as configurações que contêm **recursos de composição** sejam compiladas corretamente, primeiro você deve garantir que todos os recursos de DSC dos quais a composição depende sejam importados primeiro na automação do Azure.

Adicionar um **recurso de composição** de DSC não é diferente de adicionar qualquer módulo do PowerShell à automação do Azure.
A instrução passo a passo para esse processo está documentada no artigo [gerenciar módulos na automação do Azure](/azure/automation/shared-resources/modules).

### <a name="managing-configurationdata-when-compiling-configuration-in-azure-automation"></a>Gerenciando o ConfigurationData ao compilar a configuração na automação do Azure

O **ConfigurationData** permite separar a configuração estrutural de qualquer configuração específica do ambiente ao usar o DSC do PowerShell. Consulte separando ["o que" de "onde" na DSC do PowerShell](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) para saber mais sobre **ConfigurationData**.

> [!NOTE]
> Você pode usar o **ConfigurationData** ao compilar na configuração de estado da automação do Azure usando Azure PowerShell, mas não no portal do Azure.

O exemplo de configuração DSC a seguir usa **ConfigurationData** por meio das palavras-chave **$ConfigurationData** e **$AllNodes** . Você também precisa do [módulo **xWebAdministration** ](https://www.powershellgallery.com/packages/xWebAdministration/) para este exemplo:

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

Você pode compilar a configuração de DSC anterior com o Windows PowerShell. O script a seguir adiciona duas configurações de nó ao serviço de pull de configuração de estado da automação do Azure: **ConfigurationDataSample. MyVM1** e **ConfigurationDataSample. MyVM3**:

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

### <a name="working-with-assets-in-azure-automation-during-compilation"></a>Trabalhando com ativos na automação do Azure durante a compilação

As referências de ativos são as mesmas na configuração de estado de automação do Azure e runbooks. Para mais informações, consulte:

- [Certificados](automation-certificates.md)
- [Ligações](automation-connections.md)
- [Credenciais](automation-credentials.md)
- [Variáveis](automation-variables.md)

#### <a name="credential-assets"></a>Ativos de credencial

As configurações de DSC na automação do Azure podem referenciar ativos de credencial de automação usando o cmdlet `Get-AutomationPSCredential`. Se uma configuração tiver um parâmetro que tenha um tipo **PSCredential** , você poderá usar o cmdlet `Get-AutomationPSCredential` passando o nome da cadeia de caracteres de um ativo de credencial de automação do Azure para o cmdlet para recuperar a credencial. Em seguida, você pode usar esse objeto para o parâmetro que requer o objeto **PSCredential** . Nos bastidores, o ativo de credencial de automação do Azure com esse nome é recuperado e passado para a configuração. O exemplo a seguir mostra isso em ação.

Manter as credenciais seguras em configurações de nó (documentos de configuração do MOF) requer a criptografia das credenciais no arquivo MOF de configuração de nó. No entanto, atualmente você deve informar ao DSC do PowerShell que as credenciais sejam emitidas em texto sem formatação durante a geração de MOF de configuração de nó, porque o DSC do PowerShell não sabe que a automação do Azure estará criptografando todo o arquivo MOF após sua geração por meio de um trabalho de compilação.

Você pode informar ao DSC do PowerShell que é possível que as credenciais sejam emitidas em texto sem formatação no MOFs de configuração de nó gerado usando dados de configuração. Você deve passar `PSDscAllowPlainTextPassword = $true` via **ConfigurationData** para o nome de cada bloco de nó que aparece na configuração DSC e usa credenciais.

O exemplo a seguir mostra uma configuração DSC que usa um ativo de credencial de automação.

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

Você pode compilar a configuração de DSC anterior com o PowerShell. O PowerShell a seguir adiciona duas configurações de nó ao servidor de pull de configuração de estado da automação do Azure: **CredentialSample. MyVM1** e **CredentialSample. MyVM2**.

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
> Quando a compilação for concluída, você poderá receber um erro informando: **o módulo ' Microsoft. PowerShell. Management ' não foi importado porque o snap-in ' Microsoft. PowerShell. Management ' já foi importado.** Esse aviso pode ser ignorado com segurança.

## <a name="compiling-configurations-in-windows-powershell-and-publishing-to-azure-automation"></a>Compilando configurações no Windows PowerShell e publicando na automação do Azure

Você também pode importar configurações de nó (MOFs) que foram compiladas fora do Azure.
Isso inclui a compilação de uma estação de trabalho de desenvolvedor ou em um serviço como o [Azure DevOps](https://dev.azure.com).
Há várias vantagens para essa abordagem, incluindo desempenho e confiabilidade.
A compilação no Windows PowerShell também fornece a opção de assinar o conteúdo de configuração.
Uma configuração de nó assinado é verificada localmente em um nó gerenciado pelo agente de DSC, garantindo que a configuração aplicada ao nó venha de uma fonte autorizada.

> [!NOTE]
> Um arquivo de configuração de nó não deve ter mais de 1 MB para permitir que ele seja importado para a automação do Azure.

Para obter mais informações sobre como assinar configurações de nó, consulte [melhorias no WMF 5,1-como assinar a configuração e o módulo](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="compiling-a-configuration-in-windows-powershell"></a>Compilando uma configuração no Windows PowerShell

O processo para compilar configurações DSC no Windows PowerShell está incluído na documentação do DSC do PowerShell [gravar, compilar e aplicar uma configuração](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Isso pode ser executado em uma estação de trabalho de desenvolvedor ou em um serviço de compilação, como o [Azure DevOps](https://dev.azure.com).

O arquivo MOF ou arquivos produzidos pela compilação da configuração podem então ser importados diretamente no serviço de configuração de estado do Azure.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Importando uma configuração de nó no portal do Azure

1. Na sua conta de automação, clique em **configuração de estado (DSC)** em **Gerenciamento de configuração**.
1. Na página **configuração de estado (DSC)** , clique na guia **configurações** e, em seguida, clique em **+ Adicionar**.
1. Na página **importar** , clique no ícone de pasta ao lado da caixa de texto **arquivo de configuração de nó** para procurar um arquivo de configuração de nó (MOF) no computador local.

   ![Procurar arquivo local](./media/automation-dsc-compile/import-browse.png)

1. Insira um nome na caixa de texto **nome da configuração** . Esse nome deve corresponder ao nome da configuração da qual a configuração de nó foi compilada.
1. Clique em **OK**.

### <a name="importing-a-node-configuration-with-azure-powershell"></a>Importando uma configuração de nó com Azure PowerShell

Você pode usar o cmdlet [Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) para importar uma configuração de nó para sua conta de automação.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Passos seguintes

- Para começar, consulte [introdução à configuração de estado de automação do Azure](automation-dsc-getting-started.md)
- Para saber mais sobre como compilar configurações DSC para que você possa atribuí-las aos nós de destino, consulte [compilando configurações na configuração de estado da automação do Azure](automation-dsc-compile.md)
- Para referência de cmdlet do PowerShell, consulte [cmdlets de configuração do estado de automação do Azure](/powershell/module/az.automation)
- Para obter informações sobre preços, consulte [preços de configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de como usar a configuração de estado de automação do Azure em um pipeline de implantação contínua, consulte [implantação contínua usando configuração de estado de automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
