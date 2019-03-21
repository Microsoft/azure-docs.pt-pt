---
title: Efetuar a ativação pós-falha e dimensionar uma aplicação de contentores no Service Fabric no Azure | Microsoft Docs
description: Neste tutorial, saiba como a ativação pós-falha é processada numa aplicação de contentores do Azure Service Fabric.  Além disso, saiba como dimensionar os contentores e serviços em execução num cluster.
services: service-fabric
documentationcenter: ''
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: Docker, Contentores, Microsserviços, Service Fabric, Azure
ms.assetid: ''
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: f5ac1c53a97fc2a3749582c5f48b967e3ce0c2c4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58104272"
---
# <a name="tutorial-demonstrate-fail-over-and-scaling-of-container-services-with-service-fabric"></a>Tutorial: Demonstrar falha com o passar e dimensionamento dos serviços de contentores com o Service Fabric

Este tutorial é a terceira parte de uma série. Neste tutorial, saiba como a ativação pós-falha é processada em aplicações de contentores do Service Fabric. Além disso, saiba como dimensionar os contentores. Neste tutorial:

> [!div class="checklist"]
> * Obter mais informações sobre a ativação pós-falha de contentor num cluster do Service Fabric
> * Dimensionar os contentores de front-end da Web numa aplicação

## <a name="prerequisites"></a>Pré-requisitos

A aplicação da [Parte 2](service-fabric-tutorial-package-containers.md) está em execução num cluster ativo do Service Fabric.

## <a name="fail-over-a-container-in-a-cluster"></a>Efetuar a ativação pós-falha de um contentor num cluster

O Service Fabric garante que as instâncias de contentor são movidas automaticamente para outros nós no cluster, caso ocorra uma falha. Também pode drenar manualmente um nó de contentores e, em seguida, mover harmoniosamente para outros nós do cluster. Se tiver várias formas de dimensionar os seus serviços, neste exemplo, estamos a utilizar o Service Fabric Explorer.

Para efetuar a ativação pós-falha do contentor de front-end, execute os seguintes passos:

1. Abra o Service Fabric Explorer no seu cluster - por exemplo, `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`.
2. Clique no nó **fabric:/TestContainer/azurevotefront** na vista de árvore e expanda o nó de partição (representado por uma GUID). Tenha em atenção o nome do nó na vista de árvore, que mostra os nós nos quais o contentor está atualmente a ser executado - por exemplo `_nodetype_1`
3. Expanda o nó **Nós** na vista de árvore. Clique nas reticências (três pontos) junto ao nó, que está a executar o contentor.
4. Escolha **Reiniciar** para reiniciar esse nó e confirme a ação de reinício. O reinício provoca a ativação pós-falha do contentor para outro nó no cluster.

![noderestart][noderestart]

Repare como o nome do nó (o que indica onde os contentores de front-end são executados) é alterado para outro nó no cluster. Após alguns instantes, deverá conseguir navegar até à aplicação novamente e ver que a aplicação está agora em execução num nó diferente.

## <a name="scale-containers-and-services-in-a-cluster"></a>Dimensionar contentores e serviços num cluster

Os contentores do Service Fabric podem ser dimensionados num cluster para se prepararem para a carga nos serviços. Para dimensionar um contentor, tem de alterar o número de instâncias em execução no cluster.

Para dimensionar o front-end da Web, execute os seguintes passos:

1. Abra o Service Fabric Explorer no seu cluster - por exemplo, `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`.
2. Clique nas reticências (três pontos) junto ao nó **fabric:/TestContainer/azurevotefront** na vista de árvore e escolha **Dimensionar Serviço**.

![sfxscale][sfxscale]

Agora, pode optar por dimensionar o número de instâncias do front-end da Web.

1. Altere o número para **2** e clique em **Dimensionar Serviço**.
1. Clique no nó **fabric:/TestContainer/azurevotefront** na vista de árvore e expanda o nó de partição (representado por uma GUID).

![sfxscaledone][sfxscaledone]

Agora, pode ver que o serviço tem duas instâncias. Na vista de árvore, pode ver em que nós as instâncias são executadas.

Através desta simples tarefa de gestão, duplicámos os recursos disponíveis para o nosso serviço de front-end processar a carga de utilizador. É importante compreender que não precisa de várias instâncias de um serviço para o executar de forma fiável. Se um serviço falhar, o Service Fabric certifica-se de que uma nova instância de serviço é executado no cluster.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, a ativação pós-falha de contentor foi demonstrada, bem como o dimensionamento de uma aplicação. Foram efetuados os seguintes passos:

> [!div class="checklist"]
> * Obter mais informações sobre a ativação pós-falha de contentor num cluster do Service Fabric
> * Dimensionar os contentores de front-end da Web numa aplicação

Nesta série de tutoriais, ficou a saber como:
> [!div class="checklist"]
> * Criar imagens de contentor
> * Colocar imagens de contentor no Azure Container Registry
> * Compactar Contentores para o Service Fabric através do Yeoman
> * Criar e Executar uma Aplicação do Service Fabric com Contentores
> * Como a ativação pós-falha e o dimensionamento são processados no Service Fabric

[noderestart]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialnoderestart.png
[sfxscale]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscale.png
[sfxscaledone]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscaledone.png