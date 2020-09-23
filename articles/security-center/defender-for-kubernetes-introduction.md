---
title: Azure Defender para Kubernetes - os benefícios e funcionalidades
description: Conheça os benefícios e funcionalidades do Azure Defender para Kubernetes.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: e72875135243733a6acf03bb3aa6fb9405392d9d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940321"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Introdução ao Azure Defender para Kubernetes

O Azure Kubernetes Service (AKS) é o serviço gerido pela Microsoft para desenvolver, implantar e gerir aplicações contentorizadas.

O Azure Security Center e a AKS formam a melhor oferta de segurança de Kubernetes nativa da nuvem e, em conjunto, fornecem endurecimento ambiental, proteção da carga de trabalho e proteção do tempo de execução, conforme descrito abaixo.

Para deteção de ameaças para os seus clusters Kubernetes, ative **o Azure Defender para Kubernetes**.

A deteção de ameaças ao nível do anfitrião para os seus nós Linux AKS está disponível se ativar [o Azure Defender para servidores](defender-for-servers-introduction.md).

## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Geralmente disponível (GA)|
|Preços:|**Azure Defender for Kubernetes** é faturado como mostrado [na página de preços](security-center-pricing.md)|
|Funções e permissões necessárias:|**O administrador de segurança** pode dispensar alertas.<br>**O leitor de segurança** pode ver as descobertas.|
|Nuvens:|![Yes](./media/icons/yes-icon.png) Nuvens comerciais<br>![No](./media/icons/no-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Quais são os benefícios do Azure Defender para kubernetes?

### <a name="run-time-protection"></a>Proteção contra o tempo de execução

Através de uma análise contínua das seguintes fontes AKS, o Security Center fornece proteção contra ameaças em tempo real para os seus ambientes contentorizados e gera alertas para ameaças e atividades maliciosas detetadas ao nível do hospedeiro *e* do cluster AKS. Pode utilizar estas informações para remediar rapidamente problemas de segurança e aumentar a segurança dos contentores.

O Centro de Segurança fornece proteção contra ameaças a diferentes níveis: 

- **Nível de anfitrião (fornecido pelo Azure Defender para servidores)** - Utilizando o mesmo agente Log Analytics que o Security Center utiliza em outros VMs, o Azure Defender monitoriza os seus nós Linux AKS para atividades suspeitas, tais como deteção de conchas web e ligação com endereços IP suspeitos conhecidos. O agente também monitoriza análises específicas de contentores, tais como criação privilegiada de contentores, acesso suspeito a servidores API e servidores Secure Shell (SSH) que estão dentro de um contentor Docker.

    Para obter uma lista dos alertas de nível de anfitrião AKS, consulte a [tabela de alertas de referência](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost).

    >[!IMPORTANT]
    > Se optar por não instalar os agentes nos seus anfitriões, receberá apenas um subconjunto dos benefícios de proteção contra ameaças e alertas de segurança. Ainda receberá alertas relacionados com análises de rede e comunicações com servidores maliciosos.

- **Nível de cluster AKS (fornecido pelo Azure Defender for Kubernetes)** - Ao nível do cluster, a proteção contra ameaças baseia-se na análise dos registos de auditoria da Kubernetes. Para ativar esta monitorização **sem agente,** ative o Azure Defender. Para gerar alertas a este nível, o Security Center monitoriza os seus serviços geridos pela AKS utilizando os registos recuperados pela AKS. Exemplos de eventos a este nível incluem dashboards kubernetes expostos, criação de papéis privilegiados elevados, e a criação de suportes sensíveis.

    Para obter uma lista dos alertas de nível de cluster AKS, consulte a [tabela de alertas de referência](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster).

    >[!NOTE]
    > O Security Center gera alertas de segurança para as ações e implementações do Serviço Azure Kubernetes que ocorrem após a opção Kubernetes estar ativada nas definições de subscrição. 

Além disso, a nossa equipa global de investigadores de segurança monitoriza constantemente o cenário de ameaças. Acrescentam alertas e vulnerabilidades específicos aos contentores à medida que são descobertos.

> [!TIP]
> Pode simular alertas de contentores seguindo as instruções [neste post de blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).



## <a name="how-does-security-centers-kubernetes-protection-work"></a>Como funciona a proteção kubernetes do Security Center?

Abaixo está um diagrama de alto nível da interação entre o Azure Security Center, o Azure Kubernetes Service e a Azure Policy.

Pode ver que os itens recebidos e analisados pelo Security Center incluem:

- registos de auditoria do servidor API
- eventos de segurança bruto do agente Log Analytics
- informação de configuração do cluster AKS
- Configuração de carga de trabalho da Azure Policy (através do **addon Azure Policy para Kubernetes).** [Saiba mais sobre a proteção da carga de trabalho das melhores práticas utilizando o controlo de admissão de Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Arquitetura de alto nível da interação entre o Azure Security Center, o Azure Kubernetes Service e a Azure Policy" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender para Kubernetes - FAQ

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Ainda posso ter proteções AKS sem o agente Log Analytics?

Como mencionado acima, o plano **opcional Azure Defender for Kubernetes** fornece proteções ao nível do cluster, o agente Log Analytics do **Azure Defender para servidores** protege os seus nós. 

Recomendamos a colocação de ambos, para a proteção mais completa possível.

Se optar por não instalar o agente nos seus anfitriões, receberá apenas um subconjunto dos benefícios de proteção contra ameaças e alertas de segurança. Ainda receberá alertas relacionados com análises de rede e comunicações com servidores maliciosos.


## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre a proteção Kubernetes do Security Center, incluindo Azure Defender for Kubernetes. 

Para obter material relacionado, consulte os seguintes artigos: 

- [Ativar o Azure Defender](security-center-pricing.md)
- [Alertas de exportação para um Sentinela Azure ou um SIEM de terceiros](continuous-export.md)
- [Tabela de alertas de referência](alerts-reference.md)