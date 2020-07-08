---
title: Testes de desempenho e carga com Azure Application Insights / Microsoft Docs
description: Configurar testes de desempenho e carga com insights de aplicação Azure
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 0bfb60b876add70be6cbcd3d726fb3317a8dfcf9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77669867"
---
# <a name="performance-testing"></a>Teste de desempenho

> [!NOTE]
> O serviço de testes de carga baseado na nuvem foi depreciado. Mais informações sobre a depreciação, a disponibilidade do serviço e serviços alternativos podem ser consultados [aqui.](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops)

O Application Insights permite-lhe gerar testes de carga para os seus websites. Tal como [os testes de disponibilidade,](monitor-web-app-availability.md)pode enviar pedidos básicos ou [pedidos em várias etapas](availability-multistep.md) de agentes de teste da Azure em todo o mundo. Os testes de desempenho permitem simular até 20.000 utilizadores simultâneos até 60 minutos.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Para criar um teste de desempenho, primeiro precisa de criar um recurso Application Insights. Se já criou um recurso, dirija-se à secção seguinte.

A partir do portal Azure, **selecione Criar um Recurso**Developer  >  **Tools**  >  **Application Insights** e criar um recurso Application Insights.

## <a name="configure-performance-testing"></a>Configure testes de desempenho

Se esta for a primeira vez que crias testes de desempenho **selecione set Organization** e escolha uma organização Azure DevOps para ser a fonte para os seus testes de desempenho.

Em **Configuração,** vá ao **Teste de Desempenho** e clique em **Novo** para criar um teste.

![Indique, pelo menos, o URL do seu site](./media/performance-testing/new-performance-test.png)

Para criar um teste de desempenho básico, selecione um tipo de teste de **teste** e preencha as definições desejadas para o seu teste.

|Definição| Valor máximo
|----------|------------|
| Carga do utilizador | 20 000 |
| Duração (Minutos)  | 60 |  

Depois de o seu teste ser criado, clique em **Executar teste**.

Uma vez concluído o teste, verá resultados semelhantes aos resultados abaixo:

![Resultados dos testes](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Configure visual Studio teste web

As capacidades avançadas de teste de desempenho da Application Insights são construídas em cima de projetos de teste de desempenho e de carga do Estúdio Visual.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Próximos passos

* [Testes Web com vários passos](availability-multistep.md)
* [Testes de url ping](monitor-web-app-availability.md)
