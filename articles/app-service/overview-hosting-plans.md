---
title: Planos do Serviço de Aplicações
description: Saiba como os planos do Serviço de Aplicações funcionam no Azure App Service, como são cobrados ao cliente e como dimensioná-los para as suas necessidades.
keywords: serviço de aplicações, serviço de aplicativos azure, escala, escalável, escala, plano de serviço de aplicações, custo de serviço de aplicações
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: 6e5de3cdec7a9c503f4b7bf7056bd62f1ddf682d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594020"
---
# <a name="azure-app-service-plan-overview"></a>Descrição Geral do plano do Serviço de Aplicações do Azure

No Serviço de Aplicações (Aplicações Web, Apps API ou Aplicações Móveis), uma aplicação funciona sempre num _plano de Serviço de Aplicações._ Além disso, [a Azure Functions](../azure-functions/dedicated-plan.md) também tem a opção de executar um _plano de Serviço de Aplicações._ Um plano do Serviço de Aplicações define um conjunto de recursos de computação para a execução da aplicação Web. Estes recursos computacional são análogos à fazenda de [_servidores_](https://wikipedia.org/wiki/Server_farm) no alojamento web convencional. Uma ou mais aplicações podem ser configuradas para funcionar nos mesmos recursos informáticos (ou no mesmo plano de Serviço de Aplicações).

Quando se cria um plano de Serviço de Aplicações numa determinada região (por exemplo, Europa Ocidental), é criado um conjunto de recursos computacionares para esse plano naquela região. Quaisquer que sejam as aplicações que você colocou neste plano de Serviço de Aplicações executam estes recursos computacional como definido pelo seu plano de Serviço de Aplicações. Cada Plano do Serviço de Aplicações define o seguinte:

- Região (E.U.A. Oeste, E.U.A. Leste, etc.)
- Número de instâncias de VM
- Tamanho das instâncias de VM (pequena, média, grande)
- Preço (Grátis, Partilhado, Básico, Standard, Premium, PremiumV2, PremiumV3, Isolado)

O _nível_ de preços de um plano de Serviço de Aplicações determina quais as funcionalidades do Serviço de Aplicações que obtém e quanto paga pelo plano. Existem algumas categorias de escalões de preço:

- **Computação partilhada**: **Grátis** e **Partilhadas,** os dois níveis base, executa uma aplicação no mesmo Azure VM que outras aplicações do App Service, incluindo apps de outros clientes. Estes escalões alocam quotas de CPU a cada uma das aplicações que executam nos recursos partilhados e os recursos não podem ser aumentados horizontalmente.
- **Computação dedicada**: Os níveis **Básico,** **Standard,** **Premium,** **PremiumV2** e **PremiumV3** executam aplicações em VMs Azure dedicados. Apenas as aplicações no mesmo plano do Serviço de Aplicações partilham os mesmos recursos de computação. Quanto maior for o escalão, mais instâncias de VM estarão disponíveis para escalamento horizontal.
- **Isolado**: Este nível executa VMs Azure dedicados em redes virtuais dedicadas Azure. Proporciona isolamento de rede em cima do isolamento computacional às suas aplicações. Fornece as capacidades máximas de escalamento horizontal.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Cada nível também fornece um subconjunto específico de funcionalidades do Serviço de Aplicações. Estas funcionalidades incluem domínios personalizados e certificados TLS/SSL, autoscaling, slots de implementação, backups, integração do Traffic Manager, entre outros. Quanto maior for o nível, mais funcionalidades estão disponíveis. Para saber quais as funcionalidades suportadas em cada nível de preços, consulte os [detalhes do plano do Serviço de Aplicações.](https://azure.microsoft.com/pricing/details/app-service/plans/)

<a name="new-pricing-tier-premiumv3"></a>

> [!NOTE]
> O novo nível de preços **PremiumV3** garante máquinas com processadores mais rápidos (mínimo de 195 [ACU](../virtual-machines/acu.md) por CPU virtual), armazenamento SSD e relação memória-núcleo quádruplo em comparação com o nível **Standard.** **O PremiumV3** também suporta uma maior escala através do aumento da contagem de instâncias, ao mesmo tempo que fornece todas as capacidades avançadas encontradas no nível **Standard.** Todas as funcionalidades disponíveis no nível **PremiumV2** existente estão incluídas no **PremiumV3**.
>
> Semelhantes a outros níveis dedicados, estão disponíveis três tamanhos VM para este nível:
>
> - Pequeno (núcleo de 2 CPU, 8 GiB de memória) 
> - Médio (4 núcleos cpu, 16 GiB de memória) 
> - Grande (8 núcleos de CPU, 32 GiB de memória)  
>
> Para obter informações sobre preços **PremiumV3,** consulte [o Preço do Serviço de Aplicações.](https://azure.microsoft.com/pricing/details/app-service/)
>
> Para começar com o novo nível de preços **PremiumV3,** consulte o [nível Configure PremiumV3 para o Serviço de Aplicações.](app-service-configure-premium-tier.md)

## <a name="how-does-my-app-run-and-scale"></a>Como é que a minha aplicação funciona e escala?

Nos níveis **Gratuito** e **Partilhado,** uma aplicação recebe minutos de CPU numa instância VM partilhada e não consegue escalar. Em outros níveis, uma aplicação corre e escala da seguinte forma.

Quando cria uma aplicação no Serviço de Aplicações, é colocada num plano de Serviço de Aplicações. Quando a aplicação é executada, executa em todas as instâncias VM configuradas no plano de Serviço de Aplicações. Se várias aplicações estiverem no mesmo plano de Serviço de Aplicações, todas partilham as mesmas instâncias VM. Se tiver várias ranhuras de implementação para uma aplicação, todas as ranhuras de implementação também são executadas nas mesmas instâncias VM. Se ativar registos de diagnóstico, realizar backups ou executar WebJobs, também utiliza ciclos de CPU e memória nestas instâncias VM.

Desta forma, o plano de Serviço de Aplicações é a unidade de escala das aplicações do Serviço de Aplicações. Se o plano estiver configurado para executar cinco instâncias VM, então todas as aplicações do plano são executadas em todas as cinco instâncias. Se o plano estiver configurado para autoscaling, então todas as aplicações do plano são dimensionadas em conjunto com base nas definições de autoescala.

Para obter informações sobre a escala de uma aplicação, consulte [a contagem de instâncias de escala manual ou automática](../azure-monitor/autoscale/autoscale-get-started.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Quanto custa o meu plano de Serviço de Aplicações?

Esta secção descreve como as aplicações do Serviço de Aplicações são faturadas. Para obter informações detalhadas sobre preços específicos da região, consulte [o Preço do Serviço de Aplicações](https://azure.microsoft.com/pricing/details/app-service/).

Com exceção do **nível gratuito,** um plano de Serviço de Aplicações tem uma taxa sobre os recursos computacionáveis que utiliza.

- No nível **Partilhado,** cada aplicação recebe uma quota de minutos de CPU, pelo que _cada aplicação_ é cobrada pela quota do CPU.
- Nos níveis de computação dedicados (**Básico,** **Standard,** **Premium,** **PremiumV2**, **PremiumV3),** o plano de Serviço de Aplicações define o número de instâncias VM a que as aplicações são dimensionados, pelo que _cada instância VM_ no plano de Serviço de Aplicações é cobrada. Estas instâncias VM são cobradas da mesma forma independentemente de quantas aplicações estão a ser executadas nelas. Para evitar tarifas inesperadas, consulte [Clean up a App Service plan](app-service-plan-manage.md#delete).
- No nível **isolado,** o Ambiente de Serviço de Aplicações define o número de trabalhadores isolados que executam as suas apps, e _cada trabalhador_ é cobrado. Além disso, há uma taxa de selo plana para a execução do próprio Ambiente de Serviço de Aplicações.

Não é cobrado por usar as funcionalidades do Serviço de Aplicações que estão disponíveis para si (configurar domínios personalizados, certificados TLS/SSL, slots de implementação, backups, etc.). As exceções são:

- Domínios de Serviço de Aplicações - paga-se quando compra um no Azure e quando o renova todos os anos.
- Certificados de Serviço de Aplicações - paga-se quando compra um no Azure e quando o renova todos os anos.
- Conexões TLS baseadas em IP - Há uma taxa de hora para cada ligação TLS baseada em IP, mas algum nível **Standard** ou superior dá-lhe uma ligação TLS baseada em IP gratuitamente. As ligações TLS baseadas em SNI são gratuitas.

> [!NOTE]
> Se integrar o Serviço de Aplicações com outro serviço Azure, poderá ter de considerar os custos destes outros serviços. Por exemplo, se utilizar o Azure Traffic Manager para escalar a sua aplicação geograficamente, o Azure Traffic Manager também o cobra com base no seu uso. Para estimar o custo dos seus serviços cruzados em Azure, consulte [a calculadora de preços.](https://azure.microsoft.com/pricing/calculator/) 

Quer otimizar e economizar nos gastos na nuvem?

[!INCLUDE [cost-management-horizontal](../../includes/cost-management-horizontal.md)]

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>E se a minha aplicação precisar de mais capacidades ou funcionalidades?

O seu plano do Serviço de Aplicações pode ser aumentado e reduzido verticalmente em qualquer altura. É tão simples como alterar o nível de preços do plano. Pode escolher um escalão de preço mais baixo inicialmente e aumentar verticalmente mais tarde quando precisar de mais funcionalidades do Serviço de Aplicações.

Por exemplo, pode começar a testar a sua aplicação web num plano de Serviço de Aplicações **Gratuitas** e não pagar nada. Quando quiser adicionar o seu [nome DNS personalizado](app-service-web-tutorial-custom-domain.md) à aplicação web, basta escalar o seu plano para o nível **Partilhado.** Mais tarde, quando quiser [criar uma ligação TLS,](configure-ssl-bindings.md)dimensione o seu plano até ao nível **Básico.** Quando quiser ter [ambientes de preparação,](deploy-staging-slots.md)dimensione até ao nível **Standard.** Quando precisar de mais núcleos, memória ou armazenamento, dimensione até um tamanho VM maior no mesmo nível.

O mesmo funciona ao contrário. Quando sente que já não precisa das capacidades ou características de um nível superior, pode reduzir-se para um nível mais baixo, o que lhe poupa dinheiro.

Para obter informações sobre o escalonamento do plano de Serviço de [Aplicações, consulte Scale up uma aplicação em Azure](manage-scale-up.md).

Se a sua aplicação estiver no mesmo plano de Serviço de Aplicações com outras aplicações, talvez queira melhorar o desempenho da aplicação isolando os recursos de computação. Pode fazê-lo movendo a aplicação para um plano de Serviço de Aplicações separado. Para obter mais informações, consulte [mover uma aplicação para outro plano de Serviço de Aplicações.](app-service-plan-manage.md#move)

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Devo colocar uma aplicação num novo plano ou num plano existente?

Uma vez que paga pelos recursos informáticos que o seu plano de Serviço de Aplicações atribui (ver [quanto custa o meu plano de Serviço de Aplicações?](#cost) Pode continuar a adicionar aplicações a um plano existente, desde que o plano tenha recursos suficientes para lidar com a carga. No entanto, tenha em mente que as aplicações no mesmo plano do Serviço de Aplicações partilham todos os mesmos recursos compute. Para determinar se a nova aplicação tem os recursos necessários, terá de compreender a capacidade do plano do Serviço de Aplicações existente e a carga esperada para a nova aplicação. Sobrecarregar um plano do Serviço de Aplicações pode, potencialmente, levar a períodos de indisponibilidade para as suas aplicações novas e existentes.

Isole a sua aplicação num novo plano do Serviço de Aplicações quando:

- A aplicação é intensiva em recursos.
- Pretende escalar a aplicação de forma independente das outras aplicações do plano existente.
- A aplicação precisa de recursos numa região geográfica diferente.

Desta forma, pode alocar um novo conjunto de recursos para a sua app e ganhar um maior controlo das suas apps.

## <a name="manage-an-app-service-plan"></a>Gerir um plano de Serviço de Aplicações

> [!div class="nextstepaction"]
> [Gerir um plano de Serviço de Aplicações](app-service-plan-manage.md)