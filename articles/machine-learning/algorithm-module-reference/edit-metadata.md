---
title: 'Editar Metadados: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo editar metadados no Azure Machine Learning para alterar metadados associados a colunas num conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/10/2020
ms.openlocfilehash: e279a7c9f6810ece482b043284f0be0719a3dafe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "90908044"
---
# <a name="edit-metadata-module"></a>Editar módulo de metadados

Este artigo descreve um módulo incluído no designer de Aprendizagem automática Azure.

Utilize o módulo editar metadados para alterar metadados associados a colunas num conjunto de dados. O valor e o tipo de dados do conjunto de dados serão alterados após a utilização do módulo de metadados de edição.

As mudanças típicas de metadados podem incluir:
  
+ Tratar as colunas Boolean ou numéricas como valores categóricos.
  
+ Indicando qual a coluna que contém o rótulo **de classe** ou contém os valores que pretende categorizar ou prever.
  
+ A marcar colunas como características.
  
+ Alterar valores de data/hora para valores numéricos ou vice-versa.
  
+ Renomear colunas.
  
 Utilize Metadados de Edição sempre que precisar de modificar a definição de uma coluna, normalmente para satisfazer os requisitos de um módulo a jusante. Por exemplo, alguns módulos funcionam apenas com tipos de dados específicos ou exigem bandeiras nas colunas, tais como `IsFeature` ou `IsCategorical` .  
  
 Depois de efetuar a operação necessária, pode redefinir os metadados para o seu estado original.
  
## <a name="configure-edit-metadata"></a>Configure Editar Metadados
  
1. No designer Azure Machine Learning, adicione o módulo editar metadados ao seu pipeline e ligue o conjunto de dados que pretende atualizar. Pode encontrar o módulo na categoria **De Transformação de Dados.**
  
1. Clique na **coluna Editar** no painel direito do módulo e escolha a coluna ou conjunto de colunas para trabalhar. Pode escolher colunas individualmente por nome ou índice, ou pode escolher um grupo de colunas por tipo.  
  
1. Selecione a opção **tipo Dado** se precisar de atribuir um tipo de dado diferente às colunas selecionadas. Pode ser necessário alterar o tipo de dados para determinadas operações. Por exemplo, se o seu conjunto de dados de origem tiver números tratados como texto, deve alterá-los para um tipo de dados numérico antes de utilizar as operações de matemática.

    + Os tipos de dados suportados são **String**, **Integer**, **Double,** **Boolean** e **DateTime**.

    + Se selecionar várias colunas, deve aplicar as alterações de metadados em *todas as* colunas selecionadas. Por exemplo, digamos que escolha duas ou três colunas numéricas. Pode alterá-los todos para um tipo de dados de cadeia e renomeá-los numa única operação. No entanto, não é possível alterar uma coluna para um tipo de dados de corda e outra coluna de um flutuador para um inteiro.
  
    + Se não especificar um novo tipo de dados, os metadados da coluna são inalterados.

    + O tipo e os valores da coluna serão alterados após a realização da operação editar metadados. Pode recuperar o tipo de dados original a qualquer momento utilizando metadados de edição para redefinir o tipo de dados da coluna.  

    > [!NOTE]
    > O **formato DateTime** segue [o formato de datação incorporada python](https://docs.python.org/3/library/datetime.html#strftime-and-strptime-behavior).  
    > Se alterar qualquer tipo de número para o tipo **DateTime,** deixe o campo **de formato DateTime** em branco. Atualmente não é possível especificar o formato de dados-alvo.

1. Selecione a opção **Categórica** para especificar que os valores nas colunas selecionadas devem ser tratados como categorias.

    Por exemplo, pode ter uma coluna que contém os números 0, 1 e 2, mas saiba que os números realmente significam "Fumador", "Não fumador" e "Desconhecido". Nesse caso, assinalando a coluna como categórica, assegura-se que os valores são utilizados apenas para agrupar dados e não em cálculos numéricos.
  
1. Utilize a opção **Campos** se quiser alterar a forma como o Azure Machine Learning utiliza os dados num modelo.

    + **Característica**: Utilize esta opção para sinalizar uma coluna como uma característica em módulos que operam apenas em colunas de características. Por padrão, todas as colunas são inicialmente tratadas como funcionalidades.  
  
    + **Etiqueta**: Utilize esta opção para marcar o rótulo, que também é conhecido como o atributo previsível ou variável alvo. Muitos módulos requerem que exatamente uma coluna de etiquetas esteja presente no conjunto de dados.

        Em muitos casos, a Azure Machine Learning pode inferir que uma coluna contém um rótulo de classe. Ao configurar estes metadados, pode certificar-se de que a coluna está corretamente identificada. Definir esta opção não altera os valores de dados. Muda apenas a forma como alguns algoritmos de aprendizagem automática tratam os dados.
  
    > [!TIP]
    > Tem dados que não se enquadram nestas categorias? Por exemplo, o seu conjunto de dados pode conter valores como identificadores únicos que não são úteis como variáveis. Por vezes, tais IDs podem causar problemas quando usados num modelo.
    >
    > Felizmente, o Azure Machine Learning guarda todos os seus dados, para que não tenha de eliminar tais colunas do conjunto de dados. Quando necessitar de efetuar operações em algum conjunto especial de colunas, basta remover todas as outras colunas temporariamente utilizando as [Colunas Selecionadas no módulo Dataset.](select-columns-in-dataset.md) Mais tarde, pode fundir as colunas de volta ao conjunto de dados utilizando o módulo [Add Columns.](add-columns.md)  
  
1. Utilize as seguintes opções para limpar as seleções anteriores e restaurar metadados nos valores predefinidos.  
  
    + **Função clara**: Utilize esta opção para remover a bandeira de recurso.  
  
         Todas as colunas são inicialmente tratadas como características. Para módulos que realizam operações matemáticas, poderá ser necessário utilizar esta opção para evitar que as colunas numéricas sejam tratadas como variáveis.
  
    + **Etiqueta clara**: Utilize esta opção para remover os metadados de **etiqueta** da coluna especificada.  
  
    + **Pontuação clara**: Utilize esta opção para remover os metadados de **pontuação** da coluna especificada.  
  
         Atualmente, não é possível marcar explicitamente uma coluna como uma partitura no Azure Machine Learning. No entanto, algumas operações resultam em que uma coluna seja sinalizada como uma pontuação interna. Além disso, um módulo R personalizado pode obter valores de pontuação.

1. Para **novos nomes de colunas,** introduza o novo nome da coluna ou colunas selecionadas.  
  
    + Os nomes das colunas podem usar apenas caracteres que são suportados pela codificação UTF-8. Não são permitidas cordas vazias, nulos ou nomes que consistem inteiramente em espaços.  
  
    + Para renomear várias colunas, insira os nomes como uma lista separada por vírgula por ordem dos índices de coluna.  
  
    + Todas as colunas selecionadas devem ser renomeadas. Não pode omitir ou saltar colunas.  
  
1. Envie o oleoduto.  

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning.
