---
title: Testes de simulação de proteção DDoS Azure
description: Saiba como testar através de simulações
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 4e6c6b441684d3877a8b85f40b650b257a5159da
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106076"
---
# <a name="test-through-simulations"></a>Testar através de simulações

É uma boa prática testar as suas suposições sobre como os seus serviços vão responder a um ataque realizando simulações periódicas. Durante os testes, valide que os seus serviços ou aplicações continuam a funcionar como esperado e não há nenhuma perturbação na experiência do utilizador. Identificar lacunas do ponto de vista da tecnologia e do processo e incorporá-las na estratégia de resposta do DDoS. Recomendamos que realize tais testes em ambientes de preparação ou em horas não de ponta para minimizar o impacto no ambiente de produção.

Estabelecemos uma parceria com a [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud), um gerador de tráfego self-service, para criar uma interface onde os clientes do Azure podem gerar tráfego para pontos finais públicos com o DDoS Protection ativado para simulações. Pode utilizar a simulação para:

- Validar como o Azure DDoS Protection ajuda a proteger os recursos do Azure de ataques DDoS.
- Otimizar o processo de resposta a incidentes sob ataque DDoS.
- Documentar a conformidade do DDoS.
- Preparar equipas de segurança de rede.

> [!NOTE]
> BreakingPoint Cloud só está disponível para a nuvem pública.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de poder completar os passos neste tutorial, tem primeiro de criar um [plano de proteção Azure DDoS Standard](manage-ddos-protection.md) com endereços IP públicos protegidos.
- Primeiro tem de criar uma conta com [a BreakingPoint Cloud.](http://breakingpoint.cloud/) 

## <a name="configure-a-ddos-test-attack"></a>Configurar um ataque de teste DDoS

1. Introduza ou selecione os seguintes valores e, em seguida, selecione **Iniciar o teste**:

    |Definição        |Valor                                              |
    |---------      |---------                                          |
    |Endereço IP alvo           | Insira um dos seus endereços IP públicos que pretende testar.                     |
    |Número da Porta   | Insira _443_.                       |
    |Perfil DDos | Os valores possíveis `DNS Flood` incluem, , , , , , , , `NTPv2 Flood` , , , `SSDP Flood` , `TCP SYN Flood` `UDP 64B Flood` `UDP 128B Flood` `UDP 256B Flood` `UDP 512B Flood` `UDP 1024B Flood` `UDP 1514B Flood` `UDP Fragmentation` `UDP Memcached` .|
    |Tamanho do teste       | Os valores possíveis `100K pps, 50 Mbps and 4 source IPs` `200K pps, 100 Mbps and 8 source IPs` incluem, . . `400K pps, 200Mbps and 16 source IPs` `800K pps, 400 Mbps and 32 source IPs` .                                  |
    |Duração do teste | Os valores possíveis `10 Minutes` incluem, , , , . `15 Minutes` `20 Minutes` `25 Minutes` `30 Minutes` .|

Deve agora parecer assim:

![DDos Exemplo de simulação de ataque: BreakingPoint Cloud](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>Monitorize e valide

1. Faça login https://portal.azure.com e vá para a sua subscrição.
1. Selecione o endereço IP público em que testou o ataque.
1. Em **Monitorização**, selecione **Métricas**.
1. Para **métrica**, selecione _Under DDoS attack or not_.

Uma vez que o recurso está sob ataque, deve ver que o valor muda de **0** para **1**, como a seguinte imagem:

![DDos Exemplo de simulação de ataque: Portal](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

### <a name="breakingpoint-cloud-api-script"></a>Script de API de nuvem de breakingPoint

Este [script API](https://aka.ms/ddosbreakingpoint) pode ser usado para automatizar os testes de DDoS executando uma vez ou usando cron para agendar testes regulares. Isto é útil para validar que o seu registo está configurado corretamente e que os procedimentos de deteção e resposta são eficazes. Os scripts requerem um Linux OS (testado com Ubuntu 18.04 LTS) e Python 3. Instale pré-requisitos e cliente API utilizando o script incluído ou utilizando a documentação no website [da BreakingPoint Cloud.](http://breakingpoint.cloud/)

## <a name="next-steps"></a>Passos seguintes

- Saiba [como visualizar e configurar a telemetria de proteção DDoS](telemetry.md).
- Saiba como [visualizar e configurar a registo de diagnóstico do DDoS](diagnostic-logging.md).
- Saiba como envolver a [resposta rápida do DDoS](ddos-rapid-response.md).
