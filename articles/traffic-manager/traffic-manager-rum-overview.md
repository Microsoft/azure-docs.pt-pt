---
title: Medições de utilizador reais no Gestor de tráfego do Azure
description: Introdução ao medições de utilizador reais no Gestor de tráfego
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: fd37ef739522955ae8227db39a41aecf199d65c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60329586"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Descrição geral de medidas de utilizadores reais do Gestor de tráfego

Quando configurar um perfil do Gestor de tráfego para utilizar o método de encaminhamento de desempenho, o serviço analisa em que os pedidos de consulta DNS são provenientes e toma decisões de encaminhamento para direcionar os requerentes na região do Azure que lhes dá a menor latência. Isto é conseguido ao utilizar a análise da latência de rede que mantém do Gestor de tráfego para redes de utilizador final diferentes.

Medidas de utilizadores reais permite-lhe medir medições de latência de rede para regiões do Azure, as aplicações de cliente que seus utilizadores finais utilizam, e ter o Gestor de tráfego considere essas informações também quando tomar decisões de encaminhamento. Ao optar por utilizar medidas de utilizadores reais, pode aumentar a precisão de encaminhamento para pedidos provenientes dessas redes onde residem os seus utilizadores finais. 

## <a name="how-real-user-measurements-work"></a>Como funcionam as medições de utilizador Real

Medidas de utilizadores reais funcionam por ter a latência de medida de aplicativos de cliente de regiões do Azure que ele é visto das redes em que são utilizadas pelo utilizador final. Por exemplo, se tiver uma página da web que são acedidos por utilizadores em localizações diferentes (por exemplo, nas regiões da América do Norte), pode utilizar medidas de utilizadores reais com o método de encaminhamento de desempenho para obtê-los para a região do Azure melhor em que o servidor aplicativo está hospedado.

Ele começa ao incorporar um JavaScript fornecido do Azure (com uma chave exclusiva no mesmo) nas suas páginas web. Feito isso, sempre que um usuário visitar essa página da Web, o JavaScript consulta o Gestor de tráfego para obter informações sobre as regiões do Azure deve medir. O serviço devolve um conjunto de pontos de extremidade para o script que, em seguida, medida nestas regiões consecutivamente baixando uma imagem de pixel único hospedado nessas regiões do Azure e observar a latência entre a hora o pedido foi enviado e a hora quando o primeiro byte foi recebido . Essas medidas, em seguida, são informadas de volta ao serviço Gestor de tráfego.

Ao longo do tempo, isso ocorre muitas vezes e em muitas redes que leva ao Gestor de tráfego de obter informações mais precisas sobre as características de latência das redes no qual residem os utilizadores finais. Estas informações começa a obter a serem incluídos nas decisões de encaminhamento realizadas pelo Gestor de tráfego. Como resultado, isso leva a maior precisão nessas decisões com base em medidas de utilizadores reais enviados.

Quando utiliza medidas de utilizadores reais, é cobrado com base no número de medidas de enviados para o Gestor de tráfego. Para obter mais detalhes sobre os preços, visite o [Gestor de tráfego, página de preços](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="next-steps"></a>Passos Seguintes
- Saiba como utilizar [medidas de utilizadores reais com páginas da web](traffic-manager-create-rum-web-pages.md)
- Saiba [como funciona o Gestor de tráfego](traffic-manager-overview.md)
- Saiba mais sobre [Mobile Center](https://docs.microsoft.com/mobile-center/)
- Saiba mais sobre o [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md) suportada pelo Gestor de tráfego
- Saiba como [criar um perfil do Gestor de tráfego](traffic-manager-create-profile.md)

