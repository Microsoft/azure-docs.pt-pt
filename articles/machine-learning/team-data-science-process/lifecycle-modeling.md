---
title: Modelagem estágio do ciclo de vida do processo de ciência de dados de equipa
description: As metas, tarefas e resultados finais para a fase de modelagem dos seus projetos de ciência de dados
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1d3cd61ea3da88c4c5231f22c0e127508591fb8d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720474"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>Modelagem estágio do ciclo de vida do processo de ciência de dados de equipa

Este artigo descreve os objetivos, tarefas e resultados associados a fase de modelagem de Team Data Science Process (TDSP). Este processo fornece um ciclo de vida recomendado que pode utilizar para estruturar os seus projetos de ciência de dados. O ciclo de vida destaca as fases principais projetos normalmente executadas, muitas vezes iterativamente:

   1. **Compreensão de negócios**
   2. **Aquisição e compreensão de dados**
   3. **Modelação**
   4. **Implementação**
   5. **Aceitação do cliente**

Esta é uma representação visual de ciclo de vida do TDSP:

![Ciclo de vida do TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objetivos
* Determine os recursos de dados ideal para o modelo de aprendizagem automática.
* Crie um modelo de machine learning informativo que prevê o destino com mais precisão.
* Crie um modelo de machine learning, que é adequado para produção.

## <a name="how-to-do-it"></a>Como fazê-lo
Existem três tarefas principais abordadas neste estágio:

  * **Engenharia de recursos**: Criar funcionalidades a partir dos dados brutos para facilitar a formação de modelos.
  * **Formação de modelos**: Encontre o modelo que responde à pergunta com mais precisão comparando as suas métricas de sucesso.
  * Determine se o seu modelo é **adequado para a produção.**

### <a name="feature-engineering"></a>Com engenharia
Engenharia de funcionalidades envolve a inclusão, a agregação e a transformação de variáveis não processadas para criar os recursos utilizados na análise. Se quiser informações sobre o que está orientando um modelo, terá de compreender como os recursos se relacionam entre si e como os algoritmos de machine learning devem usar esses recursos. 

Este passo requer uma combinação criativa de experiência de domínio e as informações obtidas a partir do passo de exploração de dados. Engenharia de funcionalidades é um ato de equilíbrio de localizar e variáveis informativas, incluindo, mas ao mesmo tempo tentando evitar muitas variáveis não relacionadas. Variáveis informativas melhorar seu resultado; variáveis não relacionadas introduzem ruído desnecessário para o modelo. Também terá de gerar esses recursos para quaisquer novos dados obtidos durante de classificação. Como resultado, a geração desses recursos só pode depender de dados que estão disponíveis no momento da classificação. 

Para orientação técnica sobre engenharia de recursos quando utilizar várias tecnologias de dados Azure, consulte a [engenharia de funcionalidades no processo](create-features.md)de ciência de dados . 

### <a name="model-training"></a>Preparação de modelos
Dependendo do tipo de pergunta que está a tentar responder, há muitos algoritmos de modelagem disponíveis. Para obter orientações sobre a escolha dos algoritmos, consulte [Como escolher algoritmos para o Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Embora este artigo utiliza o Azure Machine Learning, a documentação de orientação fornece é útil para todos os projetos machine learning. 

O processo de preparação de modelos inclui os seguintes passos: 

   * **Divida os dados** de entrada aleatoriamente para modelar um conjunto de dados de treino e um conjunto de dados de teste.
   * **Construa os modelos** utilizando o conjunto de dados de treino.
   * **Avalie** o conjunto de dados de treino e teste. Utilize uma série de algoritmos concorrentes de aprendizagem automática juntamente com os vários parâmetros de afinação associados (conhecidos como uma varredura de *parâmetros)* que estão orientados para responder à questão do interesse com os dados atuais.
   * **Determine a solução "melhor"** para responder à pergunta comparando as métricas de sucesso entre métodos alternativos.

> [!NOTE]
> **Evite fugas**: Pode causar fugas de dados se incluir dados de fora do conjunto de dados de treino que permite que um modelo ou algoritmo de aprendizagem automática faça previsões irrealistas. Fuga é um motivo comum, por que dados cientistas obtém nervoso só quando recebem preditivos resultados que parecem muito boas para ser verdade. Estas dependências podem ser difíceis de detetar. Para evitar a fuga de muitas vezes requer a iteração entre a criação de um conjunto de dados de análise, criação de um modelo e avaliar a precisão dos resultados. 
> 
> 

Fornecemos uma ferramenta automatizada de [modelação e reporte](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) com TDSP que é capaz de executar através de vários algoritmos e varreduras de parâmetros para produzir um modelo de base. Também produz uma relatório que resume o desempenho de cada combinação de modelo e parâmetros, incluindo a importância de variável de modelagem de linha de base. Este processo também é iterativo, como pode promover a engenharia de funcionalidades adicional. 

## <a name="artifacts"></a>Artefactos
Os artefactos produzidos nesse estágio incluem:

   * [Conjuntos de funcionalidades](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md): As funcionalidades desenvolvidas para a modelação são descritas na secção de conjuntos de **funcionalidades** do relatório de definição de **Dados.** Ela contém ponteiros para o código para gerar os recursos e uma descrição de como o recurso foi gerado.
   * [Relatório do modelo](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): Para cada modelo que é experimentado, um relatório padrão baseado em modelos que fornece detalhes sobre cada experiência é produzido.
   * **Decisão do checkpoint**: Avaliar se o modelo funciona o suficiente para a produção. Seguem-se algumas perguntas importantes para perguntar:
     * O modelo de responder à pergunta com confiança suficiente, tendo em conta os dados de teste? 
     * Se tentasse qualquer abordagens alternativas? Deve recolher dados adicionais, fazer a engenharia de funcionalidades mais ou experimentar outros algoritmos?

## <a name="next-steps"></a>Passos Seguintes

Seguem-se ligações para cada etapa do ciclo de vida do TDSP:

   1. [Compreensão de negócios](lifecycle-business-understanding.md)
   2. [Aquisição e compreensão de dados](lifecycle-data.md)
   3. [Modelação](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Fornecemos orientações passo a passo de completa-a-ponto, que demonstram todas as etapas do processo para cenários específicos. O artigo de [walkthroughs Exemplo](walkthroughs.md) fornece uma lista dos cenários com links e descrições de miniaturas. A instruções passo a passo mostram como combinar a cloud, ferramentas no local e serviços num fluxo de trabalho ou um pipeline para criar uma aplicação inteligente. 

Por exemplo, como executar passos em TDSPs que usam o Estúdio de Aprendizagem automática Azure, consulte [Utilize o TDSP com aprendizagem automática azure](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/). 
