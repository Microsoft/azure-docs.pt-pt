---
title: 'Azure ExpressRoute: requisitos QoS'
description: Esta página fornece requisitos detalhados para configurar e gerir o QoS. O Skype para serviços de negócios/voz é discutido.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: cherylmc
ms.openlocfilehash: debc5d91478d0a5c3cc16c7b09f5713ba09b467e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74080104"
---
# <a name="expressroute-qos-requirements"></a>Requisitos do QoS do ExpressRoute
O Skype para Empresas tem várias cargas de trabalho que exigem um tratamento do QoS diferenciado. Se planear consumir serviços de voz através do ExpressRoute, deve cumprir os requisitos descritos abaixo.

![](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> Os requisitos do QoS aplicam-se apenas ao peering da Microsoft. Os valores DSCP no seu tráfego recebido de rede no peering público do Azure e no peering privado do Azure serão repostos a 0. 
> 
> 

A tabela seguinte fornece uma lista de marcas DSCP usadas pelas Equipas microsoft e Skype para negócios. Para obter mais informações, veja [Gerir QoS para Skype para Empresas](https://docs.microsoft.com/SkypeForBusiness/manage/network-management/qos/managing-quality-of-service-QoS).

| **Classe de Tráfego** | **Tratamento (Marcação DSCP)** | **Microsoft Teams e Skype para Business Workloads** |
| --- | --- | --- |
| **Voz** |EF (46) |Skype / Microsoft Teams / Voz Lync |
| **Interativo** |AF41 (34) |Video, VBSS |
| |AF21 (18) |Partilha de aplicações | 
| **Predefinição** |AF11 (10) |Transferência de ficheiros |
| |CS0 (0) |Tudo o resto |

* Deve classificar as cargas de trabalho e marcar os valores DSCP corretos. Siga as orientações fornecidas [aqui](https://docs.microsoft.com/SkypeForBusiness/manage/network-management/qos/configuring-port-ranges-for-your-skype-clients#configure-quality-of-service-policies-for-clients-running-on-windows-10) para saber como configurar as marcações DSCP na sua rede.
* Deve configurar e suportar várias filas do QoS na rede. A voz deve ser uma classe autónoma e receber o tratamento EF especificado no [RFC 3246](https://www.ietf.org/rfc/rfc3246.txt). 
* Pode decidir o mecanismo de colocação em fila, a política de deteção de congestionamento e a alocação de largura de banda por classe de tráfego. No entanto, a marcação DSCP para cargas de trabalho do Skype para Empresas tem de ser preservada. Se estiver a utilizar marcações DSCP não listadas acima, por exemplo, AF31 (26), terá de reescrever este valor DSCP para 0 antes de enviar o pacote à Microsoft. A Microsoft só envia pacotes marcados com o valor DSCP mostrado na tabela acima. 

## <a name="next-steps"></a>Próximos passos
* Veja os requisitos para [Encaminhamento](expressroute-routing.md) e [NAT](expressroute-nat.md).
* Consulte as ligações seguintes para configurar a ligação do ExpressRoute.
  
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-classic.md)
  * [Configure o encaminhamento](expressroute-howto-routing-classic.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-classic.md)

