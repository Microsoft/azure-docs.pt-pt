---
title: 'Normalizar dados: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de dados normalizar em Azure Machine Learning para transformar um conjunto de dados através da *normalização*..
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: de0a23ca9dea210d91fe259b06622226549ba3b2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90890453"
---
# <a name="normalize-data-module"></a>Normalizar módulo de dados

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para transformar um conjunto de dados através da *normalização*.

A normalização é uma técnica frequentemente aplicada como parte da preparação de dados para a aprendizagem automática. O objetivo da normalização é alterar os valores das colunas numéricas no conjunto de dados para utilizar uma escala comum, sem distorcer diferenças nas gamas de valores ou perder informação. A normalização também é necessária para que alguns algoritmos modelem os dados corretamente.

Por exemplo, assuma que o seu conjunto de dados de entrada contém uma coluna com valores que variam entre 0 e 1, e outra coluna com valores que variam entre 10.000 e 100.000. A grande diferença na *escala* dos números pode causar problemas quando se tenta combinar os valores como características durante a modelação.

*A normalização* evita estes problemas criando novos valores que mantêm a distribuição geral e os rácios nos dados de origem, mantendo os valores dentro de uma escala aplicada em todas as colunas numéricas utilizadas no modelo.

Este módulo oferece várias opções para a transformação de dados numéricos:

- Pode alterar todos os valores para uma escala de 0-1, ou transformar os valores representando-os como classificações percentil em vez de valores absolutos.
- Pode aplicar a normalização numa única coluna ou em várias colunas no mesmo conjunto de dados.
- Se precisar de repetir o gasoduto, ou aplicar os mesmos passos de normalização noutros dados, pode guardar os passos como uma transformação de normalização e aplicá-lo a outros conjuntos de dados que tenham o mesmo esquema.

> [!WARNING]
> Alguns algoritmos exigem que os dados sejam normalizados antes de treinar um modelo. Outros algoritmos executam o seu próprio dimensionamento de dados ou normalização. Portanto, quando escolher um algoritmo de aprendizagem automática para usar na construção de um modelo preditivo, certifique-se de rever os requisitos de dados do algoritmo antes de aplicar a normalização aos dados de treino.

##  <a name="configure-normalize-data"></a>Configurar dados de normalização

Pode aplicar apenas um método de normalização de cada vez utilizando este módulo. Portanto, o mesmo método de normalização é aplicado a todas as colunas que seleciona. Para utilizar diferentes métodos de normalização, utilize uma segunda instância de **Normalize Data**.

1. Adicione o módulo **de dados normalizar** ao seu oleoduto. Pode encontrar o módulo In Azure Machine Learning, em **Transformação de Dados,** na categoria **Escala e Redução.**

2. Ligue um conjunto de dados que contém pelo menos uma coluna de todos os números.

3. Utilize o Seletor de Colunas para selecionar as colunas numéricas para normalizar. Se não escolher colunas individuais, por predefinição **estão** incluídas todas as colunas de tipo numérico na entrada e o mesmo processo de normalização é aplicado a todas as colunas selecionadas. 

    Isto pode levar a resultados estranhos se incluir colunas numéricas que não devem ser normalizadas! Verifique sempre cuidadosamente as colunas.

    Se não forem detetadas colunas numéricas, verifique os metadados da coluna para verificar se o tipo de dados da coluna é um tipo numérico suportado.

    > [!TIP]
    > Para garantir que as colunas de um tipo específico são fornecidas como entrada, tente utilizar as [Colunas Selecionadas no](./select-columns-in-dataset.md) módulo Dataset antes **de normalizar os dados.**

4. **Utilizar 0 para colunas constantes quando verificadas**: Selecione esta opção quando qualquer coluna numérica contiver um único valor imutável. Isto garante que tais colunas não são utilizadas em operações de normalização.

5. A partir da lista de abandono do **método de transformação,** escolha uma função matemática única para aplicar a todas as colunas selecionadas. 
  
    - **Zscore**: Converte todos os valores para um z-score.
    
      Os valores da coluna são transformados com a seguinte fórmula:  
  
      ![normalização usando z&#45;pontuações](media/module/aml-normalization-z-score.png)
  
      O desvio médio e padrão é calculado separadamente para cada coluna. É usado um desvio padrão da população.
  
    - **MinMax**: O normalizador min-max redimensiona linearmente todas as funções para o intervalo [0,1].
    
      A rescalificação para o intervalo [0,1] é feita mudando os valores de cada recurso de modo a que o valor mínimo seja 0, e depois dividindo-se pelo novo valor máximo (que é a diferença entre os valores máximos originais e mínimos).
      
      Os valores da coluna são transformados com a seguinte fórmula:  
  
      ![normalização usando a função de min&#45;max](media/module/aml-normalization-minmax.png "AML_normalization-minmax")  
  
    - **Logística**: Os valores na coluna são transformados com a seguinte fórmula:

      ![fórmula para normalização por função logística](media/module/aml-normalization-logistic.png "AML_normalization logística")  
  
    - **LogNormal**: Esta opção converte todos os valores numa escala lognormal.
  
      Os valores da coluna são transformados com a seguinte fórmula:
  
      ![registo de fórmula&#45;distribuição normal](media/module/aml-normalization-lognormal.png "AML_normalization-lognormal")
    
      Aqui μ e σ são os parâmetros da distribuição, computados empiricamente a partir dos dados como estimativas de probabilidade máxima, para cada coluna separadamente.  
  
    - **TanH**: Todos os valores são convertidos para uma tangente hiperbólica.
    
      Os valores da coluna são transformados com a seguinte fórmula:
    
      ![normalização usando a função tanh](media/module/aml-normalization-tanh.png "AML_normalization-tanh")

6. Envie o pipeline ou clique duas vezes no módulo **de dados normalizar** e selecione **Executar Selecionado.** 

## <a name="results"></a>Resultados

O módulo **de dados normalizar** gera duas saídas:

- Para ver os valores transformados, clique com o botão direito no módulo e **selecione Visualize**.

    Por padrão, os valores são transformados no lugar. Se pretender comparar os valores transformados com os [valores originais,](./add-columns.md) utilize o módulo Add Columns para recombinar os conjuntos de dados e ver as colunas lado a lado.

- Para guardar a transformação para que possa aplicar o mesmo método de normalização a outro conjunto de dados, selecione o módulo e selecione **conjunto de dados do Registo** no separador **Saídas** no painel direito.

    Em seguida, pode carregar as transformações guardadas do grupo **Transforms** do painel de navegação esquerdo e aplicá-lo a um conjunto de dados com o mesmo esquema utilizando [a Apply Transformation](apply-transformation.md).  


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 