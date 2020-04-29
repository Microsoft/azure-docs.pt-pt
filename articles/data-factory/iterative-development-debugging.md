---
title: Desenvolvimento iterativo e depuração na Fábrica de Dados Azure
description: Aprenda a desenvolver e depurar oleodutos da Fábrica de Dados iterativamente no portal Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81411520"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Desenvolvimento iterativo e de depuração com o Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Azure Data Factory permite-lhe desenvolver e depurar iterativamente os oleodutos data factory.

Para uma introdução de oito minutos e demonstração desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Características iterativas de depuração
Crie gasodutos e faça ensaios utilizando a capacidade **de Debug** na tela do gasoduto sem escrever uma única linha de código.

![Capacidade de depuração na tela do oleoduto](media/iterative-development-debugging/iterative-development-image1.png)

Veja os resultados dos seus ensaios na janela **de saída** da tela do gasoduto.

![Janela de saída da tela do gasoduto](media/iterative-development-debugging/iterative-development-image2.png)

Depois de um teste ter sucesso, adicione mais atividades ao seu oleoduto e continue a depurar-se de forma iterativa. Também pode **cancelar** um teste enquanto estiver em curso.

![Cancele um teste](media/iterative-development-debugging/iterative-development-image3.png)

Quando faz testes, não tem de publicar as suas alterações na fábrica de dados antes de selecionar **o Debug**. Esta funcionalidade é útil em cenários onde pretende certificar-se de que as alterações funcionam como esperado antes de atualizar o fluxo de trabalho da fábrica de dados.

> [!IMPORTANT]
> Selecionando **debug** realmente executa o oleoduto. Assim, por exemplo, se o gasoduto contiver atividade de cópia, o teste executa dados de origem para destino. Como resultado, recomendamos que utilize pastas de teste nas suas atividades de cópia e outras atividades ao depurar. Depois de ter depurado o gasoduto, mude para as pastas reais que pretende utilizar em operações normais.

## <a name="visualizing-debug-runs"></a>Visualizar corridas de depuração

Pode visualizar todas as corridas de depuração que estão em curso para a sua fábrica de dados num só local. Selecione **Ver depuração corre** no canto superior direito da página. Esta funcionalidade é útil em cenários em que você tem os principais oleodutos que iniciam corridas de depuração para gasodutos infantis, e você quer uma única vista para ver todas as corridas de depuração ativas.

![Selecione o ícone de depuração ativo Ver](media/iterative-development-debugging/view-debug-runs-image1.png)

![Lista de amostras de corridas ativas de depuração](media/iterative-development-debugging/view-debug-runs-image2.png)

Se tiver sessões ativas de depuração do Fluxo de Dados, essas sessões aparecerão na parte inferior da janela de depuração ativa. Pode selecionar uma sessão de fluxo de dados ativa e parar o respetivo cluster.

![Lista de amostras de depuração de fluxo de dados ativos](media/data-flow/dfsessions.png)

## <a name="monitoring-debug-runs"></a>Monitorização de depurações

Os ensaios iniciados com a capacidade **Debug** não estão disponíveis na lista no separador **Monitor.** Só é possível ver as correções acionadas com gatilhos **de gatilho agora,** **agendar**ou **tumbling window** no separador **Monitor.** Pode ver o último ensaio iniciado com a capacidade **de Depuração** na janela **de saída** da tela do gasoduto.

## <a name="setting-breakpoints-for-debugging"></a>Definição de pontos de rutura para depuração

A Data Factory também permite depurar até atingir uma determinada atividade na tela do gasoduto. Basta colocar um ponto de rutura na atividade até que queira testar, e selecione **Debug**. A Data Factory garante que o teste só funciona até à atividade do breakpoint na tela do gasoduto. Este *depurado Até que* a funcionalidade seja útil quando não quer testar todo o oleoduto, mas apenas um subconjunto de atividades dentro do pipeline.

![Pontos de rutura na tela do oleoduto](media/iterative-development-debugging/iterative-development-image4.png)

Para definir um ponto de rutura, selecione um elemento na tela do gasoduto. Um *Debug Até que* a opção apareça como um círculo vermelho vazio no canto superior direito do elemento.

![Antes de definir um ponto de rutura no elemento selecionado](media/iterative-development-debugging/iterative-development-image5.png)

Depois de selecionar o *Debug Até à* opção, muda para um círculo vermelho preenchido para indicar que o ponto de rutura está ativado.

![Depois de definir um ponto de rutura no elemento selecionado](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Passos seguintes
[Integração e implantação contínuas na Azure Data Factory](continuous-integration-deployment.md)
