---
title: Seleção de recursos no processo de ciência de dados de equipe
description: Explica a finalidade da seleção de recursos e fornece exemplos de sua função no processo de aprimoramento de dados do aprendizado de máquina.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: da5da64538ceaf906388c49963c0d5115e1b5ab9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73480229"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Seleção de características no Team Data Science Process (TDSP)
Este artigo explica a finalidade da seleção de recursos e fornece exemplos de sua função no processo de aprimoramento de dados do aprendizado de máquina. Esses exemplos são desenhados a partir de Azure Machine Learning Studio.

A engenharia e a seleção de recursos são uma parte do TDSP (processo de ciência de dados de equipe) descrito no artigo [o que é o processo de ciência de dados de equipe?](overview.md). Engenharia e seleção de recursos são partes da etapa **desenvolver recursos** do TDSP.

* **engenharia de recursos**: esse processo tenta criar recursos relevantes adicionais a partir dos recursos brutos existentes nos dados e aumentar a capacidade de previsão para o algoritmo de aprendizado.
* **seleção de recursos**: esse processo seleciona o subconjunto de chaves dos recursos de dados originais em uma tentativa de reduzir a dimensionalidade do problema de treinamento.

Normalmente, a **engenharia de recursos** é aplicada primeiro para gerar recursos adicionais e, em seguida, a etapa de **seleção de recursos** é executada para eliminar recursos irrelevantes, redundantes ou altamente correlacionados.

## <a name="filter-features-from-your-data---feature-selection"></a>Filtrar recursos de seus dados-seleção de recursos
A seleção de recursos é um processo que normalmente é aplicado para a construção de conjuntos de de treinamento para tarefas de modelagem preditiva, como tarefas de classificação ou regressão. O objetivo é selecionar um subconjunto dos recursos do conjunto de dados original que reduzem suas dimensões usando um conjunto mínimo de recursos para representar a quantidade máxima de variações nos dados. Esse subconjunto de recursos é usado para treinar o modelo. A seleção de recursos atende a duas finalidades principais.

* Primeiro, a seleção de recursos geralmente aumenta a precisão da classificação eliminando recursos irrelevantes, redundantes ou altamente correlacionados.
* Em segundo lugar, ele diminui o número de recursos, o que torna o processo de treinamento do modelo mais eficiente. A eficiência é particularmente importante para os aprendizes que são caros de treinar, como máquinas de vetor de suporte.

Embora a seleção de recursos busque reduzir o número de recursos no conjunto de dado usado para treinar o modelo, ele não é referenciado pelo termo "redução de dimensionalidade". Os métodos de seleção de recursos extraem um subconjunto dos recursos originais nos dados sem alterá-los.  Os métodos de redução de dimensionalidade empregam recursos de engenharia que podem transformar os recursos originais e, portanto, modificá-los. Exemplos de métodos de redução de dimensionalidade incluem análise de componente principal, análise de correlação canônica e decomposição de valor singular.

Entre outros, uma categoria amplamente aplicada de métodos de seleção de recursos em um contexto supervisionado é chamada de "seleção de recursos baseada em filtro". Ao avaliar a correlação entre cada recurso e o atributo de destino, esses métodos aplicam uma medida estatística para atribuir uma pontuação a cada recurso. Os recursos são então classificados pela pontuação, que podem ser usados para ajudar a definir o limite para manter ou eliminar um recurso específico. Exemplos das medidas estatísticas usadas nesses métodos incluem correlação de pessoas, informações mútuas e o teste qui-quadrado.

Em Azure Machine Learning Studio, há módulos fornecidos para seleção de recursos. Conforme mostrado na figura a seguir, esses módulos incluem [seleção de recursos baseada em filtro][filter-based-feature-selection] e [análise de discriminante linear Fisher][fisher-linear-discriminant-analysis].

![Módulos de seleção de recursos](./media/select-features/feature-Selection.png)

Considere, por exemplo, o uso do módulo [seleção de recursos baseada em filtro][filter-based-feature-selection] . Para sua conveniência, continue usando o exemplo de mineração de texto. Suponha que você deseja criar um modelo de regressão depois que um conjunto de recursos 256 é criado por meio do módulo [hash de recurso][feature-hashing] e que a variável de resposta é a "Col1" que contém as classificações de análise de livro, variando de 1 a 5. Ao definir "método de Pontuação de recurso" como "correlação de Pearson", a "coluna de destino" como "Col1" e o "número de recursos desejados" para 50. Em seguida, a [seleção de recursos com base em filtro][filter-based-feature-selection] de módulo produz um conjunto de módulos contendo 50 recursos junto com o atributo de destino "Col1". A figura a seguir mostra o fluxo deste experimento e os parâmetros de entrada:

![Propriedades do módulo seleção de recursos baseada em filtro](./media/select-features/feature-Selection1.png)

A figura a seguir mostra os conjuntos de resultados resultantes:

![Conjunto de resultados resultante para o módulo seleção de recursos baseada em filtro](./media/select-features/feature-Selection2.png)

Cada recurso é pontuado com base na correlação de Pearson entre si mesmo e o atributo de destino "Col1". Os recursos com as principais pontuações são mantidos.

As pontuações correspondentes dos recursos selecionados são mostradas na figura a seguir:

![Pontuações do módulo seleção de recursos baseada em filtro](./media/select-features/feature-Selection3.png)

Ao aplicar esse módulo [seleção de recursos baseada em filtro][filter-based-feature-selection] , 50 de 256 são selecionados porque eles têm os recursos mais correlacionados com a variável de destino "Col1", com base no método de Pontuação "correlação de Pearson".

## <a name="conclusion"></a>Conclusão
A engenharia de recursos e a seleção de recursos são dois recursos mais projetados e selecionados aumentam a eficiência do processo de treinamento que tenta extrair as principais informações contidas nos dados. Eles também melhoram o poder desses modelos para classificar os dados de entrada com precisão e prever os resultados de interesse de forma mais robusta. A engenharia de recursos e a seleção também podem combinar para tornar o aprendizado mais computacionalmente manejável. Ele faz isso aprimorando e reduzindo o número de recursos necessários para calibrar ou treinar um modelo. Em termos de matemática, os recursos selecionados para treinar o modelo são um conjunto mínimo de variáveis independentes que explicam os padrões nos dados e, em seguida, prevêm os resultados com êxito.

Nem sempre é necessariamente executar a engenharia de recursos ou a seleção de recursos. Se é necessário ou não depende dos dados coletados, do algoritmo selecionado e do objetivo do experimento.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

