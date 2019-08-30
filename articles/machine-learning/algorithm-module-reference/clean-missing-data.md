---
title: 'Limpar dados ausentes: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo limpar dados ausentes no serviço de Azure Machine Learning para remover, substituir ou inferir valores ausentes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: a65e8224b00bb592d6e0e42abdd304cf325d4412
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128941"
---
# <a name="clean-missing-data-module"></a>Limpar módulo de dados ausentes

Este artigo descreve um módulo da interface visual (visualização) para Azure Machine Learning serviço.

Use este módulo para remover, substituir ou inferir valores ausentes. 

Os cientistas de dados geralmente verificam os dados em busca de valores ausentes e, em seguida, executam várias operações para corrigir os dados ou inserir novos valores. O objetivo dessas operações de limpeza é evitar problemas causados por dados ausentes que podem surgir durante o treinamento de um modelo. 

Este módulo dá suporte a vários tipos de operações para "limpeza" de valores ausentes, incluindo:

+ Substituindo valores ausentes por um espaço reservado, média ou outro valor
+ Removendo completamente linhas e colunas com valores ausentes
+ Inferindo valores com base em métodos estatísticos


O uso desse módulo não altera o conjunto de fonte de origem. Em vez disso, ele cria um novo conjunto de novos conjuntos de espaço de trabalho que você pode usar no Workflow subsequente. Você também pode salvar o conjunto de um novo e limpo para reutilização.

Esse módulo também gera uma definição da transformação usada para limpar os valores ausentes. Você pode usar novamente essa transformação em outros conjuntos de valores que têm o mesmo esquema, usando o módulo [aplicar transformação](./apply-transformation.md) .  

## <a name="how-to-use-clean-missing-data"></a>Como usar dados ausentes limpos

Esse módulo permite que você defina uma operação de limpeza. Você também pode salvar a operação de limpeza para que possa aplicá-la posteriormente aos novos dados. Consulte os links a seguir para obter uma descrição de como criar e salvar um processo de limpeza: 
 
+ Para substituir valores ausentes
  
+ Para aplicar uma transformação limpeza a novos dados
 
> [!IMPORTANT]
> O método de limpeza que você usa para lidar com valores ausentes pode afetar drasticamente os resultados. Recomendamos que você experimente métodos diferentes. Considere a justificativa para uso de um método específico e a qualidade dos resultados.

### <a name="replace-missing-values"></a>Substituir valores ausentes  

Cada vez que você aplicar o módulo [limpar dados ausentes](./clean-missing-data.md) a um conjunto de dados, a mesma operação de limpeza será aplicada a todas as colunas que você selecionar. Portanto, se você precisar limpar colunas diferentes usando métodos diferentes, use instâncias separadas do módulo.

1.  Adicione o módulo [limpar dados ausentes](./clean-missing-data.md) ao seu experimento e conecte-o com valores ausentes.  
  
2.  Para as **colunas a serem limpas**, escolha as colunas que contêm os valores ausentes que você deseja alterar. Você pode escolher várias colunas, mas deve usar o mesmo método de substituição em todas as colunas selecionadas. Portanto, normalmente, você precisa limpar colunas de cadeia de caracteres e colunas numéricas separadamente.

    Por exemplo, para verificar se há valores ausentes em todas as colunas numéricas:

    1. Abra o seletor de coluna e selecione **com regras**.
    2. Para **começar com**, selecione **nenhuma coluna**.

        Você também pode iniciar com todas as colunas e, em seguida, excluir colunas. Inicialmente, as regras não são mostradas se você clicar primeiro em **todas as colunas**, mas você pode clicar em **nenhuma coluna** e, em seguida, clicar em **todas as colunas** novamente para iniciar com todas as colunas e, em seguida, filtrar (excluir) colunas com base no índice de nome, tipo de dados ou colunas.

    3. Para **incluir**, selecione **tipo de coluna** na lista suspensa e, em seguida, selecione **numérico**ou um tipo numérico mais específico. 
  
    Qualquer método de limpeza ou substituição que você escolher deve ser aplicável a **todas as** colunas na seleção. Se os dados em qualquer coluna forem incompatíveis com a operação especificada, o módulo retornará um erro e interromperá o experimento.
  
3.  Para a **proporção mínima de valor ausente**, especifique o número mínimo de valores ausentes necessários para a operação a ser executada.  
  
    Use essa opção em combinação com a **taxa máxima de valor ausente** para definir as condições sob as quais uma operação de limpeza é executada no conjunto de valores. Se houver excesso ou poucas linhas que não tenham valores, a operação não poderá ser executada. 
  
    O número inserido representa a **taxa** de valores ausentes para todos os valores na coluna. Por padrão, a propriedade **taxa mínima de valor ausente** está definida como 0. Isso significa que os valores ausentes são limpos mesmo se houver apenas um valor ausente. 

    > [!WARNING]
    > Essa condição deve ser atendida por cada coluna para que a operação especificada seja aplicada. Por exemplo, suponha que você selecionou três colunas e, em seguida, defina a taxa mínima de valores ausentes como 0,2 (20%), mas apenas uma coluna tem, na verdade, 20% de valores ausentes. Nesse caso, a operação de limpeza se aplicaria somente à coluna com mais de 20% de valores ausentes. Portanto, as outras colunas ficarão inalteradas.
    > 
    > Se você tiver alguma dúvida sobre se os valores ausentes foram alterados, selecione a opção **gerar coluna de indicador de valor ausente**. Uma coluna é anexada ao DataSet para indicar se cada coluna atende aos critérios especificados para os intervalos mínimo e máximo.  
  
4. Para obter a **taxa máxima**de valores ausentes, especifique o número máximo de valores ausentes que podem estar presentes para a operação ser executada.   
  
    Por exemplo, talvez você queira executar a substituição de valor ausente somente se 30% ou menos das linhas contiverem valores ausentes, mas deixar os valores como estão se mais de 30% das linhas tiverem valores ausentes.  
  
    Você define o número como a taxa de valores ausentes para todos os valores na coluna. Por padrão, a **taxa máxima de valores ausentes** é definida como 1. Isso significa que os valores ausentes serão limpos mesmo se 100% dos valores na coluna estiverem ausentes.  
  
   
  
5. Para o **modo de limpeza**, selecione uma das seguintes opções para substituir ou remover valores ausentes:  
  
  
    + **Valor personalizado de substituição**: Use esta opção para especificar um valor de espaço reservado (como 0 ou NA) que se aplica a todos os valores ausentes. O valor que você especifica como uma substituição deve ser compatível com o tipo de dados da coluna.
  
    + **Substituir por média**: Calcula a média da coluna e usa a média como o valor de substituição para cada valor ausente na coluna.  
  
        Aplica-se somente a colunas que têm tipos de dados inteiros, duplos ou boolianos.  
  
    + **Substituir por mediana**: Calcula o valor mediano da coluna e usa o valor mediano como a substituição para qualquer valor ausente na coluna.  
  
        Aplica-se somente a colunas que têm tipos de dados inteiros ou duplos. 
  
    + **Substituir pelo modo**: Calcula o modo da coluna e usa o modo como o valor de substituição para cada valor ausente na coluna.  
  
        Aplica-se a colunas que têm tipos de dados inteiros, duplos, boolianos ou categóricos. 
  
    + **Remover linha inteira**: Remove completamente qualquer linha no conjunto de informações que tenha um ou mais valores ausentes. Isso será útil se o valor ausente puder ser considerado aleatoriamente ausente.  
  
    + **Remover coluna inteira**: Remove completamente qualquer coluna no conjunto de informações que tenha um ou mais valores ausentes.  
  
    
  
6. O **valor de substituição** de opção estará disponível se você tiver selecionado a opção **valor personalizado de substituição**. Digite um novo valor a ser usado como o valor de substituição para todos os valores ausentes na coluna.  
  
    Observe que você pode usar essa opção somente em colunas que têm os tipos de dados inteiro, duplo, booliano ou data. Para colunas de data, o valor de substituição também pode ser inserido como o número de tiques de 100 nanossegundos desde 1/1/0001 12:00 A.M.  
  
7. **Gerar coluna de indicador de valor ausente**: Selecione esta opção se desejar gerar uma indicação de se os valores na coluna atendem aos critérios de limpeza de valor ausente. Essa opção é particularmente útil quando você está configurando uma nova operação de limpeza e deseja certificar-se de que ela funciona como projetado.
  
8. Execute a experimentação.

### <a name="results"></a>Resultados

O módulo retorna duas saídas:  

-   **Conjunto**de uma limpeza: Um DataSet composto pelas colunas selecionadas, com valores ausentes tratados como especificado, juntamente com uma coluna de indicador, se você tiver selecionado essa opção.  

    As colunas não selecionadas para limpeza também são "passadas".  
  
-  **Limpando a transformação**: Uma transformação de dados usada para limpeza, que pode ser salva em seu espaço de trabalho e aplicada a novos dados posteriormente.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Aplicar uma operação de limpeza salva a novos dados  

Se você precisar repetir as operações de limpeza com frequência, recomendamos que você salve sua receita para a limpeza de dados como uma *transformação*, para reutilizar com o mesmo DataSet. Salvar uma transformação de limpeza é particularmente útil se você deve reimportar com frequência e limpar dados que têm o mesmo esquema.  
      
1.  Adicione o módulo [aplicar transformação](./apply-transformation.md) ao seu experimento.  
  
2.  Adicione o DataSet que você deseja limpar e conecte o conjunto de dados à porta de entrada à direita.  
  
3.  Expanda o grupo transformações no painel esquerdo da interface. Localize a transformação salva e arraste-a para o experimento.  
  
4.  Conecte a transformação salva à porta de entrada à esquerda de [aplicar transformação](./apply-transformation.md). 

    Ao aplicar uma transformação salva, não é possível selecionar as colunas às quais a transformação é aplicada. Isso ocorre porque a transformação já foi definida e se aplica automaticamente às colunas especificadas na operação original.

    No entanto, suponha que você criou uma transformação em um subconjunto de colunas numéricas. Você pode aplicar essa transformação a um conjunto de uma série de tipos de coluna misto sem gerar um erro, pois os valores ausentes são alterados somente nas colunas numéricas correspondentes.

6.  Execute a experimentação.  

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 