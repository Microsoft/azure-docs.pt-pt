---
title: Criar um aplicativo Web em um Ambiente do Serviço de Aplicativo v1-Azure
description: Saiba como criar aplicativos Web e planos do serviço de aplicativo em um Ambiente do Serviço de Aplicativo v1
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: cc40c2296e583ab93a7c34d709cfbf1334ae3926
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069851"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Criar um aplicativo Web em um Ambiente do Serviço de Aplicativo v1

> [!NOTE]
> Este artigo é sobre o Ambiente do Serviço de Aplicativo v1.  Há uma versão mais recente do Ambiente do Serviço de Aplicativo que é mais fácil de usar e é executada em uma infraestrutura mais potente. Para saber mais sobre a nova versão, comece com a [introdução ao ambiente do serviço de aplicativo](intro.md).
> 

## <a name="overview"></a>Descrição geral
Este tutorial mostra como criar aplicativos Web e planos do serviço de aplicativo em um [ambiente do serviço de aplicativo v1](app-service-app-service-environment-intro.md) (ase). 

> [!NOTE]
> Se você quiser saber como criar um aplicativo Web, mas não precisar fazer isso em um Ambiente do Serviço de Aplicativo, consulte [criar um aplicativo Web .net](../app-service-web-get-started-dotnet.md) ou um dos tutoriais relacionados para outras linguagens e estruturas.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial pressupõe que você criou um Ambiente do Serviço de Aplicativo. Se ainda não tiver feito isso, consulte [criar um ambiente do serviço de aplicativo](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Criar uma aplicação Web
1. No [portal do Azure](https://portal.azure.com/), clique em **criar um recurso > Web + celular > aplicativo Web**. 
   
    ![][1]
2. Selecione a sua subscrição.  
   
    Se você tiver várias assinaturas, lembre-se de que, para criar um aplicativo em seu Ambiente do Serviço de Aplicativo, precisará usar a mesma assinatura que usou ao criar o ambiente. 
3. Selecione ou crie um grupo de recursos.
   
    Os *grupos de recursos* permitem gerenciar recursos do Azure relacionados como uma unidade e são úteis ao estabelecer regras de RBAC (controle de *acesso baseado em função* ) para seus aplicativos. Para obter mais informações, veja [Descrição geral do Azure Resource Manager][ResourceGroups]. 
4. Selecione ou crie um plano do Serviço de Aplicações.
   
    Os *planos do serviço de aplicativo* são conjuntos gerenciados de aplicativos Web.  Normalmente, quando você seleciona preços, o preço cobrado é aplicado ao plano do serviço de aplicativo, e não aos aplicativos individuais. Em um ASE, você paga pelas instâncias de computação alocadas para o ASE, em vez do que você listou com o ASP.  Para escalar verticalmente o número de instâncias de um aplicativo Web, você escala verticalmente as instâncias do seu plano do serviço de aplicativo e afeta todos os aplicativos Web nesse plano.  Alguns recursos, como slots de site ou Integração VNET também têm restrições de quantidade no plano.  Para obter mais informações, consulte [visão geral dos planos de serviço Azure app](../overview-hosting-plans.md)
   
    Você pode identificar os planos do serviço de aplicativo em seu ASE examinando o local que está anotado sob o nome do plano.  
   
    ![][5]
   
    Se você quiser usar um plano do serviço de aplicativo que já existe em seu Ambiente do Serviço de Aplicativo, selecione esse plano. Se você quiser criar um novo plano do serviço de aplicativo, consulte a seguinte seção deste tutorial, [criar um plano do serviço de aplicativo em um ambiente do serviço de aplicativo](#createplan).
5. Insira o nome para seu aplicativo Web e, em seguida, clique em **criar**. 
   
    Se seu ASE usar um VIP externo, a URL de um aplicativo em um ASE será: [*SiteName*]. [*nome do seu ambiente do serviço de aplicativo*]. p.azurewebsites.net em vez de [*sitename*]. azurewebsites.net
   
    Se o seu ASE usar um VIP interno, a URL de um aplicativo nesse ASE será: [*SiteName*]. [*subdomínio especificado durante a criação do ase*]   
    Depois de selecionar o ASP durante a criação do ASE, você verá a atualização do subdomínio abaixo do **nome**

## <a name="createplan"></a>Criar um plano do serviço de aplicativo
Quando você cria um plano do serviço de aplicativo em um Ambiente do Serviço de Aplicativo, suas opções de trabalho são diferentes, pois não há nenhum trabalho compartilhado em um ASE.  Os trabalhadores que você precisa usar são aqueles que foram alocados para o ASE pelo administrador.  Isso significa que, para criar um novo plano, você precisa ter mais trabalhadores alocados para o pool de trabalho do ASE do que o número total de instâncias em todos os seus planos já existentes nesse pool de trabalho.  Se você não tiver trabalhadores suficientes em seu pool de trabalho do ASE para criar seu plano, você precisará trabalhar com seu administrador do ASE para que eles sejam adicionados.

Outra diferença com os planos do serviço de aplicativo hospedados por um Ambiente do Serviço de Aplicativo é a falta de seleção de preços.  Quando você tem um Ambiente do Serviço de Aplicativo você está pagando pelos recursos de computação usados pelo sistema e não tem encargos adicionais para os planos nesse ambiente.  Normalmente, quando você cria um plano do serviço de aplicativo, você seleciona um plano de preços que determina sua cobrança.  Um Ambiente do Serviço de Aplicativo é essencialmente um local particular onde você pode criar conteúdo.  Você paga pelo ambiente e não para hospedar seu conteúdo.

As instruções a seguir mostram como criar um plano do serviço de aplicativo enquanto você está criando um aplicativo Web, conforme explicado na seção anterior do tutorial.

1. Clique em **criar novo** na interface do usuário de seleção de plano e forneça um nome para seu plano assim como faria normalmente fora de um ASE.
2. Selecione o ASE que você deseja usar no seletor de local.
   
    Como um Ambiente do Serviço de Aplicativo é essencialmente um local de implantação particular, ele aparece em local. 
   
    ![][2]
   
    Após a seleção de um ASE no seletor de local, a criação do plano do serviço de aplicativo atualiza as atualizações da interface do usuário.  O local agora mostra o nome do sistema ASE e a região em que ele se encontra, e o seletor de plano de preços é substituído por um seletor de pool de trabalho.  
   
    ![][3]

### <a name="selecting-a-worker-pool"></a>Selecionando um pool de trabalho
Normalmente, no serviço Azure App e fora de um Ambiente do Serviço de Aplicativo, há três tamanhos de computação que estão disponíveis com a seleção de um plano de preço dedicado.  De maneira semelhante, para um ASE, você pode definir até três pools de trabalho e especificar o tamanho de computação que é usado para esse pool de trabalho.  O que isso significa para locatários do ASE é que, em vez de selecionar um plano de preços com tamanho de computação para seu plano do serviço de aplicativo, você seleciona o que é chamado de *pool de trabalho*.  

A interface do usuário de seleção do pool de trabalho mostra o tamanho de computação usado para esse pool de trabalho abaixo do nome.  A quantidade disponível refere-se a quantas instâncias de computação estão disponíveis para uso nesse pool.  O pool total pode ter, na verdade, mais instâncias do que esse número, mas esse valor se refere simplesmente a quantas não estão em uso.  Se você precisar ajustar seu Ambiente do Serviço de Aplicativo para adicionar mais recursos de computação, consulte Configurando [seu ambiente do serviço de aplicativo](app-service-web-configure-an-app-service-environment.md).

![][4]

Neste exemplo, você vê apenas dois pools de trabalho disponíveis. Isso ocorre porque o administrador do ASE alocou apenas hosts nesses dois pools de trabalho.  O terceiro aparecerá quando houver VMs alocadas a ela.  

## <a name="after-web-app-creation"></a>Após a criação do aplicativo Web
Há algumas considerações para a execução de aplicativos Web e o gerenciamento de planos do serviço de aplicativo em um ASE que precisam ser levados em conta.  

Conforme observado anteriormente, o proprietário do ASE é responsável pelo tamanho do sistema e, como resultado, eles também são responsáveis por garantir que haja capacidade suficiente para hospedar os planos do serviço de aplicativo desejado. Se não houver nenhum trabalho disponível, você não poderá criar seu plano do serviço de aplicativo.  Isso também é verdadeiro para escalar verticalmente seu aplicativo Web.  Se você precisar de mais instâncias, precisará ter seu administrador de Ambiente do Serviço de Aplicativo para adicionar mais trabalhadores.

Depois de criar seu aplicativo Web e o plano do serviço de aplicativo, é uma boa ideia escalar verticalmente.  Em um ASE, você sempre precisa ter pelo menos duas instâncias do seu plano do serviço de aplicativo para fornecer tolerância a falhas para seus aplicativos.  Dimensionar um plano do serviço de aplicativo em um ASE é o mesmo que o normal por meio da interface do usuário do plano do serviço de aplicativo.  Para obter mais informações sobre o dimensionamento, [como dimensionar um aplicativo Web em um ambiente do serviço de aplicativo](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

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
