---
title: 'Regressão de rede neural: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de regressão de rede Neural no serviço Azure Machine Learning para criar um modelo de regressão usando um algoritmo de rede neural personalizável....
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bc6a7505ab09e929e5add61eea687f871aedf242
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029314"
---
# <a name="neural-network-regression-module"></a>Módulo de regressão de rede neural

*Cria um modelo de regressão usando um algoritmo de rede neural*  
  
 Categoria: Machine Learning / inicializar modelo / regressão
  
## <a name="module-overview"></a>Descrição geral do módulo  

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para criar um modelo de regressão usando um algoritmo de rede neural personalizáveis.
  
 Embora as redes neurais são muito conhecidas para uso em problemas complexos, como reconhecimento de imagem de modelagem e de aprendizagem profunda, eles estão adaptados facilmente em problemas de regressão. Qualquer classe de modelos de estatísticos pode ser denominadas como uma rede neural se utilizar pesos adaptáveis e pode fazer uma aproximação de funções não-lineares das respetivas entradas. Assim, regressão de rede neural é adequada para onde um modelo de regressão mais tradicional não couber uma solução de problemas.
  
 Regressão de rede neural é um método de aprendizagem supervisionada e, portanto, requer uma *etiquetados de conjunto de dados*, que inclui uma coluna de etiqueta. Uma vez que um modelo de regressão prevê um valor numérico, a coluna de etiqueta tem de ser um tipo de dados numéricos.  
  
 Pode preparar o modelo, fornecendo o modelo e o conjunto de dados marcado como entrada para [modelo de formação](./train-model.md). O modelo preparado, em seguida, pode ser utilizado para prever valores para os exemplos de entrada novo.  
  
## <a name="configure-neural-network-regression"></a>Configurar a rede Neural regressão 

As redes neurais podem ser personalizadas amplamente. Esta secção descreve como criar um modelo através de dois métodos:
  
+ [Criar um modelo de rede neural usando a arquitetura de padrão](#bkmk_DefaultArchitecture)  
  
    Se aceitar a arquitetura de rede neural padrão, utilize o **propriedades** painel para definir os parâmetros que controlam o comportamento da rede neural, como o número de nós na camada oculta, taxa de aprendizagem e normalização.

    Comece por aqui se estiver familiarizado com as redes neurais. O módulo suporta muitas personalizações, bem como modelo de otimização, sem o conhecimento profundo das redes neurais. 

+ Definir uma arquitetura personalizada para uma rede neural 

    Utilize esta opção se pretender adicionar camadas ocultas extra ou personalizar por completo as funções de arquitetura, as suas ligações e ativação de rede.
    
    Esta opção é melhor se já esteja minimamente familiarizado com as redes neurais. Utilize a linguagem Net # para definir a arquitetura de rede.  

##  <a name="bkmk_DefaultArchitecture"></a> Criar um modelo de rede neural usando a arquitetura de padrão
  
1.  Adicionar a **regressão de rede Neural** módulo à sua experimentação na interface. Pode encontrar este módulo sob **Machine Learning**, **inicializar**, no **regressão** categoria. 
  
2. Indicar como pretende que o modelo de formação, definindo a **modo de instrutor de criação** opção.  
  
    -   **Único parâmetro**: Escolha esta opção se já sabe como pretende configurar o modelo.  

3.  Na **oculta a especificação de camada**, selecione **totalmente conectada caso**. Esta opção cria um modelo usando a arquitetura de rede neural padrão, que, para um modelo de regressão de rede neural, tenha estes atributos:  
  
    + A rede tem exatamente uma camada oculta.
    + A camada de saída é totalmente conectada aos camada oculta e camada oculta é totalmente conectada à camada de entrada.
    + O número de nós na camada oculta pode ser definido pelo utilizador (o valor predefinido é 100).  
  
    Como o número de nós na camada de entrada é determinado pelo número de recursos nos dados de treinamento, num modelo de regressão pode haver apenas um nó na camada de saída.  
  
4. Para **número de nós ocultos**, escreva o número de nós ocultos. A predefinição é uma camada oculta com 100 nós. (Esta opção não está disponível se definir uma arquitetura personalizada com o Net #.)
  
5.  Para **taxa de aprendizagem**, escreva um valor que define o passo efetuado em cada iteração, antes de correção. Um valor maior para a taxa de aprendizagem pode fazer com que o modelo convergir mais rapidamente, mas ele pode overshoot mínimas locais.

6.  Para **número de iterações de aprendizagem**, especifique o número máximo de vezes que o algoritmo processa os casos de treinamento.

7.  Para * * a aprendizagem inicial de pesos diâmetro, escreva um valor que determina os pesos de nó no início do processo de aprendizado.

8.  Para **a dinâmica**, escreva um valor para aplicar durante o aprendizado de como um peso em nós de iterações anteriores.

10. Selecione a opção **misturar os exemplos**, para alterar a ordem dos casos, entre as iterações. Se desmarcar esta opção, casos são processados por ordem exatamente o mesmo sempre que executar a experimentação.
  
11. Para **seed número aleatório**, opcionalmente, pode escrever um valor para utilizar como a semente. Valor especificando uma semente é útil quando pretende garantir a capacidade de repetição em execuções da experimentação mesmo.
  
13. Ligar um conjunto de dados de treinamento e uma da [módulos de treinamento](module-reference.md): 
  
    -   Se definir **modo de instrutor de criação** ao **único parâmetro**, utilize [Train Model](./train-model.md).  
  
   
14. Execute a experimentação.  

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver um resumo dos parâmetros do modelo, juntamente com a funcionalidade de pesos aprendidos de treinamento e outros parâmetros da rede neural, com o botão direito a saída de [Train Model](./train-model.md)e selecione **Visualize**.  

+ Para guardar um instantâneo do modelo preparado, com o botão direito a **modelo Trained** de saída e selecione **guardar como modelo preparado**. Este modelo não é atualizado em sucessivas execuções da experimentação mesmo.


## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 