---
title: Modelagem estágio do ciclo de vida do processo de ciência de dados de equipa
description: As metas, tarefas e resultados finais para a fase de modelagem dos seus projetos de ciência de dados
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d72d39a2a59e06954c36473083af2d2b4689a7b6
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538229"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>Modelagem estágio do ciclo de vida do processo de ciência de dados de equipa

Este artigo descreve os objetivos, tarefas e resultados associados a fase de modelagem de Team Data Science Process (TDSP). Este processo fornece um ciclo de vida recomendado que pode utilizar para estruturar os seus projetos de ciência de dados. O ciclo de vida destaca as fases principais projetos normalmente executadas, muitas vezes iterativamente:

   1. **Noções básicas sobre negócios**
   2. **Aquisição e compreensão de dados**
   3. **Mode**
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

  * **Engenharia de recursos**: Crie recursos de dados dos dados brutos para facilitar o treinamento do modelo.
  * **Treinamento de modelo**: Encontre o modelo que responde à pergunta com mais precisão, comparando suas métricas de sucesso.
  * Determine se seu modelo é **adequado para produção.**

### <a name="feature-engineering"></a>Com engenharia
Engenharia de funcionalidades envolve a inclusão, a agregação e a transformação de variáveis não processadas para criar os recursos utilizados na análise. Se quiser informações sobre o que está orientando um modelo, terá de compreender como os recursos se relacionam entre si e como os algoritmos de machine learning devem usar esses recursos. 

Este passo requer uma combinação criativa de experiência de domínio e as informações obtidas a partir do passo de exploração de dados. Engenharia de funcionalidades é um ato de equilíbrio de localizar e variáveis informativas, incluindo, mas ao mesmo tempo tentando evitar muitas variáveis não relacionadas. Variáveis informativas melhorar seu resultado; variáveis não relacionadas introduzem ruído desnecessário para o modelo. Também terá de gerar esses recursos para quaisquer novos dados obtidos durante de classificação. Como resultado, a geração desses recursos só pode depender de dados que estão disponíveis no momento da classificação. 

Para obter orientações técnicas sobre a engenharia de recursos ao usar várias tecnologias de dados do Azure, consulte [engenharia de recursos no processo de ciência de dados](create-features.md). 

### <a name="model-training"></a>Preparação de modelos
Dependendo do tipo de pergunta que está a tentar responder, há muitos algoritmos de modelagem disponíveis. Para obter orientação sobre como escolher os algoritmos, consulte [como escolher algoritmos para Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Embora este artigo utiliza o Azure Machine Learning, a documentação de orientação fornece é útil para todos os projetos machine learning. 

O processo de preparação de modelos inclui os seguintes passos: 

   * **Divida os dados de entrada** aleatoriamente para modelagem em um conjunto de dados de treinamento e um conjunto de dados de teste.
   * **Crie os modelos** usando o conjunto de dados de treinamento.
   * **Avalie** o treinamento e o conjunto de dados de teste. Use uma série de algoritmos de aprendizado de máquina concorrentes juntamente com os vários parâmetros de ajuste associados (conhecidos como *limpeza de parâmetro*) que são voltados para responder à pergunta de interesse com os dados atuais.
   * **Determine a "melhor" solução** para responder à pergunta, comparando as métricas de sucesso entre os métodos alternativos.

> [!NOTE]
> **Evitar vazamento**: você pode causar vazamento de dados se incluir dados de fora do conjunto de dados de treinamento que permite que um modelo ou algoritmo de aprendizado de máquina faça previsões inrealísticamente boas. Fuga é um motivo comum, por que dados cientistas obtém nervoso só quando recebem preditivos resultados que parecem muito boas para ser verdade. Estas dependências podem ser difíceis de detetar. Para evitar a fuga de muitas vezes requer a iteração entre a criação de um conjunto de dados de análise, criação de um modelo e avaliar a precisão dos resultados. 
> 
> 

Fornecemos uma [ferramenta automatizada de modelagem e emissão de relatórios](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) com TDSP que é capaz de executar vários algoritmos e limpezas de parâmetros para produzir um modelo de linha de base. Também produz uma relatório que resume o desempenho de cada combinação de modelo e parâmetros, incluindo a importância de variável de modelagem de linha de base. Este processo também é iterativo, como pode promover a engenharia de funcionalidades adicional. 

## <a name="artifacts"></a>Artefactos
Os artefactos produzidos nesse estágio incluem:

   * [Conjuntos de recursos](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md): os recursos desenvolvidos para a modelagem são descritos na seção **conjuntos de recursos** do relatório definição de **dados** . Ela contém ponteiros para o código para gerar os recursos e uma descrição de como o recurso foi gerado.
   * [Relatório de modelo](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): para cada modelo tentado, um relatório padrão baseado em modelo que fornece detalhes sobre cada experimento é produzido.
   * **Decisão do ponto de verificação**: avalie se o modelo executa bem o suficiente para implantá-lo em um sistema de produção. Seguem-se algumas perguntas importantes para perguntar:
     * O modelo de responder à pergunta com confiança suficiente, tendo em conta os dados de teste? 
     * Se tentasse qualquer abordagens alternativas? Deve recolher dados adicionais, fazer a engenharia de funcionalidades mais ou experimentar outros algoritmos?

## <a name="next-steps"></a>Passos seguintes

Seguem-se ligações para cada etapa do ciclo de vida do TDSP:

   1. [Noções básicas sobre negócios](lifecycle-business-understanding.md)
   2. [Aquisição e compreensão de dados](lifecycle-data.md)
   3. [Mode](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Fornecemos orientações passo a passo de completa-a-ponto, que demonstram todas as etapas do processo para cenários específicos. O artigo [passo a passos de exemplo](walkthroughs.md) fornece uma lista dos cenários com descrições de miniaturas e links. A instruções passo a passo mostram como combinar a cloud, ferramentas no local e serviços num fluxo de trabalho ou um pipeline para criar uma aplicação inteligente. 

Para obter exemplos de como executar etapas em TDSPs que usam Azure Machine Learning Studio, consulte [usar o TDSP com Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/). 
