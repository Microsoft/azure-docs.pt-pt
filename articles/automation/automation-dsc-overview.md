---
title: Visão geral da configuração do estado de automação do Azure
description: Uma visão geral da DSC (configuração de estado da automação) do Azure, seus termos e problemas conhecidos
keywords: DSC do PowerShell, configuração de estado desejado, Azure DSC do PowerShell
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 787cade13a0636bb25afa1d4043a977f512484f9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850895"
---
# <a name="azure-automation-state-configuration-overview"></a>Visão geral da configuração do estado de automação do Azure

A configuração de estado da automação do Azure é um serviço do Azure que permite que você grave, gerencie e compile [configurações](/powershell/scripting/dsc/configurations/configurations)de DSC (configuração de estado desejado) do PowerShell, importe [recursos de DSC](/powershell/scripting/dsc/resources/resources)e atribua configurações a nós de destino, tudo na nuvem.

## <a name="why-use-azure-automation-state-configuration"></a>Por que usar a configuração de estado da automação do Azure

A configuração de estado da automação do Azure fornece várias vantagens em relação ao uso de DSC fora do Azure.

### <a name="built-in-pull-server"></a>Servidor de pull interno

A configuração de estado da automação do Azure fornece um servidor de pull de DSC semelhante ao [recurso do Windows DSC-Service](/powershell/scripting/dsc/pull-server/pullserver) para que os nós de destino recebam automaticamente as configurações, estejam em conformidade com o estado desejado e relatem sua conformidade. O servidor de pull interno na automação do Azure elimina a necessidade de configurar e manter seu próprio servidor de pull. A automação do Azure pode ter como destino máquinas virtuais ou Linux físicas ou do Windows, na nuvem ou localmente.

### <a name="management-of-all-your-dsc-artifacts"></a>Gerenciamento de todos os seus artefatos de DSC

A configuração de estado da automação do Azure traz a mesma camada de gerenciamento para a [configuração de estado desejado do PowerShell](/powershell/scripting/dsc/overview/overview) , pois a automação do Azure oferece o script do PowerShell.

No portal do Azure, ou no PowerShell, você pode gerenciar todas as suas configurações de DSC, recursos e nós de destino.

![Captura de tela da página de automação do Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Importar dados de relatório em logs de Azure Monitor

Os nós gerenciados com a configuração de estado da automação do Azure enviam dados detalhados de status do relatório para o servidor de pull interno. Você pode configurar a configuração de estado da automação do Azure para enviar esses dados para seu espaço de trabalho do Log Analytics. Para saber como enviar dados de status de configuração de estado para seu espaço de trabalho Log Analytics, consulte [encaminhar dados de relatório de configuração de estado da automação do Azure para Azure monitor logs](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Pré-requisitos

Considere os seguintes requisitos ao usar a DSC (configuração de estado de automação do Azure).

### <a name="operating-system-requirements"></a>Requisitos do Sistema Operativo

Para nós que executam o Windows, há suporte para as seguintes versões:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

O SKU do produto autônomo do [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) não contém uma implementação do estado desejado configuração, portanto, ele não pode ser gerenciado pela configuração do estado da automação do PowerShell ou da DSC do Azure.

Para nós que executam o Linux, há suporte para as seguintes distribuições/versões:

A extensão do Linux do DSC dá suporte a todas as distribuições do Linux listadas em [distribuições do Linux com suporte](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>Requisitos de DSC

Para todos os nós do Windows em execução no Azure, o [WMF 5,1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) será instalado durante a integração.  Para nós que executam o Windows Server 2012 e o Windows 7, o [WinRM será habilitado](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency).

Para todos os nós do Linux em execução no Azure, o [PowerShell DSC para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) será instalado durante a integração.

### <a name="network-planning"></a>Configurar redes privadas

Se os nós estiverem localizados em uma rede privada, a porta e as URLs a seguir serão necessárias para que a DSC (configuração de estado) se comunique com a automação:

* Porta: somente TCP 443 é necessário para acesso à Internet de saída.
* URL global: *. azure-automation.net
* URL global de US Gov-Virgínia: *. azure-automation.us
* Serviço do agente: https://\<workspaceid\>. agentsvc.azure-automation.net

Isso fornece conectividade de rede para o nó gerenciado se comunicar com a automação do Azure.
Se você estiver usando recursos de DSC que se comunicam entre nós, como os [recursos WAITFOR *](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), também será necessário permitir o tráfego entre os nós.
Consulte a documentação para cada recurso de DSC para entender esses requisitos de rede.

#### <a name="proxy-support"></a>Suporte a proxy

O suporte de proxy para o agente DSC está disponível no Windows versão 1809 e posterior.
Para configurar essa opção, defina o valor de **ProxyURL** e **ProxyCredential** no [script de metaconfiguração](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) usado para registrar nós.
O proxy não está disponível no DSC para versões anteriores do Windows.

Para nós do Linux, o agente de DSC oferece suporte a proxy e utilizará a variável http_proxy para determinar a URL.

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Namespace e intervalos de rede de configuração de estado do Azure

É recomendável usar os endereços listados ao definir exceções. Para endereços IP, você pode baixar os [intervalos de IP do Microsoft Azure datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Esse arquivo é atualizado semanalmente e tem os intervalos atualmente implantados e as alterações futuras nos intervalos de IP.

Se você tiver uma conta de automação definida para uma região específica, poderá restringir a comunicação com esse datacenter regional. A tabela a seguir fornece o registro DNS para cada região:

| **Região** | **Registro DNS** |
| --- | --- |
| E.U.A. Centro-Oeste | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| E.U.A. Centro-Sul |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| E.U.A. Leste   | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| E.U.A. Leste 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Canadá Central |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa Ocidental |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa do Norte |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Ásia Sudeste |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Índia Central |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Leste do Japão |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Sudeste da Austrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Sul do Reino Unido | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov - Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Para obter uma lista de endereços IP de região em vez de nomes de região, baixe o arquivo XML do [endereço IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653) no centro de download da Microsoft.

> [!NOTE]
> O arquivo XML do endereço IP do datacenter do Azure lista os intervalos de endereços IP que são usados nos data centers Microsoft Azure. O arquivo inclui intervalos de computação, SQL e armazenamento.
>
>Um arquivo atualizado é lançado semanalmente. O arquivo reflete os intervalos atualmente implantados e as alterações futuras nos intervalos de IP. Novos intervalos que aparecem no arquivo não são usados nos data centers por pelo menos uma semana.
>
> É uma boa ideia baixar o novo arquivo XML a cada semana. Em seguida, atualize seu site para identificar corretamente os serviços em execução no Azure. Os usuários do Azure ExpressRoute devem observar que esse arquivo é usado para atualizar o anúncio de Border Gateway Protocol (BGP) do espaço do Azure na primeira semana de cada mês.

## <a name="next-steps"></a>Passos seguintes

- Para começar, consulte [introdução à configuração de estado de automação do Azure](automation-dsc-getting-started.md)
- Para saber como integrar nós, confira [máquinas de integração para gerenciamento pela configuração de estado da automação do Azure](automation-dsc-onboarding.md)
- Para saber mais sobre como compilar configurações DSC para que você possa atribuí-las aos nós de destino, consulte [compilando configurações na configuração de estado da automação do Azure](automation-dsc-compile.md)
- Para referência de cmdlet do PowerShell, consulte [cmdlets de configuração do estado de automação do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [preços de configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de como usar a configuração de estado de automação do Azure em um pipeline de implantação contínua, consulte [implantação contínua usando configuração de estado de automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
