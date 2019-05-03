---
title: 'Floresta de decisão de duas classes: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de floresta de decisão de duas classes no serviço Azure Machine Learning para criar um modelo de machine learning com base no algoritmo de florestas decisão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f3e7cab6e33fa9373095441685f6ecb04f1d91a5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029374"
---
# <a name="two-class-decision-forest-module"></a>Módulo de floresta de decisão de duas classes

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para criar um modelo de machine learning com base no algoritmo de florestas decisão.  

Florestas de decisão são modelos do ensemble rápido, supervisionado. Este módulo é uma boa opção se pretende prever um destino com um máximo de dois resultados. 

## <a name="understanding-decision-forests"></a>Compreender as florestas de decisão

Esse algoritmo de floresta de decisão é um ensemble método que se destina a tarefas de classificação de aprendizado. Métodos de Ensemble baseiam-se no princípio geral que em vez de depender de um único modelo, pode obter melhores resultados e um modelo mais generalizado, criando vários modelos relacionados e combiná-los de alguma forma. Em geral, os modelos de ensemble fornecem uma melhor cobertura e precisão do que as árvores de decisão única. 

Existem várias formas de criar modelos individuais e combiná-los num ensemble. Essa implementação particular de uma floresta de decisão funciona com a criação de várias árvores de decisão e, em seguida **voto** na classe de saída mais popular. Votação é um dos métodos better-known para gerar os resultados num modelo de ensemble. 

+ Muitos árvores de classificação individuais são criados, usando o conjunto de dados completo, mas diferentes (normalmente aleatória) pontos de partida. Isto é diferente da abordagem de floresta aleatório, em que as árvores de decisão individual podem utilizar apenas uma parte aleatória dos dados ou funcionalidades.
+ Cada árvore na árvore da floresta de decisão produz um histograma não normalizado frequência das etiquetas. 
+ O processo de agregação resume estas histogramas e normaliza o resultado para obter as "probabilidades" para cada etiqueta. 
+ As árvores que têm confiança na predição elevado terá um peso maior a decisão final da ensemble.

Árvores de decisões em geral têm muitas vantagens para tarefas de classificação:
  
- Eles podem capturar os limites de decisão de não-lineares.
- Pode dar formação e prever em muitos dados, como eles são eficientes na utilização da memória e de computação.
- Seleção de funcionalidades está integrada nos processos de formação e classificação.  
- Árvores capaz de acomodar dados desnecessárias e muitos recursos.  
- Eles são modelos não paramétrica, que significa que eles possam tratar dados com diversas distribuições. 

No entanto, as árvores de decisão simples sobre podem overfit nos dados e são menos generalizable de árvores de árvore.

Para obter mais informações, consulte [florestas de decisão](http://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Como configurar
  
1.  Adicionar a **floresta de decisão de duas classes** módulo à sua experimentação no Azure Machine Learning e abra o **propriedades** painel do módulo. 

    Pode encontrar o módulo a ser **Machine Learning**. Expanda **inicializar**e, em seguida **classificação**.  
  
2.  Para **Resampling método**, escolha o método utilizado para criar as árvores individuais.  Pode escolher entre **Bagging** ou **replicar**.  
  
    -   **Bagging**: Também é denominado bagging *agregar bootstrap*. Nesse método, cada árvore é aumentado numa nova amostra, criada pelo conjunto de dados original com a substituição de amostragem aleatoriamente até que tem um conjunto de dados, o tamanho do original.  
  
         As saídas dos modelos são combinadas pelos *voto*, que é um formulário de agregação. Cada árvore numa floresta de decisão de classificação produz uma histograma unnormalized frequência das etiquetas. A agregação é somar esses histogramas e normalizar para obter as "probabilidades" para cada etiqueta. Desta forma, as árvores que têm confiança na predição elevada terão um peso maior na decisão final da ensemble.  
  
         Para obter mais informações, consulte a entrada de Wikipedia para agregar Bootstrap.  
  
    -   **Replicar**: Na replicação, cada árvore é preparado no exatamente os mesmos dados de entrada. A determinação de qual dividir o predicado é utilizado para cada nó de árvore permanece aleatória e as árvores será diversificadas.   
  
3.  Especifique como pretende que o modelo de formação, definindo a **modo de instrutor de criação** opção.  
  
    -   **Único parâmetro**: Se souber como pretende configurar o modelo, pode fornecer um conjunto específico de valores como argumentos.
  
4.  Para **número de árvores de decisão**, escreva o número máximo de árvores de decisão que podem ser criadas no ensemble. Ao criar árvores de decisões mais, pode potencialmente obter uma melhor cobertura, mas aumenta o tempo de treinamento.  
  
    > [!NOTE]
    >  Este valor também controla o número de árvores apresentado quando visualizar o modelo preparado. Se pretender ver ou imprimir uma única árvore, pode definir o valor como 1. No entanto, apenas uma árvore pode ser produzido (a árvore com o conjunto inicial de parâmetros) e são efetuadas sem mais iterações.
  
5.  Para **profundidade máximo das árvores de decisão**, escreva um número para limitar a profundidade máxima de qualquer árvore de decisão. Aumentando a profundidade da árvore pode aumentar a precisão, sob alguma overfitting e maior tempo de treinamento.
  
6.  Para **número de divisões aleatórios por nó de**, escreva o número de divisões de usar para criar cada nó da árvore. R *dividir* significa que os recursos em cada nível da árvore (node) é divididas aleatoriamente.
  
7.  Para **número mínimo de exemplos por nó folha**, indicar o número mínimo de cenários que são necessários para criar qualquer nó de terminal (folha) numa árvore.
  
     Ao aumentar este valor, aumentar o limiar para a criação de novas regras. Por exemplo, com o valor predefinido de 1, até mesmo um único caso pode fazer com que uma nova regra a ser criada. Se aumentar o valor para 5, os dados de treinamento seriam têm de conter, pelo menos, cinco casos que cumprem as mesmas condições.  
  
8.  Selecione o **permitir valores desconhecidos para funcionalidades categóricas** opção para criar um grupo para os valores de desconhecido em conjuntos de treinamento ou validação. O modelo pode ser menos preciso para os valores conhecidos, mas ele pode fornecer a obter melhores predições para novos valores (desconhecidos). 

     Se desmarcar esta opção, o modelo pode aceitar apenas os valores que estão contidos nos dados de treinamento.
  
9. Anexar um conjunto de dados com nome e uma da [módulos de treinamento](module-reference.md):  
  
    -   Se definir **modo de instrutor de criação** para **único parâmetro**, utilize o [Train Model](./train-model.md) módulo.  
  
    
## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver a árvore que foi criada em cada iteração, com o botão direito a saída do [Train Model](./train-model.md) módulo e selecione **Visualize**.
  
    Clique em cada árvore para desagregar as divisões e ver as regras para cada nó.

+ Para guardar um instantâneo do modelo, clique com botão direito a **modelo preparado** de saída e selecione **guardar modelo**. O modelo guardado não é atualizado em sucessivas execuções da experimentação.

+ Para utilizar o modelo para a classificação, adicione a **modelo de pontuação** módulo para uma experimentação.


## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 