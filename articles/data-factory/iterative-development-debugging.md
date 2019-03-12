---
title: Desenvolvimento iterativo e de depuração no Azure Data Factory | Documentos da Microsoft
description: Saiba como desenvolver e depurar pipelines do Data Factory iterativamente no portal do Azure.
ms.date: 09/26/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: a8028fdde93d06f7b25bf9bd8b4ed5a560a35f83
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575278"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Desenvolvimento iterativo e de depuração com o Azure Data Factory

O Azure Data Factory permite-lhe iterativamente desenvolver e depurar pipelines do Data Factory.

Para uma introdução de oito minutos e uma demonstração desta funcionalidade, veja o vídeo seguinte:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Recursos de depuração iterativos
Criar pipelines e de teste com o **depurar** capacidade na tela do pipeline sem ter de escrever uma única linha de código.

![Depurar a capacidade da tela de pipeline](media/iterative-development-debugging/iterative-development-image1.png)

Ver os resultados de seu teste é executado no **saída** janela da tela de pipeline.

![Janela de saída da tela de pipeline](media/iterative-development-debugging/iterative-development-image2.png)

Depois de uma execução de teste for bem sucedida, adicionar mais atividades ao seu pipeline e continuar a depuração de forma iterativa. Também pode **Cancelar** um teste enquanto ele estiver em curso.

![Cancelar uma execução de teste](media/iterative-development-debugging/iterative-development-image3.png)

Quando de teste, não tem de publicar as alterações à fábrica de dados antes de selecionar **depurar**. Esta funcionalidade é útil em cenários em que pretende certificar-se de que as alterações funcionam conforme esperado antes de atualizar o fluxo de trabalho de fábrica de dados.

> [!IMPORTANT]
> Selecionando **depurar** , na verdade, executa o pipeline. Então, por exemplo, se o pipeline contém uma atividade de cópia, a execução de teste copia dados de origem para destino. Como resultado, recomendamos que utilize o teste de pastas em suas atividades de cópia e outras atividades durante a depuração. Depois de ter depurado o pipeline, mude para as pastas reais que pretende utilizar nas operações normais.

## <a name="visualizing-debug-runs"></a>Visualização de execuções de depuração

É possível visualizar todas as execuções de depuração que estão em curso para a fábrica de dados num único local. Selecione **depuração do modo de exibição é executado** no canto superior direito da página. Esta funcionalidade é útil em cenários em que tiver pipelines mestres iniciar depuração execuções de pipelines de subordinados e pretender uma vista única para ver que todos os depuração Active Directory é executado.

![Selecione o ícone de execuções de depuração do Active Directory do Vista](media/iterative-development-debugging/view-debug-runs-image1.png)

![Lista de exemplos de depuração do Active Directory é executado](media/iterative-development-debugging/view-debug-runs-image2.png)

## <a name="monitoring-debug-runs"></a>Depuração de monitorização é executado

As execuções do teste iniciado com o **depurar** capacidade não estão disponíveis na lista de **Monitor** separador. Pode apenas ver executa acionada com **acionar agora**, **agenda**, ou **janela em cascata** acionadores no **Monitor** separador. Pode ver o último teste executar iniciada com o **depurar** capacidade no **saída** janela da tela de pipeline.

## <a name="setting-breakpoints-for-debugging"></a>Definindo pontos de interrupção para depuração

Fábrica de dados também lhe permite depurar até chegar uma determinada atividade na tela do pipeline. Colocar um ponto de interrupção na atividade até a qual pretende testar e selecione **depurar**. Fábrica de dados garante que o teste é executado apenas até a atividade de ponto de interrupção na tela do pipeline. Isso *depurar até* funcionalidade é útil quando não pretende testar todo o pipeline, mas apenas um subconjunto de atividades no pipeline.

![Pontos de interrupção na tela do pipeline](media/iterative-development-debugging/iterative-development-image4.png)

Para definir um ponto de interrupção, selecione um elemento na tela do pipeline. R *depurar até* opção aparece como um círculo vazio vermelho no canto superior direito do elemento.

![Antes de definir um ponto de interrupção no elemento selecionado](media/iterative-development-debugging/iterative-development-image5.png)

Depois de selecionar o *depurar até* opção, muda para um círculo vermelho preenchido para indicar o ponto de interrupção é ativado.

![Depois de definir um ponto de interrupção no elemento selecionado](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Passos Seguintes
[Integração contínua e implementação no Azure Data Factory](continuous-integration-deployment.md)
