---
title: Interpretar os resultados dos modelos
titleSuffix: Azure Machine Learning Studio
description: Como escolher o parâmetro ideal definido para um algoritmo a utilizar e visualizar as saídas do modelo de pontuação.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 11/29/2017
ms.openlocfilehash: c46f22fb5c906aaffa48f39a0c643ca2a48573f9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877280"
---
# <a name="interpret-model-results-in-azure-machine-learning-studio"></a>Interpretar os resultados de modelo no Azure Machine Learning Studio
Este tópico explica como visualizar e interpretar os resultados de predição no Azure Machine Learning Studio. Depois de ter um modelo de preparação e fazer predições com base no mesmo ("classificada o modelo de"), tem de compreender e interpretar o resultado de predição.



Existem quatro tipos principais de modelos no Azure Machine Learning Studio de machine learning:

* Classificação
* Clustering
* Regressão
* Sistemas de recomendador

Os módulos utilizados para predição sobre estes modelos são:

* [Modelo de pontuação] [ score-model] módulo para a classificação e regressão
* [Atribuir a Clusters] [ assign-to-clusters] módulo para clustering
* [Pontuação Matchbox Recomendador] [ score-matchbox-recommender] para sistemas de recomendação

Este documento explica como interpretar os resultados da predição para cada um desses módulos. Para uma descrição geral destes módulos, consulte [como escolher parâmetros para otimizar os seus algoritmos no Azure Machine Learning Studio](algorithm-parameters-optimize.md).

Este tópico aborda a interpretação de previsão, mas não avaliação do modelo. Para obter mais informações sobre como avaliar o seu modelo, consulte [como avaliar o desempenho de modelo no Azure Machine Learning Studio](evaluate-model-performance.md).

Se estiver familiarizado com o Azure Machine Learning Studio e precisar de ajuda para criar uma experiência simples para começar, consulte [criar uma experimentação simple no Azure Machine Learning Studio](create-experiment.md) no Azure Machine Learning Studio.

## <a name="classification"></a>Classificação
Existem dois subcategorias dos problemas de classificação:

* Problemas com apenas duas classes (classificação de duas classes ou binária)
* Problemas com mais de duas classes (Roc classificação)

O Azure Machine Learning Studio tem diferentes módulos para lidar com cada um desses tipos de classificação, mas os métodos para interpretar os resultados de predição são semelhantes.

### <a name="two-class-classification"></a>Classificação de duas classes
**Experimentação de exemplo**

Um exemplo de um problema de classificação de duas classes é a classificação de flores íris semelhantes. A tarefa é classificar as flores íris com base nos seus recursos. O conjunto de dados de Iris fornecido no Azure Machine Learning Studio é um subconjunto das populares [conjunto de dados de Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set) que contém instâncias de apenas dois flor species (classes 0 e 1). Existem quatro recursos para cada flower (comprimento da sépala, largura de sépala, comprimento da Pétala e largura da Pétala).

![Captura de tela da experimentação de iris](./media/interpret-model-results/1.png)

Figura 1. Experimentação de problema de classificação de duas classes de Iris

Uma experimentação foi realizada para resolver este problema, conforme mostrado na figura 1. Um modelo de árvore de decisões elevada de duas classes foi treinado e classificado. Agora é possível visualizar os resultados da predição do [modelo de pontuação] [ score-model] módulo clicando na porta de saída do [Score Model] [ score-model] módulo e, em seguida, clicando em **Visualize**.

![Módulo do modelo de pontuação](./media/interpret-model-results/1_1.png)

Isso exibirá os resultados de classificação conforme mostrado na figura 2.

![Resultados da experimentação de classificação de duas classes de iris](./media/interpret-model-results/2.png)

Figura 2. Visualizar um resultado de modelo de pontuação na classificação de duas classes

**Interpretação de resultado**

Existem seis colunas na tabela de resultados. As colunas de quatro esquerdas são os quatro recursos. As colunas à direita duas etiquetas classificadas e pontuadas probabilidades, são os resultados da predição. A coluna de probabilidades classificadas mostra a probabilidade de que uma flor pertence à classe positiva (classe 1). Por exemplo, o primeiro número na coluna (0.028571) significa lá é 0.028571 probabilidade de que a flor primeiro se pertence a 1 de classe. A coluna de etiquetas classificadas mostra a classe prevista para cada flower. Isso se baseia na coluna classificada probabilidades. Se a probabilidade com a pontuação de uma flor é maior do que 0,5, se previr como de classe 1. Caso contrário, se previr como classe 0.

**Publicação de serviços da Web**

Depois dos resultados da predição foram compreendidos e considerados som, a experimentação pode ser publicada como um serviço web para que pode implementá-la em vários aplicativos e chamá-lo para obter previsões de classe de qualquer novo flor de íris. Para saber como alterar uma experimentação de preparação para uma experimentação de classificação e publicá-lo como um serviço web, consulte [Tutorial 3: Implementar o modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md). Este procedimento fornece uma experiência de classificação conforme mostrado na figura 3.

![Captura de tela de experimentação de classificação](./media/interpret-model-results/3.png)

Figura 3. A experimentação de problema de classificação de duas classes de íris de classificação

Agora precisa definir a entrada e saída para o serviço web. A entrada é a porta de entrada à direita da [modelo de pontuação][score-model], que é a flor de íris funcionalidades de entrada. A escolha da saída depende se estiver interessado na classe prevista (etiqueta com a pontuação), a probabilidade com a pontuação ou ambos. Neste exemplo, presume que está interessado em ambos. Para selecionar as colunas de saída desejada, utilize um [selecionar colunas no conjunto de dados] [ select-columns] módulo. Clique em [selecionar colunas no conjunto de dados][select-columns], clique em **iniciar Seletor de colunas**e selecione **etiquetas classificadas** e **Scored Probabilidades**. Depois de definir a porta de saída [selecionar colunas no conjunto de dados] [ select-columns] e executá-lo novamente, deve estar pronto para publicar a experimentação de classificação como um serviço da web ao clicar em **publicar WEB SERVICE** . A experimentação final se parece com a figura 4.

![A experimentação de classificação de duas classes de iris](./media/interpret-model-results/4.png)

Figura 4. Experimentação final de classificação de um problema de classificação de duas classes de iris

Depois de executar o serviço web e introduzir alguns valores de funcionalidade de uma instância de teste, o resultado devolve dois números. O primeiro número é a etiqueta com a pontuação e o segundo é a probabilidade com a pontuação. Este flor é prever como de classe 1 com probabilidade 0.9655.

![Modelo de pontuação de interpretação de teste](./media/interpret-model-results/4_1.png)

![Resultados do teste de classificação](./media/interpret-model-results/5.png)

Figura 5. Resultado do serviço Web de classificação de duas classes de iris

### <a name="multi-class-classification"></a>Classificação de Roc
**Experimentação de exemplo**

Nesta experimentação, vai realizar uma tarefa de reconhecimento de letra como um exemplo de classificação multiclasses. O classificador tenta prever uma determinada letra (classe) com base em alguns valores de atributo escrito à mão extraídos a partir das imagens escrito à mão.

![Exemplo de reconhecimento de letra](./media/interpret-model-results/5_1.png)

Os dados de treinamento, existem 16 recursos extraídos a partir de imagens de letra escrito à mão. Nossas classes de 26 de formulário de 26 letras. Figura 6 mostra uma experimentação que irá formar um modelo de classificação multiclasses para reconhecimento de letra e prever a mesma funcionalidade definido num conjunto de dados de teste.

![Experimentação de classificação de várias classes de reconhecimento de letra](./media/interpret-model-results/6.png)

Figura 6. Experimentação de problema de classificação de várias classes de reconhecimento de letra

Visualizar os resultados a partir da [Score Model] [ score-model] módulo clicando na porta de saída de [Score Model] [ score-model] módulo e, em seguida, clicando em **Visualize**, deverá ver o conteúdo conforme mostrado na figura 7.

![Resultados de modelo de pontuação](./media/interpret-model-results/7.png)

Figura 7. Visualizar os resultados de modelo de pontuação numa classificação Roc

**Interpretação de resultado**

As colunas de 16 esquerdas representam os valores da característica do conjunto de teste. As colunas com nomes como probabilidades classificadas para a classe "XX" são apenas como a coluna de probabilidades classificadas no caso de duas classes. Mostram a probabilidade de que a entrada correspondente se encaixa numa determinada classe. Por exemplo, para a primeira entrada, existe 0.003571 probabilidade, que é um "A," probabilidade 0.000451 que é uma "B" e assim por diante. A última coluna (etiquetas classificadas) é o mesmo que etiquetas classificadas no caso de duas classes. Seleciona a classe com a maior probabilidade de classificados como a classe prevista de entrada correspondente. Por exemplo, para a primeira entrada, a etiqueta com a pontuação é "F", pois ele tem a maior probabilidade de ser um "F" (0.916995).

**Publicação de serviços da Web**

Também pode obter a etiqueta com a pontuação para cada entrada e a probabilidade da etiqueta com a pontuação. A lógica básica é encontrar a maior probabilidade entre todas as probabilidades classificadas. Para tal, tem de utilizar o [executar Script R] [ execute-r-script] módulo. O código de R é mostrado na figura 8 e o resultado da experimentação é mostrado na figura 9.

![Exemplo de código de R](./media/interpret-model-results/8.png)

Figura 8. Código de R para extrair etiquetas classificadas e as probabilidades associadas das etiquetas

![Resultado de experimentação](./media/interpret-model-results/9.png)

Figura 9. Experimentação final de classificação do problema de classificação de várias classes de reconhecimento de letra

Depois de publicar e executar o serviço web e introduzir alguns valores de entrada de recurso, o aspeto do resultado devolvido semelhante à figura 10. Esta letra escrito à mão, com seus recursos de 16 extraídos, é prevista para ser um "T" com probabilidade 0.9715.

![Módulo de pontuação de interpretação de testes](./media/interpret-model-results/9_1.png)

![Resultado do teste](./media/interpret-model-results/10.png)

Figura 10. Resultado do serviço Web de classificação multiclasses

## <a name="regression"></a>Regressão
Problemas de regressão são diferentes dos problemas de classificação. Num problema de classificação, que está a tentar prever classes discretos, tais como que classe uma flor de íris pertence. Mas como pode ver no exemplo seguinte de um problema de regressão, que está a tentar prever numa variável contínua, por exemplo, o preço de um carro.

**Experimentação de exemplo**

Utilize predição do preço de automóveis como seu exemplo de regressão. Está a tentar prever o preço de um automóvel com base nos seus recursos, incluindo a marca, tipo de combustível, tipo de corpo e roda de unidade. A experimentação é mostrada na figura 11.

![Experimentação de regressão de preço de automóveis](./media/interpret-model-results/11.png)

Figura 11. Experimentação de problema de regressão de preço de automóveis

Visualizar os [modelo de pontuação] [ score-model] módulo, o resultado é semelhante a figura 12.

![Resultados de classificação para o problema de predição do preço de automóveis](./media/interpret-model-results/12.png)

Figura 12. Resultado de classificação para o problema de predição do preço de automóveis

**Interpretação de resultado**

Etiquetas classificadas é a coluna de resultado neste resultado de classificação. Os números são o preço previsto para cada carro.

**Publicação de serviços da Web**

Pode publicar a experimentação de regressão para um serviço da web e chamá-lo para previsão de preços de automóveis da mesma forma como no caso de utilização de classificação de duas classes.

![Experimentação de classificação de problema de regressão de preço de automóveis](./media/interpret-model-results/13.png)

Figura 13. Experimentação de um problema de regressão de preço de automóveis de classificação

Executar o serviço web, o resultado retornado é semelhante a figura 14. O preço previsto esse carro é US $15,085.52.

![Módulo de pontuação interpretação de teste](./media/interpret-model-results/13_1.png)

![Resultados do módulo de classificação](./media/interpret-model-results/14.png)

Figura 14. Resultado do serviço Web de um problema de regressão de preço de automóveis

## <a name="clustering"></a>Clustering
**Experimentação de exemplo**

Vamos usar o conjunto de dados de Iris novamente para criar uma experimentação de clustering. Aqui pode filtrar as etiquetas de classe no conjunto de dados para que apenas tem recursos e pode ser utilizado para o clustering. Neste íris caso de utilização, especifique o número de clusters dois durante o processo de treinamento, o que significa que poderia agrupar as flores em duas classes. A experimentação é mostrada na figura 15.

![Experimente o problema de clustering de Iris](./media/interpret-model-results/15.png)

Figura 15. Experimente o problema de clustering de Iris

Clustering é diferente da classificação em que o conjunto de dados de treinamento não tem etiquetas de verdade zero por si só. Clusters de grupos de instâncias de conjunto de dados de treinamento em clusters diferentes. Durante o processo de treinamento, o modelo das etiquetas as entradas aprendendo as diferenças entre as respetivas funcionalidades. Depois disso, pode servir-se o modelo preparado para classificar ainda mais entradas futuras. Existem duas partes do resultado que estamos interessados em dentro de um problema de clustering. A primeira parte é a etiquetagem o conjunto de dados de treinamento e o segundo é classificar um novo conjunto de dados com o modelo preparado.

A primeira parte do resultado pode ser visualizada clicando na porta saída à esquerda do [Train Clustering Model] [ train-clustering-model] e, em seguida, clicando em **Visualize**. A visualização é mostrada na figura 16.

![Clustering de resultado](./media/interpret-model-results/16.png)

Figura 16. Visualizar o clustering de resultado para o conjunto de dados de treinamento

O resultado da segunda parte, clustering novas entradas com o modelo de clustering preparado, é mostrado na figura 17.

![Visualizar o clustering de resultado](./media/interpret-model-results/17.png)

Figura 17. Visualizar o clustering resultado num novo conjunto de dados

**Interpretação de resultado**

Embora os resultados de duas partes se originam da experimentação diferentes estágios, eles parecem iguais e são interpretados dentro da mesma forma. As quatro primeiras colunas são recursos. A última coluna, atribuições, é o resultado de predição. As entradas atribuídas o mesmo número estão previstas para estar no mesmo cluster, ou seja, que eles tenham semelhanças de alguma forma (desta experiência utiliza a métrica de distância Euclidiana predefinida). Uma vez que especificar o número de clusters para ser 2, as entradas em atribuições estão identificados como 0 ou 1.

**Publicação de serviços da Web**

Pode publicar a experimentação de clustering para um serviço da web e chamá-lo para o clustering de previsões de caso de utilização da mesma forma como a classificação de duas classes.

![Experimentação de classificação para o problema de clustering de iris](./media/interpret-model-results/18.png)

Figura 18. Experimentação de um problema de clustering de íris de classificação

Depois de executar o serviço web, o resultado retornado é semelhante à figura 19. Este flor é prevista para ser em cluster 0.

![Teste interpretar o módulo de pontuação](./media/interpret-model-results/18_1.png)

![Resultado do módulo de classificação](./media/interpret-model-results/19.png)

Figura 19. Resultado do serviço Web de classificação de duas classes de iris

## <a name="recommender-system"></a>Sistema de recomendador
**Experimentação de exemplo**

Para sistemas de recomendador, pode usar o problema de recomendação do restaurante como um exemplo: pode recomendar restaurantes para clientes com base no respetivo histórico de classificação. Os dados de entrada é composta por três partes:

* Classificações de restaurante de clientes
* Dados de funcionalidade do cliente
* Dados de funcionalidade do restaurante

Há várias coisas que podemos fazer com o [Train Matchbox Recomendador] [ train-matchbox-recommender] módulo no Azure Machine Learning Studio:

* Prever as classificações para um determinado usuário e item
* Recomendar itens para um determinado utilizador
* Encontrar utilizadores relacionados com um determinado utilizador
* Localizar itens relacionados a determinado item

Pode escolher o que deseja fazer ao selecionar de entre as quatro opções no **tipo de predição de Recomendador** menu. Aqui pode percorrer todos os quatro cenários.

![Recomendador matchbox](./media/interpret-model-results/19_1.png)

Uma experimentação do Azure Machine Learning Studio típica para um sistema de recomendador é semelhante a figura 20. Para obter informações sobre como utilizar os módulos de recomendador do sistema, consulte [recomendador de matchbox Train] [ train-matchbox-recommender] e [recomendador matchbox de pontuação] [ score-matchbox-recommender].

![Experimentação do sistema de recomendador](./media/interpret-model-results/20.png)

Figura 20. Experimentação do sistema de recomendador

**Interpretação de resultado**

**Prever as classificações para um determinado usuário e item**

Selecionando **predição de classificação** sob **tipo de predição de Recomendador**, estão fazendo o sistema de recomendador para prever a classificação para um determinado usuário e item. A visualização do [pontuação Matchbox Recomendador] [ score-matchbox-recommender] saída é semelhante a figura 21.

![Resultado do sistema de recomendador - previsão de classificação de pontuação](./media/interpret-model-results/21.png)

Figura 21. Visualizar o resultado de pontuação do sistema de recomendador - previsão de classificação

As duas primeiras colunas são os pares de item de utilizador fornecidos pelos dados de entrada. A terceira coluna é a classificação prevista de um utilizador para um determinado item. Por exemplo, na primeira linha, o cliente U1048 é previsto para restaurante de taxa 135026 como 2.

**Recomendar itens para um determinado utilizador**

Selecionando **recomendação do Item** sob **tipo de predição de Recomendador**, está pedindo o sistema de recomendador para recomendar itens para um determinado utilizador. O último parâmetro para escolher neste cenário é *recomendado seleção de item*. A opção **partir da classificada de itens (para avaliação do modelo)** é principalmente para avaliação do modelo durante o processo de treinamento. Para esta fase de previsão, escolhemos **de todos os itens**. A visualização do [pontuação Matchbox Recomendador] [ score-matchbox-recommender] saída é semelhante a figura 22.

![Resultados de pontuação do sistema de recomendador – recomendação de item](./media/interpret-model-results/22.png)

Figura 22. Visualize os resultados de pontuação do sistema de recomendador – recomendação de item

A primeira das seis colunas representa o utilizador especificado IDs para recomendar itens, como a fornecida pelos dados de entrada. Outras cinco colunas representam os itens recomendados ao usuário na ordem decrescente de relevância. Por exemplo, na primeira linha, o restaurante mais recomendado para o cliente U1048 é 134986, seguido de 135018, 134975, 135021 e 132862.

**Encontrar utilizadores relacionados com um determinado utilizador**

Selecionando **utilizadores relacionados** sob **tipo de predição de Recomendador**, está pedindo o sistema de recomendador para encontrar utilizadores relacionados a um determinado usuário. Os utilizadores relacionados são os utilizadores que têm as preferências de semelhante. O último parâmetro para escolher neste cenário é *relacionados com a seleção do usuário*. A opção **de utilizadores que classificado itens (para avaliação do modelo)** é principalmente para avaliação do modelo durante o processo de treinamento. Escolher **de todos os utilizadores** para esta fase de previsão. A visualização do [pontuação Matchbox Recomendador] [ score-matchbox-recommender] saída é semelhante a figura 23.

![Resultados de pontuação do sistema de recomendador – os utilizadores relacionados](./media/interpret-model-results/23.png)

Figura 23. Visualizar os resultados de pontuação do sistema de recomendador – os utilizadores relacionados

O primeiro de seis colunas mostra o utilizador especificado que IDs necessários para localizar utilizadores relacionados, como a fornecida por dados de entrada. As outras cinco colunas armazenam os utilizadores relacionados previstos do utilizador, por ordem descendente de relevância. Por exemplo, na primeira linha, o cliente mais relevante para o cliente U1048 é U1051, seguido de U1066, U1044, U1017 e U1072.

**Localizar itens relacionados a determinado item**

Selecionando **itens relacionados** sob **tipo de predição de Recomendador**, estão fazendo o sistema de recomendador para localizar itens relacionados a determinado item. Itens relacionados são os itens mais probabilidades de ser gostou pelo mesmo utilizador. O último parâmetro para escolher neste cenário é *relacionados com a seleção de item*. A opção **partir da classificada de itens (para avaliação do modelo)** é principalmente para avaliação do modelo durante o processo de treinamento. Escolhemos **de todos os itens** para esta fase de previsão. A visualização do [pontuação Matchbox Recomendador] [ score-matchbox-recommender] saída é semelhante a figura 24.

![Resultados de pontuação do sistema de recomendador – itens relacionados](./media/interpret-model-results/24.png)

Figura 24. Visualizar os resultados de pontuação do sistema de recomendador – itens relacionados

A primeira das seis colunas representa o determinado item IDs necessários para localizar itens relacionados, como a fornecida pelos dados de entrada. As outras cinco colunas armazenam os itens relacionados previstos do item de descendentemente em termos de relevância. Por exemplo, na primeira linha, o item mais relevante para o item 135026 é 135074, seguido de 135035, 132875, 135055 e 134992.

**Publicação de serviços da Web**

O processo de publicação dessas experiências como serviços da web para obter previsões de indisponibilidade é semelhante para cada um dos quatro cenários. Aqui, vamos dar o segundo cenário (itens recomendados para um determinado usuário) como exemplo. Pode seguir o mesmo procedimento com as outras três.

A guardar o sistema de recomendador treinado como um modelo preparado e filtrar os dados de entrada para uma coluna de ID de utilizador único, conforme solicitado, pode conectar a experimentação como na figura 25 e publicá-lo como um serviço web.

![Experimentação do problema de recomendação restaurante de classificação](./media/interpret-model-results/25.png)

Figura 25. Experimentação do problema de recomendação restaurante de classificação

Executar o serviço web, o resultado retornado é semelhante a figura 26. Os cinco restaurantes recomendadas para o utilizador U1048 são 134986, 135018, 134975, 135021 e 132862.

![Exemplo de serviço do sistema de recomendador](./media/interpret-model-results/25_1.png)

![Resultados da experimentação de exemplo](./media/interpret-model-results/26.png)

Figura 26. Resultado do serviço Web de problema de recomendação do restaurante

<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
