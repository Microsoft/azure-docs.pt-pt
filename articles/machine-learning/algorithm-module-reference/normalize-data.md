---
title: 'Normalizar dados: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo Normalize data no Azure Machine Learning para transformar um conjunto de dados por meio da *normalização*.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: e74e80c7db7e624649494201d56fd82486e193d7
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76546610"
---
# <a name="normalize-data-module"></a>Módulo normalizar dados

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use este módulo para transformar um conjunto de um DataSet por meio de *normalização*.

A normalização é uma técnica geralmente aplicada como parte da preparação de dados para o aprendizado de máquina. O objetivo da normalização é alterar os valores das colunas numéricas no conjunto de dados para usar uma escala comum, sem distorcer diferenças nos intervalos de valores ou perda de informações. A normalização também é necessária para alguns algoritmos para modelar os dados corretamente.

Por exemplo, suponha que o conjunto de dados de entrada contenha uma coluna com valores variando de 0 a 1 e outra coluna com valores variando de 10.000 a 100.000. A grande diferença na *escala* dos números pode causar problemas quando você tenta combinar os valores como recursos durante a modelagem.

A *normalização* evita esses problemas criando novos valores que mantêm a distribuição geral e as proporções nos dados de origem, mantendo os valores em uma escala aplicada em todas as colunas numéricas usadas no modelo.

Esse módulo oferece várias opções para transformar dados numéricos:

- Você pode alterar todos os valores para uma escala de 0-1 ou transformar os valores, representando-os como classificações de percentil em vez de valores absolutos.
- Você pode aplicar a normalização a uma única coluna ou a várias colunas no mesmo conjunto de mesmos.
- Se você precisar repetir o pipeline ou aplicar as mesmas etapas de normalização a outros dados, poderá salvar as etapas como uma transformação de normalização e aplicá-la a outros conjuntos de dados que tenham o mesmo esquema.

> [!WARNING]
> Alguns algoritmos exigem que os dados sejam normalizados antes de treinar um modelo. Outros algoritmos executam seu próprio dimensionamento ou normalização de dados. Portanto, ao escolher um algoritmo de aprendizado de máquina para usar na criação de um modelo de previsão, certifique-se de examinar os requisitos de dados do algoritmo antes de aplicar a normalização aos dados de treinamento.

##  <a name="configure-normalize-data"></a>Configurar dados de normalização

Você pode aplicar apenas um método de normalização por vez usando esse módulo. Portanto, o mesmo método de normalização é aplicado a todas as colunas que você selecionar. Para usar métodos de normalização diferentes, use uma segunda instância de **dados normalizados**.

1. Adicione o módulo **normalizar dados** ao seu pipeline. Você pode encontrar o módulo em Azure Machine Learning, em **transformação de dados**, na categoria **escala e redução** .

2. Conecte um conjunto de um DataSet que contenha pelo menos uma coluna de todos os números.

3. Use o seletor de coluna para escolher as colunas numéricas a serem normalizadas. Se você não escolher colunas individuais, por padrão, **todas as** colunas de tipo numérico na entrada serão incluídas e o mesmo processo de normalização será aplicado a todas as colunas selecionadas. 

    Isso pode levar a resultados estranhos se você incluir colunas numéricas que não devem ser normalizadas! Sempre verifique as colunas com cuidado.

    Se nenhuma coluna numérica for detectada, verifique os metadados da coluna para verificar se o tipo de dados da coluna é um tipo numérico com suporte.

    > [!TIP]
    > Para garantir que as colunas de um tipo específico sejam fornecidas como entrada, tente usar o módulo [selecionar colunas no conjunto](./select-columns-in-dataset.md) de dados antes de **normalizar o dado**.

4. **Usar 0 para colunas constantes quando marcada**: Selecione esta opção quando qualquer coluna numérica contiver um único valor inalterável. Isso garante que essas colunas não sejam usadas em operações de normalização.

5. Na lista suspensa **método de transformação** , escolha uma única função matemática para aplicar a todas as colunas selecionadas. 
  
    - **Zscore**: converte todos os valores em uma pontuação z.
    
      Os valores na coluna são transformados usando a seguinte fórmula:  
  
      ![normalização usando pontuações z&#45;](media/module/aml-normalization-z-score.png)
  
      A média e o desvio padrão são calculados para cada coluna separadamente. O desvio padrão da população é usado.
  
    - **Por minMax**: o normalizador min-max redimensiona linearmente cada recurso para o intervalo [0, 1].
    
      Redimensionar para o intervalo [0, 1] é feito alternando os valores de cada recurso para que o valor mínimo seja 0 e, em seguida, dividindo pelo novo valor máximo (que é a diferença entre os valores máximos e mínimos originais).
      
      Os valores na coluna são transformados usando a seguinte fórmula:  
  
      ![normalização usando a função&#45;Min Max](media/module/aml-normalization-minmax.png "AML_normalization-por minMax")  
  
    - **Logística**: os valores na coluna são transformados usando a seguinte fórmula:

      ![fórmula para normalização por função de logística](media/module/aml-normalization-logistic.png "AML_normalization-logística")  
  
    - **LogNormal**: essa opção converte todos os valores em uma escala LogNormal.
  
      Os valores na coluna são transformados usando a seguinte fórmula:
  
      ![distribuição normal&#45;do log de fórmulas](media/module/aml-normalization-lognormal.png "AML_normalization-lognormal")
    
      Aqui, μ e σ são os parâmetros da distribuição, computados empiricamente dos dados como estimativas de probabilidade máxima, para cada coluna separadamente.  
  
    - **TanH**: todos os valores são convertidos em uma tangente hiperbólica.
    
      Os valores na coluna são transformados usando a seguinte fórmula:
    
      ![normalização usando a função tanh](media/module/aml-normalization-tanh.png "AML_normalization-tanh")

6. Execute o pipeline ou clique duas vezes no módulo **normalizar dados** e selecione **executar selecionado**. 

## <a name="results"></a>Resultados

O módulo **normalizar dados** gera duas saídas:

- Para exibir os valores transformados, clique com o botão direito do mouse no módulo e selecione **Visualizar**.

    Por padrão, os valores são transformados em vigor. Se você quiser comparar os valores transformados com os valores originais, use o módulo [adicionar colunas](./add-columns.md) para recombinar os conjuntos de os e exibir as colunas lado a lado.

- Para salvar a transformação para que você possa aplicar o mesmo método de normalização a outro conjunto de um, selecione o módulo e selecione **registrar conjunto de registros** na guia **saídas** no painel direito.

    Em seguida, você pode carregar as transformações salvas no grupo **transformações** do painel de navegação esquerdo e aplicá-las a um conjunto de um DataSet com o mesmo esquema usando a [transformação./Apply](apply-transformation.md).  


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 