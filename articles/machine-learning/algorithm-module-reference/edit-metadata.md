---
title: 'Editar metadados: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo editar metadados no Azure Machine Learning para alterar os metadados associados às colunas em um conjunto de informações.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 57a211c6ffab9a960c013008410f4b52b909d360
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137514"
---
# <a name="edit-metadata-module"></a>Editar módulo de metadados

Este artigo descreve um módulo incluído no designer de Machine Learning Azure (pré-visualização).

Use o módulo editar dados para alterar os metadados que estão associados a colunas em um DataSet. O valor e o tipo de dados do DataSet serão alterados após o uso do módulo editar metadados.

As alterações de metadados típicas podem incluir:
  
+ Tratando colunas booleanas ou numéricas como valores categóricos.
  
+ Indicando qual a coluna que contém a etiqueta de **classe** ou contém os valores que pretende categorizar ou prever.
  
+ Marcando colunas como recursos.
  
+ Alterar valores de data/hora para valores numéricos ou vice-versa.
  
+ Renomeando colunas.
  
 Use editar metadados sempre que você precisar modificar a definição de uma coluna, normalmente para atender aos requisitos de um módulo downstream. Por exemplo, alguns módulos funcionam apenas com tipos de dados específicos ou requerem bandeiras nas colunas, tais como `IsFeature` ou `IsCategorical`.  
  
 Depois de executar a operação necessária, você pode redefinir os metadados para seu estado original.
  
## <a name="configure-edit-metadata"></a>Configurar editar metadados
  
1. No designer de Machine Learning Azure, adicione o módulo Demetação editar ao seu pipeline e ligue o conjunto de dados que pretende atualizar. Pode encontrar o módulo na categoria **de Transformação** de Dados.
  
1. Clique na **coluna Editar** no painel direito do módulo e escolha a coluna ou o conjunto de colunas para trabalhar. Você pode escolher colunas individualmente por nome ou índice, ou pode escolher um grupo de colunas por tipo.  
  
1. Selecione a opção **Tipo Data** se precisar de atribuir um tipo de dados diferente às colunas selecionadas. Talvez seja necessário alterar o tipo de dados para determinadas operações. Por exemplo, se o conjunto de dados de origem tiver números tratados como texto, você deverá alterá-los para um tipo de dado numérico antes de usar operações matemáticas.

    + Os tipos de dados suportados são **String**, **Integer,** **Double,** **Boolean**e **DateTime**.

    + Se selecionar várias colunas, tem de aplicar as alterações dos metadados em *todas as* colunas selecionadas. Por exemplo, digamos que você escolha duas ou três colunas numéricas. Você pode alterá-los para um tipo de dados de cadeia de caracteres e renomeá-los em uma única operação. No entanto, você não pode alterar uma coluna para um tipo de dados de cadeia de caracteres e outra coluna de um float para um inteiro.
  
    + Se você não especificar um novo tipo de dados, os metadados da coluna ficarão inalterados.

    + O tipo de coluna e os valores serão alterados depois que você executar a operação de edição de metadados. Você pode recuperar o tipo de dados original a qualquer momento usando editar metadados para redefinir o tipo de dados da coluna.  

    > [!NOTE]
    > Se alterar qualquer tipo de número para o tipo **DateTime,** deixe o campo **formato DateTime** em branco. Atualmente, não é possível especificar o formato de dados de destino.  

1. Selecione a opção **Categórica** para especificar que os valores nas colunas selecionadas devem ser tratados como categorias.

    Por exemplo, você pode ter uma coluna que contém os números 0, 1 e 2, mas sabe que os números realmente significam "fumaça", "não-fumaça" e "desconhecido". Nesse caso, sinalizando a coluna como categórica você garante que os valores sejam usados apenas para agrupar dados e não em cálculos numéricos.
  
1. Utilize a opção **Fields** se quiser alterar a forma como o Azure Machine Learning utiliza os dados num modelo.

    + **Função**: Utilize esta opção para sinalizar uma coluna como uma característica em módulos que funcionam apenas em colunas de características. Por padrão, todas as colunas são tratadas inicialmente como recursos.  
  
    + **Etiqueta**: Utilize esta opção para marcar o rótulo, que também é conhecido como o atributo previsível ou variável-alvo. Muitos módulos exigem que exatamente uma coluna de rótulo esteja presente no DataSet.

        Em muitos casos, Azure Machine Learning pode inferir que uma coluna contém um rótulo de classe. Ao definir esses metadados, você pode garantir que a coluna seja identificada corretamente. Definir essa opção não altera os valores de dados. Ele altera apenas a maneira como alguns algoritmos de aprendizado de máquina manipulam os dados.
  
    > [!TIP]
    > Você tem dados que não se ajustam a essas categorias? Por exemplo, seu conjunto de seus conjuntos de seus pode conter valores como identificadores exclusivos que não são úteis como variáveis. Às vezes, essas IDs podem causar problemas quando usadas em um modelo.
    >
    > Felizmente, Azure Machine Learning mantém todos os seus dados, de modo que você não precisa excluir essas colunas do DataSet. Quando necessitar de efetuar operações num conjunto especial de colunas, basta remover todas as outras colunas temporariamente utilizando as Colunas Select no módulo [Dataset.](select-columns-in-dataset.md) Mais tarde, pode voltar a fundir as colunas no conjunto de dados utilizando o módulo [Adicionar Colunas.](add-columns.md)  
  
1. Use as opções a seguir para limpar as seleções anteriores e restaurar os metadados para os valores padrão.  
  
    + **Função clara**: Utilize esta opção para remover a bandeira da função.  
  
         Todas as colunas são tratadas inicialmente como recursos. Para módulos que executam operações matemáticas, talvez seja necessário usar essa opção para impedir que colunas numéricas sejam tratadas como variáveis.
  
    + **Etiqueta clara**: Utilize esta opção para remover os metadados da **etiqueta** da coluna especificada.  
  
    + **Pontuação clara**: Utilize esta opção para remover os metadados de **pontuação** da coluna especificada.  
  
         Atualmente, você não pode marcar uma coluna explicitamente como uma pontuação em Azure Machine Learning. No entanto, algumas operações resultam em uma coluna sendo sinalizada como uma pontuação internamente. Além disso, um módulo R personalizado pode gerar valores de pontuação.

1. Para **novos nomes**de colunas, introduza o novo nome da coluna ou colunas selecionadas.  
  
    + Os nomes de coluna podem usar apenas caracteres com suporte na codificação UTF-8. Cadeias de caracteres vazias, nulos ou nomes que consistem inteiramente de espaços não são permitidos.  
  
    + Para renomear várias colunas, insira os nomes como uma lista separada por vírgulas na ordem dos índices de coluna.  
  
    + Todas as colunas selecionadas devem ser renomeadas. Você não pode omitir ou ignorar colunas.  
  
1. Executar o pipeline.  

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning.
