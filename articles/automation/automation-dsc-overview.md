---
title: Descrição geral da configuração de estado da automatização do Azure
description: Uma descrição geral do Azure automatização Estado Configuration (DSC), seus termos e problemas conhecidos
keywords: PowerShell dsc, configuração de estado pretendido, azure dsc de powershell
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a63155e65460f9e2193c121bb49849940104eff1
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649239"
---
# <a name="azure-automation-state-configuration-overview"></a>Descrição geral da configuração de estado da automatização do Azure

A configuração de estado de automatização do Azure é um serviço do Azure que permite-lhe escrever, gerir e compilar PowerShell Desired State Configuration (DSC) [configurações](/powershell/dsc/configurations), importar [recursos de DSC](/powershell/dsc/resources), e Atribua as configurações de nós de destino, tudo na cloud.

## <a name="why-use-azure-automation-state-configuration"></a>Por que usar a configuração de estado de automatização do Azure

Configuração de estado da automatização do Azure fornece várias vantagens com o uso de DSC fora do Azure.

### <a name="built-in-pull-server"></a>Servidor de solicitação incorporada

Configuração de estado da automatização do Azure fornece um servidor de solicitação de DSC semelhante para o [DSC-serviço do Windows funcionalidade](/powershell/dsc/pullserver) para que nós de destino recebem automaticamente as configurações, está em conformidade com o estado pretendido e comunicar de volta a seus conformidade. O servidor de solicitação incorporada na automatização do Azure elimina a necessidade de configurar e manter o seu próprio servidor de solicitação. A automatização do Azure pode visar máquinas físicas ou virtuais Windows ou Linux, na cloud ou no local.

### <a name="management-of-all-your-dsc-artifacts"></a>Gestão de todos os seus artefactos de DSC

A configuração de estado de automatização do Azure traz a mesma camada de gerenciamento para [PowerShell Desired State Configuration](/powershell/dsc/overview) como a automatização do Azure fornece para scripts do PowerShell.

No portal do Azure ou no PowerShell, pode gerir todos os seus DSC configurações, recursos e nós de destino.

![Captura de ecrã da página de automatização do Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Importar dados de relatórios para o Log Analytics

Nós que são geridos com a configuração de estado de automatização do Azure enviam dados de estado de geração de relatórios detalhados para o servidor de solicitação incorporada. Pode configurar a configuração de estado de automatização do Azure para enviar estes dados para a área de trabalho do Log Analytics. Para saber como enviar dados de estado de configuração de estado para a área de trabalho do Log Analytics, veja [reencaminhar Azure Automation configuração de estado dados de relatórios para o Log Analytics](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Pré-requisitos

Considere os seguintes requisitos ao utilizar a configuração de estado de automatização do Azure (DSC).

### <a name="operating-system-requirements"></a>Requisitos do sistema operativo

Para nós a executar o Windows, são suportadas as seguintes versões:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

Para nós em execução no Linux, são suportadas as seguintes distros/versões:

A extensão de DSC Linux suporta todas as distribuições de Linux [apoiadas no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) , exceto:

Distribuição | Versão
-|-
Debian  | Todas as versões
Ubuntu  | 18.04

### <a name="dsc-requirements"></a>Requisitos de DSC

Para todos os nós do Windows em execução no Azure, [WMF 5.1](https://docs.microsoft.com/powershell/wmf/5.1/install-configure) será instalado durante a integração.  Para nós que executam o Windows Server 2012 e Windows 7, [WinRM será ativado](https://docs.microsoft.com/powershell/dsc/troubleshooting/troubleshooting#winrm-dependency).

Para todos os nós do Linux em execução no Azure, [PowerShell DSC para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) será instalado durante a integração.

### <a name="network-planning"></a>Configurar redes privadas

Se os nós estão localizados numa rede privada, os seguintes URLs e portas são necessárias para o estado de configuração (DSC) para comunicar com a automatização:

* Porta: Apenas a TCP 443 é necessária para acesso de internet de saída.
* Global URL: *. Azure-Automation.NET
* URL global do US Gov Virgínia: *.azure automation.us
* Serviço de agente: https://\<workspaceId\>.agentsvc.azure-automation.net

É recomendado para utilizar os endereços listados quando definir exceções. Para endereços IP, pode baixar o [intervalos de IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Este ficheiro é atualizado semanalmente e tem os intervalos implementados atualmente e as alterações futuras para os intervalos de IP.

Se tiver uma conta de automatização que está definida para uma região específica, pode restringir a comunicação para esse datacenter regional. A tabela seguinte fornece o registo DNS para cada região:

| **Região** | **Registo de DNS** |
| --- | --- |
| EUA Centro-Oeste | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| EUA Centro-Sul |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| EUA Leste 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Canadá Central |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa Ocidental |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa do Norte |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Sudeste Asiático |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Índia Central |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Leste do Japão |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Sudeste da Austrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Reino Unido Sul | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| Gov (US) - Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Para obter uma lista de endereços IP da região em vez de nomes de região, transfira o [endereço IP do Datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653) arquivo XML do Microsoft Download Center.

> [!NOTE]
> O ficheiro XML de endereço IP do Datacenter do Azure apresenta uma lista de intervalos de endereços IP que são utilizados nos datacenters do Microsoft Azure. O arquivo inclui os intervalos de computação, SQL e armazenamento.
>
>Um ficheiro atualizado é publicado semanalmente. O ficheiro reflete os intervalos implementados atualmente e as alterações futuras para os intervalos de IP. Os novo intervalos que aparecem no ficheiro não são utilizados nos centros de dados para, pelo menos, uma semana.
>
> É uma boa idéia para transferir o ficheiro XML novo todas as semanas. Em seguida, atualize o seu site para identificar corretamente os serviços em execução no Azure. Os utilizadores do Azure ExpressRoute devem observar que este ficheiro é utilizado para atualizar o anúncio de protocolo BGP (Border Gateway) do espaço Azure na primeira semana de cada mês.

## <a name="introduction-video"></a>Vídeo de introdução

Prefere ver do que ler? Dê uma olhada no vídeo seguinte de Maio de 2015, quando configuração de estado de automatização do Azure foi anunciada pela primeira vez.

> [!NOTE]
> Embora os conceitos e o ciclo de vida descritos neste vídeo estiverem corretos, a configuração de estado de automatização do Azure tem progredido muito desde que este vídeo foi gravado. Agora está disponível em geral, tem uma interface do Usuário muito mais extensa no portal do Azure e oferece suporte a muitos recursos adicionais.

<iframe src="https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

## <a name="next-steps"></a>Passos Seguintes

- Para começar a utilizar, consulte o artigo [introdução à configuração de estado de automatização do Azure](automation-dsc-getting-started.md)
- Para saber como a carregar nós, consulte [integrar computadores para gestão de configuração de estado de automatização do Azure](automation-dsc-onboarding.md)
- Para obter informações sobre como compilar configurações de DSC para que pode atribuí-las a nós de destino, consulte [compilar configurações na configuração de estado de automatização do Azure](automation-dsc-compile.md)
- Para referência de cmdlets do PowerShell, consulte [cmdlets de configuração de estado de automatização do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [preços de configuração de estado de automatização do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo do uso de configuração de estado de automatização do Azure num pipeline de implementação contínua, consulte [contínua através do Azure Automation estado de configuração da implementação e Chocolatey](automation-dsc-cd-chocolatey.md)
