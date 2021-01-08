---
title: Parceria com a Azure DDoS Protection Standard
description: Compreenda as oportunidades de parceria habilitados pela Azure DDoS Protection Standard.
ms.service: ddos-protection
documentationcenter: na
author: yitoh
mms.devlang: na
ms.topic: how-to
ms.date: 08/28/2020
ms.author: kumud
ms.openlocfilehash: f6cba0118a98ea50e8020b91b9c2064299ca4ccf
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019164"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Parceria com a Azure DDoS Protection Standard
Este artigo descreve oportunidades de parceria ativadas pela Norma de Proteção DDoS Azure. Este artigo destina-se a ajudar os gestores de produtos e as funções de desenvolvimento de negócios a compreender os percursos de investimento e a fornecer informações sobre as propostas de valor de parceria.

## <a name="background"></a>Fundo
Os ataques de negação de serviço distribuídos (DDoS) são uma das principais preocupações de disponibilidade e segurança expressas pelos clientes que movem as suas aplicações para a nuvem. Sendo a extorsão e o hacktivismo as motivações comuns por detrás dos ataques do DDoS, têm vindo a aumentar consistentemente em tipo, escala e frequência de ocorrência, uma vez que são relativamente fáceis e baratas de lançar.

A Azure DDoS Protection fornece contramedidas contra as ameaças mais sofisticadas do DDoS, alavancando a escala global da rede Azure. O serviço fornece capacidades de mitigação de DDoS melhoradas para aplicações e recursos implantados em redes virtuais.

Os parceiros tecnológicos podem proteger os recursos dos seus clientes de forma nativa com a Norma de Proteção DDoS da Azure para resolver as preocupações de disponibilidade e fiabilidade devido aos ataques de DDoS.

## <a name="introduction-to-azure-ddos-protection-standard"></a>Introdução à Norma de Proteção DDoS Azure
O Azure DDoS Protection Standard fornece capacidades de mitigação de DDoS melhoradas contra os ataques DDoS da Camada 3 e da Camada 4. Seguem-se as principais características do serviço DDoS Protection Standard.

### <a name="adaptive-real-time-tuning"></a>Sintonização adaptativa em tempo real
Para cada aplicação protegida, a Norma de Proteção DDoS Azure afina automaticamente os limiares da política de mitigação do DDoS com base nos padrões de perfil de tráfego da aplicação. O serviço realiza esta personalização utilizando dois insights:

- Aprendizagem automática de padrões de tráfego por cliente (por IP) para as camadas 3 e 4.
- Minimizar falsos positivos, considerando que a escala de Azure permite absorver uma quantidade significativa de tráfego.

![Afinação em tempo real adaptativo](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>Análise de ataque, telemetria, monitorização e alerta
A Azure DDoS Protection identifica e atenua os ataques DDoS sem qualquer intervenção do utilizador.

- Se o recurso protegido estiver na subscrição abrangida pelo Centro de Segurança Azure, a DDoS Protection Standard envia automaticamente um alerta para o Centro de Segurança sempre que um ataque DDoS é detetado e atenuado contra a aplicação protegida.
- Em alternativa, para ser notificado quando há uma mitigação ativa para um IP público protegido, você pode [configurar um alerta](alerts.md) sobre a métrica sob o ataque DDoS ou não.
- Você pode ainda optar por criar alertas para as outras métricas do DDoS e [configurar a telemetria](telemetry.md) de ataque para entender a escala do ataque, o tráfego ser derrubado, vetores de ataque, contribuintes de topo, e outros detalhes.

![Métricas DDoS](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>DDoS resposta rápida (DRR)
Os clientes DDoS Protection Standard têm acesso à [equipa de Resposta Rápida](ddos-rapid-response.md) durante um ataque ativo. A DRR pode ajudar na investigação de ataque durante um ataque, bem como na análise pós-ataque.

### <a name="sla-guarantee-and-cost-protection"></a>Garantia SLA e proteção de custos
O serviço DDoS Protection Standard é coberto por um SLA de 99,99%, e a proteção de custos fornece créditos de recursos para escala durante um ataque documentado. Para mais informações, consulte [sLA para a proteção do DDoS Azure](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/).

## <a name="featured-partner-scenarios"></a>Cenários de parceiros em destaque
Os seguintes são os principais benefícios que pode derivar integrando-se com a Norma de Proteção DDoS Azure:

- Os serviços oferecidos pelos parceiros (balanceador de carga, firewall de aplicação web, firewall, etc.) aos seus clientes são automaticamente protegidos (etiquetado branco) pela Azure DDoS Protection Standard na parte de trás.
- Os parceiros têm acesso à Azure DDoS Protection Standard análise de ataque e telemetria que podem integrar com os seus próprios produtos, oferecendo uma experiência unificada ao cliente.  
- Os parceiros têm acesso ao suporte de resposta rápida DDoS mesmo na ausência de resposta rápida do Azure, para questões relacionadas com o DDoS.
- As aplicações protegidas dos parceiros são apoiadas por uma garantia DDoS SLA e proteção de custos em caso de ataques DDoS.

## <a name="technical-integration-overview"></a>Visão geral da integração técnica
As oportunidades de parceria Azure DDoS Protection Standard são disponibilizadas através do portal Azure, APIs e CLI/PS.

### <a name="integrate-with-ddos-protection-standard"></a>Integrar-se com a Norma de Proteção DDoS
São necessárias as seguintes etapas para que os parceiros confirquem a integração com a Norma de Proteção DDoS da Azure:
1. Crie um Plano de Proteção DDoS na subscrição desejada (parceiro). Para obter instruções passo a passo, consulte [Criar um plano de proteção padrão DDoS](manage-ddos-protection.md#create-a-ddos-protection-plan).
   > [!NOTE]
   > Apenas 1 Plano de Proteção DDoS precisa de ser criado para um determinado inquilino. 
2. Implementar um serviço com ponto final público nas subscrições (parceiro), tais como balanceador de carga, firewalls e firewall de aplicações web. 
3. Ativar a Norma de Proteção DDoS do Azure na rede virtual do serviço que tem pontos finais públicos utilizando o Plano de Proteção DDoS criado no primeiro passo. Para obter instruções de estrias, consulte o plano de [proteção padrão DDoS](manage-ddos-protection.md#enable-ddos-protection-for-an-existing-virtual-network)
   > [!IMPORTANT] 
   > Depois de o Azure DDoS Protection Standard estar ativado numa rede virtual, todos os IPs públicos dentro dessa rede virtual são automaticamente protegidos. A origem destes IPs públicos pode estar dentro do Azure (subscrição de clientes) ou fora de Azure. 
4. Opcionalmente, integre a telemetria Standard de Proteção Azure DDoS e a análise de ataque no seu painel de instrumentos específico para o cliente. Para obter mais informações sobre a utilização da telemetria, consulte [visualização e configurar a telemetria de proteção DDoS](telemetry.md). 

### <a name="onboarding-guides-and-technical-documentation"></a>Guias de embarque e documentação técnica

- [Página de produto Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/)
- [Documentação de proteção Azure DDoS](ddos-protection-overview.md)
- [Referência Azure DDoS Protection API](/rest/api/virtualnetwork/ddosprotectionplans)
- [Referência API de rede virtual Azure](/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>Obter ajuda

- Se tiver dúvidas sobre integrações de aplicações, serviços ou produtos com a Norma de Proteção DDoS Azure, contacte a [comunidade de segurança Azure](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security).
- Acompanhe as discussões sobre [Stack Overflow](https://stackoverflow.com/tags/azure-ddos/).

### <a name="get-to-market"></a>Chegar ao mercado

- O programa principal de parceria com a Microsoft é a [Microsoft Partner Network](https://partner.microsoft.com/). – As integrações de Segurança de Gráficos da Microsoft enquadram-se na faixa do [Fornecedor independente de software (ISV) da MPN.](https://partner.microsoft.com/saas-solution-guide)
- [A Microsoft Intelligent Security Association](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) é o programa especificamente para os Microsoft Security Partners para ajudar a enriquecer os seus produtos de segurança e melhorar a descoberta dos seus clientes das suas integrações nos produtos Microsoft Security.

## <a name="next-steps"></a>Próximos passos
Ver integrações de parceiros existentes:

- [Barracuda WAF-as-a-service](https://www.barracuda.com/waf-as-a-service)
