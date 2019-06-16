---
title: 'Rede Neural várias classes: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de várias classes de rede Neural no serviço Azure Machine Learning para criar um modelo de rede neural que pode ser utilizado para prever um destino que tem vários valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7bef225c001ebd9bbb9a45c8bcc301cfb49edb6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029389"
---
# <a name="multiclass-neural-network-module"></a>Módulo várias classes de rede Neural

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para criar um modelo de rede neural que pode ser utilizado para prever um destino que tem vários valores. 

Por exemplo, redes neurais desse tipo podem ser utilizadas nas tarefas de visão do computador complexas, como reconhecimento de dígitos ou letra, classificação e reconhecimento de padrões.

Classificação usando redes neurais é um método de aprendizagem supervisionada e, portanto, requer uma *etiquetados de conjunto de dados* que inclui uma coluna de etiqueta.

Pode preparar o modelo, fornecendo o modelo e o conjunto de dados marcado como entrada para [modelo de formação](./train-model.md). O modelo preparado, em seguida, pode ser utilizado para prever valores para os exemplos de entrada novo.  

## <a name="about-neural-networks"></a>Sobre as redes neurais

Uma rede neural é um conjunto de camadas interconectadas. As entradas são a primeira camada e estão ligadas a uma camada de saída por um gráficos acíclicos composto margens ponderadas e nós.

Entre as camadas de entrada e saídas, pode inserir várias camadas ocultas. Mais tarefas preditivas podem ser conseguidas facilmente com apenas um ou mais algumas camadas ocultas. No entanto, uma recente pesquisa mostrou que redes neurais profundas (DNN) com várias camadas podem ser eficazes em tarefas complexas, como reconhecimento de imagem ou de voz. As camadas sucessivas são utilizadas para modelar cada vez maiores de níveis de profundidade semântica.

A relação entre as entradas e saídas é aprendida treinando a rede neural nos dados de entrada. Continua a direção do gráfico de entradas por meio da camada oculta e para a camada de saída. Todos os nós numa camada estão ligados através de bordas ponderadas para nós a próxima camada.

Para computar a saída da rede para determinada entrada, um valor é calculado em cada nó nas camadas ocultas e na camada de saída. O valor for definido, calculando a soma ponderada dos valores de nós da camada de anterior. Uma função de ativação é aplicada, em seguida, para que a soma ponderada.

## <a name="configure-multiclass-neural-network"></a>Configurar a rede Neural várias classes

1. Adicionar a **várias classes de rede Neural** módulo à sua experimentação na interface. Pode encontrar este módulo sob **Machine Learning**, **inicializar**, no **classificação** categoria.

2. **Criar o modo de instrutor**: Utilize esta opção para especificar como pretende que o modelo a ser treinado:

    - **Único parâmetro**: Escolha esta opção se já sabe como pretende configurar o modelo.

    

3. **Oculto a especificação de camada**: Selecione o tipo de arquitetura de rede para criar.

    - **Totalmente conectada caso**: Selecione esta opção para criar um modelo usando a arquitetura de rede neural padrão. Para modelos de rede neural várias classes, as predefinições são os seguintes:

        - Uma camada oculta
        - A camada de saída é totalmente conectada aos camada oculta.
        - Camada oculta é totalmente conectada à camada de entrada.
        - O número de nós na camada de entrada é determinado pelo número de recursos nos dados de treinamento.
        - O número de nós na camada oculta pode ser definido pelo utilizador. A predefinição é 100.
        - O número de nós na camada de saída depende o número de classes.
  
   

5. **Número de nós ocultos**: Esta opção permite-lhe personalizar o número de nós ocultos na arquitetura de padrão. Escreva o número de nós ocultos. A predefinição é uma camada oculta com 100 nós.

6. **A taxa de aprendizagem**: Defina o tamanho da etapa executada em cada iteração, antes de correção. Um valor maior para a taxa de aprendizagem pode fazer com que o modelo convergir mais rapidamente, mas ele pode overshoot mínimas locais.

7. **Número de iterações de aprendizagem**: Especifique o número máximo de vezes que o algoritmo deve processar os casos de treinamento.

8. **A aprendizagem inicial de pesos diâmetro**: Especifique os pesos de nó no início do processo de aprendizado.

9. **A dinâmica**: Especifique um peso para aplicar durante o aprendizado a nós de iterações anteriores.
  
11. **Misturar os exemplos**: Selecione esta opção misturar os casos entre as iterações.

    Se desmarcar esta opção, casos são processados por ordem exatamente o mesmo sempre que executar a experimentação.

12. **Propagação de números aleatórias**: Escreva um valor para utilizar como a semente, se quiser garantir a capacidade de repetição em execuções da experimentação mesmo.

14. Ligar um conjunto de dados de treinamento e uma da [módulos de treinamento](module-reference.md): 

    - Se definir **modo de instrutor de criação** ao **único parâmetro**, utilize [Train Model](train-model.md).  
  

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

- Para ver um resumo dos parâmetros do modelo, juntamente com a funcionalidade de pesos aprendidos de treinamento e outros parâmetros da rede neural, com o botão direito a saída de [Train Model](./train-model.md) e selecione **Visualize**.  

- Para guardar um instantâneo do modelo preparado, com o botão direito a **modelo Trained** de saída e selecione **guardar como modelo preparado**. Este modelo não é atualizado em sucessivas execuções da experimentação mesmo.


## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 