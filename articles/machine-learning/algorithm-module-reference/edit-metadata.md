---
title: 'Editar Metadados: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de metadados editar no Azure Machine Learning para alterar metadados associados a colunas num conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 9853a3decc8d145fee58d1da526926e224ee2030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064248"
---
# <a name="edit-metadata-module"></a>Editar módulo de metadados

Este artigo descreve um módulo incluído no designer de Machine Learning Azure (pré-visualização).

Utilize o módulo Deedição de Metadados para alterar metadados associados a colunas num conjunto de dados. O valor e o tipo de dados do conjunto de dados mudarão após a utilização do módulo de Metadados editar.

As alterações típicas dos metadados podem incluir:
  
+ Tratar colunas booleanas ou numéricas como valores categóricos.
  
+ Indicando qual a coluna que contém a etiqueta de **classe** ou contém os valores que pretende categorizar ou prever.
  
+ Marcando colunas como características.
  
+ Alterar valores de data/hora para valores numéricos ou vice-versa.
  
+ Renomear colunas.
  
 Utilize metadados de edição sempre que precisar modificar a definição de coluna, normalmente para satisfazer os requisitos para um módulo a jusante. Por exemplo, alguns módulos funcionam apenas com tipos de `IsFeature` dados `IsCategorical`específicos ou requerem bandeiras nas colunas, tais como ou .  
  
 Depois de executar a operação necessária, pode redefinir os metadados para o seu estado original.
  
## <a name="configure-edit-metadata"></a>Configurar metadados de edição
  
1. No designer de Machine Learning Azure, adicione o módulo Demetação editar ao seu pipeline e ligue o conjunto de dados que pretende atualizar. Pode encontrar o módulo na categoria **de Transformação** de Dados.
  
1. Clique na **coluna Editar** no painel direito do módulo e escolha a coluna ou o conjunto de colunas para trabalhar. Pode escolher colunas individualmente pelo nome ou pelo índice, ou pode escolher um grupo de colunas por tipo.  
  
1. Selecione a opção **Tipo Data** se precisar de atribuir um tipo de dados diferente às colunas selecionadas. Pode ser necessário alterar o tipo de dados para determinadas operações. Por exemplo, se o seu conjunto de dados de origem tiver números tratados como texto, deve alterá-los para um tipo de dados numéricos antes de utilizar operações matemáticas.

    + Os tipos de dados suportados são **String**, **Integer,** **Double,** **Boolean**e **DateTime**.

    + Se selecionar várias colunas, tem de aplicar as alterações dos metadados em *todas as* colunas selecionadas. Por exemplo, digamos que escolhes duas ou três colunas numéricas. Pode mudá-los todos para um tipo de dados de cadeia e renomeá-los numa operação. No entanto, não se pode mudar uma coluna para um tipo de dados de cordas e outra coluna de um flutuador para um inteiro.
  
    + Se não especificar um novo tipo de dados, os metadados da coluna permanecem inalterados.

    + O tipo e os valores da coluna mudarão após a realização da operação Editar Metadados. Pode recuperar o tipo de dados original a qualquer momento utilizando metadados de edição para redefinir o tipo de dados da coluna.  

    > [!NOTE]
    > Se alterar qualquer tipo de número para o tipo **DateTime,** deixe o campo **formato DateTime** em branco. Atualmente não é possível especificar o formato de dados-alvo.  

1. Selecione a opção **Categórica** para especificar que os valores nas colunas selecionadas devem ser tratados como categorias.

    Por exemplo, pode ter uma coluna que contém os números 0, 1 e 2, mas saiba que os números realmente significam "Fumador", "Não fumador" e "Desconhecido". Nesse caso, ao sinalizar a coluna como categórica, certifique-se de que os valores são utilizados apenas para agrupar dados e não em cálculos numéricos.
  
1. Utilize a opção **Fields** se quiser alterar a forma como o Azure Machine Learning utiliza os dados num modelo.

    + **Função**: Utilize esta opção para sinalizar uma coluna como uma característica em módulos que funcionam apenas em colunas de características. Por padrão, todas as colunas são inicialmente tratadas como características.  
  
    + **Etiqueta**: Utilize esta opção para marcar o rótulo, que também é conhecido como o atributo previsível ou variável-alvo. Muitos módulos requerem que exatamente uma coluna de etiqueta está presente no conjunto de dados.

        Em muitos casos, o Azure Machine Learning pode inferir que uma coluna contém uma etiqueta de classe. Ao definir estes metadados, pode certificar-se de que a coluna está corretamente identificada. Definir esta opção não altera os valores de dados. Muda apenas a forma como alguns algoritmos de aprendizagem automática lidam com os dados.
  
    > [!TIP]
    > Tem dados que não se enquadram nestas categorias? Por exemplo, o seu conjunto de dados pode conter valores como identificadores únicos que não são úteis como variáveis. Às vezes, tais identificações podem causar problemas quando usados num modelo.
    >
    > Felizmente, o Azure Machine Learning mantém todos os seus dados, para que não tenha de apagar essas colunas do conjunto de dados. Quando necessitar de efetuar operações num conjunto especial de colunas, basta remover todas as outras colunas temporariamente utilizando as Colunas Select no módulo [Dataset.](select-columns-in-dataset.md) Mais tarde, pode voltar a fundir as colunas no conjunto de dados utilizando o módulo [Adicionar Colunas.](add-columns.md)  
  
1. Utilize as seguintes opções para limpar as seleções anteriores e restaurar os metadados nos valores predefinidos.  
  
    + **Função clara**: Utilize esta opção para remover a bandeira da função.  
  
         Todas as colunas são inicialmente tratadas como características. Para módulos que realizam operações matemáticas, poderá ser necessário utilizar esta opção para evitar que colunas numéricas sejam tratadas como variáveis.
  
    + **Etiqueta clara**: Utilize esta opção para remover os metadados da **etiqueta** da coluna especificada.  
  
    + **Pontuação clara**: Utilize esta opção para remover os metadados de **pontuação** da coluna especificada.  
  
         Atualmente, não se pode marcar explicitamente uma coluna como uma partitura em Azure Machine Learning. No entanto, algumas operações resultam em uma coluna ser sinalizada como uma pontuação interna. Além disso, um módulo R personalizado pode obter valores de pontuação.

1. Para **novos nomes**de colunas, introduza o novo nome da coluna ou colunas selecionadas.  
  
    + Os nomes das colunas podem usar apenas caracteres suportados pela codificação UTF-8. Não são permitidas cordas vazias, nulos ou nomes que consistem inteiramente em espaços.  
  
    + Para mudar o nome de várias colunas, introduza os nomes como uma lista separada da vírem por ordem dos índices das colunas.  
  
    + Todas as colunas selecionadas devem ser renomeadas. Não pode subornar ou saltar colunas.  
  
1. Submeta o oleoduto.  

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning.
