---
title: Controlar o tráfego com o Gestor de Tráfego
description: Encontre as melhores práticas para configurar o Gestor de Tráfego Azure quando o integrar no Serviço de Aplicações Azure.
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.topic: article
ms.date: 02/25/2016
ms.custom: seodec18
ms.openlocfilehash: 200effab70b369d69b4e89b1901578ecfe1a1b87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684092"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Controlar o tráfego do Serviço de Aplicações Azure com o Gestor de Tráfego Azure
> [!NOTE]
> Este artigo fornece informações sumárias para o Microsoft Azure Traffic Manager no que diz respeito ao Serviço de Aplicações Azure. Mais informações sobre o próprio Gestor de Tráfego do Azure podem ser encontradas visitando os links no final deste artigo.
> 
> 

## <a name="introduction"></a>Introdução
Pode utilizar o Gestor de Tráfego do Azure para controlar como os pedidos de clientes Web são distribuídos para as aplicações no Serviço de Aplicações do Azure. Quando os pontos finais do Serviço de Aplicações são adicionados a um perfil do Gestor de Tráfego do Azure, o Gestor de Tráfego do Azure mantém um registo do estado das suas aplicações do Serviço de Aplicações (em execução, parado ou eliminado), para que possa decidir quais desses pontos finais deve receber tráfego.

## <a name="routing-methods"></a>Métodos de encaminhamento
O Azure Traffic Manager utiliza quatro métodos de encaminhamento diferentes. Estes métodos são descritos na seguinte lista, uma vez que dizem respeito ao Serviço de Aplicações Azure.

* ** [Prioridade](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method):** utilize uma aplicação primária para todo o tráfego e forneça cópias de segurança caso as principais ou as aplicações de backup não estejam disponíveis.
* ** [Ponderado](../traffic-manager/traffic-manager-routing-methods.md#weighted):** distribua o tráfego através de um conjunto de aplicações, uniformemente ou de acordo com os pesos, que define.
* ** [Desempenho](../traffic-manager/traffic-manager-routing-methods.md#performance):** quando tiver aplicações em diferentes locais geográficos, utilize a aplicação "mais próxima" em termos da menor latência da rede.
* ** [Geographic](../traffic-manager/traffic-manager-routing-methods.md#geographic):** utilizadores direcionados para aplicações específicas com base na localização geográfica de onde provém a sua consulta de DNS. 

Para mais informações, consulte os métodos de [encaminhamento do Gestor de Tráfego](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>Perfis de Serviço de Aplicativos e Gestor de Tráfego
Para configurar o controlo do tráfego de aplicações do App Service, cria um perfil no Gestor de Tráfego Azure que utiliza um dos quatro métodos de equilíbrio de carga descritos anteriormente e, em seguida, adicione os pontos finais (neste caso, App Service) para os quais pretende controlar o tráfego ao the perfil. O estado da sua aplicação (em execução, paragem ou eliminado) é regularmente comunicado ao perfil para que o Gestor de Tráfego Azure possa direcionar o tráfego em conformidade.

Ao utilizar o Gestor de Tráfego Azure com o Azure, tenha em mente os seguintes pontos:

* Para implementações de aplicações apenas dentro da mesma região, o App Service já fornece funcionalidade sinuosa e rodada sem ter em conta o modo de aplicação.
* Para implementações na mesma região que utilizam o App Service em conjunto com outro serviço de nuvem Azure, pode combinar ambos os tipos de pontos finais para permitir cenários híbridos.
* Só é possível especificar um ponto final do Serviço app por região num perfil. Quando seleciona uma aplicação como ponto final para uma região, as restantes aplicações nessa região tornam-se indisponíveis para seleção para esse perfil.
* Os pontos finais do Serviço de Aplicações que especifica num perfil do Gestor de Tráfego Azure aparecem na secção **Nomes** de Domínio na página Configure para a aplicação no perfil, mas não é configurável lá.
* Depois de adicionar uma aplicação a um perfil, o URL do **Site** no Dashboard da página do portal da aplicação exibe o URL de domínio personalizado da aplicação se tiver configurado uma. Caso contrário, exibe o URL de perfil `contoso.trafficmanager.net`do Gestor de Tráfego (por exemplo, ). Tanto o nome de domínio direto da aplicação como o URL do Gestor de Tráfego estão visíveis na página configure da aplicação na secção Nomes de **Domínio.**
* Os seus nomes de domínio personalizados funcionam como esperado, mas além de adicioná-los às suas aplicações, também deve configurar o seu mapa DNS para apontar para o URL do Gestor de Tráfego. Para obter informações sobre como configurar um domínio personalizado para uma aplicação de Serviço de Aplicações, consulte [mapeie um nome DNS personalizado existente para o Serviço de Aplicações Azure](app-service-web-tutorial-custom-domain.md).
* Só pode adicionar aplicações que estejam em modo standard ou premium a um perfil do Gestor de Tráfego Do Azure.

## <a name="next-steps"></a>Passos Seguintes
Para uma visão geral conceptual e técnica do Gestor de Tráfego Azure, consulte a [visão geral do Gestor de Tráfego.](../traffic-manager/traffic-manager-overview.md)


