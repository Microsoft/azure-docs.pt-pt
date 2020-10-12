---
title: Utilize o módulo de recomendação & profundo da pontuação
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Score Wide & Deep Recommender em Azure Machine Learning para marcar previsões de recomendação para um conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/12/2020
ms.openlocfilehash: 9a1a3892e6a47aabd9b5129ca551900494616bc8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905167"
---
# <a name="score-wide-and-deep-recommender"></a>Score Wide and Deep Recommender

Este artigo descreve como usar o módulo **Score Wide e Deep Recommender** no designer Azure Machine Learning, para criar previsões baseadas num modelo de recomendação treinado, baseado no Wide & Deep learning da Google.

O recomendador Wide and Deep pode gerar dois tipos diferentes de previsões:

- [Prever classificações para um determinado utilizador e item](#predict-ratings)

- [Recomendar itens a um determinado utilizador](#recommend-items)


Ao criar este último tipo de previsões, pode operar no *modo de produção* ou no *modo de avaliação*.

- **O modo de produção** considera todos os utilizadores ou itens, e é normalmente utilizado num serviço web. Pode criar pontuações para novos utilizadores, e não apenas para os utilizadores vistos durante o treino. 

- **O modo de avaliação** funciona num conjunto reduzido de utilizadores ou itens que podem ser avaliados, e é normalmente utilizado durante a experimentação.

Mais detalhes sobre o recomendador Wide and Deep e a sua teoria subjacente podem ser encontrados no artigo de investigação relevante:  [Wide & Deep Learning for Recommender Systems](https://arxiv.org/pdf/1606.07792.pdf).  

## <a name="how-to-configure-score-wide-and-deep-recommender"></a>Como configurar pontuação ampla e profunda recomendadora

Este módulo suporta diferentes tipos de recomendações, cada uma com diferentes requisitos. Clique no link para o tipo de dados que tem e o tipo de recomendação que pretende criar.

+ [Prever classificações](#predict-ratings)
+ [Recomendar itens](#recommend-items)

### <a name="predict-ratings"></a>Prever classificações

Quando prevê classificações, o modelo calcula como um determinado utilizador reagirá a um determinado item, dados os dados de formação. Por isso, os dados de entrada para a pontuação devem fornecer tanto um utilizador como o item para classificar.

1. Adicione um modelo de recomendação & Profundo treinado à sua experiência e conecte-o ao **modelo de recomendação treinado wide and deep**.  Tem de criar o modelo utilizando [o Train Wide and Deep Recommender](train-wide-and-deep-recommender.md).

2. **Tipo de previsão do recomendador**: Selecione **Previsão de Classificação**. Não são necessários mais parâmetros.

3. Adicione os dados para os quais deseja fazer previsões e conecte-os ao **Dataset para obter**.

    Para prever as classificações, o conjunto de dados de entrada deve conter pares de artigos de utilizador.

    O conjunto de dados pode conter uma terceira coluna opcional de classificações para o par de artigos de utilizador nas primeira e segunda colunas, mas a terceira coluna será ignorada durante a previsão.

4.  (Opcional). Se tiver um conjunto de dados de funcionalidades do utilizador, conecte-o às **funcionalidades do Utilizador**.

    O conjunto de dados das funcionalidades do utilizador deve conter o identificador do utilizador na primeira coluna. As colunas restantes devem conter valores que caracterizam os utilizadores, tais como o seu sexo, preferências, localização, etc.
  
    As funcionalidades dos utilizadores que avaliaram os itens no conjunto de dados de treino são ignoradas pela **Score Wide e deep Recommender**, porque já foram aprendidas durante o treino. Por isso, filtre previamente o seu conjunto de dados para incluir apenas *utilizadores de arranque a frio*, ou utilizadores que não tenham avaliado quaisquer itens.

    > [!WARNING]
    > Se o modelo foi treinado sem utilizar as funcionalidades do utilizador, não é possível introduzir as funcionalidades do utilizador durante a pontuação.

5. Se tiver um conjunto de funcionalidades de item, pode conectá-lo às **funcionalidades do Item.**

    O conjunto de dados de características do item deve conter um identificador de produto na primeira coluna. As colunas restantes devem conter valores que caracterizam os itens.

    As características dos itens classificados no conjunto de dados de treino são ignoradas pela **Score Wide e pela Deep Recommender,** uma vez que já foram aprendidas durante o treino. Por isso, restrinja o conjunto de dados de pontuação a *itens de arranque a frio,* ou itens que não tenham sido avaliados por nenhum utilizadores.

    > [!WARNING]
    > Se o modelo foi treinado sem utilizar funcionalidades de item, não é possível introduzir funcionalidades de itens durante a pontuação.

7. Execute a experimentação.

### <a name="results-for-rating-predictions"></a>Resultados das previsões de rating 

O conjunto de dados de saída contém três colunas, contendo o utilizador, o item e a classificação prevista para cada utilizador e item de entrada.

Além disso, aplicam-se as seguintes alterações durante a pontuação:

-  Para uma coluna de recurso de utilizador ou item numérico, os valores em falta são automaticamente substituídos pela **média** dos valores de conjunto de treino não desaparecidos. Para a função categórica, os valores em falta são substituídos pelo mesmo valor categórico que não sejam valores possíveis desta funcionalidade.
-  Não é aplicada nenhuma tradução aos valores de recurso, para manter a sua sparsity.

### <a name="recommend-items"></a>Recomendar itens

Para recomendar itens para os utilizadores, fornece uma lista de utilizadores e itens como entrada. A partir destes dados, o modelo utiliza o seu conhecimento sobre itens e utilizadores existentes para gerar uma lista de itens com provável apelo a cada utilizador. Pode personalizar o número de recomendações devolvidas e definir um limiar para o número de recomendações anteriores que são necessárias para gerar uma recomendação.

1. Adicione um modelo de recomendação treinado Wide and Deep à sua experiência e conecte-o ao **modelo de recomendação treinado wide and deep.**  Tem de criar o modelo utilizando [o Train Wide and Deep Recommender](train-wide-and-deep-recommender.md).

2. Para recomendar itens para uma determinada lista de utilizadores, desa um tipo de **previsão de recomendação** de recomendação para **a Recomendação do Item**.

3. **Seleção recomendada do artigo**: Indique se está a utilizar o módulo de pontuação em produção ou para avaliação do modelo, escolhendo um destes valores:

    - **A partir de Itens Classificados (para avaliação de modelos)**: Selecione esta opção se estiver a desenvolver ou testar um modelo. Esta opção permite **o modo de avaliação**, e o módulo faz recomendações apenas a partir dos itens do conjunto de dados de entrada que foram avaliados.
    - **De todos os itens**: Selecione esta opção se estiver a configurar uma experiência para utilizar num serviço web ou produção.  Esta opção permite **o modo de produção,** e o módulo faz recomendações de todos os itens vistos durante o treino.
    - **A partir de Itens Não Classificados (para sugerir novos itens aos utilizadores)**: Selecione esta opção se quiser que o módulo faça recomendações apenas a partir dos itens do conjunto de dados de formação que não foram avaliados. 
4. Adicione o conjunto de dados para o qual pretende fazer previsões e conecte-o ao **Dataset para obter**.

    - Se escolher a opção, **a partir de todos os itens,** o conjunto de dados de entrada deverá ser composto por uma e apenas uma coluna, contendo os identificadores dos utilizadores para os quais devem fazer recomendações.

        O conjunto de dados pode incluir duas colunas extra de identificadores de item e classificações, mas estas duas colunas são ignoradas. 

    - Se escolher a opção, **a partir de itens classificados (para avaliação do modelo)**, o conjunto de dados de entrada deverá ser composto por **pares de artigos de utilizador**. A primeira coluna deve conter o identificador do **utilizador.** A segunda coluna deve conter os identificadores **de item** correspondentes.

        O conjunto de dados pode incluir uma terceira coluna de classificações de item de utilizador, mas esta coluna é ignorada.
        
    - Para **itens não classificados (para sugerir novos itens aos utilizadores)**, o conjunto de dados de entrada deve ser composto por pares de artigos de utilizador. A primeira coluna deve conter o identificador do utilizador. A segunda coluna deve conter os identificadores de item correspondentes.

        O conjunto de dados pode incluir uma terceira coluna de classificações de item de utilizador, mas esta coluna é ignorada.

5. (Opcional). Se tiver um conjunto de **dados**de funcionalidades do utilizador, conecte-o às **funcionalidades do Utilizador**.

    A primeira coluna do conjunto de dados das funcionalidades do utilizador deve conter o identificador do utilizador. As colunas restantes devem conter valores que caracterizam o utilizador, tais como o seu sexo, preferências, localização, etc.

    As funcionalidades dos utilizadores que tenham classificado itens são ignoradas pela **Score Wide e deep Recommender**, uma vez que estas funcionalidades já foram aprendidas durante o treino. Portanto, pode filtrar o seu conjunto de dados com antecedência para incluir apenas *utilizadores de arranque a frio*, ou utilizadores que não tenham avaliado quaisquer itens.

    > [!WARNING]
    >  Se o modelo foi treinado sem utilizar as funcionalidades do utilizador, não é possível utilizar funcionalidades de aplicação durante a pontuação.

6. (Opcional) Se tiver um conjunto de funcionalidades de **item,** pode conectá-lo às **funcionalidades do Item.**

    A primeira coluna do conjunto de dados do item deve conter o identificador de produto. As colunas restantes devem conter valores que caracterizam os itens.

    As características dos itens classificados são ignoradas pela **Score Wide e deep Recommender,** porque estas funcionalidades já foram aprendidas durante o treino. Portanto, pode restringir o seu conjunto de dados de pontuação a *itens de arranque a frio*, ou itens que não tenham sido avaliados por nenhum utilizadores.

    > [!WARNING]
    >  Se o modelo foi treinado sem utilizar as funcionalidades do item, não utilize as funcionalidades do item ao marcar.  

7. **Número máximo de itens a recomendar a um utilizador**: Digite o número de itens a devolver para cada utilizador. Por predefinição, são recomendados 5 itens.

8. **Tamanho mínimo do conjunto de recomendações por utilizador**: Digite um valor que indique quantas recomendações prévias são necessárias.  Por predefinição, este parâmetro é definido para 2, o que significa que o item deve ter sido recomendado por pelo menos dois outros utilizadores.

    Esta opção só deve ser utilizada se estiver a pontuar no modo de avaliação. A opção não está disponível se selecionar **de todos os itens** ou **de itens não classificados (para sugerir novos itens aos utilizadores)**.

9. Para **itens não avaliados (para sugerir novos itens aos utilizadores)**, utilize a terceira porta de entrada, denominada **Dados de Formação,** para remover itens que já tenham sido classificados a partir dos resultados da previsão.

    Para aplicar este filtro, ligue o conjunto de dados de treino original à porta de entrada.

10. Execute a experimentação.
### <a name="results-of-item-recommendation"></a>Resultados da recomendação do artigo

O conjunto de dados pontuado devolvido pela **Score Wide e Deep Recommender** lista os itens recomendados para cada utilizador.

- A primeira coluna contém os identificadores do utilizador.
- São geradas várias colunas adicionais, dependendo do valor definido para o **número máximo de itens a recomendar a um utilizador**. Cada coluna contém um item recomendado (por identificador). As recomendações são ordenadas pela afinidade do artigo do utilizador, com o item com maior afinidade colocado na coluna, **ponto 1**.

##  <a name="technical-notes"></a>Notas técnicas

Esta secção contém respostas a algumas perguntas comuns sobre a utilização do recomendador Wide & Deep para criar previsões.  

###  <a name="cold-start-users-and-recommendations"></a>Utilizadores e recomendações de arranque a frio

Normalmente, para criar recomendações, o módulo **Score Wide e Deep Recommender** requer as mesmas entradas que usou durante o treino do modelo, incluindo um ID do utilizador. Isto porque o algoritmo precisa de saber se aprendeu algo sobre este utilizador durante o treino. 

No entanto, para os novos utilizadores, pode não ter um ID do utilizador, apenas algumas funcionalidades do utilizador, como idade, sexo, etc.

Ainda pode criar recomendações para utilizadores novos no seu sistema, manuseando-os como *utilizadores de arranque a frio.* Para estes utilizadores, o algoritmo de recomendação não utiliza o histórico passado ou as classificações anteriores, apenas as funcionalidades do utilizador.

Para efeitos de previsão, um utilizador de arranque a frio é definido como um utilizador com um ID que não foi utilizado para o treino. Para garantir que os IDs não correspondem aos IDs utilizados no treino, pode criar novos identificadores. Por exemplo, pode gerar IDs aleatórios dentro de um intervalo especificado, ou alocar uma série de IDs com antecedência para utilizadores de arranque a frio.

No entanto, se não tiver quaisquer dados de filtragem colaborativos, como um vetor de funcionalidades do utilizador, é melhor utilizar um aprendiz de classificação ou regressão.

###  <a name="production-use-of-the-wide-and-deep-recommender"></a>Utilização da produção do recomendador Wide and Deep

Se tiver experimentado com o recomendador Wide and Deep e, em seguida, mover o modelo para a produção, esteja ciente destas diferenças fundamentais ao utilizar o recomendador no modo de avaliação e no modo de produção:

- A avaliação, por definição, requer previsões que podem ser verificadas contra a *verdade no terreno* num conjunto de testes. Portanto, quando avaliar o recomendador, deve prever apenas itens que tenham sido classificados no conjunto de teste. Isto restringe necessariamente os valores possíveis que estão previstos.

    No entanto, quando operacionaliza o modelo, normalmente muda-se o modo de previsão para fazer recomendações com base em todos os itens possíveis, de forma a obter as melhores previsões. Para muitas destas previsões, não existe uma verdade básica correspondente, pelo que a exatidão da recomendação não pode ser verificada da mesma forma que durante a experimentação.

- Se não fornecer um ID de utilizador em produção, e fornecer apenas um vetor de funcionalidade, poderá obter como resposta uma lista de todas as recomendações para todos os utilizadores possíveis. Certifique-se de fornecer uma identificação do utilizador.

    Para limitar o número de recomendações que são devolvidas, também pode especificar o número máximo de itens devolvidos por utilizador. 



## <a name="next-steps"></a>Passos seguintes

Veja o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 
