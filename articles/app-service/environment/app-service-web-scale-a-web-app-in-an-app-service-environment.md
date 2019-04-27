---
title: Como dimensionar uma aplicação num ambiente de serviço de aplicações - Azure
description: Dimensionar uma aplicação num ambiente de serviço de aplicações
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: jimbe
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 6e683eb07b690d7d5680b7a4d429d1150f22f67e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60767718"
---
# <a name="scaling-apps-in-an-app-service-environment"></a>Dimensionar aplicações num Ambiente do Serviço de Aplicações
No serviço de aplicações do Azure, existem normalmente três coisas que pode ser dimensionado:

* Plano de preços
* tamanho de função de trabalho 
* número de instâncias.

Num ASE não é necessário selecionar ou alterar o plano de preços.  Em termos de recursos já se encontra num nível de capacidade de preços Premium.  

Em relação aos tamanhos de função de trabalho, o administrador de ASE pode atribuir o tamanho do recurso de computação a ser utilizado para cada conjunto de trabalho.  Isso significa que pode ter 1 do conjunto de trabalho com recursos de computação do P4 e recursos, de computação do conjunto de trabalho 2 com P1 se assim o desejar.  Não têm de estar numa ordem de tamanho.  Para obter detalhes sobre os tamanhos e respetivos preços, consulte o documento aqui [preços do Azure App Service][AppServicePricing].  Isso deixa as opções de dimensionamento para aplicações web e planos do serviço de aplicações num ambiente de serviço de aplicações para ser:

* seleção de conjunto de trabalho
* número de instâncias

A alteração de algum item é efetuada através da IU adequada apresentada para o seu ASE alojado planos do serviço de aplicações.  

![][1]

Não é possível aumentar verticalmente o ASP, além do número de recursos de computação disponíveis no conjunto de trabalho que está a seu ASP.  Se precisar de recursos desse conjunto de trabalho de computação terá de obter o seu administrador de ASE para adicioná-los.  Para obter informações em torno de reconfigurar o seu ASE ler as informações aqui: [Como configurar um ambiente de serviço de aplicações][HowtoConfigureASE].  Também poderá tirar partido das funcionalidades de dimensionamento automático ASE para adicionar a capacidade com base num agendamento ou métricas.  Para obter mais detalhes sobre como configurar o dimensionamento automático para o ambiente de ASE próprio ver [como configurar o dimensionamento automático para um ambiente de serviço de aplicações][ASEAutoscale].

Pode criar a aplicação de vários planos de serviço com recursos de computação de conjuntos de trabalho diferentes ou pode utilizar o mesmo conjunto de trabalho.  Por exemplo, se tiver (10) recursos de computação disponível em 1 de conjunto de trabalho, pode optar por criar um plano de serviço de aplicações com os recursos de computação (6) e um serviço de aplicações de segundo plano que utiliza (4) recursos de computação.

### <a name="scaling-the-number-of-instances"></a>Dimensionar o número de instâncias
Quando cria pela primeira vez a aplicação web num ambiente de serviço de aplicações começa com 1 instância.  Em seguida, pode aumentar horizontalmente para instâncias adicionais para fornecer recursos de computação adicionais para a sua aplicação.   

Se o seu ASE tiver capacidade suficiente, em seguida, isso é muito simples.  Vá para o seu plano do serviço de aplicações mantém os sites que pretende aumentar verticalmente e selecione dimensionamento.  Esta ação abre a interface do Usuário em que pode manualmente definir a escala do ASP ou configurar regras de dimensionamento automático para o ASP.  Para dimensionar manualmente a sua aplicação simplesmente defina ***Dimensionar*** ao ***uma contagem de instâncias que introduzo manualmente***.  A partir daqui arraste o controlo de deslize para a quantidade desejada ou introduza-o na caixa junto do controlo de deslize.  

![][2] 

As regras de dimensionamento automático para um ASP num ASE funcionam da mesma forma que normalmente.  Pode selecionar ***percentagem de CPU*** sob ***Dimensionar por*** e criar regras de dimensionamento automático para o ASP com base na percentagem de CPU, ou pode criar regras mais complexas utilizando ***regras de agendamento e desempenho*** .  Para ver mais detalhes completos sobre como configurar o dimensionamento automático usar o guia aqui [dimensionar uma aplicação no App Service do Azure][AppScale]. 

### <a name="worker-pool-selection"></a>Seleção de conjunto de trabalho
Conforme observado anteriormente, a seleção de conjunto de trabalho é acedida a partir da interface do Usuário do ASP.  Abra o painel para o ASP que pretende dimensionar e selecione o conjunto de trabalho.  Verá todos os conjuntos de trabalho que tiver configurado no seu ambiente de serviço de aplicações.  Se tiver apenas um trabalho pool, em seguida, verá apenas um conjunto listado.  Para alterar que conjunto de trabalho está a seu ASP, basta selecionar o conjunto de trabalho que pretende que o seu plano de serviço de aplicações para mover para.  

![][3]

Antes de passar o ASP do conjunto de trabalho de um para outro é importante certificar-se de que terá a capacidade adequada para seu ASP.  Na lista de conjuntos de trabalho, não só é o nome de conjunto de trabalho listado como pode também ver como muitos trabalhadores estão disponíveis nesse agrupamento de trabalho.  Certifique-se de que existem instâncias suficientes conter o seu plano do serviço de aplicações.  Se precisar de mais recursos de computação no conjunto de trabalho que pretende mover para, em seguida, obter o seu administrador de ASE para adicioná-los.  

> [!NOTE]
> Mover um ASP do agrupamento de um operador de fará com que a frio inicia as aplicações nesse ASP.  Isso pode causar pedidos a ser executado lentamente conforme a sua aplicação está fria trabalhar com os novos recursos de computação.  A arranque a frio pode ser evitada, utilizando o [disponibilize a funcionalidade de aplicativo] [ AppWarmup] no serviço de aplicações do Azure.  O módulo de inicialização de aplicação descrito no artigo também funciona para arranques a frio, porque o processo de inicialização também é invocado quando as aplicações são frios iniciado em novos recursos de computação. 
> 
> 

## <a name="getting-started"></a>Introdução
Para começar a utilizar com ambientes de serviço de aplicações, veja [como para criar uma aplicação de ambiente de serviço][HowtoCreateASE]

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ScaleWebapp]: ../web-sites-scale.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[CreateWebappinASE]: app-service-web-how-to-create-a-web-app-in-an-ase.md
[Appserviceplans]: ../overview-hosting-plans.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../web-sites-scale.md
[AppWarmup]: https://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
