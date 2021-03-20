---
title: Dimensione uma app em ASE v1
description: Escalando uma aplicação em um Ambiente de Serviço de Aplicações. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: ccompy
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0e665ec27da0a898e754817f946b965ac7360fda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86220563"
---
# <a name="scaling-apps-in-an-app-service-environment-v1"></a>Aplicativos de escala em um Ambiente de Serviço de Aplicações v1
No Serviço de Aplicações Azure existem normalmente três coisas que pode escalar:

* plano de preços
* tamanho do trabalhador 
* número de casos.

Num ASE não há necessidade de selecionar ou alterar o plano de preços.  Em termos de capacidades, já se encontra ao nível da capacidade de preços Premium.  

No que diz respeito ao tamanho dos trabalhadores, o administrador da ASE pode atribuir o tamanho do recurso de computação a utilizar para cada piscina de trabalhadores.  Isto significa que você pode ter Worker Pool 1 com recursos de computação P4 e Worker Pool 2 com recursos de computação P1, se desejar.  Não têm de estar em ordem.  Para mais detalhes sobre os tamanhos e os seus preços consulte o documento aqui [Azure App Service Pricing][AppServicePricing].  Isto deixa as opções de escala para aplicações web e planos de serviço de aplicações num Ambiente de Serviço de Aplicações:

* seleção de piscina de trabalhadores
* número de casos

A alteração de qualquer um dos artigos é feita através da UI adequada mostrada para os planos de serviço de aplicações hospedados pela ASE.  

![Screenshot que mostra onde ver os detalhes do plano de serviço escala e o plano de serviço da Piscina dos Trabalhadores.][1]

Não é possível aumentar o seu ASP para além do número de recursos de computação disponíveis na piscina de trabalhadores em que o seu ASP se encontra.  Se precisar de calcular recursos nesse pool de trabalhadores, precisa de obter o seu administrador ASE para adicioná-los.  Para obter informações sobre a reescoguração do seu ASE leia aqui as informações: [Como configurar um ambiente de Serviço de Aplicações][HowtoConfigureASE].  Também pode querer aproveitar as funcionalidades de autoescala ase para adicionar capacidade com base no horário ou métricas.  Para obter mais detalhes sobre a configuração da autoescala para o próprio ambiente ASE, consulte [Como configurar a autoescala para um Ambiente de Serviço de Aplicações][ASEAutoscale].

Você pode criar vários planos de serviço de aplicações usando recursos compute de diferentes piscinas de trabalhadores, ou você pode usar o mesmo pool de trabalhadores.  Por exemplo, se tiver (10) recursos de computação disponíveis no Worker Pool 1, pode optar por criar um plano de serviço de aplicações utilizando (6) recursos de computação, e um segundo plano de serviço de aplicações que utiliza (4) recursos de computação.

### <a name="scaling-the-number-of-instances"></a>Escalonamento do número de ocorrências
Quando cria a sua aplicação web pela primeira vez num Ambiente de Serviço de Aplicações começa com 1 instância.  Em seguida, pode escalar para instâncias adicionais para fornecer recursos de computação adicionais para a sua app.   

Se o seu ASE tem capacidade suficiente, então isto é muito simples.  Você vai ao seu Plano de Serviço de Aplicações que detém os sites que deseja escalar e selecionar Escala.  Isto abre o UI onde pode definir manualmente a balança para o seu ASP ou configurar regras de autoescala para o seu ASP.  Para escalar manualmente a sua aplicação simplesmente definir ***Escala por** _ para _* uma contagem de instância que _eu insira manualmente_**.  A partir daqui, arraste o slider para a quantidade desejada ou introduza-o na caixa ao lado do deslizador.  

![Screenshot que mostra onde pode definir a escala para o seu ASP ou configurar regras de autoescala para o seu ASP.][2] 

As regras de autoescala para um ASP num ASE funcionam da mesma forma que normalmente.  Pode selecionar ***Percentagem de CPU** _ em _*_Escala e_*_ criar regras de autoescala para o seu ASP com base na Percentagem de CPU ou pode criar regras mais complexas usando _as regras_ de agenda e desempenho _* **.  Para ver mais detalhes completos sobre configurar a autoescalação, utilize o guia aqui [Escala uma aplicação no Azure App Service][AppScale]. 

### <a name="worker-pool-selection"></a>Seleção de piscina de trabalhadores
Como já foi notado, a seleção da piscina dos trabalhadores é acedida a partir da UI ASP.  Abra a lâmina para o ASP que pretende escalar e selecionar a piscina do trabalhador.  Você verá todas as piscinas de trabalhadores que você configuraram no seu Ambiente de Serviço de Aplicações.  Se você tem apenas uma piscina de trabalhadores, então você só verá a piscina listada.  Para alterar em que grupo de trabalhadores o seu ASP está, basta selecionar o pool de trabalhadores para onde pretende que o seu Plano de Serviço de Aplicações se mude.  

![Screenshot que mostra onde você pode mudar em que trabalhador pool o seu ASP está dentro.][3]

Antes de mover o seu ASP de uma piscina de trabalhadores para outra, é importante ter a certeza de que terá capacidade adequada para o seu ASP.  Na lista de piscinas de trabalhadores, não só o nome da piscina dos trabalhadores está listado, como também pode ver quantos trabalhadores estão disponíveis nessa piscina de trabalhadores.  Certifique-se de que existem casos suficientes disponíveis para conter o seu Plano de Serviço de Aplicações.  Se precisar de mais recursos de computação no pool de trabalhadores para onde pretende mudar-se, então faça com que o administrador da ASE os adicione.  

> [!NOTE]
> Mover um ASP de um pool de trabalhadores causará arranques frios das aplicações nessa ASP.  Isto pode fazer com que os pedidos decorram lentamente à medida que a sua app está a frio começou com os novos recursos compute.  O arranque a frio pode ser evitado utilizando a capacidade de aquecimento da [aplicação][AppWarmup] no Azure App Service.  O módulo de inicialização de aplicações descrito no artigo também funciona para arranques frios porque o processo de inicialização também é invocado quando as aplicações estão a frio começou com novos recursos de computação. 
> 
> 

## <a name="getting-started"></a>Introdução
Para começar com ambientes de serviço de [aplicações,][HowtoCreateASE] consulte como criar um ambiente de serviço de aplicações

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ScaleWebapp]: ../manage-scale-up.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[CreateWebappinASE]: app-service-web-how-to-create-a-web-app-in-an-ase.md
[Appserviceplans]: ../overview-hosting-plans.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../manage-scale-up.md
[AppWarmup]: https://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
