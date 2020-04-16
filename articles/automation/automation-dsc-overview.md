---
title: Visão geral da configuração do Estado da Automação Azure
description: Este artigo é uma visão geral da Configuração do Estado da Automação Azure (DSC), dos seus termos e das suas questões conhecidas.
keywords: powershell dsc, configuração de estado desejado, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1166f5a1d7586c54255120a656b060c93f842fd9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406187"
---
# <a name="state-configuration-overview"></a>Descrição geral da Configuração de Estado

A Configuração do Estado da Automação Azure é um serviço Azure que lhe permite escrever, gerir e compilar [configurações](/powershell/scripting/dsc/configurations/configurations)de Configuração do Estado Desejado seleções (DSC) da PowerShell . O serviço também importa [recursos DSC](/powershell/scripting/dsc/resources/resources) e atribui configurações para os nós alvo, tudo na nuvem.

## <a name="why-use-azure-automation-state-configuration"></a>Porquê utilizar o State Configuration da Automatização do Azure?

A Configuração do Estado da Automação Azure oferece várias vantagens em utilizar o DSC fora do Azure.

### <a name="built-in-pull-server"></a>Servidor de puxar incorporado

A Configuração do Estado da Automação Azure fornece um servidor de puxar DSC semelhante ao [Serviço DSC da funcionalidade Windows](/powershell/scripting/dsc/pull-server/pullserver). Os nós-alvo podem receber automaticamente configurações, estar em conformidade com o estado desejado e reportar sobre a sua conformidade. O servidor de pull incorporado na Azure Automation elimina a necessidade de configurar e manter o seu próprio servidor de puxar. A Azure Automation pode visar máquinas virtual ou físicas de Windows ou Linux, na nuvem ou no local.

### <a name="manage-all-your-dsc-artifacts"></a>Gerencie todos os seus artefactos DSC

A Configuração do Estado da Automação Azure traz a mesma camada de gestão para a [Configuração do Estado Desejada pela PowerShell,](/powershell/scripting/dsc/overview/overview) que oferece para scripts PowerShell. A partir do portal Azure ou da PowerShell, pode gerir todas as suas configurações, recursos e nós alvo.

![Screenshot da página de Automação Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Dados de relatórios de importação em Registos do Monitor Azure

Os nós que são geridos com a Configuração do Estado da Automatização Azure enviam dados de estado de reporte detalhados para o servidor de pull incorporado. Pode configurar a Configuração do Estado de Automação Azure para enviar estes dados para o seu espaço de trabalho Log Analytics. Para mais informações, consulte forward [Azure Automation State Configuration reportando dados aos registos do Monitor Do Azure](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Pré-requisitos

Considere os seguintes requisitos quando utilizar a Configuração do Estado da Automação Azure para DSC.

### <a name="operating-system-requirements"></a>Requisitos do sistema operativo

Para os nós que executam o Windows, as seguintes versões são suportadas:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>Uma vez que o produto autónomo Do [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) SKU não contém uma implementação do DSC, não pode ser gerido pela PowerShell DSC ou pela Configuração do Estado de Automação Azure.

Para os nós que executam o Linux, a extensão DSC Linux suporta todas as distribuições Linux listadas no âmbito da [SupportEd Linux Distributions](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>Requisitos dSC

Para todos os nós windows em funcionamento em Azure, o [Windows Management Framework 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) é instalado durante o embarque. Para os nós que executam o Windows Server 2012 e o Windows 7, o [WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) está ativado.

Para todos os nós Linux em execução em Azure, o [PowerShell DSC para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) é instalado durante o embarque.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Configuração de redes privadas

Se os seus nós estiverem localizados numa rede privada, são necessários os seguintes portes e URLs. Estes recursos proporcionam conectividade de rede para o nó gerido e permitem à DSC comunicar com a Azure Automation.

* Porta: Apenas TCP 443 necessário para acesso à Internet de saída
* URL global: ***.azure-automation.net**
* URL global dos EUA Gov Virginia: ***.azure-automation.us**
* Serviço de agente: **https://\<workspaceId\>.agentsvc.azure-automation.net**

Se estiver a utilizar recursos DSC que comunicam entre nós, como os [recursos WaitFor**](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), também precisa de permitir a comunicação entre nós. Para compreender estes requisitos de rede, consulte a documentação para cada recurso dSC.

#### <a name="proxy-support"></a>Apoio proxy

O suporte proxy para o agente DSC está disponível na versão 1809 do Windows e posteriormente. Habilita esta opção definindo `ProxyURL` `ProxyCredential` os valores para e no script de [configuração](automation-dsc-onboarding.md#generate-dsc-metaconfigurations) que é usado para registar nós.

>[!NOTE]
>A Configuração do Estado da Automação Azure não fornece suporte a procuração DSC para versões anteriores do Windows.

Para os nós linux, o agente DSC suporta `http_proxy` um servidor proxy e utiliza a variável para especificar o URL.

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Gamas de gamas e espaço de configuração do Estado da Automatização Azure

Quando estiver a definir exceções, recomendamos que utilize os endereços IP listados na tabela seguinte. Para endereços IP, pode descarregar o ficheiro Ip Ranges XML do [Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653) do Microsoft Download Center. Este ficheiro contém as gamas atualmente implantadas e quaisquer alterações futuras nas gamas IP. É atualizado semanalmente.

Se tiver uma conta de Automação definida para uma região específica, pode restringir a comunicação a esse datacenter regional. O quadro seguinte fornece o registo dNS para cada região:

| **Região** | **Registo dNS** |
| --- | --- |
| E.U.A. Centro-Oeste | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| E.U.A. Centro-Sul |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| E.U.A. Leste    | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| E.U.A. Leste 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Canadá Central |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa ocidental |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa do Norte |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Ásia Sudeste |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Índia Central |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Leste do Japão |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Sudeste da Austrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Sul do Reino Unido | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov - Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

> [!NOTE]
> O ficheiro IP do Azure Datacenter XML lista as gamas de endereços IP que são utilizadas nos datacenters do Microsoft Azure. O ficheiro inclui gamas de cálculo, SQL e armazenamento.
>
>Um ficheiro atualizado é publicado semanalmente. O ficheiro reflete as gamas atualmente implantadas e quaisquer alterações futuras nas gamas IP. As novas gamas que aparecem no ficheiro não são usadas nos centros de dados durante pelo menos uma semana. É uma boa ideia descarregar um novo ficheiro XML todas as semanas. Em seguida, pode atualizar o seu site para identificar corretamente os serviços em funcionamento no Azure. 

Se for um utilizador do Azure ExpressRoute, note que este ficheiro é utilizado para atualizar o anúncio do Border Gateway Protocol (BGP) do espaço Azure na primeira semana de cada mês.

## <a name="next-steps"></a>Passos seguintes

- Para começar a usar o DSC na Configuração do Estado da Automação Azure, consulte [Iniciar-se com a Configuração do Estado da Automação Azure](automation-dsc-getting-started.md).
- Para aprender a bordo dos nós, consulte [máquinas onboard para gestão pela Configuração do Estado da Automação Azure](automation-dsc-onboarding.md).
- Para aprender sobre a compilação de configurações de DSC para que possa atribuí-las a nós-alvo, consulte [configurações de Compile na Configuração do Estado da Automação Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Para obter informações sobre preços, consulte os preços de configuração do Estado da [Automatização do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para um exemplo de utilização da Configuração do Estado da Automação Azure num gasoduto de implantação contínua, consulte a [implantação contínua de máquinas virtuais utilizando a Configuração do Estado da Automação Azure e o Chocolatey.](automation-dsc-cd-chocolatey.md)
