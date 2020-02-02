---
title: Medições reais do utilizador no Gestor de Tráfego Azure
description: Nesta introdução, saiba como funcionam as Medições reais do Gestor de Tráfego do Azure.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: e38e1255b1a84ab5d3fd37e16eb65c76001dbfa1
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938439"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Visão geral do gestor de tráfego real do utilizador

Ao configurar um perfil do Traffic Manager para utilizar o método de encaminhamento de desempenho, o serviço analisa de onde vêm os pedidos de consulta do DNS e toma decisões de encaminhamento para direcionar esses solicitadores para a região de Azure que lhes dá a menor latência. Isto é conseguido utilizando a inteligência de latência da rede que o Traffic Manager mantém para diferentes redes de utilizadores finais.

As Medições Reais de Utilizador permitem medir as medições de latência da rede para as regiões do Azure, a partir das aplicações do cliente que os utilizadores finais utilizam, e fazer com que o Gestor de Tráfego considere essa informação bem ao tomar decisões de encaminhamento. Ao optar por utilizar as Medições reais do Utilizador, pode aumentar a precisão do encaminhamento para pedidos provenientes das redes onde residem os seus utilizadores finais. 

## <a name="how-real-user-measurements-work"></a>Como funcionam as medições reais do utilizador

As medições reais do utilizador funcionam fazendo com que as aplicações do seu cliente meçam a latência nas regiões do Azure, tal como é visto nas redes de utilizador final em que são utilizadas. Por exemplo, se tiver uma página web que é acedida pelos utilizadores em diferentes locais (por exemplo, nas regiões norte-americanas), pode utilizar Medições reais de utilizador com método de encaminhamento de desempenho para levá-los à melhor região Azure em que o seu servidor aplicação é hospedada.

Começa por incorporar um Azure fornecido JavaScript (com uma chave única) nas suas páginas web. Uma vez feito, sempre que um utilizador visita essa página web, o JavaScript consulta o Traffic Manager para obter informações sobre as regiões azure que deve medir. O serviço devolve um conjunto de pontos finais ao guião que depois medem estas regiões consecutivamente, baixando uma única imagem de pixel alojada naquelas regiões do Azure e observando a latência entre o momento em que o pedido foi enviado e o momento em que o primeiro byte foi recebido . Estas medições são depois reportadas ao serviço do Gestor de Tráfego.

Com o tempo, isto acontece muitas vezes e em muitas redes levando o Traffic Manager a obter informações mais precisas sobre as características de latência das redes em que os seus utilizadores finais residem. Esta informação começa a ser incluída nas decisões de encaminhamento tomadas pelo Traffic Manager. Como resultado, leva a uma maior precisão nas decisões com base nas Medições reais do Utilizador enviadas.

Quando utiliza medições reais do utilizador, é faturado com base no número de medições enviadas ao Traffic Manager. Para mais detalhes sobre os preços, visite a página de preços do Gestor de [Tráfego.](https://azure.microsoft.com/pricing/details/traffic-manager/)

## <a name="faqs"></a>FAQ

* [Quais são os benefícios da utilização de Medições reais do Utilizador?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-benefits-of-using-real-user-measurements)

* [Posso usar medições reais do utilizador com regiões não-Azure?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-non-azure-regions)

* [Que método de encaminhamento beneficia das Medições reais do Utilizador?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#which-routing-method-benefits-from-real-user-measurements)

* [Preciso de ativar as medições reais do utilizador de cada perfil separadamente?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-real-user-measurements-each-profile-separately)

* [Como desligo as Medições reais do Utilizador para a minha subscrição?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-turn-off-real-user-measurements-for-my-subscription)

* [Posso usar Medições reais do utilizador com aplicações de clientes que não as páginas web?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-client-applications-other-than-web-pages)

* [Quantas medidas são feitas cada vez que a minha página web ativada pelo utilizador real é renderizada?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered)

* [Há algum atraso antes que o script de Medições de Utilizador Real seja executado na minha página web?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage)

* [Posso usar medições reais de utilizador apenas com as regiões Azure que quero medir?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure)

* [Posso limitar o número de medições feitas a um número específico?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-limit-the-number-of-measurements-made-to-a-specific-number)

* [Posso ver as medidas feitas pela aplicação do meu cliente como parte das Medições reais do Utilizador?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements)

* [Posso modificar o script de medição fornecido pelo Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-modify-the-measurement-script-provided-by-traffic-manager)

* [Será possível que outros vejam a chave que uso com medições reais do utilizador?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements)

* [Outros podem abusar da minha chave RUM?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-others-abuse-my-rum-key)

* [Preciso colocar a medida JavaScript em todas as minhas páginas web?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages)

* [As informações sobre os meus utilizadores finais podem ser identificadas pelo Traffic Manager se eu utilizar medições reais do utilizador?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements)

* [A página web que mede as medições reais do utilizador precisa de estar a utilizar o Traffic Manager para o encaminhamento?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing)

* [Preciso de receber algum serviço nas regiões do Azure para utilizar com medições reais do utilizador?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements)

* [O meu uso de largura de banda Azure aumentará quando utilizar medições reais do utilizador?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements)

## <a name="next-steps"></a>Passos seguintes
- Saiba como utilizar [medições reais do utilizador com páginas web](traffic-manager-create-rum-web-pages.md)
- Saiba como funciona o [Gestor de Tráfego](traffic-manager-overview.md)
- Saiba mais sobre [o Mobile Center](https://docs.microsoft.com/mobile-center/)
- Saiba mais sobre os [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md) suportados pelo Traffic Manager
- Saiba como [criar um perfil de Gestor](traffic-manager-create-profile.md) de Tráfego

