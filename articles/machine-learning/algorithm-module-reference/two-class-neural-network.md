---
title: 'rede neural Two-Class: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Two-Class Rede Neural em Azure Machine Learning para criar um classificador binário.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 9131a2439facef00cae818bffef38e536a40a2fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421163"
---
# <a name="two-class-neural-network-module"></a>Módulo de Rede Neural Two-Class

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para criar um modelo de rede neural que possa ser usado para prever um alvo que tenha apenas dois valores.

A classificação utilizando redes neurais é um método de aprendizagem supervisionado, pelo que requer um *conjunto de dados marcado,* que inclui uma coluna de etiquetas. Por exemplo, pode usar este modelo de rede neural para prever resultados binários, tais como se um paciente tem ou não uma determinada doença, ou se uma máquina é suscetível de falhar dentro de uma janela de tempo especificada.  

Depois de definir o modelo, treine-o fornecendo um conjunto de dados marcado e o modelo como entrada para [o Modelo de Comboio.](./train-model.md) O modelo treinado pode então ser usado para prever valores para novas entradas.

### <a name="more-about-neural-networks"></a>Mais sobre redes neurais

Uma rede neural é um conjunto de camadas interligadas. As entradas são a primeira camada, e estão ligadas a uma camada de saída por um gráfico acicílico composto por bordas e nós ponderados.

Entre as camadas de entrada e saída pode inserir várias camadas escondidas. A maioria das tarefas preditivas pode ser realizada facilmente com apenas uma ou algumas camadas escondidas. No entanto, pesquisas recentes mostraram que redes neuronais profundas (DNN) com muitas camadas podem ser eficazes em tarefas complexas como o reconhecimento de imagem ou de voz. As camadas sucessivas são usadas para modelar níveis crescentes de profundidade semântica.

A relação entre entradas e saídas é aprendida com o treino da rede neural nos dados de entrada. A direção do gráfico provém das entradas através da camada escondida e da camada de saída. Todos os nós numa camada são ligados pelas bordas ponderadas aos nós na camada seguinte.

Para calcular a saída da rede para uma determinada entrada, calcula-se um valor em cada nó nas camadas ocultas e na camada de saída. O valor é definido calculando a soma ponderada dos valores dos nós da camada anterior. Uma função de ativação é então aplicada a essa soma ponderada.
  
## <a name="how-to-configure"></a>Como configurar

1.  Adicione o módulo **de rede neural de duas classes** ao seu oleoduto. Pode encontrar este módulo na categoria **Machine Learning,** **Initialize,** na categoria **Classificação.**  
  
2.  Especifique como pretende que o modelo seja treinado, definindo a opção **modo de formação Create.**  
  
    -   **Parâmetro único**: Escolha esta opção se já sabe como pretende configurar o modelo.

    -   **Intervalo de parâmetros**: Se não tiver a certeza dos melhores parâmetros, pode encontrar os parâmetros ideais utilizando o módulo [De Hiperparametros Tune Model.](tune-model-hyperparameters.md) Fornece alguma gama de valores, e o treinador itera sobre várias combinações das definições para determinar a combinação de valores que produz o melhor resultado.  

3.  Para **especificação de camada oculta,** selecione o tipo de arquitetura de rede para criar.  
  
    -   **Caso totalmente ligado**: Utiliza a arquitetura de rede neural padrão, definida para redes neuronais de duas classes da seguinte forma:
  
        -   Tem uma camada escondida.
  
        -   A camada de saída está totalmente ligada à camada escondida, e a camada escondida está totalmente ligada à camada de entrada.
  
        -   O número de nós na camada de entrada é igual ao número de funcionalidades nos dados de treino.
  
        -   O número de nós na camada escondida é definido pelo utilizador. O valor predefinido é 100.
  
        -   O número de nós é igual ao número de aulas. Para uma rede neural de duas classes, isto significa que todas as entradas devem mapear para um de dois nós na camada de saída.

5.  Para **a taxa de aprendizagem,** defina o tamanho do passo dado em cada iteração, antes da correção. Um valor maior para a taxa de aprendizagem pode fazer com que o modelo converjam mais rapidamente, mas pode ultrapassar o minima local.

6.  Para **o número de iterações de aprendizagem, especifique** o número máximo de vezes que o algoritmo deve processar os casos de treino.

7.  Para **o diâmetro inicial dos pesos de aprendizagem,** especifique os pesos dos nós no início do processo de aprendizagem.

8.  Para **o impulso**, especifique um peso a aplicar durante a aprendizagem aos nóns de iterações anteriores  

10. Selecione a opção **De exemplos Shuffle** para baralhar casos entre iterações. Se desmarcar esta opção, os casos são processados exatamente da mesma ordem sempre que o oleoduto.
  
11. Para **sementes de número aleatório,** digite um valor a utilizar como semente.
  
     Especificar um valor de semente é útil quando pretender garantir a repetibilidade através de percursos do mesmo oleoduto.  Caso contrário, um valor do relógio do sistema é usado como semente, o que pode causar resultados ligeiramente diferentes cada vez que você executar o pipeline.
  
13. Adicione um conjunto de dados rotulado ao oleoduto e treine o modelo:

    + Se definir **Criar o modo de treinador** para single **parâmetro,** conecte um conjunto de dados marcado e o módulo Modelo de [Comboio.](train-model.md)  
  
    + Se definir **Criar modo de treinador** para intervalo de **parâmetros,** ligue um conjunto de dados marcado e treine o modelo utilizando [hiperparmetros do modelo de sintonização](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se passar uma gama de parâmetros para [o Modelo de Comboio,](train-model.md)utiliza apenas o valor predefinido na lista de parâmetros únicos.  
    > 
    > Se passar um único conjunto de valores de parâmetros para o módulo [Tune Model Hyperparameters,](tune-model-hyperparameters.md) quando espera uma gama de definições para cada parâmetro, ignora os valores e utiliza os valores predefinidos para o aluno.  
    > 
    > Se selecionar a opção De Alcance de **Parâmetros** e introduzir um único valor para qualquer parâmetro, esse valor único especificado é utilizado ao longo da varredura, mesmo que outros parâmetros se alterem numa gama de valores.  
  
14. Envie o oleoduto.

## <a name="results"></a>Resultados

Após o treino estar completo:

+ Para guardar uma imagem do modelo treinado, selecione o **separador Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone **do conjunto de dados registar** para guardar o modelo como um módulo reutilizável.

+ Para utilizar o modelo para pontuar, adicione o módulo **'Marcar',** a um pipeline.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
