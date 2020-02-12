---
title: 'Regressão de rede neural: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo de regressão de rede neural no Azure Machine Learning para criar um modelo de regressão usando um algoritmo de rede neural personalizável.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 97095e4802373a1dd3f7b795c2fe567b778d2d5e
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153830"
---
# <a name="neural-network-regression-module"></a>Módulo de regressão de rede neural

*Cria um modelo de regressão usando um algoritmo de rede neural*  
  
 Categoria: Machine Learning/inicializar modelo/regressão
  
## <a name="module-overview"></a>Visão geral do módulo  

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para criar um modelo de regressão usando um algoritmo de rede neural personalizável.
  
 Embora as redes neurais sejam amplamente conhecidas para uso em problemas complexos de aprendizado e modelagem, como reconhecimento de imagem, elas são facilmente adaptadas a problemas de regressão. Qualquer classe de modelos estatísticos pode ser chamada de rede neural se usar pesos adaptáveis e puder aproximar funções não lineares de suas entradas. Portanto, a regressão de rede neural é adequada para problemas em que um modelo de regressão mais tradicional não pode se ajustar a uma solução.
  
 A regressão da rede neural é um método de aprendizagem supervisionado e, portanto, requer um conjunto de *dados marcado,* que inclui uma coluna de etiquetas. Como um modelo de regressão prevê um valor numérico, a coluna de rótulo deve ser um tipo de dados numérico.  
  
 Pode treinar o modelo fornecendo o modelo e o conjunto de dados marcadocomo entrada para [o Modelo de Comboio](./train-model.md). O modelo treinado pode então ser usado para prever valores para os novos exemplos de entrada.  
  
## <a name="configure-neural-network-regression"></a>Configurar a regressão de rede neural 

As redes neurais podem ser amplamente personalizadas. Esta seção descreve como criar um modelo usando dois métodos:
  
+ [Criar um modelo de rede neural usando a arquitetura padrão](#bkmk_DefaultArchitecture)  
  
    Se aceitar a arquitetura da rede neural padrão, utilize o painel **Properties** para definir parâmetros que controlam o comportamento da rede neural, como o número de nós na camada escondida, taxa de aprendizagem e normalização.

    Comece aqui se você for novo nas redes neurais. O módulo dá suporte a muitas personalizações, bem como ao ajuste de modelo, sem conhecimento profundo das redes neurais. 

+ Definir uma arquitetura personalizada para uma rede neural 

    Use esta opção se você quiser adicionar camadas ocultas extras ou personalizar totalmente a arquitetura de rede, suas conexões e funções de ativação.
    
    Essa opção é melhor se você já estiver um pouco familiarizado com redes neurais. Você usa a linguagem net # para definir a arquitetura de rede.  

##  <a name="bkmk_DefaultArchitecture"></a>Criar um modelo de rede neural usando a arquitetura padrão

1.  Adicione o módulo **de regressão** da Rede Neural ao seu pipeline no designer. Pode encontrar este módulo na categoria **Machine Learning**, **Initialize,** na categoria **Regression.** 
  
2. Indique como pretende que o modelo seja treinado, definindo a opção modo **de modo Criar.**  
  
    -   **Parâmetro único**: Escolha esta opção se já sabe como pretende configurar o modelo.  

3.  Na **especificação da camada escondida,** selecione **caixa totalmente ligada**. Essa opção cria um modelo usando a arquitetura de rede neural padrão, que, para um modelo de regressão de rede neural, tem estes atributos:  
  
    + A rede tem exatamente uma camada oculta.
    + A camada de saída é totalmente conectada à camada oculta e a camada oculta é totalmente conectada à camada de entrada.
    + O número de nós na camada oculta pode ser definido pelo usuário (o valor padrão é 100).  
  
    Como o número de nós na camada de entrada é determinado pelo número de recursos nos dados de treinamento, em um modelo de regressão pode haver apenas um nó na camada de saída.  
  
4. Para **o número de nós escondidos,** digite o número de nós escondidos. O padrão é uma camada oculta com nós 100. (Essa opção não estará disponível se você definir uma arquitetura personalizada usando net #.)
  
5.  Para **a taxa de aprendizagem,** digite um valor que defina o passo dado em cada iteração, antes da correção. Um valor maior para a taxa de aprendizagem pode fazer com que o modelo seja convergido mais rapidamente, mas pode Sobreusar mínimo locais.

6.  Para **o número de iterações de aprendizagem,** especifique o número máximo de vezes que o algoritmo processa os casos de treino.

7.  Para * * o diâmetro inicial dos pesos de aprendizado, digite um valor que determina os pesos do nó no início do processo de aprendizado.

8.  Para **o impulso,** escreva um valor a aplicar durante a aprendizagem como um peso em nós de iterações anteriores.

10. Selecione a opção, **exemplos shuffle,** para alterar a ordem dos casos entre iterações. Se você desmarcar essa opção, os casos serão processados exatamente na mesma ordem toda vez que você executar o pipeline.
  
11. Para **sementes aleatórias,** pode escrever opcionalmente um valor para usar como semente. A especificação de um valor de semente é útil quando você deseja garantir a capacidade de repetição entre execuções do mesmo pipeline.
  
13. Ligue um conjunto de dados de treino e um dos módulos de [treino:](module-reference.md) 
  
    -   Se definir **criar o modo de formação** para um parâmetro **único,** utilize o [Modelo de Comboio](./train-model.md).  
  
   
14. Executar o pipeline.  

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

- Para guardar uma imagem do modelo treinado, selecione o separador **Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone do conjunto de **dados Register** para salvar o modelo como um módulo reutilizável.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 