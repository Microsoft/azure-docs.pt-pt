---
title: Recuperação geo-desastre da Nuvem de primavera de Azure Microsoft Docs
description: Saiba como proteger a sua aplicação Spring Cloud de interrupções regionais
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: 81ca6b2c365b0dd8a249a337f51d78516cb2cc61
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657189"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Recuperação do desastre da Nuvem de primavera de Azure

Este artigo explica algumas estratégias que pode usar para proteger as suas aplicações Azure Spring Cloud de experimentar o tempo de inatividade.  Qualquer região ou centro de dados pode experimentar o tempo de inatividade causado por desastres regionais, mas um planeamento cuidadoso pode mitigar o impacto nos seus clientes.

## <a name="plan-your-application-deployment"></a>Planeie a sua implementação de aplicações

As aplicações Azure Spring Cloud funcionam numa região específica.  O Azure funciona em várias geografias em todo o mundo. Uma geografia Azure é uma área definida do mundo que contém pelo menos uma região azure. Uma região de Azure é uma área dentro de uma geografia, contendo um ou mais centros de dados.  Cada região de Azure é emparelhada com outra região dentro da mesma geografia, fazendo juntos um par regional. O Azure serializa atualizações de plataformas (manutenção planeada) em pares regionais, garantindo que apenas uma região em cada par é atualizada de cada vez. Em caso de paralisação que afete várias regiões, pelo menos uma região de cada par será priorizada para recuperação.

Garantir uma elevada disponibilidade e proteção contra desastres requer que você implemente as suas aplicações de Nuvem de primavera para várias regiões.  O Azure fornece uma lista de [regiões emparelhadas](../best-practices-availability-paired-regions.md) para que possa planear as suas implantações da Nuvem de primavera para pares regionais.  Recomendamos que considere três fatores fundamentais na conceção da sua arquitetura de microserviços: disponibilidade da região, regiões emparelhadas com Azure e disponibilidade de serviço.

*  Disponibilidade da região: Escolha uma área geográfica próxima dos seus utilizadores para minimizar o atraso de rede e o tempo de transmissão.
*  Regiões emparelhadas com Azure: Escolha regiões emparelhadas dentro da sua área geográfica escolhida para garantir atualizações coordenadas da plataforma e priorizar os esforços de recuperação, se necessário.
*  Disponibilidade do serviço: Decida se as suas regiões emparelhadas devem funcionar quentes/quentes, quentes/quentes ou quentes/frios.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Utilize o Gestor de Tráfego Azure para direcionar o tráfego

[O Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) fornece o equilíbrio de carga de tráfego baseado no DNS e pode distribuir o tráfego da rede por várias regiões.  Utilize o Gestor de Tráfego Azure para direcionar os clientes para a instância de serviço azure spring cloud mais próxima.  Para melhor desempenho e redundância, direcione todo o tráfego de aplicações através do Azure Traffic Manager antes de enviá-lo para o seu serviço Azure Spring Cloud.

Se tiver aplicações Azure Spring Cloud em várias regiões, utilize o Gestor de Tráfego Azure para controlar o fluxo de tráfego para as suas aplicações em cada região.  Defina um ponto final do Gestor de Tráfego Azure para cada serviço utilizando o IP de serviço. Os clientes devem ligar-se a um nome DNS do Gestor de Tráfego Azure que aponta para o serviço Azure Spring Cloud.  A carga do Gestor de Tráfego Azure equilibra o tráfego nos pontos finais definidos.  Se um desastre atingir um centro de dados, o Gestor de Tráfego azure direcionará o tráfego daquela região para o seu par, garantindo a continuidade do serviço.

## <a name="create-azure-traffic-manager-for-azure-spring-cloud"></a>Crie o Gestor de Tráfego Azure para a Nuvem de primavera Azure

1. Crie a Nuvem de primavera Azure em duas regiões diferentes.
Você precisará de duas instâncias de serviço de Azure Spring Cloud implantadas em duas regiões diferentes (Leste dos EUA e Europa Ocidental). Lance uma aplicação Azure Spring Cloud existente utilizando o portal Azure para criar duas instâncias de serviço. Cada um servirá como ponto final primário e falhado para o tráfego. 

**Duas instâncias de serviço informação:**

| Nome do Serviço | Localização | Aplicação |
|--|--|--|
| amostra de serviço-a | E.U.A. Leste | gateway / auth-service / serviço de conta |
| serviço-amostra-b | Europa ocidental | gateway / auth-service / serviço de conta |

2. Configurar o Domínio Personalizado para o Serviço Siga [o Documento de Domínio Personalizado](spring-cloud-tutorial-custom-domain.md) para configurar o domínio personalizado para estas duas instâncias de serviço existentes. Após a configuração bem sucedida, ambas as instâncias de serviço ligar-se-ão ao domínio personalizado: bcdr-test.contoso.com

3. Criar um gestor de tráfego e dois pontos finais: Criar um perfil de Gestor de [Tráfego utilizando o portal Azure](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile).

Aqui está o perfil do gestor de tráfego:
* Nome DNS do Gestor de Tráfego:`http://asc-bcdr.trafficmanager.net`
* Perfis de ponto final: 

| Perfil | Tipo | Destino | Prioridade | Definições personalizadas do cabeçalho |
|--|--|--|--|--|
| Perfil endpoint A | Ponto Final Externo | service-sample-a.asc-test.net | 1 | anfitrião: bcdr-test.contoso.com |
| Perfil do ponto final B | Ponto Final Externo | service-sample-b.asc-test.net | 2 | anfitrião: bcdr-test.contoso.com |

4. Crie um disco CNAME na Zona DNS: bcdr-test.contoso.com asc-bcdr.trafficmanager.net CNAME. 

5. Agora, o ambiente está completamente preparado. Os clientes devem poder aceder à aplicação através de: bcdr-test.contoso.com