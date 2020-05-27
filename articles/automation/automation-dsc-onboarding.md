---
title: Ativar a configuração do Estado da Automação Azure
description: Este artigo diz como configurar máquinas para gestão com a Configuração do Estado da Automação Azure.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: f30d15615e4f3c738d969d068bf2864df23e7cdb
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836911"
---
# <a name="enable-azure-automation-state-configuration"></a>Ativar a configuração do Estado da Automação Azure

Este tópico descreve como pode configurar as suas máquinas para gestão com a Configuração do Estado da Automação Azure. Para mais detalhes deste serviço, consulte a visão geral da Configuração do Estado da [Automatização do Azure](automation-dsc-overview.md).

## <a name="enable-azure-vms"></a>Ativar VMs Azure

A Configuração do Estado da Automação Azure permite facilmente ativar os VMs Azure para a gestão de configuração, utilizando o portal Azure, os modelos do Gestor de Recursos Azure ou powerShell. Sob o capot, e sem que um administrador tenha de se transformar num VM, a extensão de configuração de configuração de configuração de estado desejada Azure VM regista o VM com configuração do Estado da Automação Azure. Uma vez que a extensão Azure funciona assincronicamente, são fornecidas medidas para acompanhar o seu progresso no [estado de verificação da configuração vm](#check-status-of-vm-setup).

> [!NOTE]
>A implantação de DSC para um nó Linux utiliza a pasta **/tmp.** Módulos como `nxautomation` são temporariamente descarregados para verificação antes de os instalarem nos locais apropriados. Para garantir que os módulos se instalam corretamente, o agente Log Analytics para o Linux necessita de permissões de leitura/escrita na pasta **/tmp.**<br><br>
>O agente Log Analytics para o Linux funciona como `omsagent` utilizador. Para conceder >autorizar o `omsagent` utilizador, execute o comando `setfacl -m u:omsagent:rwx /tmp` .

### <a name="enable-a-vm-using-azure-portal"></a>Ativar um VM utilizando o portal Azure

Para permitir uma Configuração Estatal de Um VM Azure através do [portal Azure:](https://portal.azure.com/)

1. Navegue para a conta De automação Azure na qual permita vMs. 

2. Na página de Configuração do Estado, selecione o separador **Nós** e, em seguida, clique em **Adicionar**.

3. Escolha um VM para ativar.

4. Se a máquina não tiver a extensão de estado desejada pela PowerShell instalada e o estado de alimentação estiver em funcionamento, clique em **Connect**.

5. Em **'Registo',** introduza os valores do [PowerShell DSC Local Configuration Manager necessários](/powershell/scripting/dsc/managing-nodes/metaConfig) para a sua caixa de utilização. Opcionalmente, pode introduzir uma configuração de nó para atribuir ao VM.

![permitindo vM](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>Ativar um VM utilizando modelos de Gestor de Recursos Azure

Pode instalar e ativar um VM para configuração do Estado utilizando modelos do Gestor de Recursos Azure. Consulte o [Servidor gerido pelo serviço de Configuração do Estado Desejado](https://azure.microsoft.com/resources/templates/101-automation-configuration/) para um modelo de exemplo que permite um VM existente para configuração de estado. Se estiver a gerir um conjunto de escala de máquina virtual, consulte o modelo de exemplo na configuração de conjunto de escala de [máquina virtual gerida pela Automação Azure](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="enable-machines-using-powershell"></a>Ativar máquinas utilizando powerShell

Pode utilizar o [cmdlet Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) no PowerShell para ativar os VMs para a Configuração do Estado. 

> [!NOTE]
>O `Register-AzAutomationDscNode` cmdlet é implementado atualmente apenas para máquinas que executam o Windows, uma vez que dispara apenas a extensão do Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Registe Os VMs através das assinaturas Do Azure

A melhor maneira de registar VMs de outras subscrições do Azure é usar a extensão DSC num modelo de implementação do Gestor de Recursos Azure. Exemplos são fornecidos na [extensão de Configuração do Estado Desejada com modelos de Gestor de Recursos Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).

Para encontrar a chave de registo e o URL de registo para utilizar como parâmetros no modelo, consulte [as máquinas ativarem as máquinas com segurança utilizando](#enable-machines-securely-using-registration)o registo .

## <a name="enable-physicalvirtual-windows-machines"></a>Ativar máquinas de Windows físicas/virtuais

Pode ativar os servidores windows que executam no local ou em outros ambientes de nuvem (incluindo instâncias AWS EC2) para a Configuração do Estado da Automação Azure. Os servidores devem ter [acesso de saída ao Azure](automation-dsc-overview.md#network-planning).

1. Certifique-se de que a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) está instalada nas máquinas para ativar a Configuração do Estado. Além disso, o WMF 5 deve ser instalado no computador que está a utilizar para ativar as máquinas.
1. Siga as instruções nas [configurações do DSC para](#generate-dsc-metaconfigurations) criar uma pasta contendo as configurações dSC necessárias. 
1. Utilize o seguinte cmdlet para aplicar as configurações do DSC PowerShell remotamente nas máquinas para ativar. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Se não conseguir aplicar as configurações do DSC PowerShell remotamente, copie a pasta de **configurações** para as máquinas que está a ativar. Em seguida, adicione código para ligar [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) localmente nas máquinas.
1. Utilizando o portal Azure ou cmdlets, verifique se as máquinas aparecem como nós de Configuração Do Estado registados na sua conta De automação Azure.

## <a name="enable-physicalvirtual-linux-machines"></a>Ativar máquinas linux físicas/virtuais

Pode ativar servidores Linux que executem no local ou em outros ambientes de nuvem para configuração do Estado. Os servidores devem ter [acesso de saída ao Azure](automation-dsc-overview.md#network-planning).

1. Certifique-se de que a versão mais recente da [Configuração do Estado PowerShell Desired para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) está instalada nas máquinas para ativar a Configuração do Estado.
2. Se o [PowerShell DSC Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig4) corresponder ao seu caso de utilização, e pretender ativar as máquinas de modo a que ambos se retirem e reportem à Configuração do Estado:

   - Em cada máquina Linux para ativar, utilize `Register.py` para ativar a máquina com as predefinições do PowerShell DSC Local Configuration Manager.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Para encontrar a chave de registo e url de registo da sua conta Automation, consulte [as máquinas Ativar em segurança utilizando](#enable-machines-securely-using-registration)o registo .

3. Se as predefinições do PowerShell DSC Local Configuration Manager (LCM) não corresponderem à sua caixa de utilização, ou se pretender ativar máquinas que apenas reportam à Configuração do Estado da Automação Azure, siga os passos 4-7. Caso contrário, proceda diretamente ao passo 7.

4. Siga as instruções na secção [de configurações DeD Geração DSC](#generate-dsc-metaconfigurations) para produzir uma pasta contendo as configurações dSC necessárias.

5. Certifique-se de que a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) está instalada no computador que está a ser utilizado para ativar as suas máquinas para configuração do Estado.

6. Adicione o código da seguinte forma para aplicar as configurações do DSC PowerShell remotamente às máquinas para ativar.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Se não conseguir aplicar as configurações do DSC PowerShell remotamente, copie as configurações correspondentes às máquinas remotas da pasta descrita no passo 4 para as máquinas Linux.

8. Adicione código para ligar `Set-DscLocalConfigurationManager.py` localmente em cada máquina Linux para ativar a Configuração do Estado.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Utilizando o portal Azure ou cmdlets, certifique-se de que as máquinas para ativar apareçam agora como nós DSC registados na sua conta De suposição De automação.

## <a name="generate-dsc-metaconfigurations"></a>Gerar configurações dSC

Para ativar qualquer máquina para configuração do Estado, pode gerar uma [configuração DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Esta configuração diz ao agente DSC para puxar de e/ou reportar à Configuração do Estado da Automação Azure. Pode gerar uma configuração DSC para configuração do Estado da Automatização Azure utilizando uma configuração PowerShell DSC ou os cmdlets De PowerShell de Automação Azure.

> [!NOTE]
> As configurações do DSC contêm os segredos necessários para permitir uma máquina numa conta de Automação para gestão. Certifique-se de que protege corretamente quaisquer configurações DSC que crie ou elimine-as após a utilização.

O suporte proxy para configurações é controlado pelo Gestor de [Configuração Local](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7), que é o motor DSC do Windows PowerShell. O LCM funciona em todos os nós alvo e é responsável por chamar os recursos de configuração que estão incluídos num script de configuração DSC. Pode incluir suporte por procuração numa configuração, incluindo definições de `ProxyURL` e propriedades conforme necessário nos `ProxyCredential` `ConfigurationRepositoryWeb` blocos e `ResourceRepositoryWeb` `ReportServerWeb` blocos. Um exemplo da definição de URL é `ProxyURL = "http://172.16.3.6:3128";` . A `ProxyCredential` propriedade está definida para um `PSCredential` objeto, como descrito nas [credenciais De Gestão na Automação Azure.](shared-resources/credentials.md) 

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>Gerar configurações DSC utilizando uma configuração DSC

1. Abra o VSCode (ou o seu editor favorito) como administrador numa máquina no seu ambiente local. A máquina deve ter a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) instalada.
1. Copie o seguinte guião localmente. Este script contém uma configuração DeSC PowerShell para criar configurações, e um comando para iniciar a criação de configuração.

    > [!NOTE]
    > Configuração do Estado Nomes de configuração do nó são sensíveis a casos no portal Azure. Se o caso não estiver em desajuste, o nó não aparecerá sob a conta dos **nós.**

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

1. Preencha a chave de registo e o URL para a sua conta Deautomatização, bem como os nomes das máquinas para ativar. Todos os outros parâmetros são opcionais. Para encontrar a chave de registo e url de registo da sua conta Automation, consulte [as máquinas Ativar em segurança utilizando](#enable-machines-securely-using-registration)o registo .

1. Se pretender que as máquinas reportem informações de estado dSC à Configuração do Estado da Automação Azure, mas não puxe a configuração ou os módulos PowerShell, defina o `ReportOnly` parâmetro como verdadeiro.

1. Se `ReportOnly` não estiver definido, as máquinas reportam informações de estado dSC à Configuração do Estado da Automação Azure e à configuração de puxar ou módulos PowerShell. Coloque os parâmetros em conformidade nos `ConfigurationRepositoryWeb` `ResourceRepositoryWeb` blocos e `ReportServerWeb` blocos.

1. Execute o script. Deve agora ter uma pasta de diretório de trabalho chamada **DscMetaConfigs,** contendo as configurações do DSC PowerShell para as máquinas ativarem (como administrador).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Gerar configurações DSC usando cmdlets de automação Azure

Se as predefinições do PowerShell DSC LCM corresponderem à sua caixa de utilização e pretender que as máquinas puxem e reportem para a Configuração do Estado da Automação Azure, pode gerar as configurações dSC necessárias mais simplesmente utilizando os cmdlets de Automação Azure.

1. Abra a consola PowerShell ou VSCode como administrador numa máquina no seu ambiente local.
2. Ligue-se ao Gestor de Recursos Azure utilizando [o Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
3. Descarregue as configurações do PowerShell DSC para as máquinas que pretende ativar a partir da conta Automation na qual está a configurar os nódosos.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Deve agora ter uma pasta **DscMetaConfigs** contendo as configurações do DSC PowerShell para as máquinas ativarem (como administrador).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>Ativar as máquinas com segurança utilizando o registo

Pode ativar as máquinas de forma segura para uma conta De Automação Azure através do protocolo de registo WMF 5 DSC. Este protocolo permite que um nó DSC autentique a um servidor de puxar ou reportar PowerShell DSC, incluindo a Configuração do Estado da Automação Azure. O nó regista-se com o servidor no URL de registo e autentica utilizando uma chave de registo. Durante o registo, o nó DSC e o servidor de pull/report DSC negoceiam um certificado único para o nó utilizar para autenticação no registo pós-registo do servidor. Este processo impede que os nós habilitados se personiquem uns aos outros, por exemplo, se um nó estiver comprometido e se comportar maliciosamente. Após o registo, a chave de registo não é novamente utilizada para autenticação e é eliminada do nó.

Pode obter as informações necessárias para o protocolo de registo de Configuração do Estado a partir de **Chaves** ao abrigo das **Definições de Conta** no portal Azure. 

![Chaves de automação azure e URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- URL de registo é o campo URL na página Keys.
- A chave de registo é o valor do **campo-chave** de acesso primário ou o **campo-chave** de acesso secundário na página Keys. Qualquer uma das teclas pode ser usada.

Para maior segurança, pode regenerar as chaves de acesso primária e secundária de uma conta De automação a qualquer momento na página Keys. A regeneração-chave impede que futuras inscrições nonóufodos utilizem chaves anteriores.

## <a name="re-register-a-node"></a>Re-registe um nó

Depois de registar uma máquina como nó DSC na Configuração do Estado da Automação Azure, existem várias razões pelas quais poderá ser necessário reregistar esse nó no futuro.

- **Renovação de certificado.** Para versões do Windows Server antes do Windows Server 2019, cada nó negoceia automaticamente um certificado único para autenticação que expira após um ano. Se um certificado expirar sem renovação, o nó não pode comunicar com a Automação Azure e está marcado `Unresponsive` . Atualmente, o protocolo de registo DoC PowerShell não pode renovar automaticamente os certificados quando estão perto da expiração, e deve voltar a registar os nós após um ano. Antes de voltar a registar-se, certifique-se de que cada nó está a funcionar WMF 5 RTM. 

    A reinscrição realizada 90 dias ou menos a partir do prazo de validade do certificado, ou em qualquer ponto após o prazo de validade do certificado, resulta na geração e uso de um novo certificado. Uma resolução para este problema está incluída no Windows Server 2019 e posteriormente.

- **Alterações nos valores dSC LCM.** Pode ser necessário alterar os [valores do PowerShell DSC LCM](/powershell/scripting/dsc/managing-nodes/metaConfig4) definidos durante o registo inicial do nó, por exemplo, `ConfigurationMode` . Atualmente, só pode alterar estes valores de agente DSC através de reregisto. A única exceção é o valor de configuração do nó atribuído ao nó. Pode alterar isto diretamente no Azure Automation DSC.

Pode voltar a registar um nó assim que registou o nó inicialmente, utilizando qualquer um dos métodos descritos neste documento. Não é necessário desregistar um nó da Configuração do Estado da Automação Azure antes de o reregistar.

## <a name="check-status-of-vm-setup"></a>Verifique o estado da configuração da VM

A Configuração do Estado permite-lhe facilmente ativar os VMs do Windows Azure para a gestão da configuração. Sob o capot, a extensão de configuração de configuração do Estado Desejada Azure VM é utilizada para registar o VM com configuração do Estado de Automação Azure. Uma vez que a extensão de Configuração estatal desejada azure VM corre assincronicamente, acompanhar o seu progresso e resolver a sua execução pode ser importante.

> [!NOTE]
> Qualquer método que permita aos VMs do Windows Azure para configuração estatal que utilize a extensão de configuração de configuração do Estado Desejada Azure VM pode demorar até uma hora para a Automação Azure mostrar VMs como registados. Este atraso deve-se à instalação de WMF 5 no VM pela extensão de configuração de configuração de estado desejada Azure VM, que é necessária para ativar VMs para configuração do Estado.

Para ver o estado da extensão de configuração de configuração do Estado Desejada Do Estado Do Azure VM:

1. No portal Azure, navegue até ao VM que está a ser ativado.
2. Clique em **Extensões** em **Definições**. 
3. Agora selecione **DSC** ou **DSCForLinux,** dependendo do seu sistema operativo. 
4. Para mais detalhes, pode clicar em **Ver estado detalhado**.

## <a name="next-steps"></a>Passos seguintes

- Para começar, veja [Get started com Azure Automation State Configuration](automation-dsc-getting-started.md).
- Para aprender sobre a compilação de configurações de DSC para que possa atribuí-las a nós-alvo, consulte [configurações de DSC compile na Configuração do Estado da Automação Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Para obter informações sobre preços, consulte os preços de configuração do Estado da [Automatização do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para um exemplo de utilização da Configuração do Estado da Automação Azure num gasoduto de implantação contínua, consulte A [implantação contínua com chocolatey](automation-dsc-cd-chocolatey.md).
- Para obter informações sobre resolução de problemas, consulte [Troubleshoot Azure Automation State Configuration](./troubleshoot/desired-state-configuration.md).