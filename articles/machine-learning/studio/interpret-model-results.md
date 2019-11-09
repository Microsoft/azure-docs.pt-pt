---
title: Interpretar os resultados dos modelos
titleSuffix: ML Studio (classic) - Azure
description: Como escolher o parâmetro ideal definido para um algoritmo usando e visualizando as saídas do modelo de pontuação.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 11/29/2017
ms.openlocfilehash: acac881a790644532121697e6b79e57a98b168fe
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839853"
---
# <a name="interpret-model-results-in-azure-machine-learning-studio-classic"></a>Interpretar os resultados do modelo em Azure Machine Learning Studio (clássico)
Este tópico explica como Visualizar e interpretar os resultados de previsão em Azure Machine Learning Studio (clássico). Depois de ter treinado um modelo e feito previsões sobre ele ("pontuado o modelo"), você precisa entender e interpretar o resultado da previsão.



Há quatro tipos principais de modelos de aprendizado de máquina na versão clássica do Azure Machine Learning Studio:

* Classificação
* Clustering
* Regressão
* Sistemas de recomendação

Os módulos usados para previsão sobre esses modelos são:

* Módulo [modelo de Pontuação][score-model] para classificação e regressão
* Módulo [atribuir a clusters][assign-to-clusters] para clustering
* [Pontuar recomendador Matchbox][score-matchbox-recommender] para sistemas de recomendação

Este documento explica como interpretar os resultados de previsão para cada um desses módulos. Para obter uma visão geral desses módulos, consulte [como escolher parâmetros para otimizar seus algoritmos em Azure Machine Learning Studio (clássico)](algorithm-parameters-optimize.md).

Este tópico aborda a interpretação da previsão, mas não a avaliação do modelo. Para obter mais informações sobre como avaliar seu modelo, consulte [como avaliar o desempenho do modelo no Azure Machine Learning Studio (clássico)](evaluate-model-performance.md).

Se você for novo na versão clássica do Azure Machine Learning Studio e precisar de ajuda para criar um experimento simples para começar, consulte [criar um experimento simples no Azure Machine Learning Studio (clássico)](create-experiment.md).

## <a name="classification"></a>Classificação
Há duas subcategorias de problemas de classificação:

* Problemas com apenas duas classes (classificação binária ou de duas classes)
* Problemas com mais de duas classes (classificação de várias classes)

Azure Machine Learning Studio (clássico) tem diferentes módulos para lidar com cada um desses tipos de classificação, mas os métodos para interpretar os resultados da previsão são semelhantes.

### <a name="two-class-classification"></a>Classificação de duas classes
**Experimento de exemplo**

Um exemplo de um problema de classificação de duas classes é a classificação de flores íris. A tarefa é classificar flores íris com base em seus recursos. O conjunto de dados íris fornecido na versão clássica do Azure Machine Learning Studio é um subconjunto do [conjunto de dados íris](https://en.wikipedia.org/wiki/Iris_flower_data_set) populares contendo instâncias de apenas duas espécies de flor (classes 0 e 1). Há quatro recursos para cada flor (comprimento SEPA, largura de SEPA, comprimento de pétala e largura de pétala).

![Captura de tela do experimento de íris](./media/interpret-model-results/1.png)

Figura 1. Teste de problema de classificação de duas classes da íris

Um experimento foi executado para resolver esse problema, como mostra a Figura 1. Um modelo de árvore de decisão aumentada de duas classes foi treinado e pontuado. Agora você pode visualizar os resultados da previsão do módulo [modelo de Pontuação][score-model] clicando na porta de saída do módulo [modelo de Pontuação][score-model] e, em seguida, clicando em **Visualizar**.

![Módulo modelo de Pontuação](./media/interpret-model-results/1_1.png)

Isso abre os resultados da pontuação, conforme mostrado na Figura 2.

![Resultados do experimento de classificação de duas classes da íris](./media/interpret-model-results/2.png)

Figura 2. Visualizar um resultado de modelo de pontuação na classificação de duas classes

**Interpretação de resultados**

Há seis colunas na tabela de resultados. As quatro colunas à esquerda são os quatro recursos. As duas colunas à direita, rótulos pontuados e probabilidades pontuadas, são os resultados da previsão. A coluna probabilidades de Pontuação mostra a probabilidade de uma flor pertencer à classe positiva (classe 1). Por exemplo, o primeiro número na coluna (0, 28571) significa que há 0, 28571 probabilidade de que a primeira flor pertença à classe 1. A coluna rótulos pontuados mostra a classe prevista para cada flor. Isso se baseia na coluna probabilidades de pontuação. Se a probabilidade de Pontuação de uma flor for maior que 0,5, ela será prevista como classe 1. Caso contrário, ele é previsto como classe 0.

**Publicação de serviço Web**

Depois que os resultados da previsão foram compreendidos e considerados som, o experimento pode ser publicado como um serviço Web para que você possa implantá-lo em vários aplicativos e chamá-lo para obter previsões de classe em qualquer nova flor de íris. Para saber como alterar um teste de treinamento para um experimento de Pontuação e publicá-lo como um serviço Web, consulte [tutorial 3: implantar modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md). Este procedimento fornece um experimento de pontuação, como mostrado na Figura 3.

![Captura de tela do experimento de Pontuação](./media/interpret-model-results/3.png)

Figura 3. Pontuação do teste de problema de classificação de duas classes da íris

Agora você precisa definir a entrada e a saída para o serviço Web. A entrada é a porta de entrada à direita do [modelo de Pontuação][score-model], que é a entrada de recursos de flor de íris. A escolha da saída depende se você está interessado na classe prevista (rótulo pontuado), na probabilidade pontuada ou em ambas. Neste exemplo, supõe-se que você esteja interessado em ambos. Para selecionar as colunas de saída desejadas, use um módulo [selecionar colunas no conjunto de dados][select-columns] . Clique em [selecionar colunas no conjunto de dados][select-columns], clique em **Iniciar seletor de coluna**e selecione **Rótulos pontuados** e **probabilidades pontuadas**. Depois de definir a porta de saída de [selecionar colunas no conjunto de dados][select-columns] e executá-la novamente, você deve estar pronto para publicar o teste de Pontuação como um serviço Web clicando em **publicar serviço Web**. O experimento final é semelhante à figura 4.

![O experimento de classificação de duas classes da íris](./media/interpret-model-results/4.png)

Figura 4. Experimento de pontuação final de um problema de classificação de duas classes da íris

Depois de executar o serviço Web e inserir alguns valores de recursos de uma instância de teste, o resultado retorna dois números. O primeiro número é o rótulo pontuado e o segundo é a probabilidade pontuada. Essa flor é prevista como classe 1 com probabilidade de 0,9655.

![Modelo de Pontuação de interpretação de teste](./media/interpret-model-results/4_1.png)

![Resultados do teste de Pontuação](./media/interpret-model-results/5.png)

Figura 5. Resultado do serviço Web da classificação de duas classes da íris

### <a name="multi-class-classification"></a>Classificação de várias classes
**Experimento de exemplo**

Nesse experimento, você executa uma tarefa de reconhecimento de letra como um exemplo de classificação multiclasse. O classificador tenta prever uma determinada letra (classe) com base em alguns valores de atributos escritos manualmente extraídos das imagens escritas à mão.

![Exemplo de reconhecimento de letra](./media/interpret-model-results/5_1.png)

Nos dados de treinamento, há 16 recursos extraídos de imagens de letra manuscrita. As 26 cartas formam nossas 26 classes. A Figura 6 mostra um experimento que treinará um modelo de classificação multiclasse para reconhecimento de letras e preverá o mesmo conjunto de recursos em um conjunto de dados de teste.

![Experimento de classificação multiclasse de reconhecimento de letra](./media/interpret-model-results/6.png)

Figura 6. Teste de problema de classificação multiclasse do reconhecimento de letra

Visualizando os resultados do módulo [modelo de Pontuação][score-model] clicando no módulo porta de saída do [modelo de Pontuação][score-model] e, em seguida, clicando em **Visualizar**, você deverá ver o conteúdo, conforme mostrado na Figura 7.

![Resultados do modelo de Pontuação](./media/interpret-model-results/7.png)

Figura 7. Visualizar resultados do modelo de pontuação em uma classificação de várias classes

**Interpretação de resultados**

As 16 colunas à esquerda representam os valores de recurso do conjunto de teste. As colunas com nomes como probabilidades pontuadas para a classe "XX" são exatamente como a coluna de probabilidades pontuada no caso de duas classes. Elas mostram a probabilidade de que a entrada correspondente esteja em uma determinada classe. Por exemplo, para a primeira entrada, há 0, 3571 probabilidade de que seja uma probabilidade "A", 0, 451, que é um "B" e assim por diante. A última coluna (rótulos pontuados) é a mesma que rótulos pontuados no caso de duas classes. Ele seleciona a classe com a maior probabilidade de Pontuação como a classe prevista da entrada correspondente. Por exemplo, para a primeira entrada, o rótulo pontuado é "F", pois ele tem a maior probabilidade de ser um "F" (0,916995).

**Publicação de serviço Web**

Você também pode obter o rótulo pontuado para cada entrada e a probabilidade do rótulo pontuado. A lógica básica é encontrar a maior probabilidade entre todas as probabilidades pontuadas. Para fazer isso, você precisa usar o módulo [Executar script R][execute-r-script] . O código R é mostrado na Figura 8, e o resultado do experimento é mostrado na Figura 9.

![Exemplo de código R](./media/interpret-model-results/8.png)

Figura 8. Código R para extrair rótulos pontuados e as probabilidades associadas dos rótulos

![Resultado do experimento](./media/interpret-model-results/9.png)

Figura 9. Experimento de pontuação final do problema de classificação multiclasse de reconhecimento de letra

Depois de publicar e executar o serviço Web e inserir alguns valores de recursos de entrada, o resultado retornado é semelhante à figura 10. Essa letra escrita à mão, com seus 16 recursos extraídos, é prevista para ser um "T" com probabilidade de 0,9715.

![Módulo de Pontuação de interpretação de teste](./media/interpret-model-results/9_1.png)

![Resultado do teste](./media/interpret-model-results/10.png)

Figura 10. Resultado do serviço Web da classificação multiclasse

## <a name="regression"></a>Regressão
Os problemas de regressão são diferentes dos problemas de classificação. Em um problema de classificação, você está tentando prever classes discretas, como a qual classe uma flor de íris pertence. Mas como você pode ver no exemplo a seguir de um problema de regressão, você está tentando prever uma variável contínua, como o preço de um carro.

**Experimento de exemplo**

Use a previsão de preço de automóvel como seu exemplo de regressão. Você está tentando prever o preço de um carro com base em seus recursos, incluindo Make, tipo de combustível, tipo de corpo e roda da unidade. O experimento é mostrado na Figura 11.

![Experimento de regressão de preço de automóvel](./media/interpret-model-results/11.png)

Figura 11. Teste de problema de regressão de preço de automóvel

Visualizando o módulo [modelo de Pontuação][score-model] , o resultado é semelhante à figura 12.

![Resultados de Pontuação para o problema de previsão de preço de automóvel](./media/interpret-model-results/12.png)

Figura 12. Resultado da pontuação para o problema de previsão de preço de automóvel

**Interpretação de resultados**

Rótulos pontuados é a coluna de resultado neste resultado de pontuação. Os números são o preço previsto para cada carro.

**Publicação de serviço Web**

Você pode publicar o teste de regressão em um serviço Web e chamá-lo para a previsão de preço de automóvel da mesma maneira como no caso de uso de classificação de duas classes.

![Experimento de Pontuação para problema de regressão de preço de automóvel](./media/interpret-model-results/13.png)

Figura 13. Teste de Pontuação de um problema de regressão de preço de automóvel

Executando o serviço Web, o resultado retornado é semelhante à figura 14. O preço previsto para este carro é $15085.52.

![Módulo de Pontuação de interpretação de teste](./media/interpret-model-results/13_1.png)

![Resultados do módulo de Pontuação](./media/interpret-model-results/14.png)

Figura 14. Resultado do serviço Web de um problema de regressão de preço de automóvel

## <a name="clustering"></a>Clustering
**Experimento de exemplo**

Vamos usar o conjunto de dados íris novamente para criar um experimento de clustering. Aqui você pode filtrar os rótulos de classe no conjunto de dados para que ele tenha apenas recursos e possa ser usado para clustering. Nesse caso de uso de íris, especifique o número de clusters a ser dois durante o processo de treinamento, o que significa que você agruparia as flores em duas classes. O experimento é mostrado na Figura 15.

![Teste de problema de clustering da íris](./media/interpret-model-results/15.png)

Figura 15. Teste de problema de clustering da íris

O clustering difere da classificação, pois o conjunto de dados de treinamento não tem rótulos de verdade de terra por si só. O clustering agrupa as instâncias do conjunto de dados de treinamento em clusters distintos. Durante o processo de treinamento, o modelo rotula as entradas aprendendo as diferenças entre seus recursos. Depois disso, o modelo treinado pode ser usado para classificar ainda mais as entradas futuras. Há duas partes do resultado em que estamos interessados dentro de um problema de clustering. A primeira parte está rotulando o conjunto de dados de treinamento e o segundo está classificando um novo conjunto de dados com o modelo treinado.

A primeira parte do resultado pode ser visualizada clicando na porta de saída à esquerda do [modelo de cluster de treinamento][train-clustering-model] e, em seguida, clicando em **Visualizar**. A visualização é mostrada na Figura 16.

![Resultado do clustering](./media/interpret-model-results/16.png)

Figura 16. Visualizar o resultado de clustering para o conjunto de dados de treinamento

O resultado da segunda parte, o clustering de novas entradas com o modelo de clustering treinado, é mostrado na figura 17.

![Visualizar resultado de clustering](./media/interpret-model-results/17.png)

Figura 17. Visualizar resultado de clustering em um novo conjunto de dados

**Interpretação de resultados**

Embora os resultados das duas partes sejam provenientes de diferentes estágios de teste, eles têm a mesma aparência e são interpretados da mesma maneira. As primeiras quatro colunas são recursos. A última coluna, atribuições, é o resultado da previsão. As entradas atribuídas ao mesmo número são previstas no mesmo cluster, ou seja, elas compartilham semelhanças de alguma forma (esse experimento usa a métrica de distância euclidiana padrão). Como você especificou o número de clusters como 2, as entradas nas atribuições são rotuladas como 0 ou 1.

**Publicação de serviço Web**

Você pode publicar o teste de clustering em um serviço Web e chamá-lo para previsões de clustering da mesma maneira que no caso de uso de classificação de duas classes.

![Teste de Pontuação para o problema de clustering da íris](./media/interpret-model-results/18.png)

Figura 18. Experimento de Pontuação de um problema de clustering de íris

Depois de executar o serviço Web, o resultado retornado é semelhante à figura 19. Esta flor está prevista para estar no cluster 0.

![Módulo de Pontuação de interpretação de teste](./media/interpret-model-results/18_1.png)

![Resultado do módulo de Pontuação](./media/interpret-model-results/19.png)

Figura 19. Resultado do serviço Web da classificação de duas classes da íris

## <a name="recommender-system"></a>Sistema de recomendação
**Experimento de exemplo**

Para sistemas de recomendação, você pode usar o problema de recomendação de restaurante como exemplo: você pode recomendar restaurantes para clientes com base em seu histórico de classificação. Os dados de entrada consistem em três partes:

* Classificações de restaurante de clientes
* Dados de recurso do cliente
* Dados de recurso do restaurante

Há várias coisas que podemos fazer com o módulo [treinar recomendador Matchbox][train-matchbox-recommender] na versão clássica do Azure Machine Learning Studio:

* Prever classificações para um determinado usuário e item
* Itens recomendados a um determinado usuário
* Localizar usuários relacionados a um determinado usuário
* Localizar itens relacionados a um determinado item

Você pode escolher o que deseja fazer selecionando as quatro opções no menu tipo de **previsão de recomendação** . Aqui você pode percorrer todos os quatro cenários.

![Recomendador Matchbox](./media/interpret-model-results/19_1.png)

Um experimento típico de Azure Machine Learning Studio (clássico) para um sistema de recomendação é semelhante à figura 20. Para obter informações sobre como usar esses módulos do sistema de recomendação, consulte [treinar o recomendador Matchbox][train-matchbox-recommender] e [pontuar o recomendador Matchbox][score-matchbox-recommender].

![Teste do sistema de recomendação](./media/interpret-model-results/20.png)

Figura 20. Teste do sistema de recomendação

**Interpretação de resultados**

**Prever classificações para um determinado usuário e item**

Ao selecionar **previsão de classificação** em **tipo de previsão de recomendação**, você está solicitando que o sistema de recomendação preveja a classificação de um determinado usuário e item. A visualização da saída de [recomendação do Matchbox de Pontuação][score-matchbox-recommender] é semelhante à figura 21.

![Resultado da Pontuação do sistema de recomendação – previsão de classificação](./media/interpret-model-results/21.png)

Figura 21. Visualizar o resultado da Pontuação do sistema de recomendação – previsão de classificação

As duas primeiras colunas são os pares de item de usuário fornecidos pelos dados de entrada. A terceira coluna é a classificação prevista de um usuário para um determinado item. Por exemplo, na primeira linha, o U1048 do cliente é previsto para classificar o restaurante 135026 como 2.

**Itens recomendados a um determinado usuário**

Ao selecionar a **recomendação de item** no tipo de previsão de **recomendação**, você está solicitando que o sistema de recomendação recomende itens para um determinado usuário. O último parâmetro a ser escolhido nesse cenário é a *seleção de item recomendada*. A opção **de itens classificados (para avaliação de modelo)** é principalmente para a avaliação do modelo durante o processo de treinamento. Para este estágio de previsão, escolhemos **de todos os itens**. A visualização da saída de [recomendação do Matchbox de Pontuação][score-matchbox-recommender] é semelhante à figura 22.

![Resultado da Pontuação do sistema de recomendação-recomendações de item](./media/interpret-model-results/22.png)

Figura 22. Visualizar o resultado da Pontuação do sistema de recomendação-recomendações de item

A primeira das seis colunas representa as IDs de usuário fornecidas para recomendar itens para o, conforme fornecido pelos dados de entrada. As outras cinco colunas representam os itens recomendados para o usuário em ordem decrescente de relevância. Por exemplo, na primeira linha, o restaurante mais recomendado para o cliente U1048 é 134986, seguido por 135018, 134975, 135021 e 132862.

**Localizar usuários relacionados a um determinado usuário**

Ao selecionar **usuários relacionados** em **tipo de previsão de recomendação**, você está solicitando que o sistema de recomendação Localize usuários relacionados a um determinado usuário. Os usuários relacionados são os usuários que têm preferências semelhantes. O último parâmetro a ser escolhido nesse cenário é a *seleção de usuário relacionada*. A opção **de usuários que classificaram itens (para avaliação de modelo)** é principalmente para avaliação de modelo durante o processo de treinamento. Escolha **entre todos os usuários** para este estágio de previsão. A visualização da saída de [recomendação do Matchbox de Pontuação][score-matchbox-recommender] é semelhante à figura 23.

![Resultado da Pontuação do sistema de recomendação-usuários relacionados](./media/interpret-model-results/23.png)

Figura 23. Visualizar resultados da Pontuação do sistema de recomendação – usuários relacionados

A primeira das seis colunas mostra as IDs de usuário fornecidas necessárias para localizar usuários relacionados, conforme fornecido pelos dados de entrada. As outras cinco colunas armazenam os usuários relacionados previstos do usuário em ordem decrescente de relevância. Por exemplo, na primeira linha, o cliente mais relevante para o cliente U1048 é U1051, seguido por U1066, U1044, U1017 e U1072.

**Localizar itens relacionados a um determinado item**

Ao selecionar **itens relacionados** em **tipo de previsão de recomendação**, você está solicitando que o sistema de recomendação Encontre itens relacionados a um determinado item. Itens relacionados são os itens mais prováveis de serem curtidos pelo mesmo usuário. O último parâmetro a ser escolhido neste cenário é *seleção de item relacionado*. A opção **de itens classificados (para avaliação de modelo)** é principalmente para a avaliação do modelo durante o processo de treinamento. Escolhemos **todos os itens** para este estágio de previsão. A visualização da saída de [recomendação do Matchbox de Pontuação][score-matchbox-recommender] é semelhante à figura 24.

![Resultado da Pontuação do sistema de recomendação-itens relacionados](./media/interpret-model-results/24.png)

Figura 24. Visualizar resultados da Pontuação do sistema de recomendação-itens relacionados

A primeira das seis colunas representa as IDs de item fornecidas necessárias para encontrar itens relacionados, conforme fornecido pelos dados de entrada. As outras cinco colunas armazenam os itens relacionados previstos do item em ordem decrescente em termos de relevância. Por exemplo, na primeira linha, o item mais relevante para o item 135026 é 135074, seguido por 135035, 132875, 135055 e 134992.

**Publicação de serviço Web**

O processo de publicação desses experimentos como serviços Web para obter previsões é semelhante para cada um dos quatro cenários. Aqui, pegamos o segundo cenário (recomende itens para um determinado usuário) como exemplo. Você pode seguir o mesmo procedimento com os outros três.

Ao salvar o sistema de recomendação treinado como um modelo treinado e filtrar os dados de entrada para uma única coluna de ID de usuário, conforme solicitado, você pode conectar o teste como na figura 25 e publicá-lo como um serviço Web.

![Experimento de Pontuação do problema de recomendação do restaurante](./media/interpret-model-results/25.png)

Figura 25. Experimento de Pontuação do problema de recomendação do restaurante

Executando o serviço Web, o resultado retornado é semelhante à figura 26. Os cinco restaurantes recomendados para o usuário U1048 são 134986, 135018, 134975, 135021 e 132862.

![Exemplo de serviço do sistema de recomendação](./media/interpret-model-results/25_1.png)

![Resultados do experimento de exemplo](./media/interpret-model-results/26.png)

Figura 26. Resultado do serviço Web do problema de recomendação do restaurante

<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
