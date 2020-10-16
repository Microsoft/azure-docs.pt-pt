---
title: Converter em Valores Indicadores
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Converte-se a Valores Indicadores na Aprendizagem automática Azure para converter colunas que contenham valores categóricos numa série de colunas de indicadores binários.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: f1b194f2c65f95ad4daff0353d05ca589db9ce51
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "79477668"
---
# <a name="convert-to-indicator-values"></a>Converter em Valores Indicadores
Este artigo descreve um módulo de Azure Machine Learning designer.

Utilize o módulo **Converte para Valores Indicadores** no designer de aprendizagem automática Azure para converter colunas que contenham valores categóricos numa série de colunas de indicadores binários.  

Este módulo também produz uma definição da transformação usada para converter para valores indicadores. Pode reutilizar esta transformação em outros conjuntos de dados que tenham o mesmo esquema, utilizando o módulo [De Transformação De Aplicação.](apply-transformation.md)

## <a name="how-to-configure-convert-to-indicator-values"></a>Como configurar Converter para Valores Indicadores

1.  Encontre o **Converta para Valores indicadores** e arraste-o para o projeto do seu gasoduto. Pode encontrar este módulo na categoria **De Transformação de Dados.**
    > [!NOTE]
    > Pode utilizar o módulo [editar metadados](edit-metadata.md) antes do módulo **Converter para Valores Indiciadores** para marcar a coluna(s) alvo como categórica.

1. Ligue o módulo **Converte-se a Valores Indicadores** ao conjunto de dados que contém as colunas que pretende converter. 

1. **Selecione editar** coluna para escolher uma ou mais colunas categóricas.

1. Selecione a opção **Colunas Categóricas Overwrite** se quiser descodur **apenas** as novas colunas Boolean. Por padrão, esta opção está desligada.
    

    > [!TIP]
    >  Se escolher a opção de substituir em excesso, a coluna de origem não é realmente eliminada ou modificada. Em vez disso, as novas colunas são geradas e apresentadas no conjunto de dados de saída, e a coluna de origem permanece disponível no espaço de trabalho. Se precisar de ver os dados originais, pode utilizar o módulo [Add Columns](add-columns.md) a qualquer momento para adicionar a coluna de origem de volta.

1. Envie o oleoduto.

## <a name="results"></a>Results

Suponha que tenha uma coluna com pontuações que indiquem se um servidor tem uma probabilidade alta, média ou baixa de falha.  

| ID do servidor | Pontuação de falha |
| --------- | ------------- |
| 10301     | Baixo           |
| 10302     | Médio        |
| 10303     | Alto          |

Quando aplica **Converter para Valores Indicadores,** o designer converte uma única coluna de etiquetas em várias colunas contendo valores Boolean:  

| ID do servidor | Pontuação de falha - Baixo | Pontuação de falha - Média | Pontuação de falha - Alta |
| --------- | ------------------- | ---------------------- | -------------------- |
| 10301     | 1                   | 0                      | 0                    |
| 10302     | 0                   | 1                      | 0                    |
| 10303     | 0                   | 0                      | 1                    |

Eis como funciona a conversão:  

-   Na coluna **de pontuação Desídua** que descreve o risco, existem apenas três valores possíveis (Alto, Médio e Baixo) e não faltam valores. Então, exatamente três novas colunas são criadas.  

-   As novas colunas indicadoras são nomeadas com base nas posições e valores da coluna de origem, utilizando este padrão: *\<source column>- \<data value>* .  

-   Deve haver um 1 em exatamente uma coluna indicadora, e 0 em todas as outras colunas indicadoras, uma vez que cada servidor pode ter apenas uma classificação de risco.  

Agora pode utilizar as três colunas indicadoras como características num modelo de aprendizagem automática.

O módulo devolve duas saídas:

- **Conjunto de dados de resultados**: Conjunto de dados com colunas de valores indicadores convertidos. As colunas não selecionadas para limpeza também são "passadas".
- **Transformação dos valores indicador :** Uma transformação de dados usada para converter para valores indicadores, que pode ser guardada no seu espaço de trabalho e aplicada a novos dados mais tarde.

## <a name="apply-a-saved-indicator-values-operation-to-new-data"></a>Aplicar uma operação de valores indicadores guardados a novos dados

Se precisar de repetir as operações de valores indicadores com frequência, pode guardar os seus passos de manipulação de dados como uma *transformação* para reutilizá-lo com o mesmo conjunto de dados. Isto é útil se tiver de reimportar frequentemente e, em seguida, limpar dados que tenham o mesmo esquema.

1. Adicione o módulo [de Transformação De Aplicar](apply-transformation.md) ao seu oleoduto.

1. Adicione o conjunto de dados que pretende limpar e ligue o conjunto de dados à porta de entrada da mão direita.

1. Expandir o grupo **de Transformação** de Dados no painel de designer à esquerda. Localize a transformação guardada e arraste-a para o oleoduto.

1. Ligue a transformação guardada à porta de entrada esquerda da [Apply Transformation](apply-transformation.md).

   Quando se aplica uma transformação guardada, não é possível selecionar quais colunas transformar. Isto porque a transformação foi definida e aplica-se automaticamente aos tipos de dados especificados na operação original.

1. Envie o oleoduto.
 
## <a name="technical-notes"></a>Notas técnicas  

Esta secção contém detalhes de implementação, dicas e respostas a perguntas frequentes.

### <a name="usage-tips"></a>Dicas de utilização

-   Apenas colunas marcadas como categóricas podem ser convertidas em colunas indicadoras. Se vir o seguinte erro, é provável que uma das colunas selecionadas não seja categórica:  

     Erro 0056: Coluna com nome  \<column name> não está numa categoria permitida.  

     Por predefinição, a maioria das colunas de cordas são manuseadas como funcionalidades de cadeia, pelo que deve marcar explicitamente como categóricas usando [metadados de edição](edit-metadata.md).  

-   Não há limite para o número de colunas que pode converter em colunas indicadoras. No entanto, como cada coluna de valores pode produzir várias colunas indicadoras, é possível que queira converter e rever apenas algumas colunas de cada vez.  

-   Se a coluna contiver valores em falta, é criada uma coluna indicadora separada para a categoria em falta, com este nome: * \<source column> - Falta*  

-   Se a coluna que converte para valores indicadores contiver números, devem ser marcadas como categóricas como qualquer outra coluna de recurso. Depois de o ter feito, os números são tratados como valores discretos. Por exemplo, se tiver uma coluna numérica com valores de MPG que variam entre 25 e 30, será criada uma nova coluna indicadora para cada valor discreto:  

    | Criação       | Autoestrada mpg -25 | Autoestrada mpg -26 | Autoestrada mpg -27 | Autoestrada mpg -28 | Autoestrada mpg -29 | Autoestrada mpg -30 |
    | ---------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- |
    | Carros Contoso | 0               | 0               | 0               | 0               | 0               | 1               |

- Para evitar adicionar demasiadas dimensões ao seu conjunto de dados. Recomendamos que verifique primeiro o número de valores na coluna e que coloque ou quantifica os dados adequadamente.  


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
