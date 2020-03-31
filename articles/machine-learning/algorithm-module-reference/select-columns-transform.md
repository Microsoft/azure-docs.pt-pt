---
title: 'Selecione Colunas Transform: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Select Columns Transform em Azure Machine Learning para criar uma transformação que selecione o mesmo subconjunto de colunas que no conjunto de dados dado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: a5264c14294f84858cd489f5892b8cdd19e117d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455915"
---
# <a name="select-columns-transform"></a>Selecionar Transformação de Colunas

Este artigo descreve como utilizar o módulo Select Columns Transform em Azure Machine Learning designer (pré-visualização). O objetivo do módulo Select Columns Transform é garantir que um conjunto previsível e consistente de colunas seja utilizado em operações de aprendizagem automática a jusante.

Este módulo é útil para tarefas como pontuação, que requerem colunas específicas. As alterações nas colunas disponíveis podem quebrar o gasoduto ou alterar os resultados.

Utilize colunas Select Transform para criar e guardar um conjunto de colunas. Em seguida, utilize o módulo [De Transformação aplicar](apply-transformation.md) para aplicar essas seleções a novos dados.

## <a name="how-to-use-select-columns-transform"></a>Como utilizar colunas selecionadas Transforme

Este cenário pressupõe que pretende utilizar a seleção de funcionalidades para gerar um conjunto dinâmico de colunas que serão usadas para treinar um modelo. Para garantir que as seleções de colunas são as mesmas para o processo de pontuação, utilize o módulo Select Columns Transform para capturar as seleções de colunas e aplicá-las em outros lugares do pipeline.

1. Adicione um conjunto de dados de entrada ao seu pipeline no designer.

2. Adicione uma instância de seleção de [funcionalidades baseada](filter-based-feature-selection.md)em filtros.

3. Ligue os módulos e configure o módulo de seleção de funcionalidades para encontrar automaticamente uma série de melhores funcionalidades no conjunto de dados de entrada.

4. Adicione uma instância de [Modelo de Comboio](train-model.md) e use a saída da [Seleção](filter-based-feature-selection.md) de Funcionalidades Baseada em Filtros como entrada para treino.

    > [!IMPORTANT]
    > Como a importância da funcionalidade é baseada nos valores da coluna, não se pode saber antecipadamente quais as colunas disponíveis para entrada no [Modelo de Comboio](train-model.md).  
5. Fixe uma instância do módulo Select Columns Transform. 

    Este passo gera uma seleção de colunas como uma transformação que pode ser guardada ou aplicada a outros conjuntos de dados. Este passo garante que as colunas identificadas na seleção de funcionalidades são guardadas para que outros módulos se reutilizem.

6. Adicione o módulo ['Modelo de Pontuação'.](score-model.md) 

   *Não ligue o conjunto de dados de entrada.* Em vez disso, adicione o módulo [De Transformação de Aplicação](apply-transformation.md) e ligue a saída da transformação da seleção de recursos.

   > [!IMPORTANT]
   > Não pode esperar aplicar a [Seleção de Funcionalidades Baseadas](filter-based-feature-selection.md) em Filtros no conjunto de dados de pontuação e obter os mesmos resultados. Como a seleção de funcionalidades é baseada em valores, pode escolher um conjunto diferente de colunas, o que faria com que a operação de pontuação falhasse.
7. Submeta o oleoduto.

Este processo de poupança e, em seguida, aplicação de uma seleção de colunas garante que o mesmo esquema de dados está disponível para treino e pontuação.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
