---
title: Medições reais do utilizador em Azure Traffic Manager
description: Nesta introdução, saiba como funcionam as Medições Reais do Utilizador do Azure Traffic Manager.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 618f8fff532da0f6ae315ad9e4cda35a289949d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98183715"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Descrição geral das Medições de Utilizador Reais do Gestor de Tráfego

Quando configura um perfil de Gestor de Tráfego para utilizar o método de encaminhamento de desempenho, o serviço analisa de onde vêm os pedidos de consulta de DNS e toma decisões de encaminhamento para direcionar os solicitadores para a região de Azure que lhes dá a latência mais baixa. Isto é conseguido utilizando a inteligência de latência da rede que o Traffic Manager mantém para diferentes redes de utilizadores finais.

As Medições Reais do Utilizador permitem-lhe medir as medições de latência da rede nas regiões Azure, a partir das aplicações do cliente que os seus utilizadores finais utilizam, e fazer com que o Gestor de Tráfego considere essa informação também ao tomar decisões de encaminhamento. Ao optar por utilizar as Verdadeiras Medidas do Utilizador, pode aumentar a precisão do encaminhamento para pedidos provenientes das redes onde residem os seus utilizadores finais. 

## <a name="how-real-user-measurements-work"></a>Como funcionam as medições reais do utilizador

As Verdadeiras Medições de Utilizador funcionam fazendo com que as aplicações do seu cliente meçam a latência nas regiões de Azure, como é visto a partir das redes de utilizador final em que são utilizadas. Por exemplo, se tiver uma página web que seja acedida pelos utilizadores em diferentes locais (por exemplo, nas regiões norte-americanas), pode utilizar As Medições reais do Utilizador com método de encaminhamento de desempenho para os levar à melhor região de Azure em que a sua aplicação de servidor está hospedada.

Começa por incorporar um Azure fornecido JavaScript (com uma chave única) nas suas páginas web. Uma vez feito, sempre que um utilizador visita essa página web, o JavaScript consulta o Traffic Manager para obter informações sobre as regiões Azure que deve medir. O serviço devolve um conjunto de pontos finais ao script que depois medem estas regiões consecutivamente, descarregando uma única imagem de pixel hospedada nessas regiões do Azure e observando a latência entre o momento em que o pedido foi enviado e o momento em que o primeiro byte foi recebido. Estas medições são então comunicadas de volta ao serviço de Gerente de Tráfego.

Com o tempo, isto acontece muitas vezes e em muitas redes que levam o Traffic Manager a obter informações mais precisas sobre as características de latência das redes em que os seus utilizadores finais residem. Esta informação começa a ser incluída nas decisões de encaminhamento tomadas pelo Traffic Manager. Como resultado, conduz a uma maior precisão nessas decisões com base nas Verdadeiras Medições do Utilizador enviadas.

Quando utiliza As Medições Reais do Utilizador, é faturado com base no número de medições enviadas ao Gestor de Tráfego. Para mais detalhes sobre o preço, visite a [página de preços do Gestor de Tráfego.](https://azure.microsoft.com/pricing/details/traffic-manager/)

## <a name="faqs"></a>FAQs

* [Quais são os benefícios da utilização de Medidas Reais do Utilizador?](./traffic-manager-faqs.md#what-are-the-benefits-of-using-real-user-measurements)

* [Posso utilizar as medidas reais do utilizador com regiões não-Azure?](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-non-azure-regions)

* [Que método de encaminhamento beneficia das Medições Reais do Utilizador?](./traffic-manager-faqs.md#which-routing-method-benefits-from-real-user-measurements)

* [Preciso de ativar as medições reais do utilizador separadamente?](./traffic-manager-faqs.md#do-i-need-to-enable-real-user-measurements-each-profile-separately)

* [Como desativar as Medidas reais do Utilizador para a minha subscrição?](./traffic-manager-faqs.md#how-do-i-turn-off-real-user-measurements-for-my-subscription)

* [Posso utilizar as Medidas reais do utilizador com aplicações de clientes que não as páginas web?](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-client-applications-other-than-web-pages)

* [Quantas medições são feitas cada vez que a minha página web ativada por Medições de Utilizador é feita?](./traffic-manager-faqs.md#how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered)

* [Existe um atraso antes de o script real de medições do utilizador ser executado na minha página web?](./traffic-manager-faqs.md#is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage)

* [Posso utilizar as medidas reais do utilizador apenas com as regiões Azure que quero medir?](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure)

* [Posso limitar o número de medições efetuadas a um número específico?](./traffic-manager-faqs.md#can-i-limit-the-number-of-measurements-made-to-a-specific-number)

* [Posso ver as medidas feitas pela aplicação do meu cliente como parte das Medições Reais do Utilizador?](./traffic-manager-faqs.md#can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements)

* [Posso modificar o script de medição fornecido pelo Traffic Manager?](./traffic-manager-faqs.md#can-i-modify-the-measurement-script-provided-by-traffic-manager)

* [Será possível que outros vejam a chave que uso com as medidas reais do utilizador?](./traffic-manager-faqs.md#will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements)

* [Outros podem abusar da minha chave RUM?](./traffic-manager-faqs.md#can-others-abuse-my-rum-key)

* [Preciso de colocar o JavaScript de medição em todas as minhas páginas web?](./traffic-manager-faqs.md#do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages)

* [As informações sobre os meus utilizadores finais podem ser identificadas pelo Gestor de Tráfego se eu utilizar as Medidas reais do Utilizador?](./traffic-manager-faqs.md#can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements)

* [A página web que mede as medições reais do utilizador precisa de estar a utilizar o Gestor de Tráfego para encaminhamento?](./traffic-manager-faqs.md#does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing)

* [Preciso de hospedar algum serviço nas regiões Azure para utilizar com as medidas reais do utilizador?](./traffic-manager-faqs.md#do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements)

* [O meu uso de largura de banda Azure aumentará quando utilizar as Medições reais do utilizador?](./traffic-manager-faqs.md#will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements)

## <a name="next-steps"></a>Passos seguintes
- Saiba como utilizar [as Medições reais do utilizador com páginas web](traffic-manager-create-rum-web-pages.md)
- Saiba [como funciona o Gestor de Tráfego](traffic-manager-overview.md)
- Saiba mais sobre [o Mobile Center](/mobile-center/)
- Saiba mais sobre os [métodos de encaminhamento de tráfego suportados](traffic-manager-routing-methods.md) pelo Traffic Manager
- Saiba como [criar um perfil de Gestor de Tráfego](./quickstart-create-traffic-manager-profile.md)