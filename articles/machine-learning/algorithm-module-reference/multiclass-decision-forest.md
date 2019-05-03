---
title: 'Floresta de decisão de várias classes: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de floresta de decisão de várias classes no serviço Azure Machine Learning para criar um modelo com base em machine learning a *floresta de decisão* algoritmo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d5ff1809886198f7f1e4f4b9c36f877ce61d512e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029434"
---
# <a name="multiclass-decision-forest-module"></a>Módulo de floresta de decisão de várias classes

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para criar um modelo com base em machine learning a *floresta de decisão* algoritmo. Uma floresta de decisão é um modelo de ensemble que cria rapidamente uma série de árvores de decisão, ao learning a partir dos dados marcados.

## <a name="more-about-decision-forests"></a>Mais informações sobre as florestas de decisão

O algoritmo de floresta de decisão é um método para classificação de aprendizagem de ensemble. O algoritmo funciona com a criação de várias árvores de decisão e, em seguida *voto* na classe de saída mais popular. Votação é uma forma de agregação, que árvore numa decisão de classificação de floresta produz um histograma não normalizado frequência das etiquetas. O processo de agregação resume estas histogramas e normaliza o resultado para obter as "probabilidades" para cada etiqueta. As árvores que têm confiança na predição alta têm um peso maior na decisão final da ensemble.

Árvores de decisões em geral são os modelos não paramétrica, que significa que eles oferecem suporte a dados com diversas distribuições. Árvore, de uma seqüência de testes simples é executada para cada classe, aumentando os níveis de uma estrutura de árvore até que um nó de folha (decisão) seja atingido.

Árvores de decisões têm muitas vantagens:

+ Podem representar os limites de decisão de não-lineares.
+ Eles são eficientes na utilização da memória e de computação durante a formação e predição.
+ Eles executar classificação e seleção de funcionalidades integradas.
+ Eles são flexíveis na presença de funcionalidades ruidosos.

O classificador de floresta de decisão no Azure Machine Learning é composta por um ensemble das árvores de decisão. Em geral, os modelos de ensemble fornecem uma melhor cobertura e precisão do que as árvores de decisão única. Para obter mais informações, consulte [árvores de decisões](http://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Como configurar a floresta de decisão de várias classes



1. Adicionar a **floresta de decisão de várias classes** módulo à sua experimentação na interface. Pode encontrar este módulo sob **Machine Learning**, **inicializar modelo**, e **classificação**.

2. Clique duas vezes o módulo para abrir o **propriedades** painel.

3. Para **Resampling método**, escolha o método utilizado para criar as árvores individuais.  Pode escolher entre bagging ou a replicação.

    + **Bagging**: Também é denominado bagging *agregar bootstrap*. Nesse método, cada árvore é aumentado numa nova amostra, criada pelo conjunto de dados original com a substituição de amostragem aleatoriamente até que tem um conjunto de dados, o tamanho do original. As saídas dos modelos são combinadas pelos *voto*, que é um formulário de agregação. Para obter mais informações, consulte a entrada de Wikipedia para agregar Bootstrap.

    + **Replicar**: Na replicação, cada árvore é preparado no exatamente os mesmos dados de entrada. A determinação de qual dividir o predicado é utilizado para cada nó de árvore permanece aleatória, criar árvores diversificadas.

   

4. Especifique como pretende que o modelo de formação, definindo a **modo de instrutor de criação** opção.

    + **Único parâmetro**: Selecione esta opção se souber como pretende configurar o modelo e fornecem um conjunto de valores como argumentos.


5. **Número de árvores de decisão**: Escreva o número máximo de árvores de decisão que podem ser criadas no ensemble. Ao criar árvores de decisões mais, pode potencialmente obter uma melhor cobertura, mas pode aumentar o tempo de treinamento.

    Este valor também controla o número de árvores apresentado nos resultados, ao visualizar o modelo preparado. Para ver ou imprimir uma única árvore, pode definir o valor como 1; No entanto, isso significa que apenas uma árvore pode ser produzido (a árvore com o conjunto inicial de parâmetros) e não mais iterações são executadas.

6. **Profundidade máxima das árvores de decisão**: Escreva um número para limitar a profundidade máxima de qualquer árvore de decisão. Aumentando a profundidade da árvore pode aumentar a precisão, sob alguma overfitting e maior tempo de treinamento.

7. **Número de divisões aleatórios por nó de**: Escreva o número de divisões de usar para criar cada nó da árvore. R *dividir* significa que os recursos em cada nível da árvore (node) é divididas aleatoriamente.

8. **Número mínimo de exemplos por nó folha**: Indica o número mínimo de cenários que são necessários para criar qualquer nó de terminal (folha) numa árvore. Ao aumentar este valor, aumentar o limiar para a criação de novas regras.

    Por exemplo, com o valor predefinido de 1, até mesmo um único caso pode fazer com que uma nova regra a ser criada. Se aumentar o valor para 5, os dados de treinamento seriam têm de conter, pelo menos, cinco casos que cumprem as mesmas condições.



10. Ligue-se de um conjunto de dados com nome e um dos módulos de treinamento:

    + Se definir **modo de instrutor de criação** para **único parâmetro**, utilize o [Train Model](./train-model.md) módulo.

11. Execute a experimentação.

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver a árvore que foi criada em cada iteração, com o botão direito a saída do [Train Model](./train-model.md) módulo e selecione **Visualize**.
+ Para ver as regras para cada nó, clique em cada árvore para desagregar as divisões.


## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 