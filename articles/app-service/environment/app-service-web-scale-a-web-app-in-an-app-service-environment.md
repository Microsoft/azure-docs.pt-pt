---
title: Escala rema uma aplicação em ASE v1
description: Dimensionar uma aplicação num Ambiente de Serviço de Aplicações. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: ccompy
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 43849ca7084f2237c37ad537c50f4e94ac4ea7c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74688668"
---
# <a name="scaling-apps-in-an-app-service-environment-v1"></a>Aplicativos de escala num App Service Environment v1
No Serviço de Aplicações Azure existem normalmente três coisas que pode escalar:

* plano de preços
* tamanho do trabalhador 
* número de casos.

Numa ASE não há necessidade de selecionar ou alterar o plano de preços.  Em termos de capacidades, já está a um nível de capacidade de preços Premium.  

No que diz respeito ao tamanho do trabalhador, o administrador da ASE pode atribuir o tamanho do recurso computacional a utilizar para cada grupo de trabalhadores.  Isso significa que você pode ter Worker Pool 1 com recursos de computação P4 e Pool De Trabalhador 2 com recursos de computação P1, se desejar.  Não têm de estar em ordem.  Para mais detalhes sobre os tamanhos e os seus preços consulte o documento aqui O Preço do Serviço de [Aplicações Azure][AppServicePricing].  Isto deixa as opções de escala para aplicações web e planos de serviço de aplicações num ambiente de serviço de aplicações para ser:

* seleção de piscina de trabalhador
* número de casos

A alteração de qualquer um dos itens é feita através da UI apropriada mostrada para os seus Planos de Serviço de Aplicações hospedados pela ASE.  

![][1]

Não pode aumentar o seu ASP para além do número de recursos computacionais disponíveis no conjunto de trabalhadores em que o seu ASP está.  Se precisa de recursos computacionais nesse conjunto de trabalhadores, precisa de fazer com que o seu administrador da ASE os adicione.  Para obter informações sobre a reconfiguração da sua ASE, leia as informações aqui: [Como configurar um ambiente][HowtoConfigureASE]de serviço de aplicações .  Também pode querer aproveitar as funcionalidades de escala automática DaSE para adicionar capacidade com base no horário ou nas métricas.  Para obter mais detalhes sobre a configuração da escala automática para o ambiente ASE em si, veja [como configurar a escala automática para um Ambiente][ASEAutoscale]de Serviço de Aplicações .

Você pode criar vários planos de serviço de aplicativos usando recursos computacionais de diferentes piscinas de trabalhadores, ou você pode usar o mesmo pool de trabalhadores.  Por exemplo, se tiver (10) recursos computacionais disponíveis no Pool 1 do Trabalhador, pode optar por criar um plano de serviço de aplicações utilizando (6) recursos de computação e um segundo plano de serviço de aplicações que utiliza (4) recursos de computação.

### <a name="scaling-the-number-of-instances"></a>Escalando o número de casos
Quando cria a sua aplicação web num App Service Environment começa com 1 instância.  Em seguida, pode dimensionar para instâncias adicionais para fornecer recursos de computação adicionais para a sua aplicação.   

Se a sua ASE tem capacidade suficiente, então isto é muito simples.  Vá ao seu Plano de Serviço de Aplicações que detém os sites que pretende escalar e selecionar Escala.  Isto abre o UI onde pode definir manualmente a balança para o seu ASP ou configurar regras de escala automática para o seu ASP.  Para escalar manualmente a sua aplicação basta definir ***a Escala para*** uma contagem de ***casos que eu introduzo manualmente***.  A partir daqui, ou arraste o slider para a quantidade desejada ou introduza-o na caixa ao lado do slider.  

![][2] 

As regras de escala automática para um ASP numa ASE funcionam da mesma forma que normalmente funcionam.  Pode selecionar ***a Percentagem*** de CPU em ***Escala*** e criar regras de escala automática para o seu ASP com base na Percentagem de CPU ou pode criar regras mais complexas utilizando regras de horário ***e desempenho***.  Para ver mais detalhes sobre a configuração da escala automática, utilize o guia aqui [Scale uma aplicação no Serviço de Aplicações Azure][AppScale]. 

### <a name="worker-pool-selection"></a>Seleção de Piscina de Trabalhador
Como notado anteriormente, a seleção de piscinas de trabalhadores é acedida a partir da ASP UI.  Abra a lâmina para o ASP que pretende escalar e selecionar a piscina de trabalhadores.  Você verá todas as piscinas de trabalhadores que você configurado no seu Ambiente de Serviço de Aplicações.  Se você tem apenas uma piscina de trabalhadores, então você só verá a piscina listada.  Para alterar o pool de trabalhadores em que o seu ASP está, basta selecionar a piscina de trabalhadores para a qual deseja que o seu Plano de Serviço de Aplicações se mude.  

![][3]

Antes de mover o seu ASP de uma piscina de trabalhadorpara outra, é importante certificar-se de que terá capacidade adequada para o seu ASP.  Na lista de grupos de trabalhadores, não só está listado o nome da piscina de trabalhadores, como também pode ver quantos trabalhadores estão disponíveis nessa piscina de trabalhadores.  Certifique-se de que existem instâncias suficientes disponíveis para conter o seu Plano de Serviço de Aplicações.  Se precisa de mais recursos de computação no grupo de trabalhadores para onde pretende mudar-se, então faça com que o seu administrador da ASE os adicione.  

> [!NOTE]
> A mudança de um ASP de uma piscina de trabalhadores causará um início frio das aplicações nesse ASP.  Isto pode fazer com que os pedidos corram lentamente, uma vez que a sua aplicação está a frio iniciada nos novos recursos computacionais.  O arranque a frio pode ser evitado utilizando a capacidade de aquecimento da aplicação no Serviço de [Aplicações][AppWarmup] Azure.  O módulo de inicialização de aplicações descrito no artigo também funciona para o arranque a frio porque o processo de inicialização também é invocado quando as aplicações estão frias em novos recursos computacionais. 
> 
> 

## <a name="getting-started"></a>Introdução
Para começar com ambientes de serviço de aplicação, veja [como criar um ambiente de serviço de aplicações][HowtoCreateASE]

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
