---
title: Seleção de funcionalidades no Team Data Science Process
description: Explique a finalidade da seleção de funcionalidades e fornece exemplos da sua função no processo de aprimoramento de dados do machine learning.
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
ms.openlocfilehash: a74f2c21746deb16372174d4a769f9abb825a1cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60809589"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Seleção de características no Team Data Science Process (TDSP)
Este artigo explica os efeitos da seleção de funcionalidades e fornece exemplos de seu papel no processo de aprimoramento de dados do machine learning. Esses exemplos são obtidos a partir do Azure Machine Learning Studio. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

A engenharia e a seleção de funcionalidades é uma parte do Team Data Science Process (TDSP) descritos no artigo [o que é o processo de ciência de dados de equipa?](overview.md). Engenharia de funcionalidades e seleção são partes do **desenvolver funcionalidades** passo do TDSP.

* **"Feature Engineering"** : Este processo tenta para criar as funcionalidades relevantes adicionais dos recursos existentes não processados nos dados e para aumentar o poder de previsão para o algoritmo de aprendizagem.
* **seleção de funcionalidades**: Este processo seleciona o subconjunto de chave dos recursos de dados original numa tentativa de reduzir a dimensionalidade do problema de treinamento.

Normalmente **com engenharia** é aplicada primeiro para gerar recursos adicionais e, em seguida, o **seleção de funcionalidades** passo é realizado para eliminar recursos irrelevantes, redundantes ou altamente correlacionados.

## <a name="filter-features-from-your-data---feature-selection"></a>Filtrar recursos dos seus dados - seleção de funcionalidades
Seleção de funcionalidades é um processo que normalmente é aplicado para a construção de conjuntos de dados de treinamento para tarefas de modelação preditiva como tarefas de classificação ou regressão. O objetivo é selecionar um subconjunto das funcionalidades do conjunto de dados original que reduzir suas dimensões utilizando um conjunto mínimo de recursos para representar a quantidade máxima de variação nos dados. Este subconjunto de recursos é utilizado para preparar o modelo. Seleção de funcionalidades tem duas finalidades principais.

* Em primeiro lugar, a seleção de funcionalidades, muitas vezes, aumenta a precisão da classificação, eliminando irrelevantes e redundante ou altamente correlacionadas funcionalidades.
* Em segundo lugar, ela diminui o número de recursos, que torna o processo de treinamento de modelo mais eficientes. Eficiência é particularmente importante para os aprendizes que sejam dispendiosos preparar, como máquinas de vetores de suporte.

Embora a seleção de funcionalidades procuram reduzir o número de recursos no conjunto de dados usados para treinar o modelo, não é chamado do termo "redução de dimensionalidade". Métodos de seleção de funcionalidade extrair um subconjunto de recursos originais nos dados sem alterá-los.  Métodos de redução de dimensionalidade empregam funcionalidades de engenharia que podem transformar as funcionalidades originais e, portanto, modificá-los. Exemplos de métodos de redução de dimensionalidade incluem o Principal Componente de análise, a análise de correlação canonical e decomposição de valor único.

Entre outros, uma categoria amplamente aplicada dos métodos de seleção de funcionalidade num contexto supervisionado é chamada de "seleção de funcionalidades com base no filtro". Avaliando a correlação entre cada funcionalidade e o atributo de destino, esses métodos, aplicam-se uma medida de estatística para atribuir uma classificação para cada funcionalidade. As funcionalidades, em seguida, são ordenadas pela pontuação, que pode ser usada para ajudar a definir o limiar para manter ou eliminar um recurso específico. As medidas de estatísticas usadas nesses métodos exemplos de correlação, informações mútuas e o teste ao quadrado Chi pessoa.

No Azure Machine Learning Studio, há módulos fornecidos para a seleção de funcionalidades. Conforme mostrado na figura a seguir, estes módulos incluem [seleção de funcionalidades com base no filtro] [ filter-based-feature-selection] e [análise de discriminador Linear Fisher] [ fisher-linear-discriminant-analysis].

![Módulos de seleção de recursos](./media/select-features/feature-Selection.png)

Considere, por exemplo, o uso do [seleção de funcionalidades com base no filtro] [ filter-based-feature-selection] módulo. Para sua comodidade, continue a utilizar o exemplo de extração de texto. Partem do princípio de que deseja criar um modelo de regressão, depois de um conjunto de 256 recursos forem criadas com o [funcionalidade hash] [ feature-hashing] módulo e de que a variável de resposta é o "Col1" que contém as classificações de revisão do livro entre 1 e 5. Ao definir "Método de classificação de recursos" para ser "Pearson correlação", "coluna de destino" para ser "Col1" e o "número de recursos desejados" como 50. Em seguida, o módulo [seleção de funcionalidades com base no filtro] [ filter-based-feature-selection] produz um conjunto de dados que contêm 50 recursos em conjunto com o atributo de destino "Col1". A figura seguinte mostra o fluxo desta experiência e os parâmetros de entrada:

![Filtrar propriedades do módulo de seleção de funcionalidades com base em](./media/select-features/feature-Selection1.png)

A figura seguinte mostra os conjuntos de dados resultantes:

![Conjunto de dados resultante para o módulo de filtro com base a seleção de funcionalidades](./media/select-features/feature-Selection2.png)

Cada funcionalidade é classificada com base na correlação Pearson entre ele e o atributo de destino "Col1". Os recursos com pontuações principais são mantidos.

As pontuações correspondentes dos recursos selecionados são mostradas na figura a seguir:

![Notas de módulo de filtro com base a seleção de funcionalidades](./media/select-features/feature-Selection3.png)

Ao aplicar isso [seleção de funcionalidades com base no filtro] [ filter-based-feature-selection] módulo, 50 de 256 funcionalidades estão selecionadas porque têm as funcionalidades mais correlacionadas com a variável de destino "Col1", com base no método de classificação "Correlação Pearson".

## <a name="conclusion"></a>Conclusão
Engenharia de funcionalidades e seleção de funcionalidades são duas grandes normalmente projetado e funcionalidades selecionadas aumentam a eficiência do processo de treinamento que tentativas para extrair as informações da chave contidas nos dados. Eles também melhorem o poder desses modelos para classificar os dados de entrada com precisão e prever os resultados de interesse com mais robustez. Engenharia de funcionalidades e a seleção também podem combinar para tornar a aprendizagem mais recursos computacionais tractable. Ele faz isso, a melhorar e, em seguida, reduzindo o número de recursos necessários para calibrar ou preparar um modelo. Falando em matemática, dos recursos selecionados para preparar o modelo são um conjunto mínimo de variáveis independentes, que explicam os padrões nos dados e, em seguida, prever os resultados com êxito.

Nem sempre é necessariamente para efetuar a seleção de funcionalidade ou de engenharia de funcionalidades. Se for necessário ou não depende dos dados recolhidos, o algoritmo selecionado e o objetivo da experimentação.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

