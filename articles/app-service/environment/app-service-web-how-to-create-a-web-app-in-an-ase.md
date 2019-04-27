---
title: Criar uma aplicação web no v1 ambiente de serviço de aplicações - Azure
description: Saiba como criar aplicações web e aplicações planos de serviço num ambiente de serviço de aplicações v1
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3e7db670a125f3c5f308107aabfbbab9301b7561
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765168"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Criar uma aplicação web num ambiente de serviço de aplicações v1

> [!NOTE]
> Este artigo é sobre o ambiente de serviço de aplicações v1.  Existe uma versão mais recente do ambiente de serviço de aplicação que é mais fácil de usar e é executado numa infraestrutura mais poderosa. Para saber mais sobre o novo início de versão com o [introdução ao ambiente de serviço de aplicações](intro.md).
> 

## <a name="overview"></a>Descrição geral
Este tutorial mostra como criar aplicações web e planos do App Service numa [ambiente do serviço de aplicações v1](app-service-app-service-environment-intro.md) (ASE). 

> [!NOTE]
> Se quer saber como criar uma aplicação web, mas não é necessário fazê-lo num ambiente de serviço de aplicações, consulte [criar uma aplicação web .NET](../app-service-web-get-started-dotnet.md) ou um dos tutoriais relacionados para outros idiomas e arquiteturas.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial parte do princípio de que criou um ambiente de serviço de aplicações. Se ainda não tiver feito, consulte [criar um ambiente de serviço de aplicações](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Criar uma aplicação Web
1. Na [Portal do Azure](https://portal.azure.com/), clique em **criar um recurso > Web + móvel > aplicação Web**. 
   
    ![][1]
2. Selecione a sua subscrição.  
   
    Se tiver várias subscrições, lembre-se de que para criar uma aplicação no seu ambiente de serviço de aplicações, terá de utilizar a mesma subscrição que utilizou ao criar o ambiente. 
3. Selecione ou crie um grupo de recursos.
   
    *Grupos de recursos* permitem-lhe gerir os recursos relacionados do Azure como uma unidade e são úteis durante o estabelecimento *controlo de acesso baseado em funções* regras (RBAC) para as suas aplicações. Para obter mais informações, consulte [descrição geral do Azure Resource Manager][ResourceGroups]. 
4. Selecione ou crie um plano do Serviço de Aplicações.
   
    *Planos do App Service* são geridos de conjuntos de aplicações web.  Normalmente quando seleciona a preços, o preço cobrado é aplicado, o plano do serviço de aplicações, em vez das aplicações individuais. Um ASE que paga para as instâncias de computação alocadas para o ASE em vez de ter listado com o ASP.  Para aumentar verticalmente o número de instâncias de uma aplicação web aumentar verticalmente as instâncias do seu serviço de aplicações plano e como ele afeta todas as aplicações web nesse plano.  Alguns recursos como ranhuras do site ou uma integração de VNET também tem restrições de quantidade dentro do plano.  Para obter mais informações, consulte [descrição geral dos planos do App Service do Azure](../overview-hosting-plans.md)
   
    É possível identificar os planos de serviço de aplicações no ASE ao observar a localização que é indicada com o nome do plano.  
   
    ![][5]
   
    Se pretender utilizar um plano do serviço de aplicações que já existe no seu ambiente de serviço de aplicações, selecione esse plano. Se pretender criar um novo plano do serviço de aplicações, consulte a secção seguinte deste tutorial [criar um plano do serviço de aplicações num ambiente de serviço de aplicações](#createplan).
5. Introduza o nome da sua aplicação web e, em seguida, clique em **criar**. 
   
    Se o seu ASE utiliza um VIP externo o URL de uma aplicação num ASE é: [*sitename*]. [ *nome do seu ambiente de serviço de aplicações*]. p.azurewebsites.net em vez de [*sitename*]. azurewebsites.net
   
    Se o seu ASE utiliza um VIP interno, em seguida, o URL de uma aplicação nesse ASE é: [*sitename*]. [ *subdomínio especificado durante a criação do ASE*]   
    Depois de selecionar o ASP durante a criação do ASE, verá o subdomínio de atualização abaixo **nome**

## <a name="createplan"></a> Crie um plano do serviço de aplicações
Quando cria um plano do serviço de aplicações num ambiente de serviço de aplicações, as opções de função de trabalho são diferentes porque não há nenhum trabalhadores partilhados num ASE.  Os operadores que precisa usar são aqueles que teriam sido alocados para o ASE pelo administrador.  Isso significa que para criar um novo plano, tem de ter mais funções de trabalho atribuídas ao seu conjunto de trabalho de ASE que o número total de instâncias em todos os seus planos já nesse conjunto de trabalho.  Se não tiver suficiente funções de trabalho no seu conjunto de trabalho de ASE para criar o seu plano, terá de trabalhar com o seu administrador de ASE para obtê-los adicionado.

Outra diferença com planos de serviço de aplicações alojadas por um ambiente de serviço de aplicações é a falta de seleção de preço.  Quando tiver um ambiente de serviço de aplicações de pagar para recursos de computação utilizados pelo sistema e não tem custos foram adicionados para os planos nesse ambiente.  Normalmente quando cria um plano do serviço de aplicações é selecionar um plano de preços que determina a faturação.  Um ambiente de serviço de aplicações é essencialmente um local privado em que pode criar conteúdo.  Paga para o ambiente e não para alojar o seu conteúdo.

As instruções seguintes mostram como criar um plano do serviço de aplicações, enquanto estiver a criar uma aplicação web, conforme explicado na secção anterior do tutorial.

1. Clique em **criar novo** na planear a seleção da interface do Usuário e fornecer um nome para o seu plano, tal como faria normalmente fora de um ASE.
2. Selecione o ASE que pretende utilizar no seu Seletor de localização de.
   
    Como um ambiente de serviço de aplicações é essencialmente um local de implantação particular, ele mostra na localização. 
   
    ![][2]
   
    Após a seleção de um ASE no selecionador de localização, a criação do plano de serviço de aplicações interface do Usuário atualiza.  A localização mostra agora o nome do sistema ASE e a região que esteja numa e o selecionador de plano de preços é substituído por um Seletor de conjunto de trabalho.  
   
    ![][3]

### <a name="selecting-a-worker-pool"></a>Selecionar um conjunto de trabalhos
Normalmente no serviço de aplicações do Azure e fora de um ambiente de serviço de aplicações, existem 3 tamanhos de computação que estão disponíveis com a seleção de um plano de preços dedicado.  De maneira semelhante, para um ASE pode definir o máximo de 3 conjuntos de trabalhadores e especificar o tamanho de computação que é utilizado para esse conjunto de trabalho.  O que significa para os inquilinos do ASE é que, em vez de selecionar um plano de preços com o tamanho de computação para o seu plano do serviço de aplicações, selecionar o que é chamado de um *conjunto de trabalho*.  

A seleção de conjunto de trabalho a interface do Usuário mostra o tamanho de computação utilizado para esse conjunto de trabalho abaixo do nome.  A quantidade disponível se refere a quantas instâncias de computação estão disponíveis para utilização nesse agrupamento.  O pool, na verdade, pode ter mais de instâncias que este número, mas este valor se refere ao simplesmente o número não está em utilização.  Se precisar de ajustar o ambiente de serviço de aplicações para adicionar mais recursos de computação, consulte [configurar o ambiente de serviço de aplicações](app-service-web-configure-an-app-service-environment.md).

![][4]

Neste exemplo, verá apenas dois conjuntos de trabalho disponíveis. Isso ocorre porque o administrador de ASE só alocados anfitriões para os conjuntos de trabalho de duas.  A terceira apareceria quando existem VMs alocadas para o mesmo.  

## <a name="after-web-app-creation"></a>Após a criação de aplicações web
Existem algumas considerações para executar aplicações web e gerir planos de serviço de aplicações num ASE que precisam ser levadas em conta.  

Conforme observado anteriormente, o proprietário do ASE é responsável pelo tamanho do sistema e assim também são responsáveis por garantir que existe capacidade suficiente para hospedar os planos de serviço de aplicações pretendidos. Se não houver nenhum trabalhadores disponíveis, não será capaz de criar o seu plano do serviço de aplicações.  Isso também é verdadeiro para aumentar verticalmente a sua aplicação web.  Se precisar de mais instâncias, em seguida, terá de obter o seu administrador de ambiente de serviço de aplicações para adicionar mais funções de trabalho.

Depois de criar a sua aplicação web e o plano do serviço de aplicações é uma boa idéia para aumentar vertical.  Num ASE sempre tem de ter, pelo menos, 2 instâncias do seu plano do serviço de aplicações para fornecer tolerância a falhas para as suas aplicações.  Dimensionar um plano do serviço de aplicações num ASE é o mesmo que normalmente através da interface do Usuário do plano de serviço de aplicações.  Para obter mais informações sobre o dimensionamento, [como dimensionar uma aplicação web num ambiente de serviço de aplicações](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[ResourceGroups]: ../../azure-resource-manager/resource-group-overview.md
[AzurePowershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
