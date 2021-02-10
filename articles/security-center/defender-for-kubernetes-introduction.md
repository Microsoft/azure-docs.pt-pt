---
title: Azure Defender para Kubernetes - os benefícios e funcionalidades
description: Conheça os benefícios e funcionalidades do Azure Defender para Kubernetes.
author: memildin
ms.author: memildin
ms.date: 02/07/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 1f013f22b482c1e1d093f106bd786be870103f3d
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008507"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Introdução ao Azure Defender para Kubernetes

O Azure Kubernetes Service (AKS) é o serviço gerido pela Microsoft para desenvolver, implantar e gerir aplicações contentorizadas.

O Azure Security Center e a AKS formam uma oferta de segurança kubernetes nativa da nuvem com endurecimento ambiental, proteção de carga de trabalho e proteção de tempo de execução, conforme descrito na [segurança do contentor no Centro de Segurança.](container-security.md)

Para deteção de ameaças para os seus clusters Kubernetes, ative **o Azure Defender para Kubernetes**.

A deteção de ameaças ao nível do anfitrião para os seus nós Linux AKS está disponível se ativar [o Azure Defender para servidores](defender-for-servers-introduction.md) e o seu agente Log Analytics. No entanto, se o seu cluster AKS for implantado num conjunto de escala de máquina virtual, o agente Log Analytics não está atualmente suportado.

## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Disponibilidade Geral (GA)|
|Preços:|**Azure Defender for Kubernetes** é faturado como mostrado [na página de preços](security-center-pricing.md)|
|Funções e permissões necessárias:|**O administrador de segurança** pode dispensar alertas.<br>**O leitor de segurança** pode ver as descobertas.|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Quais são os benefícios do Azure Defender para kubernetes?

O Azure Defender for Kubernetes fornece **proteção contra ameaças ao nível do cluster,** monitorizando os seus serviços geridos pela AKS através dos registos recuperados pelo Serviço Azure Kubernetes (AKS).

Exemplos de eventos de segurança que o Azure Defender para os monitores kubernetes incluem dashboards de Kubernetes expostos, criação de papéis privilegiados elevados e criação de suportes sensíveis. Para obter uma lista completa dos alertas de nível de cluster AKS, consulte a [tabela de referência dos alertas](alerts-reference.md#alerts-akscluster).

> [!TIP]
> Pode simular alertas de contentores seguindo as instruções [neste post de blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).

Além disso, a nossa equipa global de investigadores de segurança monitoriza constantemente o cenário de ameaças. Acrescentam alertas e vulnerabilidades específicos aos contentores à medida que são descobertos.

>[!NOTE]
> O Security Center gera alertas de segurança para as ações e implementações do Serviço Azure Kubernetes que ocorrem **depois** de ter ativado o Azure Defender para Kubernetes.




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender para Kubernetes - FAQ

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Ainda posso ter proteções AKS sem o agente Log Analytics?

O plano **Azure Defender for Kubernetes** fornece proteções ao nível do cluster. Se também implementar o agente Log Analytics do **Azure Defender para servidores,** receberá a proteção contra ameaças para os seus nós que está dotado desse plano. Saiba mais em [Introdução ao Azure Defender para servidores](defender-for-servers-introduction.md).

Recomendamos a colocação de ambos, para a proteção mais completa possível.

Se optar por não instalar o agente nos seus anfitriões, receberá apenas um subconjunto dos benefícios de proteção contra ameaças e alertas de segurança. Ainda receberá alertas relacionados com análises de rede e comunicações com servidores maliciosos.

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>A AKS permite-me instalar extensões VM personalizadas nos meus nós AKS?
Para que o Azure Defender monitorize os seus nós AKS, eles devem estar a executar o agente Log Analytics. 

O AKS é um serviço gerido e uma vez que o agente de análise log é uma extensão gerida pela Microsoft, também é suportado em clusters AKS.

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>Se o meu grupo já está a executar um Monitor Azure para agente de contentores, também preciso do agente Log Analytics?
Para que o Azure Defender monitorize os seus nós AKS, eles devem estar a executar o agente Log Analytics.

Se os seus clusters já estiverem a executar o Azure Monitor para agente de contentores, também pode instalar o agente Log Analytics e os dois agentes podem trabalhar lado a lado sem problemas.

[Saiba mais sobre o Azure Monitor para o agente de contentores.](../azure-monitor/insights/container-insights-manage-agent.md)


## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre a proteção Kubernetes do Security Center, incluindo Azure Defender for Kubernetes. 

> [!div class="nextstepaction"]
> [Ativar o Azure Defender](security-center-pricing.md#enable-azure-defender)

Para obter material relacionado, consulte os seguintes artigos: 

- [Alertas de fluxo para uma solução siem, SOAR ou GESTão de Serviços de TI](export-to-siem.md)
- [Tabela de alertas de referência](alerts-reference.md)
