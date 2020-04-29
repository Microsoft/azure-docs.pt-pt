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
ms.openlocfilehash: d4e008a0bd43f10b01d78a1c388f1ca6fee983ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457744"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Máquinas de embarque para gestão pela Configuração do Estado da Automação Azure

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Porquê gerir máquinas com configuração do Estado da Automação Azure?

A Configuração do Estado da Automação Azure é um serviço de gestão de configuração para nós de configuração do Estado Desejado (DSC) em qualquer nuvem ou centro de dados no local. É acedido no portal Azure selecionando a **configuração do Estado (DSC)** em gestão de **configuração**. 

Este serviço permite a escalabilidade através de milhares de máquinas de forma rápida e fácil a partir de uma localização central e segura. Pode facilmente a bordo de máquinas, atribuindo-lhes configurações declarativas e ver relatórios que mostram a conformidade de cada máquina com o estado pretendido que especifica.

O serviço de Configuração do Estado da Automação Azure é para a DSC quais os livros de execução da Automação Azure para a escrita PowerShell. Por outras palavras, da mesma forma que a Automatização Azure ajuda a gerir os scripts PowerShell, também o ajuda a gerir as configurações do DSC. Para saber mais sobre os benefícios da utilização da Configuração do Estado da Automação Azure, consulte a visão geral da Configuração do Estado da [Automação Azure](automation-dsc-overview.md).

A Configuração do Estado da Automação Azure pode ser usada para gerir uma variedade de máquinas:

- Máquinas virtuais do Azure
- Máquinas virtuais azure (clássica)
- Máquinas de Janelas físicas/virtuais no local, ou numa nuvem diferente de Azure (incluindo instâncias AWS EC2)
- Máquinas linux físicas/virtuais no local, em Azure, ou numa nuvem que não o Azure

Se não estiver pronto para gerir a configuração da máquina a partir da nuvem, pode utilizar a Configuração do Estado da Automatização Azure como ponto final apenas de relatório. Esta funcionalidade permite-lhe definir configurações (push) através do DSC e visualizar detalhes de reporte na Automação Azure.

> [!NOTE]
> Gerir VMs Azure com Configuração do Estado de Automação Azure não está incluído a custo extra se a versão instalada de extensão de configuração do Estado Do Estado Do Azure VM desired for superior a 2.70. Para mais informações, consulte [**a página de preços da Automação**](https://azure.microsoft.com/pricing/details/automation/).

As seguintes secções deste artigo descrevem como pode embarcar as máquinas listadas acima para a Configuração do Estado da Automação Azure.

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

## <a name="onboarding-azure-vms"></a>Onboarding Azure VMs

A Configuração do Estado da Automação Azure permite-lhe facilmente embarcar em VMs Azure para gestão de configuração, utilizando o portal Azure, modelos do Gestor de Recursos Azure ou PowerShell. Sob o capot, e sem que um administrador tenha de se transformar num VM, a extensão de configuração de configuração de configuração de estado desejada Azure VM regista o VM com configuração do Estado da Automação Azure. Uma vez que a extensão Azure funciona assincronicamente, as medidas para acompanhar o seu progresso ou resolução de problemas são fornecidas na secção de embarque da [máquina virtual Troubleshooting Azure](#troubleshooting-azure-virtual-machine-onboarding) deste artigo.

> [!NOTE]
>A implantação de DSC para um nó Linux utiliza a pasta **/tmp.** Módulos como `nxautomation` são temporariamente descarregados para verificação antes de os instalarem nos locais apropriados. Para garantir que os módulos se instalam corretamente, o agente Log Analytics para o Linux necessita de permissões de leitura/escrita na pasta **/tmp.**<br><br>
>O agente Log Analytics para `omsagent` o Linux funciona como utilizador. Para conceder >autorizar `omsagent` o utilizador, `setfacl -m u:omsagent:rwx /tmp`execute o comando .

### <a name="onboard-a-vm-using-azure-portal"></a>A bordo de um VM usando o portal Azure

Para embarcar uma Configuração do Estado Azure VM para a Automação Azure através do [portal Azure:](https://portal.azure.com/)

1. Navegue para a conta De automação Azure na qual se podem embarcar VMs. 

2. Na página de Configuração do Estado, selecione o separador **Nós** e, em seguida, clique em **Adicionar**.

3. Escolha um VM a bordo.

4. Se a máquina não tiver a extensão de estado desejada pela PowerShell instalada e o estado de alimentação estiver em funcionamento, clique em **Connect**.

5. Em **'Registo',** introduza os valores do [PowerShell DSC Local Configuration Manager necessários](/powershell/scripting/dsc/managing-nodes/metaConfig) para a sua caixa de utilização. Opcionalmente, pode introduzir uma configuração de nó para atribuir ao VM.

![embarque](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-a-vm-using-azure-resource-manager-templates"></a>A bordo de um VM usando modelos de Gestor de Recursos Azure

Pode implantar e embarcar um VM para a Configuração do Estado da Automação Azure utilizando modelos de Gestor de Recursos Azure. Consulte o [Servidor gerido pelo serviço de Configuração do Estado Desejado](https://azure.microsoft.com/resources/templates/101-automation-configuration/) para um modelo de exemplo que a bordo de um VM existente para configuração do Estado de Automação Azure. Se estiver a gerir um conjunto de escala de máquina virtual, consulte o modelo de exemplo na configuração de conjunto de escala de [máquina virtual gerida pela Automação Azure](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="onboard-machines-using-powershell"></a>Máquinas de bordo usando PowerShell

Pode utilizar o [cmdlet Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) na PowerShell para a bordo de VMs para a Configuração do Estado da Automação Azure. 

> [!NOTE]
>O `Register-AzAutomationDscNode` cmdlet é implementado atualmente apenas para máquinas que executam o Windows, uma vez que dispara apenas a extensão do Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Registe Os VMs através das assinaturas Do Azure

A melhor maneira de registar VMs de outras subscrições do Azure é usar a extensão DSC num modelo de implementação do Gestor de Recursos Azure. Exemplos são fornecidos na [extensão de Configuração do Estado Desejada com modelos de Gestor de Recursos Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).

Para encontrar a chave de registo e o URL de registo para utilizar como parâmetros no modelo, consulte o [Embarque utilizando de forma segura a](#onboarding-securely-using-registration) secção de registo neste artigo.

## <a name="onboarding-physicalvirtual-windows-machines"></a>No embarque de máquinas de janelas físicas/virtuais

Pode embarcar servidores do Windows que executam no local ou em outros ambientes de nuvem (incluindo instâncias AWS EC2) para a Configuração do Estado da Automação Azure. Os servidores devem ter [acesso de saída ao Azure](automation-dsc-overview.md#network-planning).

1. Certifique-se de que a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) está instalada nas máquinas para embarcar na Configuração do Estado da Automação Azure. Além disso, o WMF 5 deve ser instalado no computador que está a utilizar para a operação de embarque.
1. Siga as instruções na secção [Generatando configurações dSC](#generating-dsc-metaconfigurations) para criar uma pasta contendo as configurações dSC necessárias. 
1. Utilize o seguinte cmdlet para aplicar as configurações do DSC PowerShell remotamente nas máquinas que pretende utilizar a bordo. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Se não conseguir aplicar as configurações do DSC PowerShell remotamente, copie a pasta de **configurações** para as máquinas que está a embarcar. Em seguida, adicione código para ligar [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) localmente nas máquinas.
1. Utilizando o portal Azure ou cmdlets, verifique se as máquinas a bordo aparecem como nós de Configuração Do Estado registados na sua conta De sueste Automatização.

## <a name="onboarding-physicalvirtual-linux-machines"></a>Máquinas linux físicas/virtuais de embarque

Pode embarcar em servidores Linux que executam no local ou em outros ambientes em nuvem para a Configuração do Estado da Automação Azure. Os servidores devem ter [acesso de saída ao Azure](automation-dsc-overview.md#network-planning).

1. Certifique-se de que a mais recente versão da [Configuração do Estado PowerShell Desired para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) está instalada nas máquinas para embarcar na Configuração do Estado da Automação Azure.
2. Se o [PowerShell DSC Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig4) corresponder ao seu caso de utilização, e quiser a bordo das máquinas para que ambos se retirem e reportem à Configuração do Estado da Automação Do Azure:

   - Em cada máquina Linux para embarcar para a `Register.py` Configuração do Estado da Automatização Azure, utilize para bordo utilizando as predefinições do PowerShell DSC Local Configuration Manager.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Para encontrar a chave de registo e URL de registo da sua conta De automação, consulte o [Embarque utilizando de forma segura a](#onboarding-securely-using-registration) secção de registo deste artigo.

3. Se as predefinições do PowerShell DSC Local Configuration Manager (LCM) não corresponderem à sua caixa de utilização, ou se pretender abordo de máquinas que apenas reportam à Configuração do Estado da Automação Azure, siga os passos 4-7. Caso contrário, proceda diretamente ao passo 7.

4. Siga as instruções na secção de [configurações DSC geradora](#generating-dsc-metaconfigurations) para produzir uma pasta contendo as configurações dSC necessárias.

5. Certifique-se de que a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) está instalada na máquina que está a ser utilizada para o embarque.

6. Adicione o código da seguinte forma para aplicar as configurações do DSC PowerShell remotamente às máquinas que pretende a bordo.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Se não conseguir aplicar as configurações do DSC PowerShell remotamente, copie as configurações correspondentes às máquinas remotas da pasta descrita no passo 4 para as máquinas Linux.

8. Adicione código `Set-DscLocalConfigurationManager.py` para ligar localmente em cada máquina Linux para embarcar na Configuração do Estado da Automação Azure.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Utilizando o portal Azure ou cmdlets, certifique-se de que as máquinas a bordo aparecem agora como nós DSC registados na sua conta Azure Automation.

## <a name="generating-dsc-metaconfigurations"></a>Gerando configurações dSC

Para embarcar em qualquer máquina para a Configuração do Estado da Automação Azure, pode gerar uma [configuração DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Esta configuração diz ao agente DSC para puxar de e/ou reportar à Configuração do Estado da Automação Azure. Pode gerar uma configuração DSC para configuração do Estado da Automatização Azure utilizando uma configuração PowerShell DSC ou os cmdlets De PowerShell de Automação Azure.

> [!NOTE]
> As configurações do DSC contêm os segredos necessários para embarcar numa máquina para uma conta de Automação para gestão. Certifique-se de que protege corretamente quaisquer configurações DSC que crie ou elimine-as após a utilização.

O suporte proxy para configurações é controlado por LCM, que é o motor DSC Windows PowerShell. O LCM funciona em todos os nós alvo e é responsável por chamar os recursos de configuração que estão incluídos num script de configuração DSC. Pode incluir suporte proxy numa configuração, incluindo definições do URL proxy e `ConfigurationRepositoryWeb` `ResourceRepositoryWeb`da `ReportServerWeb` credencial de procuração, conforme necessário nos blocos e blocos. Consulte [a configuração do Gestor de Configuração Local](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

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

1. Preencha a chave de registo e o URL para a sua conta Automation, bem como os nomes das máquinas a bordo. Todos os outros parâmetros são opcionais. Para encontrar a chave de registo e url de registo da sua conta Automation, consulte o Embarque utilizando de forma segura a secção de [registo.](#onboarding-securely-using-registration)

1. Se pretender que as máquinas reportem informações de estado dSC à Configuração do `ReportOnly` Estado da Automação Azure, mas não puxe a configuração ou os módulos PowerShell, defina o parâmetro como verdadeiro.

1. Se `ReportOnly` não estiver definido, as máquinas reportam informações de estado dSC à Configuração do Estado da Automação Azure e à configuração de puxar ou módulos PowerShell. Coloque os parâmetros `ConfigurationRepositoryWeb`em `ResourceRepositoryWeb`conformidade `ReportServerWeb` nos blocos e blocos.

1. Execute o script. Deverá agora ter uma pasta de diretório de trabalho chamada **DscMetaConfigs,** contendo as configurações do DSC PowerShell para as máquinas a bordo (como administrador).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Gerar configurações DSC usando cmdlets de automação Azure

Se as predefinições do PowerShell DSC LCM corresponderem à sua caixa de utilização e pretender que as máquinas de bordo possam puxar e reportar à Configuração do Estado da Automação Azure, pode gerar as configurações dSC necessárias mais simplesmente utilizando os cmdlets de Automação Azure.

1. Abra a consola PowerShell ou VSCode como administrador numa máquina no seu ambiente local.
2. Ligue-se ao Gestor de Recursos Azure utilizando [o Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
3. Descarregue as configurações do PowerShell DSC para as máquinas que pretende embarcar a partir da conta Automation na qual está a configurar os nódosos.

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

1. Deverá agora ter uma pasta **DscMetaConfigs** contendo as configurações do DSC PowerShell para as máquinas a bordo (como administrador).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboarding-securely-using-registration"></a>Embarque de forma segura usando o registo

As máquinas podem embarcar de forma segura a bordo de uma conta De Automação Azure através do protocolo de registo WMF 5 DSC. Este protocolo permite que um nó DSC autentique a um servidor de puxar ou reportar PowerShell DSC, incluindo a Configuração do Estado da Automação Azure. O nó regista-se com o servidor no URL de registo e autentica utilizando uma chave de registo. Durante o registo, o nó DSC e o servidor de pull/report DSC negoceiam um certificado único para o nó utilizar para autenticação no registo pós-registo do servidor. Este processo impede que nós a bordo se personiquem uns aos outros, por exemplo, se um nó for comprometido e se comportar maliciosamente. Após o registo, a chave de registo não é novamente utilizada para autenticação e é eliminada do nó.

Pode obter as informações necessárias para o protocolo de registo de Configuração do Estado a partir de **Chaves** ao abrigo das **Definições de Conta** no portal Azure. 

![Chaves de automação azure e URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- URL de registo é o campo URL na página Keys.
- A chave de registo é o valor do **campo-chave** de acesso primário ou o **campo-chave** de acesso secundário na página Keys. Qualquer uma das teclas pode ser usada.

Para maior segurança, pode regenerar as chaves de acesso primária e secundária de uma conta De automação a qualquer momento na página Keys. A regeneração-chave impede que futuras inscrições nonóufodos utilizem chaves anteriores.

## <a name="re-registering-a-node"></a>Re-registar um nó

Depois de registar uma máquina como nó DSC na Configuração do Estado da Automação Azure, existem várias razões pelas quais poderá ser necessário reregistar esse nó no futuro.

- **Renovação de certificado.** Para versões do Windows Server antes do Windows Server 2019, cada nó negoceia automaticamente um certificado único para autenticação que expira após um ano. Se um certificado expirar sem renovação, o nó não pode `Unresponsive`comunicar com a Automação Azure e está marcado . Atualmente, o protocolo de registo DoC PowerShell não pode renovar automaticamente os certificados quando estão perto da expiração, e deve voltar a registar os nós após um ano. Antes de voltar a registar-se, certifique-se de que cada nó está a funcionar WMF 5 RTM. 

    A reinscrição realizada 90 dias ou menos a partir do prazo de validade do certificado, ou em qualquer ponto após o prazo de validade do certificado, resulta na geração e uso de um novo certificado. Uma resolução para este problema está incluída no Windows Server 2019 e posteriormente.

- **Alterações nos valores dSC LCM.** Pode ser necessário alterar os [valores do PowerShell DSC LCM](/powershell/scripting/dsc/managing-nodes/metaConfig4) `ConfigurationMode`definidos durante o registo inicial do nó, por exemplo, . Atualmente, só pode alterar estes valores de agente DSC através de reregisto. A única exceção é o valor de configuração do nó atribuído ao nó. Pode alterar isto diretamente no Azure Automation DSC.

Pode voltar a registar um nó assim que registou o nó inicialmente, utilizando qualquer um dos métodos de embarque descritos neste documento. Não é necessário desregistar um nó da Configuração do Estado da Automação Azure antes de o reregistar.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Resolução de problemas máquina virtual Azure no embarque

A Configuração do Estado da Automação Azure permite-lhe facilmente embarcar em VMs do Windows Azure para gestão de configuração. Sob o capot, a extensão de configuração de configuração do Estado Desejada Azure VM é utilizada para registar o VM com configuração do Estado de Automação Azure. Uma vez que a extensão de Configuração estatal desejada azure VM corre assincronicamente, acompanhar o seu progresso e resolver a sua execução pode ser importante.

> [!NOTE]
> Qualquer método de embarcar num Azure Windows VM para a Configuração do Estado de Automação Azure que utilize a extensão de Configuração do Estado Desejada Azure VM pode demorar até uma hora para a Automação Azure mostrar como registada. Este atraso deve-se à instalação de WMF 5 no VM pela extensão de configuração de configuração de configuração do Estado Desejada Azure VM, que é necessária para embarcar no VM para a Configuração do Estado da Automação Azure.

Para resolver problemas ou visualizar o estado da extensão de configuração de configuração do Estado Desejada Do Estado Azure VM:

1. No portal Azure, navegue até ao VM a bordo.
2. Clique em **Extensões** em **Definições**. 
3. Agora selecione **DSC** ou **DSCForLinux,** dependendo do seu sistema operativo. 
4. Para mais detalhes, pode clicar em **Ver estado detalhado**.

Para obter mais informações sobre resolução de problemas, consulte problemas de resolução de problemas com a [Configuração do Estado Desejada pela Automatização Azure (DSC)](./troubleshoot/desired-state-configuration.md).

## <a name="next-steps"></a>Passos seguintes

- Para começar, veja O Início com a Configuração do Estado da [Automação Azure](automation-dsc-getting-started.md).
- Para aprender sobre a compilação de configurações de DSC para que possa atribuí-las a nós-alvo, consulte [configurações de compilação na Configuração do Estado da Automação Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Para obter informações sobre preços, consulte os preços de configuração do Estado da [Automatização do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para um exemplo de utilização da Configuração do Estado da Automação Azure num gasoduto de implantação contínua, consulte o Exemplo de [Utilização: Implantação contínua em máquinas virtuais utilizando a configuração do Estado da Automação Azure e](automation-dsc-cd-chocolatey.md)o Chocolatey .
