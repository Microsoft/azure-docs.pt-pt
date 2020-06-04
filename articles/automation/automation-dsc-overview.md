---
title: Visão geral da configuração do estado da Azure Automation
description: Este artigo fornece uma visão geral da Configuração do Estado da Automação Azure.
keywords: powershell dsc, configuração de estado desejado, powershell dsc azul
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 06/03/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d2109baf077b1b4c1074cfae9edd0d2b5ef5030d
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84343008"
---
# <a name="azure-automation-state-configuration-overview"></a>Visão geral da configuração do estado da Azure Automation

A Azure Automation State Configuration é um serviço de gestão de configuração Azure que lhe permite escrever, gerir e compilar [configurações](/powershell/scripting/dsc/configurations/configurations) de Configuração do Estado (DSC) de PowerShell Desired State para nós em qualquer datacenter de nuvem ou no local. O serviço também importa [recursos DSC,](/powershell/scripting/dsc/resources/resources)e atribui configurações para os nós alvo, tudo na nuvem. Pode aceder à Configuração do Estado da Automação Azure no portal Azure selecionando a **configuração do Estado (DSC)** em **Gestão de Configuração**. 

Pode utilizar a Configuração do Estado da Automação Azure para gerir uma variedade de máquinas:

- Máquinas virtuais do Azure
- Máquinas virtuais Azure (clássicas)
- Máquinas de Windows físicas/virtuais no local, ou numa nuvem que não seja a Azure (incluindo instâncias AWS EC2)
- Máquinas Linux físicas/virtuais no local, em Azure, ou numa nuvem que não seja a Azure

Se não estiver pronto para gerir a configuração da máquina a partir da nuvem, pode utilizar a Configuração do Estado da Automação Azure como ponto final apenas de relatório. Esta funcionalidade permite-lhe definir configurações (push) através de DSC e ver detalhes de reporte na Azure Automation.

> [!NOTE]
> A gestão de VMs Azure com Configuração do Estado da Automação Azure está incluída sem custos adicionais se a versão instalada de extensão de configuração do estado Azure VM desejada for superior a 2.70. Para mais informações, consulte [**a página de preços da Automação.**](https://azure.microsoft.com/pricing/details/automation/)

## <a name="why-use-azure-automation-state-configuration"></a>Porquê usar a configuração do Estado da Automação Azure

A Azure Automation State Configuration proporciona várias vantagens sobre a utilização de DSC fora de Azure. Este serviço permite a escalabilidade em milhares de máquinas de forma rápida e fácil a partir de uma localização central e segura. Pode ativar facilmente as máquinas, atribuí-las configurações declarativas e ver relatórios que mostrem a conformidade de cada máquina com o estado pretendido especificado.

O serviço de Configuração estatal Azure Automation é para dSC o que os runbooks da Azure Automation são para scripts PowerShell. Por outras palavras, da mesma forma que a Azure Automation ajuda a gerir scripts PowerShell, também ajuda a gerir as configurações do DSC. 

### <a name="built-in-pull-server"></a>Servidor de puxar incorporado

A Azure Automation State Configuration fornece um servidor de puxar DSC semelhante ao [Serviço DSC do Windows Feature](/powershell/scripting/dsc/pull-server/pullserver). Os nós-alvo podem receber automaticamente configurações, conformes com o estado pretendido e informar sobre a sua conformidade. O servidor de puxar incorporado na Azure Automation elimina a necessidade de configurar e manter o seu próprio servidor de puxar. A Azure Automation pode visar máquinas virtuais ou físicas de Windows ou Linux, na nuvem ou no local.

### <a name="management-of-all-your-dsc-artifacts"></a>Gestão de todos os seus artefactos DSC

A Azure Automation State Configuration traz a mesma camada de gestão para a [Configuração de Estado Desejada powerShell](/powershell/scripting/dsc/overview/overview) que oferece para scripts PowerShell. A partir do portal Azure ou a partir de PowerShell, pode gerir todas as suas configurações, recursos e nós de alvo.

![Screenshot da página Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Importação de dados de reporte em registos do Monitor Azure

Os nós que são geridos com a Configuração do Estado da Automação Azure enviam dados detalhados do estado de reporte para o servidor de puxar incorporado. Pode configurar a Configuração do Estado da Automação Azure para enviar estes dados para o seu espaço de trabalho Log Analytics. Consulte [a configuração do Estado da Automação forward Azure reportando dados para registos do Monitor Azure](automation-dsc-diagnostics.md).

## <a name="prerequisites-for-using-azure-automation-state-configuration"></a>Pré-requisitos para a utilização da Configuração do Estado da Automação Azure

Considere os requisitos nesta secção ao utilizar a Configuração do Estado da Automação Azure.

### <a name="operating-system-requirements"></a>Requisitos do sistema operativo

Para os nóns que executam o Windows, as seguintes versões são suportadas:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>O produto autónomo do [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) SKU não contém uma implementação de DSC. Assim, não pode ser gerido pela PowerShell DSC ou pela Azure Automation State Configuration.

Para os nós que executam o Linux, a extensão DSC Linux suporta todas as distribuições Linux listadas nas [distribuições de Linux suportadas.](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions)

### <a name="dsc-requirements"></a>Requisitos de DSC

Para todos os nós Windows em execução em Azure, [o WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) é instalado quando as máquinas estão ativadas. Para os nós que executam o Windows Server 2012 e o Windows 7, [o WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) está ativado.

Para todos os nós Linux em execução em Azure, [o PowerShell DSC para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) é instalado quando as máquinas estão ativadas.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Configuração de redes privadas

Se os seus nós estiverem localizados numa rede privada, são necessários os seguintes portos e URLs. Estes recursos fornecem conectividade de rede para o nó gerido e permitem que a DSC comunique com a Azure Automation.

* Porto: Apenas TCP 443 necessário para acesso à Internet de saída
* URL global: ***.azure-automation.net**
* URL global dos EUA Gov Virginia: ***.azure-automation.us**
* Serviço de agente: **https:// \<workspaceId\> .agentsvc.azure-automation.net**

Se estiver a utilizar recursos DSC que comunicam entre nós, como os [recursos WaitFor*,](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)também precisa de permitir o tráfego entre nós. Consulte a documentação de cada recurso DSC para compreender estes requisitos de rede.

Para compreender os requisitos do cliente para tLS 1.2, consulte [a aplicação TLS 1.2 para a Azure Automation](automation-managing-data.md#tls-12-enforcement-for-azure-automation).

#### <a name="proxy-support"></a>Apoio proxy

O suporte proxy para o agente DSC está disponível na versão 1809 do Windows e posteriormente. Esta opção é ativada através da definição dos valores `ProxyURL` e propriedades no script de `ProxyCredential` [metaconfiguration](automation-dsc-onboarding.md#generate-dsc-metaconfigurations) utilizado para registar nós. 

>[!NOTE]
>A Azure Automation State Configuration não fornece suporte de procuração DSC para versões anteriores do Windows.

Para os nós Linux, o agente DSC suporta o proxy e utiliza a `http_proxy` variável para determinar o URL. Para saber mais sobre o suporte a procuração, consulte [gerar metaconfiguções de DSC](automation-dsc-onboarding.md#generate-dsc-metaconfigurations).

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Gamas de rede de configuração do estado de Azure Automation e espaço de nome

Recomenda-se a utilização dos endereços listados abaixo na definição de exceções. Para endereços IP, pode descarregar os intervalos IP do [Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Este ficheiro é atualizado semanalmente e tem as gamas atualmente implementadas e quaisquer alterações futuras nas gamas IP.

Se tiver uma conta Automation que está definida para uma região específica, pode restringir a comunicação a esse datacenter regional. A tabela seguinte fornece o registo de DNS para cada região:

| **Região** | **Registo de DNS** |
| --- | --- |
| E.U.A. Centro-Oeste | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| E.U.A. Centro-Sul |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| E.U.A. Leste    | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
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

Para obter uma lista de endereços IP da região em vez de nomes de região, descarregue o ficheiro XML do endereço IP do [Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653) do Microsoft Download Center.

> [!NOTE]
> O endereço IP do Azure Datacenter XML lista os intervalos de endereços IP que são utilizados nos datacenters do Microsoft Azure. O ficheiro inclui gamas de cálculo, SQL e armazenamento.
>
>Um ficheiro atualizado é publicado semanalmente. O ficheiro reflete as gamas atualmente implementadas e quaisquer alterações futuras nas gamas IP. As novas gamas que aparecem no ficheiro não são utilizadas nos datacenters durante pelo menos uma semana. É uma boa ideia descarregar o novo ficheiro XML todas as semanas. Em seguida, atualize o seu site para identificar corretamente os serviços em execução em Azure. 

Os utilizadores do Azure ExpressRoute devem ter em conta que este ficheiro é utilizado para atualizar o anúncio do Espaço Azure (Border Gateway Protocol) na primeira semana de cada mês.

## <a name="next-steps"></a>Próximos passos

- Para começar, consulte [Começar com a Configuração do Estado da Automação Azure](automation-dsc-getting-started.md).
- Para aprender a ativar os nós, consulte [Ativar a Configuração do Estado da Automação Azure](automation-dsc-onboarding.md).
- Para saber sobre a compilação de configurações de DSC para que possa atribuí-las aos nós-alvo, consulte [as configurações do Compile DSC na Configuração do Estado da Automação Azure](automation-dsc-compile.md).
- Para ver um exemplo da utilização da Configuração do Estado da Automação Azure num gasoduto de implantação contínua, consulte [Configurar uma implementação contínua com chocolateria](automation-dsc-cd-chocolatey.md).
- Para obter informações sobre preços, consulte [os preços de configuração do Estado da Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).