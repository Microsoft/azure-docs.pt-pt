---
title: 'Normalizar dados: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de normalizar dados no serviço Azure Machine Learning para transformar um conjunto de dados por meio *normalização*....
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 95069bafa94770511c7ee40e82068960298fd6c5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029449"
---
# <a name="normalize-data-module"></a>Normalizar o módulo de dados

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para transformar um conjunto de dados por meio *normalização*.

Normalização é uma técnica, muitas vezes, são aplicada como parte da preparação de dados para machine learning. O objetivo de normalização é alterar os valores das colunas numéricas no conjunto de dados para utilizar uma escala comum, sem distorçam diferenças nos intervalos de valores ou informações perdedora. Normalização também é necessária para alguns algoritmos modelar os dados corretamente.

Por exemplo, suponha que o conjunto de dados de entrada contém uma coluna com valores entre 0 e 1 e outra coluna com valores entre 10 000 para 100 000. O grande diferença na *dimensionamento* dos números o poderia causar problemas quando tenta combinar os valores como recursos durante a Modelagem.

*Normalização* evita esses problemas através da criação de novos valores que mantêm a distribuição geral e a rácios nos dados de origem, mantendo os valores dentro de um dimensionamento aplicadas em todas as colunas numéricas utilizadas no modelo.

Este módulo oferece várias opções para transformar dados numéricos:

- Pode alterar todos os valores para um 0-1 dimensionar ou transformar os valores por meio de representação-los como percentil classifica em vez de valores absolutos.
- Pode aplicar a normalização para uma única coluna ou para várias colunas no mesmo conjunto de dados.
- Se precisar repetir a experimentação, ou aplicar os mesmos passos de normalização para outros dados, pode guardar os passos como uma transformação de normalização e aplicá-la a outros conjuntos de dados que têm o mesmo esquema.

> [!WARNING]
> Alguns algoritmos exigem que os dados sejam normalizados antes de preparar um modelo. Outros algoritmos executam seus próprios dados dimensionamento ou a normalização. Portanto, ao escolher um algoritmo de machine learning para utilizar na criação de um modelo preditivo, certifique-se de que revê os requisitos de dados do algoritmo antes de aplicar a normalização para os dados de treinamento.

##  <a name="configure-normalize-data"></a>Configurar normalizar dados

Pode aplicar apenas um método de normalização cada vez usando este módulo. Por conseguinte, o mesmo método de normalização é aplicado a todas as colunas que selecionar. Para utilizar os métodos de normalização diferente, utilize uma segunda instância do **normalizar dados**.

1. Adicionar a **normalizar dados** módulo à sua experimentação. Pode encontrar o módulo no Azure Machine Learning, em **transformação de dados**, na **escala e reduza** categoria.

2. Ligar um conjunto de dados que contém pelo menos uma coluna de todos os números.

3. Utilize o Seletor de colunas para escolher as colunas numéricas normalizar. Se não escolher colunas individuais, por predefinição **todos os** colunas de tipo numérico na entrada estão incluídas, e o mesmo processo de normalização é aplicado a todas as colunas selecionadas. 

    Isso pode levar a resultados estranhos se incluir colunas numéricas que não devem ser normalizadas! Verifique sempre cuidadosamente as colunas de segurança.

    Se não existem colunas numéricas forem detetadas, verifique os metadados de coluna para verificar se o tipo de dados da coluna é um tipo numérico suportado.

    > [!TIP]
    > Para garantir que as colunas de um tipo específico são fornecidas como entrada, experimente utilizar o [Select Columns in Dataset](./select-columns-in-dataset.md) módulo antes **normalizar dados**.

4. **Utilize 0 para colunas constantes quando selecionado**:  Selecione esta opção quando qualquer coluna numérica contém um único valor inalterado. Isto garante que este tipo de colunas não é utilizada em operações de normalização.

5. Partir do **método de transformação** lista pendente lista, escolha uma única função de matemática para aplicar a todas as colunas selecionadas. 
  
    - **Zscore**: Converte todos os valores para uma pontuação de z.
    
      Os valores na coluna são transformados usando a seguinte fórmula:  
  
      ![normalização usando z&#45;pontuações](media/module/aml-normalization-z-score.png)
  
      Desvio médio e standard são calculados em separado para cada coluna. Desvio padrão da população é utilizado.
  
    - **MinMax**: O normalizer mínima-máxima linearmente redimensiona todas as funcionalidades para o intervalo [0,1].
    
      Ao redimensionar o para o intervalo [0,1] é feito com a mudar os valores de cada funcionalidade, para que o valor mínimo é 0 e, em seguida, dividindo pelo novo valor máximo (que é a diferença entre os valores máximos e mínimos originais).
      
      Os valores na coluna são transformados usando a seguinte fórmula:  
  
      ![normalização com o mínimo de&#45;número máximo de função](media/module/aml-normalization-minmax.png "AML_normalization minmax")  
  
    - **Logística**: Os valores na coluna são transformados usando a seguinte fórmula:

      ![fórmula para a normalização pela função logística](media/module/aml-normalization-logistic.png "AML_normalization logística")  
  
    - **LogNormal**: Esta opção converte todos os valores para um dimensionamento lognormal.
  
      Os valores na coluna são transformados usando a seguinte fórmula:
  
      ![registo de fórmulas&#45;distribuição normal](media/module/aml-normalization-lognormal.png "AML_normalization lognormal")
    
      Aqui μ e σ são os parâmetros da distribuição, computado empiricamente dos dados como estimativas de probabilidade máximo, para cada coluna em separado.  
  
    - **TanH**: Todos os valores são convertidos num tangente hiperbólica.
    
      Os valores na coluna são transformados usando a seguinte fórmula:
    
      ![normalização usando a função tanh](media/module/aml-normalization-tanh.png "AML_normalization tanh")

6. Execute a experimentação, ou clique duas vezes o **normalizar dados** módulo e selecione **executar seleção**. 

## <a name="results"></a>Resultados

O **normalizar dados** módulo gera duas saídas:

- Para ver os valores transformados, o módulo com o botão direito, selecione **transformou o conjunto de dados**e clique em **Visualize**.

    Por predefinição, os valores são transformados em vigor. Se deseja comparar os valores transformados para os valores originais, utilize o [adicionar colunas](./add-columns.md) módulo recombiná os conjuntos de dados e ver as colunas lado a lado.

- Para guardar a transformação para que pode aplicar o mesmo método de normalização para outro conjunto de dados semelhante, o módulo com o botão direito, selecione **função de transformação**e clique em **guardar como transformação**.

    Em seguida, é possível carregar as transformações guardadas do **transforma** grupo do painel de navegação esquerdo e aplicá-la para um conjunto de dados com o mesmo esquema utilizando [. / Aplicar transformação](apply-transformation.md).  


## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 