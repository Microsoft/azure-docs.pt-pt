---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: ba695337a1da1e440895f6b9f6fe2eb34f5041e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800614"
---
O Security Center fornece proteção contra ameaças em tempo real para os seus ambientes contentorizados e gera alertas para atividades suspeitas. Pode utilizar estas informações para remediar rapidamente problemas de segurança e aumentar a segurança dos contentores.

O Centro de Segurança fornece proteção contra ameaças a diferentes níveis: 

* **Nível de anfitrião** - O agente Log Analytics monitoriza o Linux para atividades suspeitas. O agente aciona alertas para atividades suspeitas originárias do nó ou de um contentor que nele corre. Exemplos de tais atividades incluem deteção de conchas web e conexão com endereços IP suspeitos conhecidos.

    Para uma visão mais profunda da segurança do seu ambiente contentorizado, o agente monitoriza análises específicas do contentor. Irá desencadear alertas para eventos como a criação de contentores privilegiados, acesso suspeito aos servidores da API e servidores Secure Shell (SSH) que estão dentro de um contentor do Docker.

    >[!IMPORTANT]
    > Se optar por não instalar os agentes nos seus anfitriões, receberá apenas um subconjunto dos benefícios de proteção contra ameaças e alertas de segurança. Ainda receberá alertas relacionados com análises de rede e comunicações com servidores maliciosos.

    Para obter uma lista dos alertas de nível de anfitrião AKS, consulte a [tabela de alertas de referência](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost).


* Ao **nível do cluster AKS,** a proteção contra ameaças baseia-se na análise dos registos de auditoria da Kubernetes. Para ativar esta monitorização **sem agente,** adicione a opção Kubernetes à sua subscrição a partir da página **de definições & de preços** (ver [preços).](https://docs.microsoft.com/azure/security-center/security-center-pricing) Para gerar alertas a este nível, o Security Center monitoriza os seus serviços geridos pela AKS utilizando os registos recuperados pela AKS. Exemplos de eventos a este nível incluem dashboards kubernetes expostos, criação de papéis privilegiados elevados, e a criação de suportes sensíveis.

    >[!NOTE]
    > O Security Center gera alertas de segurança para as ações e implementações do Serviço Azure Kubernetes que ocorrem após a opção Kubernetes estar ativada nas definições de subscrição. 

    Para obter uma lista dos alertas de nível de cluster AKS, consulte a [tabela de alertas de referência](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster).

Além disso, a nossa equipa global de investigadores de segurança monitoriza constantemente o cenário de ameaças. Acrescentam alertas e vulnerabilidades específicos aos contentores à medida que são descobertos.

> [!TIP]
> Pode simular alertas de contentores seguindo as instruções [neste post de blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).