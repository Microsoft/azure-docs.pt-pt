---
title: 'Floresta de decisão de duas classes: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo De Duas Classes Decision Forest em Azure Machine Learning para criar um modelo de aprendizagem automática baseado no algoritmo de florestas de decisão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: c9388da449e75dee00fd43af9a4e0407c46f597a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916715"
---
# <a name="two-class-decision-forest-module"></a>Módulo Florestal de decisão de duas classes

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para criar um modelo de aprendizagem automática baseado no algoritmo de decision forests.  

As florestas de decisão são modelos rápidos e supervisionados. Este módulo é uma boa escolha se quiser prever um alvo com um máximo de dois resultados. 

## <a name="understanding-decision-forests"></a>Compreender as florestas de decisão

Este algoritmo de floresta de decisão é um método de aprendizagem conjunto destinado a tarefas de classificação. Os métodos do conjunto baseiam-se no princípio geral de que, em vez de confiar num único modelo, pode obter melhores resultados e um modelo mais generalizado, criando vários modelos relacionados e combinando-os de alguma forma. Geralmente, os modelos conjuntos proporcionam uma melhor cobertura e precisão do que as árvores de decisão únicas. 

Há muitas formas de criar modelos individuais e combiná-los num conjunto. Esta implementação particular de uma decisão florestal funciona construindo múltiplas árvores de decisão e, em seguida, **votando** na classe de saída mais popular. Votar é um dos métodos mais conhecidos para gerar resultados num modelo conjunto. 

+ Muitas árvores de classificação individuais são criadas, usando todo o conjunto de dados, mas diferentes (geralmente aleatórios) pontos de partida. Isto difere da abordagem florestal aleatória, na qual as árvores de decisão individuais só podem usar alguma parte aleatória dos dados ou características.
+ Cada árvore na decisão da árvore florestal produz um histograma de frequência não normalizado de rótulos. 
+ O processo de agregação resume estes histogramas e normaliza o resultado para obter as "probabilidades" para cada rótulo. 
+ As árvores que têm alta confiança de previsão terão um maior peso na decisão final do conjunto.

As árvores de decisão em geral têm muitas vantagens para tarefas de classificação:
  
- Podem capturar limites de decisão não lineares.
- Pode treinar e prever em muitos dados, uma vez que são eficientes na computação e utilização da memória.
- A seleção de funcionalidades está integrada nos processos de formação e classificação.  
- As árvores podem acomodar dados ruidosos e muitas funcionalidades.  
- São modelos não paramétricos, o que significa que podem lidar com dados com distribuições variadas. 

No entanto, as árvores de decisão simples podem sobreajustar-se em dados, e são menos generalizáveis do que os conjuntos de árvores.

Para mais informações, consulte [Decision Forests](https://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Como configurar
  
1.  Adicione o módulo **Floresta de Decisão de Duas Classes** ao seu oleoduto em Azure Machine Learning e abra o painel **Properties** do módulo. 

    Pode encontrar o módulo no âmbito do **Machine Learning.** Expandir **Inicializar,** e depois **Classificar**.  
  
2.  Para o **método de rerecolha,** escolha o método utilizado para criar as árvores individuais.  Pode escolher entre **Bagging** ou **Replicate**.  
  
    -   **Bagging**: Bagging também é chamado de *agregação de botas.* Neste método, cada árvore é cultivada numa nova amostra, criada através da amostragem aleatória do conjunto de dados original com substituição até ter um conjunto de dados do tamanho do original.  
  
         As saídas dos modelos são combinadas através do *voto*, que é uma forma de agregação. Cada árvore numa decisão de classificação a floresta produz um histograma de frequência não normalizado de rótulos. A agregação é resumir estes histogramas e normalizar para obter as "probabilidades" de cada rótulo. Desta forma, as árvores que têm alta confiança de previsão terão um maior peso na decisão final do conjunto.  
  
         Para mais informações, consulte a entrada da Wikipedia para a gregação de Bootstrap.  
  
    -   **Replicação**: Na replicação, cada árvore é treinada exatamente com os mesmos dados de entrada. A determinação de que o predicado dividido é usado para cada nó de árvore permanece aleatória e as árvores serão diversas.   
  
3.  Especifique como pretende que o modelo seja treinado, definindo a opção modo **Criar.**  
  
    -   **Parâmetro único**: Se souber como pretende configurar o modelo, pode fornecer um conjunto específico de valores como argumentos.

    -   **Gama de parâmetros**: Se não tiver a certeza dos melhores parâmetros, pode encontrar os parâmetros ideais utilizando o módulo de [hiperparâmetros](tune-model-hyperparameters.md) do Modelo tune. Você fornece alguma gama de valores, e o treinador iterates sobre múltiplas combinações das configurações para determinar a combinação de valores que produz o melhor resultado.
  
4.  Para **número de árvores de decisão,** digite o número máximo de árvores de decisão que podem ser criadas no conjunto. Ao criar mais árvores de decisão, você pode potencialmente obter uma melhor cobertura, mas o tempo de treino aumenta.  
  
    > [!NOTE]
    >  Este valor também controla o número de árvores exibidas ao visualizar o modelo treinado. Se quiser ver ou imprimir uma única árvore, pode definir o valor para 1. No entanto, apenas uma árvore pode ser produzida (a árvore com o conjunto inicial de parâmetros) e não são realizadas mais iterações.
  
5.  Para **máxima profundidade das árvores de decisão,** digite um número para limitar a profundidade máxima de qualquer árvore de decisão. Aumentar a profundidade da árvore pode aumentar a precisão, com o risco de alguma sobremontagem e aumento do tempo de treino.
  
6.  Para **o número de divisões aleatórias por nó,** digite o número de divisões a utilizar ao construir cada nó da árvore. Uma *divisão* significa que as características em cada nível da árvore (nó) são divididas aleatoriamente.
  
7.  Para o **número mínimo de amostras por nó**de folha, indique o número mínimo de casos necessários para criar qualquer nó terminal (folha) numa árvore.
  
     Ao aumentar este valor, aumenta-se o limiar para a criação de novas regras. Por exemplo, com o valor padrão de 1, mesmo um único caso pode fazer com que uma nova regra seja criada. Se aumentar o valor para 5, os dados de formação teriam de conter pelo menos cinco casos que satisfaçam as mesmas condições.  
  
8.  Selecione os valores desconhecidos para a opção **de características categóricas** para criar um grupo para valores desconhecidos nos conjuntos de formação ou validação. O modelo pode ser menos preciso para valores conhecidos, mas pode fornecer melhores previsões para novos valores (desconhecidos). 

     Se desseleccionar esta opção, o modelo só pode aceitar os valores contidos nos dados de formação.
  
9. Fixe um conjunto de dados rotulado e um dos módulos de [formação:](module-reference.md)  
  
    -   Se definir o modo Criar modo **de treinador** para um parâmetro **único,** utilize o módulo Modelo de [Comboio.](./train-model.md)  
    
## <a name="results"></a>Resultados

Após o treino estar completo:

+ Para guardar uma imagem do modelo treinado, selecione o separador **Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone do conjunto de **dados Register** para salvar o modelo como um módulo reutilizável.

+ Para utilizar o modelo para a pontuação, adicione o módulo **Score Model** a um pipeline.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 