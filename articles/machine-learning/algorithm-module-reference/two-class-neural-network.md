---
title: 'Rede neural de duas classes: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo rede neural de duas classes no Azure Machine Learning para criar um modelo de rede neural que pode ser usado para prever um destino que tem apenas dois valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: d8e27d13de6b1ac6940972022f66811eb75c8986
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73485891"
---
# <a name="two-class-neural-network-module"></a>Módulo de rede neural de duas classes

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use este módulo para criar um modelo de rede neural que possa ser usado para prever um destino que tenha apenas dois valores.

A classificação usando redes neurais é um método de aprendizado supervisionado e, portanto, requer um conjunto de informações *marcado*, que inclui uma coluna de rótulo. Por exemplo, você pode usar esse modelo de rede neural para prever resultados binários, como se um paciente tem ou não uma determinada doença ou se um computador provavelmente falhará dentro de uma janela de tempo especificada.  

Depois de definir o modelo, treine-o fornecendo um conjunto de dados marcado e o modelo como uma entrada para o [modelo de treinamento](./train-model.md). O modelo treinado pode então ser usado para prever valores para novas entradas.

### <a name="more-about-neural-networks"></a>Mais sobre redes neurais

Uma rede neural é um conjunto de camadas interconectadas. As entradas são a primeira camada e são conectadas a uma camada de saída por um grafo acíclico composto por bordas e nós ponderados.

Entre as camadas de entrada e saída, você pode inserir várias camadas ocultas. A maioria das tarefas preditivas pode ser realizada facilmente com apenas uma ou algumas camadas ocultas. No entanto, pesquisas recentes mostraram que as redes neurais profundas (DNN) com muitas camadas podem ser eficazes em tarefas complexas, como reconhecimento de imagem ou de fala. As camadas sucessivas são usadas para modelar níveis crescentes de profundidade semântica.

A relação entre entradas e saídas é aprendida de treinar a rede neural nos dados de entrada. A direção do grafo prossegue das entradas por meio da camada oculta e da camada de saída. Todos os nós em uma camada são conectados pelas bordas ponderadas aos nós na próxima camada.

Para computar a saída da rede para uma entrada específica, um valor é calculado em cada nó nas camadas ocultas e na camada de saída. O valor é definido calculando a soma ponderada dos valores dos nós da camada anterior. Uma função de ativação é então aplicada a essa soma ponderada.
  
## <a name="how-to-configure"></a>Como configurar

1.  Adicione o módulo **rede neural de duas classes** ao seu pipeline. Você pode encontrar esse módulo em **Machine Learning**, **inicializar**, na categoria **classificação** .  
  
2.  Especifique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .  
  
    -   **Parâmetro único**: escolha esta opção se você já souber como deseja configurar o modelo.  

3.  Para **especificação de camada oculta**, selecione o tipo de arquitetura de rede a ser criado.  
  
    -   **Caso totalmente conectado**: usa a arquitetura de rede neural padrão, definida para redes neurais de duas classes, da seguinte maneira:
  
        -   Tem uma camada oculta.
  
        -   A camada de saída é totalmente conectada à camada oculta e a camada oculta é totalmente conectada à camada de entrada.
  
        -   O número de nós na camada de entrada é igual ao número de recursos nos dados de treinamento.
  
        -   O número de nós na camada oculta é definido pelo usuário. O valor predefinido é 100.
  
        -   O número de nós é igual ao número de classes. Para uma rede neural de duas classes, isso significa que todas as entradas devem ser mapeadas para um dos dois nós na camada de saída.

5.  Para **taxa de aprendizagem**, defina o tamanho da etapa realizada em cada iteração, antes da correção. Um valor maior para a taxa de aprendizagem pode fazer com que o modelo seja convergido mais rapidamente, mas pode Sobreusar mínimo locais.

6.  Para **número de iterações de aprendizado**, especifique o número máximo de vezes que o algoritmo deve processar os casos de treinamento.

7.  Para **o diâmetro inicial dos pesos de aprendizado**, especifique os pesos do nó no início do processo de aprendizado.

8.  Para **a dinâmica**, especifique um peso a ser aplicado durante o aprendizado a nós de iterações anteriores  

10. Selecione a opção **exemplos de ordem aleatória** para embaralhar casos entre iterações. Se você desmarcar essa opção, os casos serão processados exatamente na mesma ordem toda vez que você executar o pipeline.
  
11. Para **semente de número aleatório**, digite um valor a ser usado como a semente.
  
     A especificação de um valor de semente é útil quando você deseja garantir a capacidade de repetição entre execuções do mesmo pipeline.  Caso contrário, um valor de relógio do sistema será usado como a semente, o que pode causar resultados ligeiramente diferentes cada vez que você executar o pipeline.
  
13. Adicione um conjunto de um DataSet marcado ao pipeline e conecte um dos [módulos de treinamento](module-reference.md).  
  
    -   Se você definir **criar modo de instrutor** como **um único parâmetro**, use o módulo [treinar modelo](train-model.md) .  
  
14. Executar o pipeline.

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver um resumo dos parâmetros do modelo, junto com os pesos do recurso aprendidos do treinamento e outros parâmetros da rede neural, clique com o botão direito do mouse na saída do [modelo](./train-model.md)de treino e selecione **Visualizar**.  

+ Para salvar um instantâneo do modelo treinado, clique com o botão direito do mouse na saída do **modelo treinado** e selecione **salvar como modelo treinado**. Esse modelo não é atualizado em execuções sucessivas do mesmo pipeline.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
