---
title: Máquinas de integração para gerenciamento pela configuração de estado da automação do Azure
description: Como configurar computadores para gerenciamento com a configuração de estado de automação do Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: 89e86a6702be7314b99975cac90818252eb07df7
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046223"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Máquinas de integração para gerenciamento pela configuração de estado da automação do Azure

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Por que gerenciar computadores com a configuração de estado de automação do Azure?

A configuração de estado de automação do Azure é um serviço de gerenciamento de configuração para nós de DSC (configuração de estado desejado) em qualquer datacenter local ou na nuvem.
Ele permite a escalabilidade entre milhares de computadores de forma rápida e fácil a partir de um local central e seguro.
Você pode facilmente integrar computadores, atribuir a eles configurações declarativas e exibir relatórios mostrando a conformidade de cada computador com o estado desejado especificado.
O serviço de configuração de estado da automação do Azure é para DSC quais runbooks de automação do Azure são para scripts do PowerShell.
Em outras palavras, da mesma maneira que a automação do Azure ajuda a gerenciar scripts do PowerShell, ele também ajuda a gerenciar configurações DSC.
Para saber mais sobre os benefícios da utilização da Configuração do Estado da Automação Azure, consulte a visão geral da Configuração do Estado da [Automação Azure](automation-dsc-overview.md).

A configuração de estado da automação do Azure pode ser usada para gerenciar uma variedade de máquinas:

- Máquinas virtuais do Azure
- Máquinas virtuais do Azure (clássico)
- Máquinas físicas/virtuais do Windows locais ou em uma nuvem diferente do Azure (incluindo instâncias AWS EC2)
- Computadores Linux físicos/virtuais locais, no Azure ou em uma nuvem diferente do Azure

Além disso, se você não estiver pronto para gerenciar a configuração da máquina na nuvem, a configuração de estado da automação do Azure também poderá ser usada como um ponto de extremidade somente de relatório.
Isso permite que você defina as configurações (push) por meio da DSC e exiba detalhes de relatório na automação do Azure.

> [!NOTE]
> O gerenciamento de VMs do Azure com a configuração de estado está incluído sem custo adicional se a extensão de DSC da máquina virtual instalada for maior que 2,70. Para mais informações, consulte [**a página de preços da Automação**](https://azure.microsoft.com/pricing/details/automation/).

As seções a seguir descrevem como você pode carregar cada tipo de computador para a configuração de estado da automação do Azure.

> [!NOTE]
>A implementação de DSC para um nó Linux utiliza a pasta `/tmp` e os módulos como **o nxAutomation** são temporariamente descarregados para verificação antes de os instalarem na sua localização adequada. Para garantir que os módulos se instalam corretamente, o agente Log Analytics para o Linux necessita de autorização de leitura/escrita na `/tmp` pasta. O agente Log Analytics para o Linux funciona como o utilizador `omsagent`. 
>
>Para conceder permissão de escrita para `omsagent` utilizador, execute os seguintes comandos: `setfacl -m u:omsagent:rwx /tmp`
>

## <a name="azure-virtual-machines"></a>Máquinas virtuais do Azure

A configuração de estado da automação do Azure permite que você integre facilmente máquinas virtuais do Azure para o gerenciamento de configuração, usando o portal do Azure, Azure Resource Manager modelos ou o PowerShell. Nos bastidores, e sem que um administrador tenha que fazer o controle remoto na VM, a extensão de configuração de estado desejado da VM do Azure registrará a VM com a configuração de estado da automação do Azure.
Uma vez que a extensão de configuração de configuração de estado desejada Azure funciona assincronicamente, as etapas para acompanhar o seu progresso ou resolução de problemas são fornecidas na seguinte secção de embarque da [**máquina virtual Troubleshooting Azure.** ](#troubleshooting-azure-virtual-machine-onboarding)

### <a name="azure-portal"></a>Portal do Azure

No [portal Azure,](https://portal.azure.com/)navegue para a conta Azure Automation onde pretende embarcar em máquinas virtuais. Na página de Configuração do Estado e no separador **Nós,** clique **+ Adicionar**.

Selecione uma máquina virtual do Azure para carregar.

Se a máquina não tiver a extensão de estado desejada powerShell instalada e o estado de alimentação estiver em funcionamento, clique em **Connect**.

Em **'Registo',** introduza os valores do [PowerShell DSC Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig) necessários para a sua caixa de utilização e, opcionalmente, uma configuração de nó para atribuir ao VM.

![integração](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

As máquinas virtuais do Azure podem ser implantadas e integradas à configuração de estado da automação do Azure por meio de modelos de Azure Resource Manager. Consulte o [Servidor gerido pelo serviço de Configuração do Estado Desejado](https://azure.microsoft.com/resources/templates/101-automation-configuration/) para um modelo de exemplo que a bordo de um VM existente para configuração do Estado de Automação Azure.
Se estiver a gerir um conjunto de escala de máquina virtual, consulte o modelo de exemplo Conjunto de [escala de máquina virtual Configuração gerida pela Automação Azure](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="powershell"></a>PowerShell

O [cmdlet Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) pode ser utilizado para a bordo de máquinas virtuais em Azure utilizando o PowerShell.
No entanto, isso é atualmente implementado apenas para computadores que executam o Windows (o cmdlet dispara apenas a extensão do Windows).

### <a name="registering-virtual-machines-across-azure-subscriptions"></a>Registrando máquinas virtuais em assinaturas do Azure

A melhor maneira de registrar máquinas virtuais de outras assinaturas do Azure é usar a extensão de DSC em um modelo de implantação de Azure Resource Manager.
Exemplos são fornecidos na [extensão de Configuração do Estado Desejada com modelos de Gestor de Recursos Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).
Para encontrar a chave de registo e o URL de registo para utilizar como parâmetros no modelo, consulte a seguinte secção de [**registo Segura.** ](#secure-registration)

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances"></a>Máquinas físicas/virtuais do Windows locais ou em uma nuvem diferente do Azure (incluindo instâncias AWS EC2)

Os servidores windows que executam no local ou em outros ambientes em nuvem também podem ser a bordo da Configuração do Estado da Automação Azure, desde que tenham [acesso de saída ao Azure:](automation-dsc-overview.md#network-planning)

1. Certifique-se de que a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) está instalada nas máquinas que pretende instalar na Configuração do Estado da Automação Azure.
1. Siga as instruções na secção seguinte [**Gerando configurações dSC**](#generating-dsc-metaconfigurations) para gerar uma pasta contendo as configurações dSC necessárias.
1. Aplique remotamente a metaconfiguração do DSC do PowerShell aos computadores que você deseja carregar. A máquina de **onde este comando é executado deve ter a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) instalada:**

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Se você não puder aplicar as metaconfigurações do DSC do PowerShell remotamente, copie a pasta metaconfigurações da etapa 2 em cada máquina para carregar. Em seguida, ligue para **set-DscLocalConfigurationManager** localmente em cada máquina para bordo.
1. Usando o portal do Azure ou cmdlets, verifique se os computadores a serem integrados aparecem como nós de configuração de estado registrados em sua conta de automação do Azure.

## <a name="physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Computadores Linux físicos/virtuais locais ou em uma nuvem diferente do Azure

Os servidores Linux que executam no local ou em outros ambientes em nuvem também podem ser a bordo da Configuração do Estado da Automação Azure, desde que tenham [acesso de saída ao Azure:](automation-dsc-overview.md#network-planning)

1. Certifique-se de que a mais recente versão da [Configuração do Estado PowerShell Desired para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) está instalada nas máquinas que pretende fazer a bordo da Configuração do Estado da Automação Azure.
2. Se o [PowerShell DSC Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig4) corresponder ao seu caso de utilização, e quiser a bordo de máquinas de modo a que **ambos** retirem e reportem à Configuração do Estado da Automação Do Azure:

   - Em cada máquina Linux para embarcar na Configuração do Estado da Automação Azure, utilize `Register.py` para bordo utilizando os predefinições do PowerShell DSC Local Configuration Manager:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Para encontrar a chave de registo e URL de registo da sua conta Deautomação, consulte a seguinte secção de [**registo Segura.** ](#secure-registration)

     Se as predefinições do PowerShell DSC Local Configuration Manager **não** corresponderem ao seu caso de utilização, ou se pretender a bordo de máquinas de modo a que apenas reportem à Configuração do Estado da Automação Azure, siga os passos 3 - 6. Caso contrário, vá diretamente para a etapa 6.

3. Siga as instruções na secção de [**configurações DSC geradora**](#generating-dsc-metaconfigurations) seguinte para gerar uma pasta contendo as configurações dSC necessárias.
4. Aplique remotamente a metaconfiguração do DSC do PowerShell aos computadores que você deseja carregar:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

A máquina de onde este comando é executado deve ter a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) instalada.

1. Se você não puder aplicar as metaconfigurações do DSC do PowerShell remotamente, copie a metaconfiguração correspondente a esse computador da pasta na etapa 5 para o computador Linux. Em seguida, ligue `SetDscLocalConfigurationManager.py` localmente em cada máquina Linux que deseja embarcar para a Configuração do Estado da Automação Azure:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

2. Usando o portal do Azure ou cmdlets, verifique se os computadores a serem integrados agora aparecem como nós de DSC registrados em sua conta de automação do Azure.

## <a name="generating-dsc-metaconfigurations"></a>Gerando metaconfigurações de DSC

Para a bordo genericamente de qualquer máquina para a Configuração do Estado da Automação Azure, pode ser gerada uma [configuração DSC](/powershell/scripting/dsc/managing-nodes/metaConfig) que diga ao agente DSC para puxar e/ou reportar à Configuração do Estado da Automação Azure. As metaconfigurações de DSC para a configuração de estado de automação do Azure podem ser geradas usando uma configuração de DSC do PowerShell ou os cmdlets do PowerShell de automação do Azure.

> [!NOTE]
> Metaconfigurações de DSC contêm os segredos necessários para carregar um computador em uma conta de automação para gerenciamento. Certifique-se de proteger corretamente todas as metaconfigurações de DSC que você criar ou excluí-las após o uso.

### <a name="using-a-dsc-configuration"></a>Usando uma configuração DSC

1. Abra o VSCode (ou seu editor favorito) como administrador em um computador em seu ambiente local. A máquina deve ter a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) instalada.
1. Copie o script a seguir localmente. Esse script contém uma configuração DSC do PowerShell para criar metaconfigurações e um comando para iniciar a criação da metaconfiguração.

> [!NOTE]
> Nomes de configuração de nó de configuração de estado diferenciam maiúsculas de minúsculas no Portal. Se o caso estiver desajustado, o nó não aparecerá sob a conta de **nó.**

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

1. Preencha a chave de registro e a URL para sua conta de automação, bem como os nomes das máquinas para carregar. Todos os outros parâmetros são opcionais. Para encontrar a chave de registo e URL de registo da sua conta Deautomação, consulte a seguinte secção de [**registo Segura.** ](#secure-registration)
1. Se pretender que as máquinas reportem informações de estado dSC à Configuração do Estado da Automação Azure, mas não puxe a configuração ou os módulos PowerShell, defina o parâmetro **ReportOnly** como verdadeiro.
1. Execute o script. Deve agora ter uma pasta chamada **DscMetaConfigs** no seu diretório de trabalho, contendo as configurações do DSC powerShell para as máquinas a bordo (como administrador):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Usando os cmdlets de automação do Azure

Se os padrões de Configuration Manager local do DSC do PowerShell corresponderem ao seu caso de uso e você quiser integrar computadores de forma que eles sejam fornecidos e relatem à configuração de estado da automação do Azure, os cmdlets de automação do Azure fornecerão um método simplificado de geração as metaconfigurações de DSC são necessárias:

1. Abra o console do PowerShell ou VSCode como administrador em um computador em seu ambiente local.
2. Ligue-se ao Gestor de Recursos Azure usando `Connect-AzAccount`
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

1. Deve agora ter uma pasta chamada ***DscMetaConfigs,*** contendo as configurações do DSC PowerShell para as máquinas a bordo (como administrador):

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Registro seguro

Os computadores podem se integrar com segurança a uma conta de automação do Azure por meio do protocolo de registro de DSC do WMF 5, que permite que um nó DSC se autentique em um servidor de pull ou relatório de DSC do PowerShell (incluindo a configuração de estado da automação do Azure). O nó regista-se no servidor num URL de **Registo,** autenticando utilizando uma **tecla de registo**. Durante o registro, o nó de DSC e o servidor de recepção/relatório de DSC negociam um certificado exclusivo para esse nó usar para autenticação para o registro posterior do servidor. Esse processo impede que nós integrados se representem um do outro, como se um nó estiver comprometido e se comportando de forma mal-intencionada. Após o registro, a chave de registro não é usada novamente para autenticação e é excluída do nó.

Pode obter as informações necessárias para o protocolo de registo de Configuração do Estado a partir de **Chaves** ao abrigo das **Definições de Conta** no portal Azure. Abra esta lâmina clicando no ícone chave no painel **Essentials** para a conta Automation.

![Chaves e URL de automação do Azure](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- URL de registro é o campo URL na folha gerenciar chaves.
- A chave de registro é a chave de acesso primária ou a chave de acesso secundária na folha gerenciar chaves. Qualquer chave pode ser usada.

Para maior segurança, as chaves de acesso primário e secundário de uma conta Automation podem ser regeneradas a qualquer momento (na página **'Gerir Chaves)** para evitar futuras inscrições de nó usando chaves anteriores.

## <a name="certificate-expiration-and-re-registration"></a>Expiração e novo registro do certificado

Depois de registrar um computador como um nó DSC na configuração de estado da automação do Azure, há várias razões pelas quais você pode precisar registrar novamente esse nó no futuro:

- Para versões do Windows Server anteriores ao Windows Server 2019, cada nó negocia automaticamente um certificado exclusivo para autenticação que expira após um ano. Atualmente, o protocolo de registro DSC do PowerShell não pode renovar automaticamente os certificados quando eles estão se aproximando da expiração, portanto, você precisa registrar novamente os nós após a hora de um ano. Antes de registrar novamente, verifique se cada nó está executando o Windows Management Framework 5,0 RTM. Se o certificado de autenticação de um nó expirar e o nó não for registrado novamente, o nó não poderá se comunicar com a automação do Azure e será marcado como ' sem resposta '. o novo registro realizado em 90 dias ou menos do tempo de expiração do certificado, ou a qualquer momento após o tempo de expiração do certificado, resultará em uma nova geração e uso de um certificado.  Uma resolução para esse problema está incluída no Windows Server 2019 e posterior.
- Para alterar quaisquer valores do [PowerShell DSC Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig4) que foram definidos durante o registo inicial do nó, como o Modo de Configuração. Atualmente, esses valores do agente DSC só podem ser alterados por meio de um novo registro. A única exceção é a configuração de nó atribuída ao nó--isso pode ser alterado no Azure DSC de Automação diretamente.

o novo registro pode ser executado da mesma maneira que você registrou o nó inicialmente, usando qualquer um dos métodos de integração descritos neste documento. Você não precisa cancelar o registro de um nó da configuração de estado da automação do Azure antes de registrá-lo novamente.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Solucionando problemas de integração de máquina virtual do Azure

A configuração de estado da automação do Azure permite que você integre VMs do Windows do Azure com facilidade para gerenciamento de configuração. Nos bastidores, a extensão de configuração de estado desejado da VM do Azure é usada para registrar a VM com a configuração de estado da automação do Azure. Como a extensão de configuração de estado desejado da VM do Azure é executada de forma assíncrona, acompanhar seu progresso e solucionar problemas de sua execução pode ser importante.

> [!NOTE]
> Qualquer método de integração de uma VM do Windows do Azure à configuração de estado de automação do Azure que usa a extensão de configuração de estado desejado da VM do Azure pode levar até uma hora para que o nó seja exibido como registrado na automação do Azure. Isso ocorre devido à instalação do Windows Management Framework 5,0 na VM pela extensão de DSC da VM do Azure, que é necessária para integrar a configuração de estado da VM para a automação do Azure.

Para resolver problemas ou visualizar o estado da extensão de configuração de configuração de estado desejada Do Azure VM, no portal Azure navegar para o VM a bordo, clique em **Extensões** em **Definições**. Em seguida, clique em **DSC** ou **DSCForLinux** dependendo do seu sistema operativo. Para mais detalhes, pode clicar em **Ver estado detalhado**.

Para obter mais informações sobre resolução de problemas, consulte problemas de resolução de problemas com a [Configuração do Estado Desejada pela Automatização Azure (DSC)](./troubleshoot/desired-state-configuration.md).

## <a name="next-steps"></a>Passos seguintes

- Para começar, veja Começar com a Configuração do Estado da [Automação Azure](automation-dsc-getting-started.md)
- Para aprender sobre a compilação de configurações de DSC para que possa atribuí-las a nós-alvo, consulte [Configurações de Compilação na Configuração do Estado da Automação Azure](automation-dsc-compile.md)
- Para referência de cmdlet PowerShell, consulte [Os cmdlets](/powershell/module/az.automation#automation) de configuração do Estado da Automatização Do Azure
- Para obter informações sobre preços, consulte os preços de configuração do Estado da [Automação Do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de utilização da Configuração do Estado da Automação Azure num pipeline de implantação contínua, consulte a implantação contínua utilizando a configuração do Estado da [Automação Azure e](automation-dsc-cd-chocolatey.md) o Chocolatey
