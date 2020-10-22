---
title: Ligue máquinas híbridas ao Azure usando o PowerShell
description: Neste artigo, aprende-se a instalar o agente e a ligar uma máquina ao Azure utilizando servidores ativados pelo Azure Arc utilizando o PowerShell.
ms.date: 10/21/2020
ms.topic: conceptual
ms.openlocfilehash: d36fd174606b49b28b1d8343bff6ccc1f62e5194
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92376297"
---
# <a name="connect-hybrid-machines-to-azure-using-powershell"></a>Ligue máquinas híbridas ao Azure usando o PowerShell

Pode ativar os servidores ativados do Azure Arc para um ou um pequeno número de máquinas Windows ou Linux no seu ambiente, realizando manualmente um conjunto de passos. Ou pode utilizar o cmdlet PowerShell [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine). Este cmdlet executa as seguintes ações:

- Configura a máquina hospedeira para descarregar o agente Windows do Microsoft Download Center e o pacote de agente Linux a partir de packages.microsoft.com.
- Instala o agente 'Máquina Conectada'.
- Regista a máquina com Arco Azure

Este método requer que tenha permissões de administrador na máquina para instalar e configurar o agente. No Linux, utilizando a conta raiz e no Windows, é membro do grupo de Administradores Locais.

Antes de começar, certifique-se de rever os [pré-requisitos](agent-overview.md#prerequisites) e verificar se a sua subscrição e recursos cumprem os requisitos. Para obter informações sobre regiões apoiadas e outras considerações conexas, consulte [as regiões de Azure apoiadas.](overview.md#supported-regions)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Um computador com a Azure PowerShell. Para obter instruções, consulte [instalar e configurar a Azure PowerShell](/powershell/azure/).

Antes de utilizar o Azure PowerShell para gerir as extensões VM no seu servidor híbrido gerido por servidores ativados pelo Arc, é necessário instalar o `Az.ConnectedMachine` módulo. Executar o seguinte comando no seu servidor ativado arc:

```powershell
Install-Module -Name Az.ConnectedMachine
```

Quando a instalação estiver concluída, a seguinte mensagem é devolvida:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-and-validate-the-agent-on-windows"></a>Instale e valide o agente no Windows

1. Abra uma consola PowerShell como administrador.

2. Inscreva-se em Azure executando o comando `Connect-AzAccount` .

3. Para instalar o agente 'Máquina Conectada', `Connect-AzConnectedMachine` utilize-o com os `-Name` `-ResourceGroupName` parâmetros , e `-Location` parâmetros. Utilize o `-SubscriptionId` parâmetro para anular a subscrição predefinida como resultado do contexto Azure criado após a entrada.

    Para instalar o agente 'Máquina Conectada' na máquina-alvo que pode comunicar diretamente ao Azure, executar o seguinte comando::

    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e
    ```
    
    Se a máquina-alvo comunicar através de um servidor proxy, executar o seguinte comando:
    
    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e -proxy http://<proxyURL>:<proxyport>
    ```

    Se o agente não iniciar após a configuração terminar, verifique se os registos são informativo de erro. No Windows em *%ProgramData%\\AzureConnectedMachineAgent\Log\himds.log*, e no Linux em */var/opt/azcmagent/log/himds.log*.

## <a name="verify-the-connection-with-azure-arc"></a>Verificar a ligação com o Azure Arc

Depois de instalar o agente e configurá-lo para ligar aos servidores ativados do Azure Arc, vá ao portal Azure para verificar se o servidor se ligou com sucesso. Veja as suas máquinas no [portal do Azure](https://portal.azure.com).

![Uma ligação de servidor bem sucedida](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Passos seguintes

* As informações relativas à resolução de problemas podem ser encontradas no guia do [agente da máquina conectado de resolução de problemas.](troubleshoot-agent-onboard.md)

* Aprenda a gerir a sua máquina utilizando [a Azure Policy](../../governance/policy/overview.md), para coisas como [a configuração do hóspede](../../governance/policy/concepts/guest-configuration.md)VM , verificando se a máquina está a reportar ao espaço de trabalho esperado do Log Analytics, permitir a monitorização com o [Azure Monitor com VMs](../../azure-monitor/insights/vminsights-enable-policy.md), e muito mais.

* Saiba mais sobre o [agente Log Analytics.](../../azure-monitor/platform/log-analytics-agent.md) O agente Log Analytics para Windows e Linux é necessário quando pretende recolher dados de monitorização do sistema operativo e da carga de trabalho, geri-lo utilizando livros de aplicação da Automação ou funcionalidades como Update Management, ou utilizar outros serviços Azure como [o Azure Security Center](../../security-center/security-center-introduction.md).