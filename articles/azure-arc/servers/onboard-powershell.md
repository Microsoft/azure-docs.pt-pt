---
title: Ligue máquinas híbridas ao Azure usando o PowerShell
description: Neste artigo, aprende-se a instalar o agente e a ligar uma máquina ao Azure utilizando servidores ativados pelo Azure Arc utilizando o PowerShell.
ms.date: 10/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0755846ef02377edade98b69e478908a111ab247
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92901529"
---
# <a name="connect-hybrid-machines-to-azure-using-powershell"></a>Ligue máquinas híbridas ao Azure usando o PowerShell

Pode ativar os servidores ativados do Azure Arc para uma ou mais máquinas Windows ou Linux no seu ambiente, realizando um conjunto de passos manuais. Ou pode utilizar a cmdlet PowerShell [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) para descarregar o agente da Máquina Conectada, instalar o agente e registar a máquina com o Arco Azure. O cmdlet descarrega o pacote do Windows Agent Windows Installer do Microsoft Download Center e o pacote de agente Linux do repositório de pacotes da Microsoft.

Este método requer que tenha permissões de administrador na máquina para instalar e configurar o agente. No Linux, utilizando a conta raiz e no Windows, é membro do grupo de Administradores Locais. Pode concluir este processo de forma interativa ou remota num servidor do Windows utilizando [a remoting PowerShell](/powershell/scripting/learn/ps101/08-powershell-remoting).

Antes de começar, certifique-se de rever os [pré-requisitos](agent-overview.md#prerequisites) e verificar se a sua subscrição e recursos cumprem os requisitos. Para obter informações sobre regiões apoiadas e outras considerações conexas, consulte [as regiões de Azure apoiadas.](overview.md#supported-regions)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Uma máquina com Azure PowerShell. Para obter instruções, consulte [instalar e configurar a Azure PowerShell](/powershell/azure/).

Antes de utilizar o Azure PowerShell para gerir as extensões VM no seu servidor híbrido gerido por servidores ativados pelo Arc, é necessário instalar o `Az.ConnectedMachine` módulo. Executar o seguinte comando no seu servidor ativado arc:

```powershell
Install-Module -Name Az.ConnectedMachine
```

Quando a instalação estiver concluída, a seguinte mensagem é devolvida:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>Instale o agente e ligue-se ao Azure

1. Abra uma consola PowerShell com privilégios elevados.

2. Inscreva-se em Azure executando o comando `Connect-AzAccount` .

3. Para instalar o agente 'Máquina Conectada', `Connect-AzConnectedMachine` utilize-o com os `-Name` `-ResourceGroupName` parâmetros , e `-Location` parâmetros. Utilize o `-SubscriptionId` parâmetro para anular a subscrição predefinida como resultado do contexto Azure criado após a entrada. Executar um dos seguintes comandos:

    * Para instalar o agente 'Máquina Conectada' na máquina-alvo que pode comunicar diretamente ao Azure, corra:

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e
        ```
    
    * Para instalar o agente 'Máquina Conectada' na máquina-alvo que comunica através de um servidor proxy, execute:
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e -proxy http://<proxyURL>:<proxyport>
        ```

Se o agente não iniciar após a configuração terminar, verifique se os registos são informativo de erro. No Windows em *%ProgramData%\\AzureConnectedMachineAgent\Log\himds.log* , e no Linux em */var/opt/azcmagent/log/himds.log* .

## <a name="install-and-connect-using-powershell-remoting"></a>Instale e conecte-se utilizando a remoting PowerShell

Execute os seguintes passos para configurar um ou mais servidores windows com servidores ativados Azure Arc. A remoing PowerShell deve ser ativada na máquina remota. Utilize o `Enable-PSRemoting` cmdlet para ativar a remose powerShell.

1. Abra uma consola PowerShell como administrador.

2. Inscreva-se em Azure executando o comando `Connect-AzAccount` .

3. Para instalar o agente 'Máquina Conectada', `Connect-AzConnectedMachine` utilize-o com os `-Name` `-ResourceGroupName` parâmetros , e `-Location` parâmetros. Utilize o `-SubscriptionId` parâmetro para anular a subscrição predefinida como resultado do contexto Azure criado após a entrada.

    * Para instalar o agente 'Máquina Conectada' na máquina-alvo que pode comunicar diretamente ao Azure, executar o seguinte comando:
    
        ```azurepowershell
        $session = Connect-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -PSSession $session
        ```
    
    * Para instalar o agente 'Máquina Conectada' em várias máquinas remotas ao mesmo tempo, adicione uma lista de nomes de máquinas remotas separados por uma vírgula.

        ```azurepowershell
        $session = Connect-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -PSSession $session
        ```

    Seguem-se os resultados do comando dirigido a uma única máquina:
    
    ```azurepowershell
    time="2020-08-07T13:13:25-07:00" level=info msg="Onboarding Machine. It usually takes a few minutes to complete. Sometimes it may take longer depending on network and server load status."
    time="2020-08-07T13:13:25-07:00" level=info msg="Check network connectivity to all endpoints..."
    time="2020-08-07T13:13:29-07:00" level=info msg="All endpoints are available... continue onboarding"
    time="2020-08-07T13:13:50-07:00" level=info msg="Successfully Onboarded Resource to Azure" VM Id=f65bffc7-4734-483e-b3ca-3164bfa42941
    
    Name           Location OSName   Status     ProvisioningState
    ----           -------- ------   ------     -----------------
    myMachineName  eastus   windows  Connected  Succeeded
    ```

## <a name="verify-the-connection-with-azure-arc"></a>Verificar a ligação com o Azure Arc

Depois de instalar o agente e configurá-lo para ligar aos servidores ativados do Azure Arc, vá ao portal Azure para verificar se o servidor se ligou com sucesso. Veja as suas máquinas no [portal do Azure](https://portal.azure.com).

![Uma ligação de servidor bem sucedida](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Próximas etapas

* As informações relativas à resolução de problemas podem ser encontradas no guia do [agente da máquina conectado de resolução de problemas.](troubleshoot-agent-onboard.md)

* Aprenda a gerir a sua máquina utilizando [a Azure Policy](../../governance/policy/overview.md), para coisas como [a configuração do hóspede](../../governance/policy/concepts/guest-configuration.md)VM , verificando se a máquina está a reportar ao espaço de trabalho esperado do Log Analytics, permitir a monitorização com o [Azure Monitor com VMs](../../azure-monitor/insights/vminsights-enable-policy.md), e muito mais.

* Saiba mais sobre o [agente Log Analytics.](../../azure-monitor/platform/log-analytics-agent.md) O agente Log Analytics para Windows e Linux é necessário quando pretende recolher dados de monitorização do sistema operativo e da carga de trabalho, geri-lo utilizando livros de aplicação da Automação ou funcionalidades como Update Management, ou utilizar outros serviços Azure como [o Azure Security Center](../../security-center/security-center-introduction.md).