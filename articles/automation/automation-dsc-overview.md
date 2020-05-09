---
title: Visão geral da configuração do Estado da Automação Azure
description: Uma visão geral da Configuração do Estado da Automação Azure, seus termos e questões conhecidas
keywords: powershell dsc, configuração de estado desejado, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dbe617e6614eb69f0a7f6e31c89c1f645804fe1b
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82993857"
---
# <a name="azure-automation-state-configuration-overview"></a>Visão geral da configuração do Estado da Automação Azure

A Configuração do Estado da Automação Azure é um serviço de gestão de configuração de [configurations](/powershell/scripting/dsc/configurations/configurations) configuração de configuração de configuração de configuração de configuração de estado de configuração do Estado de PowerShell (DSC) para nós em qualquer nuvem ou centro de dados no local. O serviço também importa [recursos DSC](/powershell/scripting/dsc/resources/resources), e atribui configurações para os nós alvo, tudo na nuvem. Pode aceder à Configuração do Estado da Automação Azure no portal Azure selecionando a **configuração do Estado (DSC)** em gestão de **configuração**. 

Pode utilizar a Configuração do Estado da Automação Azure para gerir uma variedade de máquinas:

- Máquinas virtuais do Azure
- Máquinas virtuais azure (clássica)
- Máquinas de Janelas físicas/virtuais no local, ou numa nuvem diferente de Azure (incluindo instâncias AWS EC2)
- Máquinas linux físicas/virtuais no local, em Azure, ou numa nuvem que não o Azure

Se não estiver pronto para gerir a configuração da máquina a partir da nuvem, pode utilizar a Configuração do Estado da Automatização Azure como ponto final apenas de reporte. Esta funcionalidade permite-lhe definir configurações (push) através do DSC e visualizar detalhes de reporte na Automação Azure.

> [!NOTE]
> Gerir VMs Azure com Configuração do Estado de Automação Azure não está incluído a custo extra se a versão instalada de extensão de configuração do Estado Do Estado Do Azure VM desired for superior a 2.70. Para mais informações, consulte [**a página de preços da Automação**](https://azure.microsoft.com/pricing/details/automation/).

## <a name="why-use-azure-automation-state-configuration"></a>Porquê usar a configuração do Estado da Automação Azure

A Configuração do Estado da Automação Azure proporciona várias vantagens sobre a utilização de DSC fora do Azure. Este serviço permite a escalabilidade através de milhares de máquinas de forma rápida e fácil a partir de uma localização central e segura. Pode ativar facilmente as máquinas, atribuir-lhes configurações declarativas e visualizar relatórios que mostrem a conformidade de cada máquina com o estado pretendido que especifique.

O serviço de Configuração do Estado da Automação Azure é para a DSC quais os livros de execução da Automação Azure para a escrita PowerShell. Por outras palavras, da mesma forma que a Automatização Azure ajuda a gerir os scripts PowerShell, também o ajuda a gerir as configurações do DSC. 

### <a name="built-in-pull-server"></a>Servidor de puxar incorporado

A Configuração do Estado da Automação Azure fornece um servidor de puxar DSC semelhante ao [Serviço DSC da funcionalidade Windows](/powershell/scripting/dsc/pull-server/pullserver). Os nós-alvo podem receber automaticamente configurações, estar em conformidade com o estado desejado e reportar sobre a sua conformidade. O servidor de pull incorporado na Azure Automation elimina a necessidade de configurar e manter o seu próprio servidor de puxar. A Azure Automation pode visar máquinas virtual ou físicas de Windows ou Linux, na nuvem ou no local.

### <a name="management-of-all-your-dsc-artifacts"></a>Gestão de todos os seus artefactos DSC

A Configuração do Estado da Automação Azure traz a mesma camada de gestão para a [Configuração do Estado Desejada pela PowerShell,](/powershell/scripting/dsc/overview/overview) que oferece para scripts PowerShell. A partir do portal Azure ou da PowerShell, pode gerir todas as suas configurações, recursos e nós alvo.

![Screenshot da página de Automação Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Importação de dados de reporte em registos do Monitor Do Azure

Os nós que são geridos com a Configuração do Estado da Automatização Azure enviam dados de estado de reporte detalhados para o servidor de pull incorporado. Pode configurar a Configuração do Estado de Automação Azure para enviar estes dados para o seu espaço de trabalho Log Analytics. Consulte os dados de informação de informação de configuração do Estado da [Automatização Do Azure Forward para os registos do Monitor Azure](automation-dsc-diagnostics.md).

## <a name="prerequisites-for-using-azure-automation-state-configuration"></a>Pré-requisitos para a utilização da configuração do Estado da Automação Azure

Considere os requisitos nesta secção ao utilizar a Configuração do Estado da Automação Azure.

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
>O produto autónomo Do [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) SKU não contém uma implementação do DSC. Assim, não pode ser gerido pela PowerShell DSC ou pela Configuração do Estado da Automação Azure.

Para os nós que executam o Linux, a extensão DSC Linux suporta todas as distribuições Linux listadas no âmbito da [SupportEd Linux Distributions](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>Requisitos dSC

Para todos os nós do Windows em funcionamento em Azure, [o WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) é instalado quando as máquinas estão ativadas. Para os nós que executam o Windows Server 2012 e o Windows 7, o [WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) está ativado.

Para todos os nós Linux em funcionamento em Azure, o [PowerShell DSC para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) é instalado quando as máquinas estão ativadas.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Configuração de redes privadas

Se os seus nós estiverem localizados numa rede privada, são necessários os seguintes portes e URLs. Estes recursos proporcionam conectividade de rede para o nó gerido e permitem à DSC comunicar com a Azure Automation.

* Porta: Apenas TCP 443 necessário para acesso à Internet de saída
* URL global: ***.azure-automation.net**
* URL global dos EUA Gov Virginia: ***.azure-automation.us**
* Serviço de agente: **https://\<workspaceId\>.agentsvc.azure-automation.net**

Se estiver a utilizar recursos DSC que comunicam entre nós, como os [recursos WaitFor**](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), também precisa de permitir o tráfego entre nós. Consulte a documentação de cada recurso dSC para compreender estes requisitos de rede.

#### <a name="proxy-support"></a>Apoio proxy

O suporte proxy para o agente DSC está disponível na versão 1809 do Windows e posteriormente. Esta opção é ativada `ProxyURL` definindo os valores para e `ProxyCredential` no script de [configuração utilizado](automation-dsc-onboarding.md#generate-dsc-metaconfigurations) para registar nós.

>[!NOTE]
>A Configuração do Estado da Automação Azure não fornece suporte de procuração DSC para versões anteriores do Windows.

Para os nós linux, o agente DSC `http_proxy` suporta procuração e utiliza a variável para determinar o URL.

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Gamas de gamas e espaço de configuração do Estado da Automatização Azure

É aconselhável utilizar os endereços listados abaixo ao definir exceções. Para endereços IP, pode descarregar os intervalos IP do [Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Este ficheiro é atualizado semanalmente, e tem as gamas atualmente implementadas e quaisquer alterações futuras nas gamas IP.

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

Para obter uma lista de endereços IP da região em vez de nomes de região, descarregue o ficheiro XML do [endereço IP do Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653) do Microsoft Download Center.

> [!NOTE]
> O ficheiro IP do Azure Datacenter XML lista as gamas de endereços IP que são utilizadas nos datacenters do Microsoft Azure. O ficheiro inclui gamas de cálculo, SQL e armazenamento.
>
>Um ficheiro atualizado é publicado semanalmente. O ficheiro reflete as gamas atualmente implantadas e quaisquer alterações futuras nas gamas IP. As novas gamas que aparecem no ficheiro não são usadas nos centros de dados durante pelo menos uma semana. É uma boa ideia descarregar o novo ficheiro XML todas as semanas. Em seguida, atualize o seu site para identificar corretamente os serviços em funcionamento em Azure. 

Os utilizadores do Azure ExpressRoute devem notar que este ficheiro é utilizado para atualizar o anúncio do Protocolo border gateway (BGP) do espaço Azure na primeira semana de cada mês.

## <a name="next-steps"></a>Passos seguintes

- Para começar a usar o DSC na Configuração do Estado da Automação Azure, consulte Começar com a Configuração do Estado da [Automação Azure](automation-dsc-getting-started.md).
- Para aprender a ativar os nós, consulte [máquinas Ativas para gestão pela Configuração do Estado da Automação Azure](automation-dsc-onboarding.md).
- Para aprender sobre a compilação de configurações de DSC para que possa atribuí-las a nós-alvo, consulte [configurações de compilação na Configuração do Estado da Automação Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Para obter informações sobre preços, consulte os preços de configuração do Estado da [Automatização do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para ver um exemplo de utilização da Configuração do Estado da Automação Azure num gasoduto de implantação contínua, consulte a implantação contínua utilizando a Configuração do Estado da [Automação Azure e](automation-dsc-cd-chocolatey.md)o Chocolatey .
