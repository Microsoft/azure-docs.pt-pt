---
title: Teste de desempenho e carga com Insights de Aplicação Azure [ Microsoft Docs
description: Configurar testes de desempenho e carga com insights de aplicação azure
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 0bfb60b876add70be6cbcd3d726fb3317a8dfcf9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77669867"
---
# <a name="performance-testing"></a>Teste de desempenho

> [!NOTE]
> O serviço de teste de carga baseado em nuvem foi depreciado. Mais informações sobre a depreciação, a disponibilidade do serviço e serviços alternativos podem ser consultados [aqui.](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops)

Os Insights de Aplicação permitem-lhe gerar testes de carga para os seus websites. Tal como [os testes de disponibilidade,](monitor-web-app-availability.md)pode enviar pedidos básicos ou [pedidos em várias etapas](availability-multistep.md) de agentes de teste Azure em todo o mundo. Os testes de desempenho permitem simular até 20.000 utilizadores simultâneos durante 60 minutos.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Para criar um teste de desempenho, primeiro é necessário criar um recurso Application Insights. Se já criou um recurso, proceda à secção seguinte.

A partir do portal Azure, selecione **Criar um recurso** > **Developer Tools** > **Application Insights** e criar um recurso Deinsights de Aplicação.

## <a name="configure-performance-testing"></a>Configure testes de desempenho

Se esta for a sua primeira vez criando o teste de desempenho select **set Organization** e escolha uma organização Azure DevOps para ser a fonte para os seus testes de desempenho.

Em **Configurar,** vá a Testes de **Desempenho** e clique em **Novo** para criar um teste.

![Indique, pelo menos, o URL do seu site](./media/performance-testing/new-performance-test.png)

Para criar um teste de desempenho básico, selecione um tipo de teste de **teste** e preencha as definições desejadas para o seu teste.

|Definição| Valor máximo
|----------|------------|
| Carga do utilizador | 20 000 |
| Duração (Ata)  | 60 |  

Depois de criado o teste, clique no **teste executar**.

Uma vez concluído o teste, verá resultados semelhantes aos resultados abaixo:

![Resultados dos testes](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Configure Visual Studio web teste

As capacidades avançadas de teste de desempenho da Application Insights são construídas em cima de projetos de teste de desempenho e de carga do Estúdio Visual.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Passos seguintes

* [Testes Web com vários passos](availability-multistep.md)
* [Testes de ping de url](monitor-web-app-availability.md)
