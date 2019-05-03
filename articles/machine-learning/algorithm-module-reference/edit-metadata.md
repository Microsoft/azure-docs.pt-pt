---
title: 'Edite metadados: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de editar metadados no serviço Azure Machine Learning para alterar os metadados que está associado a colunas num conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: cfee607aca155b6cf68e5bddc40eb9c752df5e34
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028819"
---
# <a name="edit-metadata-module"></a>Editar o módulo de metadados

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para alterar os metadados que está associado a colunas num conjunto de dados. O tipo de dados de valor e do conjunto de dados será alterado depois de utilizar o **Editar metadados** módulo. 
 
Alterações de metadados típicas podem incluir:
  
+ Tratando booleanas ou numérico colunas como valores categóricos  
  
+ Que indica a coluna que contém o *classe* etiqueta ou os valores que pretende categorizar ou prever  
  
+ A marcação de colunas como recursos
  
+ Alterar a data/hora valores para um valor numérico ou vice versa  
  
+ Mudar o nome de colunas
  
 Utilizar [metadados editar sempre que necessitar de modificar a definição de uma coluna, normalmente, para satisfazer os requisitos para um módulo de downstream. Por exemplo, alguns módulos podem trabalhar apenas com tipos de dados específicos ou necessitam de sinalizadores em colunas, tal como `IsFeature` ou `IsCategorical`.  
  
 Depois de efetuar a operação necessária, pode repor os metadados para o estado original. 
  
## <a name="configure-edit-metadata"></a>Configurar Editar metadados
  
1.  No Azure Machine Learning, adicione [Editar metadados](./edit-metadata.md) módulo à sua experimentação e ligue-se o conjunto de dados que pretende atualizar. Pode encontrá-lo sob **transformação de dados**, na **Manipulate** categoria.
  
2.  Clique em **inicie o Seletor de colunas** e escolha a coluna ou o conjunto de colunas para trabalhar com. Pode escolher colunas individualmente, por nome ou índice, ou pode escolher um grupo de colunas, por tipo.  
  
3.  Selecione o **tipo de dados** opção se pretender atribuir um tipo de dados diferentes para as colunas selecionadas. Alterar os dados de tipo pode ser necessário para determinadas operações: por exemplo, se o conjunto de dados de origem tiver números tratados como texto, deve alterá-los para um tipo de dados numéricos antes de a utilizar operações matemáticas. 

    + São os tipos de dados suportados `String`, `Integer`, `Double`, `Boolean`, `DateTime`. 

    + Se forem selecionadas várias colunas, tem de aplicar as alterações de metadados **todos os** nas colunas selecionadas. Por exemplo, digamos que escolher colunas numéricas de 2 a 3. Pode alterá-los a um tipo de dados de cadeia de caracteres e renomeá-los numa única operação. No entanto, não é possível alterar uma coluna para um tipo de dados de cadeia de caracteres e outra coluna de um número de vírgula flutuante para um número inteiro.
  
    + Se não especificar um novo tipo de dados, os metadados da coluna não são alterado. 
    
    + O tipo de coluna e os valores que vão ser alterados depois de executar o [Editar metadados](./edit-metadata.md) operação. Pode recuperar o tipo de dados original em qualquer altura utilizando [Editar metadados](./edit-metadata.md) para repor o tipo de dados de coluna.  

    > [!NOTE]
    > Se alterar qualquer tipo de número para o **DateTime** escreva, deixe a **formato DateTime** valor de campo em branco. Atualmente, não é possível especificar o formato de dados de destino.  

      
4.  Selecione o **Categórico** opção para especificar que os valores nas colunas selecionadas devem ser tratados como categorias. 

    Por exemplo, poderá ter uma coluna que contém os números de 0,1 e 2, mas sabe que os números realmente "Smoker", "Não-smoker" e "Desconhecido". Nesse caso, ao sinalizar a coluna como categóricos pode garantir que os valores não são utilizados nos cálculos numéricos, apenas para agrupar dados. 
  
5.  Utilize o **campos** opção se pretender alterar a forma como o que o Azure Machine Learning utiliza os dados num modelo.

    + **Funcionalidade**: Utilize esta opção para sinalizar uma coluna como uma funcionalidade, para utilização com módulos que operar apenas em colunas de funcionalidades. Por predefinição, todas as colunas inicialmente são tratadas como recursos.  
  
    + **Etiqueta**: Utilize esta opção para marcar a etiqueta (também conhecido como o atributo previsível ou variável de destino). Muitos módulos requerem que, pelo menos, uma (e apenas um) coluna de etiqueta de estar presente no conjunto de dados. 
    
        Em muitos casos, o Azure Machine Learning pode inferir que uma coluna contém uma etiqueta de classe, mas ao definir esses metadados pode garantir que a coluna é identificada corretamente. Definir esta opção não altera os valores de dados, só a maneira como que alguns algoritmos de machine learning lidar com os dados.
  

  
    > [!TIP]
    >  Tem dados que não se encaixa nessas categorias?  Por exemplo, o conjunto de dados pode conter valores como identificadores exclusivos que não são úteis como variáveis. Por vezes, IDs podem causar problemas quando utilizada num modelo. 
    >   
    >  Felizmente "nos bastidores" Azure Machine Learning mantém todos os seus dados, para que não tenha de eliminar este tipo de colunas do conjunto de dados. Quando tiver de realizar operações num conjunto especial de colunas, apenas remover todas as outras colunas temporariamente com o [Select Columns in Dataset](./select-columns-in-dataset.md) módulo. Mais tarde possível intercalar as colunas de volta para o conjunto de dados utilizando o [adicionar colunas](./add-columns.md) módulo.  
  
6. Utilize as seguintes opções para limpar seleções anteriores e restaurar metadados para os valores predefinidos.  
  
    + **Funcionalidade clara**: Utilize esta opção para remover o sinalizador de funcionalidade.  
  
         Uma vez que todas as colunas inicialmente serão tratadas como recursos, para os módulos que realizam operações matemáticas, poderá ter de utilizar esta opção para impedir o que está a ser tratado como variáveis de colunas numéricas.
  
    + **Etiqueta clara**: Utilize esta opção para remover o **etiqueta** metadados a partir da coluna especificada.  
  
    + **Desmarque a classificação**: Utilize esta opção para remover o **pontuação** metadados a partir da coluna especificada.  
  
         Atualmente a capacidade de marcar explicitamente uma coluna como uma pontuação não está disponível no Azure Machine Learning. No entanto, algumas operações resultam numa coluna a ser sinalizada como uma pontuação internamente. Além disso, um módulo R personalizado poderá saída valores de pontuação.
  
  
7.  Para **novos nomes das colunas**, escreva o novo nome da coluna selecionada ou colunas.  
  
    + Os nomes das colunas, podem utilizar apenas os carateres que são suportados pelo UTF-8 codificação. Cadeias de caracteres vazias, nulos ou apenas de espaços de nomes não são permitidos.  
  
    + Para mudar o nome de várias colunas, escreva os nomes como uma lista separada por vírgulas, por ordem de índices de colunas.  
  
    + Tem de ser mudar o nome de todas as colunas selecionadas. Não é possível omitir ou ignorar colunas.  
  
  
8.  Execute a experimentação.  

## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 