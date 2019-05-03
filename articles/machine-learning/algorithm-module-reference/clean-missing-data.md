---
title: 'Limpe dados em falta: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de apagar dados em falta no serviço Azure Machine Learning para remover, substituir ou inferir valores em falta.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: de81204219a102734f1820258a3c32e59a64c685
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028789"
---
# <a name="clean-missing-data-module"></a>Módulo de dados em falta limpo

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para remover, substituir ou inferir valores em falta. 

Os cientistas de dados, muitas vezes, verifique valores em falta nos dados e, em seguida, executam várias operações para corrigir os dados ou inserir novos valores. O objetivo destas operações de limpeza é evitar problemas causados por dados em falta que podem surgir quando preparar um modelo. 

Este módulo suporta vários tipo das operações para a "limpeza" valores em falta, incluindo:

+ Substituir valores em falta com um marcador de posição, média ou outro valor
+ Remover completamente linhas e colunas que tenham valores em falta
+ Inferindo os valores com base em métodos estatísticos


Utilizar este módulo não altera o conjunto de dados de origem. Em vez disso, ele cria um novo conjunto de dados na sua área de trabalho que pode utilizar no fluxo de trabalho subsequente. Também pode guardar o conjunto de dados novo e limpo para reutilização.

Este módulo também gera uma definição da transformação utilizada para limpar os valores em falta. Pode reutilizar essa transformação em outros conjuntos de dados que tenham o mesmo esquema, ao utilizar o [transformação de aplicar](./apply-transformation.md) módulo.  

## <a name="how-to-use-clean-missing-data"></a>Como utilizar apagar dados em falta

Este módulo permite-lhe definir uma operação de limpeza. Também pode guardar a operação de limpeza para que pode aplicá-lo mais tarde para novos dados. Veja as ligações seguintes para obter uma descrição de como criar e guardar um processo de limpeza: 
 
+ Para substituir valores em falta
  
+ Para aplicar uma transformação de limpeza para novos dados
 
> [!IMPORTANT]
> O método de limpeza que utiliza para lidar com valores em falta drasticamente pode afetar os resultados. Recomendamos que experimente com diferentes métodos. Considere a justificativa para utilização de um método em particular e a qualidade dos resultados.

### <a name="replace-missing-values"></a>Substituir valores em falta  

Sempre que aplicar a [Clean Missing Data](./clean-missing-data.md) módulo para um conjunto de dados, a mesma operação de limpeza é aplicado a todas as colunas que selecionar. Portanto, se precisar de limpar a utilizando métodos diferentes de colunas diferentes, use instâncias separadas do módulo.

1.  Adicionar a [Clean Missing Data](./clean-missing-data.md) módulo à sua experimentação e ligue-se o conjunto de dados que tem valores em falta.  
  
2.  Para **colunas ser apagados**, escolha as colunas que contêm os valores em falta que pretende alterar. Pode selecionar várias colunas, mas tem de utilizar o mesmo método de substituição em todas as colunas selecionadas. Portanto, normalmente, precisa limpar colunas numéricas e de colunas de cadeia de caracteres em separado.

    Por exemplo, para verificar a existência de valores em falta em todas as colunas numéricas:

    1. Abra o Seletor de colunas e selecione **com regras**.
    2. Para **começar com**, selecione **sem colunas**.

        Pode também iniciar com todas as colunas e, em seguida, excluir colunas. Inicialmente, as regras não são apresentadas se primeiro clique em **todas as colunas**, mas é possível clicar **sem colunas** e, em seguida, clique em **todas as colunas** novamente para começar com todas as colunas e, em seguida, filtrar colunas (excluir) com base no nome do tipo de dados, ou índice de colunas.

    3. Para **inclusão**, selecione **tipo de coluna** na lista pendente e, em seguida, selecione **numérico**, ou um tipo numérico mais específico. 
  
    Qualquer método de limpeza ou substituição que escolher tem de ser aplicável a **todos os** colunas na seleção. Se os dados em qualquer coluna são incompatíveis com a operação especificada, o módulo retorna um erro e interrompe a experimentação.
  
3.  Para **mínimo em falta a proporção de valor**, especifique o número mínimo de valores em falta necessário para a operação a ser executada.  
  
    Utilize esta opção em combinação com **máximo em falta a proporção de valor** para definir as condições sob as quais uma operação de limpeza é executada no conjunto de dados. Se existirem demasiados ou muito poucas linhas que têm em falta valores, não é possível efetuar a operação. 
  
    O número que introduz representa a **proporção** de valores em falta para todos os valores na coluna. Por predefinição, o **proporção de valor em falta mínimo** propriedade está definida como 0. Isso significa que os valores em falta são limpos mesmo que haja apenas um valor em falta. 

    > [!WARNING]
    > Esta condição têm de ser cumprida por cada coluna para que a operação especificada aplicar. Por exemplo, suponha três colunas selecionadas e, em seguida, defina o rácio mínimo de valores em falta para.2 (20%), mas apenas uma coluna, na verdade, tem de 20% de valores em falta. Neste caso, a operação de limpeza aplicaria somente para a coluna com mais de 20% em falta valores. Por conseguinte, as outras colunas seria inalteradas.
    > 
    > Se tiver qualquer dúvida sobre se os valores em falta foram alteradas, selecione a opção **gerar a coluna de indicador de valor em falta**. Uma coluna é acrescentada ao conjunto de dados para indicar se é ou não cada coluna cumpre os critérios especificados para os intervalos mínimos e máximo.  
  
4. Para **máximo em falta a proporção de valor**, especifique o número máximo de valores em falta, que pode estar presente para a operação a ser executada.   
  
    Por exemplo, poderá querer efetuar em falta de valor de substituição apenas se a 30% ou menos uma das linhas contêm valores em falta, mas deixam os valores como-é se mais de 30% das linhas têm valores em falta.  
  
    Definir o número como a proporção de valores em falta para todos os valores na coluna. Por predefinição, o **máximo em falta a proporção de valor** está definido como 1. Isso significa que os valores em falta são limpos, mesmo se 100% dos valores na coluna em falta.  
  
   
  
5. Para **modo de limpeza**, selecione uma das seguintes opções para substituir ou em falta a remover valores:  
  
  
    + **Valor de substituição personalizado**: Utilize esta opção para especificar um valor de marcador de posição (por exemplo, 0 ou ND) que se aplica a todos os valores em falta. O valor que especificar como uma substituição tem de ser compatível com o tipo de dados da coluna.
  
    + **Substitua a média**: Calcula a média de coluna e usa a média como o valor de substituição para cada valor em falta na coluna.  
  
        Aplica-se apenas às colunas que tenham o Integer, Double ou tipos de dados booleano.  
  
    + **Substitua o valor mediano**: Calcula o valor mediano da coluna e, utiliza o valor mediano como a substituição para qualquer valor em falta na coluna.  
  
        Aplica-se apenas às colunas que têm tipos de dados de número inteiro ou duplo. 
  
    + **Substitua com o modo**: Calcula o modo para a coluna e, utiliza o modo como o valor de substituição para cada valor em falta na coluna.  
  
        Aplica-se às colunas que têm tipos de dados Integer, Double, booleano ou Categórico. 
  
    + **Remover linha inteira**: Remove completamente todas as linhas no conjunto de dados que tenha um ou mais valores em falta. Isto é útil se o valor em falta pode ser considerado aleatoriamente em falta.  
  
    + **Remover coluna inteira**: Remove completamente qualquer coluna no conjunto de dados que tenha um ou mais valores em falta.  
  
    
  
6. A opção **valor de substituição** está disponível se tiver selecionado a opção **valor de substituição personalizado**. Escreva um novo valor a utilizar como o valor de substituição para todos os valores em falta na coluna.  
  
    Tenha em atenção que pode utilizar esta opção apenas em colunas que tenham os tipos de dados Integer, Double, booleano ou data. Para colunas de data, o valor de substituição pode também ser inserido como o número de tiques de 100 nanossegundos desde 1/1/0001 12 horas  
  
7. **Gerar a coluna de indicador de valor em falta**: Selecione esta opção se quiser alguma indicação de se os valores na coluna cumpre os critérios para limpar o valor em falta de saída. Esta opção é particularmente útil quando é como configurar uma nova operação de limpeza e deseja para se certificar de que funciona conforme projetado.
  
8. Execute a experimentação.

### <a name="results"></a>Resultados

O módulo retorna duas saídas:  

-   **Conjunto de dados limpo**: Um conjunto de dados composto por colunas selecionadas, com valores em falta é tratada como especificado, juntamente com uma coluna de indicador, se tiver selecionado essa opção.  

    Colunas não selecionadas para a limpeza são também "transmitidas".  
  
-  **Transformação de limpeza**: Transformação de dados utilizada para a limpeza, que pode ser guardada na sua área de trabalho e aplicada aos dados de novo mais tarde.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Aplicar uma operação de limpeza guardada para novos dados  

Se precisar de muitas vezes a repetir operações de limpeza, recomendamos que guarde sua receita de limpeza de dados como um *transformar*, reutilizar com o mesmo conjunto de dados. A guardar uma transformação de limpeza é particularmente útil se tem frequentemente voltar a importar e, em seguida, limpar dados que tem o mesmo esquema.  
      
1.  Adicionar a [transformação de aplicar](./apply-transformation.md) módulo à sua experimentação.  
  
2.  Adicione o conjunto de dados que pretende limpar e ligar o conjunto de dados para a porta de entrada da direita.  
  
3.  Expanda a **transforma** grupo no painel à esquerda da interface. Localize a transformação guardada e arraste-o para a experimentação.  
  
4.  Ligar a transformação guardada para a porta de entrada à esquerda dos [transformação de aplicar](./apply-transformation.md). 

    Quando aplica uma transformação guardada, não é possível selecionar as colunas para o qual são aplicadas a transformação. Isso ocorre porque a transformação já definida e aplica-se automaticamente para as colunas especificadas da operação original.

    No entanto, suponha que criou uma transformação num subconjunto de colunas numéricas. Pode aplicar essa transformação para um conjunto de dados de tipos de coluna misto sem gerar um erro, porque os valores em falta são alterados apenas nas colunas numéricas correspondentes.

6.  Execute a experimentação.  

## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 