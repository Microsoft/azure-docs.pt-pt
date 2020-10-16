---
title: Execute o Python Script no designer
titleSuffix: Azure Machine Learning
description: Aprenda a usar python em azure machine learning designer para transformar dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 09/09/2020
ms.topic: conceptual
ms.custom: how-to, designer, devx-track-python
ms.openlocfilehash: 6a393cf2c0787a2bc2111581126cbc261d85f1f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90897732"
---
# <a name="run-python-code-in-azure-machine-learning-designer"></a>Executar código Python em Azure Machine Learning designer

Neste artigo, você aprende a usar o módulo [executar python script](algorithm-module-reference/execute-python-script.md) para adicionar lógica personalizada ao designer de Aprendizagem automática Azure. No seguinte como fazer, você usa a biblioteca pandas para fazer engenharia de recursos simples.

Você pode usar o editor de código incorporado para adicionar rapidamente a lógica simples python. Se pretender adicionar código mais complexo ou carregar bibliotecas python adicionais, deve utilizar o método do ficheiro zip.

O ambiente de execução padrão usa a distribuição de Anacondas de Python. Para obter uma lista completa de pacotes pré-instalados, consulte a página de referência do [módulo de script Execute Python.](algorithm-module-reference/execute-python-script.md)

![Execute o mapa de entrada python](media/how-to-designer-python/execute-python-map.png)

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="execute-python-written-in-the-designer"></a>Execute Python escrito no designer

### <a name="add-the-execute-python-script-module"></a>Adicione o módulo de script de Python executar

1. Encontre o módulo **de script Execute Python** na paleta de designers. Pode ser encontrado na secção **língua python.**

1. Arraste e deixe cair o módulo sobre a tela do gasoduto.

### <a name="connect-input-datasets"></a>Ligar conjuntos de dados de entrada

Este artigo utiliza o conjunto de dados da amostra, **dados sobre os preços do automóvel (Raw)**. 

1. Arraste e deixe cair o conjunto de dados para a tela do gasoduto.

1. Ligue a porta de saída do conjunto de dados à porta de entrada superior esquerda do módulo **executo python script.** O designer expõe a entrada como um parâmetro para o script do ponto de entrada.
    
    A porta de entrada certa está reservada para bibliotecas de pitões com fecho.

    ![Ligar conjuntos de dados](media/how-to-designer-python/connect-dataset.png)
        

1. Tome nota da porta de entrada que utiliza. O designer atribui a porta de entrada esquerda à variável `dataset1` e a porta de entrada média a `dataset2` . 

Os módulos de entrada são opcionais, uma vez que pode gerar ou importar dados diretamente no módulo **De Script Execute Python.**

### <a name="write-your-python-code"></a>Escreva o seu código Python

O designer fornece um script inicial de ponto de entrada para você editar e introduzir o seu próprio código Python. 

Neste exemplo, você usa Pandas para combinar duas colunas encontradas no conjunto de dados do automóvel, **Price** and **Horsepower,** para criar uma nova coluna, **Dólares por cavalo.** Esta coluna representa quanto você paga por cada cavalo, o que pode ser uma característica útil para decidir se um carro é um bom negócio para o dinheiro. 

1. Selecione o módulo **de script de Python executar.**

1. No painel que aparece à direita da tela, selecione a caixa **de texto do script Python.**

1. Copiar e colar o seguinte código na caixa de texto.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    O seu oleoduto deve olhar para a seguinte imagem:
    
    ![Executar o gasoduto Python](media/how-to-designer-python/execute-python-pipeline.png)

    O script do ponto de entrada deve conter a função `azureml_main` . Existem dois parâmetros de função que mapeiam para as duas portas de entrada para o módulo **de script de Python executo.**

    O valor de retorno deve ser um Dataframe Pandas. Pode retornar até dois dataframes como saídas de módulos.
    
1. Envie o oleoduto.

Agora, você tem um conjunto de dados com a nova funcionalidade **Dólares/HP,** o que pode ser útil na formação de um recomendador de carro. Este é um exemplo de extração de recursos e redução da dimensionalidade. 

## <a name="next-steps"></a>Passos seguintes

Aprenda a [importar os seus próprios dados](how-to-designer-import-data.md) no designer de Machine Learning Azure.
