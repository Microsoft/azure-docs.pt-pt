---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 04/07/2021
ms.topic: include
ms.openlocfilehash: 73886b966676af75cc74484ccdc0632f080b041a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029149"
---
O Azure Defender fornece proteção contra ameaças em tempo real para os seus ambientes contentorizados e gera alertas para atividades suspeitas. Pode utilizar estas informações para remediar rapidamente problemas de segurança e aumentar a segurança dos contentores.

O Azure Defender fornece proteção contra ameaças a diferentes níveis: 

* **Nível de anfitrião (fornecido pelo Azure Defender para servidores)** - Utilizando o mesmo agente Log Analytics que o Security Center utiliza em outros VMs, o Azure Defender monitoriza os seus nós Linux Kubernetes para atividades suspeitas, tais como deteção de conchas web e ligação com endereços IP suspeitos conhecidos. O agente também monitoriza análises específicas de contentores, tais como criação privilegiada de contentores, acesso suspeito a servidores API e servidores Secure Shell (SSH) que estão dentro de um contentor Docker.

    Se optar por não instalar os agentes nos seus anfitriões, receberá apenas um subconjunto dos benefícios de proteção contra ameaças e alertas de segurança. Ainda receberá alertas relacionados com análises de rede e comunicações com servidores maliciosos.

    >[!IMPORTANT]
    > Atualmente, não suportamos a instalação do agente Log Analytics nos clusters de serviço Azure Kubernetes que estão a funcionar em conjuntos de escala de máquina virtual.

    Para obter uma lista dos alertas de nível de cluster, consulte a [tabela de alertas de referência](../articles/security-center/alerts-reference.md#alerts-containerhost).


* **Nível de cluster (fornecido pelo Azure Defender for Kubernetes)** - Ao nível do cluster, a proteção contra ameaças baseia-se na análise dos registos de auditoria da Kubernetes. Para ativar esta monitorização **sem agente,** ative o Azure Defender. Se o seu cluster estiver no local ou em outro fornecedor de nuvem, ative [o Arc enabled Kubernetes e a extensão Azure Defender](../articles/security-center/defender-for-kubernetes-azure-arc.md).

    Para gerar alertas a este nível, o Azure Defender monitoriza os registos dos seus clusters. Exemplos de eventos a este nível incluem dashboards kubernetes expostos, criação de papéis privilegiados elevados, e a criação de suportes sensíveis.

    >[!NOTE]
    > O Azure Defender gera alertas de segurança para ações e implementações que ocorrem depois de ter ativado o plano Defender for Kubernetes na sua subscrição. 

    Para obter uma lista dos alertas de nível de cluster, consulte a [tabela de alertas de referência](../articles/security-center/alerts-reference.md#alerts-akscluster).

Além disso, a nossa equipa global de investigadores de segurança monitoriza constantemente o cenário de ameaças. Acrescentam alertas e vulnerabilidades específicos aos contentores à medida que são descobertos.

> [!TIP]
> Pode simular alertas de contentores seguindo as instruções [neste post de blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).