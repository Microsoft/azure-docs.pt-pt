---
title: Máquinas de integração para gerenciamento pela configuração de estado da automação do Azure
description: Como configurar computadores para gerenciamento com a configuração de estado de automação do Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.topic: conceptual
ms.date: 08/08/2018
manager: carmonm
ms.openlocfilehash: c05ac7a1894fc3e159ef8fc2b3dd2654714faccf
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965190"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Máquinas de integração para gerenciamento pela configuração de estado da automação do Azure

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Por que gerenciar computadores com a configuração de estado de automação do Azure?

A configuração de estado de automação do Azure é um serviço de gerenciamento de configuração para nós DSC em qualquer datacenter local ou na nuvem.
Ele permite a escalabilidade entre milhares de computadores de forma rápida e fácil a partir de um local central e seguro.
Você pode facilmente integrar computadores, atribuir a eles configurações declarativas e exibir relatórios mostrando a conformidade de cada computador com o estado desejado especificado.
O serviço de configuração de estado da automação do Azure é para DSC quais runbooks de automação do Azure são para scripts do PowerShell.
Em outras palavras, da mesma maneira que a automação do Azure ajuda a gerenciar scripts do PowerShell, ele também ajuda a gerenciar configurações DSC.
Para saber mais sobre os benefícios de usar a configuração de estado da automação do Azure, confira [visão geral da configuração de estado da automação do Azure](automation-dsc-overview.md).

A configuração de estado da automação do Azure pode ser usada para gerenciar uma variedade de máquinas:

- Máquinas virtuais do Azure
- Máquinas virtuais do Azure (clássico)
- Instâncias de Amazon Web Services (AWS) EC2
- Máquinas físicas/virtuais do Windows locais ou em uma nuvem diferente do Azure/AWS
- Computadores Linux físicos/virtuais locais, no Azure ou em uma nuvem diferente do Azure

Além disso, se você não estiver pronto para gerenciar a configuração da máquina na nuvem, a configuração de estado da automação do Azure também poderá ser usada como um ponto de extremidade somente de relatório.
Isso permite que você defina as configurações (push) por meio da DSC e exiba detalhes de relatório na automação do Azure.

> [!NOTE]
> O gerenciamento de VMs do Azure com a configuração de estado está incluído sem custo adicional se a extensão de DSC da máquina virtual instalada for maior que 2,70. Consulte a [**página de preços de automação**](https://azure.microsoft.com/pricing/details/automation/) para obter mais detalhes.

As seções a seguir descrevem como você pode carregar cada tipo de computador para a configuração de estado da automação do Azure.

## <a name="azure-virtual-machines"></a>Máquinas virtuais do Azure

A configuração de estado da automação do Azure permite que você integre facilmente máquinas virtuais do Azure para o gerenciamento de configuração, usando o portal do Azure, Azure Resource Manager modelos ou o PowerShell. Nos bastidores, e sem que um administrador tenha que fazer o controle remoto na VM, a extensão de configuração de estado desejado da VM do Azure registrará a VM com a configuração de estado da automação do Azure.
Como a extensão de configuração de estado desejado da VM do Azure é executada de forma assíncrona, as etapas para controlar seu progresso ou solucionar o problema são fornecidas na seção [**solução de problemas de integração da máquina virtual do Azure**](#troubleshooting-azure-virtual-machine-onboarding) a seguir.

### <a name="azure-portal"></a>Portal do Azure

Na [portal do Azure](https://portal.azure.com/), navegue até a conta de automação do Azure onde você deseja carregar as máquinas virtuais. Na página configuração de estado e na guia **nós** , clique em **+ Adicionar**.

Selecione uma máquina virtual do Azure para carregar.

Se o computador não tiver a extensão de estado desejado do PowerShell instalada e o estado de energia estiver em execução, clique em **conectar**.

Em **registro**, insira os [valores de Configuration Manager local do DSC do PowerShell](/powershell/dsc/managing-nodes/metaconfig) necessários para seu caso de uso e, opcionalmente, uma configuração de nó para atribuir à VM.

![Integração](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

As máquinas virtuais do Azure podem ser implantadas e integradas à configuração de estado da automação do Azure por meio de modelos de Azure Resource Manager. Consulte [servidor gerenciado pelo serviço de configuração de estado desejado](https://azure.microsoft.com/resources/templates/101-automation-configuration/) para obter um modelo de exemplo que integra uma VM existente à configuração de estado de automação do Azure.
Se você estiver gerenciando um conjunto de dimensionamento de máquinas virtuais, consulte o modelo de exemplo configuração de conjunto de dimensionamento de [VM gerenciado pela automação do Azure](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="powershell"></a>PowerShell

O cmdlet [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) pode ser usado para carregar máquinas virtuais no Azure usando o PowerShell.
No entanto, isso é atualmente implementado apenas para computadores que executam o Windows (o cmdlet dispara apenas a extensão do Windows).

### <a name="registering-virtual-machines-across-azure-subscriptions"></a>Registrando máquinas virtuais em assinaturas do Azure

A melhor maneira de registrar máquinas virtuais de outras assinaturas do Azure é usar a extensão de DSC em um modelo de implantação de Azure Resource Manager.
Os exemplos são fornecidos na [extensão de configuração de estado desejado com modelos de Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).
Para localizar a chave de registro e a URL de registro para usar como parâmetros no modelo, consulte a seguinte seção de [**registro seguro**](#secure-registration) .

## <a name="amazon-web-services-aws-virtual-machines"></a>Máquinas virtuais Amazon Web Services (AWS)

Você pode facilmente integrar Amazon Web Services máquinas virtuais para o gerenciamento de configuração pela configuração de estado da automação do Azure usando o kit de ferramentas de DSC do AWS. Você pode aprender mais sobre o kit de ferramentas [aqui](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Máquinas físicas/virtuais do Windows locais ou em uma nuvem diferente do Azure/AWS

Os servidores Windows em execução no local ou em outros ambientes de nuvem também podem ser integrados à configuração de estado da automação do Azure, desde que tenham [acesso de saída ao Azure](automation-dsc-overview.md#network-planning):

1. Verifique se a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) está instalada nos computadores que você deseja integrar à configuração de estado da automação do Azure.
1. Siga as instruções na seção a seguir [**gerando metaconfigurações DSC**](#generating-dsc-metaconfigurations) para gerar uma pasta que contém as metaconfigurações de DSC necessárias.
1. Aplique remotamente a metaconfiguração do DSC do PowerShell aos computadores que você deseja carregar. **O computador no qual este comando é executado deve ter a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) instalada**:

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Se você não puder aplicar as metaconfigurações do DSC do PowerShell remotamente, copie a pasta metaconfigurações da etapa 2 em cada máquina para carregar. Em seguida, chame **set-DscLocalConfigurationManager** localmente em cada computador para carregar.
1. Usando o portal do Azure ou cmdlets, verifique se os computadores a serem integrados agora aparecem como nós de configuração de estado registrados em sua conta de automação do Azure.

## <a name="physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Computadores Linux físicos/virtuais locais ou em uma nuvem diferente do Azure

Os servidores Linux em execução no local ou em outros ambientes de nuvem também podem ser integrados à configuração de estado da automação do Azure, desde que tenham [acesso de saída ao Azure](automation-dsc-overview.md#network-planning):

1. Verifique se a versão mais recente da [configuração de estado desejado do PowerShell para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) está instalada nos computadores que você deseja integrar à configuração de estado da automação do Azure.
1. Se os [padrões de Configuration Manager local do DSC do PowerShell](/powershell/dsc/metaconfig4) corresponderem ao seu caso de uso, e você quiser carregar computadores de forma **que eles sejam** pull e relatem à configuração de estado da automação do Azure:

   - Em cada computador Linux para carregar a configuração de estado da automação do Azure `Register.py` , use para carregar usando os padrões de Configuration Manager local do DSC do PowerShell:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Para localizar a chave de registro e a URL de registro para sua conta de automação, consulte a seguinte seção de [**registro seguro**](#secure-registration) .

     Se os padrões de Configuration Manager local do DSC do PowerShell **não** corresponderem ao seu caso de uso ou se você quiser carregar computadores de forma que eles reportem para a configuração de estado da automação do Azure, siga as etapas 3-6. Caso contrário, vá diretamente para a etapa 6.

1. Siga as instruções na seção gerando metaconfigurações de [**DSC**](#generating-dsc-metaconfigurations) a seguir para gerar uma pasta que contém as metaconfigurações de DSC necessárias.
1. Aplique remotamente a metaconfiguração do DSC do PowerShell aos computadores que você deseja carregar:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

O computador no qual este comando é executado deve ter a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) instalada.

1. Se você não puder aplicar as metaconfigurações do DSC do PowerShell remotamente, copie a metaconfiguração correspondente a esse computador da pasta na etapa 5 para o computador Linux. Em seguida `SetDscLocalConfigurationManager.py` , chame localmente em cada computador Linux que você deseja integrar à configuração de estado da automação do Azure:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Usando o portal do Azure ou cmdlets, verifique se os computadores a serem integrados agora aparecem como nós de DSC registrados em sua conta de automação do Azure.

## <a name="generating-dsc-metaconfigurations"></a>Gerando metaconfigurações de DSC

Para integrar genericamente qualquer máquina à configuração de estado de automação do Azure, é possível gerar uma metaconfiguração de [DSC](/powershell/dsc/metaconfig) que diz ao agente de DSC para extrair e/ou relatar a configuração de estado da automação do Azure. As metaconfigurações de DSC para a configuração de estado de automação do Azure podem ser geradas usando uma configuração de DSC do PowerShell ou os cmdlets do PowerShell de automação do Azure.

> [!NOTE]
> Metaconfigurações de DSC contêm os segredos necessários para carregar um computador em uma conta de automação para gerenciamento. Certifique-se de proteger corretamente todas as metaconfigurações de DSC que você criar ou excluí-las após o uso.

### <a name="using-a-dsc-configuration"></a>Usando uma configuração DSC

1. Abra o VSCode (ou seu editor favorito) como administrador em um computador em seu ambiente local. O computador deve ter a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) instalada.
1. Copie o script a seguir localmente. Esse script contém uma configuração DSC do PowerShell para criar metaconfigurações e um comando para iniciar a criação da metaconfiguração.

> [!NOTE]
> Nomes de configuração de nó de configuração de estado diferenciam maiúsculas de minúsculas no Portal. Se o caso não for compatível, o nó não aparecerá na guia **nós** .

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

1. Preencha a chave de registro e a URL para sua conta de automação, bem como os nomes das máquinas para carregar. Todos os outros parâmetros são opcionais. Para localizar a chave de registro e a URL de registro para sua conta de automação, consulte a seguinte seção de [**registro seguro**](#secure-registration) .
1. Se você quiser que os computadores relatem informações de status de DSC para a configuração de estado de automação do Azure, mas não para configuração de pull ou módulos do PowerShell, defina o parâmetro **ReportOnly** como true.
1. Execute o script. Agora você deve ter uma pasta chamada **DscMetaConfigs** em seu diretório de trabalho, que contém as metaconfigurações de DSC do PowerShell para os computadores a serem integrados (como um administrador):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Usando os cmdlets de automação do Azure

Se os padrões de Configuration Manager local do DSC do PowerShell corresponderem ao seu caso de uso e você quiser integrar computadores de forma que eles sejam fornecidos e relatem à configuração de estado da automação do Azure, os cmdlets de automação do Azure fornecerão um método simplificado de geração as metaconfigurações de DSC são necessárias:

1. Abra o console do PowerShell ou VSCode como administrador em um computador em seu ambiente local.
2. Conectar-se a Azure Resource Manager usando`Connect-AzAccount`
3. Baixe as metaconfigurações de DSC do PowerShell para os computadores que você deseja integrar da conta de automação para a qual você deseja carregar nós:

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Agora você deve ter uma pasta chamada ***DscMetaConfigs***, que contém as metaconfigurações de DSC do PowerShell para que os computadores sejam integrados (como um administrador):

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Registro seguro

Os computadores podem se integrar com segurança a uma conta de automação do Azure por meio do protocolo de registro de DSC do WMF 5, que permite que um nó DSC se autentique em um servidor de pull ou relatório de DSC do PowerShell (incluindo a configuração de estado da automação do Azure). O nó registra-se no servidor em uma **URL de registro**, Autenticando usando uma chave de **registro**. Durante o registro, o nó de DSC e o servidor de recepção/relatório de DSC negociam um certificado exclusivo para esse nó usar para autenticação para o registro posterior do servidor. Esse processo impede que nós integrados se representem um do outro, como se um nó estiver comprometido e se comportando de forma mal-intencionada. Após o registro, a chave de registro não é usada novamente para autenticação e é excluída do nó.

Você pode obter as informações necessárias para o protocolo de registro de configuração de estado das **chaves** em **configurações de conta** no portal do Azure. Abra essa folha clicando no ícone de chave no painel **Essentials** para a conta de automação.

![Chaves e URL de automação do Azure](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- URL de registro é o campo URL na folha gerenciar chaves.
- A chave de registro é a chave de acesso primária ou a chave de acesso secundária na folha gerenciar chaves. Qualquer chave pode ser usada.

Para maior segurança, as chaves de acesso primária e secundária de uma conta de automação podem ser geradas novamente a qualquer momento (na página **gerenciar chaves** ) para evitar registros de nó futuros usando as chaves anteriores.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Solucionando problemas de integração de máquina virtual do Azure

A configuração de estado da automação do Azure permite que você integre VMs do Windows do Azure com facilidade para gerenciamento de configuração. Nos bastidores, a extensão de configuração de estado desejado da VM do Azure é usada para registrar a VM com a configuração de estado da automação do Azure. Como a extensão de configuração de estado desejado da VM do Azure é executada de forma assíncrona, acompanhar seu progresso e solucionar problemas de sua execução pode ser importante.

> [!NOTE]
> Qualquer método de integração de uma VM do Windows do Azure à configuração de estado de automação do Azure que usa a extensão de configuração de estado desejado da VM do Azure pode levar até uma hora para que o nó seja exibido como registrado na automação do Azure. Isso ocorre devido à instalação do Windows Management Framework 5,0 na VM pela extensão de DSC da VM do Azure, que é necessária para integrar a configuração de estado da VM para a automação do Azure.

Para solucionar problemas ou exibir o status da extensão de configuração de estado desejado da VM do Azure, na portal do Azure navegue até a VM que está sendo integrada e clique em **extensões** em **configurações**. Em seguida, clique em **DSC** ou **DSCForLinux** dependendo do seu sistema operacional. Para obter mais detalhes, você pode clicar em **Exibir status detalhado**.

## <a name="certificate-expiration-and-reregistration"></a>Expiração e reregistro do certificado

Depois de registrar um computador como um nó DSC na configuração de estado da automação do Azure, há várias razões pelas quais talvez seja necessário registrar novamente esse nó no futuro:

- Para versões do Windows Server anteriores ao Windows Server 2019, cada nó negocia automaticamente um certificado exclusivo para autenticação que expira após um ano. Atualmente, o protocolo de registro DSC do PowerShell não pode renovar automaticamente os certificados quando eles estão se aproximando da expiração, portanto, você precisa registrar novamente os nós após o horário de um ano. Antes de registrar novamente, verifique se cada nó está executando o Windows Management Framework 5,0 RTM. Se o certificado de autenticação de um nó expirar e o nó não for registrado novamente, o nó não poderá se comunicar com a automação do Azure e será marcado como ' sem resposta '. O novo registro realizado em 90 dias ou menos do tempo de expiração do certificado, ou a qualquer momento após o tempo de expiração do certificado, resultará em uma nova geração e uso do certificado.  Uma resolução para esse problema está incluída no Windows Server 2019 e posterior.
- Para alterar os [valores de Configuration Manager locais do DSC do PowerShell](/powershell/dsc/metaconfig4) que foram definidos durante o registro inicial do nó, como ConfigurationMode. Atualmente, esses valores do agente DSC só podem ser alterados por meio de um novo registro. A única exceção é a configuração de nó atribuída ao nó--isso pode ser alterado no Azure DSC de Automação diretamente.

O novo registro pode ser executado da mesma maneira que você registrou o nó inicialmente, usando qualquer um dos métodos de integração descritos neste documento. Você não precisa cancelar o registro de um nó da configuração de estado da automação do Azure antes de registrá-lo novamente.

## <a name="next-steps"></a>Passos Seguintes

- Para começar, consulte [introdução à configuração de estado de automação do Azure](automation-dsc-getting-started.md)
- Para saber mais sobre como compilar configurações DSC para que você possa atribuí-las aos nós de destino, consulte [compilando configurações na configuração de estado da automação do Azure](automation-dsc-compile.md)
- Para referência de cmdlet do PowerShell, consulte cmdlets de [configuração do estado de automação do Azure](/powershell/module/az.automation#automation)
- Para obter informações sobre preços, consulte [preços de configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de como usar a configuração de estado de automação do Azure em um pipeline de implantação contínua, consulte [implantação contínua usando configuração de estado de automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
