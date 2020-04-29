---
title: Fase de modelação do ciclo de vida do Processo de Ciência de Dados da Equipa
description: Os objetivos, tarefas e entregas para a fase de modelação dos seus projetos de ciência de dados
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76720474"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>Fase de modelação do ciclo de vida do Processo de Ciência de Dados da Equipa

Este artigo descreve os objetivos, tarefas e entregas associados à fase de modelação do Processo de Ciência de Dados da Equipa (TDSP). Este processo fornece um ciclo de vida recomendado que pode usar para estruturar os seus projetos de ciência de dados. O ciclo de vida descreve as principais etapas que os projetos normalmente executam, muitas vezes iterativamente:

   1. **Noções sobre empresas**
   2. **Aquisição e compreensão de dados**
   3. **Modelação**
   4. **Implementação**
   5. **Aceitação do cliente**

Aqui está uma representação visual do ciclo de vida da TDSP:

![Ciclo de vida TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objetivos
* Determine as funcionalidades de dados ideais para o modelo de aprendizagem automática.
* Crie um modelo informativo de aprendizagem automática que preveja o alvo com mais precisão.
* Crie um modelo de aprendizagem automática adequado para a produção.

## <a name="how-to-do-it"></a>Como fazê-lo
Há três tarefas principais abordadas nesta fase:

  * **Engenharia de recursos**: Criar funcionalidades a partir dos dados brutos para facilitar a formação de modelos.
  * **Formação de modelos**: Encontre o modelo que responde à pergunta com mais precisão comparando as suas métricas de sucesso.
  * Determine se o seu modelo é **adequado para a produção.**

### <a name="feature-engineering"></a>Com engenharia
A engenharia de recursos envolve a inclusão, agregação e transformação de variáveis cruas para criar as características usadas na análise. Se quer saber o que está a conduzir um modelo, então precisa entender como as funcionalidades se relacionam entre si e como os algoritmos de aprendizagem automática são para usar essas funcionalidades. 

Este passo requer uma combinação criativa de conhecimentos de domínio e os insights obtidos a partir do passo de exploração de dados. A engenharia de recursos é um ato de equilíbrio de encontrar e incluir variáveis informativas, mas ao mesmo tempo tentar evitar demasiadas variáveis não relacionadas. As variáveis informativas melhoram o seu resultado; Variáveis não relacionadas introduzem ruído desnecessário no modelo. Também precisa de gerar estas funcionalidades para quaisquer novos dados obtidos durante a pontuação. Como resultado, a geração destas funcionalidades só pode depender de dados disponíveis no momento da pontuação. 

Para orientação técnica sobre engenharia de recursos quando utilizar várias tecnologias de dados Azure, consulte a [engenharia de funcionalidades no processo](create-features.md)de ciência de dados . 

### <a name="model-training"></a>Preparação de modelos
Dependendo do tipo de pergunta que está a tentar responder, existem muitos algoritmos de modelação disponíveis. Para obter orientações sobre a escolha dos algoritmos, consulte [Como escolher algoritmos para o Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Embora este artigo utilize o Azure Machine Learning, a orientação que fornece é útil para quaisquer projetos de aprendizagem automática. 

O processo de formação de modelos inclui os seguintes passos: 

   * **Divida os dados** de entrada aleatoriamente para modelar um conjunto de dados de treino e um conjunto de dados de teste.
   * **Construa os modelos** utilizando o conjunto de dados de treino.
   * **Avalie** o conjunto de dados de treino e teste. Utilize uma série de algoritmos concorrentes de aprendizagem automática juntamente com os vários parâmetros de afinação associados (conhecidos como uma varredura de *parâmetros)* que estão orientados para responder à questão do interesse com os dados atuais.
   * **Determine a solução "melhor"** para responder à pergunta comparando as métricas de sucesso entre métodos alternativos.

> [!NOTE]
> **Evite fugas**: Pode causar fugas de dados se incluir dados de fora do conjunto de dados de treino que permite que um modelo ou algoritmo de aprendizagem automática faça previsões irrealistas. A fuga é uma razão comum para os cientistas de dados ficarem nervosos quando obtêm resultados preditivos que parecem demasiado bons para serem verdade. Estas dependências podem ser difíceis de detetar. Para evitar fugas muitas vezes requer iteração entre a construção de um conjunto de dados de análise, a criação de um modelo e a avaliação da precisão dos resultados. 
> 
> 

Fornecemos uma ferramenta automatizada de [modelação e reporte](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) com TDSP que é capaz de executar através de vários algoritmos e varreduras de parâmetros para produzir um modelo de base. Também produz um relatório de modelação de base que resume o desempenho de cada modelo e combinação de parâmetros, incluindo a importância variável. Este processo também é iterativo, pois pode impulsionar mais engenharia de recursos. 

## <a name="artifacts"></a>Artefactos
Os artefactos produzidos nesta fase incluem:

   * [Conjuntos de funcionalidades](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md): As funcionalidades desenvolvidas para a modelação são descritas na secção de conjuntos de **funcionalidades** do relatório de definição de **Dados.** Contém indicações para o código para gerar as funcionalidades e uma descrição de como a funcionalidade foi gerada.
   * [Relatório do modelo](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): Para cada modelo que é experimentado, um relatório padrão baseado em modelos que fornece detalhes sobre cada experiência é produzido.
   * **Decisão do checkpoint**: Avaliar se o modelo funciona o suficiente para a produção. Algumas perguntas-chave a fazer são:
     * O modelo responde à pergunta com confiança suficiente dado os dados do teste? 
     * Deve tentar alguma abordagem alternativa? Deve recolher dados adicionais, fazer mais engenharia de funcionalidades ou experimentar outros algoritmos?

## <a name="next-steps"></a>Passos seguintes

Aqui estão os links para cada passo no ciclo de vida do TDSP:

   1. [Noções sobre empresas](lifecycle-business-understanding.md)
   2. [Aquisição e compreensão de dados](lifecycle-data.md)
   3. [Modelação](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Fornecemos walkthroughs completos de ponta a ponta que demonstram todos os passos no processo para cenários específicos. O artigo de [walkthroughs Exemplo](walkthroughs.md) fornece uma lista dos cenários com links e descrições de miniaturas. Os passadiços ilustram como combinar cloud, ferramentas no local e serviços em um fluxo de trabalho ou pipeline para criar uma aplicação inteligente. 

Por exemplo, como executar passos em TDSPs que usam o Estúdio de Aprendizagem automática Azure, consulte [Utilize o TDSP com aprendizagem automática azure](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/). 
