---
title: 'Normalizar dados: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Normalize Data em Azure Machine Learning para transformar um conjunto de dados através da *normalização*..
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 2cf0efb24a10da602ac0c26276a081342776cdcc
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920404"
---
# <a name="normalize-data-module"></a>Normalizar o módulo de dados

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para transformar um conjunto de dados através da *normalização*.

A normalização é uma técnica frequentemente aplicada como parte da preparação de dados para a aprendizagem automática. O objetivo da normalização é alterar os valores das colunas numéricas no conjunto de dados para utilizar uma escala comum, sem distorcer diferenças nas gamas de valores ou perder informação. A normalização também é necessária para que alguns algoritmos modelem os dados corretamente.

Por exemplo, assuma que o seu conjunto de dados de entrada contém uma coluna com valores que variam de 0 a 1, e outra coluna com valores que variam entre 10.000 e 100.000. A grande diferença na *escala* dos números pode causar problemas quando se tenta combinar os valores como características durante a modelação.

*A normalização* evita estes problemas criando novos valores que mantêm a distribuição geral e os rácios nos dados de origem, mantendo ao mesmo tempo valores dentro de uma escala aplicada em todas as colunas numéricas utilizadas no modelo.

Este módulo oferece várias opções para transformar dados numéricos:

- Pode alterar todos os valores para uma escala de 0-1, ou transformar os valores representando-os como percentil em vez de valores absolutos.
- Pode aplicar a normalização numa única coluna ou em várias colunas no mesmo conjunto de dados.
- Se precisar de repetir o pipeline ou aplicar os mesmos passos de normalização a outros dados, pode guardar os passos como uma transformação de normalização e aplicá-lo a outros conjuntos de dados que tenham o mesmo esquema.

> [!WARNING]
> Alguns algoritmos exigem que os dados sejam normalizados antes de treinar um modelo. Outros algoritmos realizam a sua própria escala de dados ou normalização. Portanto, ao escolher um algoritmo de aprendizagem automática para usar na construção de um modelo preditivo, certifique-se de rever os requisitos de dados do algoritmo antes de aplicar a normalização aos dados de treino.

##  <a name="configure-normalize-data"></a>Configurar dados de normalização

Só pode aplicar um método de normalização de cada vez utilizando este módulo. Portanto, o mesmo método de normalização é aplicado a todas as colunas que selecionar. Para utilizar diferentes métodos de normalização, utilize uma segunda instância de **Normalizar dados**.

1. Adicione o módulo **Normalize Data** ao seu pipeline. Pode encontrar o módulo Em Aprendizagem automática Azure, na categoria **De transformação**de Dados, na **categoria Escala e Redução.**

2. Ligue um conjunto de dados que contenha pelo menos uma coluna de todos os números.

3. Utilize o Seletor de Colunas para escolher as colunas numéricas para normalizar. Se não escolher colunas individuais, por defeito **todas as** colunas numéricas da entrada estão incluídas e o mesmo processo de normalização é aplicado a todas as colunas selecionadas. 

    Isto pode levar a resultados estranhos se incluir colunas numéricas que não devem ser normalizadas! Verifique sempre cuidadosamente as colunas.

    Se não forem detetadas colunas numéricas, verifique os metadados da coluna para verificar se o tipo de dados da coluna é um tipo numérico suportado.

    > [!TIP]
    > Para garantir que as colunas de um tipo específico são fornecidas como entrada, tente utilizar as [Colunas Select no](./select-columns-in-dataset.md) módulo Dataset antes de **normalizar os dados**.

4. **Utilize 0 para colunas constantes quando verificadas**: Selecione esta opção quando qualquer coluna numérica contiver um único valor imutável. Isto garante que tais colunas não são utilizadas em operações de normalização.

5. A partir da lista de abandono do **método transformação,** escolha uma única função matemática para aplicar a todas as colunas selecionadas. 
  
    - **Zscore**: Converte todos os valores para uma pontuação z.
    
      Os valores da coluna são transformados utilizando a seguinte fórmula:  
  
      ![normalização usando&#45;pontuações z](media/module/aml-normalization-z-score.png)
  
      O desvio médio e padrão é calculado para cada coluna separadamente. O desvio padrão da população é utilizado.
  
    - **MinMax**: O normalizador min-max redimensiona todas as características para o intervalo [0,1].
    
      A redimensionamento para o intervalo [0,1] é feita deslocando os valores de cada característica de modo a que o valor mínimo seja 0, e depois dividindo-se pelo novo valor máximo (que é a diferença entre os valores máximos e mínimos originais).
      
      Os valores da coluna são transformados utilizando a seguinte fórmula:  
  
      ![normalização usando a&#45;função min max](media/module/aml-normalization-minmax.png "AML_normalization-minmax")  
  
    - **Logística**: Os valores da coluna são transformados utilizando a seguinte fórmula:

      ![fórmula para normalização por função logística](media/module/aml-normalization-logistic.png "AML_normalization-logística")  
  
    - **LogNormal**: Esta opção converte todos os valores numa escala lognormal.
  
      Os valores da coluna são transformados utilizando a seguinte fórmula:
  
      ![fórmula&#45;log distribuição normal](media/module/aml-normalization-lognormal.png "lognormal AML_normalization")
    
      Aqui μ e σ são os parâmetros da distribuição, calculados empiricamente a partir dos dados como estimativas de probabilidade máxima, para cada coluna separadamente.  
  
    - **TanH:** Todos os valores são convertidos para uma tangente hiperbólica.
    
      Os valores da coluna são transformados utilizando a seguinte fórmula:
    
      ![normalização usando a função tanh](media/module/aml-normalization-tanh.png "AML_normalization-tanh")

6. Executar o pipeline ou clicar duas vezes no módulo **Normalize Data** e selecione **Executar Selecionado**. 

## <a name="results"></a>Resultados

O módulo **Normalize Data** gera duas saídas:

- Para ver os valores transformados, clique no módulo e **selecione Visualize**.

    Por defeito, os valores são transformados no lugar. Se pretender comparar os valores transformados com os valores originais, utilize o módulo [Adicionar Colunas](./add-columns.md) para recombinar os conjuntos de dados e ver as colunas lado a lado.

- Para evitar a transformação para que possa aplicar o mesmo método de normalização a outro conjunto de dados, selecione o módulo e selecione o conjunto de **dados do Registo** sob o separador **Saídas** no painel certo.

    Em seguida, pode carregar as transformações guardadas do grupo **Transforms** do painel de navegação esquerdo e aplicá-la num conjunto de dados com o mesmo esquema utilizando a [Apply Transformation](apply-transformation.md).  


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 