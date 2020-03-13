---
title: Máquinas de embarque para gestão pela Configuração do Estado da Automação Azure
description: Como configurar máquinas para gestão com configuração do Estado da Automação Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: 89e86a6702be7314b99975cac90818252eb07df7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278964"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Máquinas de embarque para gestão pela Configuração do Estado da Automação Azure

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Porquê gerir máquinas com configuração do Estado da Automação Azure?

A Configuração do Estado da Automação Azure é um serviço de gestão de configuração para nós de configuração do Estado Desejado (DSC) em qualquer nuvem ou centro de dados no local.
Permite escalabilidade através de milhares de máquinas de forma rápida e fácil a partir de um local central e seguro.
Pode facilmente a bordo de máquinas, atribuindo-lhes configurações declarativas e ver relatórios que mostram a conformidade de cada máquina com o estado pretendido que especificou.
O serviço de Configuração do Estado da Automação Azure é para a DSC quais os livros de execução da Automação Azure para a escrita PowerShell.
Por outras palavras, da mesma forma que a Automatização Azure ajuda a gerir os scripts PowerShell, também o ajuda a gerir as configurações do DSC.
Para saber mais sobre os benefícios da utilização da Configuração do Estado da Automação Azure, consulte a visão geral da Configuração do Estado da [Automação Azure](automation-dsc-overview.md).

A Configuração do Estado da Automação Azure pode ser usada para gerir uma variedade de máquinas:

- Máquinas virtuais do Azure
- Máquinas virtuais azure (clássica)
- Máquinas de Janelas físicas/virtuais no local, ou numa nuvem diferente de Azure (incluindo instâncias AWS EC2)
- Máquinas linux físicas/virtuais no local, em Azure, ou numa nuvem que não o Azure

Além disso, se não estiver pronto para gerir a configuração da máquina a partir da nuvem, a Configuração do Estado da Automatização Azure também pode ser usada como ponto final apenas de relatório.
Isto permite-lhe definir configurações (push) através do DSC e visualizar detalhes de reporte na Automação Azure.

> [!NOTE]
> Gerir VMs Azure com Configuração estatal não é incluído a custo extra se a extensão DSC da máquina virtual instalada for superior a 2,70. Para mais informações, consulte [**a página de preços da Automação**](https://azure.microsoft.com/pricing/details/automation/).

As seguintes secções descrevem como pode embarcar em cada tipo de máquina para a Configuração do Estado da Automação Azure.

> [!NOTE]
>A implementação de DSC para um nó Linux utiliza a pasta `/tmp` e os módulos como **o nxAutomation** são temporariamente descarregados para verificação antes de os instalarem na sua localização adequada. Para garantir que os módulos se instalam corretamente, o agente Log Analytics para o Linux necessita de autorização de leitura/escrita na `/tmp` pasta. O agente Log Analytics para o Linux funciona como o utilizador `omsagent`. 
>
>Para conceder permissão de escrita para `omsagent` utilizador, execute os seguintes comandos: `setfacl -m u:omsagent:rwx /tmp`
>

## <a name="azure-virtual-machines"></a>Máquinas virtuais do Azure

A Configuração do Estado da Automação Azur permite-lhe facilmente embarcar em máquinas virtuais Azure para gestão de configuração, utilizando o portal Azure, os modelos do Gestor de Recursos Azure ou o PowerShell. Sob o capot, e sem que um administrador tenha de se repercidar no VM, a extensão de configuração de configuração de configuração de estado desejada Azure VM regista o VM com configuração do Estado da Automação Azure.
Uma vez que a extensão de configuração de configuração de estado desejada Azure funciona assincronicamente, as etapas para acompanhar o seu progresso ou resolução de problemas são fornecidas na seguinte secção de embarque da [**máquina virtual Troubleshooting Azure.** ](#troubleshooting-azure-virtual-machine-onboarding)

### <a name="azure-portal"></a>Portal do Azure

No [portal Azure,](https://portal.azure.com/)navegue para a conta Azure Automation onde pretende embarcar em máquinas virtuais. Na página de Configuração do Estado e no separador **Nós,** clique **+ Adicionar**.

Selecione uma máquina virtual Azure a bordo.

Se a máquina não tiver a extensão de estado desejada powerShell instalada e o estado de alimentação estiver em funcionamento, clique em **Connect**.

Em **'Registo',** introduza os valores do [PowerShell DSC Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig) necessários para a sua caixa de utilização e, opcionalmente, uma configuração de nó para atribuir ao VM.

![embarque](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

As máquinas virtuais Azure podem ser implantadas e a bordo para a Configuração do Estado da Automação Azure através dos modelos do Gestor de Recursos Azure. Consulte o [Servidor gerido pelo serviço de Configuração do Estado Desejado](https://azure.microsoft.com/resources/templates/101-automation-configuration/) para um modelo de exemplo que a bordo de um VM existente para configuração do Estado de Automação Azure.
Se estiver a gerir um conjunto de escala de máquina virtual, consulte o modelo de exemplo Conjunto de [escala de máquina virtual Configuração gerida pela Automação Azure](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="powershell"></a>PowerShell

O [cmdlet Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) pode ser utilizado para a bordo de máquinas virtuais em Azure utilizando o PowerShell.
No entanto, atualmente, esta é apenas implementada apenas para máquinas que executam o Windows (o cmdlet apenas aciona a extensão do Windows).

### <a name="registering-virtual-machines-across-azure-subscriptions"></a>Registar máquinas virtuais através de assinaturas Azure

A melhor maneira de registar máquinas virtuais de outras subscrições do Azure é utilizar a extensão DSC num modelo de implementação do Gestor de Recursos Azure.
Exemplos são fornecidos na [extensão de Configuração do Estado Desejada com modelos de Gestor de Recursos Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).
Para encontrar a chave de registo e o URL de registo para utilizar como parâmetros no modelo, consulte a seguinte secção de [**registo Segura.** ](#secure-registration)

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances"></a>Máquinas de Janelas físicas/virtuais no local, ou numa nuvem diferente de Azure (incluindo instâncias AWS EC2)

Os servidores windows que executam no local ou em outros ambientes em nuvem também podem ser a bordo da Configuração do Estado da Automação Azure, desde que tenham [acesso de saída ao Azure:](automation-dsc-overview.md#network-planning)

1. Certifique-se de que a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) está instalada nas máquinas que pretende instalar na Configuração do Estado da Automação Azure.
1. Siga as instruções na secção seguinte [**Gerando configurações dSC**](#generating-dsc-metaconfigurations) para gerar uma pasta contendo as configurações dSC necessárias.
1. Aplique remotamente a configuração DoC PowerShell nas máquinas que pretende fazer a bordo. A máquina de **onde este comando é executado deve ter a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) instalada:**

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Se não conseguir aplicar as configurações do DSC PowerShell remotamente, copie a pasta de configurações do passo 2 para cada máquina para bordo. Em seguida, ligue para **set-DscLocalConfigurationManager** localmente em cada máquina para bordo.
1. Utilizando o portal Azure ou cmdlets, verifique se as máquinas a bordo aparecem como nós de Configuração Do Estado registados na sua conta De sueste Automatização.

## <a name="physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Máquinas linux físicas/virtuais no local, ou numa nuvem que não o Azure

Os servidores Linux que executam no local ou em outros ambientes em nuvem também podem ser a bordo da Configuração do Estado da Automação Azure, desde que tenham [acesso de saída ao Azure:](automation-dsc-overview.md#network-planning)

1. Certifique-se de que a mais recente versão da [Configuração do Estado PowerShell Desired para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) está instalada nas máquinas que pretende fazer a bordo da Configuração do Estado da Automação Azure.
2. Se o [PowerShell DSC Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig4) corresponder ao seu caso de utilização, e quiser a bordo de máquinas de modo a que **ambos** retirem e reportem à Configuração do Estado da Automação Do Azure:

   - Em cada máquina Linux para embarcar na Configuração do Estado da Automação Azure, utilize `Register.py` para bordo utilizando os predefinições do PowerShell DSC Local Configuration Manager:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Para encontrar a chave de registo e URL de registo da sua conta Deautomação, consulte a seguinte secção de [**registo Segura.** ](#secure-registration)

     Se as predefinições do PowerShell DSC Local Configuration Manager **não** corresponderem ao seu caso de utilização, ou se pretender a bordo de máquinas de modo a que apenas reportem à Configuração do Estado da Automação Azure, siga os passos 3 - 6. Caso contrário, proceda diretamente ao passo 6.

3. Siga as instruções na secção de [**configurações DSC geradora**](#generating-dsc-metaconfigurations) seguinte para gerar uma pasta contendo as configurações dSC necessárias.
4. Aplique remotamente a configuração DoC PowerShell nas máquinas que pretende a bordo:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

A máquina de onde este comando é executado deve ter a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) instalada.

1. Se não conseguir aplicar as configurações do DSC PowerShell remotamente, copie a configuração correspondente à que a máquina a partir da pasta no passo 5 para a máquina Linux. Em seguida, ligue `SetDscLocalConfigurationManager.py` localmente em cada máquina Linux que deseja embarcar para a Configuração do Estado da Automação Azure:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

2. Utilizando o portal Azure ou cmdlets, verifique se as máquinas a bordo aparecem agora como nós DSC registados na sua conta Azure Automation.

## <a name="generating-dsc-metaconfigurations"></a>Gerando configurações dSC

Para a bordo genericamente de qualquer máquina para a Configuração do Estado da Automação Azure, pode ser gerada uma [configuração DSC](/powershell/scripting/dsc/managing-nodes/metaConfig) que diga ao agente DSC para puxar e/ou reportar à Configuração do Estado da Automação Azure. As configurações dSC para configuração do Estado de Automação Azure podem ser geradas usando uma configuração PowerShell DSC, ou os cmdlets De PowerShell de Automação Azure.

> [!NOTE]
> As configurações do DSC contêm os segredos necessários para embarcar numa máquina para uma conta de Automação para gestão. Certifique-se de que protege corretamente quaisquer configurações DSC que crie ou elimine-as após a utilização.

### <a name="using-a-dsc-configuration"></a>Usando uma configuração DSC

1. Abra o VSCode (ou o seu editor favorito) como administrador numa máquina no seu ambiente local. A máquina deve ter a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) instalada.
1. Copie o seguinte guião localmente. Este script contém uma configuração DeSC PowerShell para criar configurações, e um comando para iniciar a criação de configuração.

> [!NOTE]
> Configuração do Estado Os nomes de configuração do nó são sensíveis a casos no portal. Se o caso estiver desajustado, o nó não aparecerá sob a conta de **nó.**

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

1. Preencha a chave de registo e o URL para a sua conta Automation, bem como os nomes das máquinas a bordo. Todos os outros parâmetros são opcionais. Para encontrar a chave de registo e URL de registo da sua conta Deautomação, consulte a seguinte secção de [**registo Segura.** ](#secure-registration)
1. Se pretender que as máquinas reportem informações de estado dSC à Configuração do Estado da Automação Azure, mas não puxe a configuração ou os módulos PowerShell, defina o parâmetro **ReportOnly** como verdadeiro.
1. Execute o script. Deve agora ter uma pasta chamada **DscMetaConfigs** no seu diretório de trabalho, contendo as configurações do DSC powerShell para as máquinas a bordo (como administrador):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Utilização dos cmdlets de Automação Azure

Se os predefinições do PowerShell DSC Local Configuration Manager corresponderem ao seu caso de utilização, e pretender a bordo de máquinas de modo a que ambos retirem e reportem à Configuração do Estado da Automação Azure, os cmdlets de Automação Azure fornecem um método simplificado de geração as configurações dSC necessárias:

1. Abra a consola PowerShell ou VSCode como administrador numa máquina no seu ambiente local.
2. Ligue-se ao Gestor de Recursos Azure usando `Connect-AzAccount`
3. Descarregue as configurações do PowerShell DSC para as máquinas que pretende embarcar a partir da conta Automation para a qual pretende embarcar nos dedos:

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

## <a name="secure-registration"></a>Registo seguro

As máquinas podem embarcar de forma segura a bordo de uma conta De automação Azure através do protocolo de registo WMF 5 DSC, que permite que um nó DSC autentique a um servidor powerShell DSC Pull ou Reporting (incluindo a Configuração do Estado da Automação Azure). O nó regista-se no servidor num URL de **Registo,** autenticando utilizando uma **tecla de registo**. Durante o registo, o nó DSC e o servidor DSC Pull/Reporting negoceiam um certificado único para este nó utilizar para autenticação no registo pós-registo do servidor. Este processo impede que nós a bordo se personiquem uns aos outros, como se um nó estivesse comprometido e se comportasse maliciosamente. Após o registo, a chave de registo não é utilizada novamente para autenticação e é eliminada do nó.

Pode obter as informações necessárias para o protocolo de registo de Configuração do Estado a partir de **Chaves** ao abrigo das **Definições de Conta** no portal Azure. Abra esta lâmina clicando no ícone chave no painel **Essentials** para a conta Automation.

![Chaves de automação azure e URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- URL de registo é o campo URL na lâmina 'Gerir chaves'.
- A chave de registo é a chave de acesso primário ou a chave de acesso secundário na lâmina 'Gerir chaves'. Qualquer uma das teclas pode ser usada.

Para maior segurança, as chaves de acesso primário e secundário de uma conta Automation podem ser regeneradas a qualquer momento (na página **'Gerir Chaves)** para evitar futuras inscrições de nó usando chaves anteriores.

## <a name="certificate-expiration-and-re-registration"></a>Expiração e reinscrição do certificado

Depois de registar uma máquina como nó DSC na Configuração do Estado da Automação Azure, existem várias razões pelas quais poderá ter de voltar a registar esse nó no futuro:

- Para versões do Windows Server antes do Windows Server 2019, cada nó negoceia automaticamente um certificado único para autenticação que expira após um ano. Atualmente, o protocolo de registo DoC PowerShell não pode renovar automaticamente os certificados quando estão perto da expiração, pelo que precisa de voltar a registar os nós após um ano. Antes de voltar a registar-se, certifique-se de que cada nó está a executar o Windows Management Framework 5.0 RTM. Se o certificado de autenticação de um nó expirar e o nó não for reregistado, o nó não pode comunicar com a Automação Azure e está marcado como "Sem Resposta". A reinscrição efetuada 90 dias ou menos a partir do prazo de validade do certificado, ou em qualquer ponto após o prazo de validade do certificado, resultará na geração e uso de um novo certificado.  Uma resolução para este problema está incluída no Windows Server 2019 e posteriormente.
- Para alterar quaisquer valores do [PowerShell DSC Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig4) que foram definidos durante o registo inicial do nó, como o Modo de Configuração. Atualmente, estes valores de agente DSC só podem ser alterados através de um reregisto. A única exceção é a Configuração do Nó atribuída ao nó -- isto pode ser alterado diretamente em Azure Automation DSC.

A reinscrição pode ser realizada da mesma forma que registou o nó inicialmente, utilizando qualquer um dos métodos de embarque descritos neste documento. Não é necessário desregistar um nó da Configuração do Estado da Automação Azure antes de o reregistar.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Resolução de problemas máquina virtual Azure no embarque

A Configuração do Estado da Automação Azure permite-lhe facilmente embarcar em VMs do Windows Azure para gestão de configuração. Sob o capot, a extensão de configuração de configuração do Estado Desejada Azure VM é utilizada para registar o VM com configuração do Estado de Automação Azure. Uma vez que a extensão de Configuração estatal desejada azure VM corre assincronicamente, acompanhar o seu progresso e resolver a sua execução pode ser importante.

> [!NOTE]
> Qualquer método de embarcar num Azure Windows VM para a Configuração do Estado de Automação Azure que utilize a extensão de Configuração do Estado Desejada Azure VM pode demorar até uma hora para o nó aparecer como registado na Azure Automation. Isto deve-se à instalação do Windows Management Framework 5.0 no VM pela extensão DSC Azure VM, que é necessária para embarcar no VM para a Configuração do Estado da Automação Azure.

Para resolver problemas ou visualizar o estado da extensão de configuração de configuração de estado desejada Do Azure VM, no portal Azure navegar para o VM a bordo, clique em **Extensões** em **Definições**. Em seguida, clique em **DSC** ou **DSCForLinux** dependendo do seu sistema operativo. Para mais detalhes, pode clicar em **Ver estado detalhado**.

Para obter mais informações sobre resolução de problemas, consulte problemas de resolução de problemas com a [Configuração do Estado Desejada pela Automatização Azure (DSC)](./troubleshoot/desired-state-configuration.md).

## <a name="next-steps"></a>Passos seguintes

- Para começar, veja Começar com a Configuração do Estado da [Automação Azure](automation-dsc-getting-started.md)
- Para aprender sobre a compilação de configurações de DSC para que possa atribuí-las a nós-alvo, consulte [Configurações de Compilação na Configuração do Estado da Automação Azure](automation-dsc-compile.md)
- Para referência de cmdlet PowerShell, consulte [Os cmdlets](/powershell/module/az.automation#automation) de configuração do Estado da Automatização Do Azure
- Para obter informações sobre preços, consulte os preços de configuração do Estado da [Automação Do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de utilização da Configuração do Estado da Automação Azure num pipeline de implantação contínua, consulte a implantação contínua utilizando a configuração do Estado da [Automação Azure e](automation-dsc-cd-chocolatey.md) o Chocolatey
