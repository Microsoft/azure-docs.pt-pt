---
title: Parceria com a Norma de Proteção DDoS Azure
description: Compreenda as oportunidades de parceria possibilitadas pela Norma de Proteção DDoS Azure.
ms.service: virtual-network
documentationcenter: na
author: anupamvi
mms.devlang: na
ms.topic: article
ms.date: 01/28/2020
ms.author: kumud
ms.openlocfilehash: 39cb2f90b49725c78746df69505fad7160e1db92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849685"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Parceria com a Norma de Proteção DDoS Azure
Este artigo descreve oportunidades de parceria possibilitadas pela Norma de Proteção DDoS Azure. Este artigo destina-se a ajudar os gestores de produtos e as funções de desenvolvimento de negócios a compreender as trajetórias de investimento e a fornecer informações sobre as propostas de valor de parceria.

## <a name="background"></a>Segundo plano
Os ataques de negação de serviço distribuídos (DDoS) são uma das principais preocupações de disponibilidade e segurança expressas pelos clientes que deslocam as suas aplicações para a nuvem. Com a extorsão e o hacktivismo sendo as motivações comuns por trás dos ataques do DDoS, têm vindo constantemente a aumentar em tipo, escala e frequência da ocorrência, uma vez que são relativamente fáceis e baratas de lançar.

A Azure DDoS Protection fornece contramedidas contra as ameaças mais sofisticadas do DDoS, alavancando a escala global da rede Azure. O serviço fornece capacidades de mitigação de DDoS melhoradas para aplicações e recursos implantados em redes virtuais.

Os parceiros tecnológicos podem proteger os recursos dos seus clientes de forma nativa com a Norma de Proteção DDoS Azure para resolver as preocupações de disponibilidade e fiabilidade devido aos ataques do DDoS.

## <a name="introduction-to-azure-ddos-protection-standard"></a>Introdução à Norma de Proteção DDoS Azure
A Norma de Proteção DDoS Azure fornece capacidades de mitigação dDoS melhoradas contra ataques DDoS da Camada 3 e camada 4. Seguem-se as principais características do serviço DDoS Protection Standard.

### <a name="adaptive-real-time-tuning"></a>Afinação adaptável em tempo real
Para cada aplicação protegida, a Norma de Proteção De DDoS azure afina automaticamente os limiares da política de mitigação do DDoS com base nos padrões de perfil de tráfego da aplicação. O serviço realiza esta personalização utilizando duas insights:

- Aprendizagem automática dos padrões de tráfego por cliente (por IP) para as camadas 3 e 4.
- Minimizando falsos positivos, considerando que a escala de Azure lhe permite absorver uma quantidade significativa de tráfego.

![Afinação em tempo real adaptativo](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>Análise de ataque, telemetria, monitorização e alerta
A Proteção DDoS Azure identifica e atenua os ataques do DDoS sem qualquer intervenção do utilizador.

- Se o recurso protegido estiver na subscrição abrangida pelo Azure Security Center, a Norma de Proteção DDoS envia automaticamente um alerta para o Centro de Segurança sempre que um ataque DDoS for detetado e atenuado contra a aplicação protegida.
- Alternativamente, para ser notificado quando há uma mitigação ativa para um IP público protegido, você pode [configurar um alerta](manage-ddos-protection.md#configure-alerts-for-ddos-protection-metrics) sobre a métrica sob ataque DDoS ou não.
- Pode ainda optar por criar alertas para as outras métricas do DDoS e [configurar](manage-ddos-protection.md#configure-ddos-attack-analytics) a análise de ataques para compreender a escala do ataque, o tráfego a ser retirado, vetores de ataque, contribuintes de topo e outros detalhes.

![Métricas DDoS](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>Resposta rápida dDoS (RDR)
Os clientes da DDoS Protection Standard têm acesso à [equipa de Resposta Rápida](https://azure.microsoft.com/blog/ddos-protection-attack-analytics-rapid-response/) durante um ataque ativo. A RDR pode ajudar na investigação do ataque, mitigações personalizadas durante um ataque e análise pós-ataque.

### <a name="sla-guarantee-and-cost-protection"></a>Garantia sla e proteção de custos
O serviço DDoS Protection Standard é coberto por um SLA de 99,99%, e a proteção de custos fornece créditos de recursos para escala durante um ataque documentado. Para mais informações, consulte [SLA para Proteção DDoS Azure](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/).

## <a name="featured-partner-scenarios"></a>Cenários de parceiros em destaque
Seguem-se os principais benefícios que pode obter integrando-se com a Norma de Proteção DDoS Azure:

- Os serviços oferecidos pelos parceiros (balanceador de carga, firewall de aplicação web, firewall, etc.) aos seus clientes são automaticamente protegidos (etiquetas brancas) pela Norma de Proteção DDoS Azure na parte de trás.
- Os parceiros têm acesso à análise e telemetria de ataque Azure DDoS Protection Standard que podem integrar com os seus próprios produtos, oferecendo uma experiência unificada ao cliente.  
- Os parceiros têm acesso ao suporte de resposta rápida dDoS mesmo na ausência de resposta rápida do Azure, para questões relacionadas com dDoS.
- As aplicações protegidas dos parceiros são apoiadas por uma garantia DDoS SLA e proteção de custos em caso de ataques dDoS.

## <a name="technical-integration-overview"></a>Visão geral da integração técnica
As oportunidades de parceria da Azure DDoS Protection Standard são disponibilizadas através do portal Azure, APIs e CLI/PS.

### <a name="integrate-with-ddos-protection-standard"></a>Integrar com a Norma de Proteção DDoS
São necessários os seguintes passos para que os parceiros configurem a integração com a Norma de Proteção DDoS azure:
1. Crie um Plano de Proteção DDoS na subscrição desejada (parceiro). Para obter instruções passo a passo, consulte [Criar um plano de proteção padrão DDoS](manage-ddos-protection.md#create-a-ddos-protection-plan).
   > [!NOTE]
   > Apenas 1 Plano de Proteção DDoS precisa de ser criado para um determinado inquilino. 
2. Implemente um serviço com ponto final público nas suas subscrições (parceiro), tais como balancer de carga, firewalls e firewall de aplicação web. 
3. Ativar a Norma de Proteção DDoS Azure na rede virtual do serviço que tem pontos finais públicos utilizando o Plano de Proteção DDoS criado no primeiro passo. Para instruções stpe-by-step, consulte [Enable DDoS Standard Protection Plan](manage-ddos-protection.md#enable-ddos-for-an-existing-virtual-network)
   > [!IMPORTANT] 
   > Depois de a Norma de Proteção DDoS azure ser ativada numa rede virtual, todos os IPs públicos dentro dessa rede virtual são automaticamente protegidos. A origem destes IPs públicos pode estar dentro do Azure (subscrição de clientes) ou fora do Azure. 
4. Opcionalmente, integre a telemetria E análise do Padrão de Proteção Azure DDoS e ataque no seu painel de instrumentos específico para o cliente. Para obter mais informações sobre a utilização da telemetria, consulte [A telemetria de Proteção DDoS](manage-ddos-protection.md#use-ddos-protection-telemetry). Para mais informações sobre a configuração da análise de ataques, consulte a análise de [ataque do DDoS configure](manage-ddos-protection.md#configure-ddos-attack-analytics)

### <a name="onboarding-guides-and-technical-documentation"></a>Guias de embarque e documentação técnica

- [Página de produto de proteção Azure DDoS](https://azure.microsoft.com/services/ddos-protection/)
- [Documentação de proteção de DDoS azure](ddos-protection-overview.md)
- [Referência API de Proteção DDoS Azure](https://docs.microsoft.com/rest/api/virtualnetwork/ddosprotectionplans)
- [Referência da Rede Virtual Azure API](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>Obter ajuda

- Se tiver dúvidas sobre aplicações, serviços ou integrações de produtos com a Norma de Proteção DDoS Azure, contacte a comunidade de [segurança Azure.](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security)
- Acompanhe as discussões sobre [o Transbordo de Stack](https://stackoverflow.com/tags/azure-ddos/).

### <a name="get-to-market"></a>Chegar ao mercado

- O programa principal de parceria com a Microsoft é a [Microsoft Partner Network](https://partner.microsoft.com/). – As integrações de Segurança do Microsoft Graph inserem-se na faixa do Fornecedor independente de [Software MPN (ISV).](https://partner.microsoft.com/saas-solution-guide)
- [A Microsoft Intelligent Security Association](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) é o programa especificamente para os Microsoft Security Partners para ajudar a enriquecer os seus produtos de segurança e melhorar a descoberta do cliente das suas integrações em produtos da Microsoft Security.

## <a name="next-steps"></a>Passos seguintes
Ver integrações de parceiros existentes:

- [Barracuda WAF-as-a-service](https://www.barracuda.com/waf-as-a-service)
- [Azure Cloud WAF de Radware](https://www.radware.com/resources/microsoft-azure/)
