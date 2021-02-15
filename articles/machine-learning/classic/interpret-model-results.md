---
title: 'Estúdio ML (clássico): Interpretar os resultados do modelo - Azure'
description: Como escolher o parâmetro ideal definido para um algoritmo usando e visualizando saídas de modelos de pontuação.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.date: 11/29/2017
ms.openlocfilehash: f824cf6e30ed795ef0fe4468542ae16c801bd61b
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516129"
---
# <a name="interpret-model-results-in-azure-machine-learning-studio-classic"></a>Interpretar os resultados do modelo no Azure Machine Learning Studio (clássico)

**APLICA-SE A:** ![ Esta é uma marca de verificação, o que significa que este artigo se aplica ao Machine Learning Studio (clássico). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Este é um X, o que significa que este artigo se aplica ao Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Este tópico explica como visualizar e interpretar os resultados da previsão no Azure Machine Learning Studio (clássico). Depois de ter treinado um modelo e feito previsões em cima dele ("marcou o modelo"), é preciso compreender e interpretar o resultado da previsão.

Existem quatro grandes tipos de modelos de machine learning no Azure Machine Learning Studio (clássico):

* Classificação
* Clustering
* Regressão
* Sistemas recomendadores

Os módulos utilizados para a previsão em cima destes modelos são:

* [Módulo de modelo de pontuação][score-model] para classificação e regressão
* Atribuir ao módulo [Clusters][assign-to-clusters] para agrupamento
* [Pontuar Matchbox Recomendador][score-matchbox-recommender] para sistemas de recomendação

Saiba como [escolher parâmetros para otimizar os seus algoritmos no ML Studio (clássico)](algorithm-parameters-optimize.md).

Para aprender a avaliar os seus modelos, consulte [como avaliar o desempenho do modelo.](evaluate-model-performance.md)

Se é novo no ML Studio (clássico), [aprenda a criar uma experiência simples.](create-experiment.md)

## <a name="classification"></a>Classificação
Existem duas subcategorias de problemas de classificação:

* Problemas com apenas duas classes (classificação de duas classes ou binária)
* Problemas com mais de duas classes (classificação multi-classes)

O Azure Machine Learning Studio (clássico) tem diferentes módulos para lidar com cada um destes tipos de classificação, mas os métodos para interpretar os seus resultados de previsão são semelhantes.

### <a name="two-class-classification"></a>Classificação de duas classes
**Experimentação de exemplo**

Um exemplo de um problema de classificação de duas classes é a classificação das flores da íris. A tarefa é classificar as flores da íris com base nas suas características. O conjunto de dados da Íris fornecido no Azure Machine Learning Studio (clássico) é um subconjunto do popular conjunto de [dados da Íris](https://en.wikipedia.org/wiki/Iris_flower_data_set) que contém instâncias de apenas duas espécies de flores (classes 0 e 1). Existem quatro características para cada flor (comprimento sepal, largura de sepal, comprimento de pétalas e largura de pétala).

![Screenshot da experiência da íris](./media/interpret-model-results/1.png)

Figura 1: Iris experiência de problema de classificação de duas classes

Foi realizada uma experiência para resolver este problema, como mostra a Figura 1. Um modelo de árvore de decisão reforçada de duas classes foi treinado e pontuado. Agora pode visualizar os resultados da previsão a partir do módulo ['Modelo de Pontuação',][score-model] clicando na porta de saída do módulo ['Modelo de Pontuação'][score-model] e clicando em **Visualizar**.

![Módulo de modelo de pontuação](./media/interpret-model-results/1_1.png)

Isto eleva os resultados da pontuação como mostrado na Figura 2.

![Resultados da experiência de classificação de duas classes da íris](./media/interpret-model-results/2.png)

Figura 2. Visualizar um modelo de pontuação resulta numa classificação de duas classes

**Interpretação do resultado**

Há seis colunas na tabela de resultados. As quatro colunas esquerdas são as quatro características. As duas colunas certas, etiquetas pontuadas e probabilidades pontuadas, são os resultados da previsão. A coluna De Probabilidades Pontuadas mostra a probabilidade de uma flor pertencer à classe positiva (Classe 1). Por exemplo, o primeiro número da coluna (0.028571) significa que há 0,028571 probabilidade de que a primeira flor pertença à classe 1. A coluna Marcas rótulos mostra a classe prevista para cada flor. Isto baseia-se na coluna De Probabilidades Pontuadas. Se a probabilidade pontuada de uma flor for maior que 0,5, prevê-se como classe 1. Caso contrário, prevê-se como Classe 0.

**Publicação de serviço web**

Após os resultados da previsão terem sido entendidos e avaliados em som, a experiência pode ser publicada como um serviço web para que você possa implantá-lo em várias aplicações e chamá-lo para obter previsões de classe em qualquer nova flor da íris. Para aprender a transformar uma experiência de formação numa experiência de pontuação e publicá-la como um serviço web, consulte [Tutorial 3: Implementar modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md). Este procedimento proporciona-lhe uma experiência de pontuação como mostrado na Figura 3.

![Screenshot da experiência de pontuação](./media/interpret-model-results/3.png)

Figura 3. Pontuação da experiência de problema de classificação de duas classes da íris

Agora precisa definir a entrada e saída para o serviço web. A entrada é a porta de entrada certa do [Score Model,][score-model]que é a entrada da flor de Íris. A escolha da saída depende se está interessado na classe prevista (etiqueta pontuada), na probabilidade pontuada, ou em ambos. Neste exemplo, presume-se que está interessado em ambos. Para selecionar as colunas de saída desejadas, utilize um [módulo de conjunto de colunas select em dados.][select-columns] Clique [em Selecionar Colunas no conjunto de dados,][select-columns]clique no **seletor de colunas de lançamento** e selecione **Etiquetas pontuadas** e **probabilidades pontuadas**. Depois de definir a porta de saída das [Colunas Selecionadas no conjunto][select-columns] de dados e executá-la novamente, deve estar pronto para publicar a experiência de pontuação como um serviço web clicando **em PUBLISH WEB SERVICE**. A experiência final parece a Figura 4.

![A experiência de classificação de duas classes da íris](./media/interpret-model-results/4.png)

Figura 4. Experiência de pontuação final de um problema de classificação de duas classes da íris

Depois de executar o serviço web e introduzir alguns valores de recurso de uma instância de teste, o resultado devolve dois números. O primeiro número é o rótulo marcado, e o segundo é a probabilidade pontuada. Esta flor é prevista como classe 1 com probabilidade de 0,9655.

![Modelo de pontuação de interpretação de teste](./media/interpret-model-results/4_1.png)

![Resultados dos testes de pontuação](./media/interpret-model-results/5.png)

Figura 5. Resultado do serviço web da classificação de duas classes da íris

### <a name="multi-class-classification"></a>Classificação multi-classes
**Experimentação de exemplo**

Nesta experiência, executa uma tarefa de reconhecimento de letras como um exemplo de classificação multiclasse. O classificador tenta prever uma determinada letra %28class%29 com base em alguns valores de atributos escritos à mão extraídos das imagens escritas à mão.

![Exemplo de reconhecimento de cartas](./media/interpret-model-results/5_1.png)

Nos dados de treino, existem 16 funcionalidades extraídas de imagens de cartas escritas à mão. As 26 letras formam as nossas 26 aulas. A figura 6 mostra uma experiência que irá formar um modelo de classificação multiclasse para reconhecimento de letras e prever no mesmo conjunto de recursos num conjunto de dados de teste.

![Experiência de classificação multiclasse de reconhecimento de carta](./media/interpret-model-results/6.png)

Figura 6. Experiência de problema de classificação multiclasse de reconhecimento de carta

Visualizando os resultados do módulo ['Modelo de Pontuação',][score-model] clicando na porta de saída do módulo ['Modelo de Pontuação'][score-model] e clicando em **Visualizar,** deverá ver o conteúdo como mostrado na Figura 7.

![Resultados do modelo de pontuação](./media/interpret-model-results/7.png)

Figura 7. Visualizar o modelo de pontuação resulta numa classificação multi-classe

**Interpretação do resultado**

As 16 colunas esquerdas representam os valores de característica do conjunto de testes. As colunas com nomes como Probabilidades Pontuadas para a Classe "XX" são como a coluna de Probabilidades Pontuadas no caso de duas classes. Mostram a probabilidade de a entrada correspondente se enquadrar numa determinada classe. Por exemplo, para a primeira entrada, há 0,003571 probabilidade de que seja uma probabilidade "A", 0.000451 de que seja um "B", e assim por diante. A última coluna (Marcas etiquetas) é a mesma que as etiquetas pontuadas no caso de duas classes. Seleciona a classe com a maior probabilidade pontuada como a classe prevista da entrada correspondente. Por exemplo, para a primeira entrada, a etiqueta pontuada é "F", uma vez que tem a maior probabilidade de ser um "F" (0,916995).

**Publicação de serviço web**

Também pode obter a etiqueta pontuada para cada entrada e a probabilidade da etiqueta pontuada. A lógica básica é encontrar a maior probabilidade entre todas as probabilidades pontuadas. Para isso, tem de utilizar o módulo [executo R Script.][execute-r-script] O código R é mostrado na Figura 8 e o resultado da experiência é mostrado na Figura 9.

![Exemplo de código R](./media/interpret-model-results/8.png)

Figura 8. Código R para extrair etiquetas pontuais e as probabilidades associadas dos rótulos

![Resultado da experiência](./media/interpret-model-results/9.png)

Figura 9. Experiência de pontuação final do problema de classificação multiclasse de reconhecimento de letras

Depois de publicar e executar o serviço web e introduzir alguns valores de funções de entrada, o resultado devolvido parece a Figura 10. Esta carta escrita à mão, com as suas 16 características extraídas, prevê-se que seja um "T" com probabilidade de 0,9715.

![Módulo de pontuação de interpretação de teste](./media/interpret-model-results/9_1.png)

![Resultado do teste](./media/interpret-model-results/10.png)

Figura 10. Resultado do serviço web da classificação multiclasse

## <a name="regression"></a>Regressão
Os problemas de regressão são diferentes dos problemas de classificação. Num problema de classificação, estás a tentar prever classes discretas, como a classe a que pertence uma flor de íris. Mas como pode ver no exemplo seguinte de um problema de regressão, está a tentar prever uma variável contínua, como o preço de um carro.

**Experimentação de exemplo**

Use a previsão do preço do automóvel como exemplo para regressão. Está a tentar prever o preço de um carro com base nas suas características, incluindo a gata, tipo de combustível, tipo de carroça e roda de tração. A experiência é mostrada na Figura 11.

![Experiência de regressão do preço do automóvel](./media/interpret-model-results/11.png)

Figura 11. Experiência de problema de regressão de preços do automóvel

Visualizando o módulo [do Modelo de Pontuação,][score-model] o resultado parece a Figura 12.

![Resultados da pontuação para o problema da previsão dos preços do automóvel](./media/interpret-model-results/12.png)

Figura 12. Resultado da pontuação para o problema da previsão dos preços do automóvel

**Interpretação do resultado**

Etiquetas pontuadas é a coluna de resultados neste resultado de pontuação. Os números são o preço previsto para cada carro.

**Publicação de serviço web**

Você pode publicar a experiência de regressão em um serviço web e chamá-lo para previsão de preço do automóvel da mesma forma que no caso de utilização de classificação de duas classes.

![Experiência de pontuação para problema de regressão dos preços do automóvel](./media/interpret-model-results/13.png)

Figura 13. Experiência de pontuação de um problema de regressão dos preços do automóvel

Executando o serviço web, o resultado devolvido parece a Figura 14. O preço previsto para este carro é $15.085,52.

![Teste de interpretação do módulo de pontuação](./media/interpret-model-results/13_1.png)

![Resultados do módulo de pontuação](./media/interpret-model-results/14.png)

Figura 14. Serviço web resultado de um problema de regressão dos preços do automóvel

## <a name="clustering"></a>Clustering
**Experimentação de exemplo**

Vamos usar o conjunto de dados da Íris novamente para construir uma experiência de agrupamento. Aqui pode filtrar as etiquetas de classe no conjunto de dados para que tenha apenas características e possa ser usada para o agrupamento. Neste caso de uso da íris, especifique o número de aglomerados a serem dois durante o processo de treino, o que significa que você iria agrupar as flores em duas classes. A experiência é mostrada na Figura 15.

![Experiência de problema de agrupamento de íris](./media/interpret-model-results/15.png)

Figura 15. Experiência de problema de agrupamento de íris

O agrupamento difere da classificação, na medida em que o conjunto de dados de treino não tem rótulos de verdade por si só. Agrupamentos de agrupamentos os dados de formação definem casos em aglomerados distintos. Durante o processo de treino, o modelo rotula as entradas aprendendo as diferenças entre as suas características. Depois disso, o modelo treinado pode ser usado para classificar ainda mais as entradas futuras. Há duas partes do resultado que nos interessam num problema de agrupamento. A primeira parte é a rotulagem do conjunto de dados de formação, e a segunda é classificar um novo conjunto de dados com o modelo treinado.

A primeira parte do resultado pode ser visualizada clicando na porta de saída esquerda do Modelo de Clustering de [Comboios][train-clustering-model] e, em seguida, clicando em **Visualizar**. A visualização é mostrada na Figura 16.

![Resultado do agrupamento](./media/interpret-model-results/16.png)

Figura 16. Visualizar o resultado do agrupamento para o conjunto de dados de formação

O resultado da segunda parte, agrupando novas entradas com o modelo de agrupamento treinado, é mostrado na Figura 17.

![Visualizar o resultado do agrupamento](./media/interpret-model-results/17.png)

Figura 17. Visualizar o resultado do agrupamento num novo conjunto de dados

**Interpretação do resultado**

Embora os resultados das duas partes procorrem de diferentes fases de experiência, elas são iguais e são interpretadas da mesma forma. As primeiras quatro colunas são características. A última coluna, Atribuições, é o resultado da previsão. Prevê-se que as entradas atribuídas ao mesmo número estejam no mesmo cluster, ou seja, partilham semelhanças de alguma forma (esta experiência utiliza a métrica de distância euclidídea padrão). Como especificou o número de clusters a ser 2, as entradas em Atribuições são rotuladas 0 ou 1.

**Publicação de serviço web**

Você pode publicar a experiência de clustering em um serviço web e chamá-lo para previsões de agrupamento da mesma forma que no caso de utilização de classificação de duas classes.

![Experiência de pontuação para problema de agrupamento de íris](./media/interpret-model-results/18.png)

Figura 18. Experiência de pontuação de um problema de agrupamento de íris

Depois de executar o serviço web, o resultado devolvido parece a Figura 19. Prevê-se que esta flor esteja no aglomerado 0.

![Teste interpretar módulo de pontuação](./media/interpret-model-results/18_1.png)

![Resultado do módulo de pontuação](./media/interpret-model-results/19.png)

Figura 19. Resultado do serviço web da classificação de duas classes da íris

## <a name="recommender-system"></a>Sistema de recomendadores
**Experimentação de exemplo**

Para sistemas de recomendação, pode usar o problema da recomendação do restaurante como exemplo: pode recomendar restaurantes para clientes com base no seu histórico de classificação. Os dados de entrada consistem em três partes:

* Classificações de restaurantes dos clientes
* Dados de funcionalidades do cliente
* Dados de recursos de restaurante

Há várias coisas que podemos fazer com o módulo [de recomendador train Matchbox][train-matchbox-recommender] no Azure Machine Learning Studio (clássico):

* Prever classificações para um determinado utilizador e item
* Recomendar itens a um determinado utilizador
* Localizar utilizadores relacionados com um determinado utilizador
* Encontre itens relacionados com um determinado item

Pode escolher o que pretende fazer selecionando a partir das quatro opções do menu **de previsão Recomendador.** Aqui podes andar pelos quatro cenários.

![Recomendador matchbox](./media/interpret-model-results/19_1.png)

Uma experiência típica do Azure Machine Learning Studio (clássico) para um sistema de recomendadores parece a Figura 20. Para obter informações sobre como utilizar os módulos do sistema recomendadores, consulte [o recomendador train matchbox][train-matchbox-recommender] e [o recomendador score matchbox][score-matchbox-recommender].

![Experiência do sistema recomendador](./media/interpret-model-results/20.png)

Figura 20. Experiência do sistema recomendador

**Interpretação do resultado**

**Prever classificações para um determinado utilizador e item**

Ao selecionar a **Previsão de Classificação** sob o **tipo de previsão De recomendo,** está a pedir ao sistema de recomendadores que preveja a classificação para um determinado utilizador e item. A visualização da saída do [Recomendador score Matchbox][score-matchbox-recommender] parece a Figura 21.

![Resultado da pontuação do sistema de recomendação -- previsão de classificação](./media/interpret-model-results/21.png)

Figura 21. Visualizar o resultado da pontuação do sistema de recomendação-- previsão de classificação

As duas primeiras colunas são os pares de artigos de utilizador fornecidos pelos dados de entrada. A terceira coluna é a classificação prevista de um utilizador para um determinado item. Por exemplo, na primeira fila, prevê-se que o cliente U1048 avalie o restaurante 135026 como 2.

**Recomendar itens a um determinado utilizador**

Ao selecionar a **Recomendação de Artigos** sob o **tipo de previsão De recomendo,** está a pedir ao sistema de recomendação que recomende itens a um determinado utilizador. O último parâmetro a escolher neste cenário é *a seleção recomendada de artigos.* A opção **From Rated Items (para avaliação de modelos)** destina-se principalmente à avaliação do modelo durante o processo de treino. Para esta fase de previsão, **escolhemos De Todos os Itens.** A visualização da saída do [Recomendador score Matchbox][score-matchbox-recommender] parece a Figura 22.

![Resultado da pontuação do sistema de recomendação -- recomendação de artigo](./media/interpret-model-results/22.png)

Figura 22. Visualizar o resultado da pontuação do sistema de recomendação-- recomendação de artigo

A primeira das seis colunas representa os IDs do utilizador para recomendar itens, conforme fornecidos pelos dados de entrada. As outras cinco colunas representam os itens recomendados ao utilizador por ordem descendente de relevância. Por exemplo, na primeira fila, o restaurante mais recomendado para o cliente U1048 é 134986, seguido por 135018, 134975, 135021 e 132862.

**Localizar utilizadores relacionados com um determinado utilizador**

Ao selecionar **Utilizadores Relacionados** sob o **tipo de previsão Recomendador,** está a pedir ao sistema de recomendadores que encontre utilizadores relacionados a um determinado utilizador. Os utilizadores relacionados são os utilizadores que têm preferências semelhantes. O último parâmetro a escolher neste cenário é *a seleção do utilizador relacionado.* A opção **dos utilizadores que avaliaram os itens (para avaliação de modelos)** destina-se principalmente à avaliação do modelo durante o processo de treino. Escolha **entre todos os utilizadores** para esta fase de previsão. A visualização da saída do [Recomendador score Matchbox][score-matchbox-recommender] parece a Figura 23.

![Resultado da pontuação dos utilizadores do sistema de recomendadores --utilizadores relacionados](./media/interpret-model-results/23.png)

Figura 23. Visualizar os resultados da pontuação dos utilizadores do sistema de recomendação-relacionados com o sistema

A primeira das seis colunas mostra os IDs de utilizador necessários para encontrar utilizadores relacionados, conforme fornecidos pelos dados de entrada. As outras cinco colunas armazenam os utilizadores relacionados previstos do utilizador em ordem descendente de relevância. Por exemplo, na primeira linha, o cliente mais relevante para o cliente U1048 é o U1051, seguido por U1066, U1044, U1017 e U1072.

**Encontre itens relacionados com um determinado item**

Ao selecionar **Itens Relacionados** sob **o tipo de previsão De Recomendor,** está a pedir ao sistema de recomendadores que encontre itens relacionados a um determinado item. Os itens relacionados são os itens mais propensos a serem apreciados pelo mesmo utilizador. O último parâmetro a escolher neste cenário é *a seleção de item relacionado.* A opção **From Rated Items (para avaliação de modelos)** destina-se principalmente à avaliação do modelo durante o processo de treino. Escolhemos **De Todos os Itens** para esta fase de previsão. A visualização da saída do [Recomendador score Matchbox][score-matchbox-recommender] parece a Figura 24.

![Resultado da pontuação de itens relacionados com o sistema de recomendadores](./media/interpret-model-results/24.png)

Figura 24. Visualizar os resultados da pontuação dos itens relacionados com o sistema de recomendadores

A primeira das seis colunas representa os IDs de item necessários para encontrar itens relacionados, conforme fornecidos pelos dados de entrada. As outras cinco colunas armazenam os itens relacionados previstos do artigo em ordem descendente em termos de relevância. Por exemplo, na primeira linha, o item mais relevante para o item 135026 é 135074, seguido de 135035, 132875, 135055 e 134992.

**Publicação de serviço web**

O processo de publicação destas experiências como serviços web para obter previsões é semelhante para cada um dos quatro cenários. Aqui levamos o segundo cenário (recomendar itens a um determinado utilizador) como um exemplo. Pode seguir o mesmo procedimento com os outros três.

Guardar o sistema de recomendadores treinado como um modelo treinado e filtrar os dados de entrada para uma única coluna de ID do utilizador, conforme solicitado, pode ligar a experiência como na Figura 25 e publicá-la como um serviço web.

![Experiência de pontuação do problema da recomendação do restaurante](./media/interpret-model-results/25.png)

Figura 25. Experiência de pontuação do problema da recomendação do restaurante

Executando o serviço web, o resultado devolvido parece a Figura 26. Os cinco restaurantes recomendados para o utilizador U1048 são 134986, 135018, 134975, 135021 e 132862.

![Amostra do serviço de sistema de recomendadores](./media/interpret-model-results/25_1.png)

![Resultados da experiência da amostra](./media/interpret-model-results/26.png)

Figura 26. Serviço web resultado do problema de recomendação de restaurante

<!-- Module References -->
[assign-to-clusters]: /azure/machine-learning/studio-module-reference/assign-data-to-clusters
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[score-matchbox-recommender]: /azure/machine-learning/studio-module-reference/score-matchbox-recommender
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[train-clustering-model]: /azure/machine-learning/studio-module-reference/train-clustering-model
[train-matchbox-recommender]: /azure/machine-learning/studio-module-reference/train-matchbox-recommender