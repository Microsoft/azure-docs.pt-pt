---
title: Instale o agente da máquina conectada utilizando o DSC Windows PowerShell
description: Neste artigo, aprende-se a ligar máquinas ao Azure utilizando o Azure Arc para servidores (pré-visualização) utilizando o DSC do Windows PowerShell.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 1fb64463b0372202adb04c2deb304c389c7773b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164686"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>Como instalar o agente Máquina Conectada utilizando o DSC Do Windows PowerShell

Utilizando a [Configuração de Estado Desejada](https://docs.microsoft.com/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7) pelo Windows PowerShell (DSC), pode automatizar a instalação e configuração do software para um computador Windows. Este artigo descreve como usar o DSC para instalar o Azure Arc para servidores Connected Machine agente em máquinas híbridas Windows.

## <a name="requirements"></a>Requisitos

- Windows PowerShell versão 4.0 ou superior

- Módulo [DSC AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0)

- Um diretor de serviço para ligar as máquinas ao Azure Arc para servidores não interactivamente. Siga os passos sob a secção Criar um Diretor de [Serviço para embarque em escala](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) se ainda não tiver criado um diretor de serviço para a Arc para servidores.

## <a name="install-the-connectedmachine-dsc-module"></a>Instale o módulo DSC connectedMachine

1. Para instalar manualmente o módulo, descarregue o código fonte e `$env:ProgramFiles\WindowsPowerShell\Modules folder`desaperte o conteúdo do diretório do projeto para o . Ou executar o seguinte comando para instalar a partir da galeria PowerShell utilizando powerShellGet (em PowerShell 5.0):

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. Para confirmar a instalação, execute o seguinte comando e certifique-se de que vê os recursos DSC da Máquina Conectada Azure disponíveis.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   Na saída, deve ver algo semelhante ao seguinte:

   ![Confirmação do exemplo de instalação do módulo DSC da máquina conectada](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>Instale o agente e ligue-se ao Azure

Os recursos deste módulo foram concebidos para gerir a configuração do Agente Máquina Conectada Azure. Também está incluído um `AzureConnectedMachineAgent.ps1`script PowerShell, encontrado na `AzureConnectedMachineDsc\examples` pasta. Utiliza recursos comunitários para automatizar o download e instalação e estabelecer uma ligação com o Azure Arc. Este script executa passos semelhantes descritos nas [máquinas híbridas Connect para Azure a partir do artigo do portal Azure.](onboard-portal.md)

Se a máquina precisar de comunicar através de um servidor proxy para o serviço, depois de instalar o agente, precisa de executar um comando descrito [aqui](onboard-portal.md#configure-the-agent-proxy-setting). Isto define a variável `https_proxy`ambiente do sistema proxy servidor . Em vez de executar o comando manualmente, pode executar este passo com o DSC utilizando o módulo [ComputeManagementDsc.](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0)

>[!NOTE]
>Para permitir a execução do DSC, o Windows precisa de ser configurado para receber comandos remotos PowerShell mesmo quando estiver a executar uma configuração de localização. Para configurar facilmente o seu `Set-WsManQuickConfig -Force` ambiente corretamente, basta correr num terminal powerShell elevado.
>

Os documentos de configuração (ficheiros MOF) podem ser aplicados à máquina utilizando o `Start-DscConfiguration` cmdlet.

Seguem-se os parâmetros que passas para o script PowerShell para usar.

- `TenantId`: O identificador único (GUID) que representa a sua instância dedicada de Azure AD.

- `SubscriptionId`: O ID de subscrição (GUID) da sua assinatura Azure em que deseja as máquinas.

- `ResourceGroup`: O nome do grupo de recursos onde pretende que as suas máquinas ligadas pertençam.

- `Location`: Ver [regiões azure apoiadas](overview.md#supported-regions). Esta localização pode ser a mesma ou diferente, como a localização do grupo de recursos.

- `Tags`: Conjunto de cordas de etiquetas que devem ser aplicadas ao recurso da máquina conectada.

- `Credential`: Um objeto de credencial PowerShell com o **ApplicationId** e **a palavra-passe** utilizadopara registar máquinas à escala utilizando um diretor de [serviço](onboard-service-principal.md). 

1. Numa consola PowerShell, navegue para a `.ps1` pasta onde guardou o ficheiro.

2. Executar os seguintes comandos PowerShell para compilar o documento MOF (para obter informações sobre a compilação de configurações dSC, consulte [configurações DSC:](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations?view=powershell-7)

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. Isto criará `localhost.mof file` uma numa `C:\dsc`nova pasta chamada .

Depois de instalar o agente e configurá-lo para ligar ao Azure Arc para servidores (pré-visualização), vá ao portal Azure para verificar se o servidor foi ligado com sucesso. Veja as suas máquinas no [portal Azure.](https://aka.ms/hybridmachineportal)

## <a name="adding-to-existing-configurations"></a>Adicionando às configurações existentes

Este recurso pode ser adicionado às configurações dSC existentes para representar uma configuração de ponta a ponta para uma máquina. Por exemplo, pode desejar adicionar este recurso a uma configuração que define as definições seguras do sistema operativo.

O módulo [CompsiteResource](https://www.powershellgallery.com/packages/compositeresource/0.4.0) da PowerShell Gallery pode ser usado para criar um [recurso composto](https://docs.microsoft.com/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7) da configuração do exemplo, para simplificar ainda mais a combinação de configurações.

## <a name="next-steps"></a>Passos seguintes

- Saiba como gerir a sua máquina utilizando a [Política Azure](../../governance/policy/overview.md), para coisas como a configuração de [hóspedes](../../governance/policy/concepts/guest-configuration.md)VM , verificando que a máquina está a reportar ao espaço de trabalho esperado do Log Analytics, permitir a monitorização com o [Monitor Azure com VMs](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md), e muito mais.

- Saiba mais sobre o [agente Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). O agente Log Analytics para Windows e Linux é necessário quando pretende monitorizar proativamente o SISTEMA e as cargas de trabalho em funcionamento na máquina, geri-la utilizando livros de execução automation ou soluções como a Update Management, ou utilizar outros serviços Azure como o [Azure Security Center.](../../security-center/security-center-intro.md)