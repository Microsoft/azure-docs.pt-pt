---
title: 'Dados em falta limpos: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de dados em falta no Azure Machine Learning para remover, substituir ou inferir valores em falta.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 5b7943b2026d640ae7e5d119e165bd752ae2fe7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898834"
---
# <a name="clean-missing-data-module"></a>Módulo de dados em falta limpo

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para remover, substituir ou inferir valores em falta. 

Os cientistas de dados geralmente verificam dados por valores em falta e, em seguida, realizam várias operações para corrigir os dados ou inserir novos valores. O objetivo destas operações de limpeza é prevenir problemas causados pela falta de dados que possam surgir na formação de um modelo. 

Este módulo suporta vários tipos de operações para "limpar" valores em falta, incluindo:

+ Substituição dos valores em falta por um espaço reservado, médio ou outro valor
+ Removendo completamente as linhas e colunas que têm valores em falta
+ Inferir valores baseados em métodos estatísticos


A utilização deste módulo não altera o conjunto de dados de origem. Em vez disso, cria um novo conjunto de dados no seu espaço de trabalho que pode utilizar no fluxo de trabalho subsequente. Também pode guardar o novo conjunto de dados limpo para reutilização.

Este módulo também produz uma definição da transformação usada para limpar os valores em falta. Pode reutilizar esta transformação em outros conjuntos de dados que tenham o mesmo esquema, utilizando o módulo [De Transformação De Aplicação.](./apply-transformation.md)  

## <a name="how-to-use-clean-missing-data"></a>Como utilizar dados em falta de limpeza

Este módulo permite definir uma operação de limpeza. Também pode guardar a operação de limpeza para que possa aplicá-la mais tarde a novos dados. Consulte as seguintes secções de como criar e guardar um processo de limpeza: 
 
+ [Para substituir os valores em falta](#replace-missing-values)
  
+ [Para aplicar uma transformação de limpeza a novos dados](#apply-a-saved-cleaning-operation-to-new-data)
 
> [!IMPORTANT]
> O método de limpeza que utiliza para manusear valores em falta pode afetar drasticamente os seus resultados. Recomendamos que experimente diferentes métodos. Considere tanto a justificação para a utilização de um determinado método, como a qualidade dos resultados.

### <a name="replace-missing-values"></a>Substituir valores em falta  

Cada vez que aplica o módulo  [De Dados Desaparecidos Limpos](./clean-missing-data.md) a um conjunto de dados, a mesma operação de limpeza é aplicada a todas as colunas que seleciona. Portanto, se precisar de limpar diferentes colunas utilizando diferentes métodos, utilize instâncias separadas do módulo.

1.  Adicione o módulo [de Dados Desaparecidos Limpos](./clean-missing-data.md) ao seu pipeline e conecte o conjunto de dados que tem valores em falta.  
  
2.  Para **que as colunas sejam limpas,** escolha as colunas que contêm os valores em falta que pretende alterar. Pode escolher várias colunas, mas deve utilizar o mesmo método de substituição em todas as colunas selecionadas. Portanto, normalmente é necessário limpar as colunas de cordas e colunas numéricas separadamente.

    Por exemplo, para verificar se há valores em falta em todas as colunas numéricas:

    1. Selecione o módulo **De Dados Desaparecidos Limpos** e clique na **coluna Editar** no painel direito do módulo.

    3. Para **incluir**, selecione os tipos de **colunas** da lista de dropdown e, em seguida, selecione **Numérico**. 
  
    Qualquer método de limpeza ou substituição que escolha deve ser aplicável a **todas as** colunas da seleção. Se os dados em qualquer coluna forem incompatíveis com a operação especificada, o módulo retorna um erro e para o gasoduto.
  
3.  Para **o rácio de valor em falta mínimo,** especifique o número mínimo de valores em falta necessários para a operação a realizar.  
  
    Utilize esta opção em combinação com o **rácio de valor máximo em falta** para definir as condições em que uma operação de limpeza é realizada no conjunto de dados. Se houver demasiadas ou poucas linhas que faltam valores, a operação não pode ser executada. 
  
    O número que introduz representa o **rácio** de valores em falta para todos os valores da coluna. Por predefinição, a propriedade **de rácio de valor mínimo em falta** está definida para 0. Isto significa que os valores em falta são limpos mesmo que haja apenas um valor em falta. 

    > [!WARNING]
    > Esta condição deve ser cumprida por cada coluna para que a operação especificada se aplique. Por exemplo, assuma que selecionou três colunas e depois definirá o rácio mínimo de valores em falta para 0,2 (20%), mas apenas uma coluna tem 20% de valores em falta. Neste caso, a operação de limpeza aplicar-se-ia apenas à coluna com valores mais de 20% em falta. Portanto, as outras colunas seriam inalteradas.
    > 
    > Se tiver dúvidas sobre se os valores em falta foram alterados, selecione a opção, **Gere a coluna indicadora de valor em falta**. Uma coluna é anexada ao conjunto de dados para indicar se cada coluna cumpriu ou não os critérios especificados para os intervalos mínimos e máximos.  
  
4. Para **o rácio de valor em falta máximo,** especifique o número máximo de valores em falta que podem estar presentes para a operação a realizar.   
  
    Por exemplo, pode querer realizar a substituição de valor em falta apenas se 30% ou menos das linhas contiverem valores em falta, mas deixar os valores como-é se mais de 30% das linhas tiverem valores em falta.  
  
    Define o número como a relação entre valores em falta e todos os valores da coluna. Por predefinição, o **rácio de valor em falta máximo** é definido para 1. Isto significa que os valores em falta são limpos mesmo que 100% dos valores na coluna estejam em falta.  
  
   
  
5. Para **o modo de limpeza,** selecione uma das seguintes opções para substituir ou remover valores em falta:  
  
  
    + **Valor de substituição personalizado**: Utilize esta opção para especificar um valor reservado (como um 0 ou UM) que se aplique a todos os valores em falta. O valor que especifica como substituição deve ser compatível com o tipo de dados da coluna.
  
    + **Substitua por média**: Calcula a média da coluna e utiliza a média como valor de substituição por cada valor em falta na coluna.  
  
        Aplica-se apenas a colunas que tenham tipos de dados Inteiros, Duplos ou Booleanos.  
  
    + **Substitua por mediana**: Calcula o valor mediano da coluna e utiliza o valor mediano como substituição de qualquer valor em falta na coluna.  
  
        Aplica-se apenas a colunas que tenham tipos de dados Inteiros ou Duplos. 
  
    + **Substitua-o pelo modo**: Calcula o modo para a coluna e utiliza o modo como valor de substituição por cada valor em falta na coluna.  
  
        Aplica-se a colunas que tenham tipos de dados Inteiros, Duplos, Boolean ou Categóricos. 
  
    + **Remover linha inteira**: Remove completamente qualquer linha no conjunto de dados que tenha um ou mais valores em falta. Isto é útil se o valor em falta pode ser considerado aleatoriamente em falta.  
  
    + **Remover coluna inteira**: Remove completamente qualquer coluna do conjunto de dados que tenha um ou mais valores em falta.  
  
    
  
6. A opção **O valor de substituição** está disponível se tiver selecionado a opção, **valor de substituição personalizado.** Digite um novo valor a utilizar como valor de substituição para todos os valores em falta na coluna.  
  
    Note que só pode utilizar esta opção em colunas que tenham o Número Inteiro, Duplo, Boolean ou String.
  
7. **Gerar coluna indicadora de valor em falta**: Selecione esta opção se pretender obter alguma indicação de se os valores da coluna satisfazem os critérios de limpeza de valor em falta. Esta opção é particularmente útil quando está a preparar uma nova operação de limpeza e pretende certificar-se de que funciona como foi concebido.
  
8. Envie o oleoduto.

### <a name="results"></a>Resultados

O módulo devolve duas saídas:  

-   **Conjunto de dados limpo**: Um conjunto de dados composto pelas colunas selecionadas, com valores em falta tratados conforme especificado, juntamente com uma coluna indicadora, se selecionar essa opção.  

    As colunas não selecionadas para limpeza também são "passadas".  
  
-  **Transformação de limpeza**: Uma transformação de dados usada para a limpeza, que pode ser guardada no seu espaço de trabalho e aplicada a novos dados mais tarde.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Aplicar uma operação de limpeza guardada em novos dados  

Se precisar de repetir as operações de limpeza com frequência, recomendamos que guarde a sua receita para a limpeza de dados como uma *transformação,* para reutilizar com o mesmo conjunto de dados. Salvar uma transformação de limpeza é particularmente útil se tiver de re importar frequentemente e, em seguida, limpar dados que têm o mesmo esquema.  
      
1.  Adicione o módulo [de Transformação De Aplicar](./apply-transformation.md) ao seu oleoduto.  
  
2.  Adicione o conjunto de dados que pretende limpar e ligue o conjunto de dados à porta de entrada da mão direita.  
  
3.  Expandir o grupo **Transforms** no painel esquerdo do designer. Localize a transformação guardada e arraste-a para o oleoduto.  

4.  Ligue a transformação guardada à porta de entrada esquerda da [Apply Transformation](./apply-transformation.md). 

    Quando se aplica uma transformação salva, não é possível selecionar as colunas às quais a transformação é aplicada. Isto porque a transformação já foi definida e aplica-se automaticamente às colunas especificadas na operação original.

    No entanto, suponha que criou uma transformação num subconjunto de colunas numéricas. Pode aplicar esta transformação a um conjunto de tipos de colunas mistas sem levantar um erro, porque os valores em falta são alterados apenas nas colunas numéricas correspondentes.

6.  Envie o oleoduto.  

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 