---
title: Teste de desempenho e carga com o Aplicativo Azure insights | Microsoft Docs
description: Configurar testes de carga e desempenho com o Aplicativo Azure insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: db23fae6bb15e851d22e54b323428c061f55b34f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75406559"
---
# <a name="performance-testing"></a>Teste de desempenho

> [!NOTE]
> O serviço de teste de carga baseado em nuvem foi preterido. Para obter mais informações sobre a substituição, a disponibilidade do serviço e os serviços alternativos podem ser encontrados [aqui](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops).

Application Insights permite que você gere testes de carga para seus sites. Assim como os [testes de disponibilidade](monitor-web-app-availability.md), você pode enviar solicitações básicas ou [solicitações de várias etapas](availability-multistep.md) de agentes de teste do Azure em todo o mundo. Os testes de desempenho permitem simular até 20.000 usuários simultâneos por até 60 minutos.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Para criar um teste de desempenho, primeiro você precisa criar um recurso de Application Insights. Se você já tiver criado um recurso, prossiga para a próxima seção.

Na portal do Azure, selecione **criar um recurso** > **ferramentas para desenvolvedores** > **Application insights** e crie um recurso de Application insights.

## <a name="configure-performance-testing"></a>Configurar testes de desempenho

Se esta for a primeira vez que você cria o teste de desempenho, selecione **definir organização** e escolha uma organização de DevOps do Azure para ser a origem para seus testes de desempenho.

Em **Configurar**, vá para **teste de desempenho** e clique em **novo** para criar um teste.

![Indique, pelo menos, o URL do seu site](./media/performance-testing/new-performance-test.png)

Para criar um teste de desempenho básico, selecione um tipo de teste de **teste manual** e preencha as configurações desejadas para seu teste.

|Definição| Valor máximo
|----------|------------|
| Carga de Utilizadores | 20,000 |
| Duração (minutos)  | 60 |  

Depois que o teste for criado, clique em **Executar teste**.

Depois que o teste for concluído, você verá resultados semelhantes aos resultados abaixo:

![Test Results](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Configurar o teste na Web do Visual Studio

Application Insights recursos de teste de desempenho avançado são criados sobre projetos de desempenho e teste de carga do Visual Studio.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Passos seguintes

* [Testes na Web de várias etapas](availability-multistep.md)
* [Testes de ping de URL](monitor-web-app-availability.md)
