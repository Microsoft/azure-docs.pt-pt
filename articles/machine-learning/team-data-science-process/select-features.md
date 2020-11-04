---
title: Seleção de recursos no Processo de Ciência de Dados de Equipa
description: Explica o propósito da seleção de recursos e fornece exemplos do seu papel no processo de melhoria de dados de machine learning.
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
ms.openlocfilehash: 940d4c465acb5e8478d2b204ab5b9b18c79254f0
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321316"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Seleção de características no Team Data Science Process (TDSP)
Este artigo explica as finalidades da seleção de recursos e fornece exemplos do seu papel no processo de melhoria de dados de machine learning. Estes exemplos são desenhados a partir do Azure Machine Learning Studio.

A engenharia e seleção de funcionalidades faz parte do Processo de Ciência de Dados de Equipa (TDSP) delineado no artigo [O que é o Processo de Ciência de Dados da Equipa?](overview.md) A engenharia e a seleção de recursos são partes do passo de **funcionalidades de Desenvolvimento** do TDSP.

* **engenharia de recursos** : Este processo tenta criar funcionalidades relevantes adicionais a partir das características brutas existentes nos dados, e aumentar o poder preditivo para o algoritmo de aprendizagem.
* **seleção de recursos** : Este processo seleciona o subconjunto chave das características originais dos dados numa tentativa de reduzir a dimensionalidade do problema de treino.

**Normalmente, a engenharia de recursos** é aplicada primeiro para gerar funcionalidades adicionais, e em seguida, o passo de **seleção** de recursos é executado para eliminar características irrelevantes, redundantes ou altamente correlacionadas.

## <a name="filter-features-from-your-data---feature-selection"></a>Filtrar funcionalidades a partir dos seus dados - seleção de recursos
A seleção de recursos pode ser utilizada para tarefas de classificação ou regressão. O objetivo é selecionar um subconjunto das funcionalidades do conjunto de dados originais que reduzam as suas dimensões utilizando um conjunto mínimo de funcionalidades para representar a quantidade máxima de variação nos dados. Este subconjunto de funcionalidades é utilizado para treinar o modelo. A seleção de recursos serve dois propósitos principais.

* Em primeiro lugar, a seleção de recursos aumenta frequentemente a precisão da classificação eliminando características irrelevantes, redundantes ou altamente correlacionadas.
* Em segundo lugar, diminui o número de funcionalidades, o que torna o processo de formação de modelos mais eficiente. A eficiência é importante para os alunos que são caros para treinar, como máquinas vetoriais de suporte.

Embora a seleção de recursos procure reduzir o número de funcionalidades no conjunto de dados utilizado para treinar o modelo, não é referida pelo termo "redução da dimensionalidade". Os métodos de seleção de recursos extraem um subconjunto de características originais nos dados sem os alterar.  Os métodos de redução da dimensionalidade empregam características concebidas que podem transformar as características originais e, assim, modificá-las. Exemplos de métodos de redução da dimensionalidade incluem análise principal de componentes, análise de correlação canónica e decomposição de valor singular.

Entre outros, uma categoria amplamente aplicada de métodos de seleção de recursos num contexto supervisionado é chamada de "seleção de recursos baseados em filtros". Ao avaliar a correlação entre cada recurso e o atributo alvo, estes métodos aplicam uma medida estatística para atribuir uma pontuação a cada característica. As características são então classificadas pela pontuação, que pode ser usada para ajudar a definir o limiar para manter ou eliminar uma característica específica. Exemplos das medidas estatísticas utilizadas nestes métodos incluem a correlação de pessoas, a informação mútua e o teste chi ao quadrado.

No Azure Machine Learning Studio, existem módulos fornecidos para a seleção de recursos. Como mostrado na seguinte figura, estes módulos incluem [a seleção de recursos baseados em filtros][filter-based-feature-selection] e [a análise discriminatória linear de fisher.][fisher-linear-discriminant-analysis]

![Módulos de seleção de recursos](./media/select-features/feature-Selection.png)

Considere, por exemplo, a utilização do módulo [de seleção de recursos baseado em filtros.][filter-based-feature-selection] Por conveniência, continue a usar o exemplo de mineração de texto. Assuma que pretende construir um modelo de regressão depois de um conjunto de 256 funcionalidades serem criadas através do módulo [de Hashing de Recurso,][feature-hashing] e que a variável de resposta é o "Col1" que contém classificações de revisão de livros que variam de 1 a 5. Ao definir "Método de pontuação de recurso" para ser "Pearson Correlation", a "coluna-alvo" é "Col1", e o "Número de características desejadas" para 50. Em seguida, a [seleção de recursos baseada em filtros][filter-based-feature-selection] do módulo produz um conjunto de dados contendo 50 funcionalidades juntamente com o atributo-alvo "Col1". A seguinte figura mostra o fluxo desta experiência e os parâmetros de entrada:

![Filter-Based Propriedades do módulo de seleção de recursos](./media/select-features/feature-Selection1.png)

O seguinte número mostra os conjuntos de dados resultantes:

![Conjunto de dados resultante para módulo de seleção de recursos baseado em filtro](./media/select-features/feature-Selection2.png)

Cada recurso é pontuado com base na Correlação Pearson entre si e o atributo alvo "Col1". As características com pontuações de topo são mantidas.

As pontuações correspondentes das características selecionadas são apresentadas na seguinte figura:

![Pontuações para módulo de seleção de recursos baseado em filtro](./media/select-features/feature-Selection3.png)

Ao aplicar este módulo [de seleção de recursos baseado em filtros,][filter-based-feature-selection] 50 de 256 funcionalidades são selecionadas porque têm as características mais correlacionadas com a variável-alvo "Col1", baseada no método de pontuação "Pearson Correlation".

## <a name="conclusion"></a>Conclusão
A engenharia de recursos e a seleção de recursos são duas características geralmente concebidas e selecionadas aumentam a eficiência do processo de formação que tenta extrair a informação chave contida nos dados. Também melhoram o poder destes modelos para classificar os dados de entrada com precisão e prever os resultados de interesse de forma mais robusta. A engenharia e a seleção de recursos também podem combinar para tornar a aprendizagem mais computacionalmente tratável. Fá-lo melhorando e reduzindo depois o número de funcionalidades necessárias para calibrar ou treinar um modelo. Matematicamente falando, as características selecionadas para treinar o modelo são um conjunto mínimo de variáveis independentes que explicam os padrões nos dados e depois preveem os resultados com sucesso.

Nem sempre é necessariamente para realizar engenharia de recursos ou seleção de recursos. Se é necessário ou não depende dos dados recolhidos, do algoritmo selecionado e do objetivo da experiência.

<!-- Module References -->
[feature-hashing]: /azure/machine-learning/studio-module-reference/feature-hashing
[filter-based-feature-selection]: /previous-versions/azure/dn905854(v=azure.100)
[fisher-linear-discriminant-analysis]: /azure/machine-learning/studio-module-reference/fisher-linear-discriminant-analysis