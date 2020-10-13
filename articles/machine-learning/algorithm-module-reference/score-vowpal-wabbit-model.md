---
title: Score Vowpal Wabbit Model
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Score Vowpal Wabbit Model para gerar pontuações para um conjunto de dados de entrada, utilizando um modelo vowpal Wabbit treinado existente.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 23dfee7b78f2606c54525391e1260af69a9b0779
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898406"
---
# <a name="score-vowpal-wabbit-model"></a>Score Vowpal Wabbit Model
Este artigo descreve como usar o módulo **Score Vowpal Wabbit Model** no designer Azure Machine Learning, para gerar pontuações para um conjunto de dados de entrada, utilizando um modelo de Wabbit Vowpal treinado existente.  

Este módulo fornece a versão mais recente da estrutura Vowpal Wabbit, versão 8.8.1. Utilize este módulo para obter dados utilizando um modelo treinado guardado no formato VW versão 8.  

## <a name="how-to-configure-score-vowpal-wabbit-model"></a>Como configurar o modelo de Wabbit Vowpal De Pontuação

1.  Adicione o módulo **do Modelo Wabbit Score Vowpal** à sua experiência.  
  
2.  Adicione um modelo de Wabbit Vowpal treinado e conecte-o à porta de entrada à esquerda. Pode utilizar um modelo treinado criado na mesma experiência ou localizar um modelo guardado na categoria **Datasets** do painel de navegação esquerdo do designer. No entanto, o modelo deve estar disponível no Azure Machine Learning Designer.  
  
    > [!NOTE]
    > Apenas os modelos Vowpal Wabbit 8.8.1 são suportados; não é possível ligar modelos salvos que foram treinados usando outros algoritmos.
  
3.  Adicione o conjunto de dados de teste e conecte-o à porta de entrada da direita. Se o conjunto de dados de teste for um diretório, que contém o ficheiro de dados de teste, especifique o nome do ficheiro de dados de teste com **o nome do ficheiro de dados de teste**. Se o conjunto de dados de teste for um único ficheiro, deixe **o nome do ficheiro de dados de teste** estar vazio.

4. Na caixa de texto de **argumentos VW,** digite um conjunto de argumentos válidos de linha de comando para o Vowpal Wabbit executável.  

    Para obter informações sobre quais os argumentos do Vowpal Wabbit suportados e não suportados na Azure Machine Learning, consulte a secção [Notas Técnicas.](#technical-notes)  

5.  **Nome do ficheiro de dados de teste**: Digite o nome do ficheiro que contém os dados de entrada. Este argumento só é utilizado quando o conjunto de dados de teste é um diretório.

6. **Especificar o tipo de ficheiro**: Indicar qual o formato que os seus dados de treino utilizam. A Vowpal Wabbit suporta estes dois formatos de ficheiro de entrada:  

   - **A VW** representa o formato interno utilizado pela Vowpal Wabbit . Consulte a [página wiki do Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format) para obter mais detalhes. 
   - **SVMLight** é um formato utilizado por outras ferramentas de aprendizagem automática. 

7. Selecione a opção, **Inclua uma coluna extra contendo etiquetas,** se pretender fazer etiquetas de saída juntamente com as pontuações.  

   Normalmente, ao tratar dados de texto, o Vowpal Wabbit não requer etiquetas, e devolverá apenas as pontuações para cada linha de dados.  

8. Selecione a opção, **Inclua uma coluna extra contendo pontuações brutas,** se pretender fazer a produção de pontuações brutas juntamente com os resultados.  

9. Envie o oleoduto.

## <a name="results"></a>Results

Após o treino estar completo:

+ Para visualizar os resultados, clique com o botão direito na saída do módulo [Do Modelo Wabbit Score Vowpal.](score-vowpal-wabbit-model.md) A saída indica uma pontuação de previsão normalizada de 0 a 1. 

+ Para avaliar os resultados, o conjunto de dados de saída deve conter nomes específicos de colunas de pontuação, que satisfaçam os requisitos do módulo do Modelo de Avaliação.

  + Para a tarefa de regressão, o conjunto de dados a avaliar deve ter uma coluna, `Regression Scored Labels` nomeada, que representa rótulos pontuados.
  + Para a tarefa de classificação binária, o conjunto de dados a avaliar deve ter duas colunas, `Binary Class Scored Labels` `Binary Class Scored Probabilities` nomeadas, que representam rótulos pontuados e probabilidades, respectivamente.
  + Para a tarefa de classificação múltipla, o conjunto de dados a avaliar deve ter uma coluna, `Multi Class Scored Labels` nomeada, que representa rótulos pontuados.

  Note que os resultados do módulo do Modelo Wabbit Score Vowpal não podem ser avaliados diretamente. Antes de avaliar, o conjunto de dados deve ser modificado de acordo com os requisitos acima referidos.

##  <a name="technical-notes"></a>Notas técnicas

Esta secção contém detalhes de implementação, dicas e respostas a perguntas frequentes.

### <a name="parameters"></a>Parâmetros

Vowpal Wabbit tem muitas opções de linha de comando para escolher e afinar algoritmos. Não é possível discutir todas estas opções; recomendamos que veja a [página wiki Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).  

Os seguintes parâmetros não são suportados no Azure Machine Learning Studio (clássico).  

-   As opções de entrada/saída especificadas em [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     Estas propriedades já estão configuradas automaticamente pelo módulo.  
  
-   Além disso, qualquer opção que gere várias saídas ou tome várias entradas é proibida. Estes incluem *`--cbt`* *`--lda`* , e *`--wap`* .  
  
-   Apenas os algoritmos de aprendizagem supervisionados são suportados. Isto não permite estas opções: *`–active`* , `--rank` , *`--search`* etc.  

Todos os argumentos que não os acima descritos são permitidos.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 