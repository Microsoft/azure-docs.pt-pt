---
title: Mapeando fluxo de dados Monitorização Visual
description: Como monitorizar visualmente os fluxos de dados da fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/17/2020
ms.openlocfilehash: 18099e853aa44e4434a14d7ea913f968593021ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687903"
---
# <a name="monitor-data-flows"></a>Monitorizar fluxos de dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Depois de ter concluído a construção e depurando o fluxo de dados, irá querer agendar o fluxo de dados para executar num horário no contexto de um pipeline. Pode agendar o oleoduto a partir da Azure Data Factory utilizando gatilhos. Ou pode utilizar a opção Trigger Now do Azure Data Factory Pipeline Builder para executar uma execução de uma única execução para testar o fluxo de dados dentro do contexto do gasoduto.

Quando executar o seu pipeline, poderá monitorizar o gasoduto e todas as atividades contidas no gasoduto, incluindo a atividade do Fluxo de Dados. Clique no ícone do monitor no painel UI da Fábrica de Dados Azure à esquerda. Verá um ecrã semelhante ao de baixo. Os ícones destacados permitirão perfurar as atividades no pipeline, incluindo a atividade do Fluxo de Dados.

![Monitorização do fluxo de dados](media/data-flow/mon001.png "Monitorização de Fluxo de Dados")

Verá estatísticas a este nível, incluindo os tempos de execução e o estado. O ID de execução ao nível da atividade é diferente do ID de execução ao nível do gasoduto. O ID de execução no nível anterior é para o gasoduto. Clicar nos óculos irá dar-lhe detalhes profundos sobre a execução do fluxo de dados.

![Monitorização do fluxo de dados](media/data-flow/mon002.png "Monitorização de Fluxo de Dados")

Quando estiver na vista gráfica de monitorização do nó, verá uma versão simplificada apenas do seu gráfico de fluxo de dados.

![Monitorização do fluxo de dados](media/data-flow/mon003.png "Monitorização de Fluxo de Dados")

Aqui está uma visão geral de vídeo da monitorização do desempenho dos seus dados flui do ecrã de monitorização ADF:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u4mH]

## <a name="view-data-flow-execution-plans"></a>Ver planos de execução de fluxo de dados

Quando o seu Fluxo de Dados é executado em Spark, a Azure Data Factory determina os melhores caminhos de código com base na totalidade do seu fluxo de dados. Além disso, os caminhos de execução podem ocorrer em diferentes nós de escala e divisórias de dados. Portanto, o gráfico de monitorização representa o design do seu fluxo, tendo em conta o caminho de execução das suas transformações. Quando clicar em nós individuais, verá "agrupamentos" que representam código que foi executado em conjunto no cluster. Os timings e contagens que você vê representam esses grupos em oposição aos passos individuais no seu projeto.

![Monitorização do fluxo de dados](media/data-flow/mon004.png "Monitorização de Fluxo de Dados")

* Quando clicar no espaço aberto na janela de monitorização, as estatísticas no painel inferior mostrarão o tempo e as contagens de linha para cada Sink e as transformações que levaram aos dados do lavatório para a linhagem de transformação.

* Ao selecionar transformações individuais, receberá feedback adicional no painel da direita que mostra estatísticas de partição, contagem de colunas, distorção (quão uniformemente são os dados distribuídos por divisórias) e kurtose (quão espinhoso são os dados).

* Quando clicar na pia na vista do nó, verá a linhagem da coluna. Existem três métodos diferentes que as colunas são acumuladas ao longo dos seus dados fluem para aterrar na Pia. São:

  * Computada: Utiliza a coluna para processamento condicional ou dentro de uma expressão no fluxo de dados, mas não a aterra no Lavatório
  * Derivado: A coluna é uma nova coluna que gerou no seu fluxo, ou seja, não estava presente na Fonte
  * Mapeado: A coluna originou-se da fonte e a sua está mapeando-a para um campo de afundar
  * Estado do fluxo de dados: O estado atual da sua execução
  * Tempo de arranque do cluster: Quantidade de tempo para adquirir o ambiente computacional JIT Spark para a sua execução de fluxo de dados
  * Número de transformações: Quantos passos de transformação estão a ser executados no seu fluxo
  
![Monitorização do fluxo de dados](media/data-flow/monitornew.png "Monitorização do fluxo de dados nova")  
  
## <a name="monitor-icons"></a>Ícones de monitor

Este ícone significa que os dados de transformação já estavam em cache no cluster, pelo que os timings e o caminho de execução tomaram isso em consideração:

![Monitorização do fluxo de dados](media/data-flow/mon004.png "Monitorização de Fluxo de Dados")

Você também verá ícones de círculo verde na transformação. Representam uma contagem do número de sumidouros em que os dados estão a fluir.
