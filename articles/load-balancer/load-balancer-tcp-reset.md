---
title: Reposição TCP do Balanceador de carga em inatividade no Azure
titlesuffix: Azure Load Balancer
description: Balanceador de carga com pacotes TCP RST bidirecional no tempo limite de inatividade
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/29/2019
ms.author: kumud
ms.openlocfilehash: 52524e6291faae8ccc27c0d53e9e38ab63a4c8d5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58132434"
---
# <a name="load-balancer-with-tcp-reset-on-idle-public-preview"></a>Balanceador de carga com a reposição TCP em inatividade (pré-visualização pública)

Pode usar [Balanceador de carga Standard](load-balancer-standard-overview.md) para criar um comportamento mais previsível em termos de aplicação para os seus cenários ao ativar a reposição TCP inativo para uma determinada regra. Comportamento do Balanceador de carga predefinido é descartar silenciosamente fluxos quando é atingido o tempo limite de inatividade de um fluxo.  Ativar esta funcionalidade fará com que o Balanceador de carga enviar a bidirecional TCP redefine (pacotes de TCP RST) de mensagens em fila no tempo limite de inatividade.  Isso informará seus pontos de extremidade do aplicativo que a ligação foi excedido e já não é utilizável.  Pontos de extremidade imediatamente podem estabelecer uma nova ligação se for necessário.

![Reposição de TCP de Balanceador de carga](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)

>[!NOTE] 
>Balanceador de carga com reposição na funcionalidade de tempo limite de inatividade de TCP está disponível como pré-visualização pública neste momento. Esta pré-visualização é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
 
Alterar este comportamento predefinido e ativar TCP redefine a enviar no tempo limite de inatividade em regras NAT de entrada, regras de balanceamento de carga e [regras de saída](https://aka.ms/lboutboundrules).  Quando ativado por regra, o Balanceador de carga irá enviar bidirecional (pacotes de TCP RST) de reposição de TCP para pontos finais de cliente e servidor no momento do tempo limite de inatividade para todos os fluxos correspondentes.

Pontos finais receber pacotes de TCP RST fechar o soquete correspondente imediatamente. Isso fornece uma notificação imediata para os pontos finais que ocorreu o lançamento da ligação e qualquer comunicação futura na mesma conexão TCP irá falhar.  Aplicativos podem remover ligações quando socket fecha e restabelecer as ligações conforme necessário, sem esperar pela conexão TCP, eventualmente, o tempo limite.

Para muitos cenários, isso pode reduzir a necessidade de TCP de envio (ou camada de aplicativo) keepalives para atualizar o tempo limite de inatividade de um fluxo. 

Se sua durações Inativas excederem os SLAs dos permitidos pela configuração ou a aplicação mostra um comportamento indesejado com TCP redefine ativado, ainda terá de utilizar TCP keepalives (ou keepalives de camada de aplicativo) para monitorizar o liveness das conexões TCP.  Além disso, também pode permanecer útil para quando a ligação é transmitidas por proxy em algum lugar no caminho, particularmente keepalives de camada de aplicativo keepalives.  

Examine cuidadosamente todo o cenário de ponta a ponta para decidir se se beneficiar da ativação TCP redefine, ajustar o tempo limite de inatividade, e se podem ser necessários passos adicionais para garantir o comportamento do aplicativo desejado.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>Ativar a reposição de TCP de mensagens em fila no tempo limite de inatividade

Utilizar a versão 2018-01 07 de API, pode permitir o envio de bidirecional TCP redefine no tempo limite de inatividade numa base por regra:

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

## <a name="regions"></a> Disponibilidade de região

Disponível em todas as regiões.

## <a name="limitations"></a>Limitações

- Não é possível utilizar o portal para configurar ou visualizar a reposição de TCP.  Utilize modelos, REST API, Az CLI 2.0 ou PowerShell.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [Balanceador de carga Standard](load-balancer-standard-overview.md).
- Saiba mais sobre [regras de saída](load-balancer-outbound-rules-overview.md).
