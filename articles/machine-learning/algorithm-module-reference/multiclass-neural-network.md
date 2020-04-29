---
title: 'Rede Neural Multiclasse: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo multiclasse da Rede Neural em Azure Machine Learning para criar um modelo de rede neural que pode ser usado para prever um alvo que tem múltiplos valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: c7fb02681936f575153f23ac60db43cc75541075
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82137761"
---
# <a name="multiclass-neural-network-module"></a>Módulo de Rede Neural Multiclasse

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para criar um modelo de rede neural que pode ser usado para prever um alvo que tem múltiplos valores. 

Por exemplo, redes neurais deste tipo podem ser usadas em tarefas complexas de visão computacional, tais como reconhecimento de dígitos ou letras, classificação de documentos e reconhecimento de padrões.

A classificação utilizando redes neurais é um método de aprendizagem supervisionado e, portanto, requer um conjunto de *dados marcado* que inclua uma coluna de etiquetas.

Pode treinar o modelo fornecendo o modelo e o conjunto de dados marcadocomo entrada para [o Modelo de Comboio](./train-model.md). O modelo treinado pode então ser usado para prever valores para os novos exemplos de entrada.  

## <a name="about-neural-networks"></a>Sobre redes neurais

Uma rede neural é um conjunto de camadas interligadas. As inputs são a primeira camada, e estão ligadas a uma camada de saída por um gráfico acíclico composto por bordas e nós ponderados.

Entre as camadas de entrada e saída pode inserir várias camadas escondidas. A maioria das tarefas preditivas pode ser realizada facilmente com apenas uma ou algumas camadas escondidas. No entanto, pesquisas recentes mostraram que redes neuronais profundas (DNN) com muitas camadas podem ser eficazes em tarefas complexas, como imagem ou reconhecimento de voz. As camadas sucessivas são usadas para modelar níveis crescentes de profundidade semântica.

A relação entre inputs e saídas é aprendida com a formação da rede neural nos dados de entrada. A direção do gráfico procede das inputs através da camada escondida e da camada de saída. Todos os nós em camada são ligados pelas bordas ponderadas aos nós na camada seguinte.

Para calcular a saída da rede para uma determinada entrada, calcula-se um valor em cada nó nas camadas ocultas e na camada de saída. O valor é definido calculando a soma ponderada dos valores dos nódosos da camada anterior. Uma função de ativação é então aplicada a essa soma ponderada.

## <a name="configure-multiclass-neural-network"></a>Configure rede neural multiclasse

1. Adicione o módulo **multiclasse da Rede Neural** ao seu pipeline no designer. Pode encontrar este módulo na categoria **Machine Learning**, **Initialize,** na categoria **Classificação.**

2. **Criar o modo de treinador**: Utilize esta opção para especificar como pretende que o modelo seja treinado:

    - **Parâmetro único**: Escolha esta opção se já sabe como pretende configurar o modelo.

    - **Gama de parâmetros**: Selecione esta opção se não tiver a certeza dos melhores parâmetros e pretenda fazer uma varredura de parâmetros. Selecione uma gama de valores para iterar mais, e o [Modelo tune Hyperparâmetros](tune-model-hyperparameters.md) iterates sobre todas as combinações possíveis das definições que forneceu para determinar os hiperparâmetros que produzem os melhores resultados.  

3. **Especificação**da camada escondida : Selecione o tipo de arquitetura de rede para criar.

    - **Caso totalmente ligado**: Selecione esta opção para criar um modelo utilizando a arquitetura de rede neural predefinida. Para os modelos de rede neural multiclasse, os predefinições são os seguintes:

        - Uma camada escondida
        - A camada de saída está totalmente ligada à camada escondida.
        - A camada escondida está totalmente ligada à camada de entrada.
        - O número de nós na camada de entrada é determinado pelo número de funcionalidades nos dados de formação.
        - O número de nós na camada escondida pode ser definido pelo utilizador. A predefinição é 100.
        - O número de nós na camada de saída depende do número de classes.
  
   

5. **Número de nós escondidos**: Esta opção permite personalizar o número de nós escondidos na arquitetura padrão. Digite o número de nós escondidos. O padrão é uma camada escondida com 100 nódosos.

6. **A taxa de aprendizagem**: Defina o tamanho do passo dado em cada iteração, antes da correção. Um valor maior para a taxa de aprendizagem pode fazer com que o modelo converta mais rapidamente, mas pode ultrapassar o minima local.

7. **Número de iterações de aprendizagem**: Especifique o número máximo de vezes que o algoritmo deve processar os casos de treino.

8. Diâmetro dos pesos de **aprendizagem inicial**: Especifique os pesos do nó no início do processo de aprendizagem.

9. **O impulso**: Especifique um peso a aplicar durante a aprendizagem aos nódosos de iterações anteriores.
  
11. **Exemplos**de shuffle : Selecione esta opção para baralhar casos entre iterações.

    Se desseleccionar esta opção, os casos são processados exatamente na mesma ordem sempre que executar o gasoduto.

12. **Semente**de número aleatório: Digite um valor a utilizar como a semente, se pretender garantir a repetbilidade através de percursos do mesmo gasoduto.

14. Treine o modelo:

    + Se definir o modo Criar o **modo de formação** para **um parâmetro único,** ligue um conjunto de dados marcado e o módulo [Modelo de Comboio.](train-model.md)  
  
    + Se definir O modo Criar o **modo de formação** para o intervalo do **parâmetro,** ligue um conjunto de dados marcado e treine o modelo utilizando [hiperparâmetros do Modelo tune](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se passar uma gama de parâmetros para [o Modelo de Comboio,](train-model.md)utiliza apenas o valor predefinido na lista de parâmetros individuais.  
    > 
    > Se passar um único conjunto de valores de parâmetros para o módulo de [hiperparâmetros](tune-model-hyperparameters.md) do Modelo tune, quando espera uma gama de definições para cada parâmetro, ignora os valores e utiliza os valores predefinidos para o aluno.  
    > 
    > Se selecionar a opção Gama de **Parâmetros** e introduzir um único valor para qualquer parâmetro, esse valor único especificado é utilizado ao longo da varredura, mesmo que outros parâmetros alterem uma gama de valores.  
  

## <a name="results"></a>Resultados

Após o treino estar completo:

- Para guardar uma imagem do modelo treinado, selecione o separador **Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone do conjunto de **dados Register** para salvar o modelo como um módulo reutilizável.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 