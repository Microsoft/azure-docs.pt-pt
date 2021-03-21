---
title: Ativar a configuração do estado da automação Azure
description: Este artigo diz como configurar máquinas para gestão com a Azure Automation State Configuration.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: c0dc68bd7dacf0cd7f4be9732d45831e2dbb712c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897008"
---
# <a name="enable-azure-automation-state-configuration"></a>Ativar a configuração do estado da automação Azure

Este tópico descreve como pode configurar as suas máquinas para gestão com a Configuração do Estado da Automação Azure. Para mais detalhes sobre este serviço, consulte [a visão geral da Configuração do Estado da Automação Azure](automation-dsc-overview.md).

## <a name="enable-azure-vms"></a>Ativar VMs Azure

A Azure Automation State Configuration permite-lhe facilmente ativar VMs Azure para gestão de configuração, utilizando o portal Azure, os modelos de Gestor de Recursos Azure ou PowerShell. Sob o capot, e sem que um administrador tenha de se remotor para um VM, a extensão de configuração estatal desejada Azure VM regista o VM com a configuração do Estado da Automação Azure. Uma vez que a extensão Azure é executada de forma assíncrona, são fornecidas diligências para acompanhar o seu progresso no [estado de verificação da configuração de VM](#check-status-of-vm-setup).

> [!NOTE]
>A colocação de DSC num nó Linux utiliza a pasta **/tmp.** Módulos como `nxautomation` são temporariamente descarregados para verificação antes de os instalarem nos locais apropriados. Para garantir que os módulos se instalam corretamente, o agente Log Analytics do Linux necessita de permissões de leitura/escrita na pasta **/tmp.**<br><br>
>O agente Log Analytics para Linux funciona como `omsagent` utilizador. Para conceder>escreva permissão ao `omsagent` utilizador, executar o comando `setfacl -m u:omsagent:rwx /tmp` .

### <a name="enable-a-vm-using-azure-portal"></a>Ativar um VM utilizando o portal Azure

Para ativar uma Configuração Azure VM para o Estado através do [portal Azure:](https://portal.azure.com/)

1. Navegue para a conta Azure Automation na qual ativar VMs. 

2. Na página Configuração do Estado, selecione o **separador Nodes** e, em seguida, clique em **Adicionar**.

3. Escolha um VM para ativar.

4. Se a máquina não tiver a extensão de estado desejada do PowerShell instalada e o estado de alimentação estiver em funcionamento, clique em **Connect**.

5. Em **Registo**, insira os [valores do Gestor de Configuração Local PowerShell DSC necessários](/powershell/scripting/dsc/managing-nodes/metaConfig) para o seu caso de utilização. Opcionalmente, pode introduzir uma configuração de nó para atribuir ao VM.

![permitindo vM](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>Ativar um VM utilizando modelos de Gestor de Recursos Azure

Pode instalar e ativar um VM para configuração estatal utilizando modelos do Gestor de Recursos Azure. Consulte [o Servidor gerido pelo serviço de configuração do estado desejado](https://azure.microsoft.com/resources/templates/101-automation-configuration/) para um modelo de exemplo que permite um VM existente para configuração do estado. Se estiver a gerir um conjunto de escala de máquina virtual, consulte o modelo de exemplo na [configuração de conjunto de escala de máquina virtual gerido pela Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="enable-machines-using-powershell"></a>Ativar máquinas usando PowerShell

Pode utilizar o [cmdlet Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) em PowerShell para ativar VMs para configuração do estado. 

> [!NOTE]
>O `Register-AzAutomationDscNode` cmdlet é implementado atualmente apenas para máquinas que executam o Windows, uma vez que aciona apenas a extensão do Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Registar VMs em subscrições Azure

A melhor maneira de registar VMs de outras subscrições do Azure é utilizar a extensão DSC num modelo de implementação do Azure Resource Manager. Exemplos são fornecidos na [extensão de configuração do estado desejado com modelos de Gestor de Recursos Azure](../virtual-machines/extensions/dsc-template.md).

Para encontrar a chave de registo e o URL de registo para utilizar como parâmetros no modelo, consulte [Ativar as máquinas com segurança utilizando](#enable-machines-securely-using-registration)o registo .

## <a name="enable-physicalvirtual-windows-machines"></a>Ativar máquinas físicas/virtuais do Windows

Pode ativar os servidores do Windows que executam no local ou em outros ambientes em nuvem (incluindo instâncias AWS EC2) para a Azure Automation State Configuration. Os servidores devem ter [acesso de saída ao Azure](automation-dsc-overview.md#network-planning).

1. Certifique-se de que a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) está instalada nas máquinas para ativar a Configuração do Estado. Além disso, o WMF 5 deve ser instalado no computador que está a utilizar para ativar as máquinas.
1. Siga as instruções em [Metaconfigurations DSC](#generate-dsc-metaconfigurations) para criar uma pasta que contenha as metaconfigurações DSC necessárias. 
1. Utilize o cmdlet seguinte para aplicar as metaconfigurações PowerShell DSC remotamente às máquinas para ativar. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Se não conseguir aplicar as metaconfiguções do PowerShell DSC remotamente, copie a pasta **de metaconfigurations** para as máquinas que está a ativar. Em seguida, adicione código para ligar para [Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) localmente nas máquinas.
1. Utilizando o portal Azure ou cmdlets, verifique se as máquinas aparecem como nós de Configuração estatal registados na sua conta Azure Automation.

## <a name="enable-physicalvirtual-linux-machines"></a>Ativar máquinas Linux físicas/virtuais

Pode ativar servidores Linux que executam no local ou em outros ambientes em nuvem para configuração do estado. Os servidores devem ter [acesso de saída ao Azure](automation-dsc-overview.md#network-planning).

1. Certifique-se de que a versão mais recente da [Configuração de Estado Desejada por PowerShell para o Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) está instalada nas máquinas para ativar a configuração do Estado.
2. Se o [Gestor de Configuração Local PowerShell DSC coincidir](/powershell/scripting/dsc/managing-nodes/metaConfig4) com a sua caixa de utilização, e pretende ativar as máquinas de modo a que ambas se retirem e reportem à Configuração do Estado:

   - Em cada máquina Linux para ativar, utilize `Register.py` para ativar a máquina com o Predefinido Gestor de Configuração Local PowerShell DSC.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Para encontrar a chave de registo e URL de registo para a sua conta Dem automação, consulte [Ativar as máquinas com segurança utilizando o registo.](#enable-machines-securely-using-registration)

3. Se os predefinições do PowerShell DSC Local Configuration Manager (LCM) não corresponderem à sua caixa de utilização, ou se pretender ativar máquinas que apenas reportam à Configuração do Estado da Automação Azure, siga os passos 4-7. Caso contrário, proceda diretamente ao passo 7.

4. Siga as instruções na secção [de metaconfiguções DSC](#generate-dsc-metaconfigurations) para produzir uma pasta que contenha as metaconfigurações DSC necessárias.

5. Certifique-se de que a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) está instalada no computador que está a ser utilizado para ativar as suas máquinas para configuração estatal.

6. Adicione o código da seguinte forma para aplicar as metaconfigurações DSC powerShell remotamente às máquinas para ativar.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Se não conseguir aplicar remotamente as metaconfiguções do PowerShell DSC, copie as metaconfiguções correspondentes às máquinas remotas da pasta descrita no passo 4 para as máquinas Linux.

8. Adicione código para ligar `Set-DscLocalConfigurationManager.py` localmente em cada máquina Linux para ativar a configuração do estado.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Utilizando o portal Azure ou cmdlets, certifique-se de que as máquinas que permitem agora aparecem como nós DSC registados na sua conta Azure Automation.

## <a name="generate-dsc-metaconfigurations"></a>Gerar metaconfigurações DSC

Para ativar qualquer máquina para configuração estatal, pode gerar uma [metaconfiguração DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Esta configuração diz ao agente DSC para puxar de e/ou reportar para a Configuração do Estado da Automação Azure. Pode gerar uma metaconfigura DSC para a configuração do Estado da Automação Azure utilizando uma configuração DSC powerShell ou os cmdlets PowerShell de automação Azure.

> [!NOTE]
> As metaconfiguções DSC contêm os segredos necessários para permitir uma máquina numa conta de Automação para gestão. Certifique-se de que protege corretamente quaisquer metaconfiguções DSC que criar ou elimine-as após a utilização.

O suporte proxy para metaconfigurations é controlado pelo [Gestor de Configuração Local](/powershell/scripting/dsc/managing-nodes/metaconfig), que é o motor DSC do Windows PowerShell. O LCM funciona em todos os nós-alvo e é responsável por chamar os recursos de configuração que estão incluídos num script de metaconfiguração DSC. Pode incluir suporte de procuração numa metaconfiguração, incluindo definições `ProxyURL` e `ProxyCredential` propriedades necessárias no , e `ConfigurationRepositoryWeb` `ResourceRepositoryWeb` `ReportServerWeb` blocos. Um exemplo da definição de URL é `ProxyURL = "http://172.16.3.6:3128";` . A `ProxyCredential` propriedade está configurada para um `PSCredential` objeto, conforme descrito em Manage [credenciais em Azure Automation.](shared-resources/credentials.md) 

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>Gerar metaconfiguções DSC usando uma configuração DSC

1. Abra o VSCode (ou o seu editor favorito) como administrador numa máquina no seu ambiente local. A máquina deve ter a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) instalada.
1. Copie o seguinte guião localmente. Este script contém uma configuração DSC PowerShell para criar metaconfigurations, e um comando para iniciar a criação de metaconfiguration.

    > [!NOTE]
    > Os nomes de configuração do nó de configuração do estado são sensíveis a casos no portal Azure. Se o caso for desajustado, o nó não aparecerá sob a conta **Nodes.**

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

1. Preencha a chave de registo e URL para a sua conta Dem automação, bem como os nomes das máquinas para ativar. Todos os outros parâmetros são opcionais. Para encontrar a chave de registo e URL de registo para a sua conta Dem automação, consulte [Ativar as máquinas com segurança utilizando o registo.](#enable-machines-securely-using-registration)

1. Se pretender que as máquinas reportem informações de estado DSC à Configuração do Estado da Automação Azure, mas não puxe a configuração ou os módulos PowerShell, dedije o `ReportOnly` parâmetro para o caso.

1. Se `ReportOnly` não estiverem definidas, as máquinas reportam informações de estado DSC à Configuração do Estado da Automação Azure e puxam a configuração ou os módulos PowerShell. Desa parte dos parâmetros em conformidade no `ConfigurationRepositoryWeb` `ResourceRepositoryWeb` , e `ReportServerWeb` blocos.

1. Execute o script. Deverá agora ter uma pasta de diretório de trabalho chamada **DscMetaConfigs,** contendo as metaconfiguções DSC powerShell para as máquinas permitirem (como administrador).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Gerar metaconfiguções DSC usando cmdlets de automação Azure

Se as predefinições do PowerShell DSC LCM coincidirem com a sua caixa de utilização e pretender que as máquinas puxem e reportem à Configuração do Estado da Automação Azure, pode gerar as metaconfiguções DSC necessárias mais simplesmente utilizando as cmdlets Azure Automation.

1. Abra a consola PowerShell ou VSCode como administrador numa máquina no ambiente local.
2. Ligue-se ao Gestor de Recursos Azure utilizando [o Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount).
3. Descarregue as metaconfigurações DSC powerShell para as máquinas que pretende ativar a partir da conta Automação em que está a configurar nós.

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

1. Deverá agora ter uma pasta **DscMetaConfigs** contendo as metaconfigurações DSC powerShell para as máquinas ativarem (como administrador).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>Ativar as máquinas de forma segura usando o registo

Pode ativar as máquinas de forma segura para uma conta Azure Automation através do protocolo de registo DSC WMF 5. Este protocolo permite que um nó DSC autente para um servidor de pull ou relatório DSC powerShell, incluindo a Configuração do Estado da Automação Azure. O nó regista-se com o servidor no URL de registo e autentica-se com uma chave de registo. Durante o registo, o nó DSC e o servidor de pull/report DSC negoceiam um certificado único para o nó a utilizar para autenticação no pós-registo do servidor. Este processo impede que os nós ativados se personiem uns aos outros, por exemplo, se um nó estiver comprometido e se comportar maliciosamente. Após o registo, a chave de registo não é utilizada novamente para autenticação e é eliminada do nó.

Pode obter as informações necessárias para o protocolo de registo de Configuração do Estado a partir de **Chaves** em **Definições** de Conta no portal Azure. 

![Chaves e URL de automação Azure](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- O URL de registo é o campo URL na página Chaves.
- A chave de registo é o valor do campo de **chave de acesso primário** ou do campo de chaves de acesso **secundário** na página Keys. Qualquer uma das chaves pode ser usada.

Para maior segurança, pode regenerar as chaves de acesso primário e secundário de uma conta Automation a qualquer momento na página Keys. A regeneração das chaves impede que os registos futuros dos nós utilizem chaves anteriores.

## <a name="re-register-a-node"></a>Re-registrar um nó

Depois de registar uma máquina como nó DSC na Configuração do Estado da Automação Azure, existem várias razões pelas quais poderá ter de voltar a registar esse nó no futuro.

- **Renovação do certificado.** Para versões do Windows Server antes do Windows Server 2019, cada nó negoceia automaticamente um certificado único de autenticação que expira após um ano. Se um certificado expirar sem renovação, o nó não pode comunicar com a Azure Automation e está marcado `Unresponsive` . Atualmente, o protocolo de registo DSC powerShell não pode renovar automaticamente os certificados quando estão perto de expirar, e deve voltar a registar os nós após um ano. Antes de voltar a registar-se, certifique-se de que cada nó está a executar WMF 5 RTM. 

    A reinspecção realizada 90 dias ou menos a partir do prazo de validade do certificado, ou em qualquer ponto após o termo do prazo de expiração do certificado, resulta na criação e utilização de um novo certificado. Uma resolução para este problema está incluída no Windows Server 2019 e posteriormente.

- **Alterações nos valores de DSC LCM.** Poderá ser necessário alterar os [valores de PowerShell DSC LCM definidos](/powershell/scripting/dsc/managing-nodes/metaConfig4) durante o registo inicial do nó, por exemplo, `ConfigurationMode` . Atualmente, só pode alterar estes valores de agente DSC através do re-registo. A única exceção é o valor de configuração do nó atribuído ao nó. Pode alterá-lo diretamente na Azure Automation DSC.

Pode voltar a registar um nó tal como registou inicialmente o nó, utilizando qualquer um dos métodos descritos neste documento. Não é necessário desagregar um nó da Configuração do Estado da Automação Azure antes de o voltar a registar.

## <a name="check-status-of-vm-setup"></a>Verificar o estado da configuração do VM

A Configuração do Estado permite-lhe ativar facilmente os VMs do Azure Windows para a gestão da configuração. Sob o capot, a extensão de configuração do estado desejado Azure VM é utilizada para registar o VM com a configuração do estado da automação Azure. Uma vez que a extensão de configuração do estado desejada Azure VM é executada de forma assíncrona, rastrear o seu progresso e resolver problemas na sua execução pode ser importante.

> [!NOTE]
> Qualquer método para permitir vMs Azure Windows para configuração estatal que utilize a extensão de configuração estatal Azure VM desejada pode demorar até uma hora para a Azure Automation mostrar VMs como registados. Este atraso deve-se à instalação de WMF 5 no VM pela extensão de Configuração do Estado Azure VM Desejada, que é necessária para permitir VMs para configuração do estado.

Para visualizar o estado da extensão de configuração do estado Azure VM desejada:

1. No portal Azure, navegue para o VM a ser ativado.
2. Clique em **Extensões** em **Definições**. 
3. Agora selecione **DSC** ou **DSCForLinux,** dependendo do seu sistema operativo. 
4. Para mais detalhes, clique em **Ver estado detalhado.**

## <a name="next-steps"></a>Passos seguintes

- Para começar, consulte [Começar com a Configuração do Estado da Automação Azure](automation-dsc-getting-started.md).
- Para saber sobre a compilação de configurações de DSC para que possa atribuí-las aos nós-alvo, consulte [as configurações do Compile DSC na Configuração do Estado da Automação Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](/powershell/module/az.automation).
- Para obter informações sobre preços, consulte [os preços de configuração do Estado da Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Para um exemplo de utilização da Configuração do Estado da Automatização Azure num gasoduto de implantação contínua, consulte [Configurar uma implementação contínua com chocolateria](automation-dsc-cd-chocolatey.md).
- Para obter informações sobre resolução de problemas, consulte a [configuração do Estado da Automação de Azure de Resolução de Problemas](./troubleshoot/desired-state-configuration.md).
