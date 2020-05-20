---
title: Python
titleSuffix: Azure Machine Learning
description: Aprenda a usar python em azure machine learning designer para transformar dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: peterclu
ms.author: peterlu
ms.date: 02/28/2020
ms.custom: designer
ms.openlocfilehash: e27844f9f534ea4db1aba53c12fb3947e7269846
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83644460"
---
# <a name="execute-python-code-in-azure-machine-learning-designer"></a>Execute código Python em designer de machine learning azure

Neste artigo, aprende-se a usar o módulo [Execute Python Script](algorithm-module-reference/execute-python-script.md) para adicionar lógica personalizada ao designer de Machine Learning Azure. No seguinte como, você usa a biblioteca Pandas para fazer engenharia de recursos simples.

Você pode usar o editor de código incorporado para adicionar rapidamente a lógica simples python. Se quiser adicionar código mais complexo ou carregar bibliotecas python adicionais, deve utilizar o método do ficheiro zip.

O ambiente de execução padrão usa a distribuição de Anacondas de Python. Para obter uma lista completa de pacotes pré-instalados, consulte a página de referência do [módulo Execute Python Script.](algorithm-module-reference/execute-python-script.md)

![Execute o mapa de entrada python](media/how-to-designer-python/execute-python-map.png)

## <a name="execute-python-written-in-the-designer"></a>Execute Python escrito no designer

### <a name="add-the-execute-python-script-module"></a>Adicione o módulo de script de Execução Python

1. Encontre o módulo **Execute Python Script** na paleta de design. Pode ser encontrado na secção **de Linguagem Python.**

1. Arraste e deixe cair o módulo na tela do oleoduto.

### <a name="connect-input-datasets"></a>Ligar conjuntos de dados de entrada

Este artigo utiliza o conjunto de dados da amostra, **dados sobre o preço do automóvel (Cru)**. 

1. Arraste e largue o seu conjunto de dados para a tela do oleoduto.

1. Ligue a porta de saída do conjunto de dados à porta de entrada superior esquerda do módulo **Execute Python Script.** O designer expõe a entrada como parâmetro ao script do ponto de entrada.
    
    A porta de entrada direita está reservada para bibliotecas de pitão zipped.

    ![Ligar conjuntos de dados](media/how-to-designer-python/connect-dataset.png)
        

1. Tome nota da porta de entrada que utiliza. O designer atribui a porta de entrada esquerda à variável `dataset1` e à porta de entrada média para `dataset2` . 

Os módulos de entrada são opcionais, uma vez que pode gerar ou importar dados diretamente no módulo **Execute Python Script.**

### <a name="write-your-python-code"></a>Escreva o seu código Python

O designer fornece um script de ponto de entrada inicial para que edite e introduza o seu próprio código Python. 

Neste exemplo, você usa Pandas para combinar duas colunas encontradas no conjunto de dados do automóvel, **Price** and **Horsepower,** para criar uma nova coluna, **Dólares por cavalo.** Esta coluna representa quanto se paga por cada cavalo, o que pode ser uma característica útil para decidir se um carro é um bom negócio para o dinheiro. 

1. Selecione o módulo **execute python script.**

1. No painel que aparece à direita da tela, selecione a caixa de texto do **script Python.**

1. Copiar e colar o seguinte código na caixa de texto.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    O seu oleoduto deve ter a seguinte imagem:
    
    ![Executar o gasoduto Python](media/how-to-designer-python/execute-python-pipeline.png)

    O script do ponto de entrada deve conter a função `azureml_main` . Existem dois parâmetros de função que mapeiam as duas portas de entrada para o módulo **execute Python Script.**

    O valor de retorno deve ser um Quadro de Dados pandas. Pode devolver até dois quadros de dados como saídas de módulos.
    
1. Submeta o oleoduto.

Agora, você tem um conjunto de dados com a nova funcionalidade **Dólares/HP**, que pode ser útil na formação de um recomendador de carro. Este é um exemplo de extração de recursos e redução da dimensionalidade. 

## <a name="next-steps"></a>Passos seguintes

Aprenda a [importar os seus próprios dados](how-to-designer-import-data.md) em azure machine learning designer.