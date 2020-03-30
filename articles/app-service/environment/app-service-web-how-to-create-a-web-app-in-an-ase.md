---
title: Criar uma aplicação web na ASE v1
description: Aprenda a criar aplicações web num App Service Environment v1. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: ccompy
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 5c947617f0c27708e72f9bff92e2b0041473cd92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266198"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Criar uma aplicação web num App Service Environment v1

> [!NOTE]
> Este artigo é sobre o App Service Environment v1.  Existe uma versão mais recente do App Service Environment que é mais fácil de usar e funciona em infraestruturas mais poderosas. Para saber mais sobre a nova versão comece com a Introdução ao Ambiente de Serviço de [Aplicações.](intro.md)
> 

## <a name="overview"></a>Descrição geral
Este tutorial mostra como criar aplicações web e planos de Serviço de Aplicações num [App Service Environment v1](app-service-app-service-environment-intro.md) (ASE). 

> [!NOTE]
> Se quiser aprender a criar uma aplicação web mas não precisa fazê-lo num Ambiente de Serviço de Aplicações, consulte [Criar uma aplicação web .NET](../app-service-web-get-started-dotnet.md) ou um dos tutoriais relacionados para outros idiomas e enquadramentos.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial assume que criou um Ambiente de Serviço de Aplicações. Se ainda não o fez, consulte [Create a App Service Environment](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Criar uma aplicação Web
1. No [Portal Azure,](https://portal.azure.com/)clique em **Criar um recurso > Web + Mobile > Web App**. 
   
    ![][1]
2. Selecione a sua subscrição.  
   
    Se tiver várias subscrições, esteja ciente de que para criar uma aplicação no seu App Service Environment, precisa de usar a mesma subscrição que utilizou para criar o ambiente. 
3. Selecione ou crie um grupo de recursos.
   
    *Os grupos de recursos* permitem-lhe gerir os recursos do Azure relacionados como uma unidade e são úteis ao estabelecer regras de controlo de acesso (RBAC) *baseadas em papéis* para as suas apps. Para obter mais informações, veja [Descrição geral do Azure Resource Manager][ResourceGroups]. 
4. Selecione ou crie um plano do Serviço de Aplicações.
   
    *Os planos* do Serviço de Aplicações são conjuntos geridos de aplicações web.  Normalmente, quando se leciona preços, o preço cobrado é aplicado ao plano do Serviço de Aplicações e não às aplicações individuais. Numa ASE paga-se pelos casos de cálculo atribuídos à ASE em vez do que enumerou com o seu ASP.  Para aumentar o número de casos de uma aplicação web, você escala as instâncias do seu plano de Serviço de Aplicações e afeta todas as aplicações web nesse plano.  Algumas funcionalidades, como slots de site ou Integração VNET, também têm restrições de quantidade dentro do plano.  Para mais informações, consulte a [visão geral dos planos](../overview-hosting-plans.md) do Azure App Service
   
    Pode identificar os planos do Serviço de Aplicações na sua ASE, olhando para a localização que está anotada no nome do plano.  
   
    ![][5]
   
    Se quiser utilizar um plano de Serviço de Aplicações que já existe no seu Ambiente de Serviço de Aplicações, selecione esse plano. Se quiser criar um novo plano de Serviço de Aplicações, consulte a seguinte secção deste tutorial, Crie um plano de Serviço de Aplicações num Ambiente de Serviço de [Aplicações.](#createplan)
5. Introduza o nome para a sua aplicação web e, em seguida, clique em **Criar**. 
   
    Se a sua ASE utilizar um VIP externo, o URL de uma aplicação numa ASE é: [*nome de site].* [nome*do seu App Service Environment*]p.azurewebsites.net em vez de *[sitename*].azurewebsites.net
   
    Se a sua ASE utilizar um VIP interno, então o URL de uma aplicação na aa é: [*nome do site].* [subdomínio especificado durante a criação da*ASE]*   
    Depois de selecionar o seu ASP durante a criação da ASE, verá a atualização do subdomínio abaixo **do Nome**

## <a name="create-an-app-service-plan"></a><a name="createplan"></a>Criar um plano de serviço de aplicações
Quando cria um plano de Serviço de Aplicações num Ambiente de Serviço de Aplicações, as escolhas dos seus trabalhadores são diferentes, uma vez que não existem trabalhadores partilhados numa ASE.  Os trabalhadores que tem de utilizar são os que foram atribuídos à ASE pelo administrador.  Isto significa que, para criar um novo plano, precisa de ter mais trabalhadores atribuídos à sua piscina de trabalhadores da ASE do que o número total de instâncias em todos os seus planos já naquela piscina de trabalhadores.  Se não tem trabalhadores suficientes na sua piscina de trabalhadores da ASE para criar o seu plano, precisa de trabalhar com o seu administrador da ASE para os adicionar.

Outra diferença com os planos do App Service hospedados por um App Service Environment é a falta de seleção de preços.  Quando se tem um App Service Environment está a pagar os recursos computacionais utilizados pelo sistema e não tem taxas adicionais para os planos nesse ambiente.  Normalmente, quando cria um plano de Serviço de Aplicações, seleciona um plano de preços que determina a sua faturação.  Um Ambiente de Serviço de Aplicações é essencialmente um local privado onde pode criar conteúdo.  Paga-se pelo ambiente e não para hospedar o seu conteúdo.

As seguintes instruções mostram como criar um plano de Serviço de Aplicações enquanto está a criar uma aplicação web, como explicado na secção anterior do tutorial.

1. Clique em **Criar Novo** na UI de seleção de planos e forneça um nome para o seu plano tal como normalmente faria fora de uma ASE.
2. Selecione a ASE que pretende utilizar no seu picker de localização.
   
    Como um Ambiente de Serviço de Aplicações é essencialmente um local de implementação privado, mostra em Localização. 
   
    ![][2]
   
    Após a seleção de uma ASE no picker de localização, o plano de criação de ui atualizações do plano app service.  A localização mostra agora o nome do sistema ASE e da região em que se encontra, e o apanhador de planos de preços é substituído por um apanhador de piscinas de trabalhadores.  
   
    ![][3]

### <a name="selecting-a-worker-pool"></a>Selecionando uma piscina de trabalhadores
Normalmente no Azure App Service e fora de um App Service Environment, existem 3 tamanhos de computação que estão disponíveis com a seleção de um plano de preços dedicado.  De forma semelhante, para uma ASE pode definir até 3 piscinas de trabalhadores e especificar o tamanho da computação que é usado para essa piscina de trabalhadores.  O que isso significa para os inquilinos da ASE é que, em vez de selecionar um plano de preços com o tamanho da computação para o seu plano de Serviço de Aplicações, você seleciona o que é chamado de *piscina de trabalhadores*.  

O UI de seleção de piscinas de trabalhador mostra o tamanho da computação usada para a piscina de trabalhadores abaixo do nome.  A quantidade disponível refere-se a quantas instâncias de cálculo estão disponíveis para utilização nessa piscina.  O total pode realmente ter mais instâncias do que este número, mas este valor refere-se simplesmente a quantas não estão a ser utilizadas.  Se precisar de ajustar o seu Ambiente de Serviço de Aplicações para adicionar mais recursos computacionais, consulte [configurar o seu Ambiente](app-service-web-configure-an-app-service-environment.md)de Serviço de Aplicações .

![][4]

Neste exemplo, só se vêem duas piscinas de trabalhadores disponíveis. Isto porque o administrador da ASE apenas atribuiu os anfitriões àqueles dois grupos de trabalhadores.  O terceiro apareceria quando houvesse VMs atribuídos nele.  

## <a name="after-web-app-creation"></a>Após a criação de aplicativos web
Existem algumas considerações para executar aplicações web e gerir planos de Serviço de Aplicações numa ASE que precisam de ser tidas em conta.  

Como já foi notado, o proprietário da ASE é responsável pela dimensão do sistema e, consequentemente, são também responsáveis por garantir que existe capacidade suficiente para acolher os planos de serviço de aplicação pretendidos. Se não houver trabalhadores disponíveis, não poderá criar o seu plano de Serviço de Aplicações.  Isto também é verdade para aumentar a sua aplicação web.  Se precisar de mais instâncias, terá de obter o seu administrador do App Service Environment para adicionar mais trabalhadores.

Depois de criar a sua aplicação web e plano de App Service, é uma boa ideia dimensioná-la.  Numa ASE é necessário ter sempre pelo menos 2 instâncias do seu plano de Serviço de Aplicações para fornecer tolerância a falhas para as suas apps.  Escalar um plano de Serviço de Aplicações numa ASE é o mesmo que o normal através do plano de serviço de aplicações UI.  Para mais informações sobre escala, [Como escalar uma aplicação web num Ambiente de Serviço](app-service-web-scale-a-web-app-in-an-app-service-environment.md) de Aplicações

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
[ResourceGroups]: ../../azure-resource-manager/management/overview.md
[AzurePowershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
