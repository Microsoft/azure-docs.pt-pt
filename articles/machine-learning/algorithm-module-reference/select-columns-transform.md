---
title: 'Selecionar colunas de transformação: referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo selecionar colunas de transformação no serviço Azure Machine Learning para criar uma transformação que seleciona o mesmo subconjunto de colunas que no conjunto de dados fornecido.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: def15b3c28b2278f884b36acc39fa75982fcf8c5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516204"
---
# <a name="select-columns-transform"></a>Selecionar transformação de colunas

Este artigo descreve como usar o módulo **selecionar colunas de transformação** no designer de Azure Machine Learning (versão prévia). A finalidade do módulo **selecionar colunas de transformação** é garantir que um conjunto de colunas consistente e previsível seja sempre usado em operações de aprendizado de máquina downstream.

Esse módulo é útil para tarefas como pontuação, que exigem colunas específicas. As alterações nas colunas disponíveis podem interromper o pipeline ou alterar os resultados.

Use a **transformação selecionar colunas** para criar e salvar um conjunto de colunas. Em seguida, use o módulo [aplicar transformação](apply-transformation.md) para aplicar essas seleções a novos dados.

## <a name="how-to-use-select-columns-transform"></a>Como usar a transformação selecionar colunas

Este cenário pressupõe que você pretende usar a seleção de recursos para gerar um conjunto dinâmico de colunas que serão usadas para treinar um modelo. Para garantir que as seleções de coluna sejam as mesmas para o processo de pontuação, use o módulo **selecionar colunas de transformação** para capturar as seleções de coluna e aplicá-las em outro lugar no pipeline.

1. Adicione um conjunto de dados de entrada ao seu pipeline no designer.

2. Adicione uma instância da [seleção de recursos baseada em filtro](filter-based-feature-selection.md).

3. Conecte os módulos e configure o módulo seleção de recursos para localizar automaticamente alguns dos melhores recursos no conjunto de dados de entrada.

4. Adicione uma instância do [modelo de treinamento](train-model.md) e use a saída da [seleção de recursos baseada em filtro](filter-based-feature-selection.md) como a entrada para treinamento.

    > [!IMPORTANT]
    > Como a importância do recurso é decidida com base nos valores na coluna, você não pode saber com antecedência quais colunas podem estar disponíveis para entrada para [treinar o modelo](train-model.md).  
5. Agora, anexe uma instância do módulo **selecionar colunas de transformação** . 

    Isso gera uma seleção de coluna como uma transformação que pode ser salva ou aplicada a outros conjuntos de os. Essa etapa garante que as colunas identificadas pela seleção de recursos sejam salvas para reutilização por outros módulos.

6. Adicione o módulo [modelo de Pontuação](score-model.md) . 

    **Não conecte o conjunto de dados de entrada.**

    Em vez disso, adicione o módulo [aplicar transformação](apply-transformation.md) e conecte a saída da transformação seleção de recursos.

   > [!IMPORTANT]
   > Não é possível esperar a aplicação da [seleção de recursos baseada em filtro](filter-based-feature-selection.md) ao conjunto de texto de Pontuação e obter os mesmos resultados. Como a seleção de recursos se baseia em valores, ela pode escolher um conjunto diferente de colunas, o que causaria uma falha na operação de pontuação.
7. Executar o pipeline.

Esse processo de salvar e aplicar uma seleção de coluna garante que o mesmo esquema de dados esteja disponível para treinamento e pontuação.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 
