---
title: Ligue fontes de dados ao | Azure Sentinel Microsoft Docs
description: Saiba como ligar fontes de dados como o Microsoft 365 Defender (anteriormente Microsoft Threat Protection), Microsoft 365 e Office 365, Azure AD, ATP e Cloud App Security ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: aeb92dce41c12ada41175964835d9c89a634a6e3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590262"
---
# <a name="connect-data-sources"></a>Ligar a origens de dados

Depois de ter ativado o Azure Sentinel, a primeira coisa que precisa de fazer é ligar as suas fontes de dados. O Azure Sentinel vem com uma série de conectores para soluções Microsoft, disponíveis fora da caixa e fornecendo integração em tempo real, incluindo soluções Microsoft 365 Defender (anteriormente Microsoft Threat Protection), Microsoft 365 sources (incluindo Office 365), Azure AD, Microsoft Defender for Identity (anteriormente Azure ATP), Microsoft Cloud App Security, entre outras. Além disso, existem conectores incorporados no ecossistema de segurança mais amplo para soluções não Microsoft. Também pode utilizar o Formato Comum de Eventos (CEF), Syslog ou REST-API para ligar as suas fontes de dados ao Azure Sentinel.

1. No menu, selecione **Conectores de dados.** Esta página permite-lhe ver a lista completa de conectores que o Azure Sentinel fornece e o seu estado. Selecione o conector que pretende ligar e selecione **a página do conector Open**. 

   ![Galeria de conectores de dados](./media/collect-data/collect-data-page.png)

1. Na página específica do conector, certifique-se de que cumpriu todos os pré-requisitos e siga as instruções para ligar os dados ao Azure Sentinel. Pode levar algum tempo para os registos começarem a sincronizar com o Azure Sentinel. Depois de se ligar, vê um resumo dos dados no gráfico recebido dos **Dados** e o estado de conectividade dos tipos de dados.

   ![Configurar conectores de dados](./media/collect-data/opened-connector-page.png)
  
1. Clique no separador **Passos Seguintes** para obter uma lista de conteúdos fora da caixa que o Azure Sentinel fornece para o tipo de dados específico.

   ![Próximos passos para conectores](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Métodos de ligação de dados

Os seguintes métodos de ligação de dados são suportados pelo Azure Sentinel:

- **Serviço de integração de serviços:**<br> Alguns serviços estão ligados de forma nativa, como os serviços AWS e Microsoft, estes serviços aproveitam a fundação Azure para integração fora da caixa, as seguintes soluções podem ser conectadas em alguns cliques:
    - [Amazon Web Services - CloudTrail](connect-aws.md)
    - [Azure Ative Directory](connect-azure-active-directory.md) - registos de auditoria e registos de login
    - [Atividades Azure](connect-azure-activity.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure DDoS Protection](connect-azure-ddos-protection.md)
    - [Azure Defender for IoT](connect-asc-iot.md) (anteriormente Azure Security Center for IoT)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Firewall](connect-azure-firewall.md)
    - [Centro de Segurança Azure](connect-azure-security-center.md) - alertas de soluções Azure Defender
    - [Firewall de aplicação web Azure (WAF)](connect-azure-waf.md) (anteriormente Microsoft WAF)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Servidor de nomes de domínio](connect-dns.md)
    - [Microsoft 365 Defender](connect-microsoft-365-defender.md) - inclui dados brutos do MDATP
    - [Microsoft Defender para Endpoint](connect-microsoft-defender-advanced-threat-protection.md) (anteriormente Microsoft Defender Advanced Threat Protection)
    - [Microsoft Defender para a identidade](connect-azure-atp.md) (anteriormente Azure Advanced Threat Protection)
    - [Microsoft Defender para o Office 365](connect-office-365-advanced-threat-protection.md) (anteriormente Office 365 Advanced Threat Protection)
    - [Escritório 365](connect-office-365.md) (agora com Equipas!)
    - [Firewall do Windows](connect-windows-firewall.md)
    - [Eventos de segurança do Windows](connect-windows-security-events.md)

- **Soluções externas via API**: Algumas fontes de dados estão ligadas utilizando APIs que são fornecidas pela fonte de dados conectada. Normalmente, a maioria das tecnologias de segurança fornecem um conjunto de APIs através dos quais os registos de eventos podem ser recuperados. As APIs ligam-se ao Azure Sentinel e recolhem tipos de dados específicos e enviam-nos para o Azure Log Analytics. Os aparelhos ligados através da API incluem:
    
    - [Defesa de Phishing Agari e Proteção de Marcas](connect-agari-phishing-defense.md)
    - [Alcide kAudit](connect-alcide-kaudit.md)
    - [Barracuda WAF](connect-barracuda.md)
    - [Barracuda CloudGen Firewall](connect-barracuda-cloudgen-firewall.md)
    - [Defesa Contra Ameaças para Dispositivos Móveis da BETTER](connect-better-mtd.md)
    - [Beyond Security beSECURE](connect-besecure.md)
    - [Cisco Umbrella](connect-cisco-umbrella.md)
    - [Citrix Analytics (Segurança)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Okta SSO](connect-okta-single-sign-on.md)
    - [Orca Security](connect-orca-security-alerts.md)
    - [Perimeter 81 logs](connect-perimeter-81-logs.md)
    - [Proofpoint On Demand (POD) Email Security](connect-proofpoint-pod.md)
    - [Proofpoint TAP](connect-proofpoint-tap.md)
    - [Qualys VM](connect-qualys-vm.md)
    - [Salesforce Service Cloud](connect-salesforce-service-cloud.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [ICDX da Symantec](connect-symantec.md)
    - [Ponto Final da Cloud VMware Carbon Black Standard](connect-vmware-carbon-black.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **Soluções externas através do agente**: O Azure Sentinel pode ser ligado através de um agente a qualquer outra fonte de dados que possa realizar o streaming de registo em tempo real utilizando o protocolo Syslog.

    A maioria dos aparelhos utiliza o protocolo Syslog para enviar mensagens de evento que incluem o próprio registo e dados sobre o registo. O formato dos registos varia, mas a maioria dos aparelhos suporta formatação baseada em CEF para dados de registo. 

    O agente Azure Sentinel, que é na verdade o agente Log Analytics, converte registos formatados por CEF num formato que pode ser ingerido pelo Log Analytics. Dependendo do tipo do aparelho, o agente é instalado diretamente no aparelho ou num reencaminhador dedicado à base de registo de Linux. O agente da Linux recebe eventos do daemon Syslog sobre a UDP, mas se uma máquina Linux é esperada para recolher um grande volume de eventos Syslog, eles são enviados para o TCP do daemon Syslog para o agente e de lá para Log Analytics.

    - **Firewalls, proxies e pontos finais - CEF:**
        - [AI Vectra Detect](connect-ai-vectra-detect.md)
        - [Eventos de Segurança Akamai](connect-akamai-security-events.md)
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Citrix WAF](connect-citrix-waf.md)
        - [CyberArk Enterprise Password Vault](connect-cyberark.md)
        - [ExtraHop Reveal(x)](connect-extrahop.md)
        - [F5 ASM](connect-f5.md)
        - [Produtos da Forcepoint](connect-forcepoint-casb-ngfw.md)
        - [Fortinet](connect-fortinet.md)
        - [Illusive Networks AMS](connect-illusive-attack-management-system.md)
        - [Porta de entrada da WAF Imperva](connect-imperva-waf-gateway.md)
        - [One Identity Safeguard](connect-one-identity.md)
        - [Palo Alto Networks](connect-paloalto.md)
        - [Thycotic Secret Server](connect-thycotic-secret-server.md)
        - [Trend Micro Deep Security](connect-trend-micro.md)
        - [TippingPoint da Trend Micro](connect-trend-micro-tippingpoint.md)
        - [Plataforma forense da rede WireX](connect-wirex-systems.md)
        - [Zscaler](connect-zscaler.md)
        - [Outros aparelhos baseados em CEF](connect-common-event-format.md)
    - **Firewalls, proxies e pontos finais - Syslog:**
        - [Alsid para Diretório Ativo](connect-alsid-active-directory.md)
        - [Sistema de Computação Unificada cisco (UCS)](connect-cisco-ucs.md)
        - [Infoblox NIOS](connect-infoblox.md)
        - [Juniper SRX](connect-juniper-srx.md)
        - [Pulse Connect Secure](connect-pulse-connect-secure.md)
        - [Sophos XG](connect-sophos-xg-firewall.md)
        - [Squid Proxy](connect-squid-proxy.md)
        - [Symantec Proxy SG](connect-symantec-proxy-sg.md)
        - [Symantec VIP](connect-symantec-vip.md)
        - [Outros aparelhos baseados em Syslog](connect-syslog.md)
    - [Servidor Apache HTTP](connect-apache-http-server.md)
    - Soluções DLP
    - [Fornecedores de informações sobre ameaças](connect-threat-intelligence.md)
    - [Máquinas DNS](connect-dns.md) - agente instalado diretamente na máquina DNS
    - [Azure Stack VMs](connect-azure-stack.md)
    - Servidores Linux
    - Outras nuvens
    
## <a name="agent-connection-options"></a>Opções de ligação do agente<a name="agent-options"></a>

Para ligar o seu aparelho externo ao Azure Sentinel, o agente deve ser colocado numa máquina dedicada (VM ou no local) para apoiar a comunicação entre o aparelho e o Azure Sentinel. Pode implementar o agente de forma automática ou manual. A implementação automática só está disponível se a sua máquina dedicada for um novo VM que está a criar em Azure. 

![CEF em Azure](./media/connect-cef/cef-syslog-azure.png)

Em alternativa, pode colocar o agente manualmente num VM Azure existente, num VM noutra nuvem ou numa máquina no local.

![CEF nas instalações](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Mapear tipos de dados com as opções de ligação do Azure Sentinel


| **Tipo de dados** | **Como ligar** | **Conector de dados?** | **Comentários** |
|------|---------|-------------|------|
| AWSCloudTrail | [Ligar o AWS](connect-aws.md) | &#10003; | |
| AzureActivity | Ligar a atividade e [os registos de atividades do](../azure-monitor/essentials/platform-logs-overview.md) [Azure](connect-azure-activity.md)| &#10003; | |
| Auditorias | [Ligar ao Azure AD](connect-azure-active-directory.md)  | &#10003; | |
| SigninLogs | [Ligar ao Azure AD](connect-azure-active-directory.md)  | &#10003; | |
| AzureFirewall |[Diagnóstico do Azure](../firewall/firewall-diagnostics.md) | &#10003; | |
| InformationProtectionLogs_CL  | [Relatórios de Proteção de Informação da Azure](/azure/information-protection/reports-aip)<br>[Ligar ao Azure Information Protection](connect-azure-information-protection.md)  | &#10003; | Isto utiliza normalmente a função **InformationProtectionEvents** para além do tipo de dados. Para mais informações, consulte [Como modificar os relatórios e criar consultas personalizadas](/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)|
| AzureNetworkAnalytics_CL  | [Análise de tráfego analítico de](../network-watcher/traffic-analytics.md) [tráfego](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog  | [Ligar CEF](connect-common-event-format.md)  | &#10003; | |
| OfficeActivity | [Ligar o Office 365](connect-office-365.md) | &#10003; | |
| SecurityEvents | [Ligar aos eventos de segurança do Windows](connect-windows-security-events.md)  | &#10003; | Para os livros de protocolos inseguros, consulte [a configuração do livro de trabalhos de protocolos inseguros](./quickstart-get-visibility.md#use-built-in-workbooks)  |
| Syslog | [Ligar a Syslog](connect-syslog.md) | &#10003; | |
| Firewall de aplicação web da Microsoft (WAF) - (AzureDiagnostics) |[Conecte firewall de aplicação web da Microsoft](./connect-azure-waf.md) | &#10003; | |
| SymantecICDx_CL | [Ligar a Symantec](connect-symantec.md) | &#10003; | |
| ThreatIntelligenceIndicator  | [Ligar às informações sobre ameaças](connect-threat-intelligence.md)  | &#10003; | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Mapa de serviço do Monitor Azure](../azure-monitor/vm/service-map.md)<br>[Insights VM do Monitor Azure no embarque](../azure-monitor/vm/vminsights-enable-overview.md) <br> [Ativar insights VM do Monitor Azure](../azure-monitor/vm/vminsights-enable-overview.md) <br> [Utilização de embarque único em VM](../azure-monitor/vm/vminsights-enable-portal.md)<br>  [Utilização da política de embarque via](../azure-monitor/vm/vminsights-enable-policy.md)| &#10007; | Livro de insights VM  |
| DnsEvents | [Ligar DNS](connect-dns.md) | &#10003; | |
| W3CIISLog | [Ligar registos IIS](../azure-monitor/agents/data-sources-iis-logs.md)  | &#10007; | |
| WireData | [Ligar dados de arame](../azure-monitor/insights/wire-data.md) | &#10007; | |
| Parede de Fogo windows | [Ligar firewall do Windows](connect-windows-firewall.md) | &#10003; | |
| AADIP SecurityAlert  | [Ligar ao Azure AD Identity Protection](connect-azure-ad-identity-protection.md)  | &#10003; | |
| AATP SecurityAlert  | [Ligue o Microsoft Defender para a Identidade](connect-azure-atp.md) (anteriormente Azure ATP) | &#10003; | |
| Segurança ASCAlert  | [Ligue alertas do Azure Defender](connect-azure-security-center.md) do Centro de Segurança Azure  | &#10003; | |
| Segurança MCASAlert  | [Conecte a Segurança da Aplicação Microsoft Cloud](connect-cloud-app-security.md)  | &#10003; | |
| SecurityAlert | | | |
| Sysmon (Evento) | [Ligar Sysmon](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Conecte eventos do Windows](../azure-monitor/agents/data-sources-windows-events.md) <br> [Pegue o Sysmon Parser](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/Sysmon/Sysmon-v10.42-Parser.txt)| &#10007; | A coleção Sysmon não é instalada por defeito em máquinas virtuais. Para obter mais informações sobre como instalar o Agente Sysmon, consulte [sysmon.](/sysinternals/downloads/sysmon) |
| Data de Configuração  | [Automatizar o inventário VM](../automation/change-tracking/overview.md)| &#10007; | |
| ConfiguraçãoChange  | [Automatizar o rastreio de VM](../automation/change-tracking/overview.md) | &#10007; | |
| F5 BIG-IP | [Ligar a F5 BIG-IP](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | &#10007; | |
| McasshadowItReporting  |  | &#10007; | |
| Barracuda_CL | [Ligar a Barracuda](connect-barracuda.md) | &#10003; | |


## <a name="next-steps"></a>Passos seguintes

- Para começar com o Azure Sentinel, precisa de uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/free/).
- Aprenda a [embarcar os seus dados ao Azure Sentinel](quickstart-onboard.md)e obtenha [visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)