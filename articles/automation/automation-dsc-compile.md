---
title: Compilar configurações na configuração de estado de automatização do Azure
description: Este artigo descreve como compilar configurações de Desired State Configuration (DSC) para a automatização do Azure.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 09/10/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f2c6f45081b78d330033570ff322f90cd06e50dd
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594275"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Compilar configurações de DSC na configuração de estado de automatização do Azure

Pode compilar configurações de Desired State Configuration (DSC) de duas formas com a configuração de estado de automatização do Azure: no portal do Azure e com o Windows PowerShell. A tabela seguinte ajuda-o a determinar quando deve utilizar o método com base nas características de cada:

**Portal do Azure**

- Método mais simples com a interface de utilizador interativa
- Formulário para fornecer valores de parâmetros simples
- Controlar facilmente o estado da tarefa
- Acesso autenticado com o início de sessão do Azure

**Windows PowerShell**

- Chamar a partir de linha de comandos com cmdlets do Windows PowerShell
- Pode ser incluído numa solução automatizada com vários passos
- Fornecer valores de parâmetros simples e complexas
- Controlar o estado da tarefa
- Cliente necessário para suportar os cmdlets do PowerShell
- Passar ConfigurationData
- Compilar configurações que utilizam credenciais

Assim que decidir um método de compilação, utilize os procedimentos seguintes para iniciar a compilação.

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>Compilar uma configuração de DSC com o portal do Azure

1. A partir da sua conta de automatização, clique em **State configuration (DSC)**.
1. Clique nas **configurações** separador, em seguida, clique no nome da configuração para compilar.
1. Clique em **compilar**.
1. Se a configuração não tem parâmetros, lhe for pedido para confirmar se deseja compilá-lo. Se a configuração tiver parâmetros, o **compilar configuração** é aberto o painel para fornecer valores de parâmetros. Consulte o seguinte [ **parâmetros básicos** ](#basic-parameters) secção para saber mais sobre os parâmetros.
1. O **tarefa de compilação** página é aberta para que pode controlar o estado da tarefa de compilação e as configurações de nó (documentos de configuração do MOF), causou seja colocada no servidor de solicitação de configuração de estado de automatização do Azure.

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>Compilar uma configuração de DSC com o Windows PowerShell

Pode usar [ `Start-AzureRmAutomationDscCompilationJob` ](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) para iniciar a compilação com o Windows PowerShell. O código de exemplo seguinte inicia compilação de uma configuração de DSC chamada **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzureRmAutomationDscCompilationJob` Devolve um objeto de tarefa de compilação que pode utilizar para monitorizar o estado. Em seguida, pode utilizar este objeto de tarefa de compilação com [`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob)
para determinar o estado da tarefa de compilação, e [`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput)
Para ver os respetivos fluxos (saída). O código de exemplo seguinte inicia compilação do **SampleConfig** configuração, tem de aguardar até que ele foi concluída e, em seguida, exibe seus fluxos.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

## <a name="basic-parameters"></a>Parâmetros básicos

Declaração de parâmetro em configurações de DSC, incluindo tipos de parâmetros e propriedades, funciona tal como nos runbooks de automatização do Azure. Ver [a partir de um runbook na automatização do Azure](automation-starting-a-runbook.md) para saber mais sobre os parâmetros do runbook.

O exemplo seguinte utiliza dois parâmetros, chamados **FeatureName** e **IsPresent**, para determinar os valores das propriedades do **ParametersExample.sample** nó configuração, gerada durante a compilação.

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

Pode compilar configurações de DSC que utilizam parâmetros básicos, no portal de configuração de estado de automatização do Azure ou com o Azure PowerShell:

### <a name="portal"></a>Portal

No portal, pode introduzir valores de parâmetro depois de clicar em **compilar**.

![Parâmetros de configuração de compilação](./media/automation-dsc-compile/DSC_compiling_1.png)

### <a name="powershell"></a>PowerShell

O PowerShell requer parâmetros num [hashtable](/powershell/module/microsoft.powershell.core/about/about_hash_tables) em que a chave corresponde ao nome do parâmetro e o valor igual ao valor de parâmetro.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Para obter informações sobre passando PSCredentials como parâmetros, consulte [ativos de credencial](#credential-assets) abaixo.

## <a name="composite-resources"></a>Recursos compostos

**Recursos compostos** permitem-lhe utilizar configurações de DSC como recursos aninhados dentro de uma configuração. Isto permite-lhe aplicar várias configurações para um único recurso. Consulte [recursos compostos: Utilizar uma configuração de DSC como um recurso](/powershell/dsc/authoringresourcecomposite) para saber mais sobre **recursos compostos**.

> [!NOTE]
> Para que **recursos compostos** para compilar corretamente, tem primeiro de garantir que quaisquer recursos de DSC que depende da composição primeiro estão instalados no repositório de módulos de conta de automatização do Azure ou não importar corretamente.

Para adicionar um DSC **recursos compostos**, tem de adicionar o módulo de recursos para um arquivo morto (*. zip). Vá para o repositório de módulos na sua conta de automatização do Azure. Em seguida, clique no botão "Adicionar um módulo".

![Adicionar módulo](./media/automation-dsc-compile/add_module.png)

Navegue para o diretório onde está localizado o arquivo. Selecione o ficheiro de arquivo e clique em OK.

![Selecione o módulo](./media/automation-dsc-compile/select_dscresource.png)

É direcionado novamente para o diretório de módulos, onde pode monitorizar o estado dos seus **recursos compostos** enquanto a Desempacote e registra a automatização do Azure.

![Importar recursos compostos](./media/automation-dsc-compile/register_composite_resource.png)

Assim que o módulo é registrado, em seguida, pode clicar no mesmo para validar que o **recursos compostos** estão agora disponíveis para ser utilizado numa configuração.

![Validar recursos compostos](./media/automation-dsc-compile/validate_composite_resource.png)

Em seguida, pode chamar o **recursos compostos** em sua configuração da seguinte forma:

```powershell
Node ($AllNodes.Where{$_.Role -eq 'WebServer'}).NodeName
{
    DomainConfig myCompositeConfig
    {
        DomainName = $DomainName
        Admincreds = $Admincreds
    }

    PSWAWebServer InstallPSWAWebServer
    {
        DependsOn = '[DomainConfig]myCompositeConfig'
    }
}
```

## <a name="configurationdata"></a>ConfigurationData

**ConfigurationData** permite que separe estrutural configuração a partir de qualquer configuração específicos do ambiente ao utilizar o PowerShell DSC. Ver [separar "O que" a "Where" no PowerShell DSC](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) para saber mais sobre **ConfigurationData**.

> [!NOTE]
> Pode usar **ConfigurationData** ao compilar a configuração de estado de automatização do Azure com o Azure PowerShell, mas não no portal do Azure.

Utiliza a seguinte configuração de exemplo DSC **ConfigurationData** através da **$ConfigurationData** e **$AllNodes** palavras-chave. Também tem do [ **xWebAdministration** módulo](https://www.powershellgallery.com/packages/xWebAdministration/) para este exemplo:

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

É possível compilar a configuração de DSC anterior com o PowerShell. O PowerShell seguinte adiciona duas configurações de nó para o servidor de solicitação de configuração de estado de automatização do Azure: **ConfigurationDataSample.MyVM1** e **ConfigurationDataSample.MyVM3**:

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

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

## <a name="assets"></a>Elementos

Referências de ativos são os mesmos na configuração de estado de automatização do Azure e de runbooks. Veja o seguinte para obter mais informações:

- [Certificados](automation-certificates.md)
- [Ligações](automation-connections.md)
- [Credenciais](automation-credentials.md)
- [Variáveis](automation-variables.md)

### <a name="credential-assets"></a>Ativos de credencial

Configurações de DSC na automatização do Azure podem fazer referência a recursos de credencial da automatização com o `Get-AutomationPSCredential` cmdlet. Se uma configuração tem um parâmetro que tem um **PSCredential** escrever, pode utilizar o `Get-AutomationPSCredential` cmdlet passando o nome da cadeia de um recurso de credenciais da automatização do Azure para o cmdlet para obter a credencial. Em seguida, pode utilizar esse objeto para o parâmetro que requerem a **PSCredential** objeto. Em segundo plano, o recurso de credencial da automatização do Azure com esse nome é recuperado e passado para a configuração. O exemplo abaixo mostra isso em ação.

Mantendo credenciais seguras em configurações de nó (documentos de configuração do MOF), é necessário criptografar as credenciais no ficheiro MOF de configuração de nó. No entanto, atualmente tem de pedir ao DSC de PowerShell é muito bem as credenciais ter saída em texto simples durante a geração do MOF de configuração de nó, porque o PowerShell DSC não sabe que automatização do Azure vai encriptar todo o arquivo MOF após sua geração por meio de uma tarefa de compilação.

Pode dizer ao DSC de PowerShell que há problema as credenciais ter a saída em texto simples na configuração do nó gerado MOFs usando [ **ConfigurationData**](#configurationdata). Deve passar `PSDscAllowPlainTextPassword = $true` via **ConfigurationData** para o nome de cada bloco de nó que aparece na configuração do DSC e utiliza as credenciais.

O exemplo seguinte mostra uma configuração de DSC que utiliza um recurso de credenciais de automatização.

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

É possível compilar a configuração de DSC anterior com o PowerShell. O PowerShell seguinte adiciona duas configurações de nó para o servidor de solicitação de configuração de estado de automatização do Azure: **CredentialSample.MyVM1** e **CredentialSample.MyVM2**.

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

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> Quando a compilação for concluída poderá receber um erro a indicar: **O módulo "Management" não foi importado porque o snap-in "Management" já foi importado.** Este aviso pode ser ignorado com segurança.

## <a name="partial-configuration"></a>Configuração parcial

Configuração de estado da automatização do Azure suporta a utilização de [configurações parciais](https://docs.microsoft.com/en-us/powershell/dsc/pull-server/partialconfigs).
Neste cenário, DSC está configurado para gerir várias configurações de forma independente e cada configuração é retreieved da automatização do Azure.
No entanto, apenas uma configuração pode ser atribuída a um nó por conta de automatização.
Isso significa que se estiver a utilizar duas configurações para um nó vai precisar de duas contas de automatização.
Para obter mais informações sobre como as equipes podem trabalhar em conjunto para gerir os servidores em colaboração usando configuração como código ver [função do DSC de compreensão num Pipeline CI/CD](https://docs.microsoft.com/en-us/powershell/dsc/overview/authoringadvanced).

## <a name="importing-node-configurations"></a>Importação de configurações de nó

Também pode importar configurações de nó (MOFs) que foram compiladas fora do Azure. Uma vantagem disso é que as configurações de nó podem ser assinadas. Uma configuração de nó assinado é verificada localmente num nó gerido por agente DSC, garantindo que a configuração a ser aplicada para o nó é proveniente de uma origem autorizada.

> [!NOTE]
> Pode utilizar Importar assinado configurações para a sua conta de automatização do Azure, mas a automatização do Azure não suporta atualmente compilar configurações assinadas.

> [!NOTE]
> Um ficheiro de configuração de nó tem de ser não maior do que 1 MB para permiti-lo ser importado para a automatização do Azure.

Para obter mais informações sobre como assinar configurações de nó, veja [melhorias no WMF 5.1 - como a configuração de início de sessão e módulo](/powershell/wmf/5.1/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Importar uma configuração de nó no portal do Azure

1. A partir da sua conta de automatização, clique em **State configuration (DSC)** sob **gestão de configuração**.
1. Na **State configuration (DSC)** página, clique nas **configurações** separador, em seguida, clique em **+ adicionar**.
1. No **importação** página, clique no ícone de pasta junto aos **ficheiro de configuração de nó** caixa de texto para procurar um ficheiro de configuração de nó (MOF) no seu computador local.

   ![Procurar ficheiro local](./media/automation-dsc-compile/import-browse.png)

1. Introduza um nome na **nome da configuração** caixa de texto. Este nome tem de corresponder ao nome da configuração a partir do qual a configuração do nó foi compilada.
1. Clique em **OK**.

### <a name="importing-a-node-configuration-with-powershell"></a>Importar uma configuração de nó com o PowerShell

Pode utilizar o [Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) cmdlet para importar uma configuração de nó para sua conta de automatização.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Passos Seguintes

- Para começar a utilizar, consulte o artigo [introdução à configuração de estado de automatização do Azure](automation-dsc-getting-started.md)
- Para obter informações sobre como compilar configurações de DSC para que pode atribuí-las a nós de destino, consulte [compilar configurações na configuração de estado de automatização do Azure](automation-dsc-compile.md)
- Para referência de cmdlets do PowerShell, consulte [cmdlets de configuração de estado de automatização do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [preços de configuração de estado de automatização do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo do uso de configuração de estado de automatização do Azure num pipeline de implementação contínua, consulte [contínua através do Azure Automation estado de configuração da implementação e Chocolatey](automation-dsc-cd-chocolatey.md)
