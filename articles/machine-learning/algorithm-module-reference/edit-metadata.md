---
title: 'Edite metadados: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de editar metadados no serviço do Azure Machine Learning para alterar os metadados que está associada a colunas num conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 282652adb917450c262e08bf10c3c6e537b829e7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072708"
---
# <a name="edit-metadata-module"></a>Editar o módulo de metadados

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize o módulo de dados de editar para alterar os metadados que está associada a colunas num conjunto de dados. O tipo de dados de valor e do conjunto de dados será alterado após o uso do módulo Editar metadados.

Alterações de metadados típicas podem incluir:
  
+ Tratando booleanas ou numérico colunas como valores categóricos.
  
+ Que indica a coluna que contém o **classe** etiqueta ou contém os valores que pretende categorizar ou prever.
  
+ A marcar colunas como recursos.
  
+ Alterar os valores de data/hora para valores numéricos ou vice-versa.
  
+ Mudar o nome de colunas.
  
 Utilize Editar metadados sempre que necessitar de modificar a definição de uma coluna, normalmente, para satisfazer os requisitos para um módulo de downstream. Por exemplo, alguns módulos trabalhar apenas com tipos de dados específicos ou exigir sinalizadores em colunas, tal como `IsFeature` ou `IsCategorical`.  
  
 Depois de efetuar a operação necessária, pode repor os metadados para o estado original.
  
## <a name="configure-edit-metadata"></a>Configurar Editar metadados
  
1. No Azure Machine Learning, adicione o módulo de editar metadados à sua experimentação e ligue-se o conjunto de dados que pretende atualizar. Pode encontrar o conjunto de dados sob **transformação de dados** no **Manipulate** categoria.
  
1. Selecione **inicie o Seletor de colunas** e escolha a coluna ou o conjunto de colunas para trabalhar com. Pode escolher colunas individualmente por nome ou o índice ou pode escolher um grupo de colunas por tipo.  
  
1. Selecione o **tipo de dados** opção se pretender atribuir um tipo de dados diferentes para as colunas selecionadas. Poderá ter de alterar o tipo de dados em algumas operações. Por exemplo, se o conjunto de dados de origem tiver números tratados como texto, deve alterá-los para um tipo de dados numéricos antes de a utilizar operações matemáticas.

    + Os tipos de dados suportados são **cadeia de caracteres**, **número inteiro**, **duplo**, **booleanos**, e **DateTime**.

    + Se selecionar várias colunas, tem de aplicar as alterações de metadados *todos os* nas colunas selecionadas. Por exemplo, digamos que selecione dois ou três colunas numéricas. Pode alterá-los a uma cadeia de tipo de dados e renomeá-los numa única operação. No entanto, não é possível alterar uma coluna para um tipo de dados de cadeia de caracteres e outra coluna de um número de vírgula flutuante para um número inteiro.
  
    + Se não especificar um novo tipo de dados, os metadados da coluna não são alterado.

    + O tipo de coluna e os valores serão alterados depois de efetuar a operação de editar metadados. Pode recuperar o tipo de dados original em qualquer altura utilizando Editar metadados para repor o tipo de dados de coluna.  

    > [!NOTE]
    > Se alterar qualquer tipo de número para o **DateTime** escreva, deixe a **formato DateTime** valor de campo em branco. Atualmente não é possível especificar o formato de dados de destino.  

1. Selecione o **Categórico** opção para especificar que os valores nas colunas selecionadas devem ser tratados como categorias.

    Por exemplo, pode ter uma coluna que contém os números de 0, 1 e 2, mas sabe que os números realmente "Smoker", "Não-smoker" e "Desconhecido". Nesse caso, ao sinalizar a coluna como categóricos é garantir que os valores são utilizados apenas para agrupar dados e não nos cálculos numéricos.
  
1. Utilize o **campos** opção se pretender alterar a forma como o que o Azure Machine Learning utiliza os dados num modelo.

    + **Funcionalidade**: Utilize esta opção para sinalizar uma coluna como uma funcionalidade em módulos que funcionam apenas em colunas de funcionalidades. Por predefinição, todas as colunas inicialmente são tratadas como recursos.  
  
    + **Etiqueta**: Utilize esta opção para marcar o rótulo, o que é também conhecida como a variável previsível de atributo ou de destino. Muitos módulos exigem que essa coluna exatamente uma etiqueta está presente no conjunto de dados.

        Em muitos casos, o Azure Machine Learning pode inferir que uma coluna contém uma etiqueta de classe. Ao definir estes metadados, pode certificar-se de que a coluna é identificada corretamente. Definir esta opção não altera os valores de dados. Ele é alterado apenas da forma que alguns algoritmos de machine learning lidar com os dados.
  
    > [!TIP]
    > Tem dados que não se encaixa nessas categorias? Por exemplo, o conjunto de dados pode conter valores como identificadores exclusivos que não são úteis como variáveis. Às vezes, esses IDs podem causar problemas quando utilizado num modelo.
    >
    > Felizmente, Azure Machine Learning mantém todos os seus dados, para que não precisa de eliminar este tipo de colunas do conjunto de dados. Quando tiver de realizar operações num conjunto especial de colunas, apenas remover todas as outras colunas temporariamente com o [Select Columns in Dataset](select-columns-in-dataset.md) módulo. Mais tarde possível intercalar as colunas de volta para o conjunto de dados utilizando o [adicionar colunas](add-columns.md) módulo.  
  
1. Utilize as seguintes opções para limpar seleções anteriores e restaurar metadados para os valores predefinidos.  
  
    + **Funcionalidade clara**: Utilize esta opção para remover o sinalizador de funcionalidade.  
  
         Inicialmente, todas as colunas são tratadas como recursos. Para os módulos que realizam operações matemáticas, poderá ter de utilizar esta opção para impedir que está a ser tratado como variáveis de colunas numéricas.
  
    + **Etiqueta clara**: Utilize esta opção para remover o **etiqueta** metadados a partir da coluna especificada.  
  
    + **Desmarque a classificação**: Utilize esta opção para remover o **pontuação** metadados a partir da coluna especificada.  
  
         Não é atualmente possível explicitamente marcar uma coluna como uma pontuação no Azure Machine Learning. No entanto, algumas operações resultam numa coluna a ser sinalizada como uma pontuação internamente. Além disso, um módulo R personalizado poderá saída valores de pontuação.

1. Para **novos nomes das colunas**, introduza o novo nome da coluna selecionada ou colunas.  
  
    + Os nomes das colunas, podem utilizar apenas os carateres que são suportados pelo UTF-8 codificação. Cadeias de caracteres vazias, nulos ou nomes consistem inteiramente de espaços não são permitidos.  
  
    + Para mudar o nome de várias colunas, introduza os nomes como uma lista separada por vírgulas, por ordem de índices de colunas.  
  
    + Tem de ser mudar o nome de todas as colunas selecionadas. Não é possível omitir ou ignorar colunas.  
  
1. Execute a experimentação.  

## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) para o serviço Azure Machine Learning.
