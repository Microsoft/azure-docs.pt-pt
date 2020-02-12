---
title: 'Normalizar dados: referência de módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Normalize Data em Azure Machine Learning para transformar um conjunto de dados através da *normalização*..
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 8eb54e232478ae24e1efb49a8ad43dc827aa2b6a
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77150685"
---
# <a name="normalize-data-module"></a>Módulo normalizar dados

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para transformar um conjunto de dados através da *normalização*.

A normalização é uma técnica geralmente aplicada como parte da preparação de dados para o aprendizado de máquina. O objetivo da normalização é alterar os valores das colunas numéricas no conjunto de dados para usar uma escala comum, sem distorcer diferenças nos intervalos de valores ou perda de informações. A normalização também é necessária para alguns algoritmos para modelar os dados corretamente.

Por exemplo, suponha que o conjunto de dados de entrada contenha uma coluna com valores variando de 0 a 1 e outra coluna com valores variando de 10.000 a 100.000. A grande diferença na *escala* dos números pode causar problemas quando se tenta combinar os valores como características durante a modelação.

*A normalização* evita estes problemas criando novos valores que mantêm a distribuição geral e os rácios nos dados de origem, mantendo ao mesmo tempo valores dentro de uma escala aplicada em todas as colunas numéricas utilizadas no modelo.

Esse módulo oferece várias opções para transformar dados numéricos:

- Você pode alterar todos os valores para uma escala de 0-1 ou transformar os valores, representando-os como classificações de percentil em vez de valores absolutos.
- Você pode aplicar a normalização a uma única coluna ou a várias colunas no mesmo conjunto de mesmos.
- Se você precisar repetir o pipeline ou aplicar as mesmas etapas de normalização a outros dados, poderá salvar as etapas como uma transformação de normalização e aplicá-la a outros conjuntos de dados que tenham o mesmo esquema.

> [!WARNING]
> Alguns algoritmos exigem que os dados sejam normalizados antes de treinar um modelo. Outros algoritmos executam seu próprio dimensionamento ou normalização de dados. Portanto, ao escolher um algoritmo de aprendizado de máquina para usar na criação de um modelo de previsão, certifique-se de examinar os requisitos de dados do algoritmo antes de aplicar a normalização aos dados de treinamento.

##  <a name="configure-normalize-data"></a>Configurar dados de normalização

Você pode aplicar apenas um método de normalização por vez usando esse módulo. Portanto, o mesmo método de normalização é aplicado a todas as colunas que você selecionar. Para utilizar diferentes métodos de normalização, utilize uma segunda instância de **Normalizar dados**.

1. Adicione o módulo **Normalize Data** ao seu pipeline. Pode encontrar o módulo Em Aprendizagem automática Azure, na categoria **De transformação**de Dados, na **categoria Escala e Redução.**

2. Conecte um conjunto de um DataSet que contenha pelo menos uma coluna de todos os números.

3. Use o seletor de coluna para escolher as colunas numéricas a serem normalizadas. Se não escolher colunas individuais, por defeito **todas as** colunas numéricas da entrada estão incluídas e o mesmo processo de normalização é aplicado a todas as colunas selecionadas. 

    Isso pode levar a resultados estranhos se você incluir colunas numéricas que não devem ser normalizadas! Sempre verifique as colunas com cuidado.

    Se nenhuma coluna numérica for detectada, verifique os metadados da coluna para verificar se o tipo de dados da coluna é um tipo numérico com suporte.

    > [!TIP]
    > Para garantir que as colunas de um tipo específico são fornecidas como entrada, tente utilizar as [Colunas Select no](./select-columns-in-dataset.md) módulo Dataset antes de **normalizar os dados**.

4. **Utilize 0 para colunas constantes quando verificadas**: Selecione esta opção quando qualquer coluna numérica contiver um único valor imutável. Isso garante que essas colunas não sejam usadas em operações de normalização.

5. A partir da lista de abandono do **método transformação,** escolha uma única função matemática para aplicar a todas as colunas selecionadas. 
  
    - **Zscore**: Converte todos os valores para uma pontuação z.
    
      Os valores na coluna são transformados usando a seguinte fórmula:  
  
      ![normalização usando pontuações z&#45;](media/module/aml-normalization-z-score.png)
  
      A média e o desvio padrão são calculados para cada coluna separadamente. O desvio padrão da população é usado.
  
    - **MinMax**: O normalizador min-max redimensiona todas as características para o intervalo [0,1].
    
      A redimensionamento para o intervalo [0,1] é feita deslocando os valores de cada característica de modo a que o valor mínimo seja 0, e depois dividindo-se pelo novo valor máximo (que é a diferença entre os valores máximos e mínimos originais).
      
      Os valores na coluna são transformados usando a seguinte fórmula:  
  
      ![normalização usando a&#45;função min max](media/module/aml-normalization-minmax.png "AML_normalization-minmax")  
  
    - **Logística**: Os valores da coluna são transformados utilizando a seguinte fórmula:

      ![fórmula para normalização por função logística](media/module/aml-normalization-logistic.png "AML_normalization-logística")  
  
    - **LogNormal**: Esta opção converte todos os valores numa escala lognormal.
  
      Os valores na coluna são transformados usando a seguinte fórmula:
  
      ![fórmula&#45;log distribuição normal](media/module/aml-normalization-lognormal.png "lognormal AML_normalization")
    
      Aqui, μ e σ são os parâmetros da distribuição, computados empiricamente dos dados como estimativas de probabilidade máxima, para cada coluna separadamente.  
  
    - **TanH:** Todos os valores são convertidos para uma tangente hiperbólica.
    
      Os valores na coluna são transformados usando a seguinte fórmula:
    
      ![normalização usando a função tanh](media/module/aml-normalization-tanh.png "AML_normalization-tanh")

6. Executar o pipeline ou clicar duas vezes no módulo **Normalize Data** e selecione **Executar Selecionado**. 

## <a name="results"></a>Resultados

O módulo **Normalize Data** gera duas saídas:

- Para ver os valores transformados, clique no módulo e **selecione Visualize**.

    Por padrão, os valores são transformados em vigor. Se pretender comparar os valores transformados com os valores originais, utilize o módulo [Adicionar Colunas](./add-columns.md) para recombinar os conjuntos de dados e ver as colunas lado a lado.

- Para evitar a transformação para que possa aplicar o mesmo método de normalização a outro conjunto de dados, selecione o módulo e selecione o conjunto de **dados do Registo** sob o separador **Saídas** no painel certo.

    Em seguida, pode carregar as transformações guardadas do grupo **Transforms** do painel de navegação esquerdo e aplicá-la num conjunto de dados com o mesmo esquema utilizando [a transformação ./Apply .](apply-transformation.md)  


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 