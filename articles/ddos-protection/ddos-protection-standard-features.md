---
title: Funcionalidades de Proteção Azure DDoS
description: Aprenda funcionalidades de Proteção DDoS Azure
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 104c9dcd3b7fd931e4f54841c9de9d17cfd72353
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937326"
---
# <a name="azure-ddos-protection-standard-features"></a>Funcionalidades do Azure DDoS Protection Standard

As secções seguintes descrevem as principais características do serviço Azure DDoS Protection Standard.

## <a name="always-on-traffic-monitoring"></a>Monitorização do tráfego sempre ligado

A DDoS Protection Standard monitoriza a utilização real do tráfego e compara-a constantemente com os limiares definidos na Política DDoS. Quando o limiar de tráfego é ultrapassado, a mitigação do DDoS é iniciada automaticamente. Quando o tráfego volta abaixo do limiar, a mitigação é removida.

![Azure DDos Proteção Padrão de Mitigação](./media/ddos-protection-overview/mitigation.png)

Durante a mitigação, o tráfego enviado para o recurso protegido é redirecionado pelo serviço de proteção DDoS e são realizadas várias verificações, tais como as seguintes verificações:

- Certifique-se de que os pacotes estão em conformidade com as especificações da Internet e não estão mal formados.
- Interaja com o cliente para determinar se o tráfego é potencialmente um pacote falsificado (por exemplo: SYN Auth ou SYN Cookie ou deixando cair um pacote para a fonte retransmitê-lo).
- Pacotes de limite de taxas, se nenhum outro método de execução puder ser executado.

A proteção DDoS bloqueia o tráfego do ataque e reencaminha o tráfego restante para o destino pretendido. Em poucos minutos após a deteção do ataque, será notificado através das métricas do Azure Monitor. Ao configurar a gravação na telemetria DDoS Protection Standard, pode escrever os registos para as opções disponíveis para análise futura. Os dados métricos do Azure Monitor para a Norma de Proteção DDoS são mantidos durante 30 dias.

## <a name="adaptive-real-time-tuning"></a>Afinação em tempo real adaptativo

O serviço Azure DDoS Protection Basic ajuda a proteger os clientes e a prevenir impactos para outros clientes. Por exemplo, se um serviço for prestado para um volume típico de tráfego legítimo de entrada que seja menor do que a taxa de *disparo* da política de proteção dDos em toda a infraestrutura, um ataque DDoS aos recursos desse cliente pode passar despercebido. De uma forma mais geral, a complexidade dos ataques recentes (por exemplo, DDoS multi-vectores) e os comportamentos específicos de aplicação dos inquilinos exigem políticas de proteção personalizadas por cliente. O serviço realiza esta personalização utilizando dois insights:

- Aprendizagem automática de padrões de tráfego por cliente (por IP) para as camadas 3 e 4.

- Minimizar falsos positivos, considerando que a escala de Azure permite absorver uma quantidade significativa de tráfego.

![Diagrama de como funciona a Norma de Proteção DDoS, com "Policy Generation" em círculo](./media/ddos-best-practices/image-5.png)

## <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>Telemetria de Proteção DDoS, monitorização e alerta

A DDoS Protection Standard expõe telemetria rica via [Azure Monitor](../azure-monitor/overview.md) durante a duração de um ataque DDoS. Pode configurar alertas para qualquer uma das métricas do Monitor Azure que o DDoS Protection utiliza. Pode integrar o registo com Splunk (Azure Event Hubs), registos Azure Monitor e Azure Storage para análise avançada através da interface Azure Monitor Diagnostics.

### <a name="ddos-mitigation-policies"></a>Políticas de mitigação do DDoS

No portal Azure, selecione  >  **Monitor Metrics**. No painel **Métricas,** selecione o grupo de recursos, selecione um tipo de recurso de **Endereço IP Público** e selecione o seu endereço IP público Azure. As métricas DDoS são visíveis no painel **de métricas disponíveis.**

A DDoS Protection Standard aplica três políticas de mitigação autofinadas (TCP SYN, TCP e UDP) para cada IP público do recurso protegido, na rede virtual que tem DDoS ativado. Pode ver os limiares de política selecionando os **pacotes métricos de entrada para desencadear a mitigação do DDoS**.

![Gráfico de métricas e métricas disponíveis](./media/ddos-best-practices/image-7.png)

Os limiares de política são configurados automaticamente através de perfis de tráfego de rede baseados em aprendizagem automática. A mitigação do DDoS ocorre para um endereço IP sob ataque apenas quando o limiar da apólice é ultrapassado.

### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Métrica para um endereço IP sob ataque DDoS

Se o endereço IP público estiver sob ataque, o valor da métrica sob ataque **DDoS ou não** muda para 1, uma vez que a DDoS Protection realiza mitigação no tráfego de ataque.

![Métrica e gráfico "Ataque DDoS ou não"](./media/ddos-best-practices/image-8.png)

Recomendamos configurar um alerta nesta métrica. Em seguida, será notificado quando houver uma mitigação ativa do DDoS realizada no seu endereço IP público.

Para obter mais informações, consulte [a Norma de Proteção Azure DDoS utilizando o portal Azure](manage-ddos-protection.md).

## <a name="web-application-firewall-for-resource-attacks"></a>Firewall de aplicação web para ataques de recursos

Específico para ataques de recursos na camada de aplicação, você deve configurar uma firewall de aplicação web (WAF) para ajudar a proteger aplicações web. Um WAF inspeciona o tráfego web de entrada para bloquear injeções DE SQL, scripts de cross-site, DDoS e outros ataques da Camada 7. O Azure fornece [o WAF como uma característica do Gateway de Aplicação](../web-application-firewall/ag/ag-overview.md) para proteção centralizada das suas aplicações web contra explorações e vulnerabilidades comuns. Existem outras ofertas waf disponíveis de parceiros Azure que podem ser mais adequados para as suas necessidades através do [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1)

Até as firewalls de aplicações web são suscetíveis a ataques volumosos e de exaustão do estado. Recomendamos vivamente que permita que o DDoS Protection Standard na rede virtual WAF ajude a proteger contra ataques volumosos e protocolares. Para mais informações, consulte a secção [de arquiteturas de referência da DDoS Protection.](ddos-protection-reference-architectures.md)

## <a name="protection-planning"></a>Planeamento de Proteção

O planeamento e preparação são cruciais para entender como um sistema irá funcionar durante um ataque DDoS. A elaboração de um plano de resposta à gestão de incidentes faz parte deste esforço.

Se tiver o DDoS Protection Standard, certifique-se de que está ativado na rede virtual de pontos finais virados para a Internet. Configurar alertas DDoS ajuda-o constantemente a observar eventuais ataques à sua infraestrutura. 

Monitorize as suas aplicações de forma independente. Entenda o comportamento normal de uma aplicação. Prepare-se para agir se a aplicação não se comportar como esperado durante um ataque DDoS. 

Saiba como os seus serviços responderão a um ataque [testando através de simulações.](test-through-simulations.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um plano de proteção DDoS](manage-ddos-protection.md).