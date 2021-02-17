---
title: Ligue as máquinas híbridas ao Azure utilizando o PowerShell
description: Neste artigo, aprende a instalar o agente e a ligar uma máquina ao Azure utilizando servidores ativados pelo Azure Arc. Podes fazer isto com o PowerShell.
ms.date: 10/28/2020
ms.topic: conceptual
ms.openlocfilehash: 07a00de9077378ce3e3f7a7578b66e93d1b04f2b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584948"
---
# <a name="connect-hybrid-machines-to-azure-by-using-powershell"></a>Ligue as máquinas híbridas ao Azure utilizando o PowerShell

Para servidores ativados com Azure Arc, pode tomar medidas manuais para os ativar para uma ou mais máquinas Windows ou Linux no seu ambiente. Em alternativa, pode utilizar o cmdlet PowerShell [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) para descarregar o agente 'Máquina Conectada', instalar o agente e registar a máquina com o Arco Azure. O cmdlet descarrega o pacote de agente Windows (Instalador windows) do Microsoft Download Center e o pacote de agente Linux do repositório de pacotes da Microsoft.

Este método requer que tenha permissões de administrador na máquina para instalar e configurar o agente. No Linux, utilizando a conta raiz e no Windows, é membro do grupo de Administradores Locais. Pode concluir este processo de forma interativa ou remota num servidor do Windows utilizando [a remoting PowerShell](/powershell/scripting/learn/ps101/08-powershell-remoting).

Antes de começar, reveja os [pré-requisitos](agent-overview.md#prerequisites) e verifique se a sua subscrição e recursos cumprem os requisitos. Para obter informações sobre regiões apoiadas e outras considerações conexas, consulte [as regiões de Azure apoiadas.](overview.md#supported-regions)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Uma máquina com Azure PowerShell. Para obter instruções, consulte [instalar e configurar a Azure PowerShell](/powershell/azure/).

Utiliza o PowerShell para gerir as extensões VM nos seus servidores híbridos geridos por servidores ativados pelo Azure Arc. Antes de utilizar o PowerShell, instale o `Az.ConnectedMachine` módulo. Executar o seguinte comando no seu servidor ativado com Azure Arc:

```powershell
Install-Module -Name Az.ConnectedMachine
```

Quando a instalação terminar, vê a seguinte mensagem:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>Instale o agente e ligue-se ao Azure

1. Abra uma consola PowerShell com privilégios elevados.

2. Inscreva-se em Azure com o comando `Connect-AzAccount` .

3. Para instalar o agente 'Máquina Conectada', `Connect-AzConnectedMachine` utilize-o com os `-Name` `-ResourceGroupName` parâmetros , e `-Location` parâmetros. Utilize o `-SubscriptionId` parâmetro para anular a subscrição predefinida como resultado do contexto Azure criado após a entrada. Executar um dos seguintes comandos:

    * Para instalar o agente 'Máquina Conectada' na máquina-alvo que pode comunicar diretamente ao Azure, corra:

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region>
        ```
    
    * Para instalar o agente 'Máquina Conectada' na máquina-alvo que comunica através de um servidor proxy, execute:
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -Proxy http://<proxyURL>:<proxyport>
        ```

Se o agente não iniciar após a configuração terminar, verifique se os registos são informativo de erro. No Windows, verifique este ficheiro: *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*. No Linux, consulte este ficheiro: */var/opt/azcmagent/log/himds.log*.

## <a name="install-and-connect-by-using-powershell-remoting"></a>Instale e conecte utilizando a remoting PowerShell

Eis como configurar um ou mais servidores windows com servidores ativados com Azure Arc. Tem de ativar a remoing PowerShell na máquina remota. Para tal, utilize o cmdlet `Enable-PSRemoting`.

1. Abra uma consola PowerShell como administrador.

2. Inscreva-se em Azure com o comando `Connect-AzAccount` .

3. Para instalar o agente 'Máquina Conectada', `Connect-AzConnectedMachine` utilize-o com os `-ResourceGroupName` parâmetros e `-Location` parâmetros. Os nomes dos recursos Azure utilizarão automaticamente o nome de anfitrião de cada servidor. Utilize o `-SubscriptionId` parâmetro para anular a subscrição predefinida como resultado do contexto Azure criado após a entrada.

    * Para instalar o agente 'Máquina Conectada' na máquina-alvo que pode comunicar diretamente ao Azure, executar o seguinte comando:
    
        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```
    
    * Para instalar o agente 'Máquina Conectada' em várias máquinas remotas ao mesmo tempo, adicione uma lista de nomes de máquinas remotas, cada uma separada por uma vírgula.

        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```

    O exemplo a seguir mostra os resultados do comando dirigido a uma única máquina:
    
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

Depois de instalar e configurar o agente para registar-se com servidores ativados Azure Arc, vá ao portal Azure para verificar se o servidor se ligou com sucesso. Veja a sua máquina no [portal Azure](https://portal.azure.com).

![Screenshot do dashboard do Servers, mostrando uma ligação de servidor bem sucedida.](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Passos seguintes

* Se necessário, consulte o guia do [agente de máquinas ligados à resolução de problemas](troubleshoot-agent-onboard.md).

* Saiba como gerir a sua máquina utilizando a [Política Azure](../../governance/policy/overview.md). Pode utilizar a [configuração do hóspede](../../governance/policy/concepts/guest-configuration.md)VM, verificar se a máquina está a reportar ao espaço de trabalho esperado do Log Analytics e ativar a monitorização com o [Azure Monitor com VMs](../../azure-monitor/vm/vminsights-enable-policy.md).

* Saiba mais sobre o [agente Log Analytics.](../../azure-monitor/agents/log-analytics-agent.md) O agente Log Analytics para Windows e Linux é necessário quando pretende recolher dados de monitorização do sistema operativo e da carga de trabalho, ou geri-lo utilizando livros de aplicação da Azure Automation ou funcionalidades como a Gestão de Atualização. Este agente também é obrigado a utilizar outros serviços Azure, como o [Azure Security Center](../../security-center/security-center-introduction.md).
