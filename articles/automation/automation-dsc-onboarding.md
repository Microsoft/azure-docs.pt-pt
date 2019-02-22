---
title: Integrar computadores para gestão de configuração de estado de automatização do Azure
description: Como configurar computadores para gestão com a configuração de estado de automatização do Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.topic: conceptual
ms.date: 08/08/2018
manager: carmonm
ms.openlocfilehash: b81dccf3c71fbaff7b0cbb9ba00f13b458156e73
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588267"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Integrar computadores para gestão de configuração de estado de automatização do Azure

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Por que razão gerir máquinas com a configuração de estado de automatização do Azure?

A configuração de estado de automatização do Azure é um serviço de gestão de configuração para nós de DSC em qualquer cloud ou datacenter no local.
Ele habilita a escalabilidade entre milhares de máquinas rápida e facilmente partir de uma localização central e segura.
Pode carregar facilmente máquinas, atribua-as configurações declarativas e ver relatórios, que mostra cada máquina conformidade para o estado pretendido especificado.
O serviço de configuração de estado de automatização do Azure, é para DSC, o que são runbooks de automatização do Azure para a criação de scripts do PowerShell.
Em outras palavras, da mesma forma que a automatização do Azure ajuda-o a gerir scripts do PowerShell, também o ajuda a gerenciar configurações de DSC.
Para saber mais sobre as vantagens de utilizar a configuração de estado de automatização do Azure, veja [descrição geral da configuração de estado de automatização do Azure](automation-dsc-overview.md).

A configuração de estado de automatização do Azure pode ser utilizada para gerir uma variedade de máquinas:

- Máquinas virtuais do Azure
- Máquinas virtuais do Azure (clássico)
- Instâncias do EC2 Amazon Web Services (AWS)
- Windows físicos/virtuais das máquinas no local, ou numa cloud diferente do Azure/AWS
- Linux físicos/virtuais das máquinas no local, no Azure ou numa cloud diferente do Azure

Além disso, se não está preparado para gerir a configuração da máquina na nuvem, configuração de estado de automatização do Azure também pode ser utilizada como um ponto de final apenas de relatório.
Isto permite-lhe definir as configurações de (push) através do DSC e a vista de relatório de detalhes na automatização do Azure.

> [!NOTE]
> Gerir VMs do Azure com a configuração de estado está incluído sem custo adicional, se a extensão de máquina virtual DSC instalada é superior à 2.70. Consulte a [ **página de preços de automatização** ](https://azure.microsoft.com/pricing/details/automation/) para obter mais detalhes.

As secções seguintes descrevem como pode integrar cada tipo de máquina para configuração de estado de automatização do Azure.

## <a name="azure-virtual-machines"></a>Máquinas virtuais do Azure

A configuração de estado de automatização do Azure permite-lhe um integrar facilmente as máquinas virtuais do Azure para a gestão de configuração, usando o portal do Azure, modelos Azure Resource Manager ou PowerShell. Nos bastidores e sem um administrador tem remotamente a VM, a extensão Azure VM Desired State Configuration registra a VM com a configuração de estado de automatização do Azure.
Uma vez que a extensão de Desired State Configuration da VM do Azure é executado de forma assíncrona, os passos para acompanhar o progresso ou resolução de problemas são fornecidos a seguir [ **inclusão da máquina virtual de resolução de problemas do Azure** ](#troubleshooting-azure-virtual-machine-onboarding) secção.

### <a name="azure-portal"></a>Portal do Azure

Na [portal do Azure](https://portal.azure.com/), navegue para a conta de automatização do Azure onde pretende adicionar máquinas virtuais. Na página de configuração de estado e o **nós** separador, clique em **+ adicionar**.

Selecione uma máquina virtual do Azure para carregar.

Se a máquina não tiver instalado de extensão de estado pretendido do PowerShell e o estado de energia está em execução, clique em **Connect**.

Sob **Registro**, introduza o [valores de Gestor de configuração Local do PowerShell DSC](/powershell/dsc/metaconfig4) necessárias para seu caso de utilização e, opcionalmente, uma configuração de nó para atribuir à VM.

![Integração](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Máquinas virtuais do Azure podem ser implementadas e carregados para a configuração de estado de automatização do Azure através de modelos Azure Resource Manager. Ver [servidor gerido pelo serviço de Desired State Configuration](https://azure.microsoft.com/en-us/resources/templates/101-automation-configuration/) para um modelo de exemplo que carrega uma VM existente para a configuração de estado de automatização do Azure.
Se estiver a gerir um conjunto de dimensionamento de Máquina Virtual, consulte o modelo de exemplo [configuração de conjunto de dimensionamento de VM geridos pela automatização do Azure](https://azure.microsoft.com/en-us/resources/templates/201-vmss-automation-dsc/).

### <a name="powershell"></a>PowerShell

O [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) cmdlet pode ser utilizado para adicionar máquinas virtuais no portal do Azure através do PowerShell.

### <a name="registering-virtual-machines-across-azure-subscriptions"></a>Registar máquinas virtuais nas subscrições do Azure

É a melhor forma de registar máquinas virtuais a partir de outras subscrições do Azure utilizar a extensão de DSC num modelo de implementação Azure Resource Manager.
São fornecidos exemplos [extensão de Desired State Configuration com modelos Azure Resource Manager](https://docs.microsoft.com/en-us/azure/virtual-machines/extensions/dsc-template).
Para obter a chave de registo e o URL de registo para utilizar como parâmetros no modelo, consulte o seguinte procedimento [ **proteger registo** ](#secure-registration) secção.

## <a name="azure-virtual-machines-classic"></a>Máquinas virtuais do Azure (clássico)

Com a configuração de estado de automatização do Azure, pode facilmente adicionar máquinas de virtuais do Azure (clássico) para a gestão de configuração usando o portal do Azure ou PowerShell. Nos bastidores e sem um administrador tem remotamente a VM, a extensão Azure VM Desired State Configuration registra a VM com a configuração de estado de automatização do Azure.
Passos para controlar o progresso ou resolução de problemas são fornecidos a seguir [ **inclusão da máquina virtual de resolução de problemas do Azure** ](#troubleshooting-azure-virtual-machine-onboarding) secção.

### <a name="azure-portal-classic-virtual-machines"></a>Portal do Azure (máquinas virtuais clássicas)

Na [portal do Azure](https://portal.azure.com/), clique em **procurar** -> **máquinas virtuais (clássicas)**. Selecione a VM do Windows que pretende carregar. No painel de dashboard da máquina virtual, clique em **todas as definições** -> **extensões** -> **adicionar** -> **Azure O Automation DSC** -> **criar**.
Introduza o [valores de Gestor de configuração Local do PowerShell DSC](/powershell/dsc/metaconfig4) para a chave de registo da sua conta de automatização e URL de registo e, opcionalmente, uma configuração de nó atribuir à VM.

![Extensões VM do Azure para DSC](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)

Para encontrar o URL de registo e a chave da conta de automatização para integrar a máquina para ver o seguinte [ **proteger registo** ](#secure-registration) secção:

### <a name="powershell-classic-virtual-machines"></a>PowerShell (máquinas virtuais clássicas)

```powershell
# log in to both Azure Service Management and Azure Resource Manager
Add-AzureAccount
Connect-AzureRmAccount

# fill in correct values for your VM/Automation account here
$VMName = ''
$ServiceName = ''
$AutomationAccountName = ''
$AutomationAccountResourceGroup = ''

# fill in the name of a Node Configuration in Azure Automation State Configuration, for this VM to conform to
# NOTE: DSC Node Configuration names are case sensitive in the portal.
$NodeConfigName = ''

# get Azure Automation State Configuration registration info
$Account = Get-AzureRmAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
$RegistrationInfo = $Account | Get-AzureRmAutomationRegistrationInfo

# use the DSC extension to onboard the VM for management with Azure Automation State Configuration
$VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName

$PublicConfiguration = ConvertTo-Json -Depth 8 @{
    SasToken = ''
    ModulesUrl = 'https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip'
    ConfigurationFunction = 'RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2'

# update these PowerShell DSC Local Configuration Manager defaults if they do not match your use case.
# See https://docs.microsoft.com/powershell/dsc/metaConfig for more details
    Properties = @{
        RegistrationKey = @{
            UserName = 'notused'
            Password = 'PrivateSettingsRef:RegistrationKey'
        }
        RegistrationUrl = $RegistrationInfo.Endpoint
        NodeConfigurationName = $NodeConfigName
        ConfigurationMode = 'ApplyAndMonitor'
        ConfigurationModeFrequencyMins = 15
        RefreshFrequencyMins = 30
        RebootNodeIfNeeded = $False
        ActionAfterReboot = 'ContinueConfiguration'
        AllowModuleOverwrite = $False
    }
}

$PrivateConfiguration = ConvertTo-Json -Depth 8 @{
    Items = @{
        RegistrationKey = $RegistrationInfo.PrimaryKey
    }
}

$VM = Set-AzureVMExtension `
    -VM $vm `
    -Publisher Microsoft.Powershell `
    -ExtensionName DSC `
    -Version 2.76 `
    -PublicConfiguration $PublicConfiguration `
    -PrivateConfiguration $PrivateConfiguration `
    -ForceUpdate

$VM | Update-AzureVM
```

> [!NOTE]
> Nomes de configuração de nó de configuração de estado são maiúsculas de minúsculas no portal. Caso se sem correspondência o nó não irá aparecer sob o **nós** separador.

## <a name="amazon-web-services-aws-virtual-machines"></a>Máquinas virtuais do Amazon Web Services (AWS)

Pode facilmente adicionar máquinas de virtuais de Amazon Web Services para gerenciamento de configuração por configuração de estado de automatização do Azure através do Toolkit de DSC do AWS. Pode saber mais sobre o toolkit [aqui](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Windows físicos/virtuais das máquinas no local, ou numa cloud diferente do Azure/AWS

Servidores do Windows em execução no local ou em outros ambientes de cloud também pode ser integrado à configuração de estado de automatização do Azure, desde que eles tenham acesso de saída para o Azure:

1. Certifique-se a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) está instalado nas máquinas que pretende integrar a configuração de estado de automatização do Azure.
1. Siga as instruções na secção seguinte [ **geração DSC metaconfigurations** ](#generating-dsc-metaconfigurations) para gerar uma pasta que contém o metaconfigurations DSC necessários.
1. Aplicam-se remotamente o metaconfiguration de PowerShell DSC para as máquinas que pretende carregar. **A este comando é executado a partir de máquina tem de ter a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) instalado**:

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Se não é possível aplicar o metaconfigurations DSC de PowerShell remotamente, copie a pasta de metaconfigurations do passo 2 para cada computador para carregar. Em seguida, chame **Set-dsclocalconfigurationmanager para** localmente em cada máquina para carregar.
1. Utilizar o portal do Azure ou cmdlets, verifique se as máquinas para carregar agora apresentado como a configuração de estado de nós registados na sua conta de automatização do Azure.

## <a name="physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Linux físicos/virtuais das máquinas no local, ou numa cloud diferente do Azure

Servidores Linux em execução no local ou em outros ambientes de cloud também pode ser integrado à configuração de estado de automatização do Azure, desde que eles tenham acesso de saída para o Azure:

1. Certifique-se a versão mais recente do [PowerShell Desired State Configuration para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) está instalado nas máquinas que pretende integrar a configuração de estado de automatização do Azure.
1. Se o [predefinições do Gestor de configuração Local do PowerShell DSC](/powershell/dsc/metaconfig4) corresponde ao seu caso de utilização e que pretende carregar máquinas como que eles **ambos** retirará e reportar a configuração de estado de automatização do Azure:

   - Em cada máquina Linux para carregar a configuração de estado de automatização do Azure, utilize `Register.py` integrar com as predefinições do Gestor de configuração de Local de DSC de PowerShell:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Para obter a chave de registo e o URL de registo para a sua conta de automatização, veja o seguinte procedimento [ **proteger registo** ](#secure-registration) secção.

     Se o Gestor de configuração de Local do PowerShell DSC defaults **não** corresponde ao seu caso de utilização, ou que pretende carregar máquinas, de modo a que apenas informar à configuração de estado de automatização do Azure, siga os passos 3 a 6. Caso contrário, avance diretamente para o passo 6.

1. Siga as instruções a seguir [ **geração DSC metaconfigurations** ](#generating-dsc-metaconfigurations) secção para gerar uma pasta que contém o metaconfigurations DSC necessários.
1. Aplicam-se remotamente o metaconfiguration de PowerShell DSC para as máquinas que pretende carregar:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

A este comando é executado a partir de máquina tem de ter a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) instalado.

1. Se não é possível aplicar o metaconfigurations DSC de PowerShell remotamente, copie o metaconfiguration correspondente a essa máquina a partir da pasta no passo 5 para o computador Linux. Em seguida, chamar `SetDscLocalConfigurationManager.py` localmente em cada máquina Linux pretende integrar a configuração de estado de automatização do Azure:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Utilizar o portal do Azure ou cmdlets, verifique que as máquinas para carregar agora apresentado como nós de DSC registados na sua conta de automatização do Azure.

## <a name="generating-dsc-metaconfigurations"></a>Geração DSC metaconfigurations

Para carregar genericamente qualquer máquina à configuração de estado de automatização do Azure, um [DSC metaconfiguration](/powershell/dsc/metaconfig) podem ser gerados que indica que o agente DSC transmita a partir de e/ou reportar a configuração de estado de automatização do Azure. DSC metaconfigurations para configuração de estado de automatização do Azure podem ser gerados usando uma configuração de DSC do PowerShell ou os cmdlets do PowerShell da automatização do Azure.

> [!NOTE]
> DSC metaconfigurations contêm os segredos necessário para carregar uma máquina para uma automação de conta para a gestão. Certifique-se proteger corretamente qualquer metaconfigurations DSC que cria ou eliminá-los após o uso.

### <a name="using-a-dsc-configuration"></a>Utilizar uma configuração de DSC

1. Abra o VSCode (ou seu editor favorito) como um administrador numa máquina no seu ambiente local. A máquina tem de ter a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) instalado.
1. Copie o seguinte script localmente. Este script contém uma configuração de DSC de PowerShell para criar metaconfigurations e um comando para iniciar a criação de metaconfiguration.

> [!NOTE]
> Nomes de configuração de nó de configuração de estado são maiúsculas de minúsculas no portal. Caso se sem correspondência o nó não irá aparecer sob o **nós** separador.

   ```powershell
   # The DSC configuration that will generate metaconfigurations
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. Preencha a chave de registo e o URL para a sua conta de automatização, bem como os nomes das máquinas para carregar. Todos os outros parâmetros são opcionais. Para obter a chave de registo e o URL de registo para a sua conta de automatização, veja o seguinte procedimento [ **proteger registo** ](#secure-registration) secção.
1. Se pretender que as máquinas a reportar as informações de estado de DSC para configuração de estado de automatização do Azure, mas não a configuração de solicitação ou módulos do PowerShell, definir o **ReportOnly** parâmetro como true.
1. Execute o script. Agora, deve ter uma pasta denominada **DscMetaConfigs** no diretório de trabalho, que contém o metaconfigurations de PowerShell DSC para as máquinas para carregar (como um administrador):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Utilizar os cmdlets de automatização do Azure

Se as predefinições do Gestor de configuração Local do PowerShell DSC corresponde ao seu caso de utilização e que pretende carregar máquinas, de modo que eles o retirará e reportam a configuração de estado de automatização do Azure, os cmdlets de automatização do Azure fornecem um método simplificado de geração o metaconfigurations DSC necessário:

1. Abra a consola do PowerShell ou o VSCode como administrador numa máquina no seu ambiente local.
2. Ligar através do Azure Resource Manager `Connect-AzureRmAccount`
3. Baixe o metaconfigurations de PowerShell DSC para as máquinas que pretende carregar para a conta de automatização à qual pretende carregar nós:

   ```powershell
   # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzureRmAutomationDscOnboardingMetaconfig @Params
   ```

1. Agora, deve ter uma pasta denominada ***DscMetaConfigs***, que contém o metaconfigurations de PowerShell DSC para as máquinas para carregar (como um administrador):

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Proteger o registo

As máquinas podem-se em segurança carregar para uma conta de automatização do Azure através do protocolo de registo do WMF 5 DSC, que permite que um nó de DSC autenticar um servidor de solicitação de DSC do PowerShell ou de relatórios (incluindo a configuração de estado de automatização do Azure). O nó registra para o servidor numa **URL de registo**e autentica através de um **chave de registo**. Durante o registo, o nó de DSC e o servidor de solicitação do DSC/relatórios negociam um certificado exclusivo para este nó utilizar para autenticação para o pós-registo de servidor. Este processo impede que nós de integração de representando um que outro, por exemplo, se um nó for comprometido e se comportando maliciosamente. Após o registo, a chave de registo não é utilizada para autenticação novamente e é eliminada do nó.

Pode obter as informações necessárias para o protocolo de registo de configuração de estado de **chaves** sob **definições de conta** no portal do Azure. Abrir este painel ao clicar no ícone de chave no **Essentials** painel da conta de automatização.

![Chaves de automatização do Azure e o URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- URL de registo é o campo de URL no painel chaves de gerir.
- Chave de registo é a chave de acesso primária ou uma chave de acesso secundária no painel chaves de gerir. Nenhuma das chaves pode ser utilizado.

Para maior segurança, as chaves de acesso primária e secundária de uma conta de automatização podem ser gerado novamente em qualquer altura (sobre o **gerir chaves** página) para impedir que os registos de nó futuras usando as chaves anteriores.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Resolução de problemas de integração de máquina virtual do Azure

A configuração de estado de automatização do Azure permite-lhe facilmente integrar VMs do Windows Azure para a gestão de configuração. Nos bastidores, a extensão de Desired State Configuration da VM do Azure é utilizada para registar a VM com a configuração de estado de automatização do Azure. Uma vez que a extensão de Desired State Configuration da VM do Azure é executado de forma assíncrona, controlar o progresso e resolução de problemas de sua execução podem ser importantes.

> [!NOTE]
> Qualquer método de inclusão de uma VM do Windows Azure para a configuração de estado de automatização do Azure que utiliza a extensão Azure VM Desired State Configuration poderia demorar uma hora para o nó Mostrar até registado na automatização do Azure. Isso se deve a instalação do Windows Management Framework 5.0 na VM pela extensão do DSC de VM do Azure, o que é necessária para carregar a VM para a configuração de estado de automatização do Azure.

Para resolver problemas ou ver o estado da extensão de Desired State Configuration da VM do Azure, no portal do Azure navegue para a VM que se pretende incluir e clique em **extensões** sob **definições**. Em seguida, clique em **DSC** ou **DSCForLinux** consoante o sistema operativo. Para obter mais detalhes, pode clicar **Ver estado detalhado**.

## <a name="certificate-expiration-and-reregistration"></a>Expiração do certificado e o novo registo

Depois de registar uma máquina como um nó de DSC na configuração de estado de automatização do Azure, existem vários motivos por que razão poderá ter de voltar a registar esse nó no futuro:

- Após o registro, cada nó negocia automaticamente um certificado exclusivo para a autenticação que expira após um ano. Atualmente, o protocolo de registo DSC de PowerShell não é possível renovar automaticamente certificados quando eles estão prestes a expirar, por isso terá de voltar a registar os nós após o tempo de um ano. Antes de ao registar novamente, certifique-se de que cada nó está a executar o Windows Management Framework 5.0 RTM. Se o certificado de autenticação de um nó expira e o nó não está registado novamente no, o nó não consegue comunicar com a automatização do Azure e está marcado como "Unresponsive." O novo registo feita 90 dias ou menos desde a hora de expiração do certificado ou a qualquer momento após a hora de expiração do certificado, irá resultar num novo certificado a ser gerados e usados.
- Para alterar quaisquer [valores de Gestor de configuração Local do PowerShell DSC](/powershell/dsc/metaconfig4) que foram definidas durante o registo inicial do nó, como ConfigurationMode. Atualmente, estes valores de agente DSC só podem ser alterados por meio do novo registo. A única exceção é a configuração de nó atribuída ao nó--isso pode ser alterado no DSC de automatização do Azure diretamente.

O novo registo pode ser executado da mesma forma que registrou o nó inicialmente, usando qualquer um dos métodos de integração descritos neste documento. Não é necessário anular o registo de um nó de configuração de estado de automatização do Azure antes de ao registar novamente a ele.

## <a name="next-steps"></a>Passos Seguintes

- Para começar a utilizar, consulte o artigo [introdução à configuração de estado de automatização do Azure](automation-dsc-getting-started.md)
- Para obter informações sobre como compilar configurações de DSC para que pode atribuí-las a nós de destino, consulte [compilar configurações na configuração de estado de automatização do Azure](automation-dsc-compile.md)
- Para referência de cmdlets do PowerShell, consulte [cmdlets de configuração de estado de automatização do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [preços de configuração de estado de automatização do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo do uso de configuração de estado de automatização do Azure num pipeline de implementação contínua, consulte [contínua através do Azure Automation estado de configuração da implementação e Chocolatey](automation-dsc-cd-chocolatey.md)
