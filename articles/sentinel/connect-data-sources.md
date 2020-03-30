---
title: Ligue fontes de dados ao Azure Sentinel Microsoft Docs
description: Saiba como ligar fontes de dados ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: angrobe
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: yelevin
ms.openlocfilehash: 9d2d1985b23e1c7f5e0f7d9fd2795bd85e28ace0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240087"
---
# <a name="connect-data-sources"></a>Ligar a origens de dados

Para embarcar no Azure Sentinel, primeiro é necessário ligar-se às suas fontes de dados. O Azure Sentinel vem com uma série de conectores para soluções Microsoft, disponíveis fora da caixa e proporcionando integração em tempo real, incluindo soluções de Proteção contra Ameaças da Microsoft, e fontes Microsoft 365, incluindo Office 365, Azure AD, Azure ATP, e Microsoft Cloud App Security, e muito mais. Além disso, existem conectores incorporados para o ecossistema de segurança mais amplo para soluções não Microsoft. Também pode utilizar o formato comum do evento, o Syslog ou o REST-API para ligar as suas fontes de dados ao Azure Sentinel.  

1. No menu, selecione **Conectores de Dados**. Esta página permite-lhe ver a lista completa de conectores que o Azure Sentinel fornece e o seu estado. Selecione o conector que pretende ligar e selecione página do **conector Open**. 

   ![Colecionadores de dados](./media/collect-data/collect-data-page.png)

1. Na página de conector específico, certifique-se de que preencheu todos os pré-requisitos e siga as instruções para ligar os dados ao Azure Sentinel. Pode levar algum tempo para os registos começarem a sincronizar com o Azure Sentinel. Depois de se ligar, vê um resumo dos dados no gráfico **recebido** e o estado de conectividade dos tipos de dados.

   ![Conectar colecionadores](./media/collect-data/opened-connector-page.png)
  
1. Clique no separador **De passos Seguintes** para obter uma lista de conteúdos fora da caixa que o Azure Sentinel fornece para o tipo de dados específico.

   ![Colecionadores de dados](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Métodos de ligação de dados

Os seguintes métodos de ligação de dados são suportados pelo Azure Sentinel:

- **Serviço à integração de serviços:**<br> Alguns serviços estão ligados de forma nativa, como os serviços AWS e Microsoft, estes serviços alavancam a fundação Azure para integração fora da caixa, as seguintes soluções podem ser conectadas em alguns cliques:
    - [Amazon Web Services - CloudTrail](connect-aws.md)
    - [Atividade Azure](connect-azure-activity.md)
    - [Registos de auditoria da AD Azure e inscrições](connect-azure-active-directory.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Proteção Avançada Contra Ameaças do Azure](connect-azure-atp.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Centro de Segurança do Azure](connect-azure-security-center.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Servidor de nomes de domínio](connect-dns.md)
    - [Office 365](connect-office-365.md)
    - [Microsoft Defender ATP](connect-microsoft-defender-advanced-threat-protection.md)
    - [Firewall de aplicações Web da Microsoft](connect-microsoft-waf.md)
    - [Firewall do Windows](connect-windows-firewall.md)
    - [Eventos de segurança do Windows](connect-windows-security-events.md)

- **Soluções externas via API**: Algumas fontes de dados estão ligadas utilizando APIs fornecidas pela fonte de dados conectada. Tipicamente, a maioria das tecnologias de segurança fornecem um conjunto de APIs através dos quais os registos de eventos podem ser recuperados. As APIs ligam-se ao Azure Sentinel e recolhem tipos de dados específicos e enviam-nos para o Azure Log Analytics. Os aparelhos ligados via API incluem:
    - [Barracuda](connect-barracuda.md)
    - [Barracuda CloudGen Firewall](connect-barracuda-cloudgen-firewall.md)
    - [Citrix Analytics (Segurança)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [ICDX da Symantec](connect-symantec.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **Soluções externas via agente**: O Azure Sentinel pode ser ligado a todas as outras fontes de dados que possam realizar o streaming de log em tempo real utilizando o protocolo Syslog, através de um agente. <br>A maioria dos aparelhos utiliza o protocolo Syslog para enviar mensagens de eventos que incluem o próprio log e dados sobre o registo. O formato dos registos varia, mas a maioria dos aparelhos suporta mato baseado em eventos comuns (CEF) para dados de registos. <br>O agente Azure Sentinel, que se baseia no agente Log Analytics, converte os registos formados CEF num formato que pode ser ingerido pelo Log Analytics. Dependendo do tipo de aparelho, o agente é instalado diretamente no aparelho, quer num servidor Linux dedicado. O agente da Linux recebe eventos do daemon Syslog sobre a UDP, mas se se espera que uma máquina Linux recolha um grande volume de eventos Syslog, eles são enviados através do TCP do daemon Syslog para o agente e de lá para log Analytics.
    - Firewalls, proxies e pontos finais:
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [ExtraHop Reveal(x)](connect-extrahop.md)
        - [F5](connect-f5.md)
        - [Produtos da Forcepoint](connect-forcepoint-casb-ngfw.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto Networks](connect-paloalto.md)
        - [One Identity Safeguard](connect-one-identity.md)
        - [Outros aparelhos CEF](connect-common-event-format.md)
        - [Outros aparelhos Syslog](connect-syslog.md)
        - [Trend Micro Deep Security](connect-trend-micro.md)
        - [Zscaler](connect-zscaler.md)
    - Soluções DLP
    - [Fornecedores de inteligência de ameaças](connect-threat-intelligence.md)
    - [Máquinas DNS](connect-dns.md) - agente instalado diretamente na máquina DNS
    - Servidores Linux
    - Outras nuvens
    
## <a name="agent-connection-options"></a>Opções de ligação do agente<a name="agent-options"></a>

Para ligar o seu aparelho externo ao Azure Sentinel, o agente deve ser implantado numa máquina dedicada (VM ou no local) para apoiar a comunicação entre o aparelho e o Azure Sentinel. Pode implantar o agente automaticamente ou manualmente. A implementação automática só está disponível se a sua máquina dedicada for um novo VM que está a criar no Azure. 


![CEF em Azure](./media/connect-cef/cef-syslog-azure.png)

Em alternativa, pode implantar o agente manualmente num VM Azure existente, num VM noutra nuvem ou numa máquina no local.

![CEF nas instalações](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Mapear tipos de dados com as opções de ligação do Azure Sentinel


| **Tipo de dados** | **Como ligar** | **Conector de dados?** | **Comentários** |
|------|---------|-------------|------|
| AWSCloudTrail | [Ligar AWS](connect-aws.md) | V | |
| AzureActivity | [Conecte](connect-azure-activity.md) a visão geral dos [registos](../azure-monitor/platform/platform-logs-overview.md) de atividade e atividade do Azure| V | |
| Registos de Auditoria | [Ligar ao Azure AD](connect-azure-active-directory.md)  | V | |
| SigninLogs | [Ligar ao Azure AD](connect-azure-active-directory.md)  | V | |
| AzureFirewall |[Diagnóstico do Azure](../firewall/tutorial-diagnostics.md) | V | |
| InformationProtectionLogs_CL  | [Relatórios de Proteção de Informação azure](https://docs.microsoft.com/azure/information-protection/reports-aip)<br>[Ligar ao Azure Information Protection](connect-azure-information-protection.md)  | V | Isto normalmente utiliza a função **InformationProtectionEvents** para além do tipo de dados. Para mais informações, consulte [Como modificar os relatórios e criar consultas personalizadas](https://docs.microsoft.com/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)|
| AzureNetworkAnalytics_CL  | Análise de [tráfego](../network-watcher/traffic-analytics.md) [analítico esquemaa](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog  | [Ligar CEF](connect-common-event-format.md)  | V | |
| Atividade do Escritório | [Ligar ao Office 365](connect-office-365.md) | V | |
| Eventos de Segurança | [Ligar aos eventos de segurança do Windows](connect-windows-security-events.md)  | V | Para os livros de trabalho inseguros protocolos, consulte a configuração do livro de [protocolos inseguros](/azure/sentinel/quickstart-get-visibility#use-built-in-workbooks)  |
| Syslog | [Ligar a Syslog](connect-syslog.md) | V | |
| Firewall de aplicação web da Microsoft (WAF) - (AzureDiagnostics) |[Conecte firewall de aplicação web da Microsoft](connect-microsoft-waf.md) | V | |
| SymantecICDx_CL | [Ligar symantec](connect-symantec.md) | V | |
| Indicador de Inteligência de Ameaças  | [Ligar às informações sobre ameaças](connect-threat-intelligence.md)  | V | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Mapa de serviço do Monitor Azure](../azure-monitor/insights/service-map.md)<br>[Azure Monitor VM insights onboarding](../azure-monitor/insights/vminsights-onboard.md) <br> [Ativar insights vM do Monitor Azure](../azure-monitor/insights/vminsights-enable-overview.md) <br> [Utilização de um único VM no embarque](../azure-monitor/insights/vminsights-enable-single-vm.md)<br>  [Utilização de embarque via política](../azure-monitor/insights/vminsights-enable-at-scale-policy.md)| X | Livro de insights VM  |
| DnsEvents | [Ligar DNS](connect-dns.md) | V | |
| W3CIISLog | [Ligar registos IIS](../azure-monitor/platform/data-sources-iis-logs.md)  | X | |
| WireData | [Ligar dados de arame](../azure-monitor/insights/wire-data.md) | X | |
| WindowsFirewall | [Ligar firewall do Windows](connect-windows-firewall.md) | V | |
| Alerta de segurança aADIP  | [Ligar ao Azure AD Identity Protection](connect-azure-ad-identity-protection.md)  | V | |
| Alerta de segurança AATP  | [Ligar ao Azure ATP](connect-azure-atp.md) | V | |
| Alerta de Segurança ASC  | [Ligar ao Centro de Segurança do Azure](connect-azure-security-center.md)  | V | |
| Alerta de Segurança MCAS  | [Conecte a segurança da aplicação microsoft cloud](connect-cloud-app-security.md)  | V | |
| SecurityAlert | | | |
| Sysmon (Evento) | [Conecte Sysmon](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Conectar eventos do Windows](../azure-monitor/platform/data-sources-windows-events.md) <br> [Pegue o Sysmon Parser](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/SysmonParser.txt)| X | A coleção Sysmon não é instalada por padrão em máquinas virtuais. Para obter mais informações sobre como instalar o Agente Sysmon, consulte [Sysmon](https://docs.microsoft.com/sysinternals/downloads/sysmon). |
| ConfiguraçãoData  | [Automatizar inventário VM](../automation/automation-vm-inventory.md)| X | |
| ConfiguraçãoChange  | [Automatizar o rastreio vm](../automation/change-tracking.md) | X | |
| F5 BIG-IP | [Ligar a F5 BIG-IP](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | X | |
| McasShadowItReporting  |  | X | |
| Barracuda_CL | [Ligar a Barracuda](connect-barracuda.md) | V | |


## <a name="next-steps"></a>Passos seguintes

- Para começar com o Azure Sentinel, precisa de uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/free/).
- Aprenda a [bordo dos seus dados para o Azure Sentinel](quickstart-onboard.md)e obtenha visibilidade nos seus dados e ameaças [potenciais.](quickstart-get-visibility.md)
