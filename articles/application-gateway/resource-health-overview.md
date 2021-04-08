---
title: Descrição geral do Resource Health do Gateway de Aplicação do Azure
description: Este artigo é uma visão geral da funcionalidade de saúde de recursos para O Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: 7e30a93f8270cfaf8910130cc1e2633bb80c2b8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397177"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Descrição geral do Resource Health do Gateway de Aplicação do Azure

O [Azure Resource Health](../service-health/resource-health-overview.md) ajuda a diagnosticar e obter suporte quando um problema do serviço Azure afetar os seus recursos. Informa-o sobre a saúde atual e passada dos seus recursos. E fornece suporte técnico para ajudá-lo a mitigar problemas.

Para o Gateway de Aplicação, a Resource Health baseia-se em sinais emitidos pela porta de entrada para avaliar se é saudável ou não. Se o portal não for saudável, a Resource Health analisa informações adicionais para determinar a origem do problema. Também identifica ações que a Microsoft está a tomar ou o que pode fazer para corrigir o problema.

Para mais detalhes sobre a forma como a saúde é avaliada, reveja a lista completa de tipos de recursos e verificações de saúde na [Azure Resource Health](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways).


O estado de saúde do Gateway de Aplicação é apresentado como um dos seguintes estados:

## <a name="available"></a>Disponível

Um **estado disponível** significa que o serviço não detetou quaisquer eventos que afetem a saúde do recurso. Verá a notificação **recentemente resolvida** nos casos em que o portal recuperou de tempo de paragem não planeado durante as últimas 24 horas.

![Estado de saúde disponível](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Indisponível

Um **estado indisponível** significa que o serviço detetou uma plataforma em curso ou evento não-plataforma que afeta a saúde do gateway.

### <a name="platform-events"></a>Eventos da plataforma

Os eventos da plataforma são desencadeados por múltiplos componentes da infraestrutura Azure. Incluem ambas as ações programadas (por exemplo, manutenção planeada) e incidentes inesperados (por exemplo, um reboot não planeado do hospedeiro).

A Resource Health fornece detalhes adicionais sobre o evento e o processo de recuperação. Também lhe permite contactar suporte mesmo que não tenha um acordo de suporte ativo da Microsoft.

![Estado indisponível](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Desconhecido

O **estado de** saúde desconhecido indica que a Resource Health não recebe informações sobre o portal há mais de 10 minutos. Este estatuto não é uma indicação definitiva do estado da porta de entrada. Mas é um importante ponto de dados no processo de resolução de problemas.

Se o gateway estiver a funcionar como esperado, o estado muda para **Disponível** após alguns minutos.

Se estiver com problemas, o estado de saúde **desconhecido** pode sugerir que um evento na plataforma está a afetar o portal.

![Estado desconhecido](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Degradado

O estado de saúde **degradado** indica que o seu portal de entrada detetou uma perda de desempenho, embora ainda esteja disponível para utilização.

![Estatuto desgratado](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a resolução de problemas da aplicação Gateway Web Application Firewall (WAF), consulte a Firewall de [Aplicação Web de resolução de problemas (WAF) para gateway de aplicações Azure](../web-application-firewall/ag/web-application-firewall-troubleshoot.md).