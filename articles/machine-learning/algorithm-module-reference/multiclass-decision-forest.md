---
title: 'Floresta de Decisão Multiclasse: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Multiclass Decision Forest em Azure Machine Learning para criar um modelo de aprendizagem automática baseado no algoritmo *da floresta de decisão.*
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 1be66bdd8a1cf25a32ad3102d770078c904c4b6c
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376830"
---
# <a name="multiclass-decision-forest-module"></a>Módulo floresta de decisão multiclasse

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para criar um modelo de aprendizagem automática baseado no algoritmo *da floresta de decisão.* Uma floresta de decisão é um modelo conjunto que rapidamente constrói uma série de árvores de decisão, enquanto aprende com dados marcados.

## <a name="more-about-decision-forests"></a>Mais sobre as florestas de decisão

O algoritmo da floresta de decisão é um método de aprendizagem conjunto para a classificação. O algoritmo funciona construindo várias árvores de decisão e, em seguida, *votando* na classe de saída mais popular. O voto é uma forma de agregação, na qual cada árvore numa decisão de classificação florestal produz um histograma de frequência não normalizado de rótulos. O processo de agregação resume estes histogramas e normaliza o resultado para obter as "probabilidades" de cada rótulo. As árvores que têm alta confiança de previsão têm um maior peso na decisão final do conjunto.

As árvores de decisão em geral são modelos não paramétricos, o que significa que suportam dados com distribuição variada. Em cada árvore, uma sequência de testes simples é executada para cada classe, aumentando os níveis de uma estrutura de árvore até que um nó folha (decisão) seja alcançado.

As árvores de decisão têm muitas vantagens:

+ Podem representar limites de decisão não lineares.
+ São eficientes no cálculo e no uso da memória durante o treino e previsão.
+ Realizam seleção e classificação integradas de recursos.
+ São resistentes na presença de características ruidosas.

O classificador da floresta de decisão em Azure Machine Learning consiste num conjunto de árvores de decisão. Geralmente, os modelos conjuntos proporcionam uma melhor cobertura e precisão do que as árvores de decisão únicas. Para mais informações, consulte [as árvores de decisão.](https://go.microsoft.com/fwlink/?LinkId=403677)

## <a name="how-to-configure-multiclass-decision-forest"></a>Como configurar a Floresta de Decisão Multiclasse

1. Adicione o módulo **Multiclass Decision Forest** ao seu oleoduto no designer. Pode encontrar este módulo em **Machine Learning,** **Initialize Model** e **Classification**.

2. Clique duas vezes no módulo para abrir o painel **Propriedades.**

3. Para **o método de resampling,** escolha o método utilizado para criar as árvores individuais.  Pode escolher entre embalar ou replicar.

    + **Embalamento** : O embalamento também é chamado de *agregador de botas.* Neste método, cada árvore é cultivada numa nova amostra, criada por amostragem aleatória do conjunto de dados original com substituição até ter um conjunto de dados do tamanho do original. As saídas dos modelos são combinadas com *o voto* , que é uma forma de agregação. Para mais informações, consulte a entrada na Wikipédia para a agregação de Bootstrap.

    + **Replicação:** Na replicação, cada árvore é treinada exatamente com os mesmos dados de entrada. A determinação de qual predicado dividido é usado para cada nó de árvore permanece aleatório, criando diversas árvores.

   

4. Especifique como pretende que o modelo seja treinado, definindo a opção **modo de formação Create.**

    + **Parâmetro único** : Selecione esta opção se souber como pretende configurar o modelo e fornecer um conjunto de valores como argumentos.

    + **Intervalo de parâmetros** : Selecione esta opção se não tiver a certeza dos melhores parâmetros e pretender fazer uma varredura de parâmetros. Selecione uma gama de valores para iterar e o [Tune Model Hyperparameters](tune-model-hyperparameters.md) itera sobre todas as combinações possíveis das configurações fornecidas para determinar os hiperparmetros que produzem os melhores resultados.   

5. **Número de árvores de decisão** : Digite o número máximo de árvores de decisão que podem ser criadas no conjunto. Ao criar mais árvores de decisão, você pode potencialmente obter uma melhor cobertura, mas o tempo de treino pode aumentar.

    Se definir o valor para 1; no entanto, isto significa que apenas uma árvore pode ser produzida (a árvore com o conjunto inicial de parâmetros), e não são realizadas mais iterações.

6. **Profundidade máxima das árvores de decisão** : Digite um número para limitar a profundidade máxima de qualquer árvore de decisão. Aumentar a profundidade da árvore pode aumentar a precisão, correndo o risco de alguma adaptação excessiva e maior tempo de treino.

7. **Número de divisões aleatórias por nó:** Digite o número de divisões a utilizar ao construir cada nó da árvore. Uma *divisão* significa que as características em cada nível da árvore (nó) são divididas aleatoriamente.

8. **Número mínimo de amostras por nó folha** : Indique o número mínimo de caixas necessárias para criar qualquer nó terminal (folha) numa árvore. Ao aumentar este valor, aumenta-se o limiar para a criação de novas regras.

    Por exemplo, com o valor padrão de 1, mesmo um único caso pode fazer com que uma nova regra seja criada. Se aumentar o valor para 5, os dados da formação terão de conter pelo menos cinco casos que satisfaçam as mesmas condições.



10. Ligue um conjunto de dados rotulado e treine o modelo:

    + Se definir **Criar o modo de treinador** para single **parâmetro,** conecte um conjunto de dados marcado e o módulo Modelo de [Comboio.](train-model.md)  
  
    + Se definir **Criar modo de treinador** para intervalo de **parâmetros,** ligue um conjunto de dados marcado e treine o modelo utilizando [hiperparmetros do modelo de sintonização](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se passar uma gama de parâmetros para [o Modelo de Comboio,](train-model.md)utiliza apenas o valor predefinido na lista de parâmetros únicos.  
    > 
    > Se passar um único conjunto de valores de parâmetros para o módulo [Tune Model Hyperparameters,](tune-model-hyperparameters.md) quando espera uma gama de definições para cada parâmetro, ignora os valores e utiliza os valores predefinidos para o aluno.  
    > 
    > Se selecionar a opção De Alcance de **Parâmetros** e introduzir um único valor para qualquer parâmetro, esse valor único especificado é utilizado ao longo da varredura, mesmo que outros parâmetros se alterem numa gama de valores.

11. Envie o oleoduto.



## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 