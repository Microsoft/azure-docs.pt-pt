---
title: 'Editar metadados: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo editar metadados no serviço de Azure Machine Learning para alterar os metadados associados às colunas em um conjunto de informações.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 642b2a038ec434584c8af6dd72d58810e136ed57
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128871"
---
# <a name="edit-metadata-module"></a>Editar módulo de metadados

Este artigo descreve um módulo da interface visual (visualização) para o serviço Azure Machine Learning.

Use o módulo editar dados para alterar os metadados que estão associados a colunas em um DataSet. O valor e o tipo de dados do DataSet serão alterados após o uso do módulo editar metadados.

As alterações de metadados típicas podem incluir:
  
+ Tratando colunas booleanas ou numéricas como valores categóricos.
  
+ Indicando qual coluna contém o rótulo de **classe** ou contém os valores que você deseja categorizar ou prever.
  
+ Marcando colunas como recursos.
  
+ Alterar valores de data/hora para valores numéricos ou vice-versa.
  
+ Renomeando colunas.
  
 Use editar metadados sempre que você precisar modificar a definição de uma coluna, normalmente para atender aos requisitos de um módulo downstream. Por exemplo, alguns módulos funcionam apenas com tipos de dados específicos ou exigem sinalizadores nas colunas, `IsFeature` como ou. `IsCategorical`  
  
 Depois de executar a operação necessária, você pode redefinir os metadados para seu estado original.
  
## <a name="configure-edit-metadata"></a>Configurar editar metadados
  
1. No Azure Machine Learning, adicione o módulo editar metadados ao seu experimento e conecte o conjunto de um que você deseja atualizar. Você pode encontrar o conjunto de **dados em data** Transformation na categoria **manipular** .
  
1. Selecione **iniciar o seletor de coluna** e escolha a coluna ou conjunto de colunas com a qual trabalhar. Você pode escolher colunas individualmente por nome ou índice, ou pode escolher um grupo de colunas por tipo.  
  
1. Selecione a opção **tipo de dados** se você precisar atribuir um tipo de dados diferente às colunas selecionadas. Talvez seja necessário alterar o tipo de dados para determinadas operações. Por exemplo, se o conjunto de dados de origem tiver números tratados como texto, você deverá alterá-los para um tipo de dado numérico antes de usar operações matemáticas.

    + Os tipos de dados com suporte são **cadeia de caracteres**, **inteiro**, **duplo**, **booliano**e **DateTime**.

    + Se você selecionar várias colunas, deverá aplicar as alterações de metadados a *todas as* colunas selecionadas. Por exemplo, digamos que você escolha duas ou três colunas numéricas. Você pode alterá-los para um tipo de dados de cadeia de caracteres e renomeá-los em uma única operação. No entanto, você não pode alterar uma coluna para um tipo de dados de cadeia de caracteres e outra coluna de um float para um inteiro.
  
    + Se você não especificar um novo tipo de dados, os metadados da coluna ficarão inalterados.

    + O tipo de coluna e os valores serão alterados depois que você executar a operação de edição de metadados. Você pode recuperar o tipo de dados original a qualquer momento usando editar metadados para redefinir o tipo de dados da coluna.  

    > [!NOTE]
    > Se você alterar qualquer tipo de número para o tipo **DateTime** , deixe o campo de **formato DateTime** em branco. Atualmente, não é possível especificar o formato de dados de destino.  

1. Selecione a opção **categórica** para especificar que os valores nas colunas selecionadas devem ser tratados como categorias.

    Por exemplo, você pode ter uma coluna que contém os números 0, 1 e 2, mas sabe que os números realmente significam "fumaça", "não-fumaça" e "desconhecido". Nesse caso, sinalizando a coluna como categórica você garante que os valores sejam usados apenas para agrupar dados e não em cálculos numéricos.
  
1. Use a opção **campos** se desejar alterar a maneira como Azure Machine Learning usa os dados em um modelo.

    + **Recurso**: Use esta opção para sinalizar uma coluna como um recurso em módulos que operam somente em colunas de recursos. Por padrão, todas as colunas são tratadas inicialmente como recursos.  
  
    + **Rótulo**: Use esta opção para marcar o rótulo, que também é conhecido como atributo previsível ou variável de destino. Muitos módulos exigem que exatamente uma coluna de rótulo esteja presente no DataSet.

        Em muitos casos, Azure Machine Learning pode inferir que uma coluna contém um rótulo de classe. Ao definir esses metadados, você pode garantir que a coluna seja identificada corretamente. Definir essa opção não altera os valores de dados. Ele altera apenas a maneira como alguns algoritmos de aprendizado de máquina manipulam os dados.
  
    > [!TIP]
    > Você tem dados que não se ajustam a essas categorias? Por exemplo, seu conjunto de seus conjuntos de seus pode conter valores como identificadores exclusivos que não são úteis como variáveis. Às vezes, essas IDs podem causar problemas quando usadas em um modelo.
    >
    > Felizmente, Azure Machine Learning mantém todos os seus dados, de modo que você não precisa excluir essas colunas do DataSet. Quando você precisa executar operações em um conjunto especial de colunas, basta remover todas as outras colunas temporariamente usando o módulo [selecionar colunas no conjunto](select-columns-in-dataset.md) de módulos. Posteriormente, você pode mesclar as colunas de volta para o conjunto de um usando o módulo [adicionar colunas](add-columns.md) .  
  
1. Use as opções a seguir para limpar as seleções anteriores e restaurar os metadados para os valores padrão.  
  
    + **Limpar recurso**: Use esta opção para remover o sinalizador de recurso.  
  
         Todas as colunas são tratadas inicialmente como recursos. Para módulos que executam operações matemáticas, talvez seja necessário usar essa opção para impedir que colunas numéricas sejam tratadas como variáveis.
  
    + **Limpar rótulo**: Use esta opção para remover os metadados do **rótulo** da coluna especificada.  
  
    + **Limpar Pontuação**: Use esta opção para remover os metadados de **Pontuação** da coluna especificada.  
  
         Atualmente, você não pode marcar uma coluna explicitamente como uma pontuação em Azure Machine Learning. No entanto, algumas operações resultam em uma coluna sendo sinalizada como uma pontuação internamente. Além disso, um módulo R personalizado pode gerar valores de pontuação.

1. Para **novos nomes de coluna**, insira o novo nome da coluna ou das colunas selecionadas.  
  
    + Os nomes de coluna podem usar apenas caracteres com suporte na codificação UTF-8. Cadeias de caracteres vazias, nulos ou nomes que consistem inteiramente de espaços não são permitidos.  
  
    + Para renomear várias colunas, insira os nomes como uma lista separada por vírgulas na ordem dos índices de coluna.  
  
    + Todas as colunas selecionadas devem ser renomeadas. Você não pode omitir ou ignorar colunas.  
  
1. Execute a experimentação.  

## <a name="next-steps"></a>Passos Seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o serviço de Azure Machine Learning.
