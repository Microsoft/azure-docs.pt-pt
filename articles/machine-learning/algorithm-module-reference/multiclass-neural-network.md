---
title: 'Rede neural multiclasse: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo rede neural multiclasse no Azure Machine Learning para criar um modelo de rede neural que possa ser usado para prever um destino com vários valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: f94427ddfbdc19836cd177fd642987aaaeedf1ee
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152096"
---
# <a name="multiclass-neural-network-module"></a>Módulo de rede neural multiclasse

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para criar um modelo de rede neural que possa ser usado para prever um destino que tenha vários valores. 

Por exemplo, redes neurais desse tipo podem ser usadas em tarefas complexas da pesquisa Visual computacional, como reconhecimento de dígitos ou letras, classificação de documentos e reconhecimento de padrões.

A classificação utilizando redes neurais é um método de aprendizagem supervisionado e, portanto, requer um conjunto de *dados marcado* que inclua uma coluna de etiquetas.

Pode treinar o modelo fornecendo o modelo e o conjunto de dados marcadocomo entrada para [o Modelo de Comboio](./train-model.md). O modelo treinado pode então ser usado para prever valores para os novos exemplos de entrada.  

## <a name="about-neural-networks"></a>Sobre redes neurais

Uma rede neural é um conjunto de camadas interconectadas. As entradas são a primeira camada e são conectadas a uma camada de saída por um grafo acíclico composto por bordas e nós ponderados.

Entre as camadas de entrada e saída, você pode inserir várias camadas ocultas. A maioria das tarefas preditivas pode ser realizada facilmente com apenas uma ou algumas camadas ocultas. No entanto, pesquisas recentes mostraram que as redes neurais profundas (DNN) com muitas camadas podem ser eficazes em tarefas complexas, como reconhecimento de imagem ou de fala. As camadas sucessivas são usadas para modelar níveis crescentes de profundidade semântica.

A relação entre entradas e saídas é aprendida de treinar a rede neural nos dados de entrada. A direção do grafo prossegue das entradas por meio da camada oculta e da camada de saída. Todos os nós em uma camada são conectados pelas bordas ponderadas aos nós na próxima camada.

Para computar a saída da rede para uma entrada específica, um valor é calculado em cada nó nas camadas ocultas e na camada de saída. O valor é definido calculando a soma ponderada dos valores dos nós da camada anterior. Uma função de ativação é então aplicada a essa soma ponderada.

## <a name="configure-multiclass-neural-network"></a>Configurar rede neural multiclasse

1. Adicione o módulo **multiclasse da Rede Neural** ao seu pipeline no designer. Pode encontrar este módulo na categoria **Machine Learning**, **Initialize,** na categoria **Classificação.**

2. **Criar o modo de treinador**: Utilize esta opção para especificar como pretende que o modelo seja treinado:

    - **Parâmetro único**: Escolha esta opção se já sabe como pretende configurar o modelo.

    

3. **Especificação**da camada escondida : Selecione o tipo de arquitetura de rede para criar.

    - **Caso totalmente ligado**: Selecione esta opção para criar um modelo utilizando a arquitetura de rede neural predefinida. Para modelos de rede neural multiclasse, os padrões são os seguintes:

        - Uma camada oculta
        - A camada de saída é totalmente conectada à camada oculta.
        - A camada oculta é totalmente conectada à camada de entrada.
        - O número de nós na camada de entrada é determinado pelo número de recursos nos dados de treinamento.
        - O número de nós na camada oculta pode ser definido pelo usuário. O padrão é 100.
        - O número de nós na camada de saída depende do número de classes.
  
   

5. **Número de nós escondidos**: Esta opção permite personalizar o número de nós escondidos na arquitetura padrão. Digite o número de nós ocultos. O padrão é uma camada oculta com nós 100.

6. **A taxa de aprendizagem**: Defina o tamanho do passo dado em cada iteração, antes da correção. Um valor maior para a taxa de aprendizagem pode fazer com que o modelo converta mais rapidamente, mas pode ultrapassar o minima local.

7. **Número de iterações de aprendizagem**: Especifique o número máximo de vezes que o algoritmo deve processar os casos de treino.

8. Diâmetro dos pesos de **aprendizagem inicial**: Especifique os pesos do nó no início do processo de aprendizagem.

9. **O impulso**: Especifique um peso a aplicar durante a aprendizagem aos nódosos de iterações anteriores.
  
11. **Exemplos**de shuffle : Selecione esta opção para baralhar casos entre iterações.

    Se você desmarcar essa opção, os casos serão processados exatamente na mesma ordem toda vez que você executar o pipeline.

12. **Semente**de número aleatório: Digite um valor a utilizar como a semente, se pretender garantir a repetbilidade através de percursos do mesmo gasoduto.

14. Ligue um conjunto de dados de treino e um dos módulos de [treino:](module-reference.md) 

    - Se definir **criar o modo de formação** para um parâmetro **único,** utilize o [Modelo de Comboio](train-model.md).  
  

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

- Para guardar uma imagem do modelo treinado, selecione o separador **Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone do conjunto de **dados Register** para salvar o modelo como um módulo reutilizável.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 