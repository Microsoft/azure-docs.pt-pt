---
title: Desempenho e testes de carga com o Azure Application Insights | Documentos da Microsoft
description: Configurar o desempenho e testes de carga com o Azure Application Insights
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 55d743e32f6db0828317d3764a97bcb35b104dad
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305128"
---
# <a name="performance-testing"></a>Teste de desempenho

> [!NOTE]
> A serviço de teste de carga baseado na nuvem foi preterida. Podem encontrar mais informações sobre a preterição, a disponibilidade do serviço e serviços alternativos [aqui](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops).

O Application Insights permite-lhe gerar testes de carga nos seus Web sites. Como [testes de disponibilidade](monitor-web-app-availability.md), pode enviar qualquer uma das solicitações básicas ou [pedidos com vários passos](availability-multistep.md) do Azure de agentes em todo o mundo de teste. Testes de desempenho permitem simular até 20 000 utilizadores simultâneos para até 60 minutos.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Para criar um teste de desempenho, tem primeiro de criar um recurso do Application Insights. Se já tiver criado um recurso de avançar para a secção seguinte.

A partir do portal do Azure, selecione **criar um recurso** > **ferramentas de programação** > **Application Insights** e criar um Application Insights recurso.

## <a name="configure-performance-testing"></a>Configurar testes de desempenho

Se esta é a primeira vez que criar selecione de teste de desempenho **organização definir** e escolha uma organização de DevOps do Azure para ser a origem para seus testes de desempenho.

Sob **configurar**, aceda à **testes de desempenho** e clique em **New** para criar um teste.

![Indique, pelo menos, o URL do seu site](./media/performance-testing/new-performance-test.png)

Para criar um teste de desempenho básico, selecione um tipo de teste de **teste Manual** e preencha as definições pretendidas para o teste.

|Definição| Valor máximo
|----------|------------|
| Carga de utilizador | 20,000 |
| Duração (minutos)  | 60 |  

Depois do teste é criado, clique em **executar teste**.

Assim que o teste estiver concluído, verá os resultados com aspeto semelhantes para os resultados abaixo:

![Resultados do teste](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Configurar teste de web do Visual Studio

O Application Insights, as funções de teste de desempenho avançadas são criadas com base no desempenho do Visual Studio e carga projetos de teste.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Passos Seguintes

* [Testes web de vários passos](availability-multistep.md)
* [Testes de ping do URL](monitor-web-app-availability.md)