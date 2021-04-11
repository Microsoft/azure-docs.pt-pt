---
title: Criar uma aplicação web em ASE v1
description: Saiba como criar aplicações web num App Service Environment v1. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: ccompy
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: fa92002cfef070218902aa7919900dde9fe5a9b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505414"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Criar uma aplicação web num App Service Environment v1

> [!NOTE]
> Este artigo é sobre o App Service Environment v1.  Existe uma versão mais recente do App Service Environment que é mais fácil de usar e funciona em infraestruturas mais poderosas. Para saber mais sobre a nova versão comece com a [Introdução ao Ambiente de Serviço de Aplicações.](intro.md)
> 

## <a name="overview"></a>Descrição Geral
Este tutorial mostra como criar aplicações web e planos de Serviço de Aplicações num [App Service Environment v1](app-service-app-service-environment-intro.md) (ASE). 

> [!NOTE]
> Se quiser aprender a criar uma aplicação web mas não precisar de o fazer num Ambiente de Serviço de Aplicações, consulte [criar uma aplicação web .NET](../quickstart-dotnetcore.md) ou um dos tutoriais relacionados para outros idiomas e quadros.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial pressupõe que criou um Ambiente de Serviço de Aplicações. Se ainda não o fez, consulte [Criar um Ambiente de Serviço de Aplicações.](app-service-web-how-to-create-an-app-service-environment.md) 

## <a name="create-a-web-app"></a>Criar uma aplicação Web
1. No [Portal Azure,](https://portal.azure.com/)clique em **Criar um recurso > Web + Mobile > Web App.** 
   
    ![Screenshot que mostra onde criar uma aplicação web no portal Azure.][1]
2. Selecione a sua subscrição.  
   
    Se tiver várias subscrições, saiba que para criar uma aplicação no seu Ambiente de Serviço de Aplicações, precisa de utilizar a mesma subscrição que utilizou ao criar o ambiente. 
3. Selecione ou crie um grupo de recursos.
   
    *Os grupos de recursos* permitem-lhe gerir os recursos Azure relacionados como uma unidade e são úteis ao estabelecer regras *de controlo de acesso baseado em funções Azure (Azure RBAC)* para as suas apps. Para obter mais informações, veja [Descrição geral do Azure Resource Manager][ResourceGroups]. 
4. Selecione ou crie um plano do Serviço de Aplicações.
   
    *Os planos do Serviço de Aplicações* são geridos conjuntos de aplicações web.  Normalmente, quando seleciona preços, o preço cobrado é aplicado ao plano de Serviço de Aplicações e não às aplicações individuais. Numa ASE paga-se pelas instâncias de computação atribuídas ao ASE em vez do que enumerou com o seu ASP.  Para aumentar o número de casos de uma aplicação web, você escala os casos do seu plano de Serviço de Aplicações e afeta todas as aplicações web nesse plano.  Algumas funcionalidades, como slots de site ou integração VNET também têm restrições de quantidade dentro do plano.  Para mais informações, consulte [a visão geral dos planos do Azure App Service](../overview-hosting-plans.md)
   
    Pode identificar os planos do Serviço de Aplicações no seu ASE, olhando para o local que é anotado no nome do plano.  
   
    ![Screenshot que mostra era para ver os planos do Serviço de Aplicações no seu ASE.][5]
   
    Se pretender utilizar um plano de Serviço de Aplicações que já exista no seu Ambiente de Serviço de Aplicações, selecione esse plano. Se pretender criar um novo plano de Serviço de Aplicações, consulte a seguinte secção deste tutorial, Crie um plano de Serviço de [Aplicações num Ambiente de Serviço de Aplicações.](#createplan)
5. Introduza o nome para a sua aplicação web e, em seguida, clique em **Criar**. 
   
    Se o seu ASE utilizar um VIP Externo, o URL de uma aplicação num ASE é: [*nome do site*]. [nome *do seu Ambiente de Serviço de Aplicações*].p.azurewebsites.net em vez de *[nome* do site ].azurewebsites.net
   
    Se o seu ASE utilizar um VIP Interno, então o URL de uma aplicação em que o ASE é: [*nome do site*]. *[subdomínio especificado durante a criação da ASE*]   
    Depois de selecionar o seu ASP durante a criação da ASE, verá a atualização do subdomínio abaixo **Nome**

## <a name="create-an-app-service-plan"></a><a name="createplan"></a> Criar um plano de Serviço de Aplicações
Quando cria um plano de Serviço de Aplicações num Ambiente de Serviço de Aplicações, as suas escolhas de trabalhadores são diferentes, uma vez que não existem trabalhadores partilhados num ASE.  Os trabalhadores que tem de usar são os que foram atribuídos à ASE pela administração.  Isto significa que para criar um novo plano, é necessário ter mais trabalhadores alocados à sua piscina de trabalhadores ase do que o número total de casos em todos os seus planos já naquela piscina de trabalhadores.  Se não tem trabalhadores suficientes na sua piscina de trabalhadores da ASE para criar o seu plano, precisa de trabalhar com o seu administrador ASE para os adicionar.

Outra diferença com os planos do Serviço de Aplicações hospedados por um Ambiente de Serviço de Aplicações é a falta de seleção de preços.  Quando tem um App Service Environment está a pagar pelos recursos compute utilizados pelo sistema e não tem taxas adicionais para os planos nesse ambiente.  Normalmente, quando cria um plano de Serviço de Aplicações, seleciona um plano de preços que determina a sua faturação.  Um Ambiente de Serviço de Aplicações é essencialmente um local privado onde pode criar conteúdo.  Paga-se pelo ambiente e não acolhe o seu conteúdo.

As seguintes instruções mostram como criar um plano de Serviço de Aplicações enquanto está a criar uma aplicação web, como explicado na secção anterior do tutorial.

1. Clique em **Criar Novo** na UI de seleção de planos e forneça um nome para o seu plano tal como normalmente faria fora de um ASE.
2. Selecione o ASE que pretende utilizar no seu seletor de localização.
   
    Como um Ambiente de Serviço de Aplicações é essencialmente um local de implantação privado, mostra em localização. 
   
    ![Screenshot que mostra o selecionador de localização para selecionar o ASE que deseja.][2]
   
    Após a seleção de um ASE no selecionador de localização, o plano de aplicação do serviço de aplicação atualiza as atualizações do UI.  A localização mostra agora o nome do sistema ASE e da região em que se encontra, e o selecionador de planos de preços é substituído por um apanhador de piscina de trabalhadores.  
   
    ![Screenshot que mostra os detalhes do sistema ASE depois de selecionar o ASE no seletor de localização.][3]

### <a name="selecting-a-worker-pool"></a>Selecionando uma piscina de trabalhadores
Normalmente no Azure App Service e fora de um App Service Environment, existem 3 tamanhos de cálculo que estão disponíveis com a seleção de um plano de preço dedicado.  De forma semelhante, para um ASE pode definir até 3 piscinas de trabalhadores e especificar o tamanho do cálculo que é usado para essa piscina de trabalhadores.  O que isso significa para os inquilinos da ASE é que, em vez de selecionar um plano de preços com tamanho de cálculo para o seu plano de Serviço de Aplicações, selecione o que é chamado de *piscina de trabalhadores.*  

A UI de seleção de piscinas de trabalhadores mostra o tamanho do cálculo usado para a piscina de trabalhadores abaixo do nome.  A quantidade disponível refere-se ao número de casos de computação disponíveis para utilização nessa piscina.  O pool total pode realmente ter mais casos do que este número, mas este valor refere-se simplesmente a quantos não estão em uso.  Se precisar de ajustar o seu Ambiente de Serviço de Aplicações para adicionar mais recursos computacional, consulte [configurar o seu Ambiente de Serviço de Aplicações.](app-service-web-configure-an-app-service-environment.md)

![Screenshot que mostra o painel da Piscina dos Trabalhadores onde você pode selecionar piscinas de trabalhadores para o ASE.][4]

Neste exemplo, vê-se apenas duas piscinas de trabalhadores disponíveis. Isto porque o administrador da ASE apenas atribuiu anfitriões a esses dois grupos de trabalhadores.  O terceiro apareceria quando houvesse VMs nele atribuídos.  

## <a name="after-web-app-creation"></a>Após a criação de aplicativos web
Existem algumas considerações para executar aplicações web e gerir planos de Serviço de Aplicações numa ASE que precisam de ser tidas em conta.  

Como já foi notado, o proprietário da ASE é responsável pela dimensão do sistema e, consequentemente, são também responsáveis por garantir que existe capacidade suficiente para acolher os planos de Serviço de Aplicações pretendidos. Se não houver trabalhadores disponíveis, não poderá criar o seu plano de Serviço de Aplicações.  Isto também é verdade para aumentar a sua aplicação web.  Se precisar de mais casos, terá de obter o seu administrador de App Service Environment para adicionar mais trabalhadores.

Depois de criar a sua aplicação web e o plano de Serviço de Aplicações, é uma boa ideia escaloná-la.  Numa ASE é sempre necessário ter pelo menos 2 instâncias do seu plano de Serviço de Aplicações para fornecer tolerância a falhas para as suas apps.  Escalar um plano de Serviço de Aplicações num ASE é o mesmo que o normal através do plano de Serviço de Aplicações UI.  Para mais informações sobre o dimensionamento, [Como escalar uma aplicação web num Ambiente de Serviço de Aplicações](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

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
[AzurePowershell]: /powershell/azure/