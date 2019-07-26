---
title: Medidas de Usuário Reais no Gerenciador de tráfego do Azure
description: Introdução ao Medidas de Usuário Reais no Gerenciador de tráfego
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 4aa2649ba2e49e1fec1b9b124a9b82313280cee9
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333720"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Visão geral do Gerenciador de tráfego Medidas de Usuário Reais

Quando você configura um perfil do Gerenciador de tráfego para usar o método de roteamento de desempenho, o serviço examina a origem das solicitações de consulta DNS e toma decisões de roteamento para direcionar esses solicitantes para a região do Azure que fornece a menor latência. Isso é feito utilizando a inteligência de latência de rede que o Gerenciador de tráfego mantém para diferentes redes de usuário final.

O Medidas de Usuário Reais permite que você meça as medidas de latência de rede para regiões do Azure, dos aplicativos cliente que seus usuários finais usam e que o Gerenciador de tráfego também considere essas informações ao tomar decisões de roteamento. Ao optar por usar o Medidas de Usuário Reais, você pode aumentar a precisão do roteamento para solicitações provenientes das redes em que os usuários finais residem. 

## <a name="how-real-user-measurements-work"></a>Como Medidas de Usuário Reais trabalho

Medidas de Usuário Reais funcionam fazendo com que os aplicativos cliente meçam a latência para as regiões do Azure conforme ele é visto nas redes do usuário final nas quais elas são usadas. Por exemplo, se você tiver uma página da Web que é acessada por usuários em diferentes locais (por exemplo, nas regiões da América do Norte), você pode usar Medidas de Usuário Reais com o método de roteamento de desempenho para obter a melhor região do Azure na qual o servidor o aplicativo está hospedado.

Ele começa inserindo um JavaScript fornecido pelo Azure (com uma chave exclusiva nele) em suas páginas da Web. Depois disso, sempre que um usuário visitar essa página da Web, o JavaScript consultará o Gerenciador de tráfego para obter informações sobre as regiões do Azure que ele deve medir. O serviço retorna um conjunto de pontos de extremidade para o script que, em seguida, mede essas regiões consecutivamente baixando uma única imagem de pixel hospedada nessas regiões do Azure e observando a latência entre a hora em que a solicitação foi enviada e a hora em que o primeiro byte foi recebido . Essas medidas são relatadas de volta para o serviço do Gerenciador de tráfego.

Ao longo do tempo, isso ocorre muitas vezes e em muitas redes que levam ao Gerenciador de tráfego obter informações mais precisas sobre as características de latência das redes nas quais os usuários finais residem. Essas informações começam a ser incluídas nas decisões de roteamento feitas pelo Gerenciador de tráfego. Como resultado, ele leva a uma maior precisão nessas decisões com base no Medidas de Usuário Reais enviado.

Ao usar Medidas de Usuário Reais, você será cobrado com base no número de medições enviadas ao Gerenciador de tráfego. Para obter mais detalhes sobre os preços, visite a [página de preços do Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>FAQs

* [Quais são os benefícios do uso do Medidas de Usuário Reais?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-benefits-of-using-real-user-measurements)

* [Posso usar Medidas de Usuário Reais com regiões não Azure?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-non-azure-regions)

* [Qual método de roteamento se beneficia da Medidas de Usuário Reais?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#which-routing-method-benefits-from-real-user-measurements)

* [É necessário habilitar Medidas de Usuário Reais cada perfil separadamente?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-real-user-measurements-each-profile-separately)

* [Como fazer desligar Medidas de Usuário Reais para minha assinatura?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-turn-off-real-user-measurements-for-my-subscription)

* [Posso usar Medidas de Usuário Reais com aplicativos cliente diferentes de páginas da Web?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-client-applications-other-than-web-pages)

* [Quantas medições são feitas cada vez que minha página da Web habilitada Medidas de Usuário Reais é renderizada?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered)

* [Há um atraso antes que Medidas de Usuário Reais script seja executado em minha página da Web?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage)

* [Posso usar Medidas de Usuário Reais apenas com as regiões do Azure que desejo medir?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure)

* [Posso limitar o número de medições feitas a um número específico?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-limit-the-number-of-measurements-made-to-a-specific-number)

* [Posso ver as medições feitas pelo meu aplicativo cliente como parte do Medidas de Usuário Reais?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements)

* [Posso modificar o script de medidas fornecido pelo Gerenciador de tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-modify-the-measurement-script-provided-by-traffic-manager)

* [Será possível que outras pessoas vejam a chave que uso com Medidas de Usuário Reais?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements)

* [Outras pessoas podem abuse da minha chave de RUM?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-others-abuse-my-rum-key)

* [É necessário colocar o JavaScript de medida em todas as minhas páginas da Web?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages)

* [As informações sobre meus usuários finais podem ser identificadas pelo Gerenciador de tráfego se eu usar Medidas de Usuário Reais?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements)

* [A página da Web está medindo Medidas de Usuário Reais precisa estar usando o Gerenciador de tráfego para roteamento?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing)

* [É necessário hospedar qualquer serviço nas regiões do Azure para usar com Medidas de Usuário Reais?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements)

* [O uso da largura de banda do Azure será aumentado quando eu usar Medidas de Usuário Reais?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements)

## <a name="next-steps"></a>Passos Seguintes
- Saiba como usar [medidas de usuário reais com páginas da Web](traffic-manager-create-rum-web-pages.md)
- Saiba [como funciona o Gerenciador de tráfego](traffic-manager-overview.md)
- Saiba mais sobre o [Mobile Center](https://docs.microsoft.com/mobile-center/)
- Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md) com suporte pelo Gerenciador de tráfego
- Saiba como [criar um perfil do Gerenciador de tráfego](traffic-manager-create-profile.md)

