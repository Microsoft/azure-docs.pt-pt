---
title: Controlar o tráfego com o Gestor de tráfego - serviço de aplicações do Azure
description: Este artigo fornece informações de resumo para o Gestor de tráfego do Azure, que diz respeito ao serviço de aplicações do Azure.
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
ms.devlang: na
ms.topic: article
ms.date: 02/25/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 79fe3bce558a8315f5fbf7dbc82a4979e8e24238
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831984"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Controlar o tráfego de serviço de aplicações do Azure com o Gestor de tráfego do Azure
> [!NOTE]
> Este artigo fornece informações de resumo para o Gestor de tráfego do Microsoft Azure, que diz respeito ao serviço de aplicações do Azure. Podem encontrar mais informações sobre o Azure Traffic Manager propriamente dito, visite as ligações no final deste artigo.
> 
> 

## <a name="introduction"></a>Introdução
Pode utilizar o Gestor de tráfego do Azure para controlar como os pedidos de clientes da web são distribuídos para as aplicações no serviço de aplicações do Azure. Quando os pontos finais do Serviço de Aplicações são adicionados a um perfil do Gestor de Tráfego do Azure, o Gestor de Tráfego do Azure mantém um registo do estado das suas aplicações do Serviço de Aplicações (em execução, parado ou eliminado), para que possa decidir quais desses pontos finais deve receber tráfego.

## <a name="routing-methods"></a>Métodos de encaminhamento
O Gestor de tráfego do Azure utiliza quatro métodos de encaminhamento diferentes. Esses métodos são descritos na lista seguinte, desde que pertençam ao serviço de aplicações do Azure.

* **[Prioridade](../traffic-manager/traffic-manager-routing-methods.md#priority):** utilizar uma aplicação principal para todo o tráfego e fornecer cópias de segurança no caso de principal ou as aplicações de cópia de segurança não estão disponíveis.
* **[Ponderada](../traffic-manager/traffic-manager-routing-methods.md#weighted):** distribuir o tráfego por um conjunto de aplicações, uniformemente ou, de acordo com pesos, que define.
* **[Desempenho](../traffic-manager/traffic-manager-routing-methods.md#performance):** quando tiver aplicações em localizações geográficas diferentes, utilizar a aplicação "mais próxima" em termos a menor latência de rede.
* **[Geográfica](../traffic-manager/traffic-manager-routing-methods.md#geographic):** direcionar os utilizadores para aplicações específicas com base nas localizações geográficas em suas consultas DNS são originados. 

Para obter mais informações, consulte [métodos de encaminhamento do Gestor de tráfego](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>Serviço de aplicações e perfis do Gestor de tráfego
Para configurar o controle de tráfego da aplicação de serviço de aplicações, criar um perfil no Traffic Manager do Azure que utiliza um dos quatro carregar balanceamento métodos descritos anteriormente e, em seguida, adicione os pontos de extremidade (no caso, o serviço de aplicações) para o qual pretende controlar o tráfego para o perfil. O estado da aplicação (em execução, parada ou eliminada) é comunicado regularmente para o perfil para que o Gestor de tráfego do Azure pode direcionar o tráfego em conformidade.

Ao utilizar o Gestor de tráfego do Azure com o Azure, tenha em atenção os seguintes pontos:

* Para implementações de aplicações apenas dentro da mesma região, serviço de aplicações já fornece ativação pós-falha e round robin funcionalidade independentemente do modo de aplicação.
* Para implementações na mesma região que utilizem o serviço de aplicações em conjunto com outro serviço em nuvem do Azure, pode combinar ambos os tipos de pontos de extremidade para ativar cenários híbridos.
* Só pode especificar um ponto de extremidade do serviço de aplicações por região num perfil. Quando seleciona uma aplicação como um ponto final para uma região, as restantes aplicações nessa região ficam indisponíveis para seleção para o perfil.
* Os pontos de extremidade do serviço de aplicações que especificou num perfil de Gestor de tráfego do Azure é apresentado sob o **nomes de domínio** secção na página configurar para a aplicação no perfil, mas não é configurável lá.
* Depois de adicionar uma aplicação a um perfil, o **URL do Site** no Dashboard da página da aplicação do portal apresenta o URL de domínio personalizado da aplicação, se tiver definido uma cópia de segurança. Caso contrário, ele exibe o URL de perfil do Gestor de tráfego (por exemplo, `contoso.trafficmanager.net`). Tanto o nome de domínio direto da aplicação e o URL do Gestor de tráfego são visíveis na página de configurar a aplicação no **nomes de domínio** secção.
* Os nomes de domínio personalizados funcionam conforme esperado, mas, além de adicioná-las às suas aplicações, também tem de configurar seu mapa DNS para apontar para o URL do Gestor de tráfego. Para obter informações sobre como configurar um domínio personalizado para uma aplicação de serviço de aplicações, consulte [mapear um nome DNS existente personalizado para o serviço de aplicações do Azure](app-service-web-tutorial-custom-domain.md).
* Apenas pode adicionar aplicações que estejam no modo standard ou premium, a um perfil do Gestor de tráfego do Azure.

## <a name="next-steps"></a>Próximos Passos
Para obter uma conceitual e técnica descrição geral do Gestor de tráfego do Azure, veja [descrição geral do Gestor de tráfego](../traffic-manager/traffic-manager-overview.md).

Para obter mais informações sobre como utilizar o Gestor de tráfego com o serviço de aplicações, consulte as mensagens de blogue [utilizando o Gestor de tráfego do Azure com o Azure Web Sites](https://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) e [Gestor de tráfego do Azure agora pode integrar com o Azure Web Sites](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).

