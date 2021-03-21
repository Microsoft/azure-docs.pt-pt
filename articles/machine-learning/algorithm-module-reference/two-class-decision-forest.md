---
title: 'Floresta de Decisão Two-Class: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Two-Class Decision Forest em Azure Machine Learning para criar um modelo de aprendizagem automática baseado no algoritmo das florestas de decisão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/03/2020
ms.openlocfilehash: c4c303a7640454ba0cb6622b21fd161354266068
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94375945"
---
# <a name="two-class-decision-forest-module"></a>Módulo florestal de decisão Two-Class

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para criar um modelo de aprendizagem automática baseado no algoritmo das florestas de decisão.  

As florestas de decisão são modelos de conjunto rápidos e supervisionados. Este módulo é uma boa escolha se quiser prever um alvo com um máximo de dois resultados. 

## <a name="understanding-decision-forests"></a>Compreender as florestas de decisão

Este algoritmo de floresta de decisão é um método de aprendizagem conjunto destinado a tarefas de classificação. Os métodos conjuntos baseiam-se no princípio geral de que, em vez de confiar num único modelo, é possível obter melhores resultados e um modelo mais generalizado, criando vários modelos relacionados e combinando-os de alguma forma. Geralmente, os modelos conjuntos proporcionam uma melhor cobertura e precisão do que as árvores de decisão únicas. 

Há muitas formas de criar modelos individuais e combiná-los num conjunto. Esta implementação particular de uma decisão florestal funciona construindo múltiplas árvores de decisão e, em seguida, **votando** na classe de produção mais popular. Votar é um dos métodos mais conhecidos para gerar resultados num modelo conjunto. 

+ Muitas árvores de classificação individual são criadas, usando todo o conjunto de dados, mas diferentes (geralmente aleatórios) pontos de partida. Isto difere da abordagem aleatória da floresta, na qual as árvores de decisão individuais só podem usar alguma parte aleatória dos dados ou características.
+ Cada árvore na decisão florestal produz um histograma de frequência não normalizado de rótulos. 
+ O processo de agregação resume estes histogramas e normaliza o resultado para obter as "probabilidades" de cada rótulo. 
+ As árvores que têm alta confiança de previsão terão um maior peso na decisão final do conjunto.

As árvores de decisão em geral têm muitas vantagens para tarefas de classificação:
  
- Podem capturar limites de decisão não lineares.
- Você pode treinar e prever em muitos dados, uma vez que são eficientes em computação e uso de memória.
- A seleção de recursos está integrada nos processos de formação e classificação.  
- As árvores podem acomodar dados ruidosos e muitas características.  
- São modelos não paramétricos, o que significa que podem lidar com dados com distribuição variada. 

No entanto, as árvores de decisão simples podem sobreatilar em dados, e são menos generalizáveis do que os conjuntos de árvores.

Para mais informações, consulte [As Florestas de Decisão.](https://go.microsoft.com/fwlink/?LinkId=403677)  

## <a name="how-to-configure"></a>Como configurar
  
1.  Adicione o módulo **de floresta de decisão de duas classes** ao seu oleoduto em Azure Machine Learning e abra o painel de **propriedades** do módulo. 

    Pode encontrar o módulo em **Machine Learning**. Expandir **Inicialize**, e, em seguida, **Classificação**.  
  
2.  Para **o método de resampling,** escolha o método utilizado para criar as árvores individuais.  Pode escolher entre **Embalar** ou **Replicar.**  
  
    -   **Embalamento**: O embalamento também é chamado de *agregador de botas.* Neste método, cada árvore é cultivada numa nova amostra, criada por amostragem aleatória do conjunto de dados original com substituição até ter um conjunto de dados do tamanho do original.  
  
         As saídas dos modelos são combinadas com *o voto*, que é uma forma de agregação. Cada árvore numa decisão de classificação a floresta produz um histograma de frequência não normalizado de rótulos. A agregação é resumir estes histogramas e normalizar para obter as "probabilidades" de cada rótulo. Desta forma, as árvores que têm alta confiança de previsão terão um maior peso na decisão final do conjunto.  
  
         Para mais informações, consulte a entrada na Wikipédia para a agregação de Bootstrap.  
  
    -   **Replicação:** Na replicação, cada árvore é treinada exatamente com os mesmos dados de entrada. A determinação de qual predicado dividido é usado para cada nó de árvore permanece aleatório e as árvores serão diversas.   
  
3.  Especifique como pretende que o modelo seja treinado, definindo a opção **modo de formação Create.**  
  
    -   **Parâmetro único**: Se souber como pretende configurar o modelo, pode fornecer um conjunto específico de valores como argumentos.

    -   **Intervalo de parâmetros**: Se não tiver a certeza dos melhores parâmetros, pode encontrar os parâmetros ideais utilizando o módulo [De Hiperparametros Tune Model.](tune-model-hyperparameters.md) Fornece alguma gama de valores, e o treinador itera sobre várias combinações das definições para determinar a combinação de valores que produz o melhor resultado.
  
4.  Para **o Número de árvores de decisão,** escreva o número máximo de árvores de decisão que podem ser criadas no conjunto. Ao criar mais árvores de decisão, você pode potencialmente obter uma melhor cobertura, mas o tempo de treino aumenta.  
  
    > [!NOTE]
    >  Se definir o valor para 1. No entanto, apenas uma árvore pode ser produzida (a árvore com o conjunto inicial de parâmetros) e não são realizadas mais iterações.
  
5.  Para **a máxima profundidade das árvores de decisão,** escreva um número para limitar a profundidade máxima de qualquer árvore de decisão. Aumentar a profundidade da árvore pode aumentar a precisão, correndo o risco de alguma adaptação excessiva e maior tempo de treino.
  
  
7.  Para **o número mínimo de amostras por nó folha,** indique o número mínimo de caixas necessárias para criar qualquer nó terminal (folha) numa árvore.
  
     Ao aumentar este valor, aumenta-se o limiar para a criação de novas regras. Por exemplo, com o valor padrão de 1, mesmo um único caso pode fazer com que uma nova regra seja criada. Se aumentar o valor para 5, os dados da formação terão de conter pelo menos cinco casos que satisfaçam as mesmas condições.  
  
8.  Selecione a opção Permitir valores desconhecidos para a opção **de funcionalidades categóricas** para criar um grupo para valores desconhecidos nos conjuntos de treino ou validação. O modelo pode ser menos preciso para valores conhecidos, mas pode fornecer melhores previsões para novos valores (desconhecidos). 

     Se desmarcar esta opção, o modelo só pode aceitar os valores contidos nos dados de treino.
  
9. Anexar um conjunto de dados rotulado e treinar o modelo:

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

+ Para guardar uma imagem do modelo treinado, selecione o **separador Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone **do conjunto de dados registar** para guardar o modelo como um módulo reutilizável.

+ Para utilizar o modelo para pontuar, adicione o módulo **'Marcar',** a um pipeline.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 