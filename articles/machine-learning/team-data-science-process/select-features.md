---
title: Seleção de recursos no Processo de Ciência de Dados da Equipa
description: Explica o propósito da seleção de funcionalidades e fornece exemplos do seu papel no processo de melhoria de dados de aprendizagem automática.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76716678"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Seleção de características no Team Data Science Process (TDSP)
Este artigo explica as finalidades da seleção de funcionalidades e fornece exemplos do seu papel no processo de melhoria de dados de machine learning. Estes exemplos são retirados do Azure Machine Learning Studio.

A engenharia e seleção de funcionalidades faz parte do Processo de Ciência de Dados da Equipa (TDSP) delineado no artigo Qual é o Processo de Ciência de Dados da [Equipa?](overview.md) Engenharia e seleção de recursos são partes do passo de **desenvolvimento** do TDSP.

* **engenharia**de recursos : Este processo tenta criar funcionalidades relevantes adicionais a partir das características cruas existentes nos dados, e aumentar o poder preditivo para o algoritmo de aprendizagem.
* **seleção de recursos**: Este processo seleciona o subconjunto chave das características originais dos dados numa tentativa de reduzir a dimensionalidade do problema de treino.

Normalmente, a engenharia de **recursos** é aplicada primeiro para gerar funcionalidades adicionais, e em seguida, o passo de **seleção** de recursos é realizado para eliminar funcionalidades irrelevantes, redundantes ou altamente correlacionadas.

## <a name="filter-features-from-your-data---feature-selection"></a>Filtrar funcionalidades dos seus dados - seleção de funcionalidades
A seleção de funcionalidades pode ser utilizada para tarefas de classificação ou regressão. O objetivo é selecionar um subconjunto das funcionalidades a partir do conjunto de dados original que reduzas as suas dimensões utilizando um conjunto mínimo de funcionalidades para representar a quantidade máxima de variação nos dados. Este subconjunto de características é utilizado para treinar o modelo. A seleção de recursos serve dois propósitos principais.

* Em primeiro lugar, a seleção de recursos aumenta frequentemente a precisão de classificação eliminando características irrelevantes, redundantes ou altamente correlacionadas.
* Em segundo lugar, diminui o número de funcionalidades, o que torna o processo de formação do modelo mais eficiente. A eficiência é importante para os alunos que são caros para treinar, como máquinas vetoriais de suporte.

Embora a seleção de funcionalidades procure reduzir o número de funcionalidades no conjunto de dados utilizado para treinar o modelo, não é referido pelo termo "redução da dimensionalidade". Os métodos de seleção de funcionalidades extraem um subconjunto de características originais nos dados sem os alterar.  Os métodos de redução da dimensionalidade empregam características projetadas que podem transformar as características originais e assim modificá-las. Exemplos de métodos de redução da dimensionalidade incluem análise de componentes principais, análise de correlação canónica e decomposição de valor singular.

Entre outros, uma categoria amplamente aplicada de métodos de seleção de recursos num contexto supervisionado chama-se "seleção de recursos baseado em filtros". Ao avaliar a correlação entre cada característica e o atributo-alvo, estes métodos aplicam uma medida estatística para atribuir uma pontuação a cada característica. As funcionalidades são então classificadas pela pontuação, que pode ser usada para ajudar a definir o limiar para manter ou eliminar uma característica específica. Exemplos das medidas estatísticas utilizadas nestes métodos incluem a correlação entre pessoas, a informação mútua e o teste à terra de Chi.

No Azure Machine Learning Studio, existem módulos fornecidos para a seleção de funcionalidades. Como mostra a seguinte figura, estes módulos incluem [a Seleção de Recursos Baseados][filter-based-feature-selection] em Filtros e a [Análise Discriminatória Linear do Fisher.][fisher-linear-discriminant-analysis]

![Módulos de Seleção de Recursos](./media/select-features/feature-Selection.png)

Considere, por exemplo, a utilização do módulo de [seleção de funcionalidades baseado][filter-based-feature-selection] no filtro. Por conveniência, continue a usar o exemplo de mineração de texto. Assuma que pretende construir um modelo de regressão depois de um conjunto de 256 funcionalidades serem criadas através do módulo [Feature Hashing,][feature-hashing] e que a variável de resposta é a "Col1" que contém classificações de revisão de livros que variam de 1 a 5. Ao definir "Método de pontuação de recurso" como "Pearson Correlation", a "coluna target" para ser "Col1", e o "Número de características desejadas" a 50. Em seguida, o módulo [Filter-Based Feature Selection][filter-based-feature-selection] produz um conjunto de dados contendo 50 funcionalidades juntamente com o atributo-alvo "Col1". A figura que se segue mostra o fluxo desta experiência e os parâmetros de entrada:

![Propriedades do módulo de seleção de funcionalidades baseada no filtro](./media/select-features/feature-Selection1.png)

O seguinte valor mostra os conjuntos de dados resultantes:

![Conjunto de dados resultante para módulo de seleção de funcionalidades baseada em filtros](./media/select-features/feature-Selection2.png)

Cada característica é pontuada com base na Correlação Pearson entre si e o atributo-alvo "Col1". As características com as melhores pontuações são mantidas.

As pontuações correspondentes das características selecionadas são mostradas na figura seguinte:

![Pontuações para módulo de seleção de funcionalidades baseado em filtro](./media/select-features/feature-Selection3.png)

Ao aplicar este módulo de [seleção de funcionalidades baseado][filter-based-feature-selection] em filtros, são selecionados 50 de 256 funcionalidades porque têm as características mais correlacionadas com a variável-alvo "Col1", com base no método de pontuação "Pearson Correlation".

## <a name="conclusion"></a>Conclusão
A engenharia de recursos e a seleção de funcionalidades são duas funcionalidades comumente concebidas e selecionadas aumentam a eficiência do processo de formação que tenta extrair a informação-chave contida nos dados. Também melhoram o poder destes modelos para classificar os dados de entrada com precisão e prever os resultados de interesse de forma mais robusta. A engenharia e seleção de recursos também podem combinar para tornar a aprendizagem mais computacionalmente tratável. Fá-lo melhorando e reduzindo depois o número de funcionalidades necessárias para calibrar ou treinar um modelo. Matematicamente falando, as características selecionadas para treinar o modelo são um conjunto mínimo de variáveis independentes que explicam os padrões nos dados e depois preveem resultados com sucesso.

Nem sempre é necessariamente realizar engenharia de recursos ou seleção de recursos. Se é necessário ou não depende dos dados recolhidos, do algoritmo selecionado e do objetivo da experiência.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

