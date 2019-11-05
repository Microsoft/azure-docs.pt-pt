---
title: 'Regressão de rede neural: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo de regressão de rede neural no Azure Machine Learning para criar um modelo de regressão usando um algoritmo de rede neural personalizável.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: d5cc0f06e9fb95894df30af1322d47337f51e314
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466005"
---
# <a name="neural-network-regression-module"></a>Módulo de regressão de rede neural

*Cria um modelo de regressão usando um algoritmo de rede neural*  
  
 Categoria: Machine Learning/inicializar modelo/regressão
  
## <a name="module-overview"></a>Visão geral do módulo  

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use este módulo para criar um modelo de regressão usando um algoritmo de rede neural personalizável.
  
 Embora as redes neurais sejam amplamente conhecidas para uso em problemas complexos de aprendizado e modelagem, como reconhecimento de imagem, elas são facilmente adaptadas a problemas de regressão. Qualquer classe de modelos estatísticos pode ser chamada de rede neural se usar pesos adaptáveis e puder aproximar funções não lineares de suas entradas. Portanto, a regressão de rede neural é adequada para problemas em que um modelo de regressão mais tradicional não pode se ajustar a uma solução.
  
 A regressão de rede neural é um método de aprendizado supervisionado e, portanto, requer um conjunto de informações *marcado*, que inclui uma coluna de rótulo. Como um modelo de regressão prevê um valor numérico, a coluna de rótulo deve ser um tipo de dados numérico.  
  
 Você pode treinar o modelo fornecendo o modelo e o conjunto de dados marcado como uma entrada para [treinar o modelo](./train-model.md). O modelo treinado pode então ser usado para prever valores para os novos exemplos de entrada.  
  
## <a name="configure-neural-network-regression"></a>Configurar a regressão de rede neural 

As redes neurais podem ser amplamente personalizadas. Esta seção descreve como criar um modelo usando dois métodos:
  
+ [Criar um modelo de rede neural usando a arquitetura padrão](#bkmk_DefaultArchitecture)  
  
    Se você aceitar a arquitetura de rede neural padrão, use o painel **Propriedades** para definir parâmetros que controlam o comportamento da rede neural, como o número de nós na camada oculta, a taxa de aprendizado e a normalização.

    Comece aqui se você for novo nas redes neurais. O módulo dá suporte a muitas personalizações, bem como ao ajuste de modelo, sem conhecimento profundo das redes neurais. 

+ Definir uma arquitetura personalizada para uma rede neural 

    Use esta opção se você quiser adicionar camadas ocultas extras ou personalizar totalmente a arquitetura de rede, suas conexões e funções de ativação.
    
    Essa opção é melhor se você já estiver um pouco familiarizado com redes neurais. Você usa a linguagem net # para definir a arquitetura de rede.  

##  <a name="bkmk_DefaultArchitecture"></a>Criar um modelo de rede neural usando a arquitetura padrão

1.  Adicione o módulo de **regressão de rede neural** ao seu pipeline no designer. Você pode encontrar esse módulo em **Machine Learning**, **inicializar**, na categoria **regressão** . 
  
2. Indique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .  
  
    -   **Parâmetro único**: escolha esta opção se você já souber como deseja configurar o modelo.  

3.  Em **especificação de camada oculta**, selecione **caso totalmente conectado**. Essa opção cria um modelo usando a arquitetura de rede neural padrão, que, para um modelo de regressão de rede neural, tem estes atributos:  
  
    + A rede tem exatamente uma camada oculta.
    + A camada de saída é totalmente conectada à camada oculta e a camada oculta é totalmente conectada à camada de entrada.
    + O número de nós na camada oculta pode ser definido pelo usuário (o valor padrão é 100).  
  
    Como o número de nós na camada de entrada é determinado pelo número de recursos nos dados de treinamento, em um modelo de regressão pode haver apenas um nó na camada de saída.  
  
4. Para **número de nós ocultos**, digite o número de nós ocultos. O padrão é uma camada oculta com nós 100. (Essa opção não estará disponível se você definir uma arquitetura personalizada usando net #.)
  
5.  Para **taxa de aprendizagem**, digite um valor que defina a etapa realizada em cada iteração, antes da correção. Um valor maior para a taxa de aprendizagem pode fazer com que o modelo seja convergido mais rapidamente, mas pode Sobreusar mínimo locais.

6.  Para **número de iterações de aprendizado**, especifique o número máximo de vezes que o algoritmo processa os casos de treinamento.

7.  Para * * o diâmetro inicial dos pesos de aprendizado, digite um valor que determina os pesos do nó no início do processo de aprendizado.

8.  Para **a dinâmica**, digite um valor a ser aplicado durante o aprendizado como um peso em nós de iterações anteriores.

10. Selecione a opção, **embaralhar exemplos**, para alterar a ordem dos casos entre as iterações. Se você desmarcar essa opção, os casos serão processados exatamente na mesma ordem toda vez que você executar o pipeline.
  
11. Para a **semente de número aleatório**, você pode, opcionalmente, digitar um valor a ser usado como a semente. A especificação de um valor de semente é útil quando você deseja garantir a capacidade de repetição entre execuções do mesmo pipeline.
  
13. Conecte um conjunto de uma de treinamento e um dos [módulos de treinamento](module-reference.md): 
  
    -   Se você definir **criar modo de instrutor** como **parâmetro único**, use [treinar modelo](./train-model.md).  
  
   
14. Executar o pipeline.  

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver um resumo dos parâmetros do modelo, junto com os pesos do recurso aprendidos do treinamento e outros parâmetros da rede neural, clique com o botão direito do mouse na saída do [modelo](./train-model.md)de treino e selecione **Visualizar**.  

+ Para salvar um instantâneo do modelo treinado, clique com o botão direito do mouse na saída do **modelo treinado** e selecione **salvar como modelo treinado**. Esse modelo não é atualizado em execuções sucessivas do mesmo pipeline.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 