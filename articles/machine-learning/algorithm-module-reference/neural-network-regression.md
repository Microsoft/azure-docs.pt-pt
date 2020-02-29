---
title: 'Regressão da rede neural: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de regressão da rede neural em Azure Machine Learning para criar um modelo de regressão usando um algoritmo de rede neural personalizável.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: d1e93c18b13e7171274eda2a7e8d07eefbefb592
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920387"
---
# <a name="neural-network-regression-module"></a>Módulo de regressão da rede neural

*Cria um modelo de regressão usando um algoritmo de rede neural*  
  
 Categoria: Machine Learning / Initialize Model / Regression
  
## <a name="module-overview"></a>Visão geral do módulo  

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para criar um modelo de regressão utilizando um algoritmo de rede neural personalizável.
  
 Embora as redes neurais sejam amplamente conhecidas para uso em deep learning e modelação de problemas complexos como o reconhecimento de imagem, são facilmente adaptadas a problemas de regressão. Qualquer classe de modelos estatísticos pode ser denominada uma rede neural se utilizar pesos adaptativos e pode aproximar funções não lineares das suas inputs. Assim, a regressão da rede neural é adequada a problemas em que um modelo de regressão mais tradicional não pode caber numa solução.
  
 A regressão da rede neural é um método de aprendizagem supervisionado e, portanto, requer um conjunto de *dados marcado,* que inclui uma coluna de etiquetas. Como um modelo de regressão prevê um valor numérico, a coluna de etiquetas deve ser um tipo de dados numéricos.  
  
 Pode treinar o modelo fornecendo o modelo e o conjunto de dados marcadocomo entrada para [o Modelo de Comboio](./train-model.md). O modelo treinado pode então ser usado para prever valores para os novos exemplos de entrada.  
  
## <a name="configure-neural-network-regression"></a>Reconfigurar a regressão da rede neural 

As redes neuronais podem ser extensivamente personalizadas. Esta secção descreve como criar um modelo usando dois métodos:
  
+ [Criar um modelo de rede neural usando a arquitetura padrão](#bkmk_DefaultArchitecture)  
  
    Se aceitar a arquitetura da rede neural padrão, utilize o painel **Properties** para definir parâmetros que controlam o comportamento da rede neural, como o número de nós na camada escondida, taxa de aprendizagem e normalização.

    Comece aqui se for novo em redes neurais. O módulo suporta muitas personalizações, bem como a sintonização do modelo, sem profundo conhecimento das redes neurais. 

+ Defina uma arquitetura personalizada para uma rede neural 

    Utilize esta opção se pretender adicionar camadas extra escondidas, ou personalizar totalmente a arquitetura de rede, as suas ligações e funções de ativação.
    
    Esta opção é melhor se já estiver familiarizado com redes neurais. Usa a linguagem Net# para definir a arquitetura da rede.  

##  <a name="bkmk_DefaultArchitecture"></a>Criar um modelo de rede neural usando a arquitetura padrão

1.  Adicione o módulo **de regressão** da Rede Neural ao seu pipeline no designer. Pode encontrar este módulo na categoria **Machine Learning**, **Initialize,** na categoria **Regression.** 
  
2. Indique como pretende que o modelo seja treinado, definindo a opção modo **de modo Criar.**  
  
    -   **Parâmetro único**: Escolha esta opção se já sabe como pretende configurar o modelo.

    -   **Gama de parâmetros**: Selecione esta opção se não tiver a certeza dos melhores parâmetros e pretenda fazer uma varredura de parâmetros. Selecione uma gama de valores para iterar mais, e o [Modelo tune Hyperparâmetros](tune-model-hyperparameters.md) iterates sobre todas as combinações possíveis das definições que forneceu para determinar os hiperparâmetros que produzem os melhores resultados.   

3.  Na **especificação da camada escondida,** selecione **caixa totalmente ligada**. Esta opção cria um modelo utilizando a arquitetura da rede neural padrão, que para um modelo de regressão da rede neural, tem estes atributos:  
  
    + A rede tem exatamente uma camada escondida.
    + A camada de saída está totalmente ligada à camada escondida e a camada escondida está totalmente ligada à camada de entrada.
    + O número de nós na camada oculta pode ser definido pelo utilizador (o valor predefinido é de 100).  
  
    Como o número de nós na camada de entrada é determinado pelo número de funcionalidades nos dados de treino, num modelo de regressão só pode haver um nó na camada de saída.  
  
4. Para **o número de nós escondidos,** digite o número de nós escondidos. O padrão é uma camada escondida com 100 nódosos. (Esta opção não está disponível se definir uma arquitetura personalizada usando net#.)
  
5.  Para **a taxa de aprendizagem,** digite um valor que defina o passo dado em cada iteração, antes da correção. Um valor maior para a taxa de aprendizagem pode fazer com que o modelo converta mais rapidamente, mas pode ultrapassar o minima local.

6.  Para **o número de iterações de aprendizagem,** especifique o número máximo de vezes que o algoritmo processa os casos de treino.


8.  Para **o impulso,** escreva um valor a aplicar durante a aprendizagem como um peso em nós de iterações anteriores.

10. Selecione a opção, **exemplos shuffle,** para alterar a ordem dos casos entre iterações. Se desseleccionar esta opção, os casos são processados exatamente na mesma ordem sempre que executar o gasoduto.
  
11. Para **sementes aleatórias,** pode escrever opcionalmente um valor para usar como semente. Especificar um valor de semente é útil quando pretende garantir a repetbilidade através de percursos do mesmo oleoduto.
  
13. Ligue um conjunto de dados de treino e um dos módulos de [treino:](module-reference.md) 
  
    -   Se definir **criar o modo de formação** para um parâmetro **único,** utilize o [Modelo de Comboio](./train-model.md).  
  
   
14. Executar o pipeline.  

## <a name="results"></a>Resultados

Após o treino estar completo:

- Para guardar uma imagem do modelo treinado, selecione o separador **Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone do conjunto de **dados Register** para salvar o modelo como um módulo reutilizável.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 