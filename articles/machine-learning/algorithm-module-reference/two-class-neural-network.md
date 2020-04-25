---
title: 'Rede Neural de duas classes: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de rede neural de duas classes em Azure Machine Learning para criar um modelo de rede neural que pode ser usado para prever um alvo que tem apenas dois valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 4270e4c71cca28d43b1ecbaca1ec0f262251252b
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82137608"
---
# <a name="two-class-neural-network-module"></a>Módulo de rede neural de duas classes

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para criar um modelo de rede neural que pode ser usado para prever um alvo que tem apenas dois valores.

A classificação utilizando redes neurais é um método de aprendizagem supervisionado e, portanto, requer um conjunto de *dados marcado,* que inclui uma coluna de etiquetas. Por exemplo, pode utilizar este modelo de rede neural para prever resultados binários, tais como se um paciente tem ou não uma determinada doença, ou se uma máquina é suscetível de falhar dentro de uma janela de tempo especificada.  

Depois de definir o modelo, treine-o fornecendo um conjunto de dados marcado e o modelo como entrada para [o Modelo de Comboio](./train-model.md). O modelo treinado pode então ser usado para prever valores para novas inputs.

### <a name="more-about-neural-networks"></a>Mais sobre redes neurais

Uma rede neural é um conjunto de camadas interligadas. As inputs são a primeira camada, e estão ligadas a uma camada de saída por um gráfico acíclico composto por bordas e nós ponderados.

Entre as camadas de entrada e saída pode inserir várias camadas escondidas. A maioria das tarefas preditivas pode ser realizada facilmente com apenas uma ou algumas camadas escondidas. No entanto, pesquisas recentes mostraram que redes neuronais profundas (DNN) com muitas camadas podem ser eficazes em tarefas complexas, como imagem ou reconhecimento de voz. As camadas sucessivas são usadas para modelar níveis crescentes de profundidade semântica.

A relação entre inputs e saídas é aprendida com a formação da rede neural nos dados de entrada. A direção do gráfico procede das inputs através da camada escondida e da camada de saída. Todos os nós em camada são ligados pelas bordas ponderadas aos nós na camada seguinte.

Para calcular a saída da rede para uma determinada entrada, calcula-se um valor em cada nó nas camadas ocultas e na camada de saída. O valor é definido calculando a soma ponderada dos valores dos nódosos da camada anterior. Uma função de ativação é então aplicada a essa soma ponderada.
  
## <a name="how-to-configure"></a>Como configurar

1.  Adicione o módulo **de rede neural de duas classes** ao seu oleoduto. Pode encontrar este módulo na categoria **Machine Learning**, **Initialize,** na categoria **Classificação.**  
  
2.  Especifique como pretende que o modelo seja treinado, definindo a opção modo **Criar.**  
  
    -   **Parâmetro único**: Escolha esta opção se já sabe como pretende configurar o modelo.

    -   **Gama de parâmetros**: Se não tiver a certeza dos melhores parâmetros, pode encontrar os parâmetros ideais utilizando o módulo de [hiperparâmetros](tune-model-hyperparameters.md) do Modelo tune. Você fornece alguma gama de valores, e o treinador iterates sobre múltiplas combinações das configurações para determinar a combinação de valores que produz o melhor resultado.  

3.  Para **especificação de camada escondida,** selecione o tipo de arquitetura de rede para criar.  
  
    -   **Caso totalmente ligado**: Utiliza a arquitetura da rede neural padrão, definida para redes neurais de duas classes da seguinte forma:
  
        -   Tem uma camada escondida.
  
        -   A camada de saída está totalmente ligada à camada escondida, e a camada escondida está totalmente ligada à camada de entrada.
  
        -   O número de nós na camada de entrada equivale ao número de funcionalidades nos dados de formação.
  
        -   O número de nós na camada escondida é definido pelo utilizador. O valor predefinido é 100.
  
        -   O número de nós é igual ao número de aulas. Para uma rede neural de duas classes, isto significa que todas as inputs devem mapear para um de dois nós na camada de saída.

5.  Para a taxa de **aprendizagem,** defina o tamanho do passo dado em cada iteração, antes da correção. Um valor maior para a taxa de aprendizagem pode fazer com que o modelo converta mais rapidamente, mas pode ultrapassar o minima local.

6.  Para **o número de iterações de aprendizagem,** especifique o número máximo de vezes que o algoritmo deve processar os casos de treino.

7.  Para o diâmetro dos pesos de **aprendizagem inicial,** especifique os pesos do nó no início do processo de aprendizagem.

8.  Para **o impulso,** especifique um peso a aplicar durante a aprendizagem a nós de iterações anteriores  

10. Selecione a opção **de exemploshuffle** para baralhar casos entre iterações. Se desseleccionar esta opção, os casos são processados exatamente na mesma ordem sempre que executar o gasoduto.
  
11. Para **sementes aleatórias,** digite um valor a utilizar como semente.
  
     Especificar um valor de semente é útil quando pretende garantir a repetbilidade através de percursos do mesmo oleoduto.  Caso contrário, um valor do relógio do sistema é usado como semente, o que pode causar resultados ligeiramente diferentes cada vez que executa o gasoduto.
  
13. Adicione um conjunto de dados rotulado ao gasoduto e treine o modelo:

    + Se definir o modo Criar o **modo de formação** para **um parâmetro único,** ligue um conjunto de dados marcado e o módulo [Modelo de Comboio.](train-model.md)  
  
    + Se definir O modo Criar o **modo de formação** para o intervalo do **parâmetro,** ligue um conjunto de dados marcado e treine o modelo utilizando [hiperparâmetros do Modelo tune](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se passar uma gama de parâmetros para [o Modelo de Comboio,](train-model.md)utiliza apenas o valor predefinido na lista de parâmetros individuais.  
    > 
    > Se passar um único conjunto de valores de parâmetros para o módulo de [hiperparâmetros](tune-model-hyperparameters.md) do Modelo tune, quando espera uma gama de definições para cada parâmetro, ignora os valores e utiliza os valores predefinidos para o aluno.  
    > 
    > Se selecionar a opção Gama de **Parâmetros** e introduzir um único valor para qualquer parâmetro, esse valor único especificado é utilizado ao longo da varredura, mesmo que outros parâmetros alterem uma gama de valores.  
  
14. Submeta o oleoduto.

## <a name="results"></a>Resultados

Após o treino estar completo:

+ Para guardar uma imagem do modelo treinado, selecione o separador **Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone do conjunto de **dados Register** para salvar o modelo como um módulo reutilizável.

+ Para utilizar o modelo para a pontuação, adicione o módulo **Score Model** a um pipeline.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
