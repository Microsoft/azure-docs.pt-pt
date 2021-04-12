---
title: Azure Defender para Kubernetes - os benefícios e funcionalidades
description: Conheça os benefícios e funcionalidades do Azure Defender para Kubernetes.
author: memildin
ms.author: memildin
ms.date: 04/07/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: c500c7b7afb36ffbe04fb63551c3a7d17c1347d9
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029085"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Introdução ao Azure Defender para Kubernetes

Azure Defender for Kubernetes é o plano Azure Defender que fornece proteções para os seus clusters Kubernetes onde quer que estejam a funcionar. 

Podemos defender aglomerados em:

- **Azure Kubernetes Service (AKS)** - Serviço gerido pela Microsoft para desenvolver, implantar e gerir aplicações contentorizadas

- **Instalações e ambientes multi-nuvem** - Usando uma [extensão para Arc habilitado Kubernetes](defender-for-kubernetes-azure-arc.md)

O Azure Security Center e a AKS formam uma oferta de segurança kubernetes nativa da nuvem com endurecimento ambiental, proteção de carga de trabalho e proteção de tempo de execução, conforme descrito na [segurança do contentor no Centro de Segurança.](container-security.md)

A deteção de ameaças ao nível do anfitrião para os seus nós Linux AKS está disponível se ativar [o Azure Defender para servidores](defender-for-servers-introduction.md) e o seu agente Log Analytics. No entanto, se o seu cluster for implantado num conjunto de escala de máquina virtual, o agente Log Analytics não está atualmente suportado.



## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Disponibilidade Geral (GA)|
|Preços:|**Azure Defender for Kubernetes** é faturado como mostrado nos [preços do Security Center](https://azure.microsoft.com/pricing/details/security-center/)|
|Funções e permissões necessárias:|**O administrador de segurança** pode dispensar alertas.<br>**O leitor de segurança** pode ver as descobertas.|
|Nuvens:|![Yes](./media/icons/yes-icon.png) Nuvens comerciais<br>![Yes](./media/icons/yes-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Quais são os benefícios do Azure Defender para kubernetes?

O Azure Defender for Kubernetes fornece **proteção contra ameaças ao nível do cluster,** monitorizando os registos dos seus clusters.

Exemplos de eventos de segurança que o Azure Defender para os monitores kubernetes incluem dashboards de Kubernetes expostos, criação de papéis privilegiados elevados e criação de suportes sensíveis. Para obter uma lista completa dos alertas de nível de cluster, consulte a [tabela de referência dos alertas.](alerts-reference.md#alerts-akscluster)

> [!TIP]
> Pode simular alertas de contentores seguindo as instruções [neste post de blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).

Além disso, a nossa equipa global de investigadores de segurança monitoriza constantemente o cenário de ameaças. Acrescentam alertas e vulnerabilidades específicos aos contentores à medida que são descobertos.

>[!NOTE]
> O Azure Defender gera alertas de segurança para ações e implementações que ocorrem depois de ter ativado o plano Defender for Kubernetes na sua subscrição.




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender para Kubernetes - FAQ

### <a name="can-i-still-get-cluster-protections-without-the-log-analytics-agent"></a>Ainda posso ter proteções de agrupamento sem o agente Log Analytics?

O plano **Azure Defender for Kubernetes** fornece proteções ao nível do cluster. Se também implementar o agente Log Analytics do **Azure Defender para servidores,** receberá a proteção contra ameaças para os seus nós que está dotado desse plano. Saiba mais em [Introdução ao Azure Defender para servidores](defender-for-servers-introduction.md).

Recomendamos a colocação de ambos, para a proteção mais completa possível.

Se optar por não instalar o agente nos seus anfitriões, receberá apenas um subconjunto dos benefícios de proteção contra ameaças e alertas de segurança. Ainda receberá alertas relacionados com análises de rede e comunicações com servidores maliciosos.

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>A AKS permite-me instalar extensões VM personalizadas nos meus nós AKS?
Para que o Azure Defender monitorize os seus nós AKS, eles devem estar a executar o agente Log Analytics. 

O AKS é um serviço gerido e uma vez que o agente de análise log é uma extensão gerida pela Microsoft, também é suportado em clusters AKS.

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>Se o meu grupo já está a executar um Monitor Azure para agente de contentores, também preciso do agente Log Analytics?
Para que o Azure Defender monitorize os seus nós, eles devem estar a executar o agente Log Analytics.

Se os seus clusters já estiverem a executar o Azure Monitor para agente de contentores, também pode instalar o agente Log Analytics e os dois agentes podem trabalhar lado a lado sem problemas.

[Saiba mais sobre o Azure Monitor para o agente de contentores.](../azure-monitor/containers/container-insights-manage-agent.md)


## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre a proteção Kubernetes do Security Center, incluindo Azure Defender for Kubernetes. 

> [!div class="nextstepaction"]
> [Ativar o Azure Defender](enable-azure-defender.md)

Para obter material relacionado, consulte os seguintes artigos: 

- [Alertas de fluxo para uma solução siem, SOAR ou GESTão de Serviços de TI](export-to-siem.md)
- [Tabela de alertas de referência](alerts-reference.md)
