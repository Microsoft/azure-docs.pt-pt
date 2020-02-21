---
title: Planos do Serviço de Aplicações
description: Saiba como funcionam os planos do Serviço de Aplicações no Azure App Service, como são cobrados ao cliente e como dimensioná-los para as suas necessidades.
keywords: serviço de aplicativos, serviço de aplicações azure, escala, escalável, escalabilidade, plano de serviço de aplicações, custo do serviço de aplicações
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.topic: article
ms.date: 11/09/2017
ms.custom: seodec18
ms.openlocfilehash: f1012f8c00de4b19bbf6206408ec1a806e09e54f
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77482348"
---
# <a name="azure-app-service-plan-overview"></a>Visão geral do plano do serviço de aplicações azure

No Serviço de Aplicações, as aplicações são executadas num _plano do Serviço de Aplicações_. Um plano do Serviço de Aplicações define um conjunto de recursos de computação para a execução da aplicação Web. Estes recursos computacionais são análogos à exploração de [_servidores_](https://wikipedia.org/wiki/Server_farm) em hospedagem web convencional. Uma ou mais aplicações podem ser configuradas para executar os mesmos recursos de computação (ou no mesmo plano de Serviço de Aplicações).

Quando se cria um plano de Serviço de Aplicações numa determinada região (por exemplo, na Europa Ocidental), é criado um conjunto de recursos computacionais para esse plano naquela região. Quaisquer aplicações que você colocou neste plano de App Service executado nestes recursos computacionais como definido pelo seu plano de Serviço de Aplicações. Cada plano de Serviço de Aplicações define:

- Região (Oeste dos EUA, Leste dos EUA, etc.)
- Número de casos vm
- Tamanho das instâncias VM (Pequena, Média, Grande)
- Nível de preços (Grátis, Partilhado, Básico, Standard, Premium, PremiumV2, Isolado)

O _nível de preços_ de um plano de Serviço de Aplicações determina quais as funcionalidades do App Service que obtém e quanto paga pelo plano. Existem algumas categorias de níveis de preços:

- **Computação partilhada**: **Grátis** e **Partilhados**, os dois níveis base, executa uma aplicação no mesmo Azure VM que outras aplicações do App Service, incluindo apps de outros clientes. Estes níveis atribuem quotas de CPU a cada aplicação que funciona com os recursos partilhados, e os recursos não podem ser dimensionados.
- **Computação dedicada**: Os níveis **Basic**, **Standard,** **Premium**e **PremiumV2** executam aplicações em VMs Azure dedicados. Apenas aplicações no mesmo plano de App Service partilham os mesmos recursos de computação. Quanto mais alto for o nível, mais casos de VM estão disponíveis para si para escala-out.
- **Isolado**: Este nível executa VMs Azure dedicados em redes virtuais azure dedicadas. Fornece isolamento de rede em cima do isolamento computacional para as suas apps. Fornece as capacidades máximas de escala.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Cada nível também fornece um subconjunto específico de funcionalidades do Serviço de Aplicações. Estas funcionalidades incluem domínios personalizados e certificados SSL, autoscalcificação, slots de implementação, backups, integração do Gestor de Tráfego, e muito mais. Quanto maior for o nível, mais funcionalidades estão disponíveis. Para saber quais as funcionalidades suportadas em cada nível de preços, consulte [os detalhes do plano do App Service.](https://azure.microsoft.com/pricing/details/app-service/plans/)

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> O novo nível de preços **PremiumV2** fornece [VMs da série Dv2](../virtual-machines/dv2-dsv2-series.md) com processadores mais rápidos, armazenamento SSD e dupla relação memória-núcleo em comparação com o nível **Standard.** **PremiumV2** também suporta maior escala através do aumento da contagem de instâncias, ao mesmo tempo que fornece todas as capacidades avançadas encontradas no plano Standard. Todas as funcionalidades disponíveis no nível **Premium** existente estão incluídas no **PremiumV2**.
>
> Semelhante a outros níveis dedicados, estão disponíveis três tamanhos VM para este nível:
>
> - Pequeno (um núcleo CPU, 3,5 GiB de memória) 
> - Médio (dois núcleos CPU, 7 GiB de memória) 
> - Grande (quatro núcleos cpu, 14 GiB de memória)  
>
> Para obter informações sobre preços **PremiumV2,** consulte preços de serviço de [aplicação](https://azure.microsoft.com/pricing/details/app-service/).
>
> Para começar com o novo nível de preços **PremiumV2,** consulte [configure PremiumV2 tier for App Service](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Como funciona a minha aplicação?

Nos níveis **Free** and **Shared,** uma aplicação recebe minutos de CPU numa instância de VM partilhada e não pode escalar. Em outros níveis, uma aplicação corre e escala da seguinte forma.

Quando cria uma aplicação no App Service, é colocada num plano de Serviço de Aplicações. Quando a aplicação funciona, funciona em todas as instâncias VM configuradas no plano de Serviço de Aplicações. Se várias aplicações estiverem no mesmo plano de Serviço de Aplicações, todas partilham as mesmas instâncias VM. Se tiver várias ranhuras de implementação para uma aplicação, todas as ranhuras de implementação também funcionam nas mesmas instâncias VM. Se ativar registos de diagnóstico, executar backups ou executar WebJobs, também utilizam ciclos de CPU e memória nestas instâncias VM.

Desta forma, o plano de Serviço de Aplicações é a unidade de escala das aplicações do App Service. Se o plano estiver configurado para executar cinco instâncias VM, então todas as aplicações do plano funcionam em todas as cinco instâncias. Se o plano estiver configurado para autodimensionamento, então todas as aplicações do plano são dimensionadas em conjunto com base nas definições de escala automática.

Para obter informações sobre a escala de uma aplicação, consulte a contagem de [exemplos scale manualmente ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Quanto custa o meu plano de Serviço de Aplicações?

Esta secção descreve como as aplicações do App Service são faturadas. Para obter informações detalhadas e específicas sobre preços, consulte os preços do serviço de [aplicações.](https://azure.microsoft.com/pricing/details/app-service/)

Com exceção **do** free tier, um plano de Serviço de Aplicações transporta uma carga horária sobre os recursos computacionais que utiliza.

- No nível **Partilhado,** cada aplicação recebe uma quota de minutos cpu, pelo que _cada aplicação_ é cobrada de hora em hora para a quota CPU.
- Nos níveis de cálculo dedicados (**Basic,** **Standard,** **Premium,** **PremiumV2**), o plano de Serviço de Aplicações define o número de casos vm a que as aplicações são dimensionadas, pelo que _cada instância VM_ no plano de Serviço de Aplicações tem uma carga horária. Estes casos de VM são cobrados da mesma forma, independentemente do número de aplicações que estão a ser em execução. Para evitar cargas inesperadas, consulte A Limpeza de um plano de Serviço de [Aplicações](app-service-plan-manage.md#delete).
- No nível **Isolado,** o Ambiente de Serviço de Aplicações define o número de trabalhadores isolados que executam as suas apps, e _cada trabalhador_ é cobrado de hora em hora. Além disso, há uma taxa base de hora em hora para a execução do próprio App Service Environment.

Não é cobrado por utilizar as funcionalidades do Serviço de Aplicações que estão disponíveis para si (configurar domínios personalizados, certificados SSL, ranhuras de implementação, backups, etc.). As exceções são:

- Domínios de Serviço de Aplicações - paga-se quando compra um em Azure e quando o renova todos os anos.
- Certificados de Serviço de Aplicações - paga-se quando compra um em Azure e quando o renova todos os anos.
- Ligações SSL baseadas em IP - Há uma taxa horária para cada ligação SSL baseada em IP, mas algum nível **Standard** ou acima dá-lhe uma ligação SSL baseada em IP gratuitamente. As ligações SSL baseadas em SNI são gratuitas.

> [!NOTE]
> Se integrar o Serviço de Aplicações com outro serviço Azure, poderá ter de considerar os encargos destes outros serviços. Por exemplo, se utilizar o Gestor de Tráfego Azure para escalar a sua aplicação geograficamente, o Azure Traffic Manager também o cobra com base no seu uso. Para estimar o seu custo de serviços transversais em Azure, consulte [a calculadora de preços](https://azure.microsoft.com/pricing/calculator/). 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>E se a minha aplicação precisar de mais capacidades ou funcionalidades?

O seu plano de Serviço de Aplicações pode ser dimensionado para cima e para baixo a qualquer momento. É tão simples como alterar o nível de preços do plano. Pode escolher um nível de preços mais baixo no início e aumentar mais tarde quando precisar de mais funcionalidades do Serviço de Aplicações.

Por exemplo, pode começar a testar a sua aplicação web num plano de Serviço de Aplicações **Gratuito** e não pagar nada. Quando quiser adicionar o seu [nome DNS personalizado](app-service-web-tutorial-custom-domain.md) à aplicação web, basta escalar o seu plano até ao nível **De partilha.** Mais tarde, quando quiser [criar uma ligação SSL,](configure-ssl-bindings.md)dimensione o seu plano até ao nível **Básico.** Quando quiser ter ambientes de [encenação,](deploy-staging-slots.md)escale até o nível **Standard.** Quando você precisa de mais núcleos, memória ou armazenamento, escala até um tamanho VM maior no mesmo nível.

O mesmo funciona ao contrário. Quando sente que já não precisa das capacidades ou características de um nível mais alto, pode descer para um nível mais baixo, o que poupa dinheiro.

Para obter informações sobre a ampliação do plano de serviço de aplicações, consulte [scale up uma app em Azure](manage-scale-up.md).

Se a sua aplicação estiver no mesmo plano de App Service com outras aplicações, poderá querer melhorar o desempenho da aplicação isolando os recursos da computação. Pode fazê-lo movendo a aplicação para um plano de Serviço de Aplicações separado. Para mais informações, consulte Mover uma aplicação para outro plano de Serviço de [Aplicações.](app-service-plan-manage.md#move)

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Devo colocar uma aplicação num novo plano ou num plano existente?

Uma vez que paga os recursos de computação que o seu plano de Serviço de Aplicações atribui (ver [quanto custa o meu plano de Serviço de Aplicações?](#cost) Pode continuar a adicionar apps a um plano existente, desde que o plano tenha recursos suficientes para lidar com a carga. No entanto, tenha em mente que as aplicações no mesmo plano de App Service partilham todos os mesmos recursos computacionais. Para determinar se a nova aplicação tem os recursos necessários, precisa de compreender a capacidade do plano de Serviço de Aplicações existente e a carga esperada para a nova aplicação. Sobrecarregar um plano de Serviço de Aplicações pode potencialmente causar tempo de inatividade para as suas novas e existentes aplicações.

Isole a sua aplicação num novo plano de Serviço de Aplicações quando:

- A aplicação é intensiva em recursos.
- Deseja escalar a aplicação de forma independente das outras aplicações do plano existente.
- A aplicação precisa de recursos numa região geográfica diferente.

Desta forma, pode alocar um novo conjunto de recursos para a sua app e ganhar um maior controlo das suas apps.

## <a name="manage-an-app-service-plan"></a>Gerir um plano de serviço de aplicações

> [!div class="nextstepaction"]
> [Gerir um plano de serviço de aplicações](app-service-plan-manage.md)
