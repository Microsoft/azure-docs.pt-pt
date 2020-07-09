---
title: Desenvolvimento iterativo e depuração na Azure Data Factory
description: Aprenda a desenvolver e depurar os oleodutos da Data Factory iterativamente no portal Azure.
ms.date: 09/26/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 49e406f0730189439bb1c2529b23d8ad6d760a2f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81411520"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Desenvolvimento iterativo e de depuração com o Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Azure Data Factory permite-lhe desenvolver iterativamente e depurar os oleodutos da Data Factory.

Para uma introdução de oito minutos e demonstração desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Características de depuragem iterativa
Crie oleodutos e faça ensaios utilizando a capacidade **de Debug** na tela do gasoduto sem escrever uma única linha de código.

![Capacidade de depuração na tela do gasoduto](media/iterative-development-debugging/iterative-development-image1.png)

Veja os resultados do seu teste na janela **de saída** da tela do gasoduto.

![Janela de saída da tela do gasoduto](media/iterative-development-debugging/iterative-development-image2.png)

Depois de um teste ter sucesso, adicione mais atividades ao seu oleoduto e continue a depurar de forma iterativa. Também pode **cancelar** um teste enquanto está em curso.

![Cancelar um teste](media/iterative-development-debugging/iterative-development-image3.png)

Quando faz testes, não tem de publicar as suas alterações na fábrica de dados antes de selecionar **o Debug.** Esta funcionalidade é útil em cenários em que pretende certificar-se de que as alterações funcionam como esperado antes de atualizar o fluxo de trabalho da fábrica de dados.

> [!IMPORTANT]
> A seleção **de Debug** realmente corre o oleoduto. Assim, por exemplo, se o gasoduto contiver atividade de cópia, o teste de execução copia dados de origem para destino. Como resultado, recomendamos que utilize pastas de teste nas suas atividades de cópia e outras atividades ao depurar. Depois de depurar o oleoduto, mude para as pastas reais que pretende utilizar em operações normais.

## <a name="visualizing-debug-runs"></a>Visualizar depurando runs de depurg

Você pode visualizar todas as corridas de depurg que estão em andamento para a sua fábrica de dados em um lugar. Selecione **Ver depurar corre** no canto superior direito da página. Esta funcionalidade é útil em cenários em que você tem oleodutos mestre saindo de depurações para oleodutos infantis, e você quer uma única vista para ver todos os depurs ativos executados.

![Selecione o ícone de depurar ativo ver corre](media/iterative-development-debugging/view-debug-runs-image1.png)

![Lista de amostras de depurativas ativas corre](media/iterative-development-debugging/view-debug-runs-image2.png)

Se tiver sessões de depurar fluxo de dados ativas, essas sessões aparecerão na parte inferior da janela de depurar ativa. Pode selecionar uma sessão de fluxo de dados ativo e parar o respetivo cluster.

![Lista de amostras de fluxos de dados ativos corre](media/data-flow/dfsessions.png)

## <a name="monitoring-debug-runs"></a>Depuragem de depuragem corre

Os testes iniciados com a capacidade **Debug** não estão disponíveis na lista no **separador Monitor.** Só é possível ver as execuções acionadas com **trigger Now**, **Agendar**ou **acionar** a janela na teeta **Monitor.** Pode ver o último teste iniciado com a capacidade **de Debug** na janela de **saída** da tela do gasoduto.

## <a name="setting-breakpoints-for-debugging"></a>Definição de pontos de rutura para depuração

A Data Factory também permite depurar até chegar a uma determinada atividade na tela do gasoduto. Basta colocar um ponto de rutura na atividade até que queira testar, e selecione **Debug**. A Data Factory garante que o teste funciona apenas até à atividade do ponto de rutura na tela do gasoduto. Esta funcionalidade *Debug Até* que a funcionalidade seja útil quando não pretende testar todo o pipeline, mas apenas um subconjunto de atividades dentro do oleoduto.

![Pontos de rutura na tela do gasoduto](media/iterative-development-debugging/iterative-development-image4.png)

Para definir um ponto de rutura, selecione um elemento na tela do gasoduto. Um *Debug Até* que a opção apareça como um círculo vermelho vazio no canto superior direito do elemento.

![Antes de definir um ponto de rutura no elemento selecionado](media/iterative-development-debugging/iterative-development-image5.png)

Depois de selecionar a opção *Debug Até* ao fim, muda para um círculo vermelho preenchido para indicar que o ponto de rutura está ativado.

![Depois de definir um ponto de rutura no elemento selecionado](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Passos seguintes
[Integração contínua e implantação na Azure Data Factory](continuous-integration-deployment.md)
