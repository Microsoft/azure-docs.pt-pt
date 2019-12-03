---
title: Controlar o tráfego com o Gerenciador de tráfego
description: Encontre as práticas recomendadas para configurar o Gerenciador de tráfego do Azure ao integrá-lo com o serviço Azure App.
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.topic: article
ms.date: 02/25/2016
ms.custom: seodec18
ms.openlocfilehash: 200effab70b369d69b4e89b1901578ecfe1a1b87
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74684092"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Controlando o tráfego do serviço de Azure App com o Gerenciador de tráfego do Azure
> [!NOTE]
> Este artigo fornece informações resumidas para Gerenciador de Tráfego do Microsoft Azure à medida que ele se relaciona com Azure App Service. Mais informações sobre o próprio Gerenciador de tráfego do Azure podem ser encontradas visitando os links no final deste artigo.
> 
> 

## <a name="introduction"></a>Introdução
Você pode usar o Gerenciador de tráfego do Azure para controlar como as solicitações de clientes Web são distribuídas para aplicativos no serviço Azure App. Quando os pontos finais do Serviço de Aplicações são adicionados a um perfil do Gestor de Tráfego do Azure, o Gestor de Tráfego do Azure mantém um registo do estado das suas aplicações do Serviço de Aplicações (em execução, parado ou eliminado), para que possa decidir quais desses pontos finais deve receber tráfego.

## <a name="routing-methods"></a>Métodos de encaminhamento
O Gerenciador de tráfego do Azure usa quatro métodos diferentes de roteamento. Esses métodos são descritos na lista a seguir, pois pertencem ao serviço Azure App.

* **[Prioridade](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method):** use um aplicativo primário para todo o tráfego e forneça backups caso os aplicativos primários ou de backup não estejam disponíveis.
* **[Ponderado](../traffic-manager/traffic-manager-routing-methods.md#weighted):** distribua o tráfego entre um conjunto de aplicativos, seja uniformemente ou de acordo com os pesos, que você define.
* **[Desempenho](../traffic-manager/traffic-manager-routing-methods.md#performance):** quando você tem aplicativos em diferentes localizações geográficas, use o aplicativo "mais próximo" em termos da menor latência de rede.
* **[Geográfico](../traffic-manager/traffic-manager-routing-methods.md#geographic):** encaminhe os usuários a aplicativos específicos com base no local geográfico do qual sua consulta DNS provém. 

Para obter mais informações, consulte [métodos de roteamento do Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>Perfis do serviço de aplicativo e do Gerenciador de tráfego
Para configurar o controle do tráfego do aplicativo do serviço de aplicativo, você cria um perfil no Gerenciador de tráfego do Azure que usa um dos quatro métodos de balanceamento de carga descritos anteriormente e, em seguida, adiciona os pontos de extremidade (nesse caso, o serviço de aplicativo) para o qual você deseja controlar o tráfego para o criar. O status do aplicativo (em execução, parado ou excluído) é comunicado regularmente ao perfil para que o Gerenciador de tráfego do Azure possa direcionar o tráfego de acordo.

Ao usar o Gerenciador de tráfego do Azure com o Azure, tenha em mente os seguintes pontos:

* Para implantações somente de aplicativo na mesma região, o serviço de aplicativo já fornece a funcionalidade de failover e Round Robin sem considerar o modo de aplicativo.
* Para implantações na mesma região que usam o serviço de aplicativo em conjunto com outro serviço de nuvem do Azure, você pode combinar os dois tipos de pontos de extremidade para habilitar cenários híbridos.
* Você só pode especificar um ponto de extremidade de serviço de aplicativo por região em um perfil. Quando você seleciona um aplicativo como um ponto de extremidade para uma região, os aplicativos restantes nessa região ficam indisponíveis para seleção para esse perfil.
* Os pontos de extremidade do serviço de aplicativo que você especificar em um perfil do Gerenciador de tráfego do Azure aparecem na seção **nomes de domínio** na página Configurar do aplicativo no perfil, mas não podem ser configurados lá.
* Depois que você adicionar um aplicativo a um perfil, a **URL do site** no painel da página do portal do aplicativo exibirá a URL de domínio personalizada do aplicativo se você tiver configurado um. Caso contrário, ele exibirá a URL do perfil do Gerenciador de tráfego (por exemplo, `contoso.trafficmanager.net`). O nome de domínio direto do aplicativo e a URL do Gerenciador de tráfego são visíveis na página Configurar do aplicativo na seção **nomes de domínio** .
* Os nomes de domínio personalizados funcionam conforme o esperado, mas, além de adicioná-los aos seus aplicativos, você também deve configurar o mapa DNS para apontar para a URL do Gerenciador de tráfego. Para obter informações sobre como configurar um domínio personalizado para um aplicativo do serviço de aplicativo, consulte [mapear um nome DNS personalizado existente para o serviço de Azure app](app-service-web-tutorial-custom-domain.md).
* Você só pode adicionar aplicativos que estejam no modo padrão ou Premium a um perfil do Gerenciador de tráfego do Azure.

## <a name="next-steps"></a>Próximos Passos
Para obter uma visão geral técnica e conceitual do Gerenciador de tráfego do Azure, consulte [visão geral do Gerenciador de tráfego](../traffic-manager/traffic-manager-overview.md).


