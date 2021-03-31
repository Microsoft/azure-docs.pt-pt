---
title: Controlo do tráfego com gestor de tráfego
description: Encontre as melhores práticas para configurar o Azure Traffic Manager quando o integrar com o Azure App Service.
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.topic: article
ms.date: 02/25/2016
ms.custom: seodec18
ms.openlocfilehash: 040f84288c66f4506919e775b9ea41324b617cfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "80437888"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Controle o tráfego do Serviço de Aplicações Azure com o Gestor de Tráfego Azure
> [!NOTE]
> Este artigo fornece informações sumárias para o Microsoft Azure Traffic Manager no que diz respeito ao Azure App Service. Mais informações sobre o próprio Gestor de Tráfego da Azure podem ser encontradas visitando os links no final deste artigo.
> 
> 

## <a name="introduction"></a>Introdução
Pode utilizar o Gestor de Tráfego do Azure para controlar como os pedidos de clientes Web são distribuídos para as aplicações no Serviço de Aplicações do Azure. Quando os pontos finais do Serviço de Aplicações são adicionados a um perfil do Gestor de Tráfego do Azure, o Gestor de Tráfego do Azure mantém um registo do estado das suas aplicações do Serviço de Aplicações (em execução, parado ou eliminado), para que possa decidir quais desses pontos finais deve receber tráfego.

## <a name="routing-methods"></a>Métodos de encaminhamento
O Gestor de Tráfego Azure utiliza quatro métodos de encaminhamento diferentes. Estes métodos são descritos na seguinte lista, uma vez que dizem respeito ao Serviço de Aplicações Azure.

* **[Prioridade](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method):** utilize uma aplicação primária para todo o tráfego e forneça cópias de segurança caso as aplicações primárias ou de backup não estejam disponíveis.
* **[Ponderado](../traffic-manager/traffic-manager-routing-methods.md#weighted):** distribuir o tráfego por um conjunto de aplicações, quer uniformemente, quer de acordo com os pesos, que define.
* **[Desempenho](../traffic-manager/traffic-manager-routing-methods.md#performance):** quando tiver aplicações em diferentes localizações geográficas, utilize a aplicação "mais próxima" em termos da latência da rede mais baixa.
* **[Geográfico:](../traffic-manager/traffic-manager-routing-methods.md#geographic)** utilizadores diretos para aplicações específicas com base na localização geográfica da sua consulta DNS. 

Para obter mais informações, consulte [os métodos de encaminhamento do Gestor de Tráfego.](../traffic-manager/traffic-manager-routing-methods.md)

## <a name="app-service-and-traffic-manager-profiles"></a>Perfis de Gestor de Serviços de Aplicações e Gestores de Tráfego
Para configurar o controlo do tráfego de aplicações do App Service, cria um perfil no Azure Traffic Manager que utiliza um dos quatro métodos de equilíbrio de carga descritos anteriormente e, em seguida, adicionar os pontos finais (neste caso, Serviço de Aplicações) para os quais pretende controlar o tráfego no perfil. O estado da sua aplicação (correr, parar ou apagar) é regularmente comunicado ao perfil para que o Azure Traffic Manager possa direcionar o tráfego em conformidade.

Ao utilizar o Azure Traffic Manager com a Azure, tenha em mente os seguintes pontos:

* Para implementações de aplicações apenas dentro da mesma região, o App Service já fornece funcionalidade de failover e round-robin sem ter em conta o modo app.
* Para implementações na mesma região que utilizam o App Service em conjunto com outro serviço em nuvem Azure, pode combinar ambos os tipos de pontos finais para ativar cenários híbridos.
* Só é possível especificar um ponto final do Serviço de Aplicações por região num perfil. Quando seleciona uma aplicação como ponto final para uma região, as restantes aplicações nessa região ficam indisponíveis para seleção para esse perfil.
* Os pontos finais do Serviço de Aplicações que especifica num perfil do Gestor de Tráfego Azure aparecem na secção **Nomes de Domínio** na página Configure para a aplicação no perfil, mas não é configurável lá.
* Depois de adicionar uma aplicação a um perfil, o URL do **Site** no Painel de Instrumentos da página do portal da aplicação exibe o URL de domínio personalizado da aplicação se tiver configurado uma. Caso contrário, apresenta o URL de perfil do Gestor de Tráfego (por exemplo, `contoso.trafficmanager.net` ). Tanto o nome de domínio direto da aplicação como o URL do Gestor de Tráfego estão visíveis na página de Configuração da aplicação na secção **Nomes de Domínio.**
* Os nomes de domínio personalizado funcionam como esperado, mas além de os adicionar às suas aplicações, também tem de configurar o seu mapa DNS para apontar para o URL do Gestor de Tráfego. Para obter informações sobre como configurar um domínio personalizado para uma aplicação de Serviço de Aplicações, consulte [configurar um nome de domínio personalizado no Azure App Service com integração do Traffic Manager](configure-domain-traffic-manager.md).
* Só é possível adicionar aplicações que estejam em modo standard ou premium a um perfil do Gestor de Tráfego Azure.
* Adicionar uma aplicação a um perfil de Gestor de Tráfego faz com que a aplicação seja reiniciada.

## <a name="next-steps"></a>Passos Seguintes
Para obter uma visão geral conceptual e técnica do Gestor de Tráfego da Azure, consulte [a Visão Geral do Gestor de Tráfego.](../traffic-manager/traffic-manager-overview.md)


