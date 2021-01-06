---
title: Dimensionamento e alojamento de Funções do Azure
description: Saiba como escolher entre o plano de consumo de funções Azure e o plano Premium.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 490fa46deabc822e416705fe9bf9c5cdb58f8cd6
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936778"
---
# <a name="azure-functions-hosting-options"></a>Opções de hospedagem de funções Azure Functions

Quando criar uma aplicação de função em Azure, tem de escolher um plano de hospedagem para a sua aplicação. Existem três planos básicos de hospedagem disponíveis para as Funções Azure: [Plano de Consumo,](consumption-plan.md) [Plano Premium](functions-premium-plan.md)e Plano [Dedicado (Serviço de Aplicações).](dedicated-plan.md) Todos os planos de hospedagem estão geralmente disponíveis (GA) em máquinas virtuais Linux e Windows.

O plano de hospedagem que escolhe dita os seguintes comportamentos:

* Como a sua aplicação de função é dimensionada.
* Os recursos disponíveis para cada instância de aplicação de função.
* Suporte para funcionalidades avançadas, como a conectividade da Rede Virtual Azure.

Este artigo fornece uma comparação detalhada entre os vários planos de hospedagem, juntamente com o alojamento baseado em Kubernetes.

## <a name="overview-of-plans"></a>Visão geral dos planos

Segue-se um resumo dos benefícios dos três principais planos de acolhimento de funções:

| | |
| --- | --- |  
|**[Plano de consumo](consumption-plan.md)**| Dimensione automaticamente e só pague por recursos de cálculo quando as suas funções estiverem em funcionamento.<br/><br/>No plano de Consumo, os casos do anfitrião funções são adicionados e removidos dinamicamente com base no número de eventos que chegam.<br/><br/> ✔ plano de hospedagem padrão.<br/>✔ Pague apenas quando as suas funções estiverem em funcionamento.<br/>✔ Escalas automaticamente, mesmo durante períodos de alta carga.|  
|**[Plano Premium](functions-premium-plan.md)**|Escala automaticamente com base na procura usando trabalhadores pré-aquecidos que executam aplicações sem atrasos após ficarem inativos, funcionam em casos mais poderosos e conectam-se a redes virtuais. <br/><br/>Considere o plano Azure Functions Premium nas seguintes situações: <br/><br/>✔ As aplicações de função funcionam continuamente, ou quase continuamente.<br/>✔ Tem um elevado número de pequenas execuções e uma nota de execução elevada, mas baixos segundos de GB no plano de consumo.<br/>✔ Precisa de mais OPÇÕES de CPU ou de memória do que as fornecidas pelo plano de Consumo.<br/>✔ O seu código tem de ser executado mais tempo do que o tempo máximo de execução permitido no plano de Consumo.<br/>✔ Necessita de funcionalidades que não estejam disponíveis no plano de Consumo, como a conectividade da rede virtual.|  
|**[Plano dedicado](dedicated-plan.md)** |Executar as suas funções dentro de um plano de Serviço de Aplicações com [taxas regulares de plano de serviço de aplicações.](https://azure.microsoft.com/pricing/details/app-service/windows/)<br/><br/>O melhor para cenários de longa duração onde as [funções duradouras](durable/durable-functions-overview.md) não podem ser usadas. Considere um plano de Serviço de Aplicações nas seguintes situações:<br/><br/>✔ Tem VMs existentes e subutilizados que já estão a executar outras instâncias do Serviço de Aplicações.<br/>✔ Pretende fornecer uma imagem personalizada para executar as suas funções. <br/>✔ são necessários escalões preditivos e custos.|  

As tabelas de comparação deste artigo também incluem as seguintes opções de hospedagem, que fornecem a maior quantidade de controlo e isolamento para executar as suas aplicações de função.  

| | |
| --- | --- |  
|**[ASE](dedicated-plan.md)** | App Service Environment (ASE) é uma funcionalidade de Serviço de Aplicações que fornece um ambiente totalmente isolado e dedicado para executar aplicações de Serviço de Aplicações de forma segura em alta escala.<br/><br/>AsE são adequadas para cargas de trabalho de aplicação que requerem: <br/><br/>✔ muito alta escala.<br/>✔ isolamento total do computação e acesso seguro à rede.<br/>✔ uso de alta memória.|  
| **[Utilizar o Kubernetes](functions-kubernetes-keda.md)** | Kubernetes fornece um ambiente totalmente isolado e dedicado que corre em cima da plataforma Kubernetes.<br/><br/> Kubernetes é apropriado para cargas de trabalho de aplicação que requerem: <br/>✔ requisitos de hardware personalizados.<br/>✔ isolamento e acesso seguro à rede.<br/>✔ Capacidade de funcionar em ambiente híbrido ou multi-nuvem.<br/>✔ Run ao lado das aplicações e serviços existentes da Kubernetes.|  

As restantes tabelas deste artigo comparam os planos sobre várias funcionalidades e comportamentos. Para uma comparação de custos entre planos de hospedagem dinâmicos (Consumo e Premium), consulte a página de preços do [Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Para preços das várias opções de plano dedicado, consulte a [página de preços do Serviço de Aplicações.](https://azure.microsoft.com/pricing/details/app-service/windows/) 

## <a name="operating-systemruntime"></a>Sistema operativo/tempo de execução

A tabela a seguir mostra o sistema operativo suportado e o suporte ao tempo de execução da linguagem para os planos de hospedagem.

| | Linux<sup>1</sup><br/>Apenas código | Windows<sup>2</sup><br/>Apenas código | Linux<sup>1,3</sup><br/>Recipiente de estivador |
| --- | --- | --- | --- |
| **[Plano de consumo](consumption-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | Sem apoio  |
| **[Plano Premium](functions-premium-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| **[Plano dedicado](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[ASE](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| **[Utilizar o Kubernetes](functions-kubernetes-keda.md)** | n/a | n/a |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

<sup>1</sup> Linux é o único sistema operativo suportado para a pilha de tempo de execução Python. <br/>
<sup>2</sup> O Windows é o único sistema operativo suportado para a pilha de tempo de execução PowerShell.<br/>
<sup>3</sup> O Linux é o único sistema operativo suportado para contentores Docker.<br/>

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="scale"></a>Escala

A tabela seguinte compara os comportamentos de escala dos vários planos de hospedagem.

| | Aumentar horizontalmente | Max # instâncias |
| --- | --- | --- |
| **[Plano de consumo](consumption-plan.md)** | [Motivado por eventos.](event-driven-scaling.md) Escale automaticamente, mesmo durante períodos de alta carga. A azure Functions infrastructure dimensiona CPU e recursos de memória adicionando instâncias adicionais do anfitrião funções, com base no número de eventos de gatilho de entrada. | 200 |
| **[Plano Premium](functions-premium-plan.md)** | [Motivado por eventos.](event-driven-scaling.md) Escale automaticamente, mesmo durante períodos de alta carga. A Azure Functions escala os recursos de CPU e memória adicionando instâncias adicionais do anfitrião funções, com base no número de eventos em que as suas funções são ativadas. |100|
| **[Plano dedicado](dedicated-plan.md)**<sup>1</sup> | Manual/autoescala |10-20|
| **[ASE](dedicated-plan.md)**<sup>1</sup> | Manual/autoescala |100 |
| **[Utilizar o Kubernetes](functions-kubernetes-keda.md)**  | Autoescala orientada para eventos para clusters Kubernetes usando [KEDA](https://keda.sh). | Varia &nbsp; por &nbsp; cluster&nbsp;&nbsp;|

<sup>1</sup> Para limites específicos para as várias opções do plano de Serviço de Aplicações, consulte os limites do [plano do Serviço de Aplicações.](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)

## <a name="cold-start-behavior"></a>Comportamento de início a frio

|    |    | 
| -- | -- |
| **[Plano de consumo &nbsp;](consumption-plan.md)** | As aplicações podem escalar para zero quando inativas, o que significa que alguns pedidos podem ter latência adicional no arranque.  O plano de consumo tem algumas otimizações para ajudar a diminuir o tempo de início a frio, incluindo puxar de funções de espaço reservado pré-aquecido que já têm o hospedeiro de função e os processos linguísticos em funcionamento. |
| **[Plano Premium](functions-premium-plan.md)** | Casos perpetuamente quentes para evitar qualquer arranque a frio. |
| **[Plano dedicado](dedicated-plan.md)** | Ao executar um plano dedicado, o anfitrião funções pode funcionar continuamente, o que significa que o arranque a frio não é realmente um problema. |
| **[ASE](dedicated-plan.md)** | Ao executar um plano dedicado, o anfitrião funções pode funcionar continuamente, o que significa que o arranque a frio não é realmente um problema. |
| **[Utilizar o Kubernetes](functions-kubernetes-keda.md)**  | Dependendo da configuração KEDA, as aplicações podem ser configuradas para evitar um arranque a frio. Se configurado para escalar a zero, então um arranque a frio é experimentado para novos eventos. 

## <a name="service-limits"></a>Limites do serviço

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

## <a name="networking-features"></a>Funcionalidades de rede

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="billing"></a>Faturação

| | | 
| --- | --- |
| **[Plano de consumo](consumption-plan.md)** | Pague apenas o tempo que as suas funções executarem. A faturação baseia-se no número de execuções, no tempo de execução e na memória utilizada. |
| **[Plano Premium](functions-premium-plan.md)** | O plano premium baseia-se no número de segundos e memórias do núcleo utilizados em instâncias necessárias e pré-aquecidas. Pelo menos um caso por plano deve ser sempre aquecido. Este plano fornece os preços mais previsíveis. |
| **[Plano dedicado](dedicated-plan.md)* | Você paga o mesmo por aplicações de função em um Plano de Serviço de Aplicações como você faria para outros recursos do Serviço de Aplicações, como aplicações web.|
| **[Ambiente de Serviço de Aplicações (ASE)](dedicated-plan.md)** | Há uma taxa mensal fixa para um ASE que paga a infraestrutura e não muda com o tamanho da ASE. Há também um custo por app service plano vCPU. Todas as aplicações alojadas num ASE estão na SKU de preços Isolada. |
| **[Utilizar o Kubernetes](functions-kubernetes-keda.md)**| Paga apenas os custos do seu cluster Kubernetes; nenhuma faturação adicional para funções. A sua aplicação de função funciona como uma carga de trabalho de aplicação em cima do seu cluster, tal como uma aplicação regular. |

## <a name="next-steps"></a>Passos seguintes

+ [Tecnologias de implantação em Funções Azure](functions-deployment-technologies.md) 
+ [Guia do programador das Funções do Azure](functions-reference.md)