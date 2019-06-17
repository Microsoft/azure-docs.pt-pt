---
title: 'Duas classes rede Neural: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de duas classes de rede Neural no serviço Azure Machine Learning para criar um modelo de rede neural que pode ser utilizado para prever um destino que tem apenas dois valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7ea852fcd312c6f7b1b716278ed538b7accde5bd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029224"
---
# <a name="two-class-neural-network-module"></a>Módulo de duas classes de rede Neural

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para criar um modelo de rede neural que pode ser utilizado para prever um destino que tem apenas dois valores.

Classificação usando redes neurais é um método de aprendizagem supervisionada e, portanto, requer uma *etiquetados de conjunto de dados*, que inclui uma coluna de etiqueta. Por exemplo, poderia usar esse modelo de rede neural para prever os resultados binários, como se pretende ou não um paciente tem uma determinada doenças, ou se uma máquina é probabilidade de falhar numa janela de tempo especificada.  

Depois de definir o modelo, formá-lo ao fornecer um conjunto de dados marcado e o modelo como entrada para [modelo de formação](./train-model.md). O modelo preparado, em seguida, pode ser utilizado para prever valores para entradas de novo.

### <a name="more-about-neural-networks"></a>Mais informações sobre redes neurais

Uma rede neural é um conjunto de camadas interconectadas. As entradas são a primeira camada e estão ligadas a uma camada de saída por um gráficos acíclicos composto margens ponderadas e nós.

Entre as camadas de entrada e saídas, pode inserir várias camadas ocultas. Mais tarefas preditivas podem ser conseguidas facilmente com apenas um ou mais algumas camadas ocultas. No entanto, uma recente pesquisa mostrou que redes neurais profundas (DNN) com várias camadas podem ser eficazes em tarefas complexas, como reconhecimento de imagem ou de voz. As camadas sucessivas são utilizadas para modelar cada vez maiores de níveis de profundidade semântica.

A relação entre as entradas e saídas é aprendida treinando a rede neural nos dados de entrada. Continua a direção do gráfico de entradas por meio da camada oculta e para a camada de saída. Todos os nós numa camada estão ligados através de bordas ponderadas para nós a próxima camada.

Para computar a saída da rede para determinada entrada, um valor é calculado em cada nó nas camadas ocultas e na camada de saída. O valor for definido, calculando a soma ponderada dos valores de nós da camada de anterior. Uma função de ativação é aplicada, em seguida, para que a soma ponderada.
  
## <a name="how-to-configure"></a>Como configurar

1.  Adicionar a **duas classes de rede Neural** módulo à sua experimentação. Pode encontrar este módulo sob **Machine Learning**, **inicializar**, no **classificação** categoria.  
  
2.  Especifique como pretende que o modelo de formação, definindo a **modo de instrutor de criação** opção.  
  
    -   **Único parâmetro**: Escolha esta opção se já sabe como pretende configurar o modelo.  

3.  Para **oculta a especificação de camada**, selecione o tipo de arquitetura de rede para criar.  
  
    -   **Totalmente conectada caso**: Utiliza a arquitetura de rede neural padrão, definida para as redes neurais duas classes, da seguinte forma:
  
        -   Tem uma camada oculta.
  
        -   A camada de saída é totalmente conectada aos camada oculta e camada oculta é totalmente conectada à camada de entrada.
  
        -   O número de nós na camada de entrada for igual ao número de recursos nos dados de treinamento.
  
        -   O número de nós na camada oculta é definido pelo utilizador. O valor predefinido é 100.
  
        -   O número de nós for igual ao número de classes. Para uma rede neural de duas classes, isso significa que todas as entradas tem de mapear para um dos dois nós na camada de saída.

5.  Para **taxa de aprendizagem**, defina o tamanho da etapa executada em cada iteração, antes de correção. Um valor maior para a taxa de aprendizagem pode fazer com que o modelo convergir mais rapidamente, mas ele pode overshoot mínimas locais.

6.  Para **número de iterações de aprendizagem**, especifique o número máximo de vezes que o algoritmo deve processar os casos de treinamento.

7.  Para **da aprendizagem inicial de pesos diâmetro**, especifique os pesos de nó no início do processo de aprendizado.

8.  Para **a dinâmica**, especifique um peso para aplicar durante o aprendizado a nós de iterações anteriores  

10. Selecione o **misturar os exemplos** opção misturar os casos entre as iterações. Se desmarcar esta opção, casos são processados por ordem exatamente o mesmo sempre que executar a experimentação.
  
11. Para **seed número aleatório**, escreva um valor para utilizar como a semente.
  
     Valor especificando uma semente é útil quando pretende garantir a capacidade de repetição em execuções da experimentação mesmo.  Caso contrário, um valor de relógio do sistema é utilizado como a semente, o que pode causar resultados ligeiramente diferentes sempre que executar a experimentação.
  
13. Adicionar um conjunto de dados marcado para a experimentação e ligue-se de um da [módulos de treinamento](module-reference.md).  
  
    -   Se definir **modo de instrutor de criação** para **único parâmetro**, utilize o [Train Model](train-model.md) módulo.  
  
14. Execute a experimentação.

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver um resumo dos parâmetros do modelo, juntamente com a funcionalidade de pesos aprendidos de treinamento e outros parâmetros da rede neural, com o botão direito a saída de [Train Model](./train-model.md)e selecione **Visualize**.  

+ Para guardar um instantâneo do modelo preparado, com o botão direito a **modelo Trained** de saída e selecione **guardar como modelo preparado**. Este modelo não é atualizado em sucessivas execuções da experimentação mesmo.


## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 
