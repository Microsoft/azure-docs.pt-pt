---
title: Controlar o tráfego com o serviço de Azure App do Gerenciador de tráfego
description: Este artigo fornece informações resumidas sobre o Gerenciador de tráfego do Azure, pois ele se relaciona com Azure App serviço.
services: app-service\web
documentationcenter: ''
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: bb63b25ee9257a402a9887bc8ed8aa83370f3ea0
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066417"
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

* **[Prioridade](../traffic-manager/traffic-manager-routing-methods.md#priority):** use um aplicativo primário para todo o tráfego e forneça backups caso os aplicativos primários ou de backup não estejam disponíveis.
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
* Depois que você adicionar um aplicativo a um perfil, a **URL do site** no painel da página do portal do aplicativo exibirá a URL de domínio personalizada do aplicativo se você tiver configurado um. Caso contrário, ele exibirá a URL do perfil do Gerenciador de `contoso.trafficmanager.net`tráfego (por exemplo,). O nome de domínio direto do aplicativo e a URL do Gerenciador de tráfego são visíveis na página Configurar do aplicativo na seção **nomes de domínio** .
* Os nomes de domínio personalizados funcionam conforme o esperado, mas, além de adicioná-los aos seus aplicativos, você também deve configurar o mapa DNS para apontar para a URL do Gerenciador de tráfego. Para obter informações sobre como configurar um domínio personalizado para um aplicativo do serviço de aplicativo, consulte [mapear um nome DNS personalizado existente para o serviço de Azure app](app-service-web-tutorial-custom-domain.md).
* Você só pode adicionar aplicativos que estejam no modo padrão ou Premium a um perfil do Gerenciador de tráfego do Azure.

## <a name="next-steps"></a>Próximos Passos
Para obter uma visão geral técnica e conceitual do Gerenciador de tráfego do Azure, consulte [visão geral do Gerenciador de tráfego](../traffic-manager/traffic-manager-overview.md).

Para obter mais informações sobre como usar o Gerenciador de tráfego com o serviço de aplicativo, consulte as postagens de blog [usando o Gerenciador de tráfego do Azure com sites do Azure](https://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) e o [Gerenciador de tráfego do Azure agora pode se integrar aos sites do Azure](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).

