---
title: Visão geral da configuração do estado da automatização azure
description: Uma visão geral da Configuração do Estado da Automação Azure (DSC), os seus termos e questões conhecidas
keywords: powershell dsc, configuração de estado desejado, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 787cade13a0636bb25afa1d4043a977f512484f9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278925"
---
# <a name="azure-automation-state-configuration-overview"></a>Visão geral da configuração do estado da automatização azure

A Configuração do Estado da Automação Azure é um serviço Azure que lhe permite escrever, gerir e compilar [configurações](/powershell/scripting/dsc/configurations/configurations)de Configuração do Estado Desejada pela PowerShell (DSC), importar [Recursos DSC](/powershell/scripting/dsc/resources/resources)e atribuir configurações para os nós-alvo, tudo na nuvem.

## <a name="why-use-azure-automation-state-configuration"></a>Porquê usar a configuração do Estado da Automação Azure

A Configuração do Estado da Automação Azure oferece várias vantagens em utilizar o DSC fora do Azure.

### <a name="built-in-pull-server"></a>Servidor de puxar incorporado

A Configuração do Estado de Automação Azure fornece um servidor de puxar DSC semelhante ao [Serviço DSC-Service](/powershell/scripting/dsc/pull-server/pullserver) da funcionalidade Windows para que os nós-alvo recebam automaticamente configurações, estejam em conformidade com o estado pretendido e informem sobre a sua conformidade. O servidor de pull incorporado na Azure Automation elimina a necessidade de configurar e manter o seu próprio servidor de puxar. A Azure Automation pode visar máquinas virtual ou físicas de Windows ou Linux, na nuvem ou no local.

### <a name="management-of-all-your-dsc-artifacts"></a>Gestão de todos os seus artefactos DSC

A Configuração do Estado da Automação Azure traz a mesma camada de gestão para a [Configuração estatal desejada pela PowerShell,](/powershell/scripting/dsc/overview/overview) à medida que a Azure Automation oferece para scripts PowerShell.

A partir do portal Azure, ou da PowerShell, você pode gerir todas as suas configurações, recursos e nós alvo.

![Screenshot da página de Automação Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Dados de reporte de importação em registos do Monitor Do Azure

Os nós que são geridos com a Configuração do Estado da Automatização Azure enviam dados de estado de reporte detalhados para o servidor de pull incorporado. Pode configurar a Configuração do Estado de Automação Azure para enviar estes dados para o seu espaço de trabalho Log Analytics. Para saber como enviar dados do estado de configuração para o seu espaço de trabalho Log Analytics, consulte dados de informação de configuração do Estado de [Automação Avançada Azure para registos do Monitor Azure](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Pré-requisitos

Considere os seguintes requisitos ao utilizar a Configuração do Estado da Automação Azure (DSC).

### <a name="operating-system-requirements"></a>Requisitos do sistema operativo

Para os nós que executam o Windows, as seguintes versões são suportadas:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

O produto autónomo do [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) não contém uma implementação da Configuração de Estado Desejada, pelo que não pode ser gerido pela PowerShell DSC ou pela Configuração do Estado de Automação Azure.

Para os nós que executam o Linux, são suportados os seguintes distros/versões:

A extensão DSC Linux suporta todas as distribuições Linux listadas no âmbito da [SupportEd Linux Distributions](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>Requisitos dSC

Para todos os nós do Windows em funcionamento em Azure, [o WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) será instalado durante o embarque.  Para os nós que executam o Windows Server 2012 e o Windows 7, [o WinRM estará ativado](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency).

Para todos os nós Linux em execução em Azure, [o PowerShell DSC para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) será instalado durante o embarque.

### <a name="network-planning"></a>Configurar redes privadas

Se os seus nós estiverem localizados dentro de uma rede privada, são necessários os seguintes portes e URLs para a Configuração do Estado (DSC) para comunicar com a Automação:

* Porta: Só é necessário tCP 443 para acesso à Internet de saída.
* URL global: *.azure-automation.net
* URL global dos EUA Gov Virginia: *.azure-automation.us
* Serviço de agente: https://\<espaço de trabalhoId\>.agentsvc.azure-automation.net

Isto fornece conectividade de rede para o nó gerido comunicar com a Azure Automation.
Se estiver a utilizar recursos DSC que comunicam entre nós, como os [recursos WaitFor**](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), também terá de permitir o tráfego entre nós.
Consulte a documentação de cada recurso dSC para compreender esses requisitos de rede.

#### <a name="proxy-support"></a>Apoio proxy

O suporte proxy para o agente DSC está disponível na versão 1809 do Windows e posteriormente.
Para configurar esta opção, delineie o valor para **ProxyURL** e **ProxyCredential** no script de [configuração](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) utilizado para registar nós.
A Proxy não está disponível em DSC para versões anteriores do Windows.

Para os nós linux, o agente DSC suporta procuração e utilizará a variável http_proxy para determinar o url.

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Gamas de rede de configuração do Estado Azure e espaço de nome

É aconselhável usar os endereços listados na definição de exceções. Para endereços IP pode descarregar os intervalos IP do [Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Este ficheiro é atualizado semanalmente, e tem as gamas atualmente implementadas e quaisquer alterações futuras nas gamas IP.

Se tiver uma conta de Automação definida para uma região específica, pode restringir a comunicação a esse datacenter regional. O quadro seguinte fornece o registo dNS para cada região:

| **Região** | **Registo dNS** |
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

Para obter uma lista de endereços IP da região em vez de nomes de região, descarregue o ficheiro XML do [endereço IP do Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653) do Microsoft Download Center.

> [!NOTE]
> O ficheiro IP do Azure Datacenter XML lista as gamas de endereços IP que são utilizadas nos datacenters do Microsoft Azure. O ficheiro inclui gamas de cálculo, SQL e armazenamento.
>
>Um ficheiro atualizado é publicado semanalmente. O ficheiro reflete as gamas atualmente implantadas e quaisquer alterações futuras nas gamas IP. As novas gamas que aparecem no ficheiro não são usadas nos centros de dados durante pelo menos uma semana.
>
> É uma boa ideia descarregar o novo ficheiro XML todas as semanas. Em seguida, atualize o seu site para identificar corretamente os serviços em funcionamento no Azure. Os utilizadores do Azure ExpressRoute devem notar que este ficheiro é utilizado para atualizar o anúncio do Protocolo border gateway (BGP) do espaço Azure na primeira semana de cada mês.

## <a name="next-steps"></a>Passos seguintes

- Para começar, veja Começar com a Configuração do Estado da [Automação Azure](automation-dsc-getting-started.md)
- Para aprender a bordo de nós, consulte [máquinas de embarque para gestão pela Configuração do Estado da Automação Azure](automation-dsc-onboarding.md)
- Para aprender sobre a compilação de configurações de DSC para que possa atribuí-las a nós-alvo, consulte [Configurações de Compilação na Configuração do Estado da Automação Azure](automation-dsc-compile.md)
- Para referência de cmdlet PowerShell, consulte [Os cmdlets](/powershell/module/azurerm.automation/#automation) de configuração do Estado da Automatização Do Azure
- Para obter informações sobre preços, consulte os preços de configuração do Estado da [Automação Do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de utilização da Configuração do Estado da Automação Azure num pipeline de implantação contínua, consulte a implantação contínua utilizando a configuração do Estado da [Automação Azure e](automation-dsc-cd-chocolatey.md) o Chocolatey
