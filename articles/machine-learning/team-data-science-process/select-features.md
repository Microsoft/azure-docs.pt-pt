---
title: Seleção de funcionalidades no Team Data Science Process
description: Explique a finalidade da seleção de funcionalidades e fornece exemplos da sua função no processo de aprimoramento de dados do machine learning.
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
ms.openlocfilehash: 1127a470a48660ffffa892d24c9f2991ec64c8e6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716678"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Seleção de características no Team Data Science Process (TDSP)
Este artigo explica os efeitos da seleção de funcionalidades e fornece exemplos de seu papel no processo de aprimoramento de dados do machine learning. Esses exemplos são obtidos a partir do Azure Machine Learning Studio.

A engenharia e a seleção de funcionalidades é uma parte do Team Data Science Process (TDSP) descritos no artigo [o que é o processo de ciência de dados de equipa?](overview.md). Engenharia de funcionalidades e seleção são partes do **desenvolver funcionalidades** passo do TDSP.

* **"Feature Engineering"** : Este processo tenta para criar as funcionalidades relevantes adicionais dos recursos existentes não processados nos dados e para aumentar o poder de previsão para o algoritmo de aprendizagem.
* **seleção de funcionalidades**: Este processo seleciona o subconjunto de chave dos recursos de dados original numa tentativa de reduzir a dimensionalidade do problema de treinamento.

Normalmente **com engenharia** é aplicada primeiro para gerar recursos adicionais e, em seguida, o **seleção de funcionalidades** passo é realizado para eliminar recursos irrelevantes, redundantes ou altamente correlacionados.

## <a name="filter-features-from-your-data---feature-selection"></a>Filtrar recursos dos seus dados - seleção de funcionalidades
A seleção de funcionalidades pode ser utilizada para tarefas de classificação ou regressão. O objetivo é selecionar um subconjunto das funcionalidades do conjunto de dados original que reduzir suas dimensões utilizando um conjunto mínimo de recursos para representar a quantidade máxima de variação nos dados. Este subconjunto de recursos é utilizado para preparar o modelo. Seleção de funcionalidades tem duas finalidades principais.

* Em primeiro lugar, a seleção de funcionalidades, muitas vezes, aumenta a precisão da classificação, eliminando irrelevantes e redundante ou altamente correlacionadas funcionalidades.
* Em segundo lugar, ela diminui o número de recursos, que torna o processo de treinamento de modelo mais eficientes. A eficiência é importante para os alunos que são caros para treinar, como máquinas vetoriais de suporte.

Embora a seleção de funcionalidades procuram reduzir o número de recursos no conjunto de dados usados para treinar o modelo, não é chamado do termo "redução de dimensionalidade". Métodos de seleção de funcionalidade extrair um subconjunto de recursos originais nos dados sem alterá-los.  Métodos de redução de dimensionalidade empregam funcionalidades de engenharia que podem transformar as funcionalidades originais e, portanto, modificá-los. Exemplos de métodos de redução de dimensionalidade incluem o Principal Componente de análise, a análise de correlação canonical e decomposição de valor único.

Entre outros, uma categoria amplamente aplicada dos métodos de seleção de funcionalidade num contexto supervisionado é chamada de "seleção de funcionalidades com base no filtro". Avaliando a correlação entre cada funcionalidade e o atributo de destino, esses métodos, aplicam-se uma medida de estatística para atribuir uma classificação para cada funcionalidade. As funcionalidades, em seguida, são ordenadas pela pontuação, que pode ser usada para ajudar a definir o limiar para manter ou eliminar um recurso específico. As medidas de estatísticas usadas nesses métodos exemplos de correlação, informações mútuas e o teste ao quadrado Chi pessoa.

No Azure Machine Learning Studio, há módulos fornecidos para a seleção de funcionalidades. Como mostra a seguinte figura, estes módulos incluem [a Seleção de Recursos Baseados][filter-based-feature-selection] em Filtros e a [Análise Discriminatória Linear do Fisher.][fisher-linear-discriminant-analysis]

![Módulos de seleção de recursos](./media/select-features/feature-Selection.png)

Considere, por exemplo, a utilização do módulo de [seleção de funcionalidades baseado][filter-based-feature-selection] no filtro. Para sua comodidade, continue a utilizar o exemplo de extração de texto. Assuma que pretende construir um modelo de regressão depois de um conjunto de 256 funcionalidades serem criadas através do módulo [Feature Hashing,][feature-hashing] e que a variável de resposta é a "Col1" que contém classificações de revisão de livros que variam de 1 a 5. Ao definir "Método de classificação de recursos" para ser "Pearson correlação", "coluna de destino" para ser "Col1" e o "número de recursos desejados" como 50. Em seguida, o módulo [Filter-Based Feature Selection][filter-based-feature-selection] produz um conjunto de dados contendo 50 funcionalidades juntamente com o atributo-alvo "Col1". A figura seguinte mostra o fluxo desta experiência e os parâmetros de entrada:

![Propriedades do módulo de seleção de funcionalidades baseada no filtro](./media/select-features/feature-Selection1.png)

A figura seguinte mostra os conjuntos de dados resultantes:

![Conjunto de dados resultante para o módulo de filtro com base a seleção de funcionalidades](./media/select-features/feature-Selection2.png)

Cada funcionalidade é classificada com base na correlação Pearson entre ele e o atributo de destino "Col1". Os recursos com pontuações principais são mantidos.

As pontuações correspondentes dos recursos selecionados são mostradas na figura a seguir:

![Notas de módulo de filtro com base a seleção de funcionalidades](./media/select-features/feature-Selection3.png)

Ao aplicar este módulo de [seleção de funcionalidades baseado][filter-based-feature-selection] em filtros, são selecionados 50 de 256 funcionalidades porque têm as características mais correlacionadas com a variável-alvo "Col1", com base no método de pontuação "Pearson Correlation".

## <a name="conclusion"></a>Conclusão
A engenharia de recursos e a seleção de funcionalidades são duas funcionalidades comumente concebidas e selecionadas aumentam a eficiência do processo de formação que tenta extrair a informação-chave contida nos dados. Eles também melhorem o poder desses modelos para classificar os dados de entrada com precisão e prever os resultados de interesse com mais robustez. Engenharia de funcionalidades e a seleção também podem combinar para tornar a aprendizagem mais recursos computacionais tractable. Ele faz isso, a melhorar e, em seguida, reduzindo o número de recursos necessários para calibrar ou preparar um modelo. Falando em matemática, dos recursos selecionados para preparar o modelo são um conjunto mínimo de variáveis independentes, que explicam os padrões nos dados e, em seguida, prever os resultados com êxito.

Nem sempre é necessariamente para efetuar a seleção de funcionalidade ou de engenharia de funcionalidades. Se for necessário ou não depende dos dados recolhidos, o algoritmo selecionado e o objetivo da experimentação.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

