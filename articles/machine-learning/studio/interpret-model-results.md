---
title: Interpretar resultados do modelo
titleSuffix: ML Studio (classic) - Azure
description: Como escolher o parâmetro ideal definido para um algoritmo utilizando e visualizando as saídas do modelo de pontuação.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 11/29/2017
ms.openlocfilehash: 9a0b855f48085138b28e02e0a5d01c5dd0f666be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79218070"
---
# <a name="interpret-model-results-in-azure-machine-learning-studio-classic"></a>Interpretar resultados do modelo no Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Este tópico explica como visualizar e interpretar resultados de previsão no Azure Machine Learning Studio (clássico). Depois de ter treinado um modelo e feito previsões em cima dele ("marcou o modelo"), é preciso compreender e interpretar o resultado da previsão.



Existem quatro grandes tipos de modelos de machine learning no Azure Machine Learning Studio (clássico):

* Classificação
* Clustering
* Regressão
* Sistemas de recomendação

Os módulos utilizados para a previsão em cima destes modelos são:

* [Módulo modelo de pontuação][score-model] para classificação e regressão
* Atribuir ao módulo [clusters][assign-to-clusters] para agrupamento
* [Score Matchbox Recomendador][score-matchbox-recommender] para sistemas de recomendação

Este documento explica como interpretar os resultados da previsão para cada um destes módulos. Para uma visão geral destes módulos, consulte [como escolher parâmetros para otimizar os seus algoritmos no Azure Machine Learning Studio (clássico)](algorithm-parameters-optimize.md).

Este tópico aborda a interpretação da previsão, mas não a avaliação do modelo. Para obter mais informações sobre como avaliar o seu modelo, consulte como avaliar o desempenho do [modelo no Azure Machine Learning Studio (clássico)](evaluate-model-performance.md).

Se você é novo no Azure Machine Learning Studio (clássico) e precisa de ajuda para criar uma experiência simples para começar, consulte [Criar uma experiência simples no Azure Machine Learning Studio (clássico)](create-experiment.md).

## <a name="classification"></a>Classificação
Existem duas subcategorias de problemas de classificação:

* Problemas com apenas duas classes (classificação de duas classes ou binário)
* Problemas com mais de duas classes (classificação multi-classe)

O Azure Machine Learning Studio (clássico) tem diferentes módulos para lidar com cada um destes tipos de classificação, mas os métodos para interpretar os seus resultados de previsão são semelhantes.

### <a name="two-class-classification"></a>Classificação de duas classes
**Experimentação de exemplo**

Um exemplo de um problema de classificação de duas classes é a classificação das flores da íris. A tarefa é classificar as flores da íris com base nas suas características. O conjunto de dados da Íris fornecido no Azure Machine Learning Studio (clássico) é um subconjunto do popular conjunto de dados da [Íris](https://en.wikipedia.org/wiki/Iris_flower_data_set) que contém instâncias de apenas duas espécies de flores (classes 0 e 1). Existem quatro características para cada flor (comprimento sepal, largura sepal, comprimento de pétala e largura de pétala).

![Screenshot da experiência da íris](./media/interpret-model-results/1.png)

Figura 1: Experiência de problema de classificação de duas classes da Íris

Foi realizada uma experiência para resolver este problema, como mostra a Figura 1. Um modelo de árvore de decisão de duas classes foi treinado e marcado. Agora pode visualizar os resultados da previsão do módulo ['Modelo de Pontuação',][score-model] clicando na porta de saída do módulo ['Modelo de Pontuação'][score-model] e, em seguida, clicando em **Visualizar**.

![Módulo de modelo de pontuação](./media/interpret-model-results/1_1.png)

Isto eleva os resultados das pontuações, como mostra a Figura 2.

![Resultados da experiência de classificação de duas classes da íris](./media/interpret-model-results/2.png)

Figura 2. Visualizar um resultado de um modelo de pontuação na classificação de duas classes

**Interpretação do resultado**

Há seis colunas na tabela de resultados. As quatro colunas esquerdas são as quatro características. As duas colunas certas, etiquetas pontuais e probabilidades pontuadas, são os resultados da previsão. A coluna De Probabilidades Pontuais mostra a probabilidade de uma flor pertencer à classe positiva (Classe 1). Por exemplo, o primeiro número na coluna (0.028571) significa que há 0,028571 probabilidade de que a primeira flor pertença à classe 1. A coluna Etiquetas Pontuais mostra a classe prevista para cada flor. Isto baseia-se na coluna De Probabilidades Pontuais. Se a probabilidade pontuada de uma flor for maior que 0,5, prevê-se como classe 1. Caso contrário, prevê-se como classe 0.

**Publicação do serviço web**

Depois de os resultados da previsão terem sido compreendidos e julgados som, a experiência pode ser publicada como um serviço web para que possa implantá-la em várias aplicações e chamá-la para obter previsões de classe em qualquer nova flor de íris. Para aprender a transformar uma experiência de formação numa experiência de pontuação e publicá-la como um serviço web, consulte [Tutorial 3: Implementar modelo](tutorial-part3-credit-risk-deploy.md)de risco de crédito . Este procedimento proporciona-lhe uma experiência de pontuação, como mostra a Figura 3.

![Screenshot da experiência de pontuação](./media/interpret-model-results/3.png)

Figura 3. Pontuação da experiência de problema de classificação de duas classes da íris

Agora precisa definir a entrada e a saída para o serviço web. A entrada é a entrada certa do [Modelo de Pontuação,][score-model]que é a entrada da flor Iris. A escolha da saída depende se você está interessado na classe prevista (etiqueta pontuada), na probabilidade pontuada, ou ambos. Neste exemplo, presume-se que está interessado em ambos. Para selecionar as colunas de saída desejadas, utilize uma Coluna Select no módulo conjunto de [dados.][select-columns] Clique [em Selecionar Colunas no conjunto][select-columns]de dados, clique no seletor de **colunas de lançamento,** e selecione **Etiquetas pontuadas** e **probabilidades pontuadas**. Depois de definir a porta de saída de [Colunas Select em conjunto][select-columns] de dados e executá-la novamente, deve estar pronto para publicar a experiência de pontuação como um serviço web clicando em **PUBLICAR SERVIÇO WEB**. A experiência final parece a Figura 4.

![A experiência de classificação de duas classes da íris](./media/interpret-model-results/4.png)

Figura 4. Experiência final de pontuação de um problema de classificação de duas classes da íris

Depois de executar o serviço web e introduzir alguns valores de características de uma instância de teste, o resultado devolve dois números. O primeiro número é o rótulo marcado, e o segundo é a probabilidade pontuada. Esta flor é prevista como classe 1 com 0,9655 probabilidade.

![Modelo de pontuação de interpretação de teste](./media/interpret-model-results/4_1.png)

![Resultados dos testes de pontuação](./media/interpret-model-results/5.png)

Figura 5. Resultado do serviço web da classificação de duas classes da íris

### <a name="multi-class-classification"></a>Classificação multi-classe
**Experimentação de exemplo**

Nesta experiência, realiza-se uma tarefa de reconhecimento de cartas como exemplo de classificação multiclasse. O classificador tenta prever uma determinada letra (classe) com base em alguns valores de atributo escritos à mão extraídos das imagens escritas à mão.

![Exemplo de reconhecimento de cartas](./media/interpret-model-results/5_1.png)

Nos dados de treino, existem 16 funcionalidades extraídas de imagens de letras escritas à mão. As 26 letras formam as nossas 26 aulas. A Figura 6 mostra uma experiência que irá treinar um modelo de classificação multiclasse para reconhecimento de letras e prever na mesma funcionalidade definida num conjunto de dados de teste.

![Experiência de classificação multiclasse de reconhecimento de cartas](./media/interpret-model-results/6.png)

Figura 6. Experiência de problema de classificação multiclasse de reconhecimento de letras

Visualizando os resultados do módulo ['Modelo de Pontuação',][score-model] clicando na porta de saída do módulo 'Modelo de [Pontuação'][score-model] e, em seguida, clicando em **Visualizar,** deve ver o conteúdo como mostrado na Figura 7.

![Resultados do modelo de pontuação](./media/interpret-model-results/7.png)

Figura 7. Visualizar o modelo de pontuação resulta numa classificação multi-classe

**Interpretação do resultado**

As 16 colunas esquerdas representam os valores de característica do conjunto de teste. As colunas com nomes como Pontuações de Probabilidades para classe "XX" são como a coluna De Probabilidades Pontuais no caso de duas classes. Mostram a probabilidade de a entrada correspondente cair numa determinada classe. Por exemplo, para a primeira entrada, há 0,003571 probabilidade de ser uma probabilidade "A", 0,000451 de que é um "B", e assim por diante. A última coluna (Etiquetas Pontuais) é a mesma que etiquetas pontuais no caso de duas classes. Ele seleciona a classe com a maior probabilidade de pontuação como a classe prevista da entrada correspondente. Por exemplo, para a primeira entrada, o rótulo marcado é "F", uma vez que tem a maior probabilidade de ser um "F" (0,916995).

**Publicação do serviço web**

Também pode obter o rótulo de pontuação para cada entrada e a probabilidade do rótulo marcado. A lógica básica é encontrar a maior probabilidade entre todas as probabilidades pontuadas. Para isso, é necessário utilizar o módulo [Execute R Script.][execute-r-script] O código R é mostrado na Figura 8, e o resultado da experiência é mostrado na Figura 9.

![Exemplo de código R](./media/interpret-model-results/8.png)

Figura 8. Código R para a extração de etiquetas pontuadas e as probabilidades associadas dos rótulos

![Resultado da experiência](./media/interpret-model-results/9.png)

Figura 9. Experiência final de pontuação do problema de classificação multiclasse de reconhecimento de letras

Depois de publicar e executar o serviço web e introduzir alguns valores de funcionalidade de entrada, o resultado devolvido parece a Figura 10. Esta carta escrita à mão, com as suas 16 características extraídas, prevê-se que seja um "T" com 0,9715 probabilidades.

![Módulo de pontuação de interpretação de teste](./media/interpret-model-results/9_1.png)

![Resultado do teste](./media/interpret-model-results/10.png)

Figura 10. Resultado do serviço web da classificação multiclasse

## <a name="regression"></a>Regressão
Os problemas de regressão são diferentes dos problemas de classificação. Num problema de classificação, estás a tentar prever classes discretas, como a classe a que uma flor de íris pertence. Mas como pode ver no exemplo seguinte de um problema de regressão, está a tentar prever uma variável contínua, como o preço de um carro.

**Experimentação de exemplo**

Use a previsão do preço do automóvel como exemplo para a regressão. Está a tentar prever o preço de um carro com base nas suas características, incluindo a make, o tipo de combustível, o tipo de carroçae e a roda de tração. A experiência é mostrada na Figura 11.

![Experiência de regressão do preço do automóvel](./media/interpret-model-results/11.png)

Figura 11. Experiência de problema de regressão do preço do automóvel

Visualizando o módulo [Score Model,][score-model] o resultado parece figura 12.

![Resultados de pontuação para o problema da previsão de preços do automóvel](./media/interpret-model-results/12.png)

Figura 12. Resultado de pontuação para o problema da previsão de preços do automóvel

**Interpretação do resultado**

Etiquetas pontuais é a coluna de resultados neste resultado de pontuação. Os números são o preço previsto para cada carro.

**Publicação do serviço web**

Pode publicar a experiência de regressão num serviço web e chamá-la para previsão de preços de automóvel da mesma forma que no caso de utilização de classificação de duas classes.

![Experiência de pontuação para problema de regressão dos preços do automóvel](./media/interpret-model-results/13.png)

Figura 13. Experiência de pontuação de um problema de regressão do preço do automóvel

Funcionando o serviço web, o resultado devolvido parece figura 14. O preço previsto para este carro é $15.085,52.

![Teste de interpretação do módulo de pontuação](./media/interpret-model-results/13_1.png)

![Resultados do módulo de pontuação](./media/interpret-model-results/14.png)

Figura 14. Resultado do serviço web de um problema de regressão dos preços do automóvel

## <a name="clustering"></a>Clustering
**Experimentação de exemplo**

Vamos usar o conjunto de dados da Íris novamente para construir uma experiência de agrupamento. Aqui pode filtrar as etiquetas de classe no conjunto de dados para que só tenha funcionalidades e possa ser usada para o agrupamento. Neste caso de utilização da íris, especifique o número de aglomerados para ser dois durante o processo de treino, o que significa que você iria agrupar as flores em duas classes. A experiência é mostrada na Figura 15.

![Experiência de problema de cluster iris](./media/interpret-model-results/15.png)

Figura 15. Experiência de problema de cluster iris

O agrupamento difere da classificação na medida em que o conjunto de dados de treino não tem rótulos de verdade terrestre por si só. Agrupamentos de dados de formação, os conjuntos de dados de formação em clusters distintos. Durante o processo de formação, o modelo rotula as entradas aprendendo as diferenças entre as suas características. Depois disso, o modelo treinado pode ser usado para classificar ainda mais as futuras entradas. Há duas partes do resultado que nos interessam por um problema de agrupamento. A primeira parte é a rotulagem do conjunto de dados de formação, e a segunda é classificar um novo conjunto de dados com o modelo treinado.

A primeira parte do resultado pode ser visualizada clicando na porta de saída esquerda do Modelo de Clustering de [Comboios][train-clustering-model] e, em seguida, clicando em **Visualizar**. A visualização é mostrada na Figura 16.

![Resultado do agrupamento](./media/interpret-model-results/16.png)

Figura 16. Visualizar o resultado do agrupamento para o conjunto de dados de formação

O resultado da segunda parte, agrupamento de novas entradas com o modelo de agrupamento treinado, é mostrado na Figura 17.

![Visualizar o resultado do agrupamento](./media/interpret-model-results/17.png)

Figura 17. Visualizar o resultado do clustering num novo conjunto de dados

**Interpretação do resultado**

Embora os resultados das duas partes decorrem de diferentes fases de experimentação, elas parecem iguais e são interpretadas da mesma forma. As primeiras quatro colunas são características. A última coluna, Atribuições, é o resultado da previsão. Prevê-se que as entradas atribuídas ao mesmo número estejam no mesmo cluster, ou seja, partilham semelhanças de alguma forma (esta experiência utiliza a métrica de distância euclidea predefinida). Como especificou o número de clusters a 2, as entradas em Atribuições são rotuladas como 0 ou 1.

**Publicação do serviço web**

Pode publicar a experiência de agrupamento num serviço web e chamá-la para previsões de agrupamento da mesma forma que no caso de utilização de classificação de duas classes.

![Experiência de pontuação para problema de agrupamento de íris](./media/interpret-model-results/18.png)

Figura 18. Experiência de pontuação de um problema de agrupamento de íris

Depois de executar o serviço web, o resultado devolvido parece a Figura 19. Prevê-se que esta flor esteja no aglomerado 0.

![Teste interpretar módulo de pontuação](./media/interpret-model-results/18_1.png)

![Resultado do módulo de pontuação](./media/interpret-model-results/19.png)

Figura 19. Resultado do serviço web da classificação de duas classes da íris

## <a name="recommender-system"></a>Sistema de recomendação
**Experimentação de exemplo**

Para sistemas de recomendação, pode utilizar o problema da recomendação do restaurante como exemplo: pode recomendar restaurantes para clientes com base no seu histórico de classificação. Os dados de entrada são constituídos por três partes:

* Classificações de restaurantes de clientes
* Dados da funcionalidade do cliente
* Dados de características do restaurante

Há várias coisas que podemos fazer com o módulo [recomendador Train Matchbox][train-matchbox-recommender] no Estúdio de Aprendizagem automática Azure (clássico):

* Prever classificações para um determinado utilizador e item
* Recomendar itens a um determinado utilizador
* Encontre utilizadores relacionados com um determinado utilizador
* Encontre itens relacionados com um determinado item

Pode escolher o que pretende fazer selecionando entre as quatro opções do menu **do tipo previsão do Recomendador.** Aqui pode percorrer os quatro cenários.

![Recomendador de caixa de fósforos](./media/interpret-model-results/19_1.png)

Uma experiência típica do Azure Machine Learning Studio (clássico) para um sistema de recomendação parece a Figura 20. Para obter informações sobre como utilizar os módulos do sistema recomendador, consulte o recomendador de [matchbox do Comboio][train-matchbox-recommender] e o [recomendador de matchbox Score][score-matchbox-recommender].

![Experiência do sistema de recomendação](./media/interpret-model-results/20.png)

Figura 20. Experiência do sistema de recomendação

**Interpretação do resultado**

**Prever classificações para um determinado utilizador e item**

Ao selecionar a Previsão de **Classificação** sob o tipo de **previsão do Recomendador,** está a pedir ao sistema de recomendação que preveja a classificação de um determinado utilizador e item. A visualização da saída do Recomendor score [Matchbox][score-matchbox-recommender] parece a Figura 21.

![Resultado da pontuação do sistema de recomendação - previsão de classificação](./media/interpret-model-results/21.png)

Figura 21. Visualizar o resultado da pontuação do recomendador do sistema -- previsão de classificação

As duas primeiras colunas são os pares de artigos de utilizador fornecidos pelos dados de entrada. A terceira coluna é a classificação prevista de um utilizador para um determinado item. Por exemplo, na primeira fila, prevê-se que o cliente U1048 arate restaurante 135026 como 2.

**Recomendar itens a um determinado utilizador**

Ao selecionar a Recomendação do **Item** no âmbito do tipo de **previsão do Recomendador,** está a pedir ao sistema de recomendação que recomende itens a um determinado utilizador. O último parâmetro a escolher neste cenário é *a seleção recomendada do item*. A opção **From Rated Itens (para avaliação de modelos)** é principalmente para avaliação de modelodurante o processo de formação. Para esta fase de previsão, escolhemos **Entre Todos os Itens.** A visualização da saída do Recomendor score [Matchbox][score-matchbox-recommender] parece a Figura 22.

![Resultado da pontuação do sistema de recomendação -- recomendação do item](./media/interpret-model-results/22.png)

Figura 22. Visualizar o resultado da pontuação do sistema recomendador -- recomendação do item

A primeira das seis colunas representa as iDs do utilizador para recomendar itens, conforme fornecidos pelos dados de entrada. As outras cinco colunas representam os itens recomendados ao utilizador por ordem de relevância descendente. Por exemplo, na primeira fila, o restaurante mais recomendado para o cliente U1048 é 134986, seguido por 135018, 134975, 135021 e 132862.

**Encontre utilizadores relacionados com um determinado utilizador**

Ao selecionar **utilizadores relacionados** sob o tipo de **previsão do Recomendador,** está a pedir ao sistema de recomendação que encontre utilizadores relacionados com um determinado utilizador. Os utilizadores relacionados são os utilizadores que têm preferências semelhantes. O último parâmetro a escolher neste cenário é a *seleção de utilizadores relacionados.* A opção **dos utilizadores que avaliaram itens (para avaliação de modelos)** é principalmente para avaliação de modelos durante o processo de formação. Escolha **entre todos os utilizadores** para esta fase de previsão. A visualização da saída do Recomendor score [Matchbox][score-matchbox-recommender] parece a Figura 23.

![Resultado da pontuação do sistema de recomendação -utilizadores relacionados](./media/interpret-model-results/23.png)

Figura 23. Visualizar os resultados da pontuação dos utilizadores relacionados com o sistema de recomendação

A primeira das seis colunas mostra os iDs de utilizador necessários para encontrar utilizadores relacionados, conforme fornecido pelos dados de entrada. As outras cinco colunas armazenam os utilizadores relacionados previstos do utilizador por ordem descendente de relevância. Por exemplo, na primeira fila, o cliente mais relevante para o cliente U1048 é o U1051, seguido dos Sub-1066, Sub1044, Sub1017 e U1072.

**Encontre itens relacionados com um determinado item**

Ao selecionar **itens relacionados** sob o tipo de **previsão do Recomendador,** está a pedir ao sistema de recomendação que encontre itens relacionados com um determinado item. Itens relacionados são os itens mais propensos a serem apreciados pelo mesmo utilizador. O último parâmetro a escolher neste cenário é a *seleção de itens relacionados.* A opção **From Rated Itens (para avaliação de modelos)** é principalmente para avaliação de modelodurante o processo de formação. Escolhemos **Entre Todos os Itens** para esta fase de previsão. A visualização da saída do Recomendor score [Matchbox][score-matchbox-recommender] parece a Figura 24.

![Resultado da pontuação do sistema de recomendação -itens relacionados](./media/interpret-model-results/24.png)

Figura 24. Visualizar os resultados da pontuação dos itens relacionados com o sistema de recomendação

A primeira das seis colunas representa os iDs de itens necessários para encontrar itens relacionados, conforme fornecidos pelos dados de entrada. As outras cinco colunas armazenam os itens relacionados previstos do item em ordem descendente em termos de relevância. Por exemplo, na primeira fila, o item mais relevante para o ponto 135026 é 135074, seguido por 135035, 132875, 135055 e 134992.

**Publicação do serviço web**

O processo de publicação destas experiências como serviços web para obter previsões é semelhante para cada um dos quatro cenários. Aqui tomamos o segundo cenário (recomendar itens a um dado utilizador) como exemplo. Pode seguir o mesmo procedimento com os outros três.

Salvar o sistema de recomendação treinado como um modelo treinado e filtrar os dados de entrada para uma única coluna de IDENTIFICAção do utilizador, conforme solicitado, pode ligar a experiência como na Figura 25 e publicá-la como um serviço web.

![Experiência de pontuação do problema da recomendação do restaurante](./media/interpret-model-results/25.png)

Figura 25. Experiência de pontuação do problema da recomendação do restaurante

Funcionando o serviço web, o resultado devolvido parece a Figura 26. Os cinco restaurantes recomendados para o utilizador Sub1048 são 134986, 135018, 134975, 135021 e 132862.

![Amostra do serviço de sistema de recomendação](./media/interpret-model-results/25_1.png)

![Resultados da experiência da amostra](./media/interpret-model-results/26.png)

Figura 26. Resultado do serviço web do problema de recomendação do restaurante

<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
