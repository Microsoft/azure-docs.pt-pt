---
title: Como dimensionar um aplicativo em um Ambiente do Serviço de Aplicativo-Azure
description: Dimensionando um aplicativo em um Ambiente do Serviço de Aplicativo
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: jimbe
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 711dc4d59785418d6637eb144b644948ed495e2c
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069734"
---
# <a name="scaling-apps-in-an-app-service-environment"></a>Dimensionar aplicações num Ambiente do Serviço de Aplicações
No Azure App serviço, normalmente há três coisas que você pode dimensionar:

* plano de preços
* tamanho do trabalhador 
* número de instâncias.

Em um ASE, não é necessário selecionar ou alterar o plano de preços.  Em termos de recursos, ele já está em um nível de capacidade de preço premium.  

Em relação aos tamanhos de trabalho, o administrador do ASE pode atribuir o tamanho do recurso de computação a ser usado para cada pool de trabalho.  Isso significa que você pode ter o pool de trabalho 1 com os recursos de computação P4 e o pool de trabalho 2 com recursos de computação P1, se desejado.  Eles não precisam estar em ordem de tamanho.  Para obter detalhes sobre os tamanhos e seus preços, consulte o documento aqui [Azure app preço do serviço][AppServicePricing].  Isso deixa as opções de dimensionamento para aplicativos Web e planos do serviço de aplicativo em um Ambiente do Serviço de Aplicativo ser:

* seleção do pool de trabalho
* número de instâncias

A alteração de qualquer item é feita por meio da interface do usuário apropriada mostrada para seus planos do serviço de aplicativo hospedado do ASE.  

![][1]

Você não pode escalar verticalmente o ASP além do número de recursos de computação disponíveis no pool de trabalho no qual seu ASP está.  Se precisar de recursos de computação nesse pool de trabalho, você precisará obter o administrador do ASE para adicioná-los.  Para obter informações sobre como reconfigurar seu ASE, leia as informações aqui: [Como configurar um ambiente do serviço de aplicativo][HowtoConfigureASE].  Talvez você também queira aproveitar os recursos de dimensionamento automático do ASE para adicionar capacidade com base na agenda ou métricas.  Para obter mais detalhes sobre a configuração de dimensionamento automático para o próprio ambiente do ASE, consulte [como configurar o dimensionamento automático para um ambiente do serviço de aplicativo][ASEAutoscale].

Você pode criar vários planos do serviço de aplicativo usando recursos de computação de diferentes pools de trabalho ou pode usar o mesmo pool de trabalho.  Por exemplo, se você tiver (10) recursos de computação disponíveis no pool de trabalho 1, poderá optar por criar um plano do serviço de aplicativo usando (6) recursos de computação e um segundo plano do serviço de aplicativo que usa (4) recursos de computação.

### <a name="scaling-the-number-of-instances"></a>Dimensionando o número de instâncias
Quando você cria seu aplicativo Web em um Ambiente do Serviço de Aplicativo ele começa com 1 instância.  Em seguida, você pode expandir para instâncias adicionais para fornecer recursos de computação adicionais para seu aplicativo.   

Se seu ASE tiver capacidade suficiente, isso é muito simples.  Você vai para o plano do serviço de aplicativo que contém os sites que você deseja escalar verticalmente e selecionar escala.  Isso abre a interface do usuário, na qual você pode definir manualmente a escala para o ASP ou configurar regras de dimensionamento automático para o ASP.  Para dimensionar manualmente seu aplicativo, basta definir ***escalar por*** para ***uma contagem de instâncias que eu inseri manualmente***.  A partir daqui, arraste o controle deslizante para a quantidade desejada ou insira-o na caixa ao lado do controle deslizante.  

![][2] 

As regras de dimensionamento automático para um ASP em um ASE funcionam da mesma forma que normalmente.  Você pode selecionar a ***porcentagem de CPU*** em ***Dimensionar por*** e criar regras de dimensionamento automático para seu ASP com base na porcentagem de CPU ou pode criar regras mais complexas usando ***regras de agendamento e desempenho***.  Para ver detalhes mais completos sobre como configurar o dimensionamento automático, use o guia [dimensionar um aplicativo no serviço Azure app][AppScale]. 

### <a name="worker-pool-selection"></a>Seleção do pool de trabalho
Conforme observado anteriormente, a seleção do pool de trabalho é acessada na interface do usuário do ASP.  Abra a folha do ASP que você deseja dimensionar e selecione o pool de trabalho.  Você verá todos os pools de trabalho que você configurou em seu Ambiente do Serviço de Aplicativo.  Se você tiver apenas um pool de trabalho, verá apenas um pool listado.  Para alterar em qual pool de trabalho seu ASP está, basta selecionar o pool de trabalho ao qual você deseja que seu plano do serviço de aplicativo seja movido.  

![][3]

Antes de mover o ASP de um pool de trabalho para outro, é importante certificar-se de que você terá capacidade adequada para o ASP.  Na lista de pools de trabalho, não apenas o nome do pool de trabalho está listado, mas você também pode ver quantos trabalhadores estão disponíveis nesse pool de trabalho.  Verifique se há instâncias suficientes disponíveis para conter o plano do serviço de aplicativo.  Se você precisar de mais recursos de computação no pool de trabalho para o qual deseja mover, obtenha o administrador do ASE para adicioná-los.  

> [!NOTE]
> Mover um ASP de um pool de trabalho causará inícios frios dos aplicativos nesse ASP.  Isso pode fazer com que as solicitações sejam executadas lentamente à medida que seu aplicativo é iniciado por frio nos novos recursos de computação.  O início frio pode ser evitado usando o [recurso de aquecimento do aplicativo][AppWarmup] no serviço Azure app.  O módulo de inicialização de aplicativo descrito no artigo também funciona para inicialização a frio porque o processo de inicialização também é invocado quando os aplicativos são iniciados por frio em novos recursos de computação. 
> 
> 

## <a name="getting-started"></a>Introdução
Para começar a usar os ambientes do serviço de aplicativo, consulte [como criar um ambiente do serviço de aplicativo][HowtoCreateASE]

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
