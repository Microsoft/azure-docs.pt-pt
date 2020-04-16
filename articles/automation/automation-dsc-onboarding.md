---
title: Máquinas de bordo para gestão pela Configuração do Estado da Automação Azure
description: Este artigo descreve como configurar máquinas para gestão com a Configuração do Estado da Automação Azure.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 04/15/2020
manager: carmonm
ms.openlocfilehash: 4fc9f701f4f832deb1fed34d7ac9f888521d3830
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406207"
---
# <a name="onboard-machines-for-management-by-azure-automation-state-configuration"></a>Máquinas de bordo para gestão pela Configuração do Estado da Automação Azure

A Configuração do Estado da Automação Azure é um serviço de gestão de configuração para nós de configuração do Estado Desejado (DSC) em centros de dados em nuvem ou no local. Pode aceder ao serviço no portal Azure selecionando a **configuração do Estado (DSC)** em gestão de **configuração**. 

Com a Configuração do Estado da Automação Azure, pode obter escalabilidade através de milhares de máquinas de forma rápida e fácil a partir de uma localização central e segura. Pode facilmente a bordo de máquinas, atribuir-lhes configurações declarativas e ver relatórios que mostrem a conformidade de cada máquina com o estado que especificou.

O serviço de Configuração do Estado da Automação Azure é para a DSC quais os livros de execução da Automação Azure para a escrita PowerShell. Por outras palavras, da mesma forma que a Automatização Azure ajuda a gerir os scripts PowerShell, também o ajuda a gerir as configurações do DSC. Para saber mais sobre os benefícios da utilização da Configuração do Estado da Automação Azure, consulte a visão geral da Configuração do Estado da [Automação Azure](automation-dsc-overview.md).

Pode utilizar a Configuração do Estado da Automação Azure para gerir uma variedade de máquinas:

- Máquinas virtuais (VMs) do Azure
- Máquinas virtuais azure (clássica)
- Máquinas de Windows físicas/virtuais no local, ou numa nuvem diferente do Azure (incluindo os casos de Nuvem de Computação Elástica [AWS] Elástico da Amazon [AWS])
- Máquinas linux físicas/virtuais no local, em Azure, ou numa nuvem que não o Azure

Se não estiver pronto para gerir a configuração da máquina a partir da nuvem, pode utilizar a Configuração do Estado da Automatização Azure como ponto final apenas de reporte. Esta funcionalidade permite-lhe definir, ou empurrar, configurações através do DSC e ver detalhes de reporte na Automação Azure.

> [!NOTE]
> Gerir VMs Azure com configuração do Estado de Automação Azure não está incluído a custo extra se a extensão de configuração de configuração do Estado do Estado Do Azure VM instalada for a versão 2.70 ou posterior. Para mais informações, consulte [os preços da Automação.](https://azure.microsoft.com/pricing/details/automation/)

As seguintes secções deste artigo descrevem como pode embarcar nas máquinas previamente listadas para a Configuração do Estado da Automação Azure.

## <a name="onboard-azure-vms"></a>VMs Azure a bordo

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az para o seu Trabalhador de Livro híbrido, consulte [Instalar o módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente utilizando [módulos Update Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

Como a extensão Azure funciona assincronicamente, fornecemos passos para acompanhar o seu progresso ou resolver problemas na secção de embarque da [máquina virtual Troubleshoot Azure](#troubleshoot-azure-virtual-machine-onboarding) deste artigo.

> [!NOTE]
> A implantação de DSC para um nó Linux utiliza a pasta */tmp.* Módulos como `nxautomation` são temporariamente descarregados para verificação antes de serem instalados. Para garantir que os módulos estão instalados corretamente, o agente Log Analytics para o Linux necessita de permissões de leitura/escrita na pasta */tmp.*<br><br>
> O agente Log Analytics para o Linux funciona como utilizador de *omsagent.* Para conceder permissão de escrita ao utilizador `setfacl -m u:omsagent:rwx /tmp` *omsagent,* execute o comando .

### <a name="onboard-vms-by-using-the-azure-portal"></a>VMs a bordo usando o portal Azure

Para embarcar VMs Azure para Azure Automation State Configuração através do [portal Azure:](https://portal.azure.com/)

1. Vá à conta da Automação Azure na qual pretende embarcar Em VMs. 

1. Na página **de Configuração do Estado,** selecione o separador **Nós** e, em seguida, selecione **Adicionar**.

1. Escolha um VM a bordo.

1. Se a máquina não tiver a extensão powerShell Desireed State instalada e o estado de alimentação estiver em funcionamento, selecione **Connect**.

1. Em **'Registo',** introduza os valores de [Configuração Local PowerShell DSC (LCM)](/powershell/scripting/dsc/managing-nodes/metaConfig) necessários para a sua caixa de utilização. Opcionalmente, pode introduzir uma configuração de nó para atribuir ao VM.

![O painel de registo VM](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-vms-by-using-azure-resource-manager-templates"></a>VMs de bordo usando modelos de Gestor de Recursos Azure

Pode implantar e embarcar um VM para a Configuração do Estado da Automação Azure utilizando modelos de Gestor de Recursos Azure. Para um modelo de exemplo que a bordo de um VM existente para configuração do Estado de Automação Azure, consulte [server gerido pelo serviço de Configuração do Estado Desejado](https://azure.microsoft.com/resources/templates/101-automation-configuration/). Se estiver a gerir um conjunto de escala de máquina virtual, consulte o modelo de exemplo na configuração de conjunto de escala de [máquina virtual gerida pela Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="onboard-vms-by-using-powershell"></a>VMs a bordo usando powerShell

Pode utilizar o [cmdlet Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) na PowerShell para a bordo de VMs para a Configuração do Estado da Automação Azure. 

> [!NOTE]
>O `Register-AzAutomationDscNode` cmdlet é implementado atualmente apenas para máquinas que executam o Windows, porque o cmdlet dispara apenas a extensão do Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Registe Os VMs através das assinaturas Do Azure

A melhor maneira de registar VMs de outras subscrições do Azure é usar a extensão DSC num modelo de implementação do Gestor de Recursos Azure. Exemplos são fornecidos na [extensão de Configuração do Estado Desejada com modelos de Gestor de Recursos Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).

Para encontrar a chave de registo e o URL de registo para utilizar como parâmetros no modelo, consulte o [Bordo de forma segura utilizando](#onboard-securely-by-using-registration) a secção de registo neste artigo.

## <a name="onboard-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Máquinas de janelas físicas/virtuais a bordo no local, ou numa nuvem diferente de Azure

Pode embarcar em servidores Windows que estejam a funcionar no local ou noutros ambientes em nuvem, incluindo instâncias AWS EC2, para a Configuração do Estado da Automação Azure. Os servidores devem ter [acesso de saída ao Azure](automation-dsc-overview.md#network-planning).

1. Certifique-se de que a versão mais recente do [Windows Management Framework 5](https://aka.ms/wmf5latest) está instalada nas máquinas a bordo da Configuração do Estado da Automação Azure. Além disso, o Windows Management Framework 5 deve ser instalado no computador que está a utilizar para a operação de embarque.
1. Para criar uma pasta que contenha as configurações dSC necessárias, siga as instruções na secção de [configurações DeDSC gerada.](#generate-dsc-metaconfigurations) 
1. Para aplicar as configurações do DSC PowerShell remotamente nas máquinas que pretende embarcar, utilize o seguinte cmdlet: 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Se não conseguir aplicar as configurações do DSC PowerShell remotamente, copie a pasta de **configurações** para as máquinas que está a embarcar. Em seguida, adicione código para ligar [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) localmente nas máquinas.

1. No portal Azure ou utilizando cmdlets, verifique se as máquinas a bordo aparecem como nós de Configuração Do Estado registados na sua conta De automação Azure.

## <a name="onboard-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Máquinas linux físicas/virtuais a bordo no local, ou numa nuvem que não o Azure

Pode embarcar em servidores Linux que executam no local ou em outros ambientes em nuvem para a Configuração do Estado da Automação Azure. Os servidores devem ter [acesso de saída ao Azure](automation-dsc-overview.md#network-planning).

1. Certifique-se de que a mais recente versão da [Configuração do Estado PowerShell Desired para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) está instalada nas máquinas a bordo da Configuração do Estado da Automação Azure.
1. Se as [predefinições do PowerShell DSC LCM](/powershell/scripting/dsc/managing-nodes/metaConfig4) corresponderem ao seu estojo de utilização, e pretender embarcar nas máquinas para que ambos se retirem e apresentem à Configuração do Estado da Automação Azure, faça o seguinte:

   a. Em cada máquina Linux para embarcar na Configuração do Estado da Automatização Azure, utilize `Register.py` para embarcar utilizando as predefinições do PowerShell DSC Local Configuration Manager.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   b. Para encontrar a chave de registo e url de registo da sua conta Deautomação, consulte o Bordo de [forma segura utilizando](#onboard-securely-by-using-registration) a secção de registo deste artigo.  
   c. Passe para o passo 4.
   
1. Se as predefinições do PowerShell DSC LCM não corresponderem ao seu caso de utilização, ou se pretender embarcar em máquinas que reportem apenas à Configuração do Estado da Automação Azure, siga os passos a-d. Caso contrário, proceda diretamente ao passo d.

    a. Para produzir uma pasta que contenha as configurações dSC necessárias, siga as instruções na secção de [configurações DeDSC gerada.](#generate-dsc-metaconfigurations)

    b. Certifique-se de que a versão mais recente do [Windows Management Framework 5](https://aka.ms/wmf5latest) está instalada na máquina que está a ser utilizada para o embarque.

    c. Para aplicar as configurações do DSC PowerShell remotamente nas máquinas que pretende embarcar, adicione o seguinte código:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

    d. Se não conseguir aplicar as configurações do DSC PowerShell remotamente, copie as configurações correspondentes às máquinas remotas da pasta descrita no passo 3a para as máquinas Linux.

1. Adicione código para chamar *Set-DscLocalConfigurationManager.py* localmente em cada máquina Linux para ser a bordo da Configuração do Estado da Automação Azure.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Utilizando o portal Azure ou cmdlets, certifique-se de que as máquinas a bordo aparecem agora como nós DSC registados na sua conta Azure Automation.

## <a name="generate-dsc-metaconfigurations"></a>Gerar configurações dSC

Para embarcar em qualquer máquina para a Configuração do Estado da Automação Azure, pode gerar uma [configuração DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Esta configuração diz ao agente DSC para retirar ou reportar à Configuração do Estado da Automação Azure. Pode gerar uma configuração DSC para configuração do Estado da Automatização Azure utilizando uma configuração PowerShell DSC ou os cmdlets De PowerShell de Automação Azure.

> [!NOTE]
> As configurações do DSC contêm os segredos que precisa para embarcar numa máquina para uma conta de Automação para gestão. Certifique-se de que protege corretamente quaisquer configurações DSC que crie ou elimine-as após a utilização.

O suporte proxy para configurações é controlado por LCM, que é o motor DSC Windows PowerShell. O LCM funciona em todos os nós-alvo e é responsável por chamar os recursos de configuração que estão incluídos num script de configuração DSC. 

Pode incluir suporte proxy numa configuração, incluindo definições do URL proxy e `ConfigurationRepositoryWeb` `ResourceRepositoryWeb`da `ReportServerWeb` credencial de procuração, conforme necessário nos blocos e blocos. Consulte [Configurar o Gestor](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7)de Configuração Local .

### <a name="generate-dsc-metaconfigurations-by-using-a-dsc-configuration"></a>Gerar configurações DSC utilizando uma configuração DSC

1. Open Visual Studio Code (ou o seu editor favorito) como administrador numa máquina no seu ambiente local. A máquina deve ter a versão mais recente do [Windows Management Framework 5](https://aka.ms/wmf5latest) instalada.
1. Copie o seguinte guião localmente. Este script contém uma configuração DeSC PowerShell para criar configurações, e um comando para iniciar a criação de configuração.

    > [!NOTE]
    > Configuração do Estado Nomes de configuração do nó são sensíveis a casos no portal Azure. Se o caso não for desajustado, o nó não aparecerá sob a conta dos **Nós.**

   ```powershell
   # The DSC configuration that will generate metaconfigurations.
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

    # Create the metaconfigurations.
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: Edit the following as needed for your use case.
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

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked.
   # For more info about splatting, run: Get-Help -Name about_Splatting.
   DscMetaConfigs @Params
   ```

1. Preencha a chave de registo e o URL para a sua conta Automation, bem como os nomes das máquinas a bordo. Todos os outros parâmetros são opcionais. Para encontrar a chave de registo e url de registo da sua conta Automation, consulte o Bordo de forma segura utilizando a secção [de registo.](#onboard-securely-by-using-registration)

1. Se pretender que as máquinas reportem informações de estado dSC à Configuração do `ReportOnly` Estado da Automação Azure, mas não puxe a configuração ou os módulos PowerShell, defina o parâmetro como *verdadeiro*.

1. Se `ReportOnly` não estiver definido, as máquinas reportam informações de estado dSC à Configuração do Estado da Automação Azure e à configuração de puxar ou módulos PowerShell. Coloque os parâmetros `ConfigurationRepositoryWeb`em `ResourceRepositoryWeb`conformidade `ReportServerWeb` nos blocos e blocos.

1. Execute o script. Deverá agora ter uma pasta de diretório de trabalho chamada *DscMetaConfigs,* que contém as configurações do DSC PowerShell para as máquinas a bordo (como administrador).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-by-using-azure-automation-cmdlets"></a>Gerar configurações DSC utilizando cmdlets de automação Azure

Se as predefinições Do PowerShell DSC LCM corresponderem à sua caixa de utilização e pretender que as máquinas de bordo possam puxar e reportar à Configuração do Estado da Automação Azure, pode gerar as configurações dSC necessárias mais simplesmente utilizando os cmdlets de automação Azure.

1. Abra a consola PowerShell ou o Código do Estúdio Visual como administrador numa máquina no seu ambiente local.
1. Ligue-se ao Gestor de Recursos Azure utilizando [o Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
1. A partir da conta Automation em que está a configurar nós, descarregue as configurações do PowerShell DSC para as máquinas que pretende embarcar.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting.
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked.
   # For more info about splatting, run: Get-Help -Name about_Splatting.
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Deverá agora ter uma pasta *DscMetaConfigs* que contém as configurações do DSC PowerShell para as máquinas que pretende embarcar (como administrador).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboard-securely-by-using-registration"></a>A bordo de forma segura, utilizando o registo

As máquinas podem ser a bordo de forma segura para uma conta De Automação Azure através do protocolo de registo do Windows Management Framework 5 DSC. Este protocolo permite que um nó DSC autentique a um servidor de puxar ou reportar PowerShell DSC, incluindo a Configuração do Estado da Automação Azure. O nó regista-se com o servidor no URL de registo e autentica utilizando uma chave de registo. 

Durante o registo, o nó DSC e o servidor dSC pull/report negoceiam um certificado único para o nó usar para autenticação no registo pós-registo do servidor. Este processo impede que nós a bordo se personiquem uns aos outros (por exemplo, se um nó estiver comprometido e se comportar maliciosamente). 

Após o registo, a chave de registo não é reutilizada para autenticação e é eliminada do nó.

Para obter a informação necessária para o protocolo de registo de Configuração do Estado, vá ao portal Azure e, em Definições de **Conta,** selecione **Keys**. 

![Chaves de automação azure e URL no painel de Teclas](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- **URL de registo**: O valor na caixa **URL.**
- **Chave de registo**: O valor na **caixa-chave** de acesso primário ou na **caixa-chave** de acesso secundário. Pode usar qualquer uma das chaves.

Para maior segurança, pode regenerar as chaves de acesso primária e secundária de uma conta de Automação a qualquer momento no painel **keys.** A regeneração-chave impede que futuras inscrições nonóufodos utilizem chaves anteriores.

## <a name="re-register-a-node"></a>Re-registe um nó

Depois de registar uma máquina como nó DSC na Configuração do Estado da Automação Azure, poderá ter de voltar a registar esse nó no futuro por qualquer uma das seguintes razões:

- **Renovação**do certificado : Para versões do Windows Server mais cedo do que o Windows Server 2019, cada nó negoceia automaticamente um certificado único de autenticação que expira após um ano. Se um certificado expirar sem renovação, o nó não pode comunicar com a Automação Azure e está marcado *Sem Resposta*. 

  Atualmente, o protocolo de registo DoC PowerShell não pode renovar automaticamente os certificados quando estão perto da expiração, e deve voltar a registar os nós após um ano. Antes de voltar a registar-se, certifique-se de que cada nó está a executar o Windows Management Framework 5 RTM. 

    A reinscrição efetuada 90 ou menos dias a partir do prazo de validade do certificado, ou em qualquer ponto após o prazo de validade do certificado, resulta na geração e uso de um novo certificado. Uma resolução para este problema está incluída no Windows Server 2019 e posteriormente.

- **Alterações aos valores DSC LCM**: Pode ser necessário alterar os valores Do [PowerShell DSC LCM](/powershell/scripting/dsc/managing-nodes/metaConfig4) que foram definidos durante o registo inicial do nó (por exemplo, `ConfigurationMode`). Atualmente, pode alterar estes valores de agente DSC apenas através de re-registo. A única exceção é o valor de configuração do nó atribuído ao nó. Pode alterar este valor diretamente em DSC de Automação Azure.

Pode voltar a registar um nó tal como o registou inicialmente, utilizando qualquer um dos métodos de embarque descritos neste documento. Não precisa de desregistar um nó da Configuração do Estado da Automação Azure antes de o voltar a registar.

## <a name="troubleshoot-azure-virtual-machine-onboarding"></a>Máquina virtual de Troubleshoot Azure a bordo

A Configuração do Estado da Automação Azure permite-lhe facilmente embarcar em VMs do Windows Azure para gestão de configuração. Sob o capot, a extensão de configuração de configuração do Estado Desejada Azure VM é utilizada para registar o VM com configuração do Estado de Automação Azure. Como a extensão de Configuração estatal desejada Azure VM funciona assincronicamente, acompanhar o seu progresso e resolver a sua execução pode ser importante.

> [!NOTE]
> Qualquer método de embarcar num Azure Windows VM para a Configuração do Estado de Automação Azure que utilize a extensão de Configuração do Estado Desejada Azure VM pode demorar até uma hora para a Automação Azure mostrar como registada. Este atraso resulta da instalação da Estrutura de Gestão do Windows 5 no VM pela extensão de configuração de configuração de configuração do Estado Desejada Azure VM, que é necessária para embarcar no VM para a Configuração do Estado de Automação Azure.

Para resolver problemas ou visualizar o estado da extensão de configuração de configuração do Estado Desejada Do Estado Azure VM:

1. No portal Azure, vá ao VM que está a bordo.
2. Em **Definições**, selecione **Extensões**. 
3. Selecione **DSC** ou **DSCForLinux,** dependendo do seu sistema operativo. 
4. Para mais informações sobre a extensão, selecione **Ver estado detalhado**.

Para mais informações sobre resolução de [problemas, consulte problemas de resolução de problemas com a Configuração do Estado Desejado pela Automatização Azure (DSC)](./troubleshoot/desired-state-configuration.md).

## <a name="next-steps"></a>Passos seguintes

- Para começar, veja [Get started com Azure Automation State Configuration](automation-dsc-getting-started.md).
- Para aprender sobre a compilação de configurações de DSC para que possa atribuí-las a nós-alvo, consulte [configurações de Compile na Configuração do Estado da Automação Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Para obter informações sobre preços, consulte os preços de configuração do Estado da [Automatização do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para um exemplo de utilização da Configuração do Estado da Automação Azure num gasoduto de implantação contínua, consulte a [implantação contínua de máquinas virtuais utilizando a Configuração do Estado da Automação Azure e o Chocolatey.](automation-dsc-cd-chocolatey.md)
