---
title: Visão geral do plano do serviço de aplicativo – Azure | Microsoft Docs
description: Saiba como os planos do serviço de aplicativo para Azure App serviço funcionam e como eles beneficiam sua experiência de gerenciamento.
keywords: serviço de aplicativo, serviço de aplicativo do Azure, escala, escalonável, escalabilidade, plano do serviço de aplicativo, custo do serviço de aplicativo
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 78440185b4a26bccc8ffb0258416a19aa929af6b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470250"
---
# <a name="azure-app-service-plan-overview"></a>Visão geral do plano de serviço Azure App

No Serviço de Aplicações, as aplicações são executadas num _plano do Serviço de Aplicações_. Um plano do Serviço de Aplicações define um conjunto de recursos de computação para a execução da aplicação Web. Esses recursos de computação são análogos ao [_farm de servidores_](https://wikipedia.org/wiki/Server_farm) na hospedagem na Web convencional. Um ou mais aplicativos podem ser configurados para serem executados nos mesmos recursos de computação (ou no mesmo plano do serviço de aplicativo).

Quando você cria um plano do serviço de aplicativo em uma determinada região (por exemplo, Europa Ocidental), um conjunto de recursos de computação é criado para esse plano nessa região. Quaisquer aplicativos que você coloque nesse plano do serviço de aplicativo são executados nesses recursos de computação, conforme definido pelo seu plano do serviço de aplicativo. Cada plano do serviço de aplicativo define:

- Região (oeste dos EUA, leste dos EUA, etc.)
- Número de instâncias de VM
- Tamanho das instâncias de VM (pequeno, médio, grande)
- Tipo de preço (gratuito, compartilhado, básico, Standard, Premium, PremiumV2, isolado)

O _tipo de preço_ de um plano do serviço de aplicativo determina quais recursos do serviço de aplicativo você obtém e quanto você paga para o plano. Há algumas categorias de tipos de preço:

- **Computação compartilhada**: **gratuita** e **compartilhada**, as duas camadas de base, o executa um aplicativo na mesma VM do Azure que outros aplicativos do serviço de aplicativo, incluindo aplicativos de outros clientes. Essas camadas alocam cotas de CPU para cada aplicativo que é executado nos recursos compartilhados, e os recursos não podem ser expandidos.
- **Computação dedicada**: as camadas **Basic**, **Standard**, **Premium**e **PremiumV2** executam aplicativos em VMs do Azure dedicadas. Somente os aplicativos no mesmo plano do serviço de aplicativo compartilham os mesmos recursos de computação. Quanto maior a camada, mais instâncias de VM estarão disponíveis para você para expansão.
- **Isolado**: essa camada executa VMs do Azure dedicadas em redes virtuais do Azure dedicadas. Ele fornece isolamento de rede na parte superior do isolamento de computação para seus aplicativos. Ele fornece os recursos máximos de expansão.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Cada camada também fornece um subconjunto específico de recursos do serviço de aplicativo. Esses recursos incluem domínios personalizados e certificados SSL, dimensionamento automático, slots de implantação, backups, integração do Gerenciador de tráfego e muito mais. Quanto maior a camada, mais recursos estarão disponíveis. Para descobrir quais recursos têm suporte em cada tipo de preço, consulte [detalhes do plano do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/).

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> O novo tipo de preço **PremiumV2** fornece [VMs da série Dv2](../virtual-machines/windows/sizes-general.md#dv2-series) com processadores mais rápidos, armazenamento SSD e proporção de memória e núcleo duplo em comparação com a camada **Standard** . O **PremiumV2** também dá suporte à escala maior por meio de maior contagem de instâncias e ainda fornece todos os recursos avançados encontrados no plano padrão. Todos os recursos disponíveis na camada **Premium** existente estão incluídos no **PremiumV2**.
>
> Semelhante a outras camadas dedicadas, três tamanhos de VM estão disponíveis para essa camada:
>
> - Pequeno (um núcleo de CPU, 3,5 GiB de memória) 
> - Médio (dois núcleos de CPU, 7 GiB de memória) 
> - Grande (quatro núcleos de CPU, 14 GiB de memória)  
>
> Para obter informações sobre preços do **PremiumV2** , consulte [preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/).
>
> Para começar a usar o novo tipo de preço do **PremiumV2** , consulte [Configurar a camada PremiumV2 para o serviço de aplicativo](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Como meu aplicativo é executado e dimensionado?

Nas camadas **gratuita** e **compartilhada** , um aplicativo recebe minutos de CPU em uma instância de VM compartilhada e não pode escalar horizontalmente. Em outras camadas, um aplicativo é executado e dimensionado da seguinte maneira.

Quando você cria um aplicativo no serviço de aplicativo, ele é colocado em um plano do serviço de aplicativo. Quando o aplicativo é executado, ele é executado em todas as instâncias de VM configuradas no plano do serviço de aplicativo. Se vários aplicativos estiverem no mesmo plano do serviço de aplicativo, todos eles compartilharão as mesmas instâncias de VM. Se você tiver vários slots de implantação para um aplicativo, todos os slots de implantação também são executados nas mesmas instâncias de VM. Se você habilitar os logs de diagnóstico, executar backups ou executar trabalhos Web, eles também usarão os ciclos de CPU e a memória nessas instâncias de VM.

Dessa forma, o plano do serviço de aplicativo é a unidade de escala dos aplicativos do serviço de aplicativo. Se o plano estiver configurado para executar cinco instâncias de VM, todos os aplicativos no plano serão executados em todas as cinco instâncias. Se o plano estiver configurado para dimensionamento automático, todos os aplicativos no plano serão dimensionados juntos com base nas configurações de autoescala.

Para obter informações sobre como escalar horizontalmente um aplicativo, consulte [dimensionar a contagem de instâncias manual ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Quanto custa o plano do serviço de aplicativo?

Esta seção descreve como os aplicativos do serviço de aplicativo são cobrados. Para obter informações detalhadas sobre preços específicos da região, consulte [preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/).

Exceto para a camada **gratuita** , um plano do serviço de aplicativo recebe uma cobrança por hora sobre os recursos de computação que ele usa.

- Na camada **compartilhada** , cada aplicativo recebe uma cota de minutos de CPU, de modo que _cada aplicativo_ é cobrado por hora para a cota de CPU.
- Nas camadas de computação dedicadas (**Basic**, **Standard**, **Premium**, **PremiumV2**), o plano do serviço de aplicativo define o número de instâncias de VM para as quais os aplicativos são dimensionados, de modo que _cada instância de VM_ no plano do serviço de aplicativo tem um custo por hora. Essas instâncias de VM são cobradas da mesma forma, independentemente de quantos aplicativos estão em execução nelas. Para evitar encargos inesperados, consulte [limpar um plano do serviço de aplicativo](app-service-plan-manage.md#delete).
- Na camada **isolada** , o ambiente do serviço de aplicativo define o número de trabalhadores isolados que executam seus aplicativos e _cada trabalho_ é cobrado por hora. Além disso, há uma taxa de base por hora para a execução do Ambiente do Serviço de Aplicativo em si.

Você não é cobrado pelo uso dos recursos do serviço de aplicativo que estão disponíveis para você (Configurando domínios personalizados, certificados SSL, slots de implantação, backups, etc.). As exceções são:

- Domínios do serviço de aplicativo-você paga ao comprar um no Azure e quando renová-lo todos os anos.
- Certificados do serviço de aplicativo-você paga ao comprar um no Azure e quando renová-lo por ano.
- Conexões SSL baseadas em IP – há uma cobrança por hora para cada conexão SSL baseada em IP, mas alguma camada **Standard** ou superior fornece uma conexão SSL baseada em IP gratuitamente. Conexões SSL baseadas em SNI são gratuitas.

> [!NOTE]
> Se você integrar o serviço de aplicativo com outro serviço do Azure, talvez seja necessário considerar os encargos desses outros serviços. Por exemplo, se você usar o Gerenciador de tráfego do Azure para dimensionar seu aplicativo geograficamente, o Gerenciador de tráfego do Azure também cobrará com base no seu uso. Para estimar o custo de serviços cruzados no Azure, consulte [calculadora de preços](https://azure.microsoft.com/pricing/calculator/). 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>E se meu aplicativo precisar de mais recursos ou recursos?

Seu plano do serviço de aplicativo pode ser escalado verticalmente e horizontalmente a qualquer momento. É tão simples quanto alterar o tipo de preço do plano. Você pode escolher um tipo de preço mais baixo primeiro e escalar verticalmente mais tarde quando precisar de mais recursos do serviço de aplicativo.

Por exemplo, você pode começar a testar seu aplicativo Web em um plano do serviço de aplicativo **gratuito** e não pagar nada. Quando você quiser adicionar seu [nome DNS personalizado](app-service-web-tutorial-custom-domain.md) ao aplicativo Web, basta dimensionar seu plano até a camada **compartilhada** . Posteriormente, quando você quiser [criar uma associação SSL](configure-ssl-bindings.md), dimensione seu plano até a camada **básica** . Quando você quiser ter [ambientes de preparo](deploy-staging-slots.md), escale verticalmente para a camada **Standard** . Quando você precisar de mais núcleos, memória ou armazenamento, escale verticalmente para um tamanho maior de VM na mesma camada.

O mesmo funciona no inverso. Quando você achar que não precisa mais dos recursos ou recursos de uma camada superior, poderá reduzir verticalmente para uma camada inferior, o que poupa dinheiro.

Para obter informações sobre como escalar verticalmente o plano do serviço de aplicativo, consulte [escalar verticalmente um aplicativo no Azure](manage-scale-up.md).

Se seu aplicativo estiver no mesmo plano do serviço de aplicativo com outros aplicativos, talvez você queira melhorar o desempenho do aplicativo isolando os recursos de computação. Você pode fazer isso movendo o aplicativo para um plano do serviço de aplicativo separado. Para obter mais informações, consulte [mover um aplicativo para outro plano do serviço de aplicativo](app-service-plan-manage.md#move).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Devo colocar um aplicativo em um novo plano ou um plano existente?

Como você paga pelos recursos de computação que seu plano do serviço de aplicativo aloca (veja [quanto custa o plano do serviço de aplicativo?](#cost)), potencialmente, você pode economizar dinheiro colocando vários aplicativos em um plano do serviço de aplicativo. Você pode continuar a adicionar aplicativos a um plano existente, contanto que o plano tenha recursos suficientes para lidar com a carga. No entanto, tenha em mente que os aplicativos no mesmo plano do serviço de aplicativo compartilham os mesmos recursos de computação. Para determinar se o novo aplicativo tem os recursos necessários, você precisa entender a capacidade do plano do serviço de aplicativo existente e a carga esperada para o novo aplicativo. A sobrecarga de um plano do serviço de aplicativo pode causar tempo de inatividade para seus aplicativos novos e existentes.

Isole seu aplicativo em um novo plano do serviço de aplicativo quando:

- O aplicativo tem uso intensivo de recursos.
- Você deseja dimensionar o aplicativo independentemente dos outros aplicativos no plano existente.
- O aplicativo precisa de recursos em uma região geográfica diferente.

Dessa forma, você pode alocar um novo conjunto de recursos para seu aplicativo e obter mais controle sobre seus aplicativos.

## <a name="manage-an-app-service-plan"></a>Gerenciar um plano do serviço de aplicativo

> [!div class="nextstepaction"]
> [Gerenciar um plano do serviço de aplicativo](app-service-plan-manage.md)
