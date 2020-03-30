---
title: Converter em Valores Indicadores
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Converter para Valores Indicadores em Aprendizagem automática Azure para converter colunas que contenham valores categóricos numa série de colunas indicadoras binárias.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: f1b194f2c65f95ad4daff0353d05ca589db9ce51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477668"
---
# <a name="convert-to-indicator-values"></a>Converter em Valores Indicadores
Este artigo descreve um módulo de designer de Machine Learning Azure.

Utilize o módulo **Converter para Valores Indicadores** no designer de Machine Learning Azure para converter colunas que contenham valores categóricos numa série de colunas indicadoras binárias.  

Este módulo também produz uma definição da transformação usada para converter em valores indicadores. Pode reutilizar esta transformação em outros conjuntos de dados que tenham o mesmo esquema, utilizando o módulo [De Transformação de Aplicação.](apply-transformation.md)

## <a name="how-to-configure-convert-to-indicator-values"></a>Como configurar Converter em Valores Indicadores

1.  Encontre o **Converter em Valores Indicadores** e arraste-o para o seu projeto de pipeline. Pode encontrar este módulo na categoria **de Transformação** de Dados.
    > [!NOTE]
    > Pode utilizar o módulo [Deedição de Metadados](edit-metadata.md) antes do módulo **Converter para Valores Indiciadores** para marcar as colunas de alvo como categóricas.

1. Ligue o módulo **Converter a Valores Indicadores** ao conjunto de dados que contém as colunas que pretende converter. 

1. Selecione **editar colunas** para escolher uma ou mais colunas categóricas.

1. Selecione a opção **colunas categóricas de sobreescrita** se pretender passar **apenas** as novas colunas Boolean. Por defeito, esta opção está desligada.
    

    > [!TIP]
    >  Se escolher a opção de sobrepor, a coluna de origem não é realmente eliminada ou modificada. Em vez disso, as novas colunas são geradas e apresentadas no conjunto de dados de saída, e a coluna de origem permanece disponível no espaço de trabalho. Se precisar de ver os dados originais, pode utilizar o módulo [Adicionar Colunas](add-columns.md) a qualquer momento para voltar a adicionar a coluna de origem.

1. Submeta o oleoduto.

## <a name="results"></a>Resultados

Suponha que tenha uma coluna com pontuações que indique se um servidor tem uma alta, média ou baixa probabilidade de falha.  

| ID do servidor | Pontuação de falha |
| --------- | ------------- |
| 10301     | Baixa           |
| 10302     | Médio        |
| 10303     | Alta          |

Quando aplica **Converter a Valores Indicadores,** o designer converte uma única coluna de etiquetas em múltiplas colunas contendo valores booleanos:  

| ID do servidor | Pontuação de insucesso - Baixo | Pontuação de falha - Médio | Pontuação de insucesso - Alta |
| --------- | ------------------- | ---------------------- | -------------------- |
| 10301     | 1                   | 0                      | 0                    |
| 10302     | 0                   | 1                      | 0                    |
| 10303     | 0                   | 0                      | 1                    |

É assim que funciona a conversão:  

-   Na coluna **de pontuação de Falha** que descreve o risco, existem apenas três valores possíveis (Alto, Médio e Baixo) e nenhum valore em falta. Então, exatamente três novas colunas são criadas.  

-   As novas colunas indicadoras são nomeadas com base nas rubricas e valores da coluna de origem, utilizando este padrão: * \<coluna de origem \<>- valor de dados>*.  

-   Deve haver um 1 em exatamente uma coluna indicadora, e 0 em todas as outras colunas indicadoras, uma vez que cada servidor pode ter apenas uma classificação de risco.  

Agora pode utilizar as três colunas indicadoras como características num modelo de aprendizagem automática.

O módulo devolve duas saídas:

- **Conjunto de dados de resultados**: Um conjunto de dados com colunas de valores indicadores convertidos. As colunas não selecionadas para limpeza também são "passadas".
- **Transformação de valores indicadores**: Uma transformação de dados utilizada para converter em valores indicadores, que pode ser guardada no seu espaço de trabalho e aplicada a novos dados posteriormente.

## <a name="apply-a-saved-indicator-values-operation-to-new-data"></a>Aplicar um conjunto de valores indicadores guardados a novos dados

Se precisar de repetir frequentemente as operações de valores indicadores, pode guardar os seus passos de manipulação de dados como *uma transformação* para reutilizá-lo com o mesmo conjunto de dados. Isto é útil se tiver de reimportar frequentemente e, em seguida, limpar dados que tenham o mesmo esquema.

1. Adicione o módulo [de Transformação aplicar](apply-transformation.md) ao seu pipeline.

1. Adicione o conjunto de dados que pretende limpar e ligue o conjunto de dados à porta de entrada da direita.

1. Expanda o grupo **de Transformação** de Dados no painel esquerdo do designer. Localize a transformação guardada e arraste-a para o oleoduto.

1. Ligue a transformação guardada à entrada esquerda da Transformação de [Aplicação](apply-transformation.md).

   Quando aplica uma transformação guardada, não é possível selecionar quais colunas para transformar. Isto porque a transformação foi definida e aplica-se automaticamente aos tipos de dados especificados na operação original.

1. Submeta o oleoduto.
 
## <a name="technical-notes"></a>Notas técnicas  

Esta secção contém detalhes de implementação, dicas e respostas a perguntas frequentes.

### <a name="usage-tips"></a>Dicas de utilização

-   Apenas colunas marcadas como categóricas podem ser convertidas em colunas indicadoras. Se vir o seguinte erro, é provável que uma das colunas selecionadas não seja categórica:  

     Erro 0056: Coluna \<com nome de coluna de nome> não está numa categoria permitida.  

     Por predefinição, a maioria das colunas de cordas são manuseadas como funcionalidades de cordas, por isso deve marcá-las explicitamente como categóricas usando [metadados de edição](edit-metadata.md).  

-   Não há limite para o número de colunas que pode converter em colunas indicadoras. No entanto, como cada coluna de valores pode produzir várias colunas indicadoras, pode querer converter e rever apenas algumas colunas de cada vez.  

-   Se a coluna contiver valores em falta, é criada uma coluna indicadora separada para a categoria em falta, com este nome: * \<coluna de origem>- Desaparecido*  

-   Se a coluna que converte em valores indicadoros contiver números, devem ser marcadas como categóricas como qualquer outra coluna de características. Depois de o ter feito, os números são tratados como valores discretos. Por exemplo, se tiver uma coluna numérica com valores MPG que variam entre os 25 e os 30, será criada uma nova coluna indicadora para cada valor discreto:  

    | Fabrico       | Autoestrada mpg -25 | Autoestrada mpg -26 | Autoestrada mpg -27 | Autoestrada mpg -28 | Autoestrada mpg -29 | Autoestrada mpg -30 |
    | ---------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- |
    | Carros Contoso | 0               | 0               | 0               | 0               | 0               | 1               |

- Para evitar adicionar demasiadas dimensões ao seu conjunto de dados. Recomendamos que verifique primeiro o número de valores na coluna e que se desloque ou quantize os dados de forma adequada.  


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
