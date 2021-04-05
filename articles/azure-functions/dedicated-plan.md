---
title: Azure Funções Hospedagem Dedicada
description: Conheça os benefícios de executar funções Azure num plano de hospedagem dedicado do Serviço de Aplicações.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: a9f976eda4a551c302ba7df92fbdbbf7a4fce1d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101704570"
---
# <a name="dedicated-hosting-plans-for-azure-functions"></a>Planos de hospedagem dedicados para funções Azure

Este artigo é sobre hospedar a sua aplicação de função num plano de Serviço de Aplicações, incluindo num Ambiente de Serviço de Aplicações (ASE). Para outras opções de hospedagem, consulte o artigo do [plano de hospedagem.](functions-scale.md)

Um plano de Serviço de Aplicações define um conjunto de recursos computacionares para uma aplicação a executar. Estes recursos computacional são análogos à fazenda de [_servidores_](https://wikipedia.org/wiki/Server_farm) no alojamento convencional. Uma ou mais aplicações de função podem ser configuradas para executar nos mesmos recursos de computação (plano de Serviço de Aplicações) que outras aplicações do Serviço de Aplicações, como aplicações web. Estes planos incluem SKUs básicos, standard, premium e isolados. Para mais detalhes sobre o funcionamento do plano do Serviço de Aplicações, consulte os planos do [Azure App Service em visão geral aprofundada.](../app-service/overview-hosting-plans.md)

Considere um plano de Serviço de Aplicações nas seguintes situações:

* Tem VMs existentes e subutilizados que já estão a executar outras instâncias do Serviço de Aplicações.
* Pretende fornecer uma imagem personalizada para executar as suas funções.

## <a name="billing"></a>Faturação

Você paga por aplicativos de função em um Plano de Serviço de Aplicações como faria para outros recursos do Serviço de Aplicações. Isto difere do plano de [consumo](consumption-plan.md) de funções Azure ou do alojamento do [plano Premium,](functions-premium-plan.md) que têm componentes de custos baseados no consumo. Você é cobrado apenas para o plano, independentemente de quantas aplicações de funções ou aplicações web executam no plano. Para saber mais, consulte a [página de preços do Serviço de Aplicações.](https://azure.microsoft.com/pricing/details/app-service/windows/) 

## <a name="always-on"></a><a name="always-on"></a> Sempre ligado

Se executar um plano de Serviço de Aplicações, deve ativar o **Always on** Setting para que a sua aplicação de função funcione corretamente. Num plano de Serviço de Aplicações, o tempo de funcionamento das funções fica inativo após alguns minutos de inatividade, pelo que apenas os gatilhos HTTP "acordam" as suas funções. A definição **Always on** está disponível apenas num plano de Serviço de Aplicações. Num plano de Consumo, a plataforma ativa as aplicações de função automaticamente.

Mesmo com Always On ativado, o tempo de execução para funções individuais é controlado pela `functionTimeout` definição no [host.jsno](functions-host-json.md#functiontimeout) ficheiro do projeto.

## <a name="scaling"></a>Dimensionamento

Utilizando um plano de Serviço de Aplicações, pode escalar manualmente adicionando mais instâncias VM. Também pode ativar a autoescala, embora a autoescala seja mais lenta do que a escala elástica do plano Premium. Para obter mais informações, consulte [a contagem de instâncias de escala manual ou automática.](../azure-monitor/autoscale/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json) Também pode aumentar a escala escolhendo um plano de Serviço de Aplicações diferente. Para obter mais informações, consulte [Scale up uma aplicação em Azure](../app-service/manage-scale-up.md). 

> [!NOTE] 
> Ao executar funções JavaScript (Node.js) num plano de Serviço de Aplicações, deve escolher um plano que tenha menos vCPUs. Para obter mais informações, consulte [Escolha os planos do Serviço de Aplicações de núcleo único.](functions-reference-node.md#choose-single-vcpu-app-service-plans) 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

## <a name="app-service-environments"></a>Ambientes do App Service

Executar num Ambiente de Serviço de Aplicações (ASE) permite isolar totalmente as suas funções e tirar partido de um maior número de casos do que um Plano de Serviço de Aplicações. Para começar, consulte [Introdução aos Ambientes de Serviço de Aplicações.](../app-service/environment/intro.md)

Se apenas quiser executar a sua aplicação de função numa rede virtual, pode fazê-lo utilizando o [plano Premium.](functions-premium-plan.md) Para saber mais, consulte [o Acesso ao site privado Do Azure Functions.](functions-create-private-site-access.md) 

## <a name="next-steps"></a>Passos seguintes

+ [Opções de hospedagem de funções Azure Functions](functions-scale.md)
+ [Descrição Geral do plano do Serviço de Aplicações do Azure](../app-service/overview-hosting-plans.md)
