---
title: Load Balancer redefinição de TCP on Idle no Azure
titleSuffix: Azure Load Balancer
description: Com este artigo, saiba mais sobre Azure Load Balancer com pacotes TCP RST bidirecionais no tempo limite de ociosidade.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2019
ms.author: allensu
ms.openlocfilehash: b37253f37043d902d33504b99401781eb1c761c5
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075926"
---
# <a name="load-balancer-with-tcp-reset-on-idle-public-preview"></a>Balanceador de Carga com Reposição de TCP quando Inativo (Pré-visualização Pública)

Você pode usar [Standard Load Balancer](load-balancer-standard-overview.md) para criar um comportamento de aplicativo mais previsível para seus cenários habilitando a redefinição de TCP em ociosidade para uma determinada regra. O comportamento padrão de Load Balancer é descartar os fluxos silenciosamente quando o tempo limite de ociosidade de um fluxo for atingido.  Habilitar esse recurso fará com que Load Balancer envie redefinições TCP bidirecionais (pacote TCP RST) no tempo limite de ociosidade.  Isso informará os pontos de extremidade do aplicativo que a conexão atingiu o tempo limite e não é mais utilizável.  Os pontos de extremidade podem estabelecer imediatamente uma nova conexão, se necessário.

![Redefinição de TCP Load Balancer](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)

>[!NOTE] 
>Load Balancer com a funcionalidade de redefinição de TCP no tempo limite de ociosidade está disponível como visualização pública no momento. Esta pré-visualização é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
 
Você altera esse comportamento padrão e habilita o envio de redefinições de TCP no tempo limite de ociosidade em regras de NAT de entrada, regras de balanceamento de carga e [regras de saída](https://aka.ms/lboutboundrules).  Quando habilitado por regra, Load Balancer enviará a redefinição TCP bidirecional (pacotes TCP RST) para os pontos de extremidade do cliente e do servidor no momento do tempo limite de ociosidade para todos os fluxos correspondentes.

Pontos de extremidade que recebem pacotes TCP RST fecham o soquete correspondente imediatamente. Isso fornece uma notificação imediata para os pontos de extremidade que o lançamento da conexão ocorreu e qualquer comunicação futura na mesma conexão TCP falhará.  Os aplicativos podem limpar as conexões quando o soquete fecha e restabelece as conexões, conforme necessário, sem esperar que a conexão TCP eventualmente expire.

Para muitos cenários, isso pode reduzir a necessidade de enviar keepalives de TCP (ou camada de aplicativo) para atualizar o tempo limite de ociosidade de um fluxo. 

Se suas durações ociosas excederem aquelas de permitidas pela configuração ou seu aplicativo mostrar um comportamento indesejável com redefinições TCP habilitadas, talvez você ainda precise usar as keepalives TCP (ou keepalives da camada de aplicativo) para monitorar a vida das conexões TCP.  Além disso, as keepalives também podem permanecer úteis quando a conexão é coficada em algum lugar no caminho, especialmente em keepalives da camada de aplicativo.  

Examine cuidadosamente todo o cenário de ponta a ponta para decidir se você se beneficia com a habilitação de redefinições de TCP, o ajuste do tempo limite de ociosidade e se outras etapas podem ser necessárias para garantir o comportamento desejado do aplicativo.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>Habilitando a redefinição de TCP no tempo limite ocioso

Usando a versão de API 2018-07-01, você pode habilitar o envio de redefinições TCP bidirecionais no tempo limite de ociosidade por regra:

```json
      "loadBalancingRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "inboundNatRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "outboundRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

## <a name="regions"></a>Disponibilidade da região

Disponível em todas as regiões.

## <a name="limitations"></a>Limitações

- O portal não pode ser usado para configurar ou exibir a redefinição de TCP.  Utilize modelos, REST API, Az CLI 2.0 ou PowerShell.
- TCP RST é enviado somente durante a conexão TCP no estado estabelecido.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [Balanceador de carga Standard](load-balancer-standard-overview.md).
- Saiba mais sobre [as regras de saída](load-balancer-outbound-rules-overview.md).
