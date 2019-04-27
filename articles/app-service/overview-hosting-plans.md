---
title: Plano do serviço de aplicações descrição geral - Azure | Documentos da Microsoft
description: Saiba como planos de serviço de aplicações para o trabalho de serviço de aplicações do Azure e como eles beneficiar sua experiência de gestão.
keywords: serviço de aplicações, serviço de aplicações do azure, dimensionamento, dimensionável, escalabilidade, o plano do serviço de aplicações, o custo do serviço de aplicações
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ab04d1288eb3a851774128b8aaaae03868c2ffa7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60839016"
---
# <a name="azure-app-service-plan-overview"></a>Descrição geral de plano de serviço de aplicações do Azure

No Serviço de Aplicações, as aplicações são executadas num _plano do Serviço de Aplicações_. Um plano do Serviço de Aplicações define um conjunto de recursos de computação para a execução da aplicação Web. Estes recursos de computação são análogos para o [ _farm de servidores_ ](https://wikipedia.org/wiki/Server_farm) na hospedagem na web convencional. Uma ou mais aplicações podem ser configuradas para executar os mesmos recursos de computação (ou no mesmo plano de serviço de aplicações).

Quando cria um plano do serviço de aplicações numa determinada região (por exemplo, Europa Ocidental), é criado um conjunto de recursos de computação para esse plano nessa região. As aplicações que colocar neste plano de serviço de aplicações executado recursos de computação conforme definido pelo seu plano do serviço de aplicações. Define a cada plano de serviço de aplicações:

- Região (E.U.A. oeste, E.U.A. leste, etc.)
- Número de instâncias de VM
- Tamanho das instâncias VM (pequeno, médio, grande)
- Escalão de preço (gratuito, partilhado, básico, Standard, Premium, PremiumV2, Isolated, consumo)

O _escalão de preço_ do serviço de aplicações do plano determina quais recursos de serviço de aplicações obtém e quanto Compre o plano. Existem algumas categorias de escalões de preço:

- **Partilhado computação**: **Livre** e **partilhado**, as duas camadas, executa uma aplicação na mesma VM do Azure como outras aplicações de serviço de aplicações, incluindo aplicações de outros clientes de base. Estas camadas alocar quotas de CPU para cada aplicação que é executado nos recursos partilhados e os recursos não é possível aumentar horizontalmente.
- **Dedicado computação**: O **básica**, **padrão**, **Premium**, e **PremiumV2** escalões executam aplicações em VMs do Azure dedicado. Apenas as aplicações no mesmo plano de serviço de aplicações partilham os mesmos recursos de computação. Quanto maior for o escalão, as mais instâncias VM estão disponíveis para que possa aumentar horizontalmente.
- **Isolado**: Este escalão executa VMs dedicadas do Azure no dedicado redes virtuais do Azure, que fornece o isolamento de rede com base no isolamento de computação para as suas aplicações. Ele fornece as capacidades de escalamento horizontal máximas.
- **Consumo**: Só está disponível para esta camada [aplicações de funções](../azure-functions/functions-overview.md). Dimensiona-se as funções dinamicamente dependendo da carga de trabalho. Para obter mais informações, consulte [comparação de planos de alojamento das funções do Azure](../azure-functions/functions-scale.md).

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Cada camada também fornece um subconjunto específico de recursos de serviço de aplicações. Estas funcionalidades incluem domínios personalizados e certificados SSL, dimensionamento automático, blocos de implementação, as cópias de segurança, integração do Gestor de tráfego e muito mais. Quanto maior for o escalão, quanto mais recursos estão disponíveis. Para saber quais recursos têm suporte em cada escalão de preço, consulte [detalhes do plano de serviço de aplicações](https://azure.microsoft.com/pricing/details/app-service/plans/).

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> A nova **PremiumV2** escalão de preço oferece [VMs de série Dv2](../virtual-machines/windows/sizes-general.md#dv2-series) com processadores mais rápidos, armazenamento SSD e double rácio de memória/núcleo em comparação comparada **padrão** escalão. **PremiumV2** também suporta um maior dimensionamento através do maior número de instâncias, continuando a oferecer todas as capacidades avançadas contidas no plano Standard. Todas as funcionalidades disponíveis no existente **Premium** escalão estão incluídas na **PremiumV2**.
>
> Assim como outros escalões dedicados, três tamanhos VM estão disponíveis para esta camada:
>
> - Pequeno (um núcleo de CPU, 3,5 GiB de memória) 
> - Médio (dois núcleos de CPU, 7 GiB de memória) 
> - Grande (quatro núcleos de CPU, 14 GiB de memória)  
>
> Para **PremiumV2** informações sobre preços, veja [preços do App Service](https://azure.microsoft.com/pricing/details/app-service/).
>
> Para começar a utilizar com o novo **PremiumV2** escalão de preço, veja [escalão PremiumV2 configurar serviço de aplicações](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Como meu aplicativo executar e dimensionar?

Na **gratuito** e **partilhado** camadas, uma aplicação recebe minutos de CPU numa instância VM partilhada e não é possível aumentar horizontalmente. Em outras camadas, uma aplicação é executada e dimensiona-se da seguinte forma.

Quando cria uma aplicação no serviço de aplicações, será colocado num plano do serviço de aplicações. Quando a aplicação é executada, ele é executado em todas as instâncias VM configuradas no plano do serviço de aplicações. Se várias aplicações estão no mesmo plano de serviço de aplicações, todas compartilham as mesmas instâncias VM. Se tiver vários blocos de implementação para uma aplicação, todos os blocos de implementação também executam nas instâncias da mesmas VM. Se ativar registos de diagnóstico, fazer cópias de segurança ou executar o WebJobs, também utilizam ciclos de CPU e memória nestas instâncias de VM.

Dessa forma, o plano de serviço de aplicações é a unidade de dimensionamento de aplicações do serviço de aplicações. Se o plano estiver configurado para executar cinco instâncias VM, todas as aplicações no plano de execução em todas as instâncias de cinco. Se o plano estiver configurado para o dimensionamento automático, em seguida, todas as aplicações no plano são dimensionadas em conjunto com base nas definições de dimensionamento automático.

Para obter informações sobre como aumentar horizontalmente uma aplicação, consulte [dimensionar a contagem de instâncias manual ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Quanto custa o meu plano de serviço de aplicações?

Esta secção descreve como são cobradas as aplicações de serviço de aplicações. Para informações de preços detalhadas, a região específicos, consulte [preços do App Service](https://azure.microsoft.com/pricing/details/app-service/).

Exceto para **gratuito** escalão, um plano do serviço de aplicação realiza uma tarifa por hora aos recursos de computação que utiliza.

- Na **partilhado** cada aplicação de camada recebe uma quota de minutos de CPU, então _cada aplicação_ é cobrada por hora para a quota de CPU.
- O dedicado em camadas de computação (**básica**, **padrão**, **Premium**, **PremiumV2**), plano do serviço de aplicações define o número de VM instâncias de aplicações são dimensionadas para, então _cada instância de VM_ no serviço de aplicações plano tem uma tarifa por hora. Estas instâncias de VM são cobradas as mesmas aplicações, independentemente de quantos estão em execução nos mesmos. Para evitar cobranças inesperadas, consulte [limpar um plano do serviço de aplicações](app-service-plan-manage.md#delete).
- Na **Isolated** escalão, o ambiente de serviço de aplicação define o número de trabalhadores isolados que executam as suas aplicações, e _cada trabalho_ é cobrada por hora. Além disso, existe uma tarifa por hora de base para a execução do ambiente de serviço de aplicações em si. 
- (Apenas funções do azure) O **consumo** escalão dinamicamente aloca instâncias de VM para a carga de trabalho de uma aplicação de funções de serviço e é cobrada dinamicamente por segundo pelo Azure. Para obter mais informações, consulte [preços de funções do Azure](https://azure.microsoft.com/pricing/details/functions/).

O utilizador não pagar para utilizar as funcionalidades do serviço de aplicações que estão disponíveis para (configurar domínios personalizados, certificados SSL, blocos de implementação, as cópias de segurança, etc.). As exceções são:

- Domínios do serviço de aplicações - paga ao adquirir um no Azure e quando o renovar por ano.
- Certificados de serviço de aplicações - paga ao adquirir um no Azure e quando o renovar por ano.
- Ligações de SSL baseado em IP - daí de uma tarifa por hora para cada ligação de SSL baseado em IP, mas alguns **padrão** camada ou acima dá-lhe uma ligação de SSL baseado em IP gratuitamente. Ligações de SSL baseado em SNI são gratuitas.

> [!NOTE]
> Se integrar o serviço de aplicações com outro serviço do Azure, terá de considerar os custos desses outros serviços. Por exemplo, se usar Traffic Manager do Azure para dimensionar a sua aplicação geograficamente, o Gestor de tráfego do Azure também custos base na sua utilização. Para estimar o custo entre serviços no Azure, veja [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/). 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>E se meu aplicativo precisa de mais recursos ou funcionalidades?

Seu plano do serviço de aplicações pode ser aumentado e diminuído em qualquer altura. É tão simples como alterar o escalão de preço do plano. Pode escolher um escalão de preço mais baixo em primeiro lugar e aumentar verticalmente mais tarde quando precisar de mais funcionalidades do serviço de aplicações.

Por exemplo, pode começar a testar a sua aplicação web num **gratuito** serviço de aplicações planejar e pagar nada. Quando quiser adicionar seu [nome DNS personalizado](app-service-web-tutorial-custom-domain.md) para a aplicação web, apenas dimensionar o seu plano de até **partilhado** escalão. Posteriormente, quando desejar adicionar um [certificado SSL personalizado](app-service-web-tutorial-custom-ssl.md), dimensionar o seu plano de até **básica** escalão. Quando quiser ter [ambientes de teste](deploy-staging-slots.md), dimensionar até **padrão** escalão. Quando precisar de mais núcleos, memória ou armazenamento, aumente verticalmente para um maior tamanho de VM da mesma camada.

O mesmo funciona na ordem inversa. Quando sentir que já não precisar de recursos de um escalão mais elevado, pode reduzir verticalmente para um escalão mais baixo, o que lhe permite poupar dinheiro.

Para obter informações sobre como aumentar verticalmente o plano do serviço de aplicações, consulte [aumentar verticalmente uma aplicação no Azure](web-sites-scale.md).

Se a aplicação está no mesmo plano de serviço de aplicações com outras aplicações, poderá melhorar o desempenho da aplicação ao isolar os recursos de computação. Pode fazer isso movendo a aplicação para um plano de serviço de aplicações separado. Para obter mais informações, consulte [mover uma aplicação para outro plano do serviço de aplicações](app-service-plan-manage.md#move).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Deve colocar uma aplicação num novo plano ou de um plano existente?

Uma vez que paga os recursos de computação em seu plano do serviço de aplicações aloca (consulte [qual é o meus custos do plano de serviço de aplicações?](#cost)), potencialmente pode poupar dinheiro ao colocar várias aplicações num único plano do serviço de aplicações. Pode continuar a adicionar aplicações para um plano existente, desde que o plano tem recursos suficientes para processar a carga. No entanto, tenha em mente que aplicações no mesmo plano de serviço de aplicações em que todas partilham os mesmos recursos de computação. Para determinar se a nova aplicação tem os recursos necessários, terá de compreender a capacidade do plano do serviço de aplicações existente e a carga esperada para a nova aplicação. Sobrecarregar um plano do serviço de aplicações pode, potencialmente, levar a períodos de indisponibilidade para as suas aplicações de novas e existentes.

Isolar quando planear a sua aplicação para um novo serviço de aplicações:

- A aplicação está com muitos recursos.
- Pretende dimensionar a aplicação de forma independente a partir de outras aplicações do plano existente.
- A aplicação tem recursos numa região geográfica diferente.

Desta forma pode alocar um novo conjunto de recursos para a sua aplicação e obter um maior controlo das suas aplicações.

## <a name="manage-an-app-service-plan"></a>Gerir um plano do serviço de aplicações

> [!div class="nextstepaction"]
> [Gerir um plano do serviço de aplicações](app-service-plan-manage.md)
