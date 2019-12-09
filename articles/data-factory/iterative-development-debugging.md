---
title: Desenvolvimento iterativo e depuração no Azure Data Factory
description: Saiba como desenvolver e depurar Data Factory pipelines iterativamente no portal do Azure.
ms.date: 09/26/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 3a771181f8f2785339cbc47e0a0234b9c4e39adc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926852"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Desenvolvimento iterativo e depuração com Azure Data Factory

Azure Data Factory permite desenvolver e depurar de forma iterativa Data Factory pipelines.

Para uma introdução e uma demonstração de oito minutos desse recurso, Assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Recursos de depuração iterativa
Crie pipelines e execute testes usando o recurso de **depuração** na tela do pipeline sem escrever uma única linha de código.

![Capacidade de depuração na tela do pipeline](media/iterative-development-debugging/iterative-development-image1.png)

Exiba os resultados das execuções de teste na janela **saída** da tela do pipeline.

![Janela de saída da tela do pipeline](media/iterative-development-debugging/iterative-development-image2.png)

Depois que uma execução de teste for realizada com sucesso, adicione mais atividades ao seu pipeline e continue a depuração de maneira iterativa. Você também pode **Cancelar** uma execução de teste enquanto ele está em andamento.

![Cancelar uma execução de teste](media/iterative-development-debugging/iterative-development-image3.png)

Quando você realiza execuções de teste, não precisa publicar suas alterações na data factory antes de selecionar **depurar**. Esse recurso é útil em cenários em que você deseja garantir que as alterações funcionem conforme o esperado antes de atualizar o data factory fluxo de trabalho.

> [!IMPORTANT]
> Selecionar **depurar** realmente executa o pipeline. Portanto, por exemplo, se o pipeline contiver atividade de cópia, a execução de teste copiará dados da origem para o destino. Como resultado, recomendamos que você use pastas de teste em suas atividades de cópia e outras atividades durante a depuração. Depois de ter depurado o pipeline, alterne para as pastas reais que você deseja usar em operações normais.

## <a name="visualizing-debug-runs"></a>Visualizando execuções de depuração

Você pode visualizar todas as execuções de depuração que estão em andamento para sua data factory em um único local. Selecione **Exibir execuções de depuração** no canto superior direito da página. Esse recurso é útil em cenários em que você tem os pipelines mestres iniciar fora das execuções de depuração para pipelines filho e você deseja uma única exibição para ver todas as execuções de depuração ativas.

![Selecione o ícone Exibir execuções de depuração ativas](media/iterative-development-debugging/view-debug-runs-image1.png)

![Lista de exemplos de execuções de depuração ativas](media/iterative-development-debugging/view-debug-runs-image2.png)

Se você tiver sessões de depuração de fluxo de dados ativas, essas sessões serão exibidas na parte inferior da janela de depuração ativa. Você pode selecionar uma sessão de fluxo de dados ativa e parar o respectivo cluster.

![Lista de exemplos de execuções de depuração de fluxo de dados ativas](media/data-flow/dfsessions.png)

## <a name="monitoring-debug-runs"></a>Monitorando execuções de depuração

As execuções de teste iniciadas com a capacidade de **depuração** não estão disponíveis na lista na guia **Monitor** . Você só pode ver execuções disparadas com gatilhos de **janela** de **gatilho agora**, **agendamento**ou em cascata na guia **monitorar** . Você pode ver a última execução de teste iniciada com a capacidade de **depuração** na janela **saída** da tela do pipeline.

## <a name="setting-breakpoints-for-debugging"></a>Definindo pontos de interrupção para depuração

Data Factory também permite que você depure até atingir uma atividade específica na tela do pipeline. Basta colocar um ponto de interrupção na atividade até o qual você deseja testar e selecionar **depurar**. Data Factory garante que o teste seja executado somente até a atividade de ponto de interrupção na tela do pipeline. Esse recurso de *depuração until* é útil quando você não deseja testar todo o pipeline, mas apenas um subconjunto de atividades dentro do pipeline.

![Pontos de interrupção na tela do pipeline](media/iterative-development-debugging/iterative-development-image4.png)

Para definir um ponto de interrupção, selecione um elemento na tela do pipeline. Uma opção *debug until* aparece como um círculo vermelho vazio no canto superior direito do elemento.

![Antes de definir um ponto de interrupção no elemento selecionado](media/iterative-development-debugging/iterative-development-image5.png)

Depois de selecionar a opção *depurar até* , ela é alterada para um círculo vermelho preenchido para indicar que o ponto de interrupção está habilitado.

![Depois de definir um ponto de interrupção no elemento selecionado](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Passos seguintes
[Integração e implantação contínuas no Azure Data Factory](continuous-integration-deployment.md)
