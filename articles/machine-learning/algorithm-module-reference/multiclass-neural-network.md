---
title: 'Rede Neural Multiclasse: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Multiclass Neural Network no designer de Aprendizagem automática Azure para prever um alvo que tenha valores multi-classes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: a4f7f8a7793f31ffbf2099cbfb314fc5097319f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421265"
---
# <a name="multiclass-neural-network-module"></a>Módulo multiclasse de rede neural

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para criar um modelo de rede neural que possa ser usado para prever um alvo que tenha vários valores. 

Por exemplo, redes neurais deste tipo podem ser usadas em tarefas complexas de visão computacional, tais como reconhecimento de dígitos ou letras, classificação de documentos e reconhecimento de padrões.

A classificação utilizando redes neurais é um método de aprendizagem supervisionado, e, portanto, requer um *conjunto de dados marcado* que inclui uma coluna de etiqueta.

Pode treinar o modelo fornecendo o modelo e o conjunto de dados marcados como uma entrada para [o Modelo de Comboio.](./train-model.md) O modelo treinado pode então ser usado para prever valores para os novos exemplos de entrada.  

## <a name="about-neural-networks"></a>Sobre redes neuronais

Uma rede neural é um conjunto de camadas interligadas. As entradas são a primeira camada, e estão ligadas a uma camada de saída por um gráfico acicílico composto por bordas e nós ponderados.

Entre as camadas de entrada e saída pode inserir várias camadas escondidas. A maioria das tarefas preditivas pode ser realizada facilmente com apenas uma ou algumas camadas escondidas. No entanto, pesquisas recentes mostraram que redes neuronais profundas (DNN) com muitas camadas podem ser eficazes em tarefas complexas como o reconhecimento de imagem ou de voz. As camadas sucessivas são usadas para modelar níveis crescentes de profundidade semântica.

A relação entre entradas e saídas é aprendida com o treino da rede neural nos dados de entrada. A direção do gráfico provém das entradas através da camada escondida e da camada de saída. Todos os nós numa camada são ligados pelas bordas ponderadas aos nós na camada seguinte.

Para calcular a saída da rede para uma determinada entrada, calcula-se um valor em cada nó nas camadas ocultas e na camada de saída. O valor é definido calculando a soma ponderada dos valores dos nós da camada anterior. Uma função de ativação é então aplicada a essa soma ponderada.

## <a name="configure-multiclass-neural-network"></a>Configure rede neural multiclasse

1. Adicione o módulo **MultiClass Neural Network** ao seu pipeline no designer. Pode encontrar este módulo na categoria **Machine Learning,** **Initialize,** na categoria **Classificação.**

2. **Criar o modo de treinador**: Utilize esta opção para especificar como pretende que o modelo seja treinado:

    - **Parâmetro único**: Escolha esta opção se já sabe como pretende configurar o modelo.

    - **Intervalo de parâmetros**: Selecione esta opção se não tiver a certeza dos melhores parâmetros e pretender fazer uma varredura de parâmetros. Selecione uma gama de valores para iterar e o [Tune Model Hyperparameters](tune-model-hyperparameters.md) itera sobre todas as combinações possíveis das configurações fornecidas para determinar os hiperparmetros que produzem os melhores resultados.  

3. **Especificação de camada escondida**: Selecione o tipo de arquitetura de rede para criar.

    - **Caso totalmente ligado**: Selecione esta opção para criar um modelo utilizando a arquitetura de rede neural padrão. Para os modelos de rede neural multiclasse, os padrão são os seguintes:

        - Uma camada escondida
        - A camada de saída está totalmente ligada à camada oculta.
        - A camada escondida está totalmente ligada à camada de entrada.
        - O número de nós na camada de entrada é determinado pelo número de funcionalidades nos dados de treino.
        - O número de nós na camada oculta pode ser definido pelo utilizador. A predefinição é 100.
        - O número de nós na camada de saída depende do número de classes.
  
   

5. **Número de nós ocultos**: Esta opção permite personalizar o número de nós ocultos na arquitetura padrão. Digite o número de nós escondidos. O padrão é uma camada escondida com 100 nós.

6. **Taxa de aprendizagem**: Definir o tamanho do passo dado em cada iteração, antes da correção. Um valor maior para a taxa de aprendizagem pode fazer com que o modelo converjam mais rapidamente, mas pode ultrapassar o minima local.

7. **Número de iterações de aprendizagem**: Especifique o número máximo de vezes que o algoritmo deve processar os casos de treino.

8. **O diâmetro dos pesos de aprendizagem inicial**: Especifique os pesos dos nós no início do processo de aprendizagem.

9. **O impulso**: Especifique um peso a aplicar durante a aprendizagem aos nós de iterações anteriores.
  
11. **Shuffle exemplos**: Selecione esta opção para baralhar casos entre iterações.

    Se desmarcar esta opção, os casos são processados exatamente da mesma ordem sempre que o oleoduto.

12. **Semente de número aleatório**: Digite um valor a utilizar como semente, se quiser garantir a repetibilidade através de percursos do mesmo oleoduto.

14. Treine o modelo:

    + Se definir **Criar o modo de treinador** para single **parâmetro,** conecte um conjunto de dados marcado e o módulo Modelo de [Comboio.](train-model.md)  
  
    + Se definir **Criar modo de treinador** para intervalo de **parâmetros,** ligue um conjunto de dados marcado e treine o modelo utilizando [hiperparmetros do modelo de sintonização](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se passar uma gama de parâmetros para [o Modelo de Comboio,](train-model.md)utiliza apenas o valor predefinido na lista de parâmetros únicos.  
    > 
    > Se passar um único conjunto de valores de parâmetros para o módulo [Tune Model Hyperparameters,](tune-model-hyperparameters.md) quando espera uma gama de definições para cada parâmetro, ignora os valores e utiliza os valores predefinidos para o aluno.  
    > 
    > Se selecionar a opção De Alcance de **Parâmetros** e introduzir um único valor para qualquer parâmetro, esse valor único especificado é utilizado ao longo da varredura, mesmo que outros parâmetros se alterem numa gama de valores.  
  

## <a name="results"></a>Resultados

Após o treino estar completo:

- Para guardar uma imagem do modelo treinado, selecione o **separador Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone **do conjunto de dados registar** para guardar o modelo como um módulo reutilizável.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 