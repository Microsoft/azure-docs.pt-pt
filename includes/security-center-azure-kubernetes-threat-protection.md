---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: 1b650fa5a0e9ba2f7019e6e67690d9d1fd65e72a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90894902"
---
O Security Center fornece proteção contra ameaças em tempo real para os seus ambientes contentorizados e gera alertas para atividades suspeitas. Pode utilizar estas informações para remediar rapidamente problemas de segurança e aumentar a segurança dos contentores.

O Centro de Segurança fornece proteção contra ameaças a diferentes níveis: 

* **Nível de anfitrião (fornecido pelo Azure Defender para servidores)** - Utilizando o mesmo agente Log Analytics que o Security Center utiliza em outros VMs, o Azure Defender monitoriza os seus nós Linux AKS para atividades suspeitas, tais como deteção de conchas web e ligação com endereços IP suspeitos conhecidos. O agente também monitoriza análises específicas de contentores, tais como criação privilegiada de contentores, acesso suspeito a servidores API e servidores Secure Shell (SSH) que estão dentro de um contentor Docker.

    >[!IMPORTANT]
    > Se optar por não instalar os agentes nos seus anfitriões, receberá apenas um subconjunto dos benefícios de proteção contra ameaças e alertas de segurança. Ainda receberá alertas relacionados com análises de rede e comunicações com servidores maliciosos.

    Para obter uma lista dos alertas de nível de anfitrião AKS, consulte a [tabela de alertas de referência](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost).


* **Nível de cluster AKS (fornecido pelo Azure Defender for Kubernetes)** - Ao nível do cluster, a proteção contra ameaças baseia-se na análise dos registos de auditoria da Kubernetes. Para ativar esta monitorização **sem agente,** ative o Azure Defender. Para gerar alertas a este nível, o Security Center monitoriza os seus serviços geridos pela AKS utilizando os registos recuperados pela AKS. Exemplos de eventos a este nível incluem dashboards kubernetes expostos, criação de papéis privilegiados elevados, e a criação de suportes sensíveis.

    >[!NOTE]
    > O Security Center gera alertas de segurança para as ações e implementações do Serviço Azure Kubernetes que ocorrem após a opção Kubernetes estar ativada nas definições de subscrição. 

    Para obter uma lista dos alertas de nível de cluster AKS, consulte a [tabela de alertas de referência](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster).

Além disso, a nossa equipa global de investigadores de segurança monitoriza constantemente o cenário de ameaças. Acrescentam alertas e vulnerabilidades específicos aos contentores à medida que são descobertos.

> [!TIP]
> Pode simular alertas de contentores seguindo as instruções [neste post de blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).