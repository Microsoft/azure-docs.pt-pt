---
title: Estágio de modelagem do ciclo de vida do processo de ciência de dados de equipe
description: As metas, as tarefas e os resultados finais para o estágio de modelagem de seus projetos de ciência de dados
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
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>Estágio de modelagem do ciclo de vida do processo de ciência de dados de equipe

Este artigo descreve as metas, as tarefas e os resultados associados ao estágio de modelagem do TDSP (processo de ciência de dados de equipe). Esse processo fornece um ciclo de vida recomendado que você pode usar para estruturar seus projetos de ciência de dados. O ciclo de vida descreve os principais estágios que os projetos normalmente executam, muitas vezes iterativamente:

   1. **Noções básicas sobre negócios**
   2. **Aquisição e compreensão de dados**
   3. **Mode**
   4. **Implementação**
   5. **Aceitação do cliente**

Aqui está uma representação visual do ciclo de vida do TDSP:

![Ciclo de vida do TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Atingir
* Determine os recursos de dados ideais para o modelo de aprendizado de máquina.
* Crie um modelo de aprendizado de máquina informativo que prevê o destino com mais precisão.
* Crie um modelo de aprendizado de máquina adequado para produção.

## <a name="how-to-do-it"></a>Como fazer isso
Há três tarefas principais abordadas neste estágio:

  * **Engenharia de recursos**: Crie recursos de dados dos dados brutos para facilitar o treinamento do modelo.
  * **Treinamento de modelo**: Encontre o modelo que responde à pergunta com mais precisão, comparando suas métricas de sucesso.
  * Determine se seu modelo é **adequado para produção.**

### <a name="feature-engineering"></a>Com engenharia
A engenharia de recursos envolve a inclusão, agregação e transformação de variáveis brutas para criar os recursos usados na análise. Se você quiser obter informações sobre o que está orientando um modelo, precisará entender como os recursos estão relacionados entre si e como os algoritmos de aprendizado de máquina devem usar esses recursos. 

Esta etapa requer uma combinação criativa de experiência de domínio e as informações obtidas na etapa de exploração de dados. A engenharia de recursos é um ato de balanceamento de localização e inclusão de variáveis informativas, mas ao mesmo tempo tentando evitar muitas variáveis não relacionadas. Variáveis informativas melhoram seu resultado; variáveis não relacionadas introduzem ruído desnecessário no modelo. Você também precisa gerar esses recursos para novos dados obtidos durante a pontuação. Como resultado, a geração desses recursos só pode depender de dados disponíveis no momento da pontuação. 

Para obter orientações técnicas sobre a engenharia de recursos ao usar várias tecnologias de dados do Azure, consulte [engenharia de recursos no processo de ciência de dados](create-features.md). 

### <a name="model-training"></a>Preparação de modelos
Dependendo do tipo de pergunta que você está tentando responder, há muitos algoritmos de modelagem disponíveis. Para obter orientação sobre como escolher os algoritmos, consulte [como escolher algoritmos para Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Embora este artigo use Azure Machine Learning, a orientação que ele fornece é útil para qualquer projeto de aprendizado de máquina. 

O processo de treinamento do modelo inclui as seguintes etapas: 

   * **Divida os dados de entrada** aleatoriamente para modelagem em um conjunto de dados de treinamento e um conjunto de dados de teste.
   * **Crie os modelos** usando o conjunto de dados de treinamento.
   * **Avalie** o treinamento e o conjunto de dados de teste. Use uma série de algoritmos de aprendizado de máquina concorrentes juntamente com os vários parâmetros de ajuste associados (conhecidos como *limpeza de parâmetro*) que são voltados para responder à pergunta de interesse com os dados atuais.
   * **Determine a "melhor" solução** para responder à pergunta, comparando as métricas de sucesso entre os métodos alternativos.

> [!NOTE]
> **Evitar vazamento**: você pode causar vazamento de dados se incluir dados de fora do conjunto de dados de treinamento que permite que um modelo ou algoritmo de aprendizado de máquina faça previsões inrealísticamente boas. O vazamento é um motivo comum pelo qual os cientistas de dados ficam preocupadoss quando eles obtêm resultados previsíveis que parecem muito bons para serem verdadeiros. Essas dependências podem ser difíceis de detectar. Para evitar o vazamento geralmente, é necessário iterar entre a criação de um conjunto de dados de análise, a criação de um modelo e a avaliação da precisão dos resultados. 
> 
> 

Fornecemos uma [ferramenta automatizada de modelagem e emissão de relatórios](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) com TDSP que é capaz de executar vários algoritmos e limpezas de parâmetros para produzir um modelo de linha de base. Ele também produz um relatório de modelagem de linha de base que resume o desempenho de cada modelo e combinação de parâmetros, incluindo importância variável. Esse processo também é iterativo, pois pode gerar mais engenharia de recursos. 

## <a name="artifacts"></a>Artefactos
Os artefatos produzidos neste estágio incluem:

   * [Conjuntos de recursos](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md): os recursos desenvolvidos para a modelagem são descritos na seção **conjuntos de recursos** do relatório definição de **dados** . Ele contém ponteiros para o código para gerar os recursos e uma descrição de como o recurso foi gerado.
   * [Relatório de modelo](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): para cada modelo tentado, um relatório padrão baseado em modelo que fornece detalhes sobre cada experimento é produzido.
   * **Decisão do ponto de verificação**: avalie se o modelo executa bem o suficiente para implantá-lo em um sistema de produção. Algumas perguntas importantes a serem feitas são:
     * O modelo responde à pergunta com confiança suficiente considerando os dados de teste? 
     * Você deve tentar qualquer abordagem alternativa? Você deve coletar dados adicionais, fazer mais engenharia de recursos ou experimentar outros algoritmos?

## <a name="next-steps"></a>Passos seguintes

Aqui estão os links para cada etapa no ciclo de vida do TDSP:

   1. [Noções básicas sobre negócios](lifecycle-business-understanding.md)
   2. [Aquisição e compreensão de dados](lifecycle-data.md)
   3. [Mode](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Fornecemos orientações completas de ponta a ponta que demonstram todas as etapas no processo para cenários específicos. O artigo [passo a passos de exemplo](walkthroughs.md) fornece uma lista dos cenários com descrições de miniaturas e links. As orientações ilustram como combinar a nuvem, as ferramentas locais e os serviços em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente. 

Para obter exemplos de como executar etapas em TDSPs que usam Azure Machine Learning Studio, consulte [usar o TDSP com Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/). 
