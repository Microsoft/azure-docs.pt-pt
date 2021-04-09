---
title: Desenvolvimento iterativo e depuração na Azure Data Factory
description: Aprenda a desenvolver e depurar os oleodutos da Data Factory iterativamente no ADF UX
ms.date: 02/23/2021
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: kromerm
ms.author: makromer
ms.openlocfilehash: ef47d311f5f096db962ea27792e7871dbf0ef81a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101712968"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Desenvolvimento iterativo e de depuração com o Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Azure Data Factory permite-lhe desenvolver e depurar iterativamente os oleodutos da Data Factory à medida que está a desenvolver as suas soluções de integração de dados. Estas funcionalidades permitem-lhe testar as suas alterações antes de criar um pedido de puxar ou publicá-las no serviço de fábrica de dados. 

Para uma introdução de oito minutos e demonstração desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="debugging-a-pipeline"></a>Depurar um pipeline

Como autor é que usa a tela do pipeline, pode testar as suas atividades utilizando a capacidade **de Debug.** Quando faz testes, não tem de publicar as suas alterações na fábrica de dados antes de selecionar **o Debug.** Esta funcionalidade é útil em cenários em que pretende certificar-se de que as alterações funcionam como esperado antes de atualizar o fluxo de trabalho da fábrica de dados.

![Capacidade de depuração na tela do gasoduto](media/iterative-development-debugging/iterative-development-1.png)

À medida que o gasoduto está em funcionamento, pode ver os resultados de cada atividade no **separador saída** da tela do gasoduto.

Veja os resultados do seu teste na janela **de saída** da tela do gasoduto.

![Janela de saída da tela do gasoduto](media/iterative-development-debugging/iterative-development-2.png)

Depois de um teste ter sucesso, adicione mais atividades ao seu oleoduto e continue a depurar de forma iterativa. Também pode **cancelar** um teste enquanto está em curso.

> [!IMPORTANT]
> A seleção **de Debug** realmente corre o oleoduto. Por exemplo, se o gasoduto contiver atividade de cópia, o teste de execução copia dados de origem para destino. Como resultado, recomendamos que utilize pastas de teste nas suas atividades de cópia e outras atividades ao depurar. Depois de depurar o oleoduto, mude para as pastas reais que pretende utilizar em operações normais.

### <a name="setting-breakpoints"></a>Definição de pontos de rutura

A Azure Data Factory permite-lhe depurar um oleoduto até chegar a uma determinada atividade na tela do gasoduto. Coloque um ponto de rutura na atividade até à qual pretende testar e selecione **Debug**. A Data Factory garante que o teste funciona apenas até à atividade do ponto de rutura na tela do gasoduto. Esta funcionalidade *Debug Até* que a funcionalidade seja útil quando não pretende testar todo o pipeline, mas apenas um subconjunto de atividades dentro do oleoduto.

![Pontos de rutura na tela do gasoduto](media/iterative-development-debugging/iterative-development-3.png)

Para definir um ponto de rutura, selecione um elemento na tela do gasoduto. Um *Debug Até* que a opção apareça como um círculo vermelho vazio no canto superior direito do elemento.

![Antes de definir um ponto de rutura no elemento selecionado](media/iterative-development-debugging/iterative-development-4.png)

Depois de selecionar a opção *Debug Até* ao fim, muda para um círculo vermelho preenchido para indicar que o ponto de rutura está ativado.

![Depois de definir um ponto de rutura no elemento selecionado](media/iterative-development-debugging/iterative-development-5.png)

## <a name="monitoring-debug-runs"></a>Depuragem de depuragem corre

Quando executar uma depuragem de gasoduto, os resultados aparecerão na janela de **saída** da tela do gasoduto. O separador de saída apenas conterá a execução mais recente que ocorreu durante a sessão do navegador atual. 

![Janela de saída da tela do gasoduto](media/iterative-development-debugging/iterative-development-2.png)

Para ver uma visão histórica das corridas de depurg ou ver uma lista de todas as corridas de depurg ativos, você pode entrar na experiência **Monitor.** 

![Selecione o ícone de depurar ativo ver corre](media/iterative-development-debugging/view-debug-runs.png)

> [!NOTE]
> O serviço Azure Data Factory apenas persiste na história do depuração durante 15 dias. 

## <a name="debugging-mapping-data-flows"></a>Depurando fluxos de dados de mapeamento

Mapear fluxos de dados permite-lhe construir uma lógica de transformação de dados sem código que corre em escala. Ao construir a sua lógica, pode ligar uma sessão de depuração para trabalhar interativamente com os seus dados usando um cluster De spark vivo. Para saber mais, leia sobre [o modo de depuramento de fluxo de dados de mapeamento](concepts-data-flow-debug-mode.md).

Pode monitorizar sessões de depurar fluxo de dados ativos através de uma fábrica na experiência **Monitor.**

![Ver sessões de depurar fluxo de dados](media/iterative-development-debugging/view-dataflow-debug-sessions.png)

A pré-visualização de dados no designer de fluxos de dados e na depuragem de gasodutos dos fluxos de dados destinam-se a funcionar melhor com pequenas amostras de dados. No entanto, se precisar de testar a sua lógica num pipeline ou fluxo de dados contra grandes quantidades de dados, aumente o tamanho do Tempo de Execução de Integração Azure que está a ser utilizado na sessão de depuração com mais núcleos e um mínimo de cálculo de finalidade geral.
 
### <a name="debugging-a-pipeline-with-a-data-flow-activity"></a>Depurar um oleoduto com uma atividade de fluxo de dados

Ao executar um gasoduto de depuragem executado com um fluxo de dados, você tem duas opções sobre quais computar a utilizar. Pode utilizar um cluster de depuração existente ou criar um novo cluster just-in-time para os fluxos de dados.

A utilização de uma sessão de depuração existente reduzirá consideravelmente o tempo de arranque do fluxo de dados, uma vez que o cluster já está em funcionamento, mas não é recomendado para cargas de trabalho complexas ou paralelas, uma vez que pode falhar quando vários trabalhos são executados ao mesmo tempo.

A utilização do tempo de funcionação da atividade criará um novo cluster utilizando as definições especificadas no tempo de integração de cada atividade de fluxo de dados. Isto permite que cada trabalho seja isolado e deve ser utilizado para cargas de trabalho complexas ou testes de desempenho. Também pode controlar o TTL no Azure IR para que os recursos de cluster utilizados para depuração ainda estejam disponíveis para esse período de tempo para atender pedidos de emprego adicionais.

> [!NOTE]
> Se tiver um pipeline com fluxos de dados executados em paralelo ou fluxos de dados que precisam de ser testados com grandes conjuntos de dados, escolha "Use Activity Runtime" para que a Data Factory possa utilizar o Tempo de Execução de Integração que selecionou na sua atividade de fluxo de dados. Isto permitirá que os fluxos de dados sejam executados em vários clusters e podem acomodar as suas execuções paralelas de fluxo de dados.

![Executar um oleoduto com um fluxo de dados](media/iterative-development-debugging/iterative-development-dataflow.png)

## <a name="next-steps"></a>Passos seguintes

Depois de testar as suas alterações, promova-as para ambientes mais elevados utilizando [integração contínua e implantação na Azure Data Factory](continuous-integration-deployment.md).
