---
title: Testes de simulação de proteção DDoS Azure
description: Saiba como testar através de simulações
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: eff738e24b3abce52e80291c55a3ae64c3c8c853
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905498"
---
# <a name="test-through-simulations"></a>Teste através de simulações

É uma boa prática testar as suas suposições sobre como os seus serviços vão responder a um ataque realizando simulações periódicas. Durante os testes, valide que os seus serviços ou aplicações continuam a funcionar como esperado e não há nenhuma perturbação na experiência do utilizador. Identificar lacunas do ponto de vista da tecnologia e do processo e incorporá-las na estratégia de resposta do DDoS. Recomendamos que realize tais testes em ambientes de preparação ou em horas não de ponta para minimizar o impacto no ambiente de produção.

Estabelecemos uma parceria com [a BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud), um gerador de tráfego de autosserviço, para construir uma interface onde os clientes do Azure podem gerar tráfego contra pontos finais públicos habilitados pela DDoS Protection para simulações. Pode utilizar a simulação para:

- Valide como a Azure DDoS Protection ajuda a proteger os seus recursos Azure de ataques DDoS.
- Otimize o seu processo de resposta a incidentes sob ataque DDoS.
- Conformidade do DDoS do documento.
- Treine as suas equipas de segurança de rede.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de poder completar os passos neste tutorial, tem primeiro de criar um [plano de proteção Azure DDoS Standard](manage-ddos-protection.md) com endereços IP públicos protegidos.
- Primeiro tem de criar uma conta com [a BreakingPoint Cloud.](http://breakingpoint.cloud/) 

## <a name="configure-a-ddos-test-attack"></a>Configurar um ataque de teste DDoS

1. Introduza ou selecione os seguintes valores e, em seguida, selecione **Iniciar o teste** :

    |Definição        |Valor                                              |
    |---------      |---------                                          |
    |Endereço IP alvo           | Insira um dos seus endereços IP públicos que pretende testar.                     |
    |Número da Porta   | Insira _443_ .                       |
    |Perfil DDos | Selecione **O Dilúvio DE SINA TCP** .|
    |Tamanho do teste       | Selecione **200K pps, 100 Mbps e 8 IPs de origem.**                                  |
    |Duração do teste | Selecione **10 Minutos** .|

Deve agora parecer assim:

![DDos Exemplo de simulação de ataque: BreakingPoint Cloud](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>Monitorize e valide

1. Faça login https://portal.azure.com e vá para a sua subscrição.
1. Selecione o endereço IP público em que testou o ataque.
1. Em **Monitorização** , selecione **Métricas** .
1. Para **métrica** , selecione _Under DDoS attack or not_ .

Uma vez que o recurso está sob ataque, deve ver que o valor muda de **0** para **1** , como a seguinte imagem:

![DDos Exemplo de simulação de ataque: Portal](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba [como visualizar e configurar a telemetria de proteção DDoS](telemetry-monitoring-alerting.md).
- Saiba como [configurar relatórios de mitigação de ataques dDoS e registos de fluxo](reports-and-flow-logs.md).
- Saiba como envolver a [resposta rápida do DDoS](ddos-rapid-response.md).