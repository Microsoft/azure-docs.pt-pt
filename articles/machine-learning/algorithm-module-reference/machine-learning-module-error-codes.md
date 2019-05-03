---
title: Resolver erros de módulo
titleSuffix: Azure Machine Learning service
description: Resolver problemas relacionados com exceções de módulo no Azure Machine Learning Studio com códigos de erro
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 09a2b616e2bba93be86241c64d37daec7d6dea3b
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029764"
---
# <a name="exceptions-and-error-codes-for-algorithm--module-reference"></a>Exceções e códigos de erro de algoritmo de & referência do módulo

Saiba mais sobre as mensagens de erro e códigos de exceção que poderá encontrar a utilização de módulos no Azure Machine Learning Studio. 

Para resolver o problema, procure o erro neste artigo para ler mais sobre as causas comuns. Existem duas formas de obter o texto completo de uma mensagem de erro no Studio:  
 
- Clique no link **ver o registo de saída**, no painel direito e desloque-se para a parte inferior. É apresentada a mensagem de erro detalhada nas duas últimas linhas da janela.  
  
- Selecione o módulo com o erro e clique no X vermelho. É apresentado apenas o texto de erro pertinentes.  
  
Se o texto da mensagem de erro não é útil, envie-nos informações sobre o contexto e qualquer pretendidas adições ou alterações. Pode enviar comentários sobre o tópico de erro ou visite o [fórum do Azure Machine Learning STUDIO](https://aka.ms/aml-forum-studio) e poste uma pergunta.  


## <a name="error-0001"></a>Erro 0001  
 Ocorre uma exceção se não foi possível encontrar um ou mais colunas especificadas de conjunto de dados.  
  
 Irá receber este erro se é efetuada uma seleção de coluna para um módulo, mas não existem colunas selecionadas no conjunto de dados de entrada. Este erro pode ocorrer se o ter digitado manualmente num nome de coluna ou se o Seletor de colunas forneceu uma coluna sugerida que não existe no conjunto de dados quando executou a experimentação.  
  
**Resolução:** Examine o módulo de gerar esta exceção e validar se o nome de coluna ou nomes estão corretos e que todas as colunas referenciadas existe.  
  
|Mensagens de exceção|  
|------------------------|  
|Não foram encontradas um ou mais colunas especificadas|  
|Coluna com o nome ou o índice "{0}" não encontrado|  
|Coluna com o nome ou o índice "{0}"não existe na"{1}"|  
 

## <a name="error-0002"></a>Erro 0002  
 Ocorre uma exceção se um ou mais parâmetros não podem ser analisado ou convertido do tipo especificado em necessária por tipo de método de destino.  
  
 Este erro ocorre no Azure Machine Learning, quando especificar um parâmetro como entrada e o tipo de valor é diferente do tipo que é esperado e não é possível efetuar a conversão implícita.  
  
**Resolução:** Verifique os requisitos de módulo e determinar que tipo de valor é necessário (string, integer, double, etc.)  
  
|Mensagens de exceção|  
|------------------------|  
|Falha ao analisar o parâmetro|  
|Falha ao analisar "{0}" parâmetro|  
|Falha ao analisar (converter) "{0}"parâmetro para"{1}"|  
|Falha ao converter "{0}"parâmetro de"{1}"para"{2}"|  
|Falha ao converter "{0}"valor de parâmetro"{1}"de"{2}"para"{3}"|  
|Falha ao converter o valor "{0}"na coluna"{1}"de"{2}"para"{3}"com a utilização do formato de"{4}" fornecido|  
  

## <a name="error-0003"></a>Erro 0003  
 Ocorre uma exceção se uma ou mais das entradas são nulos ou estar vazio.  
  
 Irá receber este erro no Azure Machine Learning, se quaisquer entradas ou parâmetros a um módulo são nulo ou estar vazio.  Este erro poderá ocorrer, por exemplo, quando não digitamos em qualquer valor para um parâmetro. Também pode acontecer se optou por um conjunto de dados que tem valores em falta ou um conjunto de dados vazio.  
  
**Resolução:**
 
+ Abra o módulo que produziu a exceção e certifique-se de que todas as entradas foram especificadas. Certifique-se de que todas as entradas são especificadas necessárias. 
+ Certifique-se de que os dados que são carregados a partir do armazenamento do Azure estão acessíveis e que o nome da conta ou a chave não foi alterado.  
+ Verifique os dados de entrada para valores em falta ou nulos.
+ Se utilizar uma consulta numa origem de dados, certifique-se de que estão a ser devolvidos dados no formato esperado. 
+ Verificar a existência de erros de digitação ou outras alterações na especificação de dados.
  
|Mensagens de exceção|  
|------------------------|  
|Uma ou mais das entradas são nulos ou estar vazio|  
|Entrada "{0}" é nulo ou está vazio|  
  

## <a name="error-0004"></a>Erro 0004  
 Ocorre uma exceção se o parâmetro é inferior ou igual ao valor específico.  
  
 Irá receber este erro no Azure Machine Learning, se o parâmetro na mensagem for inferior a um valor de limite necessário para o módulo processar os dados.  
  
**Resolução:** Examine o módulo gera a exceção e modifique o parâmetro de ser maior que o valor especificado.  
  
|Mensagens de exceção|  
|------------------------|  
|Parâmetro deve ser superior ao valor de limite.|  
|Parâmetro "{0}" valor deve ser maior que {1}.|  
|Parâmetro "{0}"tem o valor"{1}" que deve ser superior a {2}|  
  


## <a name="error-0005"></a>Erro 0005  
 Ocorre uma exceção se o parâmetro é menor do que um valor específico.  
  
 Irá receber este erro no Azure Machine Learning, se o parâmetro na mensagem é inferior ou igual a um valor de limite necessário para o módulo processar os dados.  
  
**Resolução:** Examine o módulo gera a exceção e modifique o parâmetro de ser maior ou igual ao valor especificado.  
  
|Mensagens de exceção|  
|------------------------|  
|Parâmetro deve ser maior ou igual ao valor de limite.|  
|Parâmetro "{0}" valor deve ser maior ou igual a {1}.|  
|Parâmetro "{0}"tem o valor"{1}" que deve ser maior ou igual a {2}.|  
  

## <a name="error-0006"></a>Erro 0006  
 Ocorre uma exceção se o parâmetro é maior que ou igual ao valor especificado.  
  
 Irá receber este erro no Azure Machine Learning, se o parâmetro na mensagem for maior que ou igual a um valor de limite necessário para o módulo processar os dados.  
  
**Resolução:** Examine o módulo gera a exceção e modifique o parâmetro de ser inferior ao valor especificado.  
  
|Mensagens de exceção|  
|------------------------|  
|Erro de correspondência de parâmetros. Um dos parâmetros deve ser menor que o outro.|  
|Parâmetro "{0}"valor deve ser inferior ao parâmetro"{1}" valor.|  
|Parâmetro "{0}"tem o valor"{1}" que deve ser inferior a {2}.|  
  

## <a name="error-0007"></a>Erro 0007  
 Ocorre uma exceção se o parâmetro é maior do que um valor específico.  
  
 Irá receber este erro no Azure Machine Learning, se, nas propriedades para o módulo, tiver especificado um valor que é maior do que é permitido. Por exemplo, poderá especificar um de dados que está fora do intervalo de datas suportados, ou pode indicar que cinco colunas utilizada quando apenas três colunas estão disponíveis. 
 
 Também poderá ver este erro se estiver especificando dois conjuntos de dados que precisam de correspondência de alguma forma. Por exemplo, se está renomeando colunas e especifica as colunas pelo índice, o número de nomes que é fornecer deve corresponder ao número de índices de colunas. Outro exemplo poderá ser uma operação matemática que utiliza duas colunas, onde as colunas tem de ter o mesmo número de linhas. 
  
**Resolução:**
 
 + Abra o módulo em questão e reveja as definições de propriedade numérica.
 + Certifique-se de que quaisquer valores de parâmetro enquadram-se no intervalo suportado de valores para essa propriedade.
 + Se o módulo precisar de várias entradas, certifique-se de que as entradas são do mesmo tamanho.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Verifique se a origem de dados ou conjunto de dados foram alterados. Por vezes, um valor que funcionou com uma versão anterior dos dados irá falhar depois do número de colunas, os tipos de dados de coluna ou o tamanho dos dados foi alterado.  
  
|Mensagens de exceção|  
|------------------------|  
|Erro de correspondência de parâmetros. Um dos parâmetros deve ser menor ou igual a outro.|  
|Parâmetro "{0}"valor deve ser inferior ou igual ao parâmetro"{1}" valor.|  
|Parâmetro "{0}"tem o valor"{1}" que deve ser inferior ou igual a {2}.|  
  

## <a name="error-0008"></a>Erro 0008  
 Ocorre uma exceção se o parâmetro não está no intervalo.  
  
 Irá receber este erro no Azure Machine Learning, se o parâmetro na mensagem está fora dos limites necessários para o módulo processar os dados.  
  
 Por exemplo, é apresentado este erro se tentar usar [adicionar linhas](add-rows.md) combinar dois conjuntos de dados que têm um número diferente de colunas.  
  
**Resolução:** Examine o módulo gera a exceção e modifique o parâmetro de estar no intervalo especificado.  
  
|Mensagens de exceção|  
|------------------------|  
|Valor do parâmetro não está no intervalo especificado.|  
|Parâmetro "{0}" valor não está no intervalo.|  
|Parâmetro "{0}" valor deve estar no intervalo [{1}, {2}].|  
  

## <a name="error-0009"></a>Erro 0009  
 Exceção ocorre quando o nome da conta de armazenamento do Azure ou o nome de contentor foi incorretamente especificado.  
  
Este erro ocorre no Azure Machine Learning Studio quando especificar parâmetros para uma conta de armazenamento do Azure, mas não é possível resolver o nome ou a palavra-passe. Erros de palavras-passe ou nomes de conta podem ocorrer por diversos motivos:
 
 + A conta é o tipo errado. Alguns novos tipos de conta não são suportados para utilização com o Machine Learning Studio. Ver [importar dados](import-data.md) para obter detalhes.
 + Que introduziu o nome de conta incorreta
 + A conta já não existe
 + A palavra-passe para a conta de armazenamento está errada ou foi alterada
 + Não especificou o nome do contentor ou o contentor não existe
 + Não especificou totalmente o caminho do ficheiro (caminho para o blob)
   
**Resolução:**

Esse tipo de problema ocorre, muitas vezes, quando tenta introduzir manualmente o caminho de nome, a palavra-passe ou o contentor da conta. Recomendamos que utilize o Assistente de novo para o [importar dados](import-data.md) módulo, que lhe permite procurar e verificar nomes.

Também pode verificar se a conta, contentor ou blob foi eliminado. Utilize outro utilitário de armazenamento do Azure para verificar que o nome da conta e palavra-passe tenham sido introduzidos corretamente e se o contentor existe. 

Alguns tipos de conta mais recente não são suportados pelo Azure Machine Learning. Por exemplo, o novo "acesso frequente" ou tipos de armazenamento "frios" não podem ser utilizados para o machine learning. Contas de armazenamento clássicas e contas de armazenamento criadas como "Fins gerais" funcionam bem.

Se foi especificado o caminho completo para um blob, certifique-se de que o caminho é especificado como **contentor/blobname**, e que o contentor e blob existem na conta.  
  
 O caminho não deve conter uma barra à esquerda. Por exemplo **blob/contentor/** estão incorretas e devem ser introduzidos como **blob/contentor**.  

  
|Mensagens de exceção|  
|------------------------|  
|O nome da conta de armazenamento do Azure ou o nome do contentor está incorreto.|  
|O nome da conta de armazenamento do Azure "{0}"ou o nome do contentor"{1}" está incorreto; era esperado um nome de contentor do formato contentor/blob.|  
  

## <a name="error-0010"></a>Erro 0010  
 Ocorre uma exceção se os conjuntos de dados de entrada têm nomes de coluna que devem corresponder a, mas não o fizer.  
  
 Irá receber este erro no Azure Machine Learning, se o índice de coluna na mensagem tem diferentes nomes de coluna nos dois conjuntos de dados de entrada.  
  
**Resolução:** Uso [Editar metadados](edit-metadata.md) ou modificar o conjunto de dados original para ter o mesmo nome de coluna para o índice de coluna especificada.  
  
|Mensagens de exceção|  
|------------------------|  
|Colunas com índice correspondente em conjuntos de dados de entrada têm nomes diferentes.|  
|Nomes de coluna não são o mesmo para coluna {0} (baseado em zero) de conjuntos de dados de entrada ({1} e {2} respectivamente).|  
  

## <a name="error-0011"></a>Erro 0011  
 Ocorre uma exceção se transmitido argumento de conjunto de coluna não é aplicável a qualquer uma das colunas do conjunto de dados.  
  
 Irá receber este erro no Azure Machine Learning, se a seleção da coluna especificada não corresponder a qualquer uma das colunas no conjunto de dados indicado.  
  
 Também pode obter este erro se não selecionou uma coluna e pelo menos uma coluna for necessária para o módulo trabalhar.  
  
**Resolução:** Modifique a seleção de coluna no módulo, para que ele se aplicará às colunas no conjunto de dados.  
  
 Se o módulo requer que selecione uma coluna de específica, como uma coluna de etiqueta, certifique-se de que a coluna da direita é selecionada.  
  
 Se forem selecionadas colunas inadequadas, removê-los e execute novamente a experimentação.  
  
|Mensagens de exceção|  
|------------------------|  
|Conjunto de colunas especificado não é aplicável a qualquer uma das colunas do conjunto de dados.|  
|Especificar um conjunto de colunas "{0}" não é aplicável a qualquer uma das colunas do conjunto de dados.|  
  

## <a name="error-0012"></a>Erro 0012  
 Ocorre uma exceção se não foi possível criar a instância da classe com um conjunto com êxito de argumentos.  
  
**Resolução:** Este erro não é possível acionar pelo usuário e será preterido numa versão futura.  
  
|Mensagens de exceção|  
|------------------------|  
|Destreinado modelar, preparar modelo pela primeira vez.|  
|Modelo destreinado ({0}), utilize o modelo preparado.|  
  

## <a name="error-0013"></a>Erro 0013  
 Ocorre uma exceção se o aprendiz passado para o módulo é um tipo inválido.  
  
 Este erro ocorre sempre que um modelo preparado é incompatível com o módulo de pontuação ligado. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the experiment is run.  -->
  
**Resolução:**

Determinar o tipo de aprendiz é produzido pelo módulo de treinamento e determine o módulo de pontuação que é adequado para o aprendiz. 

Se foi preparado o modelo usando qualquer um dos módulos de treinamento especializadas, ligue-se o modelo treinado apenas para o módulo de pontuação especializado correspondente. 


|Tipo de modelo|Módulo de treinamento| Módulo de pontuação|
|----|----|----|
|qualquer classificador|[Preparar modelo](train-model.md) |[Modelo de pontuação](score-model.md)|
|qualquer modelo de regressão|[Preparar modelo](train-model.md) |[Modelo de pontuação](score-model.md)|
<!--| Clustering de modelos| [Clustering do modelo de formação](train-clustering-model.md) ou [Clustering de abertura](sweep-clustering.md)| [Atribua os dados para Clusters](assign-data-to-clusters.md)|
| Deteção de anomalias - SVM de uma classe | [Preparar modelo de deteção de anomalias](train-anomaly-detection-model.md) |[Modelo de pontuação](score-model.md)|
| Deteção de anomalias - PCA |[Preparar modelo](train-model.md) |[Modelo de pontuação](score-model.md) </br> Algumas etapas adicionais são necessários para avaliar o modelo. |
| Deteção de anomalias - série de tempo|  [Deteção de anomalias de série de tempo](time-series-anomaly-detection.md) |Modelo prepara a partir dos dados e gera as pontuações. O módulo não cria um aprendiz preparado e não de classificação adicional é necessária. |
| modelo de recomendação| [Treinar Matchbox Recomendador](train-matchbox-recommender.md) | [Pontuação Matchbox Recomendador](score-matchbox-recommender.md) |
| classificação de imagens | [Classificação de imagens de pré-preparadas com Cascade](pretrained-cascade-image-classification.md) | [Modelo de pontuação](score-model.md) |
|Modelos de Vowpal Wabbit| [Preparar o Vowpal Wabbit versão modelo de 4 de 7](train-vowpal-wabbit-version-7-4-model.md) | [Modelo de 7-4 de versão do Vowpal Wabbit pontuação](score-vowpal-wabbit-version-7-4-model.md) |   
|Modelos de Vowpal Wabbit| [Preparar o Vowpal Wabbit versão modelo de 7 a 10](train-vowpal-wabbit-version-7-10-model.md) | [Modelo de 7 a 10 de Vowpal Wabbit versão pontuação](score-vowpal-wabbit-version-7-10-model.md) |
|Modelos de Vowpal Wabbit| [Preparar o modelo de versão 8 Vowpal Wabbit](score-vowpal-wabbit-version-8-model.md) | [Modelo de versão 8 Vowpal Wabbit de pontuação](score-vowpal-wabbit-version-8-model.md) |-->
  
|Mensagens de exceção|  
|------------------------|  
|Aprendiz de tipo inválido é passado.|  
|Aprendiz "{0}" tem um tipo inválido.|  


## <a name="error-0014"></a>Erro 0014  
 Ocorre uma exceção se a contagem de valores de coluna exclusivos é superior ao limite permitido.  
  
 Este erro ocorre quando uma coluna contém demasiados valores exclusivos.  Por exemplo, poderá ver este erro se especificar que uma coluna ser tratados como em dados categóricos, mas existem demasiados valores exclusivos na coluna para permitir o processamento seja concluído. Também poderá ver este erro se não existir um erro de correspondência entre o número de valores exclusivos nas duas entradas.   
  
**Resolução:**

Abra o módulo que gerou o erro e identifique as colunas usadas como entradas. Para alguns módulos, pode com o botão direito do conjunto de dados de entrada e selecione **Visualize** para obter as estatísticas em colunas individuais, incluindo o número de valores exclusivos e a distribuição dos mesmos.

Para colunas que pretende utilizar para o agrupamento ou categorização, tome medidas para reduzir o número de valores exclusivos nas colunas. Pode reduzir de diversas formas, consoante o tipo de dados da coluna. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Não é possível encontrar uma resolução que corresponda ao seu cenário? Pode fornecer seus comentários sobre este tópico, que inclui o nome do módulo que gerou o erro e o tipo de dados e a cardinalidade da coluna. Utilizaremos as informações para fornecer mais direcionada passos para cenários comuns de resolução de problemas.   
  
|Mensagens de exceção|  
|------------------------|  
|Número de valores de coluna exclusivos é superior ao limite permitido.|  
|Número de valores exclusivos na coluna: "{0}" excede a contagem de cadeias de identificação de {1}.|  
  

## <a name="error-0015"></a>Erro 0015  
 Ocorre uma exceção se a ligação de base de dados falhou.  
  
 Irá receber este erro, se introduzir um nome de conta SQL incorreto, palavra-passe, o servidor de base de dados ou nome de base de dados, ou se não é possível estabelecer uma ligação com a base de dados devido a problemas com a base de dados ou o servidor.  
  
**Resolução:** Certifique-se de que o nome da conta, palavra-passe, o servidor de base de dados e base de dados tenham sido introduzidos corretamente e se a conta especificada tem o nível correto de permissões. Certifique-se de que a base de dados está atualmente acessível.  
  
|Mensagens de exceção|  
|------------------------|  
|Erro ao efetuar a ligação de base de dados.|  
|Erro ao efetuar a ligação de base de dados: {0}.|  
  


## <a name="error-0016"></a>Erro 0016  
 Ocorre uma exceção se conjuntos de dados de entrada transmitidos para o módulo devem ter tipos de coluna compatível, mas não o fizer.  
  
 Irá receber este erro no Azure Machine Learning, se os tipos de colunas passados em duas ou mais conjuntos de dados não forem compatíveis entre si.  
  
**Resolução:** Uso [Editar metadados](edit-metadata.md) ou modificar o conjunto de dados de entrada original<!--, or use [Convert to Dataset](convert-to-dataset.md)--> para garantir que os tipos de colunas são compatíveis.  
  
|Mensagens de exceção|  
|------------------------|  
|Colunas com índice correspondente em conjuntos de dados de entrada têm tipos incompatíveis.|  
|Colunas {0} e {1} são incompatíveis.|  
|Tipos de elementos de coluna não são compatíveis para a coluna {0} (baseado em zero) de conjuntos de dados de entrada ({1} e {2} respectivamente).|  
  

## <a name="error-0017"></a>Erro 0017  
 Ocorre uma exceção se uma coluna selecionada utiliza um tipo de dados que não é suportado pelo módulo de atual.  
  
 Por exemplo, poderá receber este erro no Azure Machine Learning se sua seleção de coluna inclui uma coluna com um tipo de dados que não consegue processar o módulo, tal como uma coluna de cadeia de caracteres para uma operação matemática, ou uma coluna de classificação em que é uma coluna de funcionalidade categóricos é necessário.  
  
**Resolução:**
 1. Identifique a coluna que é o problema.
 2. Reveja os requisitos do módulo.
 3. Modificar a coluna para torná-lo a estar em conformidade com os requisitos. Poderá ter de utilizar vários dos seguintes módulos para fazer alterações, consoante a coluna e a conversão que está a tentar:
    + Uso [Editar metadados](edit-metadata.md) para alterar o tipo de dados das colunas, ou para alterar a utilização de coluna de funcionalidade para numéricos, categóricos para não categóricos e assim por diante.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Como último recurso, poderá ter de modificar o conjunto de dados de entrada original.

> [!TIP]
> Não é possível encontrar uma resolução que corresponda ao seu cenário? Pode fornecer seus comentários sobre este tópico, que inclui o nome do módulo que gerou o erro e o tipo de dados e a cardinalidade da coluna. Utilizaremos as informações para fornecer mais direcionada passos para cenários comuns de resolução de problemas. 
  
|Mensagens de exceção|  
|------------------------|  
|Não é possível processar a coluna do tipo atual. O tipo não é suportado pelo módulo.|  
|Não é possível processar a coluna do tipo {0}. O tipo não é suportado pelo módulo.|  
|Não é possível processar a coluna "{1}" do tipo {0}. O tipo não é suportado pelo módulo.|  
|Não é possível processar a coluna "{1}" do tipo {0}. O tipo não é suportado pelo módulo. Nome do parâmetro: {2}|  
  

## <a name="error-0018"></a>Erro 0018  
 Ocorre uma exceção se o conjunto de dados de entrada não é válido.  
  
**Resolução:** Este erro no Azure Machine Learning pode aparecer em vários contextos, portanto, não há não uma resolução única. Em geral, o erro indica que os dados fornecidos como entrada para um módulo tem um número incorreto de colunas ou que o tipo de dados não coincide com os requisitos do módulo. Por exemplo:  
  
-   O módulo requer uma coluna de etiqueta, mas não existe nenhuma coluna está marcada como uma etiqueta ou não selecionou uma coluna de etiqueta ainda.  
  
-   O módulo requer que dados ser categóricos, mas os seus dados são numéricos.  
  
<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->
  
-   Os dados estão no formato errado.  
  
-   Os dados importados contém carateres inválidos, valores incorretos, ou para fora de valores de intervalo.  
-   A coluna está vazia ou contém demasiados valores em falta.  
  
 Para determinar os requisitos e como os dados podem, reveja o tópico de ajuda para o módulo que irão consumir o conjunto de dados como entrada.  
  
 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  
  
|Mensagens de exceção|  
|------------------------|  
|Conjunto de dados não é válido.|  
|{0} contém dados inválidos.|  
|{0} e {1} deve ser consistente coluna inteligente.|  
  

## <a name="error-0019"></a>Erro 0019  
 Ocorre uma exceção se a coluna é esperada que contenha valores classificados, mas não existir.  
  
 Irá receber este erro no Azure Machine Learning, se os valores de coluna especificados estão fora de ordem.  
  
**Resolução:** Ordenar os valores da coluna ao modificar manualmente o conjunto de dados de entrada e volte a executar o módulo.  
  
|Mensagens de exceção|  
|------------------------|  
|Não são ordenados valores existentes na coluna.|  
|Valores existentes na coluna "{0}" não são ordenados.|  
|Valores existentes na coluna "{0}"do conjunto de dados"{1}" não são ordenados.|  
  

## <a name="error-0020"></a>Erro 0020  
 Ocorre uma exceção se o número de colunas em alguns dos conjuntos de dados passados para o módulo é demasiado pequeno.  
  
 Irá receber este erro no Azure Machine Learning se não for suficiente colunas foram selecionadas para um módulo.  
  
**Resolução:** Examine o módulo e certifique-se de que Seletor de colunas tem o número correto de colunas selecionadas.  
  
|Mensagens de exceção|  
|------------------------|  
|Número de colunas no conjunto de dados de entrada é inferior ao mínimo permitido.|  
|Número de colunas no conjunto de dados de entrada é inferior ao mínimo permitido de {0} coluna (s).|  
|Número de colunas no conjunto de dados de entrada "{0}" é inferior ao mínimo permitido de {1} coluna (s).|

## <a name="error-0021"></a>Erro 0021  
 Ocorre uma exceção se o número de linhas em alguns dos conjuntos de dados passados para o módulo é demasiado pequeno.  
  
 Este erro no visto no Azure Machine Learning quando existem linhas não suficientes no conjunto de dados para executar a operação especificada. Por exemplo, poderá ver este erro se o conjunto de dados de entrada estiver vazio ou se estiver a tentar realizar uma operação que exige um número mínimo de linhas para ser válida. Essas operações podem incluir (mas não se limitam a) agrupamento ou classificação com base nos métodos estatísticos, determinados tipos de discretização e aprender com as contagens.  
  
**Resolução:**
 
 + Abra o módulo que devolveu o erro e verificar as propriedades do conjunto de dados e o módulo de entrada. 
 + Certifique-se de que o conjunto de dados de entrada não está vazio e que existem suficientes linhas de dados para cumprir os requisitos descritos na ajuda do módulo.  
 + Se os dados são carregados a partir de uma origem externa, certifique-se de que a origem de dados está disponível e que não há nenhum erro ou alterar a definição de dados que faria com que o processo de importação obter menos linhas.
 + Se estiver a efetuar uma operação no upstream dos dados do módulo que poderá afetar o tipo de dados ou o número de valores, tais como de limpeza, dividir, ou ingresse em operações, verifica os resultados dessas operações para determinar o número de linhas devolvidas.  



## <a name="error-0022"></a>Erro 0022  
 Ocorre uma exceção se o número de colunas selecionadas no conjunto de dados de entrada não é igual para o número esperado.  
  
 Este erro no Azure Machine Learning pode ocorrer quando o módulo de downstream ou a operação requer um determinado número de colunas ou entradas, e forneceu muito poucos ou demasiadas colunas ou entradas. Por exemplo:  
  
-   Especificar uma coluna de etiqueta única ou de uma coluna de chave e selecionou acidentalmente várias colunas.  
  
-   Está renomeando colunas, mas fornecidos nomes mais ou menos do que colunas.  
  
-   O número de colunas na origem ou destino tiver sido alterado ou não corresponde ao número de colunas utilizado pelo módulo.  
  
-   Forneceu uma lista separada por vírgulas de valores de entradas de dados, mas não coincide com o número de valores ou várias entradas não são suportadas.  
  
**Resolução:** Examine o módulo e verifique a seleção de coluna para se certificar de que o número correto de colunas está selecionado. Certifique-se de que as saídas de módulos a montante e os requisitos de operações de downstream.  
  
 Se utilizou uma das opções de seleção da coluna, que podem selecionar várias colunas (índices de colunas, todos os recursos, todos os números, etc.), valide o número exato de colunas retornadas pela seleção.  
  
 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->
  
 Certifique-se de que o número ou tipo de colunas a montante não foi alterado.  
  
 Se estiver a utilizar um conjunto de dados de recomendação para preparar um modelo, lembre-se de que o recomendador espera um número limitado de colunas, correspondente pares item-utilizador ou de classificações de item de utilizador. Remova colunas adicionais antes do modelo de formação ou divisão de conjuntos de dados de recomendação. Para obter mais informações, consulte [Split Data](split-data.md).  
  
|Mensagens de exceção|  
|------------------------|  
|Número de colunas selecionadas no conjunto de dados de entrada não é igual para o número esperado.|  
|Número de colunas selecionadas no conjunto de dados de entrada não é igual para {0}.|  
|Padrão de seleção da coluna "{0}" fornece o número de colunas selecionadas no conjunto de dados entrada não é igual a {1}.|  
|Padrão de seleção da coluna "{0}" é esperado para fornecer {1} coluna (s) selecionado no conjunto de dados de entrada, mas {2} coluna (s) é/é fornecido.|  



## <a name="error-0023"></a>Erro 0023  
 Ocorre uma exceção se a coluna de destino do conjunto de dados de entrada não é válida para o módulo atual do instrutor.  
  
 Este erro no Azure Machine Learning ocorre se a coluna de destino (como selecionado nos parâmetros do módulo) não é do tipo de dados válido, contidos todos os valores em falta ou não era categórica conforme esperado.  
  
**Resolução:** Examine o módulo de entrada para inspecionar o conteúdo da coluna etiqueta/destino. Certifique-se de que não dispõe de todos os valores em falta. Se o módulo está esperando a coluna de destino para que seja categóricos, certifique-se de que existem mais de valores distintos da coluna de destino.  
  
|Mensagens de exceção|  
|------------------------|  
|Conjunto de dados de entrada tem não suportadas coluna de destino.|  
|Conjunto de dados de entrada tem não suportados a coluna de destino "{0}".|  
|Conjunto de dados de entrada tem não suportados a coluna de destino "{0}" para o aprendiz do tipo {1}.|  
 

## <a name="error-0024"></a>Erro 0024  
Ocorre uma exceção se o conjunto de dados não contém uma coluna de etiqueta.  

 Este erro no Azure Machine Learning ocorre quando o módulo requer uma coluna de etiqueta e o conjunto de dados não tem uma coluna de etiqueta. Por exemplo, avaliação de um conjunto de dados com a pontuação normalmente, requer que uma coluna de etiqueta está presente para computar métricas de precisão.  
 
Também pode acontecer que uma coluna de etiqueta está presente no conjunto de dados, mas não detetada corretamente pelo Azure Machine Learning.
  
**Resolução:**

+ Abra o módulo que gerou o erro e determinar se existe uma coluna de etiqueta. O tipo de dados ou o nome da coluna não importa, contanto que a coluna contém um único resultado (ou variável dependente) que está a tentar prever. Se não tiver a certeza de que a coluna tem a etiqueta, procure um nome genérico, como *classe* ou *destino*. 
+  Se o conjunto de dados não inclui uma coluna de etiqueta, é possível que a coluna de etiqueta foi acidentalmente ou explicitamente removida a montante. Também pode ser que o conjunto de dados não é a saída de um módulo de classificação a montante.
+ Para marcar explicitamente a coluna como a coluna de etiqueta, adicione a [Editar metadados](edit-metadata.md) módulo e ligue-se o conjunto de dados. Selecione apenas a coluna de etiqueta e selecione **rótulo** partir a **campos** lista suspensa. 
+ Se a coluna errada é escolhida como a etiqueta, pode selecionar **etiqueta clara** partir do **campos** para corrigir os metadados na coluna. 
  
|Mensagens de exceção|  
|------------------------|  
|Não existe nenhuma coluna de etiqueta no conjunto de dados.|  
|Não existe nenhuma coluna de etiqueta na "{0}".|  
  

## <a name="error-0025"></a>Erro 0025  
 Ocorre uma exceção se o conjunto de dados não contém uma coluna de pontuação.  
  
 Este erro no Azure Machine Learning ocorre se a entrada para o modelo de evaluate não contém pontuação válida colunas. Por exemplo, o utilizador tenta avaliar um conjunto de dados antes de ele foi classificado com um modelo preparado correto ou a coluna de pontuação explicitamente foi removida a montante. Essa exceção ocorre também se as colunas de pontuação nos dois conjuntos de dados são incompatíveis. Por exemplo, poderá estar a tentar comparar a precisão de um regressor foi linear com que um classificador binário.  
  
**Resolução:** Examine a entrada para o modelo de evaluate e examinar se contiver uma ou mais colunas de pontuação. Caso contrário, o conjunto de dados não foi classificado ou as colunas de pontuação foram removidas num módulo a montante.  
  
|Mensagens de exceção|  
|------------------------|  
|Não existe nenhuma coluna de classificação no conjunto de dados.|  
|Não existe nenhuma coluna de pontuação na "{0}".|  
|Não existe nenhuma coluna de pontuação na "{0}" que é produzido por um "{1}". Classificar o conjunto de dados com o tipo correto de aprendiz.|  
  

## <a name="error-0026"></a>Erro 0026  
 Ocorre uma exceção se não são permitidas colunas com o mesmo nome.  
  
 Este erro no Azure Machine Learning ocorre se várias colunas têm o mesmo nome. Uma forma, poderá receber este erro é se o conjunto de dados não tem uma linha de cabeçalho e os nomes das colunas são atribuídos automaticamente: Col0, Col1, etc.  
  
**Resolução:** Se as colunas de ter o mesmo nome, inserir um [Editar metadados](edit-metadata.md) módulo entre o conjunto de dados de entrada e o módulo. Utilize o Seletor de coluna na [Editar metadados](edit-metadata.md) para selecionar colunas para mudar o nome, escrevendo os nomes dos novos para o **novos nomes das colunas** caixa de texto.  
  
|Mensagens de exceção|  
|------------------------|  
|Os nomes das colunas igual são especificadas nos argumentos. Os nomes das colunas igual não são permitidas por módulo.|  
|Igual a nomes das colunas de argumentos "{0}"e"{1}" não são permitidos. Especifique nomes diferentes.|  
  

## <a name="error-0027"></a>Erro 0027  
 Ocorre uma exceção caso quando dois objetos têm de ser do mesmo tamanho, mas não são.  
  
 Este é um erro comum no Azure Machine Learning e pode ser causado por várias condições.  
  
**Resolução:** Não há nenhuma resolução específica. No entanto, pode verificar condições como o seguinte:  
  
-   Se está renomeando colunas, certifique-se de que cada lista (as colunas de entrada e a lista de nomes novo) tem o mesmo número de itens.  
  
-   Se estiver se juntar ou concatenar dois conjuntos de dados, certifique-se de que têm o mesmo esquema.  
  
-   Se estiver associando dois conjuntos de dados que têm várias colunas, certifique-se de que as colunas chave têm os mesmos dados, escreva e selecione a opção **permite duplicatas e preservar a ordem das colunas na seleção**.  
  
|Mensagens de exceção|  
|------------------------|  
|O tamanho dos objetos com êxito é inconsistente.|  
|O tamanho de "{0}"é inconsistente com o tamanho de"{1}".|  
  

## <a name="error-0028"></a>Erro 0028  
 Ocorre uma exceção no caso de quando um conjunto de colunas contém nomes de coluna duplicada e não é permitido.  
  
 Este erro no Azure Machine Learning ocorre quando os nomes das colunas são duplicados; ou seja, não exclusiva.  
  
**Resolução:** Se quaisquer colunas têm o mesmo nome, adicionar uma instância da [Editar metadados](edit-metadata.md) entre o conjunto de dados de entrada e o módulo de elevar o erro. Utilize o Seletor de coluna na [Editar metadados](edit-metadata.md) para selecionar colunas para mudar o nome e escreva os novos nomes de colunas para o **novos nomes das colunas** caixa de texto. Se está renomeando várias colunas, certifique-se de que os valores que digitar a **novos nomes das colunas** são exclusivos.  
  
|Mensagens de exceção|  
|------------------------|  
|Conjunto de colunas contém nomes de coluna duplicada.|  
|O nome "{0}" está duplicado.|  
|O nome "{0}"são duplicados em"{1}".|  
  

## <a name="error-0029"></a>Erro 0029  
 Ocorre uma exceção caso quando inválido é passado.  
  
 Este erro no Azure Machine Learning ocorre no caso de quando o URI inválido for passado.  Irá receber este erro se uma das seguintes condições for verdadeira:, ou.  
  
-   O público ou o URI de SAS fornecido para o armazenamento de Blobs do Azure para leitura ou escrita contém um erro.  
  
-   A janela de tempo a SAs expirou.  
  
-   O URL de Web por meio de origem HTTP representa um ficheiro ou um URI de loopback.  
  
-   O URL do Web via HTTP contém um URL formatado incorretamente.  
  
-   O URL não é possível resolver a origem remota.  
  
**Resolução:** Examine o módulo e verifique se o formato do URI. Se a origem de dados é um URL de Web via HTTP, certifique-se de que a origem pretendida não é um ficheiro ou um loopback URI (localhost).  
  
|Mensagens de exceção|  
|------------------------|  
|Uri inválido é passado.|  
  

## <a name="error-0030"></a>Erro 0030  
 Ocorre uma exceção no caso de quando não é possível transferir um ficheiro.  
  
 Essa exceção no Azure Machine Learning ocorre quando não é possível transferir um ficheiro. Receberá esta exceção, quando uma tentativa de leitura de uma origem HTTP falhou depois de repetir três (3) tentativas.  
  
**Resolução:** Certifique-se de que o URI para a origem HTTP está correto e se o site é atualmente acessível através da Internet.  
  
|Mensagens de exceção|  
|------------------------|  
|Não é possível transferir um ficheiro.|  
|Erro ao transferir o ficheiro: {0}.|  
  

## <a name="error-0031"></a>Erro 0031  
 Ocorre uma exceção se o número de colunas no conjunto de colunas é menor que o necessário.  
  
 Este erro no Azure Machine Learning ocorre se o número de colunas selecionado é menor do que necessário.  Irá receber este erro se o mínimo necessário de número de colunas não está selecionado.  
  
**Resolução:** Adicionar colunas adicionais para a seleção de coluna com o **Seletor de colunas**.  
  
|Mensagens de exceção|  
|------------------------|  
|Número de colunas no conjunto de colunas é menor que o necessário.|  
|{0} coluna (s) deve ser especificado. O número real de colunas especificados é {1}.|  

## <a name="error-0032"></a>Erro 0032  
 Ocorre uma exceção se o argumento não é um número.  
  
 Irá receber este erro no Azure Machine Learning, se o argumento é um valor de duplo ou NaN.  
  
**Resolução:** Modifique o argumento especificado para utilizar um valor válido.  
  
|Mensagens de exceção|  
|------------------------|  
|Argumento não é um número.|  
|"{0}" não é um número.|  
  

## <a name="error-0033"></a>Erro 0033  
 Ocorre uma exceção se o argumento é infinito.  
  
 Este erro no Azure Machine Learning ocorre se o argumento é infinito. Irá receber este erro se o argumento `double.NegativeInfinity` ou `double.PositiveInfinity`.  
  
**Resolução:** Modifique o argumento especificado para ser um valor válido.  
  
|Mensagens de exceção|  
|------------------------|  
|É o argumento tem de ser finito.|  
|"{0}" não é finita.|  
  

## <a name="error-0034"></a>Erro 0034  
 Ocorre uma exceção se não existe mais do que uma classificação para um par de determinado item do utilizador.  
  
 Este erro no Azure Machine Learning ocorre na recomendação se um par de item de utilizador tem o valor de mais do que uma classificação.  
  
**Resolução:** Certifique-se de que o par de item de utilizador tiver apenas um valor de classificação.  
  
|Mensagens de exceção|  
|------------------------|  
|Existe mais do que uma classificação para o valor ou valores no conjunto de dados.|  
|Mais do que uma classificação para usuário {0} item e {1} na tabela de dados de predição de classificação.|  
  

## <a name="error-0035"></a>Erro 0035  
 Ocorre uma exceção se nenhum recurso foram fornecido para um determinado usuário ou um item.  
  
 Este erro no Azure Machine Learning ocorre está a tentar utilizar um modelo de recomendação para a classificação, mas não é possível encontrar um vetor de funcionalidade.  
  
**Resolução:**

O recomendador Matchbox tem determinados requisitos que têm de ser cumpridos ao utilizar funcionalidades de item ou recursos do usuário.  Este erro indica que um vetor de recurso está em falta para um utilizador ou um item fornecido como entrada.  Tem de se certificar de que um vetor de recursos está disponível nos dados para cada utilizador ou o item.  
  
 Por exemplo, se treinados uma recomendação com funcionalidades como a idade do usuário, localização ou renda de modelo, mas agora pretende criar as pontuações para novos utilizadores que não foram observadas durante o treinamento, tem de fornecer um conjunto equivalente de recursos (ou seja, idade, localização, e valores de renda) para os novos utilizadores para fazer previsões adequados para eles. 
 
 Se não tiver quaisquer funcionalidades para estes utilizadores, considere a engenharia de funcionalidades para gerar recursos apropriados.  Por exemplo, se não tiver valores de idade ou rendimentos a utilizadores individuais, pode gerar valores aproximados para utilizar para um grupo de utilizadores. 
 
<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).
 
For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->
  
 > [!TIP]
 > Resolução não aplicável ao seu caso? Está bem-vindo ao enviar comentários sobre este artigo e fornecer informações sobre o cenário, incluindo o módulo e o número de linhas na coluna. Nós usaremos estas informações para fornecer mais detalhado passos resolução de problemas no futuro.
   
|Mensagens de exceção|  
|------------------------|  
|Não existem funcionalidades foram fornecidas para um item ou o utilizador necessário.|  
|Funcionalidades para {0} necessária mas não foi fornecida.|  
  

## <a name="error-0036"></a>Erro 0036  
 Ocorre uma exceção se vários vetores de funcionalidade foram fornecidos para um determinado usuário ou um item.  
  
 Este erro no Azure Machine Learning ocorre se um vetor de recurso é definido mais do que uma vez.  
  
**Resolução:** Certifique-se de que o vetor de funcionalidade não está definido mais do que uma vez.  
  
|Mensagens de exceção|  
|------------------------|  
|Duplicar a definição de recurso para um utilizador ou o item.|  
|Duplicar a definição de recurso para {0}.|  
  

## <a name="error-0037"></a>Erro 0037  
 Ocorre uma exceção se forem especificadas várias colunas de etiqueta e apenas um é permitido.  
  
 Este erro no Azure Machine Learning ocorre se mais de uma coluna é selecionada para ser a nova coluna de etiqueta. Mais os algoritmos de aprendizagem supervisionado necessitam de uma única coluna seja marcado como o destino ou etiqueta.  
  
**Resolução:** Certifique-se de selecionar uma única coluna como a nova coluna de etiqueta.  
  
|Mensagens de exceção|  
|------------------------|  
|Foram especificadas várias colunas de etiqueta.|  
  

## <a name="error-0038"></a>Error 0038  
 Exceção ocorre se o número de elementos esperado deve ser um valor exato, mas não é.  
  
 Este erro no Azure Machine Learning ocorre se o número de elementos esperado deve ser um valor exato, mas não é.  Irá receber este erro se o número de elementos não for igual ao valor esperado válido.  
  
**Resolução:** Modifique a entrada para ter o número correto de elementos.  
  
|Mensagens de exceção|  
|------------------------|  
|Número de elementos não é válido.|  
|Número de elementos na "{0}" não é válido.|  
|Número de elementos na "{0}" não é igual ao número válido de {1} elemento (s).|  
  

## <a name="error-0039"></a>Erro 0039  
 Ocorre uma exceção se uma operação falhou.  
  
 Este erro no Azure Machine Learning ocorre quando não é possível concluir uma operação interna.  
  
**Resolução:** Este erro é causado por várias condições e não há nenhum recurso específico.  
 A tabela seguinte contém as mensagens genéricas para este erro, que são seguidas de uma descrição específica da condição. 
 
 Se não estão disponíveis, detalhes [enviar comentários](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) e fornecer informações sobre os módulos que gerou o erro e as condições relacionadas.
  
|Mensagens de exceção|  
|------------------------|  
|Falha na operação.|  
|Ocorreu um erro ao concluir a operação: {0}.|  
  

## <a name="error-0040"></a>Erro 0040  
 Ocorre uma exceção ao chamar um módulo preterido.  
  
 Este erro no Azure Machine Learning é produzido ao chamar um módulo preterido.  
  
**Resolução:** Substitua o módulo preterido por um suportados. Consulte o registo de saída do módulo para obter as informações sobre qual módulo para utilizar em vez disso.  
  
|Mensagens de exceção|  
|------------------------|  
|Aceder ao módulo preterido.|  
|Módulo "{0}" foi preterida. Módulo de utilização "{1}" em vez disso.|  
 

## <a name="error-0041"></a>Erro 0041  
 Ocorre uma exceção ao chamar um módulo preterido.  
  
 Este erro no Azure Machine Learning é produzido ao chamar um módulo preterido.  
  
**Resolução:** Substitua o módulo preterido um conjunto de aplicações suportadas. Estas informações devem estar visíveis no log de saída do módulo.  
  
|Mensagens de exceção|  
|------------------------|  
|Aceder ao módulo preterido.|  
|Módulo "{0}" foi preterida. Utilizar os módulos "{1}" para a funcionalidade pedida.|  
 

## <a name="error-0042"></a>Erro 0042  
 Exceção ocorre quando não é possível converter a coluna a outro tipo.  
  
 Este erro no Azure Machine Learning ocorre quando não é possível converter a coluna no tipo especificado.  Irá receber este erro, se um módulo exigir um tipo de dados específico, como datetime, texto, um número de vírgula flutuante ou inteiros, mas não é possível converter uma coluna existente para o tipo solicitado.  
 
Por exemplo, poderá selecionar uma coluna e tentar convertê-lo para um tipo de dados numéricos para utilização numa operação matemática e obter este erro se a coluna continha dados inválidos. 

Outro motivo, que poderá receber este erro se tentar usar uma coluna que contém os números de ponto flutuante ou muitos valores exclusivos, como uma coluna categórica. 
  
**Resolução:**

+ Abra a página de ajuda para o módulo que gerou o erro e certifique-se os requisitos de tipo de dados.
+ Reveja os tipos de dados das colunas do conjunto de dados de entrada.
+ Inspecione com origem em origens de dados de esquema chamado.
+ Verifique o conjunto de dados para valores em falta ou carateres especiais que poderão bloquear a conversão para o tipo de dados pretendido. 
    + Tipos de dados numéricos devem ser consistentes: por exemplo, verificar a existência de números de pontos de vírgula flutuante numa coluna de números inteiros.
    + Procure cadeias de texto ou valores de ND numa coluna do número. 
    + Valores booleanos podem ser convertidos numa representação adequada consoante o tipo de dados necessários.
    + Examinar as colunas de texto para caracteres de não-unicode, carateres de separador ou carateres de controlo
    + Os dados de DateTime devem estar consistentes para evitar erros de modelagem, mas limpeza pode ser complexa devido os muitos formatos. Considere a utilização <!--the [Execute R Script](execute-r-script.md) or -->[Executar o Script de Python](execute-python-script.md) módulos para executar a limpeza.  
+ Se necessário, modifique os valores do conjunto de dados de entrada para que a coluna pode ser convertida com êxito. Modificação pode incluir discretização, truncamento ou operações de arredondamento, eliminação de valores atípicos ou imputation de valores em falta. Veja os artigos seguintes para alguns cenários comuns para a transformação de dados no machine learning:
    + [Limpar dados em falta](clean-missing-data.md)
    + [Normalizar dados](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->
 
> [!TIP]
> Resolução clara, ou não é aplicável ao seu caso? Está bem-vindo ao enviar comentários sobre este artigo e fornecer informações sobre o cenário, incluindo o módulo e o tipo de dados da coluna. Nós usaremos estas informações para fornecer mais detalhado passos resolução de problemas no futuro.  
  
|Mensagens de exceção|  
|------------------------|  
|Não permitido conversão.|  
|Não foi possível converter a coluna do tipo {0} a coluna do tipo {1}.|  
|Não foi possível converter a coluna "{2}" do tipo {0} a coluna do tipo {1}.|  
|Não foi possível converter a coluna "{2}" do tipo {0} a coluna "{3}" do tipo {1}.|  
  

## <a name="error-0043"></a>Erro 0043  
 Exceção ocorre quando o tipo de elemento não implementa explicitamente é igual a.  
  
 Este erro no Azure Machine Learning é não utilizado e vai ser preterido.  
  
**Resolução:** Nenhum.  
  
|Mensagens de exceção|  
|------------------------|  
|Nenhum método explícito acessível é igual a encontrado.|  
|Não é possível comparar valores de coluna \\"{0}\\" do tipo {1}. Nenhum método explícito acessível é igual a encontrado.|  


## <a name="error-0044"></a>Erro 0044  
 Exceção ocorre quando não é possível derivar o tipo de elemento da coluna dos valores existentes.  
  
 Este erro no Azure Machine Learning ocorre quando não é possível inferir o tipo de uma coluna ou colunas num conjunto de dados. Esta situação ocorre normalmente quando concatenar dois ou mais conjuntos de dados com tipos de elementos diferentes. Se não é possível determinar um tipo comum que é capaz de representar todos os valores numa coluna ou colunas sem perda de informações do Azure Machine Learning, irá gerar este erro.  
  
**Resolução:** Certifique-se de que todos os valores numa coluna indicada em ambos os conjuntos de dados que serão combinados são do mesmo tipo (numérica, booleana, categórica, cadeia de caracteres, data, etc.) ou pode ser forçada para o mesmo tipo.  
  
|Mensagens de exceção|  
|------------------------|  
|Não é possível derivar o tipo de elemento da coluna.|  
|Não é possível derivar o tipo de elemento para a coluna "{0}" – todos os elementos são referências nulas.|  
|Não é possível derivar o tipo de elemento para a coluna "{0}"do conjunto de dados"{1}" – todos os elementos são referências nulas.|  
  

## <a name="error-0045"></a>Erro 0045  
 Exceção ocorre quando não é possível criar uma coluna devido a tipos de elementos mista na origem.  
  
 Este erro no Azure Machine Learning é produzido quando os tipos de elemento de dois conjuntos de dados que serão combinados são diferentes.  
  
**Resolução:** Certifique-se de que todos os valores numa coluna indicada em ambos os conjuntos de dados que serão combinados são do mesmo tipo (numérica, booleana, categórica, cadeia de caracteres, data, etc.).  
  
|Mensagens de exceção|  
|------------------------|  
|Não é possível criar a coluna com tipos de elementos misto.|  
|Não é possível criar a coluna com o ID "{0}" do elemento misto tipos: \n\tType de dados [{1}, {0}] é {2}\n\tType de dados [{3}, {0}] é {4}.|  
  

## <a name="error-0046"></a>Erro 0046  
 Exceção ocorre quando não é possível criar o diretório no caminho especificado.  
  
 Este erro no Azure Machine Learning ocorre quando não é possível criar um diretório no caminho especificado. Irá receber este erro se qualquer parte do caminho para o diretório de saída para uma consulta do Hive é incorreto ou inacessível.  
  
**Resolução:** Examine o módulo e certifique-se de que o caminho do diretório está formatado corretamente e que é acessível com as credenciais atuais.  
  
|Mensagens de exceção|  
|------------------------|  
|Especifique um diretório de saída válida.|  
|Diretório: {0} não é possível criar. Especifique o caminho válido.|  
  

## <a name="error-0047"></a>Erro 0047  
 Ocorre uma exceção se o número de colunas de funcionalidades em alguns dos conjuntos de dados passados para o módulo é demasiado pequeno.  
  
 Este erro no Azure Machine Learning ocorre se o conjunto de dados de entrada para treinamento não contém o número mínimo de colunas necessárias para o algoritmo. Normalmente, o conjunto de dados está vazio ou contém apenas as colunas de treinamento.  
  
**Resolução:** Rever o conjunto de dados de entrada para tornar-se de que há um ou mais colunas adicionais para além da coluna de etiqueta.  
  
|Mensagens de exceção|  
|------------------------|  
|Número de colunas de funcionalidades no conjunto de dados de entrada é inferior ao mínimo permitido.|  
|Número de colunas de funcionalidades no conjunto de dados de entrada é inferior ao mínimo permitido de {0} coluna (s).|  
|Número de colunas de funcionalidades no conjunto de dados de entrada "{0}" é inferior ao mínimo permitido de {1} coluna (s).|  
  

## <a name="error-0048"></a>Error 0048  
 Ocorre uma exceção no caso de quando não é possível abrir um arquivo.  
  
 Este erro no Azure Machine Learning ocorre quando não é possível abrir um ficheiro para leitura ou escrita. Pode receber este erro por esses motivos:  
  
-   O contentor ou o ficheiro (blob) não existe  
  
-   O nível de acesso do ficheiro ou contentor não lhe permite aceder ao ficheiro  
  
-   O ficheiro é demasiado grande para leitura ou o formato incorreto  
  
**Resolução:** Examine o módulo e o ficheiro que está a tentar ler.  
  
 Certifique-se de que os nomes de contentor e do ficheiro estão corretos.  
  
 Utilize o portal clássico do Azure ou uma ferramenta de armazenamento do Azure para verificar se tem permissão para aceder ao ficheiro.  
  
  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->
  
|Mensagens de exceção|  
|------------------------|  
|Não é possível abrir um ficheiro.|  
|Erro ao abrir o ficheiro: {0}.|  


## <a name="error-0049"></a>Erro 0049  
 Ocorre uma exceção no caso de quando não é possível analisar um ficheiro.  
  
 Este erro no Azure Machine Learning ocorre quando não é possível analisar um ficheiro. Irá receber este erro se o formato de ficheiro selecionado na [importar dados](import-data.md) módulo não coincide com o formato real do arquivo, ou se o ficheiro contém um caráter irreconhecível.  
  
**Resolução:** Examine o módulo e corrigir a seleção do formato de ficheiro, se ele não corresponde ao formato do ficheiro. Se possível, Inspecione o ficheiro para confirmar que não contém quaisquer carateres ilegais.  
  
|Mensagens de exceção|  
|------------------------|  
|Não é possível analisar um arquivo.|  
|Ocorreu um erro ao analisar o ficheiro: {0}.|  
  

## <a name="error-0050"></a>Erro 0050  
 No caso de entrada quando ocorre uma exceção e ficheiros de saída são os mesmos.  
  
**Resolução:** Este erro no Azure Machine Learning é não utilizado e vai ser preterido.  
  
|Mensagens de exceção|  
|------------------------|  
|Os ficheiros especificados para entrada e saída não podem ser o mesmo.|


## <a name="error-0051"></a>Erro 0051  
 Ocorre uma exceção no caso de quando vários arquivos de saída são os mesmos.  
  
**Resolução:** Este erro no Azure Machine Learning é não utilizado e vai ser preterido.  
  
|Mensagens de exceção|  
|------------------------|  
|Os ficheiros especificados para saídas não podem ser o mesmo.|


## <a name="error-0052"></a>Erro 0052  
 Ocorre uma exceção se a chave de conta de armazenamento do Azure foi incorretamente especificado.  
  
 Este erro no Azure Machine Learning ocorre se a chave utilizada para aceder à conta de armazenamento do Azure está incorreta. Por exemplo, poderá ver este erro se a chave de armazenamento do Azure foi truncada quando copiados e colados ou se foi utilizada uma chave incorreta.  
  
 Para obter mais informações sobre como obter a chave para uma conta de armazenamento do Azure, consulte [ver, copiar e voltar a gerar armazenamento de chaves de acesso](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/).  
  
**Resolução:** Examine o módulo e certifique-se de que a chave de armazenamento do Azure está correta para a conta. Copie a chave novamente no portal clássico do Azure se for necessário.  
  
|Mensagens de exceção|  
|------------------------|  
|A chave de conta de armazenamento do Azure está incorreta.|  
  

## <a name="error-0053"></a>Erro 0053  
 Ocorre uma exceção no caso de quando não são recursos do usuário ou itens para recomendações de matchbox.  
  
 Este erro no Azure Machine Learning é produzido quando não é possível encontrar um vetor de funcionalidade.  
  
**Resolução:** Certifique-se de que um vetor de recurso está presente no conjunto de dados de entrada.  
  
|Mensagens de exceção|  
|------------------------|  
|Recursos do usuário ou / e os itens são necessários, mas não fornecidos.|  

## <a name="error-0054"></a>Error 0054  
 Ocorre uma exceção se há valores distintos da coluna para concluir a operação.  
  
**Resolução:** Este erro no Azure Machine Learning é não utilizado e vai ser preterido.  
  
|Mensagens de exceção|  
|------------------------|  
|Dados têm valores distintos na coluna especificada para concluir a operação.|  
|Dados têm valores distintos na coluna especificada para concluir a operação. É o mínimo exigido {0} elementos.|  
|Dados têm valores distintos na coluna "{1}" para concluir a operação. É o mínimo exigido {0} elementos.|  
  

## <a name="error-0055"></a>Erro 0055  
 Ocorre uma exceção ao chamar um módulo preterido.  Este erro no Azure Machine Learning é apresentada se tentar chamar um módulo que foi preterido.
  
**Resolução:**
  
|Mensagens de exceção|  
|------------------------|  
|Aceder ao módulo preterido.|  
|Módulo "{0}" foi preterida.|  

## <a name="error-0056"></a>Erro 0056  
 Ocorre uma exceção se as colunas que selecionou para uma operação viola requisitos.  
  
 Este erro no Azure Machine Learning ocorre ao escolher colunas para uma operação que exige que a coluna ter um tipo de dados específico. 
 
 Este erro também pode acontecer se a coluna é o tipo de dados correto, mas o módulo que está a utilizar requer que a coluna também ser marcada como um recurso, uma etiqueta ou uma coluna categórica.  
  
  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->
  
**Resolução:**
  
1.  Reveja o tipo de dados das colunas atualmente selecionadas. 

2. Determinar se as colunas selecionadas são categóricos, etiqueta ou colunas de funcionalidades.  
  
3.  Reveja o tópico de ajuda para o módulo no qual efetuar a seleção de coluna, para determinar se existem requisitos específicos para a utilização de tipo ou coluna de dados.  
  
3.  Uso [Editar metadados](edit-metadata.md) para alterar o tipo de coluna para a duração desta operação. Não se esqueça de alterar o tipo de coluna para o valor original, a utilizar outra instância do [Editar metadados](edit-metadata.md), se precisar dela para operações de downstream.  
  
|Mensagens de exceção|  
|------------------------|  
|Um ou mais colunas selecionadas não eram uma categoria permitidos.|  
|Coluna com o nome "{0}" não é uma categoria permitidos.|  
  

## <a name="error-0057"></a>Erro 0057  
 Ocorre uma exceção ao tentar criar um ficheiro ou blob que já existe.  
  
 Essa exceção ocorre quando estiver a utilizar o [exportar dados](export-data.md) módulo ou outro módulo para salvar os resultados de uma experimentação no Azure Machine Learning para o armazenamento de Blobs do Azure, mas tentar criar um ficheiro ou blob que já existe.   
  
**Resolução:**
 
 Irá receber este erro apenas se definir a propriedade anteriormente **modo de escrita de armazenamento de Blobs do Azure** ao **erro**. Por predefinição, este módulo gera um erro se tentar escrever um conjunto de dados para um blob que já existe.
 
 - Abra as propriedades do módulo e altere a propriedade **modo de escrita de armazenamento de Blobs do Azure** ao **substituir**.
 - Em alternativa, pode escreva o nome de um blob de destino diferente ou ficheiro e certifique-se de que especifique um blob que já não existe.  
  
|Mensagens de exceção|  
|------------------------|  
|Ficheiro ou Blob já existe.|  
|Ficheiro ou Blob "{0}" já existe.|  
  

## <a name="error-0058"></a>Erro 0058  
 Este erro no Azure Machine Learning ocorre se o conjunto de dados não contém a coluna de etiqueta esperado.  
  
 Essa exceção também pode ocorrer quando a coluna de etiqueta fornecido não corresponde a dados ou o tipo de dados esperado pelo aprendiz ou com os valores de errado. Por exemplo, esta exceção é produzida quando utilizar uma coluna de etiqueta de valor real ao treinar um classificador binário.  
  
**Resolução:** A resolução depende o aprendiz ou instrutor que está a utilizar e os tipos de dados das colunas no conjunto de dados. Em primeiro lugar, verifique se os requisitos do algoritmo de machine learning ou do módulo de treinamento.  
  
 Rever o conjunto de dados de entrada. Certifique-se de que a coluna espera que deve ser tratada como a etiqueta tiver os dados corretos, escreva para o modelo que está a criar.  
  
 Verifique as entradas para valores em falta e eliminar ou substituí-las conforme necessário.  
  
 Se necessário, adicione a [Editar metadados](edit-metadata.md) módulo e certifique-se de que a coluna de etiqueta está marcada como uma etiqueta.  
  
|Mensagens de exceção|  
|------------------------|  
|A coluna de etiqueta não é conforme esperado|  
|A coluna de etiqueta não é funciona como esperado no "{0}".|  
|A coluna de etiqueta "{0}"não é esperado no"{1}".|  
  

## <a name="error-0059"></a>Error 0059  
 Ocorre uma exceção se um índice de coluna especificado num Seletor de coluna não pode ser analisado.  
  
 Este erro no Azure Machine Learning ocorre se um índice de coluna especificado quando é utilizado o Seletor de coluna não pode ser analisado.  Irá receber este erro quando o índice de coluna está num formato inválido que não pode ser analisado.  
  
**Resolução:** Modificar o índice de coluna para utilizar um valor de índice válido.  
  
|Mensagens de exceção|  
|------------------------|  
|Não foi possível analisar um ou mais índices de colunas especificado ou intervalos de índice.|  
|Índice de coluna ou de intervalo "{0}" não foi possível analisar.|  
  

## <a name="error-0060"></a>Erro 0060  
 Exceção ocorre quando um fora do intervalo de coluna de intervalo é especificado num Seletor de coluna.  
  
 Este erro no Azure Machine Learning ocorre quando um intervalo de coluna fora do intervalo é especificado no Seletor de colunas. Irá receber este erro se o intervalo de coluna no selecionador de coluna não correspondem às colunas no conjunto de dados.  
  
**Resolução:** Modificar o intervalo de coluna no selecionador de coluna para correspondem às colunas no conjunto de dados.  
  
|Mensagens de exceção|  
|------------------------|  
|Inválido ou fora do intervalo de índice de coluna de intervalo especificado.|  
|Intervalo de coluna "{0}" é inválido ou fora do intervalo.|  
  

## <a name="error-0061"></a>Erro 0061  
 Ocorre uma exceção ao tentar adicionar uma linha a uma DataTable com um número diferente de colunas do que a tabela.  
  
 Este erro no Azure Machine Learning ocorre quando tentar adicionar uma linha para um conjunto de dados que tem um número diferente de colunas do que o conjunto de dados.  Irá receber este erro se a linha que está a ser adicionada ao conjunto de dados tem um número diferente de colunas do conjunto de dados de entrada.  A linha não pode ser anexada ao conjunto de dados se o número de colunas é diferente.  
  
**Resolução:** Modificar o conjunto de dados de entrada para ter o mesmo número de colunas como a linha adicionada ou modificar a linha adicionada para ter o mesmo número de colunas como o conjunto de dados.  
  
|Mensagens de exceção|  
|------------------------|  
|Todas as tabelas tem de ter o mesmo número de colunas.|  
  

## <a name="error-0062"></a>Erro 0062  
 Ocorre uma exceção ao tentar comparar dois modelos com tipos diferentes de aprendiz.  
  
 Este erro no Azure Machine Learning é produzido quando as métricas de avaliação de dois diferentes com a pontuação conjuntos de dados não podem ser comparadas. Neste caso, não é possível comparar a eficiência, os modelos usados para produzir os dois conjuntos de dados com a pontuação.  
  
**Resolução:** Certifique-se de que os resultados com a pontuação são produzidos pelo mesmo tipo de modelo de aprendizagem automática (classificação binária, regressão, classificação de Roc, recomendação, clustering, deteção de anomalias, etc.) Todos os modelos que compare tem de ter o mesmo tipo de aprendiz.  
  
|Mensagens de exceção|  
|------------------------|  
|Todos os modelos de tem de ter o mesmo tipo de aprendiz.|  
  

 <!--## Error 0063  
 This exception is raised when R script evaluation fails with an error.  
  
 This error occurs when you have provided an R script in one of the [R language modules](r-language-modules.md) in Azure Machine Learning, and the R code contains internal syntax errors. The exception can also occur if you provide the wrong inputs to the R script. 
 
 The error can also occur if the script is too large to execute in the workspace. The maximum script size for the **Execute R Script** module is 1,000 lines or 32 KB of work space, whichever is lesser.
  
**Resolution:**

1. In Azure Machine Learning Studio, right-click the module that has the error, and select **View Log**.
2. Examine the standard error log of the module, which contains the stack trace.
    + Lines beginning with [ModuleOutput] indicate output from R.
    + Messages from R marked as **warnings** typically do not cause the experiment to fail.
3. Resolve script issues.  
    + Check for R syntax errors. Check for variables that are defined but never populated.
    + Review the input data and the script to determine if either the data or variables in the script use characters not supported by Azure Machine Learning.
    + Check whether all package dependencies are installed.
    + Check whether your code loads required libraries that are not loaded by default.
    + Check whether the required packages are the correct version.
    + Make sure that any dataset that you want to output is converted to a data frame.  
4.  Resubmit the experiment.

 <!--
> [!NOTE]
> These topics contains examples of R code that you can use, as well as links to experiments in the [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com) that use R script.
> + [Execute R Script](execute-r-script.md)
> + [Create R Model](create-r-model.md)
-->  
|Mensagens de exceção|  
|------------------------|  
|Erro durante a avaliação de R script.|  
|Ocorreu o erro seguinte durante a avaliação de R script:---início da mensagem de erro do R--- {0} ---final da mensagem de erro do R--|  
|Durante a avaliação de R script "{1}" Ocorreu o erro seguinte:---início da mensagem de erro do R--- {0} ---final da mensagem de erro do R--|  
  


## <a name="error-0064"></a>Erro 0064  
 Ocorre uma exceção se o nome da conta de armazenamento do Azure ou a chave de armazenamento foi incorretamente especificada.  
  
 Este erro no Azure Machine Learning ocorre se o nome da conta de armazenamento do Azure ou a chave de armazenamento foi incorretamente especificado. Irá receber este erro, se introduzir um nome de conta incorreta ou a palavra-passe para a conta de armazenamento. Isto pode ocorrer se introduzir manualmente o nome de conta ou palavra-passe. Também pode ocorrer se a conta tiver sido eliminada.  
  
**Resolução:** Certifique-se de que o nome da conta e palavra-passe tenham sido introduzidos corretamente e que a conta existe.  
  
|Mensagens de exceção|  
|------------------------|  
|O nome da conta de armazenamento do Azure ou a chave de armazenamento está incorreto.|  
|O nome da conta de armazenamento do Azure "{0}" ou a chave de armazenamento para o nome da conta está incorreta.|  
  

## <a name="error-0065"></a>Erro 0065  
 Ocorre uma exceção se o nome do blob do Azure foi incorretamente especificado.  
  
 Este erro no Azure Machine Learning ocorre se o nome do blob do Azure foi incorretamente especificado.  Receberá o erro se:  
  
-   Não é possível encontrar o blob no contentor especificado.  
  
 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->
  
-   Apenas o contentor foi especificado como a origem num [importar dados](import-data.md) quando era o formato Excel ou CSV com codificação do pedido; concatenação do conteúdo de todos os blobs num contentor não é permitida com esses formatos.  
  
-   Um URI de SAS não contém o nome de um blob válido.  
  
**Resolução:** Examine o módulo gera a exceção. Certifique-se de que o blob especificado existir no contentor na conta de armazenamento e que as permissões permitirem-lhe ver o blob. Certifique-se de que a entrada é o formato **containername/filename** se tiver o Excel ou CSV com formatos de codificação. Certifique-se de que um URI de SAS contém o nome de um blob válido.  
  
|Mensagens de exceção|  
|------------------------|  
|O blob de armazenamento do Azure está incorreto.|  
|O nome do blob de armazenamento do Azure "{0}" está incorreto|  
  

## <a name="error-0066"></a>Erro 0066  
 Ocorre uma exceção se não foi possível carregar um recurso para um Blob do Azure.  
  
 Este erro no Azure Machine Learning ocorre se um recurso não foi possível carregar para um Blob do Azure.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Ambos são guardadas para a mesma conta de armazenamento do Azure como a conta que contém o ficheiro de entrada.  
  
**Resolução:** Examine o módulo. Certifique-se de que o nome da conta do Azure, a chave de armazenamento e o contentor estão corretas e que a conta tem permissão de escrita ao contentor.  
  
|Mensagens de exceção|  
|------------------------|  
|Não foi possível carregar o recurso para o armazenamento do Azure.|  
|O ficheiro "{0}" não foi possível carregar para o armazenamento do Azure como {1}.|  
  

## <a name="error-0067"></a>Erro 0067  
 Ocorre uma exceção se a um conjunto de dados tiver um número diferente de colunas que o esperado.  
  
 Este erro no Azure Machine Learning ocorre se a um conjunto de dados tiver um número diferente de colunas que o esperado.  Irá receber este erro quando o número de colunas no conjunto de dados é diferente do número de colunas que o módulo de espera durante a execução.  
  
**Resolução:** Modificar o conjunto de dados de entrada ou os parâmetros.  
  
|Mensagens de exceção|  
|------------------------|  
|Número inesperado de colunas na datatable.|  
|Era esperado "{0}"colunas, mas encontrada"{1}" colunas em vez disso.|  
  

## <a name="error-0068"></a>Erro 0068  
 Ocorre uma exceção se o script de ramo de registo especificado não está correto.  
  
 Este erro no Azure Machine Learning ocorre se existem erros de sintaxe num script de Hive QL, ou se o interpretador de Hive encontra um erro ao executar a consulta ou script.  
  
**Resolução:**

A mensagem de erro do Hive é normalmente relatada no registo de erros para que pode agir com base no erro específico. 

+ Abra o módulo e inspecionar a consulta para erros.  
+ Certifique-se de que a consulta funciona, corretamente fora do Azure Machine Learning, iniciando sessão consola do Hive do cluster do Hadoop e executar a consulta.  
+ Tente colocar comentários numa linha separada em vez de misturar instruções executáveis e comentários numa única linha do script do Hive.  

### <a name="resources"></a>Recursos

Veja os artigos seguintes para obter ajuda com consultas do Hive para machine learning:

+ [Criar tabelas do Hive e carregar dados do armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Explorar dados em tabelas com consultas do Hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Criar características para dados num cluster de Hadoop com consultas do Hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Hive para folha de referência rápida de usuários do SQL (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Mensagens de exceção|  
|------------------------|  
|Script de ramo de registo está incorreto.|  
|Script de ramo de registo {0} não está correto.|  
  

## <a name="error-0069"></a>Erro 0069  
 Ocorre uma exceção se o script SQL especificado não está correto.  
  
 Este erro no Azure Machine Learning ocorre se o script SQL especificado tem problemas de sintaxe ou, se as colunas ou tabela especificada no script não é válido. 
 
 Irá receber este erro se o motor SQL encontra qualquer erro ao executar a consulta ou script. A mensagem de erro SQL é normalmente relatada no registo de erros para que pode agir com base no erro específico.  
  
**Resolução:** Examine o módulo e inspecionar a consulta SQL para erros.  
  
 Certifique-se de que a consulta funciona corretamente fora do Azure ML iniciando sessão diretamente no servidor de base de dados e executar a consulta.  
  
 Se existir uma mensagem SQL gerado comunicada pela exceção de módulo, agir com base no erro comunicado. Por exemplo, as mensagens de erro incluem, por vezes, obter instruções específicas sobre a probabilidade de erro:
+ *Não existe esse coluna ou a base de dados em falta*, indicando que pode ter escrito um nome de coluna errado. Se tiver a certeza de que o nome de coluna está correto, tente utilizar parênteses ou aspas delimitar o identificador de coluna.
+ *Erro de lógica SQL junto \<palavra-chave SQL\>*, indicando que poderá ter um erro de sintaxe antes da palavra-chave especificada

  
|Mensagens de exceção|  
|------------------------|  
|Script SQL está incorreto.|  
|Consulta SQL "{0}" não está correto.|  
|Consulta SQL "{0}" não está correto: {1}|  
  

## <a name="error-0070"></a>Erro 0070  
 Ocorre uma exceção quando tentar aceder a tabelas do Azure de não existente.  
  
 Este erro no Azure Machine Learning ocorre quando tenta acessar uma tabela do Azure não existente. Irá receber este erro, se especificar uma tabela no armazenamento do Azure, que não existe ao ler a partir de ou escrever no armazenamento de tabelas do Azure. Isto pode acontecer se digitar o nome da tabela desejada, ou tem um erro de correspondência entre o nome de destino e o tipo de armazenamento. Por exemplo, se destina a ler a partir de uma tabela mas introduzido o nome de um blob em vez disso.  
  
**Resolução:** Examine o módulo para verificar se o nome da tabela está correto.  
  
|Mensagens de exceção|  
|------------------------|  
|Tabela do Azure não existe.|  
|Tabela do Azure "{0}" não existe.|  
  
## <a name="error-0071"></a>Erro 0071  
 Ocorre uma exceção se fornecido as credenciais estão incorretas.  
  
 Este erro no Azure Machine Learning ocorre se as credenciais fornecidas estão incorretas.  
  
 Também poderá receber este erro se o módulo não é possível ligar a um cluster do HDInsight.  
  
**Resolução:** Reveja as entradas para o módulo e verifique se o nome da conta e palavra-passe.  
  
 Verifique os seguintes problemas que podem causar um erro:  
  
-   O esquema do conjunto de dados não coincide com o esquema da datatable de destino.  
  
-   Os nomes de coluna estão em falta ou com erros ortográficos  
  
-   Estiver a escrever para uma tabela com nomes de colunas com carateres ilegais. Normalmente pode incluir esses nomes de colunas em parênteses Retos, mas se isto não resultar, editar os nomes das colunas para utilizar apenas letras e carateres de sublinhado (_)  
  
-   Cadeias de caracteres que está a tentar escrever contenham plicas  
  
 Se estiver a tentar ligar a um cluster do HDInsight, certifique-se de que o cluster de destino está acessível com as credenciais fornecidas.  
  
|Mensagens de exceção|  
|------------------------|  
|São transmitidas credenciais incorretas.|  
|Nome de utilizador incorreta "{0}" ou palavra-passe é passado|  
  

## <a name="error-0072"></a>Erro 0072  
 Ocorre uma exceção no caso de tempo limite da ligação.  
  
 Este erro no Azure Machine Learning ocorre quando uma ligação exceder o tempo limite. Irá receber este erro se não existem atualmente problemas de conectividade com a origem de dados ou de destino, como conectividade de internet lenta, ou se o conjunto de dados é grande e/ou a consulta SQL para os dados de leitura realiza processamento complexo.  
  
**Resolução:** Determine se existem atualmente problemas com conexões lentas para o armazenamento do Azure ou na internet.  
  
|Mensagens de exceção|  
|------------------------|  
|Ocorreu um tempo limite da ligação.|  
  

## <a name="error-0073"></a>Erro 0073  
 Ocorre uma exceção se ocorrer um erro ao converter uma coluna a outro tipo.  
  
 Este erro no Azure Machine Learning ocorre quando não é possível converter a coluna a outro tipo.  Irá receber este erro, se um módulo exigir um tipo específico e não é possível converter a coluna para o novo tipo.  
  
**Resolução:** Modifique o conjunto de dados de entrada para que a coluna pode ser convertida com base na exceção interna.  
  
|Mensagens de exceção|  
|------------------------|  
|Falha ao converter a coluna.|  
|Falha ao converter a coluna para {0}.|  
  

## <a name="error-0074"></a>Erro 0074  
 Ocorre uma exceção quando o [Editar metadados](edit-metadata.md) tenta converter uma coluna dispersa para categóricos.  
  
 Este erro no Azure Machine Learning ocorre quando o [Editar metadados](edit-metadata.md) tenta converter uma coluna dispersa para categóricos.  Irá receber este erro quando tentar converter colunas dispersas categóricos com o **tornar categóricos** opção.  Do Azure machine Learning não suporta dispersas matrizes categóricas, para que o módulo irá falhar.  
  
 <!--**Resolution:**
 Make the column dense by using [Convert to Dataset](convert-to-dataset.md) first or do not convert the column to categorical.  -->
  
|Mensagens de exceção|  
|------------------------|  
|Não não possível converter colunas dispersas Categórico.|  
  

## <a name="error-0075"></a>Erro 0075  
Exceção ocorre quando uma função de discretização inválida é utilizada quando quantizing um conjunto de dados.  
  
Este erro no Azure Machine Learning ocorre quando está a tentar a compartimentação de dados usando um método não suportado, ou quando as combinações de parâmetro são inválidas.  
  
**Resolução:**

Tratamento de erro para este evento foi introduzido numa versão anterior do Azure Machine Learning que permissão mais personalização de discretização métodos. Atualmente todos os métodos de discretização baseiam-se numa seleção de uma lista suspensa, tecnicamente, já não deve ser possível obter este erro.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->
  
|Mensagens de exceção|  
|------------------------|  
|Foi utilizada uma função de discretização inválida.|  
  

## <a name="error-0077"></a>Erro 0077  
 Exceção ocorre quando o ficheiro blob desconhecido escreve modo passado.  
  
 Este erro no Azure Machine Learning ocorre se um argumento inválido for passado nas especificações para um destino de ficheiro blob ou uma fonte.  
  
**Resolução:** Em quase todos os módulos que importar ou exportar dados para e do armazenamento de Blobs do Azure, os valores de parâmetro controlar o modo de escrita são atribuídos ao utilizar uma lista suspensa; Por conseguinte, não é possível passar um valor inválido e não deve aparecer este erro. Este erro vai ser preterido numa versão posterior.  
  
|Mensagens de exceção|  
|------------------------|  
|Os BLOBs não suportado escreve modo.|  
|Modo de escritas de blob não suportado: {0}.|  
  

## <a name="error-0078"></a>Erro 0078  
 Ocorre uma exceção quando o HTTP opção para [importar dados](import-data.md) recebe um código de estado de 3xx indicando o redirecionamento.  
  
 Este erro no Azure Machine Learning ocorre quando o HTTP opção para [importar dados](import-data.md) recebe um 3xx (301, 302, 304, etc.) o código de estado que indica o redirecionamento. Irá receber este erro se tentar ligar a uma origem HTTP que redireciona o navegador para outra página. Para segurança motivos, redirecionando os Web sites não são permitidos como fontes de dados do Azure Machine Learning.  
  
**Resolução:** Se o Web site é um Web site fidedigno, introduza o URL redirecionado diretamente.  
  
|Mensagens de exceção|  
|------------------------|  
|Redirecionamento de HTTP não permitido|  
  

## <a name="error-0079"></a>Erro 0079  
 Ocorre uma exceção se o nome do contentor de armazenamento do Azure foi incorretamente especificado.  
  
 Este erro no Azure Machine Learning ocorre se o nome do contentor de armazenamento do Azure foi incorretamente especificado. Irá receber este erro se não tiver especificado o contentor e o nome de blob (ficheiro) com o **o caminho para o início com o contentor de BLOBs** opção ao escrever no armazenamento de Blobs do Azure.  
  
**Resolução:** Examine os [exportar dados](export-data.md) módulo e certifique-se de que o caminho especificado para o blob contém o contentor e o nome de ficheiro, no formato **contentor/filename**.  
  
|Mensagens de exceção|  
|------------------------|  
|O nome do contentor de armazenamento do Azure está incorreto.|  
|O nome do contentor de armazenamento do Azure "{0}" está incorreto; era esperado um nome de contentor do formato contentor/blob.|  
  

## <a name="error-0080"></a>Erro 0080  
 Exceção ocorre quando a coluna com todos os valores em falta não é permitida pelo módulo.  
  
 Este erro no Azure Machine Learning é produzido quando um ou mais das colunas consumidas pelo módulo contém todos os valores em falta. Por exemplo, se um módulo é a informática estatísticas agregadas para cada coluna, ele não pode operar numa coluna que contém dados não. Nesses casos, a execução do módulo é suspensa com essa exceção.  
  
**Resolução:** Examine o conjunto de dados de entrada e remova todas as colunas que contêm todos os valores em falta.  
  
|Mensagens de exceção|  
|------------------------|  
|Não são permitidas colunas com todos os valores em falta.|  
|Coluna {0} tem todos os valores em falta.|  
  

## <a name="error-0081"></a>Erro 0081  
 Ocorre uma exceção no módulo do PCA se o número de dimensões para reduzir o de é igual ao número de colunas de funcionalidades no conjunto de dados de entrada, que contém pelo menos uma coluna dispersa funcionalidade.  
  
 Este erro no Azure Machine Learning é produzido se as seguintes condições são cumpridas: (a) o conjunto de dados de entrada tem pelo menos uma coluna dispersa e (b) o número final de dimensões solicitado é o mesmo que o número de dimensões de entrada.  
  
**Resolução:** Considere reduzir o número de dimensões na saída a ser menos do que o número de dimensões na entrada. Isso é comum em aplicativos do PCA.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->
  
|Mensagens de exceção|  
|------------------------|  
|Para o conjunto de dados que contém colunas dispersas funcionalidade número de dimensões para reduzir deve ser inferior ao número de colunas de funcionalidades.|  
 

## <a name="error-0082"></a>Erro 0082  
 Exceção ocorre quando um modelo não pode ser com êxito a serialização anulado.  
  
 Este erro no Azure Machine Learning ocorre quando uma modelo de aprendizagem guardada ou transformação não pode ser carregada por uma versão mais recente do runtime do Azure Machine Learning como resultado de uma alteração de última hora.  
  
**Resolução:** A experimentação de preparação que produziu o modelo ou a transformação tem de ser volte a executar e o modelo ou transformação tem de ser resaved.  
  
|Mensagens de exceção|  
|------------------------|  
|Não foi possível anular o modelo porque ele provavelmente é serializado com um formato de serialização mais antigo. Voltar a preparar e volte a guardar o modelo.|  
  

## <a name="error-0083"></a>Erro 0083  
 Ocorre uma exceção se o conjunto de dados utilizado para treinamento não pode ser utilizado para o tipo concreto de aprendiz.  
  
 Este erro no Azure Machine Learning é produzido quando o conjunto de dados é incompatível com o aprendiz a ser preparado. Por exemplo, o conjunto de dados pode conter, pelo menos, um valor em falta em cada linha, e como resultado, todo o conjunto de dados poderia ser ignorado durante o treinamento. Em outros casos, alguns algoritmos de machine learning, como deteção de anomalias não espera etiquetas estar presente e podem emitir esta exceção se as etiquetas estão presentes no conjunto de dados.  
  
**Resolução:** Consulte a documentação de aprendiz a ser utilizado para verificar os requisitos para o conjunto de dados de entrada. Examine as colunas para ver todas as colunas necessárias estão presentes.  
  
|Mensagens de exceção|  
|------------------------|  
|Conjunto de dados usado para treinamento é inválido.|  
|{0} contém dados inválidos para treinamento.|  
|{0} contém dados inválidos para treinamento. Tipo de aprendiz: {1}.|  
  

## <a name="error-0084"></a>Erro 0084  
 Ocorre uma exceção quando são avaliadas as pontuações produzidas a partir de um Script R. Isso não é atualmente suportado.  
  
 Este erro no Azure Machine Learning ocorre se tentar usar um dos módulos para avaliar um modelo com a saída de um script R que contém as pontuações.  
  
**Resolução:**
  
|Mensagens de exceção|  
|------------------------|  
|Avaliar as pontuações produzidas pelo R não é atualmente suportado.|  
  

## <a name="error-0085"></a>Erro 0085  
 Exceção ocorre quando a avaliação de script falha com um erro.  
  
 Este erro no Azure Machine Learning ocorre quando estiver a executar o script personalizado que contém erros de sintaxe.  
  
**Resolução:** Reveja o seu código num editor de externa e verificação de erros.  
  
|Mensagens de exceção|  
|------------------------|  
|Erro durante a avaliação de script.|  
|Ocorreu o erro seguinte durante a avaliação de script, consulte o registo de saída para obter mais informações:---início da mensagem de erro da {0} interpretador--- {1} ---final da mensagem de erro do {0} interpretador-- ------|  
  

## <a name="error-0086"></a>Erro 0086  
 Exceção ocorre quando uma transformação contagem é inválida.  
  
 Este erro no Azure Machine Learning ocorre quando seleciona uma transformação com base numa tabela de contagem, mas a transformação selecionada não é compatível com os dados atuais, ou com a nova tabela de contagem.  
  
**Resolução:** O módulo suporta a guardar as contagens e regras que compõem a transformação em dois formatos diferentes. Se está a intercalar contagem de tabelas, certifique-se de que ambas as tabelas que pretende intercalar utilizam o mesmo formato.  
  
Em geral, uma transformação com base em contagem só pode ser aplicada a conjuntos de dados que têm o mesmo esquema como o conjunto de dados em que a transformação foi originalmente criada.  
  
 <!-- For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Mensagens de exceção|  
|------------------------|  
|Transformação de contagem inválida especificada.|  
|A transformação contagem na porta de entrada "{0}" é inválido.|  
|A transformação contagem na porta de entrada "{0}"não pode ser intercalada com a transformação contagem na porta de entrada"{1}'. Consulte para verificar os metadados usado para contagem de correspondências.|  
  

## <a name="error-0087"></a>Erro 0087  
 Exceção ocorre quando é especificado um tipo de tabela de contagem inválida para a aprendizagem automática com contagens de módulos.  
  
 Este erro no Azure Machine Learning ocorre quando tentar importar uma tabela de contagem existente, mas a tabela não é compatível com os dados atuais, ou com a nova tabela de contagem.  
  
**Resolução:** Existem diferentes formatos para salvar as contagens e regras que compõem a transformação. Se está a intercalar contagem de tabelas, certifique-se de que usem o mesmo formato.  
  
 Em geral, uma transformação com base em contagem só pode ser aplicada a conjuntos de dados que têm o mesmo esquema como o conjunto de dados em que a transformação foi originalmente criada.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). -->
  

## <a name="error-0088"></a>Erro 0088  
 Exceção ocorre quando uma contagem inválida o tipo é especificado para a aprendizagem automática com contagens de módulos.  
  
 Este erro no Azure Machine Learning ocorre quando tenta utilizar um método de contagem diferente do que é suportado para featurization com base em contagem.  
  
**Resolução:** Em geral, os métodos de contagem são escolhidos numa lista suspensa, para que não verá este erro.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Mensagens de exceção|  
|------------------------|  
|Foi especificado inválido, tipo de contagem.|  
|O tipo de contagem especificado '{0}' não é um tipo de contagem válido.|  
  

## <a name="error-0089"></a>Erro 0089  
 Exceção ocorre quando o número de classes especificado é inferior ao número real de classes num conjunto de dados usado para contagem.  
  
 Este erro no Azure Machine Learning ocorre quando estiver a criar uma tabela de contagem e a etiqueta contém um número diferente de classes que especificado nos parâmetros do módulo.  
  
**Resolução:** Verifique o seu conjunto de dados e descobrir exatamente quantos valores distintos (classes possíveis) há na coluna de etiqueta. Quando cria a tabela de contagem, tem de especificar, pelo menos, este número de classes.  
  
 A tabela contagem não pode determinar automaticamente o número de classes disponíveis.  
  
 Quando cria a tabela de contagem, não é possível especificar 0 ou qualquer número que é inferior ao número real de classes na coluna de etiqueta.  
  
|Mensagens de exceção|  
|------------------------|  
|O número de classes está incorreto. Certifique-se de que o número de classes que especificar no painel de parâmetro é maior que ou igual ao número de classes na coluna de etiqueta.|  
|O número de classes especificado é '{0}', que não é maior do que um valor de etiqueta'{1}' no conjunto de dados utilizado para contar. Certifique-se de que o número de classes que especificar no painel de parâmetro é maior que ou igual ao número de classes na coluna de etiqueta.|  
  

## <a name="error-0090"></a>Erro 0090  
 Ocorre uma exceção quando ocorre uma falha de criação de tabelas do Hive.  
  
 Este erro no Azure Machine Learning ocorre quando estiver a utilizar [exportar dados](export-data.md) ou outra opção para guardar os dados para um cluster do HDInsight e a tabela de ramo de registo especificada não é possível criar.  
  
**Resolução:** Verifique o nome de conta de armazenamento do Azure associado ao cluster e certifique-se de que está a utilizar a mesma conta nas propriedades do módulo.  
  
|Mensagens de exceção|  
|------------------------|  
|Não foi possível criar a tabela do Hive. Para um cluster do HDInsight, certifique-se de que o nome da conta de armazenamento do Azure associado cluster é o mesmo que o que é transmitido através do parâmetro do módulo.|  
|Tabela do Hive "{0}" não foi possível criar. Para um cluster do HDInsight, certifique-se de que o nome da conta de armazenamento do Azure associado cluster é o mesmo que o que é transmitido através do parâmetro do módulo.|  
|Tabela do Hive "{0}" não foi possível criar. Para um cluster do HDInsight, certifique-se o nome da conta de armazenamento do Azure associado cluster é "{1}".|  
 

## <a name="error-0100"></a>Erro 0100  
 Exceção ocorre quando um idioma não suportado foi especificado para um módulo personalizado.  
  
 Este erro no Azure Machine Learning ocorre quando a criação de um módulo personalizado e a propriedade de nome de **linguagem** elemento num arquivo de definição de xml de módulo personalizado tem um valor inválido. Atualmente, o único valor válido para esta propriedade é `R`. Por exemplo:  
  
 `<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Resolução:** Certifique-se de que a propriedade de nome de **linguagem** elemento no arquivo de definição de xml do módulo personalizado é definido como `R`. Guarde o ficheiro, atualizar o pacote zip do módulo personalizado e tentar adicionar o módulo personalizado novamente.  
  
|Mensagens de exceção|  
|------------------------|  
|Idioma de sem suporte de módulo personalizado especificado|  
  

## <a name="error-0101"></a>Erro 0101  
 Todos os IDs de porta e o parâmetro tem de ser exclusivos.  
  
 Este erro no Azure Machine Learning ocorre quando um ou mais portas ou os parâmetros recebem o mesmo valor de ID num arquivo de definição XML de módulo personalizado.  
  
**Resolução:** Verifique que os valores de ID em todas as portas e os parâmetros são exclusivos. Guarde o ficheiro xml, atualizar o pacote zip do módulo personalizado e tentar adicionar o módulo personalizado novamente.  
  
|Mensagens de exceção|  
|------------------------|  
|Todas as portas e IDs de parâmetro para um módulo tem de ser exclusivos|  
|Módulo '{0}' tem duplicado porta/argumento IDs. Todos os IDs de porta/argumento tem de ser exclusivos para um módulo.|  
  

## <a name="error-0102"></a>Erro 0102  
 Lançada quando não é possível extrair um arquivo ZIP.  
  
 Este erro no Azure Machine Learning ocorre quando estiver a importar um pacote zipado com a extensão. zip, mas o pacote de não é um arquivo zip, ou o ficheiro não utiliza um formato de zip suportados.  
  
**Resolução:** Certificar-se de que o ficheiro selecionado é um ficheiro. zip válido e que foi comprimida utilizando um dos algoritmos de compressão suportados.  
  
 Se obtiver este erro ao importar os conjuntos de dados em formato compactado, certifique-se de que todos os arquivos contidos utilizam um dos formatos de ficheiro suportados e estão no formato Unicode.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->
  
 Tente adicionar novamente os ficheiros pretendidos para uma nova pasta compactada zipada e tente adicionar o módulo personalizado novamente.  
  
|Mensagens de exceção|  
|------------------------|  
|Fornecido ZIP de ficheiro não está no formato correto|  


## <a name="error-0103"></a>Erro 0103  
 Emitida quando um ficheiro ZIP não contém quaisquer ficheiros. XML  
  
 Este erro no Azure Machine Learning ocorre quando o pacote zip do módulo personalizado não contém quaisquer ficheiros de definição (. xml) do módulo. Esses arquivos precisam de residir na raiz do pacote zip (por exemplo, não numa subpasta.)  
  
**Resolução:** Certifique-se de que um ou mais ficheiros de definição de módulo de xml na pasta raiz do pacote zip por extraí-lo para uma pasta temporária na sua unidade de disco. Todos os arquivos xml devem ser diretamente na pasta da que qual extraiu o pacote zip para. Certifique-se de que ao criar o pacote zip que não seleciona uma pasta que contém os arquivos xml, zip como este procedimento criará uma subpasta dentro do pacote zip com o mesmo nome que a pasta que selecionou zip.  
  
|Mensagens de exceção|  
|------------------------|  
|Fornecido ZIP ficheiro não contém quaisquer ficheiros de definição de módulo (arquivos. xml)|  


## <a name="error-0104"></a>Erro 0104  
 Lançada quando um script que não é possível localizar faz referência a um ficheiro de definição de módulo  
  
 Este erro no Azure Machine Learning é apresentado quando um ficheiro de definição de xml de módulo personalizado faz referência um ficheiro de script no **linguagem** elemento que não existe no pacote zip. O caminho do ficheiro de script é definido no **sourceFile** propriedade da **linguagem** elemento. O caminho para o ficheiro de origem é relativo à raiz do pacote zip (mesma localização que os ficheiros de definição de xml do módulo). Se o ficheiro de script numa subpasta, o caminho relativo para o ficheiro de script tem de ser especificado. Por exemplo, se todos os scripts foram armazenados num **myScripts** pasta dentro do pacote zip, o **linguagem** elemento seria necessário adicionar este caminho para o **sourceFile** propriedade como abaixo. Por exemplo:  
  
 `<Language name="R" sourceFile="myScripts/CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Resolução:** Certifique-se de que o valor do **sourceFile** propriedade na **linguagem** elemento de definição de xml do módulo personalizado está correto e se o ficheiro de origem existe no caminho relativo correto no pacote zip.  
  
|Mensagens de exceção|  
|------------------------|  
|Ficheiro de script R referenciado não existe.|  
|Ficheiro de script R referenciado '{0}' não é possível encontrar. Certifique-se de que o caminho relativo para o ficheiro está correto da localização de definições.|  


## <a name="error-0105"></a>Erro 0105  
 Este erro é apresentado quando um ficheiro de definição de módulo contém um tipo de parâmetro não suportado  
  
 Este erro no Azure Machine Learning é produzido quando a criar uma definição de xml de módulo personalizado e o tipo de um parâmetro ou o argumento na definição não corresponde um tipo suportado.  
  
**Resolução:** Certifique-se de que a propriedade de tipo de qualquer **Arg** elemento no arquivo de definição de xml do módulo personalizado é um tipo suportado.  
  
|Mensagens de exceção|  
|------------------------|  
|Tipo de parâmetro não suportado.|  
|Tipo de parâmetro não suportado '{0}' especificado.|  


## <a name="error-0106"></a>Erro 0106  
 Lançada quando um ficheiro de definição de módulo define um tipo de entrada não suportado  
  
 Este erro no Azure Machine Learning é produzido quando o tipo de uma porta de entrada num módulo personalizado definição XML não corresponde um tipo suportado.  
  
**Resolução:** Certifique-se de que a propriedade de tipo de um elemento de entrada no arquivo de definição de XML do módulo personalizado é um tipo suportado.  
  
|Mensagens de exceção|  
|------------------------|  
|Tipo de entrada não suportado.|  
|Tipo de entrada não suportado '{0}' especificado.|  


## <a name="error-0107"></a>Erro 0107  
 Lançada quando um ficheiro de definição de módulo define um tipo de saída não suportado  
  
 Este erro no Azure Machine Learning é produzido quando o tipo de uma porta de saída numa definição de xml de módulo personalizado não corresponde um tipo suportado.  
  
**Resolução:** Certifique-se de que a propriedade de tipo de um elemento de saída no arquivo de definição de xml do módulo personalizado é um tipo suportado.  
  
|Mensagens de exceção|  
|------------------------|  
|Tipo de saída não suportado.|  
|Tipo de saída não suportado '{0}' especificado.|  


## <a name="error-0108"></a>Erro 0108  
 Lançada quando um ficheiro de definição de módulo define mais portas de entrada ou de saída que são suportados  
  
 Este erro no Azure Machine Learning é produzido quando muitas portas de entrada ou de saída são definidas numa definição de xml de módulo personalizado.  
  
**Resolução:** Certifica-se de que o número máximo de portas de entrada e saídas definido na definição de xml de módulo personalizado não excede o número máximo de portas suportadas.  
  
|Mensagens de exceção|  
|------------------------|  
|Foi excedido o número suportado de portas de entrada ou de saída.|  
|Excedeu o número de suportado '{0}' portas. Número de máximo permitido '{0}'é de portas'{1}'.| 

## <a name="error-0109"></a>Erro 0109  
 Lançada quando um ficheiro de definição de módulo define um Seletor de coluna incorretamente  
  
 Este erro no Azure Machine Learning é produzido quando a sintaxe para um argumento de Seletor de coluna contém um erro numa definição de xml de módulo personalizado.  
  
**Resolução:** Este erro é produzido quando a sintaxe para um argumento de Seletor de coluna contém um erro numa definição de xml de módulo personalizado.  
  
|Mensagens de exceção|  
|------------------------|  
|Sintaxe não suportada para o Seletor de coluna.|  
  

## <a name="error-0110"></a>Erro 0110  
 Lançada quando um ficheiro de definição de módulo define um Seletor de coluna que faz referência a um ID de porta de entrada não existente  
  
 Este erro no Azure Machine Learning é produzido quando os *portId* propriedade dentro do elemento de propriedades de um Arg do tipo ColumnPicker não coincide com o valor de ID de uma porta de entrada.  
  
**Resolução:** Certifique-se de que a propriedade de portId coincide com o valor de ID de uma porta de entrada definido na definição de xml de módulo personalizado.  
  
|Mensagens de exceção|  
|------------------------|  
|Seletor de coluna faz referência a um ID de porta de entrada não existente.|  
|Seletor de coluna faz referência a um ID de porta de entrada não existente '{0}'.|  
  

## <a name="error-0111"></a>Erro 0111  
 Lançada quando um ficheiro de definição de módulo define uma propriedade inválida  
  
 Este erro no Azure Machine Learning é produzido quando uma propriedade inválida é atribuída a um elemento no módulo personalizado definição XML.  
  
**Resolução:** Certifique-se de que a propriedade é suportada pelo elemento do módulo personalizado.  
  
|Mensagens de exceção|  
|------------------------|  
|Definição de propriedade é inválida.|  
|Definição de propriedade '{0}"é inválido.|  
  

## <a name="error-0112"></a>Erro 0112  
 Lançada quando não é possível analisar um ficheiro de definição de módulo  
  
 Este erro no Azure Machine Learning é produzido quando existe um erro no formato xml que impede o definição de XML de ser analisado como um ficheiro XML válido de módulo personalizado.  
  
**Resolução:** Certifique-se de que cada elemento é aberto e fechado corretamente. Certifique-se de que não há nenhum erro na formatação de XML.  
  
|Mensagens de exceção|  
|------------------------|  
|Não é possível analisar o ficheiro de definição de módulo.|  
|Não é possível analisar o ficheiro de definição de módulo "{0}'.|  
  

## <a name="error-0113"></a>Erro 0113  
 Acionada quando um ficheiro de definição de módulo contém erros.  
  
 Este erro no Azure Machine Learning é produzido quando o arquivo de definição XML de módulo personalizado pode ser analisado, mas contém erros, tais como a definição de elementos não suportado por módulos personalizados.  
  
**Resolução:** Certifique-se de que o ficheiro de definição de módulo personalizado define elementos e propriedades que são suportadas por módulos personalizados.  
  
|Mensagens de exceção|  
|------------------------|  
|Ficheiro de definição de módulo contém erros.|  
|Ficheiro de definição de módulo "{0}" contém erros.|  
|Ficheiro de definição de módulo "{0}" contém erros. {1}|  
  

## <a name="error-0114"></a>Erro 0114  
 Emitida ao criar um módulo personalizado falha.  
  
 Este erro no Azure Machine Learning é produzido quando ocorre uma falha de uma compilação de módulo personalizado. Isto ocorre quando um ou mais erros personalizados relacionados com o módulo são encontrados ao adicionar o módulo personalizado. Os erros adicionais são comunicados dentro desta mensagem de erro.  
  
**Resolução:** Resolva os erros comunicados dentro desta mensagem de exceção.  
  
|Mensagens de exceção|  
|------------------------|  
|Falha ao criar módulo personalizado.|  
|Módulo personalizado baseia-se com falha com erro (s): {0}|  
  

## <a name="error-0115"></a>Erro 0115  
 Acionada quando um script padrão do módulo personalizado tem uma extensão não suportada.  
  
 Este erro no Azure Machine Learning ocorre quando é fornecer um script para um módulo personalizado que utiliza uma extensão de nome de arquivo desconhecido.  
  
**Resolução:** Certifique-se a extensão de formato e o nome de ficheiro do ficheiro de qualquer arquivo de script incluídos no módulo personalizado.  
  
|Mensagens de exceção|  
|------------------------|  
|Extensão não suportada para o script de predefinição.|  
|Essa extensão do ficheiro não suportado {0} para script padrão.|  
  

## <a name="error-0121"></a>Erro 0121  
 Lançada quando o SQL escreve falha porque a tabela se unwriteable  
  
 Este erro no Azure Machine Learning é produzido quando estiver a utilizar o [exportar dados](export-data.md) módulo para salvar os resultados numa tabela numa base de dados SQL e a tabela não pode ser escrito. Normalmente, verá este erro se o [exportar dados](export-data.md) módulo com êxito estabelece uma conexão com a instância do SQL Server, mas é, em seguida, não é possível escrever o conteúdo do conjunto de dados do Azure ML na tabela.  
  
**Resolução:**
 - Abrir o painel de propriedades do [exportar dados](export-data.md) módulo e certifique-se de que os nomes de base de dados e tabela são introduzidos corretamente. 
 - Reveja o esquema do conjunto de dados que está a exportar e certifique-se de que os dados são compatíveis com a tabela de destino.
 - Certifique-se de que o SQL iniciar sessão associado ao nome de utilizador e palavra-passe tem permissões para escrever na tabela. 
 - Se a exceção contém informações de erro adicionais do SQL Server, use essas informações para fazer correções.  
  
|Mensagens de exceção|  
|------------------------|  
|Ligado ao servidor, não é possível escrever à tabela.|  
|Não é possível escrever a tabela de Sql: {0}|  


## <a name="error-0122"></a>Erro 0122  
 Ocorre uma exceção se forem especificadas várias colunas de peso e apenas um é permitido.  
  
 Este erro no Azure Machine Learning ocorre quando foram selecionadas demasiadas colunas como colunas de peso.  
  
**Resolução:** Reveja o conjunto de dados de entrada e seus metadados. Certifique-se de que apenas uma coluna contém pesos.  
  
|Mensagens de exceção|  
|------------------------|  
|Foram especificadas várias colunas de peso.|  


## <a name="error-0123"></a>Erro 0123  
 Ocorre uma exceção se a coluna de vetores está especificada a coluna de etiqueta.  
  
 Este erro no Azure Machine Learning ocorre se usar um vetor como a coluna de etiqueta.  
  
**Resolução:** Alterar o formato de dados da coluna, se necessário, ou escolha uma coluna diferente.  
  
|Mensagens de exceção|  
|------------------------|  
|Coluna de vetores está especificada como coluna de etiqueta.|  


## <a name="error-0124"></a>Erro 0124  
 Ocorre uma exceção se não numéricos colunas são especificadas para ser a coluna de peso.  
  
**Resolução:**
  
|Mensagens de exceção|  
|------------------------|  
|Coluna de não numéricas é especificada como a coluna de peso.|  
  


## <a name="error-0125"></a>Erro 0125  
 Lançada quando não corresponde ao esquema para vários conjuntos de dados.  
  
**Resolução:**
  
|Mensagens de exceção|  
|------------------------|  
|Esquema de conjunto de dados não corresponde.|  


## <a name="error-0126"></a>Erro 0126  
 Ocorre uma exceção se o usuário especificar um domínio SQL que não é suportado no Azure ML.  
  
 Este erro é produzido quando o usuário Especifica um domínio SQL que não é suportado no Azure Machine Learning. Irá receber este erro se está a tentar ligar a um servidor de base de dados num domínio que não está na lista de permissões. Atualmente, os domínios SQL permitidos são: ". database.windows.net",". cloudapp.net", ou ". database.secure.windows.net". Ou seja, o servidor tem de ser um servidor SQL do Azure ou um servidor numa máquina virtual no Azure.  
  
**Resolução:** Examine o módulo. Certifique-se de que o servidor de base de dados SQL pertence a um dos domínios aceites:  
  
-   .database.windows.net  
  
-   .cloudapp.net  
  
-   .database.secure.windows.net  
  
|Mensagens de exceção|  
|------------------------|  
|Domínio SQL não suportado.|  
|O domínio SQL {0} não é atualmente suportado no Azure ML|  
  

## <a name="error-0127"></a>Erro 0127  
 Tamanho de pixel de imagem excede o limite permitido  
  
 Este erro ocorre se estiver lendo as imagens de um conjunto de dados de imagem para classificação e as imagens são maiores do que pode processar o modelo.  
  
 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  
  
-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->
  
|Mensagens de exceção|  
|------------------------|  
|Tamanho de pixel de imagem excede o limite permitido.|  
|Tamanho de pixel no arquivo da imagem "{0}' excede o limite permitido:"{1}'|  


## <a name="error-0128"></a>Erro 0128  
 Número de probabilidades condicionais para colunas categóricas excede o limite.  
  
**Resolução:**
  
|Mensagens de exceção|  
|------------------------|  
|Número de probabilidades condicionais para colunas categóricas excede o limite.|  
|Número de probabilidades condicionais para colunas categóricas excede o limite. Das colunas{0}"e"{1}"são o par problemático.|  


## <a name="error-0129"></a>Erro 0129  
 Número de colunas no conjunto de dados excede o limite permitido.  
  
**Resolução:**
  
|Mensagens de exceção|  
|------------------------|  
|Número de colunas no conjunto de dados excede o limite permitido.|  
|Número de colunas no conjunto de dados em "{0}"excede permitido."|  
|Número de colunas no conjunto de dados no '{0}"excede o limite permitido de"{1}'.'|  
|Número de colunas no conjunto de dados no '{0}"excede permitido"{1}"limite de"{2}'.'|  
## <a name="error-0130"></a>Erro 0130  
 Ocorre uma exceção quando todas as linhas no conjunto de dados de treinamento contém valores em falta.  
  
 Isto ocorre quando algumas colunas no conjunto de dados de treinamento está vazia.  
  
**Resolução:** Utilize o [Clean Missing Data](clean-missing-data.md) módulo para remover colunas com todos os valores em falta.  
  
|Mensagens de exceção|  
|------------------------|  
|Todas as linhas no conjunto de dados de treinamento contêm valores em falta.  Considere utilizar o módulo de apagar dados em falta para remover valores em falta.|  
 

## <a name="error-0131"></a>Erro 0131  
 Ocorre uma exceção se uma ou mais conjuntos de dados num arquivo zip não conseguir ser descompactei e registrado corretamente  
  
 Este erro é produzido quando um ou mais conjuntos de dados num arquivo zip não consegue ser descompactei e ler corretamente. Irá receber este erro se o descompactar falhar porque o próprio ficheiro zip ou um dos ficheiros na mesma está danificado ou existe um erro de sistema ao tentar descompactar e expandir um ficheiro.  
  
**Resolução:** Utilize os detalhes fornecidos na mensagem de erro para determinar como proceder.  
  
|Mensagens de exceção|  
|------------------------|  
|Carregar conjuntos de dados zipados falhou|  
|Conjunto de dados de comprimido {0} falhou com a seguinte mensagem: {1}|  
|Comprimido o conjunto de dados {0} falhou com um {1} exceção com a mensagem: {2}|  
  

## <a name="error-0132"></a>Erro 0132  
 Foi especificado nenhum nome de ficheiro para descompactar; vários arquivos foram encontrados no arquivo zip.  
  
 Este erro é produzido quando foi especificado nenhum nome de ficheiro para descompactar; vários arquivos foram encontrados no arquivo zip. Irá receber este erro se o ficheiro. zip contém mais do que um ficheiro comprimido, mas não especificou um ficheiro de extração no **conjunto de dados para Unpack** caixa de texto, além do **propriedade** painel do módulo. Atualmente, apenas um ficheiro pode ser extraído sempre que o módulo for executado.  
  
**Resolução:** A mensagem de erro fornece uma lista dos ficheiros encontrados no arquivo. zip. Copie o nome do ficheiro pretendido e cole-o para o **conjunto de dados para Unpack** caixa de texto.  
  
|Mensagens de exceção|  
|------------------------|  
|Ficheiro zip contém vários ficheiros; tem de especificar o ficheiro para expandir.|  
|O ficheiro contém mais de um ficheiro. Especifique o ficheiro para expandir. Foram encontrados os seguintes ficheiros: {0}|  
  

## <a name="error-0133"></a>Erro 0133  
 O ficheiro especificado não foi encontrado no ficheiro zip  
  
 Este erro é produzido quando o nome do ficheiro introduzido na **conjunto de dados para Unpack** campo a **propriedade** painel não corresponde ao nome de qualquer ficheiro encontrado no arquivo. zip. As causas mais comuns deste erro são um erro de digitação ou procurar o ficheiro de arquivo errada para o ficheiro expandir.  
  
**Resolução:** Examine o módulo. Se o nome do ficheiro que se destina a descomprimir aparece na lista de arquivos encontrados, copie o nome de ficheiro e cole-o para o **conjunto de dados para Unpack** caixa da propriedade. Se não vir o nome de ficheiro pretendido na lista, certifique-se de que tem o ficheiro. zip correto e o nome correto para o arquivo desejado.  
  
|Mensagens de exceção|  
|------------------------|  
|O ficheiro especificado não foi encontrado o ficheiro zip de int.|  
|O ficheiro especificado não foi encontrado. Foram encontrados os seguintes ficheiros: {0}|  
  

## <a name="error-0134"></a>Erro 0134
Exceção ocorre quando a coluna de etiqueta está em falta ou tem um número insuficiente de linhas com nome.  
  
Este erro ocorre quando o módulo requer uma coluna de etiqueta, mas não incluía um na seleção de coluna ou a coluna de etiqueta está em falta demasiados valores.

Este erro também pode ocorrer quando uma operação anterior altera o conjunto de dados que linhas insuficientes estão disponíveis para uma operação de downstream. Por exemplo, suponha que use uma expressão na **partição e amostras** módulo dividir um conjunto de dados por valores. Se não forem encontradas correspondências para a sua expressão, um dos conjuntos de dados resultante da partição deve estar vazio.

Resolução: 

 Se incluir uma coluna de etiqueta na seleção de coluna, mas não é reconhecido, utilize o [Editar metadados](edit-metadata.md) módulo para marcá-lo como uma coluna de etiqueta.
  
  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->Em seguida, pode utilizar o [Clean Missing Data](clean-missing-data.md) módulo para remover linhas com valores em falta na coluna de etiqueta. 

 Verifique os conjuntos de dados de entrada para se certificar de que eles contenham dados válidos e linhas suficientes para satisfazer os requisitos da operação. Muitos algoritmos irão gerar uma mensagem de erro, se eles exigem algumas linhas de número mínimo de dados, mas os dados contêm apenas algumas linhas, ou apenas um cabeçalho.
  
|Mensagens de exceção|
|------------------------|
|Exceção ocorre quando a coluna de etiqueta está em falta ou tem um número insuficiente de linhas com nome.|  
|Exceção ocorre quando a coluna de etiqueta está em falta ou tem menos de {0} rotulado como linhas|  
  

## <a name="error-0135"></a>Erro 0135  
 Com base em centroide o cluster só é suportado.  
  
**Resolução:** Poderá encontrar esta mensagem de erro se foi efetuada uma tentativa avaliar um modelo de clustering que se baseia num algoritmo de clustering personalizado que não utiliza centróides para inicializar o cluster.  
  
  <!--You can use [Evaluate Model](evaluate-model.md) to evaluate clustering models that are based on the  [K-Means Clustering](k-means-clustering.md) module. For custom algorithms, use the [Execute R Script](execute-r-script.md) module to create a custom evaluation script.  -->
  
|Mensagens de exceção|  
|------------------------|  
|Com base em centroide o cluster só é suportado.|  
  

## <a name="error-0136"></a>Erro 0136  
 Não foi devolvido nenhum nome de ficheiro; Não é possível processar o ficheiro como resultado.  
  
**Resolução:**
  
|Mensagens de exceção|  
|------------------------|  
|Não foi devolvido nenhum nome de ficheiro; Não é possível processar o ficheiro como resultado.|  
  

## <a name="error-0137"></a>Erro 0137  
 SDK de armazenamento do Azure encontrou um erro ao converter entre propriedades de tabela e colunas do conjunto de dados durante a leitura ou escrita.  
  
**Resolução:**
  
|Mensagens de exceção|  
|------------------------|  
|Erro de conversão entre a coluna de propriedade e o conjunto de dados do armazenamento de tabelas do Azure.|  
|Erro de conversão entre a coluna de propriedade e o conjunto de dados do armazenamento de tabelas do Azure. Informações adicionais: {0}|  

## <a name="error-0138"></a>Error 0138  
 Memória foi esgotada, não é possível para a execução completa do módulo. Downsampling o conjunto de dados pode ajudar a minimizar o problema.  
  
 Este erro ocorre quando o módulo que está a executar requer mais memória do que está disponível no contentor do Azure. Isto pode acontecer se estiver a trabalhar com um grande conjunto de dados e a operação atual não é possível se encaixam na memória.  
  
**Resolução:** Se estiver a tentar ler um grande conjunto de dados e não é possível concluir a operação, downsampling o conjunto de dados pode ajudar.  
  
  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  
  
 Sometimes transient loads can lead to this error. Machine support also changes over time. 
  
 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->
  
|Mensagens de exceção|  
|------------------------|  
|Memória foi esgotada, não é possível para a execução completa do módulo.|  
  

## <a name="error-0139"></a>Erro 0139  
 Exceção ocorre quando não é possível converter uma coluna a outro tipo.  
  
 Este erro no Azure Machine Learning ocorre quando tentar converter uma coluna para um tipo de dados diferentes, mas que tipo não é suportado a operação atual ou o módulo.  
  
 Também pode aparecer o erro quando um módulo tenta converter implicitamente os dados para atender aos requisitos do módulo atual, mas a conversão não é possível.  
  
**Resolução:**

1. Reveja os dados de entrada e determinar o tipo de exatamente os dados da coluna que pretende utilizar e o tipo de dados da coluna que produz o erro. Por vezes, pode pensar o tipo de dados está correto, mas encontrar o que uma operação a montante tiver modificado o tipo de dados ou a utilização de uma coluna. Utilize o [Editar metadados](edit-metadata.md) módulo repor os metadados da coluna para seu estado original. 
2. Ver a página de ajuda do módulo para verificar os requisitos para a operação especificada. Determine que tipos de dados são suportados pelo módulo de atual e o intervalo de valores é suportado. 
 <!--3. If values need to be truncated, rounded, or outliers removed, use the [Apply Math Operation](apply-math-operation.md) or [Clip Values](clip-values.md) modules to make corrections.-->
4. Considere se é possível converter ou converter a coluna para um tipo de dados diferentes. Os seguintes módulos todos os fornecem considerável flexibilidade e poder para modificar os dados: 
 <!--
   + [Apply SQL Transformation](apply-sql-transformation.md)
   + [Execute R Script](execute-r-script.md)
-->   
   + [Executar o Script de Python](execute-python-script.md).  

> [!NOTE]
> Ainda não funcionar? Considere fornecer comentários adicionais sobre o problema, para ajudar-na desenvolver as orientações para a melhor resolução. Basta enviar comentários sobre esta página e forneça o nome do módulo que gerou o erro e a conversão de tipo de dados que falhou.
  
|Mensagens de exceção|  
|------------------------|  
|Não permitido conversão.|  
|Não foi possível converter: {0}.|  
|Não foi possível converter: {0}, na linha {1}.|  
|Não foi possível converter a coluna do tipo {0} a coluna do tipo {1} na linha {2}.|  
|Não foi possível converter a coluna "{2}" do tipo {0} a coluna do tipo {1} na linha {3}.|  
|Não foi possível converter a coluna "{2}" do tipo {0} a coluna "{3}" do tipo {1} na linha {4}.| 

## <a name="error-0140"></a>Erro 0140  
 Ocorre uma exceção se transmitido argumento de conjunto de colunas não contém outras colunas, exceto a coluna de etiqueta.  
  
 Este erro ocorre se estiver ligado um conjunto de dados a um módulo que requer várias colunas, incluindo recursos, mas que forneceu apenas a coluna de etiqueta.  
  
**Resolução:** Escolha pelo menos uma coluna de funcionalidade para incluir no conjunto de dados.  
  
|Mensagens de exceção|  
|------------------------|  
|Conjunto de colunas especificado não contém outras colunas, exceto a coluna de etiqueta.|  
  

## <a name="error-0141"></a>Erro 0141  
 Ocorre uma exceção se o número das colunas numéricas selecionadas e valores exclusivos na categórica e as colunas de cadeia de caracteres é demasiado pequeno.  
  
 Este erro no Azure Machine Learning ocorre quando não existem suficientes valores exclusivos na coluna selecionada para efetuar a operação.  
  
**Resolução:** Algumas operações de efetuar operações estatísticas no recurso e colunas categóricas e se não forem suficientes valores, a operação poderá falhar ou devolver um resultado inválido. Verifique o seu conjunto de dados para ver quantos valores existem nas colunas de funcionalidade e a etiqueta e determinar se a operação que está a tentar executar é estatisticamente válida.  
  
 Se o conjunto de dados de origem for válido, também pode verificar se uma operação de manipulação ou metadados de dados a montante tem os dados alterados e removido alguns valores.  
  
 Se as operações a montante incluem a divisão, amostragem ou resampling, certifique-se de que as saídas contém o número esperado de linhas e valores.  
  
|Mensagens de exceção|  
|------------------------|  
|O número das colunas numéricas selecionadas e valores exclusivos na categórica e as colunas de cadeia de caracteres é demasiado pequeno.|  
|O número total das colunas numéricas selecionadas e valores exclusivos na categórica e as colunas de cadeia de caracteres (atualmente {0}) deve ser, pelo menos, {1}|  
  

## <a name="error-0142"></a>Erro 0142  
 Exceção ocorre quando o sistema não é possível carregar o certificado para autenticar.  
  
**Resolução:**
  
|Mensagens de exceção|  
|------------------------|  
|Não é possível carregar o certificado.|  
|O certificado {0} não pode ser carregado. Sua impressão digital é {1}.|  
  

## <a name="error-0143"></a>Erro 0143  
 Não é possível analisar o URL fornecido pelo utilizador que deve para ser a partir do GitHub.  
  
 Este erro no Azure Machine Learning ocorre quando especificar um URL inválido e o módulo requer um URL válido do GitHub.  
  
**Resolução:** Certifique-se de que o URL se refere a um repositório do GitHub válido. Não são suportados outros tipos de site.  
  
|Mensagens de exceção|  
|------------------------|  
|URL não é do github.com.|  
|O URL não é do github.com: {0}|  

## <a name="error-0144"></a>Erro 0144  
 Utilizador url fornecido pelo GitHub está em falta a parte esperada.  
  
 Este erro no Azure Machine Learning ocorre quando especificar uma origem de ficheiro do GitHub com um formato de URL inválido.  
  
**Resolução:** Verifique se o URL do repositório do GitHub é válido e termina com \blob\ ou \tree\\.  
  
|Mensagens de exceção|  
|------------------------|  
|Não é possível analisar o URL do GitHub.|  
|Não é possível analisar o URL do GitHub (era esperado ' \blob\\' ou ' \tree\\"após o nome do repositório): {0}|  

## <a name="error-0145"></a>Erro 0145  
 Não é possível criar o diretório de replicação por algum motivo.  
  
 Este erro no Azure Machine Learning ocorre quando o módulo não consegue criar o diretório especificado.  
  
**Resolução:**
  
|Mensagens de exceção|  
|------------------------|  
|Não é possível criar o diretório de replicação.|  
  

## <a name="error-0146"></a>Erro 0146  
 Quando os ficheiros de utilizador estão descompactados para o diretório local, o caminho combinado pode ser demasiado longo.  
  
 Este erro no Azure Machine Learning ocorre quando está a extrair arquivos, mas alguns nomes de ficheiro são demasiado longos quando descompactei.  
  
**Resolução:** Edite os nomes dos ficheiros, de modo a que combinados caminho e nome de ficheiro é não ultrapassa a 248 carateres.  
  
|Mensagens de exceção|  
|------------------------|  
|Caminho de replicação tiver mais de 248 carateres, Encurte o nome do script ou o caminho.|  

## <a name="error-0147"></a>Erro 0147  
 Não foi possível transferir coisas do GitHub por algum motivo  
  
 Este erro no Azure Machine Learning ocorre quando não é possível ler ou transferir os ficheiros especificados a partir do GitHub.  
  
**Resolução:** O problema poderá ser temporário; poderia tentar aceder aos ficheiros em outro momento. Ou certifique-se de que tem as permissões necessárias e que a origem é válida.  
  
|Mensagens de exceção|  
|------------------------|  
|Erro de acesso do GitHub.|  
|Erro de acesso do GitHub. {0}|  
  

## <a name="error-0148"></a>Erro 0148  
 Problemas de acesso não autorizado ao extrair dados ou a criar o diretório.  
  
 Este erro no Azure Machine Learning ocorre quando está tentando criar um diretório ou ler os dados do armazenamento, mas não tem as permissões necessárias.  
  
**Resolução:**
  
|Mensagens de exceção|  
|------------------------|  
|Exceção de acesso não autorizado ao extrair dados.|  
  

## <a name="error-0149"></a>Erro 0149  
 O ficheiro de utilizador não existe no interior do pacote do GitHub.  
  
 Este erro no Azure Machine Learning ocorre quando não é possível localizar o ficheiro especificado.  
  
Resolução: 
  
|Mensagens de exceção|  
|------------------------|  
|Não foi encontrado o ficheiro do GitHub.|  
|Arquivo do GitHub não for encontrado.: {0}|  
  

## <a name="error-0150"></a>Erro 0150  
 Os scripts que são provenientes do pacote de utilizador não foi possível descompactados, provavelmente devido a uma colisão de ficheiros do GitHub.  
  
 Este erro no Azure Machine Learning ocorre quando um script não é possível extrair, normalmente, quando existe um ficheiro existente, o mesmo nome.  
  
Resolução:
  
|Mensagens de exceção|  
|------------------------|  
|Não é possível descomprimir o pacote; colisão de nomes de possíveis com os ficheiros do GitHub.|  
  

## <a name="error-0151"></a>Erro 0151  
 Ocorreu um erro ao escrever no armazenamento na nuvem. Verifique o URL.  
  
 Este erro no Azure Machine Learning ocorre quando o módulo tenta gravar dados no armazenamento na cloud, mas o URL está indisponível ou é inválido.  
  
Resolução: Verifique o URL e certifique-se de que é gravável.  
  
|Mensagens de exceção|  
|------------------------|  
|Erro ao escrever (possivelmente uma url ruim) de armazenamento na nuvem.|  
|Erro ao escrever no armazenamento na nuvem: {0}. Verifique o url.|  
  
## <a name="error-0152"></a>Erro 0152  
 O tipo de cloud do Azure foi incorretamente especificado no contexto do módulo.  
  
|Mensagens de exceção|  
|------------------------|  
|Tipo de nuvem do Azure|  
|Tipo de nuvem do Azure: {0}|  
  
## <a name="error-0153"></a>Erro 0153  
 O ponto de final de armazenamento especificado é inválido.  
  
|Mensagens de exceção|  
|------------------------|  
|Tipo de nuvem do Azure|  
|Ponto de final de armazenamento ruim: {0}|  

## <a name="error-0154"></a>Error 0154  
 Não foi possível resolver o nome de servidor especificado  
  
|Mensagens de exceção|  
|------------------------|  
|Não foi possível resolver o nome de servidor especificado|  
|O servidor especificado {0}. não foi possível resolver documents.azure.com|

## <a name="error-0155"></a>Erro 0155  
 O cliente de DocDb emitiu uma exceção  
  
|Mensagens de exceção|  
|------------------------|  
|O cliente de DocDb emitiu uma exceção|  
|Cliente de DocDb: {0}|

## <a name="error-0156"></a>Erro 0156  
 Resposta incorreta para o servidor de HCatalog.  
  
|Mensagens de exceção|  
|------------------------|  
|Resposta incorreta para o servidor de HCatalog. Verifique se todos os serviços estão em execução.|  
|Resposta incorreta para o servidor de HCatalog. Verifique se todos os serviços estão em execução. Detalhes do erro: {0}|

## <a name="error-0157"></a>Erro 0157  
 Ocorreu um erro ao ler a partir do Azure Cosmos DB devido a esquemas inconsistentes ou outro documento. Leitor requer que todos os documentos para ter o mesmo esquema.  
  
|Mensagens de exceção|  
|------------------------|  
|Detetado documentos com esquemas diferentes. Certifique-se de que todos os documentos com o mesmo esquema|

## <a name="error-1000"></a>Erro de 1000  
Exceção da biblioteca interna.  
  
Este erro é fornecido capturar, caso contrário, não processada erros de motor interno. Por conseguinte, a causa deste erro poderá ser diferente consoante o módulo que gerou o erro.  
  
Para obter mais ajuda, recomendamos que publique a mensagem detalhada que acompanha o erro para o Fórum do Azure Machine Learning, juntamente com uma descrição do cenário, incluindo os dados utilizados como entradas. Estes comentários nos ajudarão a priorizar erros e identificar os problemas mais importantes para o trabalho adicional.  
  
|Mensagens de exceção|  
|------------------------|  
|Exceção de biblioteca.|  
|Exceção de biblioteca: {0}|  
|{0} Exceção de biblioteca: {1}|  
