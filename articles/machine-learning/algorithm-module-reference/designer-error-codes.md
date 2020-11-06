---
title: Erros do módulo de design de resolução de problemas
titleSuffix: Azure Machine Learning
description: Saiba como pode ler e resolver códigos de erro automatizados do módulo no designer de Machine Learning Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: troubleshooting
author: likebupt
ms.author: keli19
ms.date: 04/16/2020
ms.openlocfilehash: 569cf130b464d97e0ac10904ffd86365b57610a5
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420840"
---
# <a name="exceptions-and-error-codes-for-the-designer"></a>Exceções e códigos de erro para o designer

Este artigo descreve as mensagens de erro e os códigos de exceção no designer de Aprendizagem automática Azure para ajudá-lo a resolver problemas nos seus oleodutos de aprendizagem automática.

Pode encontrar a mensagem de erro no designer seguindo estes passos:  

- Selecione o módulo falhado, vá ao separador **Saídas+logs,** pode encontrar o registo detalhado no ficheiro **70_driver_log.txt** na categoria **de registos azureml.**

- Para um erro detalhado do módulo, pode verificar na error_info.jsna categoria **module_statistics.**

Seguem-se códigos de erro de módulos no designer.

## <a name="error-0001"></a>Erro 0001  
 A exceção ocorre se uma ou mais colunas especificadas de conjunto de dados não puderem ser encontradas.  

 Receberá este erro se for feita uma seleção de colunas para um módulo, mas as colunas selecionadas não existem no conjunto de dados de entrada. Este erro pode ocorrer se tiver digitado manualmente um nome de coluna ou se o seletor de colunas tiver fornecido uma coluna sugerida que não existisse no seu conjunto de dados quando executou o pipeline.  

**Resolução:** Reveja o módulo que lança esta exceção e valide que o nome ou nomes da coluna estão corretos e que todas as colunas referenciadas existem.  

|Mensagens de Exceção|
|------------------------|
|Não foram encontradas uma ou mais colunas especificadas.|
|Coluna com nome ou índice "{column_id}" não encontrada.|
|Coluna com nome ou índice "{column_id}" não existe em "{arg_name_missing_column}".|
|Coluna com nome ou índice "{column_id}" não existe em "{arg_name_missing_column}", mas existe em "{arg_name_has_column}".|
|Colunas com nome ou índice "{column_names}" não encontradas.|
|Colunas com nome ou índice "{column_names}" não existem em "{arg_name_missing_column}".|
|Colunas com nome ou índice "{column_names}" não existem em "{arg_name_missing_column}", mas existem em "{arg_name_has_column}".|


## <a name="error-0002"></a>Erro 0002  
 A exceção ocorre se um ou mais parâmetros não puderem ser analisados ou convertidos do tipo especificado para o tipo de método-alvo.  

 Este erro ocorre no Azure Machine Learning quando especifica um parâmetro como entrada e o tipo de valor é diferente do tipo que é esperado, e a conversão implícita não pode ser realizada.  

**Resolução:** Verifique os requisitos do módulo e determine qual o tipo de valor necessário (corda, inteiro, duplo, etc.)  

|Mensagens de Exceção|
|------------------------|
|Falhou em analisar o parâmetro.|
|Não analisou o parâmetro "{arg_name_or_column}".|
|Falhou na conversão do parâmetro "{arg_name_or_column}" para "{to_type}".|
|Falhou na conversão do parâmetro "{arg_name_or_column}" de "{from_type}" para "{to_type}".|
|Falhou na conversão do valor do parâmetro "{arg_name_or_column}" "{arg_value}" de "{from_type}" para "{to_type}".|
|Falhou na conversão do valor "{arg_value}" na coluna "{arg_name_or_column}" de "{from_type}" para "{to_type}" com a utilização do formato "{fmt}" fornecido.|


## <a name="error-0003"></a>Erro 0003  
 A exceção ocorre se uma ou mais entradas forem nulas ou vazias.  

 Receberá este erro no Azure Machine Learning se quaisquer entradas ou parâmetros de um módulo estiverem nulos ou vazios.  Este erro pode ocorrer, por exemplo, quando não digitou qualquer valor para um parâmetro. Também pode acontecer se escolher um conjunto de dados que tenha valores em falta ou um conjunto de dados vazio.  

**Resolução:**

+ Abra o módulo que produziu a exceção e verifique se todas as entradas foram especificadas. Certifique-se de que todas as entradas necessárias são especificadas. 
+ Certifique-se de que os dados que são carregados a partir do armazenamento do Azure estão acessíveis e que o nome ou chave da conta não foi alterado.  
+ Verifique os dados de entrada para obter valores em falta ou nulos.
+ Se utilizar uma consulta numa fonte de dados, verifique se os dados estão a ser devolvidos no formato que espera. 
+ Verifique se há tipografias ou outras alterações na especificação dos dados.
  
|Mensagens de Exceção|
|------------------------|
|Uma ou mais entradas são nulas ou vazias.|
|A entrada "{name}" é nula ou vazia.|


## <a name="error-0004"></a>Erro 0004  
 A exceção ocorre se o parâmetro for inferior ou igual ao valor específico.  

 Receberá este erro no Azure Machine Learning se o parâmetro da mensagem estiver abaixo de um valor limite necessário para que o módulo processe os dados.  

**Resolução:** Reveja o módulo lançando a exceção e modifique o parâmetro para ser maior do que o valor especificado.  

|Mensagens de Exceção|
|------------------------|
|O parâmetro deve ser maior do que o valor limite.|
|O valor do parâmetro "{arg_name}" deve ser maior do que {lower_boundary}.|
|O parâmetro "{arg_name}" tem valor "{atual_value}" que deve ser maior do que {lower_boundary}.|


## <a name="error-0005"></a>Erro 0005  
 A exceção ocorre se o parâmetro for inferior a um valor específico.  

 Receberá este erro no Azure Machine Learning se o parâmetro da mensagem estiver abaixo ou igual a um valor limite necessário para que o módulo processe os dados.  

**Resolução:** Reveja o módulo lançando a exceção e modifique o parâmetro para ser maior ou igual ao valor especificado.  

|Mensagens de Exceção|
|------------------------|
|O parâmetro deve ser maior ou igual ao valor limite.|
|O valor do parâmetro "{arg_name}" deve ser maior ou igual a {lower_boundary}.|
|O parâmetro "{arg_name}" tem o valor "{value}" que deve ser maior ou igual a {lower_boundary}.|


## <a name="error-0006"></a>Erro 0006  
 A exceção ocorre se o parâmetro for maior ou igual ao valor especificado.  

 Receberá este erro no Azure Machine Learning se o parâmetro da mensagem for maior ou igual a um valor limite necessário para que o módulo processe os dados.  

**Resolução:** Reveja o módulo lançando a exceção e modifique o parâmetro para ser inferior ao valor especificado.  

|Mensagens de Exceção|
|------------------------|
|Parâmetros incompatíveis. Um dos parâmetros deve ser menos que outro.|
|O valor do parâmetro "{arg_name}" deve ser inferior ao valor do parâmetro "{upper_boundary_parameter_name}".|
|O parâmetro "{arg_name}" tem valor "{value}" que deve ser inferior a {upper_boundary_parameter_name}.|


## <a name="error-0007"></a>Erro 0007  
 A exceção ocorre se o parâmetro for maior do que um valor específico.  

 Receberá este erro no Azure Machine Learning se, nas propriedades do módulo, tiver especificado um valor superior ao permitido. Por exemplo, pode especificar um dado que esteja fora do alcance das datas suportadas, ou pode indicar que cinco colunas são usadas quando apenas três colunas estão disponíveis. 

 Pode também ver este erro se estiver a especificar dois conjuntos de dados que precisam de coincidir de alguma forma. Por exemplo, se estiver a renomear colunas e especificar as colunas por índice, o número de nomes que fornece deve corresponder ao número de índices de colunas. Outro exemplo pode ser uma operação matemática que usa duas colunas, onde as colunas devem ter o mesmo número de linhas. 

**Resolução:**

 + Abra o módulo em questão e reveja as definições de propriedade numérica.
 + Certifique-se de que quaisquer valores de parâmetros se enquadram na gama de valores suportado para esse imóvel.
 + Se o módulo necessitar de várias entradas, certifique-se de que as entradas são do mesmo tamanho.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Verifique se o conjunto de dados ou a fonte de dados mudaram. Por vezes, um valor que funcionou com uma versão anterior dos dados falhará após o número de colunas, os tipos de dados da coluna ou o tamanho dos dados terem mudado.  

|Mensagens de exceção|
|------------------------|
|Parâmetros incompatíveis. Um dos parâmetros deve ser inferior ou igual a outro.|
|O valor do parâmetro "{arg_name}" deve ser inferior ou igual ao valor do parâmetro "{upper_boundary_parameter_name}".|
|O parâmetro "{arg_name}" tem o valor "{atual_value}" que deve ser inferior ou igual a {upper_boundary}.|
|O valor do parâmetro "{arg_name}" {atual_value} deve ser inferior ou igual ao valor {upper_boundary_parameter_name}" {upper_boundary}.|
|O valor do parâmetro "{arg_name}" {atual_value} deve ser inferior ou igual ao valor {upper_boundary_meaning} {upper_boundary}.|


## <a name="error-0008"></a>Erro 0008  
 A exceção ocorre se o parâmetro não estiver ao alcance.  

 Receberá este erro no Azure Machine Learning se o parâmetro da mensagem estiver fora dos limites necessários para que o módulo processe os dados.  

 Por exemplo, este erro é apresentado se tentar utilizar [Add Rows](add-rows.md) para combinar dois conjuntos de dados que têm um número diferente de colunas.  

**Resolução:** Reveja o módulo lançando a exceção e modifique o parâmetro para estar dentro do intervalo especificado.  

|Mensagens de Exceção|
|------------------------|
|O valor do parâmetro não está no intervalo especificado.|
|O valor do parâmetro "{arg_name}" não está ao alcance.|
|O valor do parâmetro "{arg_name}" deve estar na gama de [{lower_boundary}, {upper_boundary}].|
|O valor do parâmetro "{arg_name}" não está ao alcance. {razão}|


## <a name="error-0009"></a>Erro 0009  
 A exceção ocorre quando o nome da conta de armazenamento Azure ou o nome do recipiente são especificados incorretamente.  

Este erro ocorre no designer de Machine Learning Azure quando especifica parâmetros para uma conta de armazenamento Azure, mas o nome ou senha não pode ser resolvido. Erros em palavras-passe ou nomes de contas podem acontecer por muitas razões:

 + A conta é do tipo errado. Alguns novos tipos de conta não são suportados para uso com o designer de Machine Learning. Consulte [os Dados de Importação](import-data.md) para mais detalhes.
 + Inseriu o nome da conta incorreta
 + A conta já não existe
 + A palavra-passe para a conta de armazenamento está errada ou mudou
 + Não especificou o nome do recipiente, ou o contentor não existe.
 + Não especificou completamente o caminho do ficheiro (caminho para a bolha)
   

**Resolução:**

Estes problemas ocorrem frequentemente quando se tenta introduzir manualmente o nome da conta, a palavra-passe ou o caminho do contentor. Recomendamos que utilize o novo assistente para o módulo [de Dados de Importação,](import-data.md) o que o ajuda a procurar e verificar nomes.

Verifique também se a conta, o recipiente ou a bolha foram apagados. Utilize outro utilitário de armazenamento Azure para verificar se o nome da conta e a palavra-passe foram introduzidos corretamente e que o recipiente existe. 

Alguns tipos de conta mais recentes não são suportados pela Azure Machine Learning. Por exemplo, os novos tipos de armazenamento "quente" ou "frio" não podem ser utilizados para a aprendizagem automática. Tanto as contas clássicas de armazenamento como as contas de armazenamento criadas como "finalidade geral" funcionam bem.

Se o caminho completo para uma bolha for especificado, verifique se o caminho é especificado como **recipiente/blobname** , e que tanto o recipiente como o blob existem na conta.  

 O caminho não deve conter um corte de liderança. Por **exemplo/recipiente/bolha** está incorreto e deve ser introduzido como **recipiente/bolha**.  


|Mensagens de Exceção|
|------------------------|
|O nome da conta de armazenamento Azure ou o nome do recipiente estão incorretos.|
|O nome da conta de armazenamento Azure "{account_name}" ou o nome do recipiente "{container_name}" está incorreto; Esperava-se um nome de contentor do recipiente/bolha de formato.|


## <a name="error-0010"></a>Erro 0010  
 A exceção ocorre se os conjuntos de dados de entrada tiverem nomes de colunas que devem coincidir, mas não correspondem.  

 Receberá este erro no Azure Machine Learning se o índice de coluna na mensagem tiver diferentes nomes de colunas nos dois conjuntos de dados de entrada.  

**Resolução:** Utilize [Metadados de Edição](edit-metadata.md) ou modificar o conjunto de dados original para ter o mesmo nome de coluna para o índice de coluna especificado.  

|Mensagens de Exceção|
|------------------------|
|Colunas com índice correspondente em conjuntos de dados de entrada têm nomes diferentes.|
|Os nomes das colunas não são os mesmos para a coluna {col_index} (com base em zero) dos conjuntos de dados de entrada ({dataset1} e {dataset2} respectivamente).|


## <a name="error-0011"></a>Erro 0011  
 A exceção ocorre se o argumento de conjunto de colunas aprovada não se aplicar a nenhuma das colunas do conjunto de dados.  

 Receberá este erro no Azure Machine Learning se a seleção de colunas especificada não corresponder a nenhuma das colunas do conjunto de dados dados.  

 Também pode obter este erro se não tiver selecionado uma coluna e pelo menos uma coluna for necessária para que o módulo funcione.  

**Resolução:** Modifique a seleção da coluna no módulo de modo a que se aplique às colunas do conjunto de dados.  

 Se o módulo exigir que selecione uma coluna específica, como uma coluna de etiqueta, verifique se a coluna certa está selecionada.  

 Se forem selecionadas colunas inadequadas, retire-as e reenquadrem a tubagem.  

|Mensagens de Exceção|
|------------------------|
|O conjunto de colunas especificado não se aplica a nenhuma das colunas do conjunto de dados.|
|O conjunto de colunas especificado "{column_set}" não se aplica a nenhuma das colunas do conjunto de dados.|


## <a name="error-0012"></a>Erro 0012  
 A exceção ocorre se a instância de classe não puder ser criada com um conjunto de argumentos aprovados.  

**Resolução:** Este erro não é exequível pelo utilizador e será depreciado numa futura versão.  

|Mensagens de Exceção|
|------------------------|
|Modelo destreinado, por favor treine o modelo primeiro.|
|Modelo não treinado ({arg_name}), utilize modelo treinado.|


## <a name="error-0013"></a>Erro 0013  
 A exceção ocorre se o aluno passar para o módulo é um tipo inválido.  

 Este erro ocorre sempre que um modelo treinado é incompatível com o módulo de pontuação ligado. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->

**Resolução:**

Determine o tipo de aprendiz que é produzido pelo módulo de treino e determine o módulo de pontuação adequado para o aluno. 

Se o modelo foi treinado utilizando qualquer um dos módulos de formação especializados, ligue o modelo treinado apenas ao módulo de pontuação especializado correspondente. 


|Tipo de modelo|Módulo de formação| Módulo de pontuação|
|----|----|----|
|qualquer classificador|[Preparar Modelo](train-model.md) |[Modelo de Classificação](score-model.md)|
|qualquer modelo de regressão|[Preparar Modelo](train-model.md) |[Modelo de Classificação](score-model.md)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The module does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->

|Mensagens de Exceção|
|------------------------|
|Aprendendo do tipo inválido é passado.|
|O aprendiz "{arg_name}" tem um tipo inválido.|
|O aprendiz "{arg_name}" tem o tipo inválido "{learner_type}".|
|Aprendendo do tipo inválido é passado. Mensagem de exceção: {exception_message}|


## <a name="error-0014"></a>Erro 0014  
 A exceção ocorre se a contagem de valores únicos da coluna for maior do que o permitido.  

 Este erro ocorre quando uma coluna contém demasiados valores únicos.  Por exemplo, pode ver este erro se especificar que uma coluna é tratada como dados categóricos, mas existem demasiados valores únicos na coluna para permitir o processamento completo. Pode também ver este erro se houver uma incompatibilidade entre o número de valores únicos em duas entradas.   

**Resolução:**

Abra o módulo que gerou o erro e identifique as colunas utilizadas como entradas. Para alguns módulos, pode clicar com o botão direito na entrada do conjunto de dados e selecionar **o Visualize** para obter estatísticas sobre colunas individuais, incluindo o número de valores únicos e a sua distribuição.

Para colunas que pretende utilizar para agrupar ou categorizar, tome medidas para reduzir o número de valores únicos nas colunas. Pode reduzir de diferentes formas, dependendo do tipo de dados da coluna. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Incapaz de encontrar uma resolução que corresponda ao seu cenário? Pode fornecer feedback sobre este tópico que inclui o nome do módulo que gerou o erro, e o tipo de dados e cardinalidade da coluna. Utilizaremos a informação para fornecer medidas de resolução de problemas mais direcionadas para cenários comuns.   

|Mensagens de Exceção|
|------------------------|
|A quantidade de valores únicos da coluna é maior do que o permitido.|
|Número de valores únicos na coluna: "{column_name}" é maior do que o permitido.|
|Número de valores únicos na coluna: "{column_name}" excede a contagem de tuples de {limitation}.|


## <a name="error-0015"></a>Erro 0015  
 A exceção ocorre se a ligação da base de dados tiver falhado.  

 Receberá este erro se introduzir um nome de conta SQL incorreto, palavra-passe, servidor de base de dados ou nome de base de dados, ou se não for possível estabelecer uma ligação com a base de dados devido a problemas na base de dados ou servidor.  

**Resolução:** Verifique se o nome da conta, palavra-passe, servidor de base de dados e base de dados foram corretamente introduzidos e que a conta especificada tem o nível correto de permissões. Verifique se a base de dados está atualmente acessível.  

|Mensagens de Exceção|
|------------------------|
|Ligação de base de dados de erros.|
|Ligação de base de dados de erros: {connection_str}.|


## <a name="error-0016"></a>Erro 0016  
 A exceção ocorre se os conjuntos de dados de entrada passados para o módulo devem ter tipos de coluna compatíveis, mas não o fazem.  

 Receberá este erro no Azure Machine Learning se os tipos de colunas passadas em dois ou mais conjuntos de dados não forem compatíveis entre si.  

**Resolução:** Utilize [Metadados de Edição](edit-metadata.md) ou modifique o conjunto de dados de entrada original<!--, or use [Convert to Dataset](convert-to-dataset.md)--> para garantir que os tipos das colunas são compatíveis.  

|Mensagens de Exceção|
|------------------------|
|Colunas com índice correspondente em conjuntos de dados de entrada têm tipos incompatíveis.|
|As colunas '{first_col_names}' são incompatíveis entre os dados do comboio e do teste.|
|As colunas '{first_col_names}' e '{second_col_names}' são incompatíveis.|
|Os tipos de elementos de coluna não são compatíveis com a coluna '{first_col_names}' (com base em zero) dos conjuntos de dados de entrada ({first_dataset_names} e {second_dataset_names} respectivamente).|


## <a name="error-0017"></a>Erro 0017  
 A exceção ocorre se uma coluna selecionada utilizar um tipo de dado que não é suportado pelo módulo atual.  

 Por exemplo, poderá receber este erro no Azure Machine Learning se a sua seleção de colunas incluir uma coluna com um tipo de dado que não pode ser processado pelo módulo, como uma coluna de cordas para uma operação matemática, ou uma coluna de pontuação onde é necessária uma coluna de características categóricas.  

**Resolução:**
 1. Identifique a coluna que é o problema.
 2. Reveja os requisitos do módulo.
 3. Modifique a coluna para a tornar conforme aos requisitos. Poderá ser necessário utilizar vários dos seguintes módulos para escutar alterações, dependendo da coluna e da conversão que está a tentar:
    + Utilize [metadados de edição](edit-metadata.md) para alterar o tipo de dados de colunas, ou para alterar a utilização da coluna de função para numérica, categórica para não categórica, e assim por diante.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Como último recurso, poderá ser necessário modificar o conjunto de dados de entrada original.

> [!TIP]
> Incapaz de encontrar uma resolução que corresponda ao seu cenário? Pode fornecer feedback sobre este tópico que inclui o nome do módulo que gerou o erro, e o tipo de dados e cardinalidade da coluna. Utilizaremos a informação para fornecer medidas de resolução de problemas mais direcionadas para cenários comuns. 

|Mensagens de Exceção|
|------------------------|
|Não é possível processar coluna do tipo atual. O tipo não é suportado pelo módulo.|
|Não é possível processar coluna do tipo {col_type}. O tipo não é suportado pelo módulo.|
|Não é possível processar a coluna "{col_name}" do tipo {col_type}. O tipo não é suportado pelo módulo.|
|Não é possível processar a coluna "{col_name}" do tipo {col_type}. O tipo não é suportado pelo módulo. Nome do parâmetro: {arg_name}.|


## <a name="error-0018"></a>Erro 0018  
 A exceção ocorre se o conjunto de dados de entrada não for válido.  

**Resolução:** Este erro no Azure Machine Learning pode aparecer em muitos contextos, pelo que não existe uma única resolução. Em geral, o erro indica que os dados fornecidos como entrada num módulo têm o número errado de colunas, ou que o tipo de dados não corresponde aos requisitos do módulo. Por exemplo:  

-   O módulo requer uma coluna de etiqueta, mas nenhuma coluna está marcada como uma etiqueta, ou ainda não selecionou uma coluna de etiqueta.  
  
-   O módulo requer que os dados sejam categóricos, mas os seus dados são numéricos.  

<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->

-   Os dados estão no formato errado.  
  
-   Os dados importados contêm caracteres inválidos, maus valores ou valores fora do alcance.  
-   A coluna está vazia ou contém demasiados valores em falta.  

 Para determinar os requisitos e como os seus dados podem, reveja o tópico de ajuda para o módulo que irá consumir o conjunto de dados como entrada.  

 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  

|Mensagens de Exceção|
|------------------------|
|Conjunto de dados não é válido.|
|{dataset1} contém dados inválidos.|
|{dataset1} e {dataset2} devem ser consistentes em termos de coluna.|
|{dataset1} contém dados inválidos, {reason}.|
|{dataset1} contém {invalid_data_category}. {troubleshoot_hint}|
|{dataset1} não é válido, {reason}. {troubleshoot_hint}|


## <a name="error-0019"></a>Erro 0019  
 A exceção ocorre se se espera que a coluna contenha valores classificados, mas não contém.  

 Receberá este erro no Azure Machine Learning se os valores da coluna especificados estiverem fora de ordem.  

**Resolução:** Serdene os valores da coluna modificando manualmente o conjunto de dados de entrada e reexame o módulo.  

|Mensagens de Exceção|
|------------------------|
|Os valores na coluna não estão classificados.|
|Os valores na coluna "{col_index}" não estão classificados.|
|Os valores na coluna "{col_index}" do conjunto de dados "{dataset}" não estão classificados.|
|Os valores no argumento "{arg_name}" não estão classificados na ordem "{sorting_order}".|


## <a name="error-0020"></a>Erro 0020  
 A exceção ocorre se o número de colunas em alguns dos conjuntos de dados passados para o módulo for demasiado pequeno.  

 Receberá este erro no Azure Machine Learning se não tiverem sido selecionadas colunas suficientes para um módulo.  

**Resolução:** Reveja o módulo e certifique-se de que o seletor de colunas tem o número correto de colunas selecionadas.  

|Mensagens de Exceção|
|------------------------|
|O número de colunas no conjunto de dados de entrada é inferior ao permitido no mínimo.|
|O número de colunas no conjunto de dados de entrada "{arg_name}" é inferior ao mínimo permitido.|
|O número de colunas no conjunto de dados de entrada é inferior ao permitido no mínimo de colunas {required_columns_count}.|
|O número de colunas no conjunto de dados de entrada "{arg_name}" é inferior ao permitido no mínimo de colunas {required_columns_count}.|


## <a name="error-0021"></a>Erro 0021  
 A exceção ocorre se o número de linhas em alguns dos conjuntos de dados passados para o módulo for demasiado pequeno.  

 Este erro visto no Azure Machine Learning quando não há linhas suficientes no conjunto de dados para executar a operação especificada. Por exemplo, pode ver este erro se o conjunto de dados de entrada estiver vazio, ou se estiver a tentar executar uma operação que exija que algum número mínimo de linhas seja válido. Tais operações podem incluir (mas não se limitam a) agrupamentos ou classificações com base em métodos estatísticos, certos tipos de binning, e aprendizagem com contagens.  

**Resolução:**

 + Abra o módulo que devolveu o erro e verifique as propriedades do conjunto de dados de entrada e do módulo. 
 + Verifique se o conjunto de dados de entrada não está vazio e que existem linhas de dados suficientes para satisfazer os requisitos descritos na ajuda do módulo.  
 + Se os seus dados forem carregados a partir de uma fonte externa, certifique-se de que a fonte de dados está disponível e que não há erro ou alteração na definição de dados que faria com que o processo de importação ficasse com menos filas.
 + Se estiver a realizar uma operação sobre os dados a montante do módulo que possam afetar o tipo de dados ou o número de valores, tais como limpeza, divisão ou junção de operações, verifique as saídas dessas operações para determinar o número de linhas devolvidas.  

|Mensagens de Exceção|
|------------------------|
|O número de linhas no conjunto de dados de entrada é inferior ao permitido no mínimo.|
|O número de linhas no conjunto de dados de entrada é inferior ao permitido no mínimo de linha {required_rows_count}.|
|O número de linhas no conjunto de dados de entrada é inferior ao permitido no mínimo de linha {required_rows_count}. {razão}|
|O número de linhas no conjunto de dados de entrada "{arg_name}" é inferior ao permitido no mínimo de linha {required_rows_count}).|
|O número de linhas no conjunto de dados de entrada "{arg_name}" é {atual_rows_count}, menos do que o permitido mínimo de {required_rows_count} linha(s).|
|O número de linhas "{row_type}" no conjunto de dados de entrada "{arg_name}" é {atual_rows_count}, menos do que o permitido mínimo de {required_rows_count} linha(s).|


## <a name="error-0022"></a>Erro 0022  
 A exceção ocorre se o número de colunas selecionadas no conjunto de dados de entrada não for igual ao número esperado.  

 Este erro no Azure Machine Learning pode ocorrer quando o módulo ou funcionamento a jusante requer um número específico de colunas ou entradas, e forneceu poucas ou demasiadas colunas ou entradas. Por exemplo:  

-   Especifica uma coluna de etiqueta ou coluna de chave e seleciona acidentalmente várias colunas.  
  
-   Está a renomear colunas, mas desde que haja mais ou menos nomes do que colunas.  
  
-   O número de colunas na fonte ou destino mudou ou não corresponde ao número de colunas utilizadas pelo módulo.  
  
-   Forneceu uma lista de valores separadas por vírgula para entradas, mas o número de valores não corresponde, ou várias entradas não são suportadas.  

**Resolução:** Reveja o módulo e verifique a seleção da coluna para garantir que o número correto de colunas é selecionado. Verifique as saídas dos módulos a montante e os requisitos das operações a jusante.  

 Se utilizar uma das opções de seleção de colunas que pode selecionar várias colunas (índices de colunas, todas as funcionalidades, todos numéricos, etc.), valide o número exato de colunas devolvidas pela seleção.  

 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->

 Verifique se o número ou o tipo de colunas a montante não se alteraram.  

 Se estiver a utilizar um conjunto de dados de recomendação para treinar um modelo, lembre-se de que o recomendador espera um número limitado de colunas, correspondentes a pares de artigos de utilizador ou rankings de artigos de utilizador. Remova colunas adicionais antes de treinar o modelo ou dividir conjuntos de dados de recomendação. Para obter mais informações, consulte [Dados Divididos.](split-data.md)  

|Mensagens de Exceção|
|------------------------|
|O número de colunas selecionadas no conjunto de dados de entrada não é igual ao número esperado.|
|O número de colunas selecionadas no conjunto de dados de entrada não é igual a {expected_col_count}.|
|O padrão de seleção da coluna "{selection_pattern_friendly_name}" fornece um número de colunas selecionadas no conjunto de dados de entrada não igual a {expected_col_count}.|
|Espera-se que o padrão de seleção de colunas "{selection_pattern_friendly_name}" forneça colunas {expected_col_count} selecionadas no conjunto de dados de entrada, mas as colunas {selected_col_count} são/são realmente fornecidas.|


## <a name="error-0023"></a>Erro 0023  

A exceção ocorre se a coluna-alvo do conjunto de dados de entrada não for válida para o módulo de treinador atual.  

Este erro na Aprendizagem automática Azure ocorre se a coluna-alvo (selecionada nos parâmetros do módulo) não for do tipo de dados válido, conter todos os valores em falta ou não for categórica como esperado.  

**Resolução:** Reveja a entrada do módulo para inspecionar o conteúdo da coluna etiqueta/alvo. Certifique-se de que não tem todos os valores em falta. Se o módulo espera que a coluna-alvo seja categórica, certifique-se de que existem mais de um valore distinto na coluna-alvo.  

|Mensagens de Exceção|
|------------------------|
|O conjunto de dados de entrada tem coluna-alvo não suportada.|
|O conjunto de dados de entrada tem coluna-alvo não suportada "{column_index}".|
|O conjunto de dados de entrada tem coluna-alvo não suportada "{column_index}" para o aprendiz do tipo {learner_type}.|


## <a name="error-0024"></a>Erro 0024  
A exceção ocorre se o conjunto de dados não contiver uma coluna de etiqueta.  

 Este erro na Aprendizagem automática Azure ocorre quando o módulo requer uma coluna de etiqueta e o conjunto de dados não tem uma coluna de etiqueta. Por exemplo, a avaliação de um conjunto de dados pontuado geralmente requer que uma coluna de etiqueta esteja presente para calcular métricas de precisão.  

Também pode acontecer que uma coluna de etiqueta esteja presente no conjunto de dados, mas não detetada corretamente pela Azure Machine Learning.

**Resolução:**

+ Abra o módulo que gerou o erro e determine se está presente uma coluna de etiqueta. O nome ou tipo de dados da coluna não importa, desde que a coluna contenha um único resultado (ou variável dependente) que esteja a tentar prever. Se não tiver a certeza de qual coluna tem a etiqueta, procure um nome genérico como  *Classe* ou *Target*. 
+  Se o conjunto de dados não incluir uma coluna de etiqueta, é possível que a coluna da etiqueta tenha sido explicitamente ou acidentalmente removida a montante. Também pode ser que o conjunto de dados não seja a saída de um módulo de pontuação a montante.
+ Para marcar explicitamente a coluna como coluna de etiqueta, adicione o módulo [editar metadados](edit-metadata.md) e ligue o conjunto de dados. Selecione apenas a coluna de etiqueta e **selecione Label** da lista de retirada **de campos.** 
+ Se a coluna errada for escolhida como a etiqueta, pode selecionar **a etiqueta Clear** dos **Campos** para fixar os metadados na coluna. 
  
|Mensagens de Exceção|
|------------------------|
|Não existe nenhuma coluna de etiqueta no conjunto de dados.|
|Não existe nenhuma coluna de etiqueta em "{dataset_name}".|


## <a name="error-0025"></a>Erro 0025  
 A exceção ocorre se o conjunto de dados não contiver uma coluna de pontuação.  

 Este erro no Azure Machine Learning ocorre se a entrada para o modelo de avaliação não contiver colunas de pontuação válidas. Por exemplo, o utilizador tenta avaliar um conjunto de dados antes de ser marcado com um modelo treinado correto, ou a coluna de pontuação foi explicitamente deixada a montante. Esta exceção também ocorre se as colunas de pontuação dos dois conjuntos de dados forem incompatíveis. Por exemplo, pode estar a tentar comparar a precisão de um regressor linear com um classificador binário.  

**Resolução:** Reveja a entrada para o modelo de avaliação e examine se contém uma ou mais colunas de pontuação. Caso contrário, o conjunto de dados não foi marcado ou as colunas de pontuação foram deixadas num módulo a montante.  

|Mensagens de Exceção|
|------------------------|
|Não há nenhuma coluna de pontuação no conjunto de dados.|
|Não há nenhuma coluna de pontuação em "{dataset_name}".|
|Não existe nenhuma coluna de pontuação em "{dataset_name}" que é produzida por um "{learner_type}". Marque o conjunto de dados utilizando o tipo correto de aprendiz.|


## <a name="error-0026"></a>Erro 0026  
 A exceção ocorre se não forem permitidas colunas com o mesmo nome.  

 Este erro na Aprendizagem automática Azure ocorre se várias colunas tiverem o mesmo nome. Uma das formas de receber este erro é se o conjunto de dados não tiver uma linha de cabeçalho e os nomes das colunas forem automaticamente atribuídos: Col0, Col1, etc.  

**Resolução:** Se as colunas tiverem o mesmo nome, insira um módulo [de Metadados de Edição](edit-metadata.md) entre o conjunto de dados de entrada e o módulo. Utilize o seletor de colunas em [Editar Metadados](edit-metadata.md) para selecionar colunas para renomear, digitando os novos nomes na caixa de texto **de novos nomes de colunas.**  

|Mensagens de Exceção|
|------------------------|
|Nomes de colunas iguais são especificados em argumentos. Nomes de colunas iguais não são permitidos por módulo.|
|Não são permitidos nomes de colunas iguais nos argumentos "{arg_name_1}" e "{arg_name_2}". Por favor, especifique nomes diferentes.|


## <a name="error-0027"></a>Erro 0027  
 A exceção ocorre no caso de dois objetos terem o mesmo tamanho, mas não são.  

 Este é um erro comum no Azure Machine Learning e pode ser causado por muitas condições.  

**Resolução:** Não há uma resolução específica. No entanto, pode verificar se existem condições como:  

-   Se estiver a renomear colunas, certifique-se de que cada lista (as colunas de entrada e a lista de novos nomes) tem o mesmo número de itens.  
  
-   Se estiver a juntar-se ou a concatenar dois conjuntos de dados, certifique-se de que têm o mesmo esquema.  
  
-   Se estiver a juntar dois conjuntos de dados que tenham várias colunas, certifique-se de que as colunas-chave têm o mesmo tipo de dados e selecione a opção **Permitir duplicações e preservar a ordem da coluna na seleção**.  

|Mensagens de Exceção|
|------------------------|
|O tamanho de objetos passados é inconsistente.|
|O tamanho de "{friendly_name1}" é inconsistente com o tamanho de "{friendly_name2}".|


## <a name="error-0028"></a>Erro 0028  
 A exceção ocorre no caso de o conjunto de colunas conter nomes de colunas duplicados e não for permitido.  

 Este erro na aprendizagem automática Azure ocorre quando os nomes das colunas são duplicados; isto é, não é único.  

**Resolução:** Se alguma coluna tiver o mesmo nome, adicione uma instância de [Metadados](edit-metadata.md) de Edição entre o conjunto de dados de entrada e o módulo aumentando o erro. Utilize o Seletor de Colunas em [Metadados](edit-metadata.md) de Edição para selecionar colunas para renomear e digite os nomes das novas colunas na caixa de texto de nomes de **colunas Novas.** Se estiver a renomear várias colunas, certifique-se de que os valores que digita nos **nomes** da nova coluna são únicos.  

|Mensagens de Exceção|
|------------------------|
|O conjunto de colunas contém nomes de colunas duplicados.|
|O nome "{duplicated_name}" é duplicado.|
|O nome "{duplicated_name}" é duplicado em "{arg_name}".|
|O nome "{duplicated_name}" é duplicado. Detalhes: {detalhes}|


## <a name="error-0029"></a>Erro 0029  
 A exceção ocorre no caso de quando o URI inválido é passado.  

 Este erro na Aprendizagem automática Azure ocorre no caso de passar uri inválido.  Receberá este erro se alguma das seguintes condições for verdadeira:  

-   O Public ou SAS URI fornecido para Azure Blob Storage para leitura ou escrita contém um erro.  
  
-   A janela de tempo para o SAS expirou.  
  
-   O URL web via fonte HTTP representa um ficheiro ou um URI de backback.  
  
-   O URL web via HTTP contém um URL formatado incorretamente.  
  
-   A URL não pode ser resolvida pela fonte remota.  

**Resolução:** Reveja o módulo e verifique o formato do URI. Se a fonte de dados for um URL web via HTTP, verifique se a fonte pretendida não é um ficheiro ou um URI de backback (local local).  

|Mensagens de Exceção|
|------------------------|
|Uri inválido passou.|
|O Uri "{invalid_url}" é inválido.|


## <a name="error-0030"></a>Erro 0030  
 A exceção ocorre no caso em que não é possível descarregar um ficheiro.  

 Esta exceção no Azure Machine Learning ocorre quando não é possível descarregar um ficheiro. Receberá esta exceção quando uma tentativa de leitura de uma fonte HTTP falhou após três (3) tentativas de repetição.  

**Resolução:** Verifique se o URI para a fonte HTTP está correto e se o site está atualmente acessível através da Internet.  

|Mensagens de Exceção|
|------------------------|
|Não é possível descarregar um ficheiro.|
|Erro ao descarregar o ficheiro: {file_url}.|


## <a name="error-0031"></a>Erro 0031  
 A exceção ocorre se o número de colunas no conjunto de colunas for inferior ao necessário.  

 Este erro na Aprendizagem automática Azure ocorre se o número de colunas selecionadas for inferior ao necessário.  Receberá este erro se o número mínimo de colunas não for selecionado.  

**Resolução:** Adicione colunas adicionais à seleção da coluna utilizando o **Seletor de Colunas**.  

|Mensagens de Exceção|
|------------------------|
|O número de colunas no conjunto de colunas é inferior ao necessário.|
|Pelo menos as colunas {required_columns_count} devem ser especificadas para o argumento de entrada "{arg_name}".|
|Pelo menos as colunas {required_columns_count} devem ser especificadas para o argumento de entrada "{arg_name}". O número real de colunas especificadas é {input_columns_count}.|


## <a name="error-0032"></a>Erro 0032  
 A exceção ocorre se o argumento não for um número.  

 Receberá este erro no Azure Machine Learning se o argumento for duplo ou NaN.  

**Resolução:** Modifique o argumento especificado para utilizar um valor válido.  

|Mensagens de Exceção|
|------------------------|
|Argumento não é um número.|
|"{arg_name}" não é um número.|


## <a name="error-0033"></a>Erro 0033  
 A exceção ocorre se o argumento for infinito.  

 Este erro na Aprendizagem automática Azure ocorre se o argumento for infinito. Receberá este erro se o argumento for `double.NegativeInfinity` ou `double.PositiveInfinity` .  

**Resolução:** Modifique o argumento especificado para ser um valor válido.  

|Mensagens de Exceção|
|------------------------|
|O argumento deve ser finito.|
|"{arg_name}" não é finito.|
|Coluna "{column_name}" contém valores infinitos.|


## <a name="error-0034"></a>Erro 0034  
 A exceção ocorre se existir mais de uma classificação para um determinado par de artigos de utilizador.  

 Este erro no Azure Machine Learning ocorre por recomendação se um par de artigos de utilizador tiver mais de um valor de classificação.  

**Resolução:** Certifique-se de que o par de artigos de utilizador possui apenas um valor de classificação.  

|Mensagens de Exceção|
|------------------------|
|Existe mais de uma classificação para o(s) valor(s) no conjunto de dados.|
|Mais de uma classificação para o utilizador {user} e item {item} na tabela de dados de previsão de classificação.|
|Mais de uma classificação para o utilizador {user} e item {item} em {dataset}.|


## <a name="error-0035"></a>Erro 0035  
 A exceção ocorre se não forem fornecidas funcionalidades para um determinado utilizador ou item.  

 Este erro no Azure Machine Learning ocorre que você está tentando usar um modelo de recomendação para pontuar, mas um vetor de recurso não pode ser encontrado.  

**Resolução:**

O recomendador matchbox tem certos requisitos que devem ser cumpridos quando utilizar as funcionalidades do item ou as funcionalidades do utilizador.  Este erro indica que falta um vetor de recurso para um utilizador ou item que forneceu como entrada. Certifique-se de que um vetor de funcionalidades está disponível nos dados de cada utilizador ou item.  

 Por exemplo, se treinou um modelo de recomendação utilizando funcionalidades como a idade, localização ou rendimento do utilizador, mas agora quer criar pontuações para novos utilizadores que não foram vistos durante o treino, deve fornecer algum conjunto equivalente de funcionalidades (nomeadamente, idade, localização e valores de rendimento) para os novos utilizadores, de forma a fazer previsões adequadas para os mesmos. 

 Se não possuir funcionalidades para estes utilizadores, considere a engenharia de recursos para gerar funcionalidades adequadas.  Por exemplo, se não tiver idade de utilizador individual ou valores de rendimento, poderá gerar valores aproximados para utilizar para um grupo de utilizadores. 

<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).

For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->

 > [!TIP]
 > Resolução não aplicável ao seu caso? É bem-vindo a enviar feedback sobre este artigo e a fornecer informações sobre o cenário, incluindo o módulo e o número de linhas na coluna. Utilizaremos estas informações para fornecer, no futuro, etapas mais pormenorizadas de resolução de problemas.

|Mensagens de Exceção|
|------------------------|
|Não foram fornecidas funcionalidades para um utilizador ou item necessário.|
|Funcionalidades para {required_feature_name} necessárias, mas não fornecidas.|


## <a name="error-0036"></a>Erro 0036  
 A exceção ocorre se vários vetores de recurso foram fornecidos para um determinado utilizador ou item.  

 Este erro na Aprendizagem automática Azure ocorre se um vetor de recurso for definido mais de uma vez.  

**Resolução:** Certifique-se de que o vetor de recurso não é definido mais de uma vez.  

|Mensagens de Exceção|
|------------------------|
|Duplicar a definição de recurso para um utilizador ou item.|


## <a name="error-0037"></a>Erro 0037  
 A exceção ocorre se várias colunas de etiquetas forem especificadas e apenas uma for permitida.  

 Este erro na Aprendizagem automática Azure ocorre se mais de uma coluna for selecionada para ser a nova coluna de etiquetas. A maioria dos algoritmos de aprendizagem supervisionados requerem que uma única coluna seja marcada como o alvo ou o rótulo.  

**Resolução:** Certifique-se de que seleciona uma única coluna como a nova coluna da etiqueta.  

|Mensagens de Exceção|
|------------------------|
|São especificadas várias colunas de etiquetas.|
|Várias colunas de etiquetas são especificadas em "{dataset_name}".|


## <a name="error-0039"></a>Erro 0039  
 A exceção ocorre se uma operação falhar.  

 Este erro na Aprendizagem automática Azure ocorre quando uma operação interna não pode ser concluída.  

**Resolução:** Este erro é causado por muitas condições e não existe uma solução específica.  
 A tabela seguinte contém mensagens genéricas para este erro, que são seguidas por uma descrição específica da circunstância. 

 Se não houver detalhes disponíveis, [o Microsoft Q&Página de perguntas para enviar feedback](/answers/topics/azure-machine-learning-studio-classic.html) e fornecer informações sobre os módulos que geraram o erro e as condições relacionadas.

|Mensagens de Exceção|
|------------------------|
|A operação falhou.|
|Erro durante a conclusão da operação: "{failed_operation}".|
|Erro durante a conclusão da operação: "{failed_operation}". Razão: "{reason}".|


## <a name="error-0042"></a>Erro 0042  
 A exceção ocorre quando não é possível converter coluna para outro tipo.  

 Este erro na Aprendizagem automática Azure ocorre quando não é possível converter a coluna para o tipo especificado.  Receberá este erro se um módulo necessitar de um determinado tipo de dados, como data, texto, número de ponto flutuante ou número inteiro, mas não é possível converter uma coluna existente para o tipo exigido.  

Por exemplo, pode selecionar uma coluna e tentar convertê-la num tipo de dados numérico para utilização numa operação matemática, e obter este erro se a coluna contiver dados inválidos. 

Outra razão para obter este erro se tentar utilizar uma coluna que contenha números de pontos flutuantes ou muitos valores únicos como coluna categórica. 

**Resolução:**

+ Abra a página de ajuda para o módulo que gerou o erro e verifique os requisitos do tipo de dados.
+ Reveja os tipos de dados das colunas no conjunto de dados de entrada.
+ Inspecione os dados originários das chamadas fontes de dados sem esquema.
+ Verifique se o conjunto de dados é de valores em falta ou caracteres especiais que possam bloquear a conversão para o tipo de dados pretendido. 
    + Os tipos de dados numéricos devem ser consistentes: por exemplo, verifique se existem números de pontos flutuantes numa coluna de inteiros.
    + Procure fios de texto ou valores NA numa coluna de números. 
    + Os valores boolean podem ser convertidos para uma representação adequada, dependendo do tipo de dados necessário.
    + Examine colunas de texto para caracteres não-unicocos, caracteres do separador ou caracteres de controlo
    + Os dados da data devem ser consistentes para evitar erros de modelação, mas a limpeza pode ser complexa devido aos muitos formatos. Considere usar <!--the [Execute R Script](execute-r-script.md) or -->[Execute](execute-python-script.md) módulos de Script Python para executar a limpeza.  
+ Se necessário, modifique os valores do conjunto de dados de entrada para que a coluna possa ser convertida com sucesso. A modificação pode incluir operações de vinculação, truncação ou arredondamento, eliminação de outliers ou imputação de valores em falta. Consulte os seguintes artigos para alguns cenários comuns de transformação de dados na aprendizagem automática:
    + [Limpar Dados em Falta](clean-missing-data.md)
    + [Normalizar Dados](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->

> [!TIP]
> Resolução pouco clara, ou não aplicável ao seu caso? É bem-vindo a enviar feedback sobre este artigo e a fornecer informações sobre o cenário, incluindo o módulo e o tipo de dados da coluna. Utilizaremos estas informações para fornecer, no futuro, etapas mais pormenorizadas de resolução de problemas.  

|Mensagens de Exceção|
|------------------------|
|Não é permitida a conversão.|
|Não foi possível converter a coluna do tipo {type1} para coluna do tipo {type2}.|
|Não foi possível converter a coluna "{col_name1}" do tipo {type1} para coluna do tipo {type2}.|
|Não foi possível converter a coluna "{col_name1}" do tipo {type1} para a coluna "{col_name2}" do tipo {type2}.|


## <a name="error-0044"></a>Erro 0044  
 A exceção ocorre quando não é possível derivar o tipo de coluna dos valores existentes.  

 Este erro na Aprendizagem automática Azure ocorre quando não é possível inferir o tipo de coluna ou colunas num conjunto de dados. Isto acontece normalmente quando concatena dois ou mais conjuntos de dados com diferentes tipos de elementos. Se o Azure Machine Learning não conseguir determinar um tipo comum capaz de representar todos os valores numa coluna ou colunas sem perda de informação, gerará este erro.  

**Resolução:** Certifique-se de que todos os valores de uma determinada coluna em ambos os conjuntos de dados combinados são do mesmo tipo (numérico, booleano, categórico, corda, data, etc.) ou podem ser coagidos ao mesmo tipo.  

|Mensagens de Exceção|
|------------------------|
|Não é possível derivar o tipo de elemento da coluna.|
|Não é possível derivar o tipo de elemento para a coluna "{column_name}" -- todos os elementos são referências nulas.|
|Não é possível derivar o tipo de elemento para a coluna "{column_name}" do conjunto de dados "{dataset_name}" -- todos os elementos são referências nulas.|


## <a name="error-0045"></a>Erro 0045  
 A exceção ocorre quando não é possível criar uma coluna devido a tipos de elementos mistos na fonte.  

 Este erro no Azure Machine Learning é produzido quando os tipos de elementos de dois conjuntos de dados combinados são diferentes.  

**Resolução:** Certifique-se de que todos os valores numa determinada coluna em ambos os conjuntos de dados combinados são do mesmo tipo (numérico, Boolean, categórico, string, data, etc.).  

|Mensagens de Exceção|
|------------------------|
|Não é possível criar coluna com tipos de elementos mistos.|
|Não é possível criar coluna com id "{column_id}" de tipos de elementos mistos:<br />Tipo de dados[{row_1}, {column_id}] é "{type_1}". <br />Tipo de dados[{row_2}, {column_id}] é "{type_2}".|
|Não é possível criar coluna com id "{column_id}" de tipos de elementos mistos:<br />Digitar em pedaço {chunk_id_1} é "{type_1}". <br />Digite em pedaço {chunk_id_2} é "{type_2}" com tamanho do pedaço: {chunk_size}.|


## <a name="error-0046"></a>Erro 0046  
 A exceção ocorre quando não é possível criar diretório em caminho especificado.  

 Este erro na Aprendizagem automática Azure ocorre quando não é possível criar um diretório no caminho especificado. Receberá este erro se alguma parte do caminho para o diretório de saída de uma Consulta de Colmeia estiver incorreta ou inacessível.  

**Resolução:** Reveja o módulo e verifique se o percurso do diretório está corretamente formatado e que está acessível com as credenciais atuais.  

|Mensagens de Exceção|
|------------------------|
|Por favor, especifique um diretório de saída válido.|
|Diretório: {path} não pode ser criado. Por favor, especifique o caminho válido.|


## <a name="error-0047"></a>Erro 0047  
 A exceção ocorre se o número de colunas de funcionalidades em alguns dos conjuntos de dados passados para o módulo for demasiado pequeno.  

 Este erro na Aprendizagem automática Azure ocorre se o conjunto de dados de entrada para a formação não contiver o número mínimo de colunas exigidas pelo algoritmo. Normalmente, ou o conjunto de dados está vazio ou contém apenas colunas de treino.  

**Resolução:** Reveja o conjunto de dados de entrada para se certificar de que existem uma ou mais colunas adicionais para além da coluna do rótulo.  

|Mensagens de Exceção|
|------------------------|
|O número de colunas de funcionalidades no conjunto de dados de entrada é inferior ao permitido no mínimo.|
|O número de colunas de funcionalidades no conjunto de dados de entrada é inferior ao permitido no mínimo de colunas {required_columns_count}.|
|O número de colunas de funcionalidades no conjunto de dados de entrada "{arg_name}" é inferior ao permitido no mínimo de colunas {required_columns_count}.|


## <a name="error-0048"></a>Erro 0048  
 A exceção ocorre no caso em que não é possível abrir um ficheiro.  

 Este erro na Azure Machine Learning ocorre quando não é possível abrir um ficheiro para leitura ou escrita. Pode receber este erro por estas razões:  

-   O recipiente ou o ficheiro (blob) não existe  
  
-   O nível de acesso do ficheiro ou do contentor não lhe permite aceder ao ficheiro  
  
-   O ficheiro é demasiado grande para ser lido ou o formato errado  

**Resolução:** Reveja o módulo e o ficheiro que está a tentar ler.  

 Verifique se os nomes do recipiente e do ficheiro estão corretos.  

 Utilize o portal clássico Azure ou uma ferramenta de armazenamento Azure para verificar se tem permissão para aceder ao ficheiro.  

  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->

|Mensagens de Exceção|
|------------------------|
|Incapaz de abrir um ficheiro.|
|Erro ao abrir o ficheiro: {file_name}.|
|Erro ao abrir o ficheiro: {file_name}. Mensagem de exceção de armazenamento: {exceção}.|


## <a name="error-0049"></a>Erro 0049  
 A exceção ocorre no caso em que não é possível analisar um ficheiro.  

 Este erro na Azure Machine Learning ocorre quando não é possível analisar um ficheiro. Receberá este erro se o formato de ficheiro selecionado no módulo [Dados de Importação](import-data.md) não corresponder ao formato real do ficheiro, ou se o ficheiro contiver um carácter irreconhecível.  

**Resolução:** Reveja o módulo e corrija a seleção do formato de ficheiro se não corresponder ao formato do ficheiro. Se possível, inspecione o ficheiro para confirmar que não contém caracteres ilegais.  

|Mensagens de Exceção|
|------------------------|
|Incapaz de analisar um ficheiro.|
|Erro ao analisar o ficheiro {file_format}.|
|Erro ao analisar o ficheiro {file_format}: {file_name}.|
|Erro ao analisar o ficheiro {file_format}. Razão: {failure_reason}.|
|Erro ao analisar o ficheiro {file_format}: {file_name}. Razão: {failure_reason}.|


## <a name="error-0052"></a>Erro 0052  
 A exceção ocorre se a tecla de conta de armazenamento Azure for especificada incorretamente.  

 Este erro no Azure Machine Learning ocorre se a chave utilizada para aceder à conta de armazenamento Azure estiver incorreta. Por exemplo, pode ver este erro se a chave de armazenamento Azure foi truncada quando copiada e colada, ou se a chave errada foi utilizada.  

 Para obter mais informações sobre como obter a chave para uma conta de armazenamento Azure, consulte [ver, copiar e regenerar as teclas de acesso ao armazenamento](../../storage/common/storage-account-create.md).  

**Resolução:** Reveja o módulo e verifique se a chave de armazenamento Azure está correta para a conta; copiar a chave novamente do portal clássico Azure, se necessário.  

|Mensagens de Exceção|
|------------------------|
|A chave da conta de armazenamento Azure está incorreta.|


## <a name="error-0053"></a>Erro 0053  
 A exceção ocorre no caso de não existirem funcionalidades ou itens do utilizador para recomendações de caixa de fósforos.  

 Este erro no Azure Machine Learning é produzido quando um vetor de recurso não pode ser encontrado.  

**Resolução:** Certifique-se de que um vetor de recurso está presente no conjunto de dados de entrada.  

|Mensagens de Exceção|
|------------------------|
|As funcionalidades ou/e itens do utilizador são necessários, mas não fornecidos.|


## <a name="error-0056"></a>Erro 0056  
 A exceção ocorre se as colunas selecionadas para uma operação violarem os requisitos.  

 Este erro na Aprendizagem automática Azure ocorre quando está a escolher colunas para uma operação que requer que a coluna seja de um determinado tipo de dados. 

 Este erro também pode ocorrer se a coluna for o tipo de dados correto, mas o módulo que está a utilizar requer que a coluna também seja marcada como uma função, etiqueta ou coluna categórica.  

  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->

**Resolução:**

1.  Reveja o tipo de dados das colunas que estão atualmente selecionadas. 

2. Verificar se as colunas selecionadas são categóricas, etiquetadas ou colunas de características.  
  
3.  Reveja o tópico de ajuda para o módulo em que fez a seleção da coluna, para determinar se existem requisitos específicos para o tipo de dados ou utilização de colunas.  
  
3.  Utilize [metadados de edição](edit-metadata.md) para alterar o tipo de coluna durante a duração desta operação. Certifique-se de que altera o tipo de coluna de volta ao seu valor original, utilizando outra instância de [Editar Metadados,](edit-metadata.md)se precisar dele para operações a jusante.  

|Mensagens de Exceção|
|------------------------|
|Uma ou mais colunas selecionadas não estavam numa categoria permitida.|
|Coluna com o nome "{col_name}" não está numa categoria permitida.|


## <a name="error-0057"></a>Erro 0057  
 A exceção ocorre quando se tenta criar um ficheiro ou uma bolha que já exista.  

 Esta exceção ocorre quando está a utilizar o módulo [de Dados de Exportação](export-data.md) ou outro módulo para guardar os resultados de um pipeline no armazenamento de bolhas Azure Machine Learning to Azure, mas tenta criar um ficheiro ou bolha que já exista.   

**Resolução:**

 Só receberá este erro se tiver definido previamente o modo de escrita de armazenamento de blob da propriedade **Azure** para **Error**. Por design, este módulo levanta um erro se tentar escrever um conjunto de dados para uma bolha que já existe.

 - Abra as propriedades do módulo e altere o **modo de escrita de armazenamento de bolhas Azure para** **Overwrite**.
 - Em alternativa, pode escrever o nome de uma bolha ou ficheiro de destino diferente e não se esqueça de especificar uma bolha que já não existe.  

|Mensagens de Exceção|
|------------------------|
|O ficheiro ou blob já existe.|
|O ficheiro ou blob "{file_path}" já existe.|


## <a name="error-0058"></a>Erro 0058  
 Este erro na Aprendizagem automática Azure ocorre se o conjunto de dados não contiver a coluna de etiquetas esperada.  

 Esta exceção também pode ocorrer quando a coluna de etiqueta fornecida não corresponde aos dados ou tipo de dados esperados pelo aluno, ou tem os valores errados. Por exemplo, esta exceção é produzida quando se utiliza uma coluna de etiquetas de valor real ao treinar um classificador binário.  

**Resolução:** A resolução depende do aluno ou do treinador que está a utilizar e dos tipos de dados das colunas no seu conjunto de dados. Primeiro, verifique os requisitos do algoritmo de aprendizagem automática ou módulo de treino.  

 Reveja o conjunto de dados de entrada. Verifique se a coluna que espera ser tratada como a etiqueta tem o tipo de dados certo para o modelo que está a criar.  

 Verifique se há valores em falta e elimine-os ou substitua-os se necessário.  

 Se necessário, adicione o módulo [editar metadados](edit-metadata.md) e certifique-se de que a coluna da etiqueta está marcada como uma etiqueta.  

|Mensagens de Exceção|
|------------------------|
|Os valores da coluna de etiqueta e os valores da coluna de etiquetas pontuados não são comparáveis.|
|A coluna da etiqueta não é tão esperada em "{dataset_name}".|
|A coluna da etiqueta não é como esperado em "{dataset_name}", {reason}.|
|A coluna da etiqueta "{column_name}" não é esperada em "{dataset_name}".|
|A coluna da etiqueta "{column_name}" não é esperada em "{dataset_name}", {reason}.|


## <a name="error-0059"></a>Erro 0059  
 A exceção ocorre se um índice de coluna especificado num selecionador de colunas não puder ser analisado.  

 Este erro na aprendizagem automática Azure ocorre se um índice de coluna especificado ao utilizar o Seletor de Colunas não puder ser analisado.  Receberá este erro quando o índice da coluna estiver num formato inválido que não possa ser analisado.  

**Resolução:** Modifique o índice da coluna para utilizar um valor de índice válido.  

|Mensagens de Exceção|
|------------------------|
|Um ou mais índices de coluna especificados ou intervalos de índice não podiam ser analisados.|
|O índice de coluna ou intervalo "{column_index_or_range}" não pôde ser analisado.|


## <a name="error-0060"></a>Erro 0060  
 A exceção ocorre quando uma coluna fora de alcance é especificada num selecionador de colunas.  

 Este erro na aprendizagem automática Azure ocorre quando uma gama de colunas fora de alcance é especificada no Seletor de Colunas. Receberá este erro se o intervalo de colunas no selecionador de colunas não corresponder às colunas do conjunto de dados.  

**Resolução:** Modificar o intervalo de colunas no selecionador de colunas para corresponder às colunas do conjunto de dados.  

|Mensagens de Exceção|
|------------------------|
|Inválido ou fora do intervalo de índice de colunas especificado.|
|A gama de colunas "{column_range}" é inválida ou fora de alcance.|


## <a name="error-0061"></a>Erro 0061  
 A exceção ocorre quando se tenta adicionar uma linha a uma DataTable que tem um número diferente de colunas do que a tabela.  

 Este erro no Azure Machine Learning ocorre quando se tenta adicionar uma linha a um conjunto de dados que tem um número diferente de colunas do que o conjunto de dados.  Receberá este erro se a linha que está a ser adicionada ao conjunto de dados tiver um número diferente de colunas do conjunto de dados de entrada.  A linha não pode ser anexada ao conjunto de dados se o número de colunas for diferente.  

**Resolução:** Modificar o conjunto de dados de entrada para ter o mesmo número de colunas que a linha adicionada, ou modificar a linha adicionada para ter o mesmo número de colunas que o conjunto de dados.  

|Mensagens de Exceção|
|------------------------|
|Todas as mesas devem ter o mesmo número de colunas.|
|Colunas no pedaço "{chunk_id_1}" é diferente com o pedaço "{chunk_id_2}" com tamanho do pedaço: {chunk_size}.|
|A contagem de colunas no ficheiro "{filename_1}" (count={column_count_1}) é diferente com o ficheiro "{filename_2}" (count={column_count_2}).|


## <a name="error-0062"></a>Erro 0062  
 A exceção ocorre quando se tenta comparar dois modelos com diferentes tipos de aprendizes.  

 Este erro no Azure Machine Learning é produzido quando as métricas de avaliação de dois conjuntos de dados pontuados diferentes não podem ser comparadas. Neste caso, não é possível comparar a eficácia dos modelos utilizados para produzir os dois conjuntos de dados pontuados.  

**Resolução:** Verifique se os resultados pontuados são produzidos pelo mesmo tipo de modelo de aprendizagem automática (classificação binária, regressão, classificação multi-classes, recomendação, agrupamento, deteção de anomalias, etc.) Todos os modelos que comparar devem ter o mesmo tipo de aprendiz.  

|Mensagens de Exceção|
|------------------------|
|Todos os modelos devem ter o mesmo tipo de aprendiz.|
|Tem um tipo de aprendiz incompatível: "{atual_learner_type}". Os tipos de aprendizes esperados são: "{expected_learner_type_list}".|


## <a name="error-0064"></a>Erro 0064  
 A exceção ocorre se o nome da conta de armazenamento Azure ou a chave de armazenamento forem especificados incorretamente.  

 Este erro na Aprendizagem automática Azure ocorre se o nome ou a chave de armazenamento Azure for especificado incorretamente. Receberá este erro se introduzir um nome de conta ou senha incorreto para a conta de armazenamento. Isto pode ocorrer se introduzir manualmente o nome da conta ou a palavra-passe. Pode também ocorrer se a conta tiver sido eliminada.  

**Resolução:** Verifique se o nome da conta e a palavra-passe foram introduzidos corretamente e se a conta existe.  

|Mensagens de Exceção|
|------------------------|
|O nome ou chave de armazenamento Azure está incorreto.|
|O nome da conta de armazenamento Azure "{account_name}" ou a chave de armazenamento para o nome da conta está incorreta.|


## <a name="error-0065"></a>Erro 0065  
 A exceção ocorre se o nome da bolha Azure for especificado incorretamente.  

 Este erro na Aprendizagem automática Azure ocorre se o nome da bolha Azure for especificado incorretamente.  Receberá o erro se:  

-   A bolha não pode ser encontrada no recipiente especificado.  

 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->

-   Apenas o contentor foi especificado como fonte num pedido [de dados de importação](import-data.md) quando o formato era Excel ou CSV com codificação; a concatenação do conteúdo de todas as bolhas dentro de um recipiente não é permitida com estes formatos.  
  
-   Um SAS URI não contém o nome de uma bolha válida.  

**Resolução:** Revisite o módulo que lança a exceção. Verifique se existe a bolha especificada no recipiente na conta de armazenamento e que as permissões permitem ver a bolha. Verifique se a entrada é do **nome de recipiente/nome de arquivo** se tiver Excel ou CSV com formatos de codificação. Verifique se um SAS URI contém o nome de uma bolha válida.  

|Mensagens de Exceção|
|------------------------|
|O nome da bolha de armazenamento Azure está incorreto.|
|O nome da bolha de armazenamento Azure "{blob_name}" está incorreto.|
|O nome da bolha de armazenamento Azure com prefixo "{blob_name_prefix}" não existe.|
|Não conseguiu encontrar nenhuma bolha de armazenamento Azure debaixo do recipiente "{container_name}".|
|Não consegui encontrar nenhuma bolha de armazenamento Azure com o caminho wildcard "{blob_wildcard_path}".|


## <a name="error-0066"></a>Erro 0066  
 A exceção ocorre se um recurso não puder ser enviado para uma Bolha Azure.  

 Este erro na Azure Machine Learning ocorre se um recurso não puder ser enviado para uma Bolha Azure.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Ambos são guardados na mesma conta de armazenamento Azure que a conta que contém o ficheiro de entrada.  

**Resolução:** Revisite o módulo. Verifique se o nome da conta Azure, a chave de armazenamento e o recipiente estão corretos e que a conta tem permissão para escrever ao recipiente.  

|Mensagens de Exceção|
|------------------------|
|O recurso não pôde ser enviado para o armazenamento da Azure.|
|O ficheiro "{source_path}" não pôde ser enviado para o armazenamento do Azure como "{dest_path}".|


## <a name="error-0067"></a>Erro 0067  
 A exceção ocorre se um conjunto de dados tiver um número diferente de colunas do que o esperado.  

 Este erro na Aprendizagem automática Azure ocorre se um conjunto de dados tiver um número diferente de colunas do que o esperado.  Receberá este erro quando o número de colunas no conjunto de dados for diferente do número de colunas que o módulo espera durante a execução.  

**Resolução:** Modifique o conjunto de dados de entrada ou os parâmetros.  

|Mensagens de Exceção|
|------------------------|
|Número inesperado de colunas na datatável.|
|Número inesperado de colunas no conjunto de dados "{dataset_name}".|
|Colunas "{expected_column_count}" mas encontradas colunas "{atual_column_count}" em vez disso.|
|No conjunto de dados de entrada "{dataset_name}", a coluna esperada "{expected_column_count}" mas encontrou colunas "{atual_column_count}" em vez disso.|


## <a name="error-0068"></a>Erro 0068  
 A exceção ocorre se o script da Colmeia especificado não estiver correto.  

 Este erro no Azure Machine Learning ocorre se houver erros de sintaxe num script Hive QL, ou se o intérprete da Hive encontrar um erro durante a execução da consulta ou do script.  

**Resolução:**

A mensagem de erro da Hive é normalmente reportada no Registo de Erros para que possa tomar medidas com base no erro específico. 

+ Abra o módulo e inspecione a consulta para obter erros.  
+ Verifique se a consulta funciona corretamente fora do Azure Machine Learning, iniciando sessão na consola Hive do seu cluster Hadoop e executando a consulta.  
+ Experimente colocar comentários no seu script Hive numa linha separada em vez de misturar declarações e comentários executáveis numa única linha.  

### <a name="resources"></a>Recursos

Consulte os seguintes artigos para obter ajuda com consultas de Hive para machine learning:

+ [Criar tabelas de Colmeia e carregar dados a partir do armazenamento do Blob Azure](../team-data-science-process/move-hive-tables.md)
+ [Explore dados em tabelas com consultas de Colmeia](../team-data-science-process/explore-data-hive-tables.md)
+ [Criar características para dados num cluster do Hadoop com consultas do Hive](../team-data-science-process/create-features-hive.md)
+ [Colmeia para utilizadores SQL Cheat Sheet (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Mensagens de Exceção|
|------------------------|
|O guião da colmeia está incorreto.|


## <a name="error-0069"></a>Erro 0069  
 A exceção ocorre se o script SQL especificado não estiver correto.  

 Este erro na Aprendizagem automática Azure ocorre se o script SQL especificado tiver problemas de sintaxe, ou se as colunas ou tabela especificadas no script não forem válidas. 

 Receberá este erro se o motor SQL encontrar algum erro durante a execução da consulta ou do script. A mensagem de erro SQL é normalmente reportada no Registo de Erros para que possa tomar medidas com base no erro específico.  

**Resolução:** Reveja o módulo e inspecione a consulta SQL para obter erros.  

 Verifique se a consulta funciona corretamente fora do Azure ML, iniciando sessão no servidor de base de dados diretamente e executando a consulta.  

 Se houver uma mensagem gerada pelo SQL reportada pela exceção do módulo, tome medidas com base no erro reportado. Por exemplo, as mensagens de erro por vezes incluem orientações específicas sobre o erro provável:
+ *Nenhuma coluna ou base de dados em falta,* indicando que pode ter digitado um nome de coluna errado. Se tiver a certeza de que o nome da coluna está correto, tente utilizar suportes ou marcas de aspas para incluir o identificador de colunas.
+ *SQL erro \<SQL keyword\> lógico próximo* , indicando que pode ter um erro de sintaxe antes da palavra-chave especificada

  
|Mensagens de Exceção|
|------------------------|
|O script SQL está incorreto.|
|A consulta SQL "{sql_query}" não está correta.|
|A consulta SQL "{sql_query}" não está correta. Mensagem de exceção: {exceção}.|


## <a name="error-0070"></a>Erro 0070  
 A exceção ocorre quando se tenta aceder à tabela Azure inexistente.  

 Este erro na Aprendizagem automática Azure ocorre quando tenta aceder a uma tabela Azure inexistente. Receberá este erro se especificar uma tabela no armazenamento Azure, que não existe ao ler ou escrever para O Armazenamento de Mesa Azure. Isto pode acontecer se desajustar o nome da tabela desejada, ou se tiver um desfasamento entre o nome-alvo e o tipo de armazenamento. Por exemplo, pretendia ler de uma mesa, mas inseriu o nome de uma bolha.  

**Resolução:** Reveja o módulo para verificar se o nome da tabela está correto.  

|Mensagens de Exceção|
|------------------------|
|A mesa azul não existe.|
|A tabela azul "{table_name}" não existe.|


## <a name="error-0072"></a>Erro 0072  
 A exceção ocorre no caso do tempo limite de ligação.  

 Este erro na Aprendizagem automática Azure ocorre quando uma ligação termina. Receberá este erro se existirem atualmente problemas de conectividade com a fonte de dados ou destino, como a conectividade lenta da internet, ou se o conjunto de dados for grande e/ou a consulta SQL para ler nos dados realiza um processamento complicado.  

**Resolução:** Determine se existem atualmente problemas com ligações lentas ao armazenamento do Azure ou à internet.  

|Mensagens de Exceção|
|------------------------|
|O tempo limite de ligação ocorreu.|


## <a name="error-0073"></a>Erro 0073  
 A exceção ocorre se ocorrer um erro ao converter uma coluna para outro tipo.  

 Este erro na Aprendizagem automática Azure ocorre quando não é possível converter coluna para outro tipo.  Receberá este erro se um módulo necessitar de um determinado tipo e não for possível converter a coluna para o novo tipo.  

**Resolução:** Modifique o conjunto de dados de entrada para que a coluna possa ser convertida com base na exceção interna.  

|Mensagens de Exceção|
|------------------------|
|Falhou em converter a coluna.|
|Falhou em converter a coluna para {target_type}.|


## <a name="error-0075"></a>Erro 0075  
A exceção ocorre quando uma função de binário inválido é utilizada ao quantificar um conjunto de dados.  

Este erro na Aprendizagem automática Azure ocorre quando está a tentar remender dados usando um método não suportado, ou quando as combinações de parâmetros são inválidas.  

**Resolução:**

O tratamento de erros para este evento foi introduzido numa versão anterior do Azure Machine Learning que permitiu uma maior personalização dos métodos de binário. Atualmente, todos os métodos de ligação são baseados numa seleção de uma lista de abandono, pelo que, tecnicamente, não deverá continuar a ser possível obter este erro.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Microsoft Q&A question page for Azure Machine Learning](/answers/topics/azure-machine-learning-studio-classic.html), providing the data types, parameter settings, and the exact error message.  -->

|Mensagens de Exceção|
|------------------------|
|Função de binário inválido utilizada.|


## <a name="error-0077"></a>Erro 0077  
 A exceção ocorre quando o modo de escrita de ficheiro blob desconhecido passou.  

 Este erro no Azure Machine Learning ocorre se um argumento inválido for transmitido nas especificações para um destino ou fonte de ficheiro blob.  

**Resolução:** Em quase todos os módulos que importam ou exportam dados de e para o armazenamento de blob Azure, os valores dos parâmetros que controlam o modo de escrita são atribuídos através de uma lista de abandono; portanto, não é possível passar um valor inválido, e este erro não deve aparecer. Este erro será depreciado numa versão posterior.  

|Mensagens de Exceção|
|------------------------|
|Modo de escrita de bolhas não suportadas.|
|Modo de escrita de bolhas não suportadas: {blob_write_mode}.|


## <a name="error-0078"></a>Erro 0078  
 A exceção ocorre quando a opção HTTP para [Dados de Importação](import-data.md) recebe um código de estado de 3xx indicando a reorientação.  

 Este erro na Aprendizagem automática Azure ocorre quando a opção HTTP para [Dados de Importação](import-data.md) recebe um código de estado de 3xx (301, 302, 304, etc.) que indica a reorientação. Receberá este erro se tentar ligar-se a uma fonte HTTP que redireciona o navegador para outra página. Por razões de segurança, a reorientação de websites não é permitida como fontes de dados para a Azure Machine Learning.  

**Resolução:** Se o site for um site de confiança, insira o URL redirecionado diretamente.  

|Mensagens de Exceção|
|------------------------|
|Http reorientação não é permitido.|


## <a name="error-0079"></a>Erro 0079  
 A exceção ocorre se o nome do recipiente de armazenamento Azure for especificado incorretamente.  

 Este erro na Aprendizagem automática Azure ocorre se o nome do recipiente de armazenamento Azure for especificado incorretamente. Receberá este erro se não tiver especificado o nome do recipiente e do blob (ficheiro) utilizando o caminho **para blob começar com** a opção de recipiente ao escrever para O Armazenamento Azure Blob.  

**Resolução:** Reveja o módulo [dados de exportação](export-data.md) e verifique se o caminho especificado para a bolha contém tanto o recipiente como o nome do ficheiro, no **formato recipiente/nome de arquivo**.  

|Mensagens de Exceção|
|------------------------|
|O nome do recipiente de armazenamento Azure está incorreto.|
|O nome do recipiente de armazenamento Azure "{container_name}" está incorreto; Esperava-se um nome de contentor do recipiente/bolha de formato.|


## <a name="error-0080"></a>Erro 0080  
 A exceção ocorre quando a coluna com todos os valores em falta não é permitida pelo módulo.  

 Este erro no Azure Machine Learning é produzido quando uma ou mais das colunas consumidas pelo módulo contêm todos os valores em falta. Por exemplo, se um módulo estiver a calcular estatísticas agregadas para cada coluna, não pode funcionar numa coluna que não contém dados. Nesses casos, a execução do módulo é interrompida com esta exceção.  

**Resolução:** Reveja o conjunto de dados de entrada e remova quaisquer colunas que contenham todos os valores em falta.  

|Mensagens de Exceção|
|------------------------|
|Não são permitidas colunas com todos os valores em falta.|
|Coluna {col_index_or_name} tem todos os valores em falta.|


## <a name="error-0081"></a>Erro 0081  
 A exceção ocorre no módulo PCA se o número de dimensões a reduzir for igual ao número de colunas de características no conjunto de dados de entrada, contendo pelo menos uma coluna de características escassas.  

 Este erro no Azure Machine Learning é produzido se forem satisfeitas as seguintes condições: a O conjunto de dados de entrada tem pelo menos uma coluna escassa e (b) o número final de dimensões solicitadas é o mesmo que o número de dimensões de entrada.  

**Resolução:** Considere reduzir o número de dimensões na saída para ser inferior ao número de dimensões na entrada. Isto é típico em aplicações de APC.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->

|Mensagens de Exceção|
|------------------------|
|Para o conjunto de dados que contenha colunas de características escassas, o número de dimensões a reduzir deve ser inferior ao número de colunas de características.|


## <a name="error-0082"></a>Erro 0082  
 A exceção ocorre quando um modelo não pode ser deseralizado com sucesso.  

 Este erro no Azure Machine Learning ocorre quando um modelo ou transformação de máquinas salvas não pode ser carregado por uma versão mais recente do tempo de funcionamento da Azure Machine Learning como resultado de uma mudança de rutura.  

**Resolução:** O gasoduto de treino que produziu o modelo ou a transformação deve ser reexaminado e o modelo ou transformação deve ser ressarcida.  

|Mensagens de Exceção|
|------------------------|
|O modelo não pôde ser deseralizado porque é provável que seja serializado com um formato de serialização mais antigo. Por favor, retreine e relemine o modelo.|


## <a name="error-0083"></a>Erro 0083  
 A exceção ocorre se o conjunto de dados utilizado para a formação não puder ser utilizado para o tipo de aprendiz de concreto.  

 Este erro no Azure Machine Learning é produzido quando o conjunto de dados é incompatível com a formação do aluno. Por exemplo, o conjunto de dados pode conter pelo menos um valor em falta em cada linha, e como resultado, todo o conjunto de dados seria ignorado durante o treino. Noutros casos, alguns algoritmos de aprendizagem automática, como a deteção de anomalias, não esperam que as etiquetas estejam presentes e podem lançar esta exceção se as etiquetas estiverem presentes no conjunto de dados.  

**Resolução:** Consulte a documentação do aluno que está a ser utilizado para verificar os requisitos do conjunto de dados de entrada. Examine as colunas para ver se todas as colunas necessárias estão presentes.  

|Mensagens de Exceção|
|------------------------|
|O conjunto de dados utilizado para o treino é inválido.|
|{data_name} contém dados inválidos para o treino.|
|{data_name} contém dados inválidos para o treino. Tipo de aprendiz: {learner_type}.|
|{data_name} contém dados inválidos para o treino. Tipo de aprendiz: {learner_type}. Razão: {reason}.|
|Não conseguiu aplicar ação "{action_name}" em dados de treino {data_name}. Razão: {reason}.|


## <a name="error-0084"></a>Erro 0084  
 A exceção ocorre quando as pontuações produzidas a partir de um Script R são avaliadas. Isto não é apoiado.  

 Este erro no Azure Machine Learning ocorre se tentar utilizar um dos módulos para avaliar um modelo com saída de um script R que contenha pontuações.  

**Resolução:**

|Mensagens de Exceção|
|------------------------|
|A avaliação das pontuações produzidas pelo Custom Model não é atualmente suportada.|


## <a name="error-0085"></a>Erro 0085  
 A exceção ocorre quando a avaliação do script falha com um erro.  

 Este erro no Azure Machine Learning ocorre quando está a executar scripts personalizados que contêm erros de sintaxe.  

**Resolução:** Reveja o seu código num editor externo e verifique se há erros.  

|Mensagens de Exceção|
|------------------------|
|Erro durante a avaliação do script.|
|O seguinte erro ocorreu durante a avaliação do script, por favor veja o registo de saída para mais informações:<br />---------- Mensagem de erro inicial do intérprete {script_language} ----------<br />{mensagem}<br />---------- Fim da mensagem de erro do intérprete {script_language} ----------|


## <a name="error-0090"></a>Erro 0090  
 A exceção ocorre quando a criação da mesa da Colmeia falha.  

 Este erro no Azure Machine Learning ocorre quando está a utilizar [dados de exportação](export-data.md) ou outra opção para guardar dados para um cluster HDInsight e a tabela de Colmeia especificada não pode ser criada.  

**Resolução:** Verifique o nome da conta de armazenamento Azure associada ao cluster e verifique se está a utilizar a mesma conta nas propriedades do módulo.  

|Mensagens de Exceção|
|------------------------|
|A mesa da Colmeia não pôde ser criada. Para um cluster HDInsight, certifique-se de que o nome da conta de armazenamento Azure associado ao cluster é o mesmo que o que é passado através do parâmetro do módulo.|
|A mesa hive "{table_name}" não pôde ser criada. Para um cluster HDInsight, certifique-se de que o nome da conta de armazenamento Azure associado ao cluster é o mesmo que o que é passado através do parâmetro do módulo.|
|A mesa hive "{table_name}" não pôde ser criada. Para um cluster HDInsight, certifique-se de que o nome da conta de armazenamento Azure associado ao cluster é "{cluster_name}".|


## <a name="error-0102"></a>Erro 0102  
 Lançado quando um ficheiro ZIP não pode ser extraído.  

 Este erro no Azure Machine Learning ocorre quando está a importar um pacote com fecho com a extensão .zip, mas o pacote ou não é um ficheiro zip, ou o ficheiro não utiliza um formato zip suportado.  

**Resolução:** Certifique-se de que o ficheiro selecionado é um ficheiro .zip válido e que foi comprimido utilizando um dos algoritmos de compressão suportados.  

 Se tiver este erro ao importar conjuntos de dados em formato comprimido, verifique se todos os ficheiros contidos utilizam um dos formatos de ficheiros suportados e estão em formato Unicode.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->

 Tente ler os ficheiros desejados para uma nova pasta com fecho comprimido e tente voltar a adicionar o módulo personalizado.  

|Mensagens de Exceção|
|------------------------|
|Dado que o ficheiro ZIP não está no formato correto.|


## <a name="error-0105"></a>Erro 0105  
 Este erro é apresentado quando um ficheiro de definição de módulo contém um tipo de parâmetro não suportado  
  
 Este erro no Azure Machine Learning é produzido quando se cria uma definição de xml de módulo personalizado e o tipo de parâmetro ou argumento na definição não corresponde a um tipo suportado.  
  
**Resolução:** Certifique-se de que a propriedade tipo de qualquer elemento **Arg** no ficheiro de definição de módulo personalizado xml é um tipo suportado.  
  
|Mensagens de Exceção|  
|------------------------|  
|Tipo de parâmetro não suportado.|  
|Tipo de parâmetro não suportado {0} ' especificado.|  


## <a name="error-0107"></a>Erro 0107  
 Lançado quando um ficheiro de definição de módulo define um tipo de saída não suportado  
  
 Este erro no Azure Machine Learning é produzido quando o tipo de porta de saída numa definição de xml de módulo personalizado não corresponde a um tipo suportado.  
  
**Resolução:** Certifique-se de que a propriedade tipo de um elemento de saída no ficheiro de definição de módulo personalizado xml é um tipo suportado.  
  
|Mensagens de Exceção|  
|------------------------|  
|Tipo de saída não suportado.|  
|Especificado o tipo de saída não suportado '{output_type}'.|  


## <a name="error-0125"></a>Erro 0125  
 Lançado quando o esquema de vários conjuntos de dados não corresponde.  

**Resolução:**

|Mensagens de Exceção|
|------------------------|
|O esquema do conjunto de dados não corresponde.|


## <a name="error-0127"></a>Erro 0127  
 O tamanho do pixel de imagem excede o limite permitido  

 Este erro ocorre se estiver a ler imagens de um conjunto de dados de imagem para classificação e as imagens forem maiores do que o modelo pode suportar.  

 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  

-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->

|Mensagens de Exceção|
|------------------------|
|O tamanho do pixel de imagem excede o limite permitido.|
|O tamanho do pixel de imagem no ficheiro '{file_path}' excede o limite permitido: '{size_limit}'.|


## <a name="error-0128"></a>Erro 0128  
 O número de probabilidades condicionais para colunas categóricas excede o limite.  

**Resolução:**

|Mensagens de Exceção|
|------------------------|
|O número de probabilidades condicionais para colunas categóricas excede o limite.|
|O número de probabilidades condicionais para colunas categóricas excede o limite. Colunas '{column_name_or_index_1}' e '{column_name_or_index_2}' são o par problemático.|


## <a name="error-0129"></a>Erro 0129  
 O número de colunas no conjunto de dados excede o limite permitido.  

**Resolução:**

|Mensagens de Exceção|
|------------------------|
|O número de colunas no conjunto de dados excede o limite permitido.|
|O número de colunas no conjunto de dados em '{dataset_name}' excede o permitido.|
|O número de colunas no conjunto de dados em '{dataset_name}' excede o limite permitido de '{component_name}'.|
|O número de colunas no conjunto de dados em '{dataset_name}' excede o limite permitido de '{limit_columns_count}' de '{component_name}'.|


## <a name="error-0134"></a>Erro 0134
A exceção ocorre quando a coluna da etiqueta está em falta ou tem um número insuficiente de linhas rotuladas.  

Este erro ocorre quando o módulo requer uma coluna de etiqueta, mas não incluiu uma na seleção da coluna, ou a coluna da etiqueta está a perder demasiados valores.

Este erro também pode ocorrer quando uma operação anterior altera o conjunto de dados de modo a que linhas insuficientes estejam disponíveis para uma operação a jusante. Por exemplo, suponha que use uma expressão no módulo **Partição e Amostra** para dividir um conjunto de dados por valores. Se não forem encontrados fósforos para a sua expressão, um dos conjuntos de dados resultantes da partição estaria vazio.

Resolução: 

 Se incluir uma coluna de etiqueta na seleção da coluna, mas não é reconhecida, utilize o módulo [editar metadados](edit-metadata.md) para a marcar como coluna de etiqueta.

  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->
  Em seguida, pode utilizar o módulo [De Dados Desaparecidos Limpos](clean-missing-data.md) para remover linhas com valores em falta na coluna da etiqueta. 

 Verifique os seus conjuntos de dados de entrada para se certificar de que contêm dados válidos e linhas suficientes para satisfazer os requisitos da operação. Muitos algoritmos gerarão uma mensagem de erro se precisarem de algumas linhas mínimas de número de dados, mas os dados contêm apenas algumas linhas, ou apenas um cabeçalho.

|Mensagens de Exceção|
|------------------------|
|A exceção ocorre quando a coluna da etiqueta está em falta ou tem um número insuficiente de linhas rotuladas.|
|A exceção ocorre quando a coluna da etiqueta está em falta ou tem menos de {required_rows_count} linhas etiquetadas.|
|A exceção ocorre quando a coluna de etiquetas no conjunto de dados {dataset_name} está em falta ou tem menos de {required_rows_count} linhas etiquetadas.|


## <a name="error-0138"></a>Erro 0138  
 A memória foi esgotada, incapaz de completar o funcionamento do módulo. A desafinação do conjunto de dados pode ajudar a aliviar o problema.  

 Este erro ocorre quando o módulo que está em funcionamento requer mais memória do que está disponível no recipiente Azure. Isto pode acontecer se estiver a trabalhar com um conjunto de dados grande e a operação atual não se encaixar na memória.  

**Resolução:** Se estiver a tentar ler um conjunto de dados grande e a operação não puder ser concluída, a desafinação do conjunto de dados pode ajudar.  

  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  

 Sometimes transient loads can lead to this error. Machine support also changes over time. 

 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->

|Mensagens de Exceção|
|------------------------|
|A memória foi esgotada, incapaz de completar o funcionamento do módulo.|
|A memória foi esgotada, incapaz de completar o funcionamento do módulo. Detalhes: {detalhes}|


## <a name="error-0141"></a>Erro 0141  
 A exceção ocorre se o número das colunas numéricas selecionadas e valores únicos nas colunas categóricas e de cordas for demasiado pequeno.  

 Este erro na Aprendizagem automática Azure ocorre quando não existem valores únicos suficientes na coluna selecionada para realizar a operação.  

**Resolução:** Algumas operações realizam operações estatísticas em funções e colunas categóricas, e se não houver valores suficientes, a operação pode falhar ou devolver um resultado inválido. Verifique o seu conjunto de dados para ver quantos valores existem nas colunas de funcionalidades e etiquetas e determine se a operação que está a tentar realizar é estatisticamente válida.  

 Se o conjunto de dados de origem for válido, poderá também verificar se alguma manipulação de dados a montante ou operação de metadados alterou os dados e removeu alguns valores.  

 Se as operações a montante incluírem a divisão, a amostragem ou o resamplamento, verifique se as saídas contêm o número esperado de linhas e valores.  

|Mensagens de Exceção|
|------------------------|
|O número de colunas numéricas selecionadas e valores únicos nas colunas categóricas e de cordas é demasiado pequeno.|
|O número total das colunas numéricas selecionadas e valores únicos nas colunas categóricas e de cordas (atualmente {atual_num}) deve ser pelo menos {lower_boundary}.|


## <a name="error-0154"></a>Erro 0154  
 A exceção ocorre quando o utilizador tenta juntar dados em colunas-chave com o tipo de coluna incompatível.

|Mensagens de Exceção|
|------------------------|
|Os tipos de elementos de coluna-chave não são compatíveis.|
|Os tipos de elementos de coluna-chave não são compatíveis. (esquerda: {keys_left}; direita: {keys_right})|


## <a name="error-0155"></a>Erro 0155  
 A exceção ocorre quando os nomes das colunas do conjunto de dados não são de cadeia.

|Mensagens de Exceção|
|------------------------|
|O nome da coluna dataframe deve ser do tipo de corda. Os nomes das colunas não são cordas.|
|O nome da coluna dataframe deve ser do tipo de corda. Os nomes das colunas {column_names} não são cordas.|


## <a name="error-0156"></a>Erro 0156  
 A exceção ocorre quando não foi lido os dados da Base de Dados Azure SQL.

|Mensagens de Exceção|
|------------------------|
|Não consegui ler dados da Base de Dados Azure SQL.|
|Não conseguiu ler os dados da Base de Dados Azure SQL: {detailed_message} DB: {database_server_name}:{database_name} Consulta: {sql_statement}|


## <a name="error-0157"></a>Erro 0157  
 Datastore não encontrado.

|Mensagens de Exceção|
|------------------------|
|A informação da datastore é inválida.|
|A informação da datastore é inválida. Não conseguiu obter a loja de dados AzureML '{datastore_name}' no espaço de trabalho '{workspace_name}'.|


## <a name="error-0158"></a>Erro 0158
 Jogado quando um diretório de transformação é inválido.

|Mensagens de Exceção|
|------------------------------------------------------------|
|Dado que a Diretiva de Transformação é inválida.|
|TransformationDirectory "{arg_name}" é inválido. Razão: {reason}. Por favor, refasse a experiência de treino que gera o ficheiro Transform. Se a experiência de treino foi eliminada, por favor recrie e guarde o ficheiro Transform.|
|TransformationDirectory "{arg_name}" é inválido. Razão: {reason}. {troubleshoot_hint}|


## <a name="error-0159"></a>Erro 0159
 A exceção ocorre se o diretório do modelo do módulo for inválido. 

|Mensagens de Exceção|
|------------------------------------------------------------|
|Dado o ModelDirectory é inválido.|
|ModelDirectory "{arg_name}" é inválido.|
|ModelDirectory "{arg_name}" é inválido. Razão: {reason}.|
|ModelDirectory "{arg_name}" é inválido. Razão: {reason}. {troubleshoot_hint}|


## <a name="error-1000"></a>Erro 1000  
Exceção da biblioteca interna.  

Este erro é fornecido para capturar erros internos do motor de outra forma não manipulados. Portanto, a causa deste erro pode ser diferente dependendo do módulo que gerou o erro.  

Para obter mais ajuda, recomendamos que publique a mensagem detalhada que acompanha o erro no [fórum Azure Machine Learning](/answers/topics/azure-machine-learning.html), juntamente com uma descrição do cenário, incluindo os dados utilizados como entradas. Este feedback ajudar-nos-á a priorizar os erros e a identificar as questões mais importantes para o trabalho suplementar.  

|Mensagens de Exceção|
|------------------------|
|Exceção da biblioteca.|
|Exceção da biblioteca: {exceção}.|
|Exceção da biblioteca desconhecida: {exceção}. {customer_support_guidance}.|