---
title: 'Rede neural de duas classes: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo rede neural de duas classes no Azure Machine Learning para criar um modelo de rede neural que pode ser usado para prever um destino que tem apenas dois valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: c87673fef0079ff1b04b0840da0434b090cda67c
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153660"
---
# <a name="two-class-neural-network-module"></a>Módulo de rede neural de duas classes

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para criar um modelo de rede neural que possa ser usado para prever um destino que tenha apenas dois valores.

A classificação utilizando redes neurais é um método de aprendizagem supervisionado e, portanto, requer um conjunto de *dados marcado,* que inclui uma coluna de etiquetas. Por exemplo, você pode usar esse modelo de rede neural para prever resultados binários, como se um paciente tem ou não uma determinada doença ou se um computador provavelmente falhará dentro de uma janela de tempo especificada.  

Depois de definir o modelo, treine-o fornecendo um conjunto de dados marcado e o modelo como entrada para [o Modelo de Comboio](./train-model.md). O modelo treinado pode então ser usado para prever valores para novas entradas.

### <a name="more-about-neural-networks"></a>Mais sobre redes neurais

Uma rede neural é um conjunto de camadas interconectadas. As entradas são a primeira camada e são conectadas a uma camada de saída por um grafo acíclico composto por bordas e nós ponderados.

Entre as camadas de entrada e saída, você pode inserir várias camadas ocultas. A maioria das tarefas preditivas pode ser realizada facilmente com apenas uma ou algumas camadas ocultas. No entanto, pesquisas recentes mostraram que as redes neurais profundas (DNN) com muitas camadas podem ser eficazes em tarefas complexas, como reconhecimento de imagem ou de fala. As camadas sucessivas são usadas para modelar níveis crescentes de profundidade semântica.

A relação entre entradas e saídas é aprendida de treinar a rede neural nos dados de entrada. A direção do grafo prossegue das entradas por meio da camada oculta e da camada de saída. Todos os nós em uma camada são conectados pelas bordas ponderadas aos nós na próxima camada.

Para computar a saída da rede para uma entrada específica, um valor é calculado em cada nó nas camadas ocultas e na camada de saída. O valor é definido calculando a soma ponderada dos valores dos nós da camada anterior. Uma função de ativação é então aplicada a essa soma ponderada.
  
## <a name="how-to-configure"></a>Como configurar

1.  Adicione o módulo **de rede neural de duas classes** ao seu oleoduto. Pode encontrar este módulo na categoria **Machine Learning**, **Initialize,** na categoria **Classificação.**  
  
2.  Especifique como pretende que o modelo seja treinado, definindo a opção modo **Criar.**  
  
    -   **Parâmetro único**: Escolha esta opção se já sabe como pretende configurar o modelo.  

3.  Para **especificação de camada escondida,** selecione o tipo de arquitetura de rede para criar.  
  
    -   **Caso totalmente ligado**: Utiliza a arquitetura da rede neural padrão, definida para redes neurais de duas classes da seguinte forma:
  
        -   Tem uma camada oculta.
  
        -   A camada de saída é totalmente conectada à camada oculta e a camada oculta é totalmente conectada à camada de entrada.
  
        -   O número de nós na camada de entrada é igual ao número de recursos nos dados de treinamento.
  
        -   O número de nós na camada oculta é definido pelo usuário. O valor predefinido é 100.
  
        -   O número de nós é igual ao número de classes. Para uma rede neural de duas classes, isso significa que todas as entradas devem ser mapeadas para um dos dois nós na camada de saída.

5.  Para a taxa de **aprendizagem,** defina o tamanho do passo dado em cada iteração, antes da correção. Um valor maior para a taxa de aprendizagem pode fazer com que o modelo seja convergido mais rapidamente, mas pode Sobreusar mínimo locais.

6.  Para **o número de iterações de aprendizagem,** especifique o número máximo de vezes que o algoritmo deve processar os casos de treino.

7.  Para o diâmetro dos pesos de **aprendizagem inicial,** especifique os pesos do nó no início do processo de aprendizagem.

8.  Para **o impulso,** especifique um peso a aplicar durante a aprendizagem a nós de iterações anteriores  

10. Selecione a opção **de exemploshuffle** para baralhar casos entre iterações. Se você desmarcar essa opção, os casos serão processados exatamente na mesma ordem toda vez que você executar o pipeline.
  
11. Para **sementes aleatórias,** digite um valor a utilizar como semente.
  
     A especificação de um valor de semente é útil quando você deseja garantir a capacidade de repetição entre execuções do mesmo pipeline.  Caso contrário, um valor de relógio do sistema será usado como a semente, o que pode causar resultados ligeiramente diferentes cada vez que você executar o pipeline.
  
13. Adicione um conjunto de dados marcado ao gasoduto e ligue um dos [módulos](module-reference.md)de treino .  
  
    -   Se definir o modo Criar modo **de treinador** para um parâmetro **único,** utilize o módulo Modelo de [Comboio.](train-model.md)  
  
14. Executar o pipeline.

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para guardar uma imagem do modelo treinado, selecione o separador **Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone do conjunto de **dados Register** para salvar o modelo como um módulo reutilizável.

+ Para utilizar o modelo para a pontuação, adicione o módulo **Score Model** a um pipeline.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
