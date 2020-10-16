---
title: Instale o agente da máquina conectada utilizando o Windows PowerShell DSC
description: Neste artigo, aprende-se a ligar máquinas ao Azure utilizando servidores ativados pelo Azure Arc utilizando o Windows PowerShell DSC.
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: b351aa8e989bd36c135271d3adafca6eb64ccf39
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107560"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>Como instalar o agente da Máquina Conectada utilizando o Windows PowerShell DSC

Utilizando [a configuração estatal desejada do Windows PowerShell](/powershell/scripting/dsc/getting-started/winGettingStarted) (DSC), pode automatizar a instalação e configuração do software para um computador Windows. Este artigo descreve como utilizar o DSC para instalar o Azure Arc ativado por servidores Agentes de Máquinas Conectadas em máquinas híbridas do Windows.

## <a name="requirements"></a>Requisitos

- Versão 4.0 ou superior do Windows PowerShell

- O módulo [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc) DSC

- Um chefe de serviço para ligar as máquinas ao Azure Arc ativou servidores não interactivamente. Siga os passos sob a secção [Criar um Principal de Serviço para embarque à escala](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) se ainda não tiver criado um principal de serviço para servidores ativados pela Arc.

## <a name="install-the-connectedmachine-dsc-module"></a>Instale o módulo ConnectedMachine DSC

1. Para instalar manualmente o módulo, descarregue o código fonte e desaperte o conteúdo do diretório do projeto para o `$env:ProgramFiles\WindowsPowerShell\Modules folder` . Ou, executar o seguinte comando para instalar a partir da galeria PowerShell utilizando o PowerShellGet (em PowerShell 5.0):

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. Para confirmar a instalação, execute o seguinte comando e certifique-se de que vê os recursos DSC da máquina ligada Azure disponíveis.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   Na saída, deverá ver algo semelhante ao seguinte:

   ![Confirmação do exemplo de instalação do módulo DSC da máquina conectada](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>Instale o agente e ligue-se ao Azure

Os recursos deste módulo são projetados para gerir a configuração do Agente de Máquinas Conectada Azure. Também está incluído um script PowerShell, `AzureConnectedMachineAgent.ps1` encontrado na `AzureConnectedMachineDsc\examples` pasta. Utiliza recursos comunitários para automatizar o download e instalação e estabelecer uma ligação com o Arco Azure. Este script executa passos semelhantes descritos nas [máquinas híbridas Connect a Azure a partir do artigo do portal Azure.](onboard-portal.md)

Se a máquina precisar de comunicar através de um servidor proxy para o serviço, depois de instalar o agente, precisa de executar um comando que está descrito [aqui](manage-agent.md#update-or-remove-proxy-settings). Isto define a variável do ambiente do sistema de servidor de procuração `https_proxy` . Em vez de executar o comando manualmente, pode executar este passo com o DSC utilizando o módulo [ComputeManagementDsc.](https://www.powershellgallery.com/packages/ComputerManagementDsc)

>[!NOTE]
>Para permitir a execução do DSC, o Windows precisa de ser configurado para receber comandos remotos PowerShell mesmo quando estiver a executar uma configuração local. Para configurar facilmente o seu ambiente corretamente, basta correr `Set-WsManQuickConfig -Force` num terminal PowerShell elevado.
>

Os documentos de configuração (ficheiros MOF) podem ser aplicados à máquina utilizando o `Start-DscConfiguration` cmdlet.

Seguem-se os parâmetros que passa para o script PowerShell para utilizar.

- `TenantId`: O identificador único (GUID) que representa o seu exemplo dedicado de Azure AD.

- `SubscriptionId`: O ID de subscrição (GUID) da sua assinatura Azure em que pretende que as máquinas entrem.

- `ResourceGroup`: O nome do grupo de recursos onde pretende que as suas máquinas ligadas pertençam.

- `Location`: Ver [regiões de Azure apoiadas.](overview.md#supported-regions) Esta localização pode ser a mesma ou diferente, como a localização do grupo de recursos.

- `Tags`: Conjunto de etiquetas de corda que devem ser aplicadas ao recurso da máquina ligada.

- `Credential`: Um objeto credencial PowerShell com o **ApplicationId** e **a palavra-passe** utilizada para registar máquinas à escala utilizando um [principal serviço](onboard-service-principal.md).

1. Numa consola PowerShell, navegue para a pasta onde guardou o `.ps1` ficheiro.

2. Executar os seguintes comandos PowerShell para compilar o documento MOF (para obter informações sobre a compilação de configurações de DSC, consulte [configurações de DSC](/powershell/scripting/dsc/configurations/configurations):

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. Isto criará uma `localhost.mof file` nova pasta chamada `C:\dsc` .

Depois de instalar o agente e configurá-lo para ligar aos servidores ativados do Azure Arc, vá ao portal Azure para verificar se o servidor foi conectado com sucesso. Veja as suas máquinas no [portal do Azure](https://aka.ms/hybridmachineportal).

## <a name="adding-to-existing-configurations"></a>Adicionar às configurações existentes

Este recurso pode ser adicionado às configurações DSC existentes para representar uma configuração de ponta a ponta para uma máquina. Por exemplo, é melhor adicionar este recurso a uma configuração que define as definições seguras do sistema operativo.

O módulo [CompositeResource](https://www.powershellgallery.com/packages/compositeresource) da PowerShell Gallery pode ser utilizado para criar um [recurso composto](/powershell/scripting/dsc/resources/authoringResourceComposite) da configuração do exemplo, para simplificar ainda mais as configurações combinadas.

## <a name="next-steps"></a>Passos seguintes

* As informações relativas à resolução de problemas podem ser encontradas no guia do [agente da máquina conectado de resolução de problemas.](troubleshoot-agent-onboard.md)

* Aprenda a gerir a sua máquina utilizando [a Azure Policy](../../governance/policy/overview.md), para coisas como [a configuração do hóspede](../../governance/policy/concepts/guest-configuration.md)VM , verificando se a máquina está a reportar ao espaço de trabalho esperado do Log Analytics, permitir a monitorização com o [Azure Monitor com VMs](../../azure-monitor/insights/vminsights-enable-policy.md), e muito mais.

* Saiba mais sobre o [agente Log Analytics.](../../azure-monitor/platform/log-analytics-agent.md) O agente Log Analytics para Windows e Linux é necessário quando pretende monitorizar proactivamente o SISTEMA e as cargas de trabalho em funcionamento na máquina, geri-lo utilizando livros de automação ou soluções como a Update Management, ou utilizar outros serviços Azure como o [Azure Security Center.](../../security-center/security-center-introduction.md)