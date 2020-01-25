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

   1. **Compreensão empresarial**
   2. **Aquisição de dados e compreensão**
   3. **Modelagem**
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

  * **"Feature Engineering"** : criar recursos de dados a partir dos dados não processados para facilitar a preparação de modelos.
  * **Treinamento de modelo**: localizar o modelo que responde a pergunta com mais precisão ao comparar as suas métricas de sucesso.
  * Determinar se o seu modelo fica **adequado para produção.**

### <a name="feature-engineering"></a>Com engenharia
Engenharia de funcionalidades envolve a inclusão, a agregação e a transformação de variáveis não processadas para criar os recursos utilizados na análise. Se quiser informações sobre o que está orientando um modelo, terá de compreender como os recursos se relacionam entre si e como os algoritmos de machine learning devem usar esses recursos. 

Este passo requer uma combinação criativa de experiência de domínio e as informações obtidas a partir do passo de exploração de dados. Engenharia de funcionalidades é um ato de equilíbrio de localizar e variáveis informativas, incluindo, mas ao mesmo tempo tentando evitar muitas variáveis não relacionadas. Variáveis informativas melhorar seu resultado; variáveis não relacionadas introduzem ruído desnecessário para o modelo. Também terá de gerar esses recursos para quaisquer novos dados obtidos durante de classificação. Como resultado, a geração desses recursos só pode depender de dados que estão disponíveis no momento da classificação. 

Para obter orientação técnica sobre a funcionalidade de engenharia quando fazer várias tecnologias de dados do Azure, consulte ["Feature Engineering" no processo de ciência de dados](create-features.md). 

### <a name="model-training"></a>Preparação de modelos
Dependendo do tipo de pergunta que está a tentar responder, há muitos algoritmos de modelagem disponíveis. Para obter orientações sobre como escolher os algoritmos, consulte [como escolher algoritmos para o Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Embora este artigo utiliza o Azure Machine Learning, a documentação de orientação fornece é útil para todos os projetos machine learning. 

O processo de preparação de modelos inclui os seguintes passos: 

   * **Dividir os dados de entrada** aleatoriamente para a Modelagem num conjunto de dados de treinamento e um conjunto de dados de teste.
   * **Criar os modelos** ao utilizar o conjunto de dados de treinamento.
   * **Avaliar** a formação e o conjunto de dados de teste. Usar uma série de algoritmos de machine learning concorrentes, juntamente com os vários parâmetros de ajuste associados (conhecido como um *varrimentos*) que são adaptadas na direção de responder à pergunta de interesse com os dados atuais.
   * **Determinar a solução de "melhor"** para responder à pergunta ao comparar as métricas de sucesso entre métodos alternativos.

> [!NOTE]
> **Evitar a fuga**: pode causar vazamento de dados se incluir dados a partir de fora do conjunto de dados de treinamento que permite a um modelo ou o algoritmo de aprendizagem automática para fazer previsões inacreditavelmente boas. Fuga é um motivo comum, por que dados cientistas obtém nervoso só quando recebem preditivos resultados que parecem muito boas para ser verdade. Estas dependências podem ser difíceis de detetar. Para evitar a fuga de muitas vezes requer a iteração entre a criação de um conjunto de dados de análise, criação de um modelo e avaliar a precisão dos resultados. 
> 
> 

Fornecemos uma [automatizada de modelagem e ferramenta de relatório](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) com TDSP que pode ser executada por meio de vários algoritmos e parâmetros largas para produzir um modelo de linha de base. Também produz uma relatório que resume o desempenho de cada combinação de modelo e parâmetros, incluindo a importância de variável de modelagem de linha de base. Este processo também é iterativo, como pode promover a engenharia de funcionalidades adicional. 

## <a name="artifacts"></a>Artefactos
Os artefactos produzidos nesse estágio incluem:

   * [Conjuntos de recursos](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md): os recursos desenvolvidos para a Modelagem são descritos no **conjuntos de recursos** seção o **definição de dados** relatório. Ela contém ponteiros para o código para gerar os recursos e uma descrição de como o recurso foi gerado.
   * [Relatório de modelos](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): para cada modelo é experimentado uma norma de relatório baseado em modelo, que fornece detalhes sobre cada experimentação é produzido.
   * **Decisão do checkpoint**: Avaliar se o modelo funciona o suficiente para a produção. Seguem-se algumas perguntas importantes para perguntar:
     * O modelo de responder à pergunta com confiança suficiente, tendo em conta os dados de teste? 
     * Se tentasse qualquer abordagens alternativas? Deve recolher dados adicionais, fazer a engenharia de funcionalidades mais ou experimentar outros algoritmos?

## <a name="next-steps"></a>Passos seguintes

Seguem-se ligações para cada etapa do ciclo de vida do TDSP:

   1. [Compreensão empresarial](lifecycle-business-understanding.md)
   2. [Aquisição de dados e compreensão](lifecycle-data.md)
   3. [Modelagem](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Fornecemos orientações passo a passo de completa-a-ponto, que demonstram todas as etapas do processo para cenários específicos. O [instruções passo a passo do exemplo](walkthroughs.md) artigo fornece uma lista dos cenários com links e descrições em miniatura. A instruções passo a passo mostram como combinar a cloud, ferramentas no local e serviços num fluxo de trabalho ou um pipeline para criar uma aplicação inteligente. 

Para obter exemplos de como executar os passos no TDSPs que utilizam o Azure Machine Learning Studio, consulte [utilizar o TDSP com o Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/). 
