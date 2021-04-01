---
title: Azure Spring Cloud recuperação de geo-desastres | Microsoft Docs
description: Saiba como proteger a sua aplicação Cloud spring contra interrupções regionais
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: e18193b40bb7c59a21a279f451673dc7d11140e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92092907"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Recuperação de desastres em Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java ✔️ C #

Este artigo explica algumas estratégias que pode usar para proteger as suas aplicações Azure Spring Cloud de experimentar tempo de inatividade.  Qualquer região ou centro de dados pode experimentar um tempo de inatividade causado por desastres regionais, mas um planeamento cuidadoso pode atenuar o impacto nos seus clientes.

## <a name="plan-your-application-deployment"></a>Planeie a sua implementação de candidaturas

As aplicações Azure Spring Cloud funcionam numa região específica.  O Azure funciona em várias geografias em todo o mundo. Uma geografia azul é uma área definida do mundo que contém pelo menos uma região de Azure. Uma região de Azure é uma área dentro de uma geografia, contendo um ou mais centros de dados.  Cada região de Azure é emparelhada com outra região dentro da mesma geografia, fazendo juntos um par regional. O Azure serializa as atualizações da plataforma (manutenção planeada) em pares regionais, garantindo que apenas uma região em cada par é atualizada de cada vez. Em caso de paralisação que afete várias regiões, pelo menos uma região em cada par será priorizada para a recuperação.

Garantir uma elevada disponibilidade e proteção contra desastres requer que implemente as suas aplicações Cloud spring em várias regiões.  O Azure fornece uma lista de [regiões emparelhadas](../best-practices-availability-paired-regions.md) para que possa planear as suas missões da Cloud da primavera para pares regionais.  Recomendamos que considere três fatores-chave ao conceber a sua arquitetura de micro-serviços: disponibilidade da região, regiões emparelhadas Azure e disponibilidade de serviços.

*  Disponibilidade da região: Escolha uma área geográfica próxima dos seus utilizadores para minimizar o atraso da rede e o tempo de transmissão.
*  Regiões emparelhadas Azure: Escolha regiões emparelhadas dentro da área geográfica escolhida para garantir atualizações coordenadas da plataforma e esforços de recuperação priorizados, se necessário.
*  Disponibilidade do serviço: Decida se as suas regiões emparelhadas devem funcionar quentes/quentes, quentes/quentes ou quentes/frios.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Use o Gestor de Tráfego Azure para encaminhar o tráfego

[O Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) fornece o equilíbrio de carga de tráfego baseado em DNS e pode distribuir o tráfego da rede em várias regiões.  Utilize o Azure Traffic Manager para direcionar os clientes para a mais próxima instância de serviço Azure Spring Cloud para eles.  Para melhor desempenho e redundância, direcione todo o tráfego de aplicações através do Azure Traffic Manager antes de enviá-lo para o seu serviço Azure Spring Cloud.

Se tiver aplicações Azure Spring Cloud em várias regiões, use o Azure Traffic Manager para controlar o fluxo de tráfego para as suas aplicações em cada região.  Defina um ponto final do Azure Traffic Manager para cada serviço utilizando o IP de serviço. Os clientes devem ligar-se a um nome DNS do Azure Traffic Manager que aponta para o serviço Azure Spring Cloud.  A carga do Gestor de Tráfego Azure equilibra o tráfego através dos pontos finais definidos.  Se um desastre atingir um centro de dados, o Azure Traffic Manager irá direcionar o tráfego daquela região para o seu par, garantindo a continuidade do serviço.

## <a name="create-azure-traffic-manager-for-azure-spring-cloud"></a>Criar gestor de tráfego Azure para Azure Spring Cloud

1. Crie a nuvem de primavera Azure em duas regiões diferentes.
Você precisará de duas instâncias de serviço da Azure Spring Cloud implantadas em duas regiões diferentes (Leste dos EUA e Europa Ocidental). Lançar uma aplicação Azure Spring Cloud existente utilizando o portal Azure para criar duas instâncias de serviço. Cada um servirá como ponto final primário e de saída para o tráfego. 

**Duas instâncias de serviço informações:**

| Nome do Serviço | Localização | Aplicação |
|--|--|--|
| serviço-amostra-a | E.U.A. Leste | gateway / auth-service / serviço de conta |
| amostra de serviço-b | Europa Ocidental | gateway / auth-service / serviço de conta |

2. Configurar o Domínio Personalizado para o Serviço Seguir [o Documento de Domínio Personalizado](spring-cloud-tutorial-custom-domain.md) para configurar o domínio personalizado para estas duas instâncias de serviço existentes. Após a configuração bem sucedida, ambas as instâncias de serviço ligar-se-ão ao domínio personalizado: bcdr-test.contoso.com

3. Criar um gestor de tráfego e dois pontos finais: [Criar um perfil de Gestor de Tráfego utilizando o portal Azure](../traffic-manager/quickstart-create-traffic-manager-profile.md).

Aqui está o perfil do gestor de tráfego:
* Nome DNS do Gestor de Tráfego: `http://asc-bcdr.trafficmanager.net`
* Perfis de ponto final: 

| Perfil | Tipo | Destino | Prioridade | Definições personalizadas do cabeçalho |
|--|--|--|--|--|
| Ponto final um perfil | Ponto final externo | service-sample-a.asc-test.net | 1 | anfitrião: bcdr-test.contoso.com |
| Perfil do ponto final B | Ponto final externo | service-sample-b.asc-test.net | 2 | anfitrião: bcdr-test.contoso.com |

4. Crie um disco CNAME na Zona DNS: bcdr-test.contoso.com asc-bcdr.trafficmanager.net CNAME. 

5. Agora, o ambiente está completamente preparado. Os clientes devem poder aceder à aplicação através de: bcdr-test.contoso.com

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Implemente a sua primeira aplicação Azure Spring Cloud](spring-cloud-quickstart.md)
