---
title: Modelo de serviço de esprevação Azure
description: Saiba como embarcar no Azure Peering Service
services: peering-service
documentationcenter: na
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 7a257b4c2bea3bd3384a55c0a6b85d7fdd2ca583
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84872079"
---
# <a name="onboarding-peering-service-model"></a>Modelo de serviço de peering de bordo

O processo de onboarding do Serviço de Observação é composto por dois modelos listados abaixo:

 - Ligação de serviço de peering de bordo

 - Telemetria de conexão de serviço de esprevação de bordo

Os planos de ação para os modelos acima listados são descritos como abaixo:

| **Passo** | **Ação**| **O que obtém**|
|-----------|---------|---------|---------|
|1|Cliente para providenciar a conectividade a partir de um parceiro de conectividade (sem interação com a Microsoft). |Um fornecedor de Internet que esteja bem ligado à Microsoft e que cumpra os requisitos técnicos para uma conectividade performante e fiável para a Microsoft.  |
|2 (Opcional)|O cliente regista as localizações no portal Azure. Uma localização é definida por: ISP/IXP Name, localização física do site do cliente (nível estatal), Prefix IP dado à localização pelo Fornecedor de Serviços ou pela empresa.  |Telemetria: Monitorização da rota da Internet, priorização de tráfego da Microsoft para a localização POP de ponta mais próxima do utilizador. |



## <a name="onboarding-peering-service-connection"></a>Ligação de serviço de peering de bordo

Para embarcar a ligação do Serviço de Peering, faça o seguinte:

Trabalhe com o fornecedor de Serviços de Internet (ISP) ou com o Internet Exchange (IX) Partner para obter o Peering Service para ligar a sua rede à rede Microsoft.

Certifique-se de que os [fornecedores de conectividade](location-partners.md) são parceiros com a Microsoft para o Serviço de Peering. 

## <a name="onboarding-peering-service-connection-telemetry"></a>Telemetria de conexão de serviço de esprevação de bordo

Os clientes podem optar pela sua telemetria, como a análise de rotas BGP, para monitorizar a latência e desempenho da rede ao aceder à rede microsoft. Isto pode ser conseguido registando a ligação no portal Azure.

Para embarcar a telemetria do Serviço de Observação de Pares, o cliente deve registar a ligação do Serviço de Observação de Pares no portal Azure. Consulte o [Serviço de Observação de Registos - Portal Azure](azure-portal.md) para conhecer o procedimento.

Depois disso, pode medir a telemetria referindo-se [aqui.](measure-connection-telemetry.md)

## <a name="next-steps"></a>Passos seguintes

Para aprender passo a passo processo sobre como registar a ligação do Serviço de Peering, consulte [o Serviço de Peering Register - Portal Azure](azure-portal.md).

Para saber mais sobre a telemetria de conexão de medida, consulte [a telemetria de conexão medida](measure-connection-telemetry.md).
