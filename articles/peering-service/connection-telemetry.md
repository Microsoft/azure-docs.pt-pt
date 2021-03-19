---
title: Telemetria de conexão Azure Peering Service
description: Saiba mais sobre a telemetria de conexão microsoft Azure Peering Service
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0e54b20cc6aefa69226c24f557ae02166dc4b4b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84872009"
---
# <a name="peering-service-connection-telemetry"></a>Telemetria de conexão de serviço de espreitar

A telemetria de conexão fornece insights recolhidos para a conectividade entre a localização do cliente e a rede Microsoft. Os clientes podem obter telemetria para a ligação Azure Peering Service registando a ligação no portal Azure. Esta funcionalidade fornece segurança pré-fixa e insights sobre o desempenho da rede.


A telemetria de ligação é composta pelos seguintes âmbitos:

### <a name="latency-measurement"></a>Medição de latência

 A latência é medida do cliente para o Microsoft Edge PoP para os prefixos registados no Serviço de Peering.

### <a name="route-prefix-monitoring-and-protection"></a>Monitorização e proteção do prefixo da rota

Os caminhos de encaminhamento são monitorizados para qualquer atividade suspeita que seja capturada em registos de eventos. Por exemplo, os registos de eventos são criados para alguns destes fatores:

- Sequestros de prefixos
- Retirada pré-fixa
- Fugas de rota

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a ligação do Serviço de Peering, consulte [a ligação do Serviço de Peering](connection.md).
- Para embarcar numa ligação de Serviço de Peering, consulte [a bordo de um modelo de Serviço de Observação.](onboarding-model.md)
- Para medir a telemetria, consulte [a telemetria de ligação medida](measure-connection-telemetry.md).
