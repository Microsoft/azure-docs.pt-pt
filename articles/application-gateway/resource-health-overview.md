---
title: Descrição geral do Estado de funcionamento de recursos de Gateway de aplicação do Azure
description: Este artigo é uma descrição geral da funcionalidade de estado de funcionamento do recurso de Gateway de aplicação do Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659505"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Descrição geral do Estado de funcionamento de recursos de Gateway de aplicação do Azure

[O Azure Resource Health](../service-health/resource-health-overview.md) ajuda-o a diagnosticar e obter suporte quando um problema de serviço do Azure afeta os seus recursos. Informa-o sobre o estado de funcionamento atual e anterior dos seus recursos. E fornece suporte técnico para o ajudar a atenuar os problemas.

Para o Gateway de aplicação, estado de funcionamento do recurso baseia-se em sinais emitidos pelo gateway para avaliar se está em bom estado ou não. Se o gateway está em mau estado de funcionamento, o Resource Health analisa as informações adicionais para determinar a origem do problema. Ele também identifica ações que a Microsoft está a demorar ou o que pode fazer para corrigir o problema.

Para obter mais informações sobre como o estado de funcionamento é analisado, analise a lista completa de tipos de recursos e verificações do Estado de funcionamento no [do Azure Resource Health](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways).


O estado de funcionamento para o Gateway de aplicação é apresentado como um dos seguintes Estados:

## <a name="available"></a>Disponível

Uma **disponível** estado significa que o serviço ainda não detetou quaisquer eventos que afetam o estado de funcionamento do recurso. Verá a **resolvidos recentemente** notificação em casos em que o gateway recuperou de um período de indisponibilidade não planeado durante as últimas 24 horas.

![Estado de funcionamento disponíveis](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Não disponível

Uma **indisponível** estado significa que o serviço detetou uma plataforma em curso ou um evento de plataforma de não que afeta o estado de funcionamento do gateway.

### <a name="platform-events"></a>Eventos de plataforma

Eventos de plataforma são acionados por vários componentes da infraestrutura do Azure. Elas incluem ações agendadas (por exemplo, a manutenção planeada) e incidentes inesperados (por exemplo, um reinício do anfitrião não planeada).

Estado de funcionamento do recurso fornece detalhes adicionais sobre o evento e o processo de recuperação. Ele também permite-lhe contactar o suporte, mesmo se não tiver um contrato de suporte de Microsoft Active Directory.

![Estado indisponível](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Desconhecido

O **desconhecido** estado de funcionamento indica o estado de funcionamento do recurso não recebe informações sobre o gateway para mais de 10 minutos. Este estado não é uma indicação definitiva do Estado do gateway. Mas é um ponto de dados importantes no processo de resolução de problemas.

Se o gateway estiver em execução conforme esperado, o estado muda para **disponível** após alguns minutos.

Se estiver com problemas, o **desconhecido** estado de funcionamento pode sugerir que um evento na plataforma está a afetar o gateway.

![Estado desconhecido](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Degradado

O **Degraded** estado de funcionamento indica que o gateway detetou uma perda de desempenho, embora ele ainda está disponível para utilização.

![Estado degrated](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a resolução de problemas de Application Gateway Web Application Firewall (WAF), veja [resolver problemas de Web Application Firewall (WAF) para o Gateway de aplicação do Azure](web-application-firewall-troubleshoot.md).