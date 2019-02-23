---
title: Monitorização Visual de fluxo de dados de mapeamento de fábrica de dados do Azure
description: Para monitorizar visualmente fluxos de dados do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 90aa6261aebb9d1f7da89c101854bad8061dd6ff
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737863"
---
# <a name="monitor-data-flows"></a>Fluxos de dados do monitor

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Depois de concluir a criação e a depuração do seu fluxo de dados, desejará agendar o fluxo de dados para executar com base numa agenda dentro do contexto de um pipeline. Pode agendar o pipeline do Azure Data Factory usando gatilhos. Ou pode utilizar a opção acionar agora do criador do Pipeline da fábrica de dados do Azure para executar uma execução single-use para testar o fluxo de dados dentro do contexto de pipeline.

Quando executar o seu pipeline, poderá monitorizar o pipeline e todas as atividades contidas no pipeline, incluindo a atividade de fluxo de dados. Clique no ícone do monitor no painel à esquerda de IU do Azure Data Factory. Verá um ecrã semelhante ao abaixo. Os ícones realçados, poderá explorar as atividades no pipeline, incluindo a atividade de fluxo de dados.

<img src="media/data-flow/mon001.png" width="800">

Irá ver estatísticas neste nível como inculding bem a tempos de execução e o estado. O ID de execução ao nível de atividade é diferente de que a execução ID ao nível do pipeline. O ID de execução de mensagens em fila ao nível do anterior é para o pipeline. Clicar os óculos irá dar-lhe detalhes profunda sobre a execução de fluxo de dados.

<img src="media/data-flow/mon002.png" width="800">

Quando estiver no nó de gráfico, a vista de monitorização, verá uma versão simplificada de só de visualização de seu gráfico de fluxo de dados.

<img src="media/data-flow/mon003.png" width="800">

## <a name="view-data-flow-execution-plans"></a>Planos de execução de fluxo de dados de exibição

Quando o fluxo de dados é executado no Databricks, Azure Data Factory determina os caminhos de código ideal com base em entirity do seu fluxo de dados. Além disso, os caminhos de execução podem ocorrer em diferentes nós de escalamento horizontal e partições de dados. Por conseguinte, o gráfico de monitorização representa o design do seu fluxo, tendo em conta o caminho de execução das transformações. Quando clica em nós individuais, verá "agrupamentos", que representam o código que foi executado em conjunto no cluster. Os tempos e contagens de que vê representam esses grupos em vez dos passos individuais em seu design.

<img src="media/data-flow/mon004.png" width="800"> 

* Ao clicar no espaço de aberto na janela de monitorização, as estatísticas no painel inferior irão apresentar a exceder o tempo e a contagem de linhas para cada Sink e as transformações que levou para os dados de sink de linhagem de transformação.

* Quando seleciona transformações individuais, irá receber comentários adicionais sobre o painel do lado direito que mostra estatísticas de partição, contagens de coluna, assimetrias (como uniformemente é os dados distribuídos por partições), e kurtosis (os dados são como spikey).

* Quando clicar no Sink na vista de nós, verá a linhagem de coluna. Existem três métodos diferentes que as colunas são acumuladas em todo o fluxo de dados para ser encaminhado para o Sink. São:

  * Calculado: Utilizar a coluna para processamento condicional ou dentro de uma expressão no seu fluxo de dados, mas não a cair no Sink
  * Derivada: A coluna é uma nova coluna que gerou no seu fluxo, ou seja, não estava presente na origem
  * Mapeadas: A coluna teve origem a partir da origem e está a mapeia num campo de sink
  
## <a name="monitor-icons"></a>Ícones de monitor

Este ícone significa que os dados de transformação foi já colocado em cache no cluster, para que o caminho de execução e temporizações tem demorado que em consideração:

<img src="media/data-flow/mon005.png" width="800"> 

Também verá ícones do círculo verde na transformação. Eles representam uma contagem do número de Coletores de dados estão a ser encaminhados para.
