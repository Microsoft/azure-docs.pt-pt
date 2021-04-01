---
title: 'Regressão da rede neural: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de regressão da rede neural em Azure Machine Learning para criar um modelo de regressão utilizando um algoritmo de rede neural personalizável..
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 403576454615effeb53651b51679681422b08e9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "90890355"
---
# <a name="neural-network-regression-module"></a>Módulo de regressão da rede neural

*Cria um modelo de regressão usando um algoritmo de rede neural*  
  
 Categoria: Machine Learning / Initialize Model / Regression
  
## <a name="module-overview"></a>Visão geral do módulo  

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para criar um modelo de regressão utilizando um algoritmo de rede neural personalizável.
  
 Embora as redes neurais sejam amplamente conhecidas pelo uso em problemas complexos de aprendizagem profunda e modelação, tais como o reconhecimento de imagem, são facilmente adaptados a problemas de regressão. Qualquer classe de modelos estatísticos pode ser denominada rede neural se usar pesos adaptativos e pode aproximar funções não lineares das suas entradas. Assim, a regressão da rede neural é adequada a problemas em que um modelo de regressão mais tradicional não consegue encaixar numa solução.
  
 A regressão da rede neural é um método de aprendizagem supervisionado, pelo que requer um *conjunto de dados marcado,* que inclui uma coluna de etiquetas. Como um modelo de regressão prevê um valor numérico, a coluna de etiqueta deve ser um tipo de dado numérico.  
  
 Pode treinar o modelo fornecendo o modelo e o conjunto de dados marcados como uma entrada para [o Modelo de Comboio.](./train-model.md) O modelo treinado pode então ser usado para prever valores para os novos exemplos de entrada.  
  
## <a name="configure-neural-network-regression"></a>Configurar a regressão da rede neural 

As redes neuronais podem ser extensivamente personalizadas. Esta secção descreve como criar um modelo utilizando dois métodos:
  
+ [Criar um modelo de rede neural usando a arquitetura padrão](#bkmk_DefaultArchitecture)  
  
    Se aceitar a arquitetura de rede neural padrão, use o painel **de Propriedades** para definir parâmetros que controlem o comportamento da rede neural, como o número de nós na camada oculta, taxa de aprendizagem e normalização.

    Comece aqui se for novo em redes neuronais. O módulo suporta muitas personalizações, bem como afinação de modelos, sem profundo conhecimento de redes neurais. 

+ Defina uma arquitetura personalizada para uma rede neural 

    Utilize esta opção se pretender adicionar camadas extra escondidas ou personalizar totalmente a arquitetura da rede, as suas ligações e funções de ativação.
    
    Esta opção é melhor se já estiver familiarizado com redes neurais. Usa o idioma Net# para definir a arquitetura da rede.  

##  <a name="create-a-neural-network-model-using-the-default-architecture"></a><a name="bkmk_DefaultArchitecture"></a> Criar um modelo de rede neural usando a arquitetura padrão

1.  Adicione o módulo **de regressão da rede neural** ao seu oleoduto no designer. Pode encontrar este módulo na categoria **Machine Learning**, **Initialize,** na categoria **Regression.** 
  
2. Indique como pretende que o modelo seja treinado, definindo a opção **modo de formação Create.**  
  
    -   **Parâmetro único**: Escolha esta opção se já sabe como pretende configurar o modelo.

    -   **Intervalo de parâmetros**: Selecione esta opção se não tiver a certeza dos melhores parâmetros e pretender fazer uma varredura de parâmetros. Selecione uma gama de valores para iterar e o [Tune Model Hyperparameters](tune-model-hyperparameters.md) itera sobre todas as combinações possíveis das configurações fornecidas para determinar os hiperparmetros que produzem os melhores resultados.   

3.  Na **especificação da camada escondida,** selecione **caixa totalmente ligada**. Esta opção cria um modelo utilizando a arquitetura de rede neural padrão, que para um modelo de regressão da rede neural, tem estes atributos:  
  
    + A rede tem exatamente uma camada escondida.
    + A camada de saída está totalmente ligada à camada escondida e a camada escondida está totalmente ligada à camada de entrada.
    + O número de nós na camada oculta pode ser definido pelo utilizador (o valor predefinido é de 100).  
  
    Como o número de nós na camada de entrada é determinado pelo número de funcionalidades nos dados de treino, num modelo de regressão só pode haver um nó na camada de saída.  
  
4. Para **o número de nós ocultos,** digite o número de nós ocultos. O padrão é uma camada escondida com 100 nós. (Esta opção não está disponível se definir uma arquitetura personalizada usando Net#.)
  
5.  Para **a taxa de aprendizagem,** escreva um valor que defina o passo dado em cada iteração, antes da correção. Um valor maior para a taxa de aprendizagem pode fazer com que o modelo converjam mais rapidamente, mas pode ultrapassar o minima local.

6.  Para **o número de iterações de aprendizagem, especifique** o número máximo de vezes que o algoritmo processa os casos de treino.


8.  Para **o momento,** escreva um valor a aplicar durante a aprendizagem como um peso sobre nós de iterações anteriores.

10. Selecione a opção, **Shuffle exemplos**, para alterar a ordem dos casos entre iterações. Se desmarcar esta opção, os casos são processados exatamente da mesma ordem sempre que o oleoduto.
  
11. Para **sementes de número aleatório,** pode escrever opcionalmente um valor a utilizar como semente. Especificar um valor de semente é útil quando pretender garantir a repetibilidade através de percursos do mesmo oleoduto.
  
13. Ligue um conjunto de dados de formação e treine o modelo:

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

- Para guardar uma imagem do modelo treinado, selecione o **separador Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone **do conjunto de dados registar** para guardar o modelo como um módulo reutilizável.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 