---
title: Erros no módulo de resolução de problemas
titleSuffix: Azure Machine Learning
description: Exceções do módulo de resolução de problemas em designer de Machine Learning Azure usando códigos de erro
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/16/2020
ms.openlocfilehash: bfb70aaa092cc62fbff87e9e3e327ee7364f8701
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83833818"
---
# <a name="exceptions-and-error-codes-for-the-designer-preview"></a>Exceções e códigos de erro para o designer (pré-visualização)

Este artigo descreve as mensagens de erro e códigos de exceção no designer de Machine Learning Azure (pré-visualização) para ajudá-lo a resolver os seus oleodutos de aprendizagem automática.

Pode encontrar a mensagem de erro no designer seguindo estes passos:  

- Selecione o módulo falhado, vá ao separador **de registos Outputs+,** pode encontrar o registo detalhado no ficheiro **70_driver_log.txt** na categoria de **registos em azul.**

- Para um erro detalhado do módulo, pode verificar na categoria error_info.json na categoria **module_statistics.**

Seguem-se códigos de erro de módulos no designer.

## <a name="error-0001"></a>Erro 0001  
 A exceção ocorre se uma ou mais colunas especificadas do conjunto de dados não puderem ser encontradas.  

 Receberá este erro se for feita uma seleção de colunas para um módulo, mas as colunas selecionadas não existem no conjunto de dados de entrada. Este erro pode ocorrer se tiver digitado manualmente um nome de coluna ou se o seletor de colunas tiver fornecido uma coluna sugerida que não existia no seu conjunto de dados quando executou o pipeline.  

**Resolução:** Reveja o módulo lançando esta exceção e valide que o nome ou nomes da coluna estão corretos e que todas as colunas referenciadas existem.  

|Mensagens de Exceção|
|------------------------|
|Não foram encontradas uma ou mais colunas especificadas.|
|Coluna com nome ou índice "{column_id}" não encontrada.|
|Coluna com nome ou índice "{column_id}" não existe em "{arg_name_missing_column}".|
|Coluna com nome ou índice "{column_id}" não existe em "{arg_name_missing_column}", mas existe em "{arg_name_has_column}".|
|Colunas com nome ou índice "{column_names}" não encontradas.|
|Colunas com nome ou índice "{column_names}" não existem em "{arg_name_missing_column}".|
|Colunas com nome ou índice "{column_names}" não existe em "{arg_name_missing_column}", mas existe em "{arg_name_has_column}".|


## <a name="error-0002"></a>Erro 0002  
 A exceção ocorre se um ou mais parâmetros não puderem ser analisados ou convertidos do tipo especificado para o tipo de método-alvo.  

 Este erro ocorre no Azure Machine Learning quando especifica um parâmetro como entrada e o tipo de valor é diferente do tipo que se espera, e a conversão implícita não pode ser realizada.  

**Resolução:** Verifique os requisitos do módulo e determine qual o tipo de valor necessário (corda, inteiro, duplo, etc.)  

|Mensagens de Exceção|
|------------------------|
|Falhou em analisar o parâmetro.|
|Falhou em analisar o parâmetro "{arg_name_or_column}".|
|Falhou na conversão do parâmetro "{arg_name_or_column}" para "{to_type}".|
|Falhou na conversão do parâmetro "{arg_name_or_column}" de "{from_type}" para "{to_type}".|
|Falhou em converter o valor do parâmetro "{arg_name_or_column}" de "{arg_value from_type}" para "{to_type}".|
|Falhou em converter o valor "{arg_value}" na coluna "{arg_name_or_column}" de "{from_type}" para "{to_type}" com a utilização do formato "{fmt}" fornecido.|


## <a name="error-0003"></a>Erro 0003  
 A exceção ocorre se uma ou mais das inputs estiverem nulas ou vazias.  

 Receberá este erro no Azure Machine Learning se alguma entrada ou parâmetros de um módulo estiverem nulos ou vazios.  Este erro pode ocorrer, por exemplo, quando não escreveu qualquer valor para um parâmetro. Também pode acontecer se escolher um conjunto de dados que tenha valores em falta, ou um conjunto de dados vazio.  

**Resolução:**

+ Abra o módulo que produziu a exceção e verifique se todas as inputs foram especificadas. Certifique-se de que todas as inputs necessárias são especificadas. 
+ Certifique-se de que os dados que são carregados a partir do armazenamento do Azure são acessíveis e que o nome da conta ou chave não mudou.  
+ Verifique os dados de entrada para valores em falta ou nulos.
+ Se utilizar uma consulta numa fonte de dados, verifique se os dados estão a ser devolvidos no formato que espera. 
+ Verifique se existem erros ou outras alterações na especificação dos dados.
  
|Mensagens de Exceção|
|------------------------|
|Uma ou mais das inputs são nulas ou vazias.|
|A entrada "{name}" é nula ou vazia.|


## <a name="error-0004"></a>Erro 0004  
 A exceção ocorre se o parâmetro for inferior ou igual a valor específico.  

 Receberá este erro no Azure Machine Learning se o parâmetro na mensagem estiver abaixo do valor de limite necessário para que o módulo processe os dados.  

**Resolução:** Reveja o módulo lançando a exceção e modifique o parâmetro para ser superior ao valor especificado.  

|Mensagens de Exceção|
|------------------------|
|O parâmetro deve ser maior do que o valor limite.|
|O valor do parâmetro "{arg_name}" deve ser superior ao valor de {lower_boundary}.|
|O parâmetro "{arg_name}" tem valor "{atual_value}" que deve ser superior a {lower_boundary}.|


## <a name="error-0005"></a>Erro 0005  
 A exceção ocorre se o parâmetro for inferior a um valor específico.  

 Receberá este erro no Azure Machine Learning se o parâmetro na mensagem estiver abaixo ou igual a um valor de limite necessário para que o módulo processe os dados.  

**Resolução:** Reveja o módulo lançando a exceção e modifique o parâmetro para ser maior ou igual ao valor especificado.  

|Mensagens de Exceção|
|------------------------|
|O parâmetro deve ser maior ou igual ao valor limite.|
|O valor do parâmetro "{arg_name}" deve ser maior ou igual a {lower_boundary}.|
|O parâmetro "{arg_name}" tem valor "{value}" que deve ser maior ou igual a {lower_boundary}.|


## <a name="error-0006"></a>Erro 0006  
 A exceção ocorre se o parâmetro for maior ou igual ao valor especificado.  

 Receberá este erro no Azure Machine Learning se o parâmetro na mensagem for maior ou igual a um valor de limite necessário para que o módulo processe os dados.  

**Resolução:** Reveja o módulo lançando a exceção e modifique o parâmetro para ser inferior ao valor especificado.  

|Mensagens de Exceção|
|------------------------|
|Parâmetros desfasam. Um dos parâmetros deve ser menos do que outro.|
|O valor do parâmetro "{arg_name}" deve ser inferior ao valor do parâmetro "{upper_boundary_parameter_name}".|
|O parâmetro "{arg_name}" tem o valor "{value}" que deve ser inferior a {upper_boundary_parameter_name}.|


## <a name="error-0007"></a>Erro 0007  
 A exceção ocorre se o parâmetro for maior do que um valor específico.  

 Receberá este erro no Azure Machine Learning se, nas propriedades do módulo, tiver especificado um valor superior ao permitido. Por exemplo, pode especificar um dado fora do intervalo de datas suportadas, ou pode indicar que cinco colunas são usadas quando apenas três colunas estão disponíveis. 

 Também pode ver este erro se estiver a especificar dois conjuntos de dados que precisam de corresponder de alguma forma. Por exemplo, se estiver a renomear colunas e especificar as colunas por índice, o número de nomes que fornece deve corresponder ao número de índices de colunas. Outro exemplo pode ser uma operação matemática que usa duas colunas, onde as colunas devem ter o mesmo número de linhas. 

**Resolução:**

 + Abra o módulo em questão e reveja quaisquer definições de propriedade numérica.
 + Certifique-se de que os valores dos parâmetros se enquadram na gama de valores suportados para esse imóvel.
 + Se o módulo tiver várias inputs, certifique-se de que as inputs têm o mesmo tamanho.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Verifique se o conjunto de dados ou a fonte de dados mudaram. Por vezes, um valor que funcionou com uma versão anterior dos dados falhará após o número de colunas, os tipos de dados da coluna ou o tamanho dos dados ter mudado.  

|Mensagens de exceção|
|------------------------|
|Parâmetros desfasam. Um dos parâmetros deve ser inferior ou igual a outro.|
|O valor do parâmetro "{arg_name}" deve ser inferior ou igual ao valor do parâmetro "{upper_boundary_parameter_name}".|
|O parâmetro "{arg_name}" tem valor "{atual_value}" que deve ser inferior ou igual a {upper_boundary}.|
|O valor do parâmetro "{arg_name}" {atual_value} deve ser inferior ou igual ao valor do parâmetro "{upper_boundary_parameter_name}" {upper_boundary}.|
|O valor do parâmetro "{arg_name}" {atual_value} deve ser inferior ou igual ao valor de {upper_boundary_meaning} {upper_boundary}.|


## <a name="error-0008"></a>Erro 0008  
 A exceção ocorre se o parâmetro não estiver ao alcance.  

 Receberá este erro no Azure Machine Learning se o parâmetro na mensagem estiver fora dos limites necessários para que o módulo processe os dados.  

 Por exemplo, este erro é apresentado se tentar utilizar Add [Rows](add-rows.md) para combinar dois conjuntos de dados que têm um número diferente de colunas.  

**Resolução:** Reveja o módulo lançando a exceção e modifique o parâmetro para estar dentro do intervalo especificado.  

|Mensagens de Exceção|
|------------------------|
|O valor do parâmetro não está na gama especificada.|
|O valor do parâmetro "{arg_name}" não está ao alcance.|
|O valor do parâmetro "{arg_name}" deve estar na gama de {lower_boundary}, {upper_boundary}].|
|O valor do parâmetro "{arg_name}" não está ao alcance. {razão}|


## <a name="error-0009"></a>Erro 0009  
 A exceção ocorre quando o nome da conta de armazenamento Azure ou o nome do contentor são especificados incorretamente.  

Este erro ocorre no designer de Machine Learning Azure quando especifica parâmetros para uma conta de armazenamento Azure, mas o nome ou palavra-passe não pode ser resolvido. Erros em palavras-passe ou nomes de conta podem acontecer por muitas razões:

 + A conta é do tipo errado. Alguns novos tipos de conta não são suportados para uso com o designer de Machine Learning. Consulte [os Dados de Importação](import-data.md) para mais detalhes.
 + Inseriu o nome da conta incorreta
 + A conta já não existe
 + A palavra-passe para a conta de armazenamento está errada ou mudou
 + Não especificou o nome do recipiente, ou o recipiente não existe.
 + Não especificou completamente o caminho do ficheiro (caminho para a bolha)
   

**Resolução:**

Tais problemas ocorrem frequentemente quando se tenta introduzir manualmente o nome da conta, palavra-passe ou caminho do contentor. Recomendamos que utilize o novo assistente para o módulo [De dados de importação,](import-data.md) o que o ajuda a procurar e verificar nomes.

Verifique também se a conta, o contentor ou a bolha foram eliminados. Utilize outro utilitário de armazenamento Azure para verificar se o nome da conta e a palavra-passe foram introduzidos corretamente e que o recipiente existe. 

Alguns tipos de conta mais recentes não são suportados pela Azure Machine Learning. Por exemplo, os novos tipos de armazenamento "quentes" ou "frios" não podem ser utilizados para aprendizagem automática. Tanto as contas clássicas de armazenamento como as contas de armazenamento criadas como "propósito geral" funcionam bem.

Se o caminho completo para uma bolha tiver sido especificado, verifique se o caminho está especificado como **recipiente/blobname,** e que tanto o recipiente como a bolha existem na conta.  

 O caminho não deve conter um corte de liderança. Por **exemplo/contentor/bolha** está incorreto e deve ser introduzido como **recipiente/bolha**.  


|Mensagens de Exceção|
|------------------------|
|O nome da conta de armazenamento Azure ou do nome do contentor está incorreto.|
|A denominação de conta de armazenamento Azure "{account_name}" ou o nome do recipiente "{container_name}" está incorreta; Esperava-se um nome de contentor do recipiente/bolha de formato.|


## <a name="error-0010"></a>Erro 0010  
 A exceção ocorre se os conjuntos de dados de entrada tiverem nomes de colunas que devem corresponder, mas não o fazem.  

 Receberá este erro no Azure Machine Learning se o índice de colunas na mensagem tiver diferentes nomes de colunas nos dois conjuntos de dados de entrada.  

**Resolução:** Utilize [editar Metadados](edit-metadata.md) ou modificar o conjunto de dados original para ter o mesmo nome de coluna para o índice de coluna especificado.  

|Mensagens de Exceção|
|------------------------|
|As colunas com índice correspondente nos conjuntos de dados de entrada têm nomes diferentes.|
|Os nomes das colunas não são os mesmos para a coluna {col_index} (com base zero) dos conjuntos de dados de entrada ({dataset1} e {dataset2} respectivamente).|


## <a name="error-0011"></a>Erro 0011  
 A exceção ocorre se o argumento do conjunto de colunas não se aplicar a nenhuma das colunas de conjuntode dados.  

 Receberá este erro no Azure Machine Learning se a seleção de colunas especificada não corresponder a nenhuma das colunas no conjunto de dados dado.  

 Também pode obter este erro se não tiver selecionado uma coluna e seja necessária pelo menos uma coluna para o módulo funcionar.  

**Resolução:** Modifique a seleção da coluna no módulo de modo a aplicar-se às colunas no conjunto de dados.  

 Se o módulo exigir que selecione uma coluna específica, como uma coluna de etiquetas, verifique se a coluna certa está selecionada.  

 Se forem selecionadas colunas inadequadas, retire-as e recorra o gasoduto.  

|Mensagens de Exceção|
|------------------------|
|O conjunto de colunas especificado não se aplica a nenhuma das colunas de conjunto de dados.|
|O conjunto de colunas especificado "{column_set}" não se aplica a nenhuma das colunas do conjunto de dados.|


## <a name="error-0012"></a>Erro 0012  
 A exceção ocorre se o exemplo de classe não puder ser criado com um conjunto de argumentos aprovados.  

**Resolução:** Este erro não é exequível pelo utilizador e será depreciado numa futura versão.  

|Mensagens de Exceção|
|------------------------|
|Modelo destreinado, por favor treine o modelo primeiro.|
|Modelo não treinado ({arg_name}), utilize modelo treinado.|


## <a name="error-0013"></a>Erro 0013  
 A exceção ocorre se o aluno passar para o módulo é um tipo inválido.  

 Este erro ocorre sempre que um modelo treinado é incompatível com o módulo de pontuação ligado. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->

**Resolução:**

Determine o tipo de aprendiz que é produzido pelo módulo de treino, e determine o módulo de pontuação adequado para o aluno. 

Se o modelo foi treinado utilizando algum dos módulos de treino especializados, ligue o modelo treinado apenas ao módulo de pontuação especializado correspondente. 


|Tipo de modelo|Módulo de treino| Módulo de pontuação|
|----|----|----|
|qualquer classificador|[Preparar Modelo](train-model.md) |[Modelo de Pontuação](score-model.md)|
|qualquer modelo de regressão|[Preparar Modelo](train-model.md) |[Modelo de Pontuação](score-model.md)|

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
|Aprendedor de tipo inválido é passado.|
|O aprendiz "{arg_name}" tem um tipo inválido.|
|O aprendiz "{arg_name}" tem o tipo inválido "{learner_type}".|
|Aprendedor de tipo inválido é passado. Mensagem de exceção: {exception_message}|


## <a name="error-0014"></a>Erro 0014  
 A exceção ocorre se a contagem de valores únicos da coluna for maior do que o permitido.  

 Este erro ocorre quando uma coluna contém demasiados valores únicos.  Por exemplo, pode ver este erro se especificar que uma coluna é tratada como dados categóricos, mas existem demasiados valores únicos na coluna para permitir que o processamento seja concluído. Também pode ver este erro se houver um desfasamento entre o número de valores únicos em duas inputs.   

**Resolução:**

Abra o módulo que gerou o erro e identifique as colunas utilizadas como inputs. Para alguns módulos, pode clicar na entrada do conjunto de dados e selecionar **visualize** para obter estatísticas em colunas individuais, incluindo o número de valores únicos e a sua distribuição.

Para colunas que pretende utilizar para agrupamento ou categorização, tome medidas para reduzir o número de valores únicos nas colunas. Pode reduzir de diferentes formas, dependendo do tipo de dados da coluna. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Incapaz de encontrar uma resolução que corresponda ao seu cenário? Pode fornecer feedback sobre este tópico que inclui o nome do módulo que gerou o erro, e o tipo de dados e cardinalidade da coluna. Usaremos a informação para fornecer passos mais direcionados para cenários comuns.   

|Mensagens de Exceção|
|------------------------|
|A quantidade de valores únicos da coluna é maior do que o permitido.|
|Número de valores únicos na coluna: "{column_name}" é maior do que o permitido.|
|Número de valores únicos na coluna: "{column_name}" excede a contagem de tuple de {limitation}.|


## <a name="error-0015"></a>Erro 0015  
 A exceção ocorre se a ligação da base de dados tiver falhado.  

 Receberá este erro se introduzir um nome de conta SQL incorreto, palavra-passe, servidor de base de dados ou nome de base de dados, ou se uma ligação com a base de dados não puder ser estabelecida devido a problemas com a base de dados ou servidor.  

**Resolução:** Verifique se o nome da conta, palavra-passe, servidor de base de dados e base de dados foram introduzidos corretamente e que a conta especificada tem o nível correto de permissões. Verifique se a base de dados está atualmente acessível.  

|Mensagens de Exceção|
|------------------------|
|Ligação de base de dados de erro.|
|Ligação de base de dados de erro: {connection_str}.|


## <a name="error-0016"></a>Erro 0016  
 A exceção ocorre se os conjuntos de dados de entrada passados para o módulo devem ter tipos de colunacompatíveis, mas não o fazem.  

 Receberá este erro no Azure Machine Learning se os tipos de colunas passados em dois ou mais conjuntos de dados não forem compatíveis entre si.  

**Resolução:** Utilizar [Editar Metadados](edit-metadata.md) ou modificar o conjunto de dados de entrada original<!--, or use [Convert to Dataset](convert-to-dataset.md)--> para garantir que os tipos das colunas são compatíveis.  

|Mensagens de Exceção|
|------------------------|
|As colunas com índice correspondente nos conjuntos de dados de entrada têm tipos incompatíveis.|
|As colunas '{first_col_names}' são incompatíveis entre os dados do comboio e do teste.|
|As colunas '{first_col_names}' e '{second_col_names}' são incompatíveis.|
|Os tipos de elementos da coluna não são compatíveis com a coluna '{first_col_names}' (com base em zero) dos conjuntos de dados de entrada ({first_dataset_names} e {second_dataset_names} respectivamente).|


## <a name="error-0017"></a>Erro 0017  
 A exceção ocorre se uma coluna selecionada utilizar um tipo de dados que não seja suportado pelo módulo atual.  

 Por exemplo, poderá receber este erro no Azure Machine Learning se a seleção da coluna incluir uma coluna com um tipo de dados que não possa ser processado pelo módulo, como uma coluna de cordas para uma operação matemática, ou uma coluna de pontuação onde seja necessária uma coluna de características categórica.  

**Resolução:**
 1. Identifique a coluna que é o problema.
 2. Reveja os requisitos do módulo.
 3. Modifique a coluna para que esteja em conformidade com os requisitos. Pode ser necessário utilizar vários dos seguintes módulos para fazer alterações, dependendo da coluna e da conversão que está a tentar:
    + Utilize [metadados editar](edit-metadata.md) para alterar o tipo de dados de colunas, ou para alterar o uso da coluna de função para numérico, categórico para não categórico, e assim por diante.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Como último recurso, poderá ter de modificar o conjunto de dados de entrada original.

> [!TIP]
> Incapaz de encontrar uma resolução que corresponda ao seu cenário? Pode fornecer feedback sobre este tópico que inclui o nome do módulo que gerou o erro, e o tipo de dados e cardinalidade da coluna. Usaremos a informação para fornecer passos mais direcionados para cenários comuns. 

|Mensagens de Exceção|
|------------------------|
|Não é possível processar colunas do tipo atual. O tipo não é suportado pelo módulo.|
|Não é possível processar coluna de tipo {col_type}. O tipo não é suportado pelo módulo.|
|Não é possível processar a coluna "{col_name}" do tipo {col_type}. O tipo não é suportado pelo módulo.|
|Não é possível processar a coluna "{col_name}" do tipo {col_type}. O tipo não é suportado pelo módulo. Nome do parâmetro: {arg_name}.|


## <a name="error-0018"></a>Erro 0018  
 A exceção ocorre se o conjunto de dados de entrada não for válido.  

**Resolução:** Este erro no Azure Machine Learning pode aparecer em muitos contextos, pelo que não existe uma única resolução. Em geral, o erro indica que os dados fornecidos como entrada para um módulo têm o número errado de colunas, ou que o tipo de dados não corresponde aos requisitos do módulo. Por exemplo:  

-   O módulo requer uma coluna de etiqueta, mas nenhuma coluna está marcada como uma etiqueta, ou ainda não selecionou uma coluna de etiquetas.  
  
-   O módulo requer que os dados sejam categóricos, mas os seus dados são numéricos.  

<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->

-   Os dados estão no formato errado.  
  
-   Os dados importados contêm caracteres inválidos, valores ruins ou valores fora do alcance.  
-   A coluna está vazia ou contém demasiados valores em falta.  

 Para determinar os requisitos e como os seus dados podem, reveja o tópico de ajuda para o módulo que irá consumir o conjunto de dados como entrada.  

 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  

|Mensagens de Exceção|
|------------------------|
|O conjunto de dados não é válido.|
|{dataset1} contém dados inválidos.|
|{dataset1} e {dataset2} devem ser consistentes em termos de coluna.|
|{dataset1} contém dados inválidos, {reason}.|
|{dataset1} contém {invalid_data_category}. {troubleshoot_hint}|
|{dataset1} não é válido, {reason}. {troubleshoot_hint}|


## <a name="error-0019"></a>Erro 0019  
 A exceção ocorre se se espera que a coluna contenha valores ordenados, mas não o faz.  

 Receberá este erro no Azure Machine Learning se os valores de coluna especificados estiverem fora de ordem.  

**Resolução:** Separe os valores da coluna modificando manualmente o conjunto de dados de entrada e reexecutar o módulo.  

|Mensagens de Exceção|
|------------------------|
|Os valores na coluna não estão classificados.|
|Os valores na coluna "{col_index}" não são classificados.|
|Os valores na coluna "{col_index}" do conjunto de dados "{dataset}" não estão classificados.|
|Os valores no argumento "{arg_name}" não são classificados na ordem "{sorting_order}".|


## <a name="error-0020"></a>Erro 0020  
 A exceção ocorre se o número de colunas em alguns dos conjuntos de dados passados para o módulo for demasiado pequeno.  

 Receberá este erro no Azure Machine Learning se não forem selecionadas colunas suficientes para um módulo.  

**Resolução:** Reveja o módulo e certifique-se de que o seletor de colunas tem um número correto de colunas selecionadas.  

|Mensagens de Exceção|
|------------------------|
|O número de colunas no conjunto de dados de entrada é inferior ao mínimo permitido.|
|O número de colunas no conjunto de dados de entrada "{arg_name}" é inferior ao mínimo permitido.|
|O número de colunas no conjunto de dados de entrada é inferior ao permitido mínimo de colunas {required_columns_count}|
|O número de colunas no conjunto de dados de entrada "{arg_name}" é inferior ao mínimo permitido de colunas {required_columns_count}|


## <a name="error-0021"></a>Erro 0021  
 A exceção ocorre se o número de linhas em alguns dos conjuntos de dados passados para o módulo for demasiado pequeno.  

 Este erro observado no Azure Machine Learning quando não há filas suficientes no conjunto de dados para executar a operação especificada. Por exemplo, pode ver este erro se o conjunto de dados de entrada estiver vazio ou se estiver a tentar executar uma operação que exija que algum número mínimo de linhas seja válido. Tais operações podem incluir (mas não se limitam a) agrupamento ou classificação com base em métodos estatísticos, certos tipos de ligação e aprendizagem com contagens.  

**Resolução:**

 + Abra o módulo que devolveu o erro e verifique as propriedades do conjunto de dados de entrada e do módulo. 
 + Verifique se o conjunto de dados de entrada não está vazio e que existem filas suficientes de dados para satisfazer os requisitos descritos na ajuda do módulo.  
 + Se os seus dados forem carregados a partir de uma fonte externa, certifique-se de que a fonte de dados está disponível e que não há nenhum erro ou alteração na definição de dados que faça com que o processo de importação obtenha menos linhas.
 + Se estiver a realizar uma operação sobre os dados a montante do módulo que possa afetar o tipo de dados ou o número de valores, tais como limpeza, divisão ou operações de adesão, verifique as saídas dessas operações para determinar o número de linhas devolvidas.  

|Mensagens de Exceção|
|------------------------|
|O número de linhas no conjunto de dados de entrada é inferior ao mínimo permitido.|
|O número de linhas no conjunto de dados de entrada é inferior ao permitido mínimo de {required_rows_count} linha(s).|
|O número de linhas no conjunto de dados de entrada é inferior ao permitido mínimo de {required_rows_count} linha(s). {razão}|
|O número de linhas no conjunto de dados de entrada "{arg_name}" é inferior ao mínimo permitido de {required_rows_count} linha( s).|
|O número de linhas no conjunto de dados de entrada "{arg_name}" é {atual_rows_count}, menos do que o mínimo permitido de {required_rows_count} linha(s).|
|O número de linhas "{row_type}" no conjunto de dados de entrada "{arg_name}" é {atual_rows_count}, menos do que o mínimo permitido de {required_rows_count} linha(s).|


## <a name="error-0022"></a>Erro 0022  
 A exceção ocorre se o número de colunas selecionadas no conjunto de dados de entrada não for igual ao número esperado.  

 Este erro no Azure Machine Learning pode ocorrer quando o módulo ou operação a jusante requer um número específico de colunas ou inputs, e forneceu poucas ou demasiadas colunas ou inputs. Por exemplo:  

-   Especifica uma única coluna de etiquetaou uma coluna de teclas e selecionou acidentalmente várias colunas.  
  
-   Estás a renomear colunas, mas forneceste mais ou menos nomes do que há colunas.  
  
-   O número de colunas na fonte ou destino mudou ou não corresponde ao número de colunas utilizadas pelo módulo.  
  
-   Forneceu uma lista separada de valores para entradas, mas o número de valores não corresponde, ou várias entradas não são suportadas.  

**Resolução:** Reveja o módulo e verifique a seleção da coluna para se certificar de que o número correto de colunas é selecionado. Verifique as saídas de módulos a montante e os requisitos das operações a jusante.  

 Se utilizou uma das opções de seleção da coluna que pode selecionar várias colunas (índices de colunas, todas as características, todas numéricas, etc.), validar o número exato de colunas devolvidas pela seleção.  

 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->

 Verifique se o número ou o tipo de colunas a montante não se alterou.  

 Se estiver a utilizar um conjunto de dados de recomendação para treinar um modelo, lembre-se que o recomendador espera um número limitado de colunas, correspondentes a pares de artigos de utilizador ou classificações de artigos de utilizador. Remova colunas adicionais antes de treinar o modelo ou dividir conjuntos de dados de recomendação. Para mais informações, consulte [Dados Divididos](split-data.md).  

|Mensagens de Exceção|
|------------------------|
|O número de colunas selecionadas no conjunto de dados de entrada não é igual ao número esperado.|
|O número de colunas selecionadas no conjunto de dados de entrada não é igual a {expected_col_count}.|
|O padrão de seleção da coluna "{selection_pattern_friendly_name}" fornece o número de colunas selecionadas no conjunto de dados de entrada não igual a {expected_col_count}.|
|Espera-se que o padrão de seleção da coluna "{selection_pattern_friendly_name}" forneça {expected_col_count} colunas(s) selecionadas no conjunto de dados de entrada, mas é fornecido selected_col_count} colunas..são fornecidas.|


## <a name="error-0023"></a>Erro 0023  

A exceção ocorre se a coluna-alvo do conjunto de dados de entrada não for válida para o módulo de treinador atual.  

Este erro no Azure Machine Learning ocorre se a coluna-alvo (conforme selecionado nos parâmetros do módulo) não for do tipo de dados válido, contiver todos os valores em falta, ou não fosse categórica como esperado.  

**Resolução:** Reveja a entrada do módulo para inspecionar o conteúdo da coluna de etiqueta/alvo. Certifique-se de que não tem todos os valores em falta. Se o módulo estiver à espera que a coluna do alvo seja categórica, certifique-se de que existem mais de um valores distintos na coluna-alvo.  

|Mensagens de Exceção|
|------------------------|
|O conjunto de dados de entrada tem uma coluna de alvo não suportada.|
|O conjunto de dados de entrada tem a coluna de destino não suportada "{column_index}".|
|O conjunto de dados de entrada tem a coluna de destino não suportada "{column_index}" para o aluno do tipo {learner_type}.|


## <a name="error-0024"></a>Erro 0024  
A exceção ocorre se o conjunto de dados não contiver uma coluna de etiqueta.  

 Este erro no Azure Machine Learning ocorre quando o módulo necessita de uma coluna de etiqueta e o conjunto de dados não tem uma coluna de etiqueta. Por exemplo, a avaliação de um conjunto de dados pontuado geralmente requer que uma coluna de etiqueta está presente para calcular métricas de precisão.  

Também pode acontecer que uma coluna de etiquetas esteja presente no conjunto de dados, mas não detetada corretamente pelo Azure Machine Learning.

**Resolução:**

+ Abra o módulo que gerou o erro e determine se uma coluna de etiquetaestá presente. O nome ou tipo de dados da coluna não importa, desde que a coluna contenha um único resultado (ou variável dependente) que esteja a tentar prever. Se não tiver a certeza de qual coluna tem a etiqueta, procure um nome genérico como *Classe* ou *Target*. 
+  Se o conjunto de dados não incluir uma coluna de etiqueta, é possível que a coluna de etiquetas tenha sido explicitamente ou acidentalmente removida a montante. Também pode ser que o conjunto de dados não seja a saída de um módulo de pontuação a montante.
+ Para marcar explicitamente a coluna como coluna de etiquetas, adicione o módulo [Dedados editar](edit-metadata.md) e ligue o conjunto de dados. Selecione apenas a coluna de etiquetas e selecione **Etiqueta** da lista de abandono dos **Campos.** 
+ Se a coluna errada for escolhida como etiqueta, pode selecionar a **etiqueta Clear** dos **Campos** para fixar os metadados na coluna. 
  
|Mensagens de Exceção|
|------------------------|
|Não há nenhuma coluna de etiqueta no conjunto de dados.|
|Não existe nenhuma coluna de etiquetaem "{dataset_name}".|


## <a name="error-0025"></a>Erro 0025  
 A exceção ocorre se o conjunto de dados não contiver uma coluna de pontuação.  

 Este erro no Azure Machine Learning ocorre se a entrada para o modelo de avaliação não contiver colunas de pontuação válidas. Por exemplo, o utilizador tenta avaliar um conjunto de dados antes de ser marcado com um modelo treinado correto, ou a coluna de pontuação foi explicitamente deixada a montante. Esta exceção também ocorre se as colunas de pontuação dos dois conjuntos de dados forem incompatíveis. Por exemplo, pode estar a tentar comparar a precisão de um regresso linear com um classificador binário.  

**Resolução:** Reveja a entrada do modelo de avaliação e examine se contém uma ou mais colunas de pontuação. Caso contrário, o conjunto de dados não foi marcado ou as colunas de pontuação foram largadas num módulo a montante.  

|Mensagens de Exceção|
|------------------------|
|Não há nenhuma coluna de pontuação no conjunto de dados.|
|Não há coluna de pontuação em "{dataset_name}".|
|Não há nenhuma coluna de pontuação em "{dataset_name}" que seja produzida por um "{learner_type}". Marque o conjunto de dados utilizando o tipo correto de aprendizagem.|


## <a name="error-0026"></a>Erro 0026  
 A exceção ocorre se não forem permitidas colunas com o mesmo nome.  

 Este erro no Azure Machine Learning ocorre se várias colunas tiverem o mesmo nome. Uma das formas de receber este erro é se o conjunto de dados não tiver uma linha de cabeçalho e os nomes das colunas forem automaticamente atribuídos: Col0, Col1, etc.  

**Resolução:** Se as colunas tiverem o mesmo nome, insira um módulo [de Metadados de Edição](edit-metadata.md) entre o conjunto de dados de entrada e o módulo. Utilize o seletor de colunas em [Editar Metadados](edit-metadata.md) para selecionar colunas para mudar o nome, digitando os novos nomes na **nova coluna nomea** a caixa de texto.  

|Mensagens de Exceção|
|------------------------|
|Os nomes de colunas iguais são especificados em argumentos. Nomes de colunas iguais não são permitidos pelo módulo.|
|Não são permitidos nomes de colunas iguais nos argumentos "{arg_name_1}" e "{arg_name_2}". Por favor, especifique nomes diferentes.|


## <a name="error-0027"></a>Erro 0027  
 A exceção ocorre no caso de dois objetos terem de ter o mesmo tamanho, mas não são.  

 Este é um erro comum no Azure Machine Learning e pode ser causado por muitas condições.  

**Resolução:** Não há uma resolução específica. No entanto, pode verificar se há condições como:  

-   Se estiver a renomear colunas, certifique-se de que cada lista (as colunas de entrada e a lista de novos nomes) tem o mesmo número de itens.  
  
-   Se estiver a juntar-se ou a concatenar dois conjuntos de dados, certifique-se de que têm o mesmo esquema.  
  
-   Se estiver a juntar dois conjuntos de dados que possuem múltiplas colunas, certifique-se de que as colunas-chave têm o mesmo tipo de dados e selecione a opção Permitir duplicados e preservar a **ordem da coluna na seleção**.  

|Mensagens de Exceção|
|------------------------|
|O tamanho dos objetos passados é inconsistente.|
|O tamanho de "{friendly_name1}" é inconsistente com o tamanho de "{friendly_name2}".|


## <a name="error-0028"></a>Erro 0028  
 A exceção ocorre no caso em que o conjunto de colunas contém nomes de colunas duplicadas e não é permitido.  

 Este erro na Aprendizagem automática azure ocorre quando os nomes das colunas são duplicados; isto é, não é único.  

**Resolução:** Se alguma coluna tiver o mesmo nome, adicione uma instância de [Editar Metadados](edit-metadata.md) entre o conjunto de dados de entrada e o módulo aumentando o erro. Utilize o Seletor de Colunas em [Editar Metadados](edit-metadata.md) para selecionar colunas para mudar o nome e digitar os nomes das novas colunas na **nova coluna nomea** a caixa de texto. Se estiver a renomear várias colunas, certifique-se de que os valores que digita nos **nomes** da coluna Nova são únicos.  

|Mensagens de Exceção|
|------------------------|
|O conjunto de colunas contém nomes de colunas duplicados.|
|O nome "{duplicated_name}" é duplicado.|
|O nome "{duplicated_name}" é duplicado em "{arg_name}".|
|O nome "{duplicated_name}" é duplicado. Detalhes: {detalhes}|


## <a name="error-0029"></a>Erro 0029  
 A exceção ocorre no caso de ser aprovada uri inválida.  

 Este erro na Aprendizagem automática azure ocorre no caso de ser passado o URI inválido.  Receberá este erro se alguma das seguintes condições for verdadeira:  

-   O Public ou SAS URI fornecido para armazenamento de Blob Azure para leitura ou escrita contém um erro.  
  
-   O prazo para o SAS expirou.  
  
-   O URL web via http fonte representa um ficheiro ou um URI loopback.  
  
-   O URL web via HTTP contém um URL mal formatado.  
  
-   O URL não pode ser resolvido pela fonte remota.  

**Resolução:** Revisite o módulo e verifique o formato do URI. Se a fonte de dados for um URL web via HTTP, verifique se a fonte pretendida não é um ficheiro ou um URI (hospedeiro local).  

|Mensagens de Exceção|
|------------------------|
|Uri inválido é passado.|
|O Uri "{invalid_url}" é inválido.|


## <a name="error-0030"></a>Erro 0030  
 A exceção ocorre no caso em que não é possível descarregar um ficheiro.  

 Esta exceção no Azure Machine Learning ocorre quando não é possível descarregar um ficheiro. Receberá esta exceção quando uma tentativa de leitura de uma fonte http falhou após três (3) tentativas de retry.  

**Resolução:** Verifique se o URI à fonte http está correto e que o site está atualmente acessível através da Internet.  

|Mensagens de Exceção|
|------------------------|
|Incapaz de descarregar um ficheiro.|
|Erro ao descarregar o ficheiro: {file_url}.|


## <a name="error-0031"></a>Erro 0031  
 A exceção ocorre se o número de colunas no conjunto de colunas for inferior ao necessário.  

 Este erro no Azure Machine Learning ocorre se o número de colunas selecionadas for inferior ao necessário.  Receberá este erro se o número mínimo exigido de colunas não for selecionado.  

**Resolução:** Adicione colunas adicionais à seleção da coluna utilizando o Seletor de **Colunas**.  

|Mensagens de Exceção|
|------------------------|
|O número de colunas no conjunto de colunas é inferior ao necessário.|
|Pelo menos as colunas de {required_columns_count} devem ser especificadas para o argumento de entrada "{arg_name}".|
|Pelo menos as colunas de {required_columns_count} devem ser especificadas para o argumento de entrada "{arg_name}". O número real de colunas especificadas é {input_columns_count}.|


## <a name="error-0032"></a>Erro 0032  
 A exceção ocorre se o argumento não for um número.  

 Receberá este erro no Azure Machine Learning se o argumento for duplo ou NaN.  

**Resolução:** Modifique o argumento especificado para utilizar um valor válido.  

|Mensagens de Exceção|
|------------------------|
|Argumento não é um número.|
|"{arg_name}" não é um número.|


## <a name="error-0033"></a>Erro 0033  
 A exceção ocorre se o argumento for Infinito.  

 Este erro na Aprendizagem automática azure ocorre se o argumento for infinito. Receberá este erro se o argumento for `double.NegativeInfinity` ou `double.PositiveInfinity` .  

**Resolução:** Modificar o argumento especificado para ser um valor válido.  

|Mensagens de Exceção|
|------------------------|
|Argumento deve ser finito.|
|"{arg_name}" não é finito.|
|A coluna "{column_name}" contém valores infinitos.|


## <a name="error-0034"></a>Erro 0034  
 A exceção ocorre se existir em mais de uma classificação para um determinado par de artigos de utilizador.  

 Este erro no Azure Machine Learning ocorre por recomendação se um par de artigos de utilizador tiver mais de um valor de classificação.  

**Resolução:** Certifique-se de que o par de artigos de utilizador possui apenas um valor de classificação.  

|Mensagens de Exceção|
|------------------------|
|Existe mais do que uma classificação para o valor(s) no conjunto de dados.|
|Mais de uma classificação para o utilizador {utilizador} e item {item} na tabela de dados de previsão de classificação.|
|Mais de uma classificação para o utilizador {utilizador} e item {item} em {dataset}.|


## <a name="error-0035"></a>Erro 0035  
 A exceção ocorre se não forem fornecidas funcionalidades para um determinado utilizador ou item.  

 Este erro no Azure Machine Learning ocorre que está a tentar usar um modelo de recomendação para pontuar, mas não é possível encontrar um vetor de características.  

**Resolução:**

O recomendador Matchbox tem determinados requisitos que devem ser cumpridos quando utilizar as funcionalidades do item ou as funcionalidades do utilizador.  Este erro indica que falta um vetor de funcionalidade para um utilizador ou item que forneceu como entrada. Certifique-se de que um vetor de funcionalidades está disponível nos dados de cada utilizador ou item.  

 Por exemplo, se treinou um modelo de recomendação utilizando funcionalidades como a idade, localização ou rendimento do utilizador, mas agora quer criar pontuações para novos utilizadores que não foram vistos durante o treino, deve fornecer algum conjunto equivalente de funcionalidades (nomeadamente, idade, localização e valores de rendimento) para os novos utilizadores, de forma a fazer previsões adequadas para os mesmos. 

 Caso não possua quaisquer funcionalidades para estes utilizadores, considere a engenharia de funcionalidades para gerar funcionalidades apropriadas.  Por exemplo, se não tiver valores individuais de idade de utilizador ou rendimento, poderá gerar valores aproximados para utilizar para um grupo de utilizadores. 

<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).

For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->

 > [!TIP]
 > Resolução não aplicável ao seu caso? Você é bem-vindo a enviar feedback sobre este artigo e fornecer informações sobre o cenário, incluindo o módulo e o número de linhas na coluna. Utilizaremos esta informação para fornecer passos mais detalhados de resolução de problemas no futuro.

|Mensagens de Exceção|
|------------------------|
|Não foram fornecidas funcionalidades para um utilizador ou item necessários.|
|Características para {required_feature_name} necessárias, mas não fornecidas.|


## <a name="error-0036"></a>Erro 0036  
 A exceção ocorre se vários vetores de características foram fornecidos para um determinado utilizador ou item.  

 Este erro no Azure Machine Learning ocorre se um vetor de características for definido mais de uma vez.  

**Resolução:** Certifique-se de que o vetor de características não é definido mais de uma vez.  

|Mensagens de Exceção|
|------------------------|
|Duplicar a definição de funcionalidade para um utilizador ou item.|


## <a name="error-0037"></a>Erro 0037  
 A exceção ocorre se várias colunas de etiquetas forem especificadas e apenas uma for permitida.  

 Este erro no Azure Machine Learning ocorre se for selecionada mais de uma coluna para ser a nova coluna de etiquetas. A maioria dos algoritmos de aprendizagem supervisionados requer que uma única coluna seja marcada como o alvo ou rótulo.  

**Resolução:** Certifique-se de selecionar uma única coluna como a nova coluna de etiquetas.  

|Mensagens de Exceção|
|------------------------|
|São especificadas várias colunas de etiquetas.|
|Várias colunas de etiquetas são especificadas em "{dataset_name}".|


## <a name="error-0039"></a>Erro 0039  
 A exceção ocorre se uma operação tiver falhado.  

 Este erro na Aprendizagem automática azure ocorre quando uma operação interna não pode ser concluída.  

**Resolução:** Este erro é causado por muitas condições e não existe uma solução específica.  
 A tabela seguinte contém mensagens genéricas para este erro, que são seguidas por uma descrição específica da circunstância. 

 Se não houver detalhes disponíveis, o [Microsoft Q&uma página](https://docs.microsoft.com/answers/topics/azure-machine-learning-studio-classic.html) de perguntas para enviar feedback e fornecer informações sobre os módulos que geraram o erro e as condições relacionadas.

|Mensagens de Exceção|
|------------------------|
|A operação falhou.|
|Erro ao completar o funcionamento: "{failed_operation}".|
|Erro ao completar o funcionamento: "{failed_operation}". Razão: "{reason}".|


## <a name="error-0042"></a>Erro 0042  
 A exceção ocorre quando não é possível converter coluna para outro tipo.  

 Este erro na Aprendizagem automática azure ocorre quando não é possível converter coluna para o tipo especificado.  Receberá este erro se um módulo necessitar de um determinado tipo de dados, como data, texto, número de ponto flutuante ou inteiro, mas não é possível converter uma coluna existente para o tipo necessário.  

Por exemplo, pode selecionar uma coluna e tentar convertê-la num tipo de dados numéricos para utilização numa operação matemática, e obter este erro se a coluna contiver dados inválidos. 

Outra razão para que possa obter este erro se tentar usar uma coluna contendo números de pontos flutuantes ou muitos valores únicos como coluna categórica. 

**Resolução:**

+ Abra a página de ajuda para o módulo que gerou o erro e verifique os requisitos do tipo de dados.
+ Reveja os tipos de dados das colunas no conjunto de dados de entrada.
+ Inspecione os dados originários das chamadas fontes de dados sem esquemas.
+ Verifique o conjunto de dados para valores em falta ou caracteres especiais que possam bloquear a conversão para o tipo de dados desejado. 
    + Os tipos de dados numéricos devem ser consistentes: por exemplo, verifique se há números de pontos flutuantes numa coluna de inteiros.
    + Procure cordas de texto ou valores DE NA numa coluna de números. 
    + Os valores booleanos podem ser convertidos para uma representação adequada, dependendo do tipo de dados necessário.
    + Examinar colunas de texto para caracteres não-unicodificais, caracteres de separadores ou caracteres de controlo
    + Os dados da data devem ser consistentes para evitar erros de modelação, mas a limpeza pode ser complexa devido aos muitos formatos. Considere usar <!--the [Execute R Script](execute-r-script.md) or -->[Execute](execute-python-script.md) os módulos do Script Python para efetuar a limpeza.  
+ Se necessário, modifique os valores no conjunto de dados de entrada para que a coluna possa ser convertida com sucesso. A modificação pode incluir operações de fixação, truncação ou arredondamento, eliminação de outliers ou imputação de valores em falta. Consulte os seguintes artigos para alguns cenários comuns de transformação de dados na aprendizagem automática:
    + [Limpar Dados em Falta](clean-missing-data.md)
    + [Normalizar Dados](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->

> [!TIP]
> Resolução pouco clara, ou não aplicável ao seu caso? É bem-vindo a enviar feedback sobre este artigo e a fornecer informações sobre o cenário, incluindo o módulo e o tipo de dados da coluna. Utilizaremos esta informação para fornecer passos mais detalhados de resolução de problemas no futuro.  

|Mensagens de Exceção|
|------------------------|
|Não é permitido a conversão.|
|Não podia converter a coluna do tipo {type1} para coluna do tipo {type2}.|
|Não consegui converter a coluna "{col_name1}" do tipo {type1} para coluna do tipo {type2}.|
|Não consegui converter a coluna "{col_name1}" do tipo {type1} para a coluna "{col_name2}" do tipo {type2}.|


## <a name="error-0044"></a>Erro 0044  
 A exceção ocorre quando não é possível derivar o tipo de coluna do elemento dos valores existentes.  

 Este erro na Aprendizagem automática azure ocorre quando não é possível inferir o tipo de coluna ou colunas num conjunto de dados. Isto normalmente acontece quando concatena dois ou mais conjuntos de dados com diferentes tipos de elementos. Se o Azure Machine Learning não conseguir determinar um tipo comum capaz de representar todos os valores numa coluna ou colunas sem perda de informação, irá gerar este erro.  

**Resolução:** Certifique-se de que todos os valores de uma determinada coluna em ambos os conjuntos de dados combinados são do mesmo tipo (numérico, booleano, categórico, string, data, etc.) ou podem ser coagidos ao mesmo tipo.  

|Mensagens de Exceção|
|------------------------|
|Não pode derivar o tipo de elemento da coluna.|
|Não é possível derivar o tipo de elemento para a coluna "{column_name}" -- todos os elementos são referências nulas.|
|Não é possível obter o tipo de elemento para a coluna "{column_name}" do conjunto de dados "{dataset_name}" -- todos os elementos são referências nulas.|


## <a name="error-0045"></a>Erro 0045  
 A exceção ocorre quando não é possível criar uma coluna devido a tipos de elementos mistos na fonte.  

 Este erro no Azure Machine Learning é produzido quando os tipos de elementos de dois conjuntos de dados combinados são diferentes.  

**Resolução:** Certifique-se de que todos os valores de uma dada coluna em ambos os conjuntos de dados combinados são do mesmo tipo (numérico, booleano, categórico, corda, data, etc.).  

|Mensagens de Exceção|
|------------------------|
|Não é possível criar coluna com tipos de elementos mistos.|
|Não é possível criar coluna com id "{column_id}" de tipos de elementos mistos:<br />Tipo de dados[{row_1}, {column_id}] é "{type_1}". <br />Tipo de dados[{row_2}, {column_id}] é "{type_2}".|
|Não é possível criar coluna com id "{column_id}" de tipos de elementos mistos:<br />Digite em pedaço {chunk_id_1} é "{type_1}". <br />Digite em pedaço {chunk_id_2} é "{type_2}" com tamanho de pedaço: {chunk_size}.|


## <a name="error-0046"></a>Erro 0046  
 A exceção ocorre quando não é possível criar diretório sinuoso no caminho especificado.  

 Este erro na Aprendizagem automática azure ocorre quando não é possível criar um diretório no caminho especificado. Receberá este erro se qualquer parte do caminho para o diretório de saída para uma Hive Query estiver incorreta ou inacessível.  

**Resolução:** Reveja o módulo e verifique se o percurso de diretório está corretamente formatado e que está acessível com as credenciais atuais.  

|Mensagens de Exceção|
|------------------------|
|Por favor, especifique um diretório de saída válido.|
|Diretório: não pode ser criado o {path}. Por favor, especifique o caminho válido.|


## <a name="error-0047"></a>Erro 0047  
 A exceção ocorre se o número de colunas de funcionalidades em alguns dos conjuntos de dados passados para o módulo for demasiado pequeno.  

 Este erro no Azure Machine Learning ocorre se o conjunto de dados de entrada para o treino não contiver o número mínimo de colunas exigida pelo algoritmo. Normalmente, ou o conjunto de dados está vazio ou contém apenas colunas de treino.  

**Resolução:** Reveja o conjunto de dados de entrada para se certificar de que existem uma ou mais colunas adicionais para além da coluna de etiquetas.  

|Mensagens de Exceção|
|------------------------|
|O número de colunas de funcionalidades no conjunto de dados de entrada é inferior ao mínimo permitido.|
|O número de colunas de características no conjunto de dados de entrada é inferior ao permitido mínimo de colunas {required_columns_count}|
|O número de colunas de características no conjunto de dados de entrada "{arg_name}" é inferior ao permitido mínimo de colunas {required_columns_count}|


## <a name="error-0048"></a>Erro 0048  
 A exceção ocorre no caso em que não é possível abrir um ficheiro.  

 Este erro no Azure Machine Learning ocorre quando não é possível abrir um ficheiro para leitura ou escrita. Pode receber este erro por estas razões:  

-   O recipiente ou o ficheiro (blob) não existe  
  
-   O nível de acesso do ficheiro ou do recipiente não lhe permite aceder ao ficheiro  
  
-   O ficheiro é muito grande para ler ou o formato errado  

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

 Este erro no Azure Machine Learning ocorre quando não é possível analisar um ficheiro. Receberá este erro se o formato de ficheiro selecionado no módulo [Dados de Importação](import-data.md) não corresponder ao formato real do ficheiro, ou se o ficheiro contiver um carácter irreconhecível.  

**Resolução:** Reveja o módulo e corrija a seleção do formato de ficheiro se não corresponder ao formato do ficheiro. Se possível, inspecione o ficheiro para confirmar que não contém caracteres ilegais.  

|Mensagens de Exceção|
|------------------------|
|Incapaz de analisar um ficheiro.|
|Erro ao analisar o ficheiro {file_format}.|
|Erro ao analisar o ficheiro {file_format} { file_name}.|
|Erro ao analisar o ficheiro {file_format}. Razão: {failure_reason}.|
|Erro ao analisar o ficheiro {file_format} { file_name}. Razão: {failure_reason}.|


## <a name="error-0052"></a>Erro 0052  
 A exceção ocorre se a chave da conta de armazenamento Azure for especificada incorretamente.  

 Este erro no Azure Machine Learning ocorre se a chave utilizada para aceder à conta de armazenamento Azure estiver incorreta. Por exemplo, pode ver este erro se a chave de armazenamento Azure foi truncada quando copiada e colada, ou se a chave errada foi utilizada.  

 Para obter mais informações sobre como obter a chave para uma conta de armazenamento Azure, consulte [Visualização, cópia e regenerar chaves de acesso](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/)ao armazenamento .  

**Resolução:** Reveja o módulo e verifique se a chave de armazenamento Azure está correta para a conta; copiar a chave novamente do portal clássico Azure, se necessário.  

|Mensagens de Exceção|
|------------------------|
|A chave da conta de armazenamento Azure está incorreta.|


## <a name="error-0053"></a>Erro 0053  
 A exceção ocorre no caso de não existirem funcionalidades ou itens de utilizador para recomendações de matchbox.  

 Este erro no Azure Machine Learning é produzido quando não é possível encontrar um vetor de características.  

**Resolução:** Certifique-se de que um vetor de características está presente no conjunto de dados de entrada.  

|Mensagens de Exceção|
|------------------------|
|As funcionalidades do utilizador ou/e os itens são necessários, mas não fornecidos.|


## <a name="error-0056"></a>Erro 0056  
 A exceção ocorre se as colunas selecionadas para uma operação violarem os requisitos.  

 Este erro no Azure Machine Learning ocorre quando está a escolher colunas para uma operação que requer que a coluna seja de um tipo de dados específico. 

 Este erro também pode acontecer se a coluna for o tipo de dados correto, mas o módulo que está a utilizar requer que a coluna também seja marcada como uma característica, etiqueta ou coluna categórica.  

  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->

**Resolução:**

1.  Reveja o tipo de dados das colunas atualmente selecionadas. 

2. Verifique se as colunas selecionadas são categóricas, etiquetas ou colunas de características.  
  
3.  Reveja o tópico de ajuda para o módulo em que fez a seleção da coluna, para determinar se existem requisitos específicos para o tipo de dados ou utilização da coluna.  
  
3.  Utilize [metadados editar](edit-metadata.md) para alterar o tipo de coluna durante a duração desta operação. Certifique-se de que altera o tipo de coluna de volta ao seu valor original, utilizando outra instância de [Editar Metadados,](edit-metadata.md)se precisar para operações a jusante.  

|Mensagens de Exceção|
|------------------------|
|Uma ou mais colunas selecionadas não estavam numa categoria permitida.|
|A coluna com o nome "{col_name}" não está numa categoria permitida.|


## <a name="error-0057"></a>Erro 0057  
 A exceção ocorre ao tentar criar um ficheiro ou uma bolha que já existe.  

 Esta exceção ocorre quando está a utilizar o módulo De dados de [exportação](export-data.md) ou outro módulo para guardar resultados de um pipeline em Azure Machine Learning para o armazenamento de blob Azure, mas tenta criar um ficheiro ou uma bolha que já existe.   

**Resolução:**

 Só receberá este erro se definir previamente o modo de escrita de **armazenamento de blob Azure** para **Error**. Por design, este módulo levanta um erro se tentar escrever um conjunto de dados para uma bolha que já existe.

 - Abra as propriedades do módulo e altere o modo de escrita de **armazenamento de blob Azure** para **sobreescrever**.
 - Em alternativa, pode digitar o nome de uma bolha ou ficheiro de destino diferente e não se esqueça de especificar uma bolha que ainda não existe.  

|Mensagens de Exceção|
|------------------------|
|Arquivo ou Blob já existe.|
|Arquivo ou Blob "{file_path}" já existe.|


## <a name="error-0058"></a>Erro 0058  
 Este erro no Azure Machine Learning ocorre se o conjunto de dados não contiver a coluna de etiquetas esperada.  

 Esta exceção também pode ocorrer quando a coluna de etiquetafornecida não corresponde aos dados ou tipos de dados esperados pelo aluno, ou tem os valores errados. Por exemplo, esta exceção é produzida quando se utiliza uma coluna de etiquetas de valor real ao treinar um classificador binário.  

**Resolução:** A resolução depende do aprendiz ou treinador que está a utilizar, e dos tipos de dados das colunas no seu conjunto de dados. Primeiro, verifique os requisitos do algoritmo de aprendizagem automática ou módulo de treino.  

 Reveja o conjunto de dados de entrada. Verifique se a coluna que espera ser tratada como a etiqueta tem o tipo de dados certo para o modelo que está a criar.  

 Verifique as inputs dos valores em falta e elimine-as ou substitua-as se necessário.  

 Se necessário, adicione o módulo [Editar Metadados](edit-metadata.md) e certifique-se de que a coluna de etiquetas está marcada como uma etiqueta.  

|Mensagens de Exceção|
|------------------------|
|Os valores da coluna de etiquetae os valores da coluna de etiquetas pontuados não são comparáveis.|
|A coluna de etiquetas não é como esperado em "{dataset_name}".|
|A coluna de etiquetas não é como esperado em "{dataset_name}", {reason}.|
|A coluna de etiquetas "{column_name}" não é esperada em "{dataset_name}".|
|A coluna de etiquetas "{column_name}" não é esperada em "{dataset_name}", {reason}.|


## <a name="error-0059"></a>Erro 0059  
 A exceção ocorre se um índice de coluna especificado num apanhador de colunas não puder ser analisado.  

 Este erro na Aprendizagem automática azure ocorre se um índice de coluna especificado ao utilizar o Seletor de Colunas não puder ser analisado.  Receberá este erro quando o índice de colunas estiver num formato inválido que não pode ser analisado.  

**Resolução:** Modifique o índice da coluna para utilizar um valor de índice válido.  

|Mensagens de Exceção|
|------------------------|
|Um ou mais índices de colunas especificados ou intervalos de índices não puderam ser analisados.|
|O índice de coluna ou o intervalo "{column_index_or_range}" não puderam ser analisados.|


## <a name="error-0060"></a>Erro 0060  
 A exceção ocorre quando um alcance fora de alcance é especificado num apanhador de colunas.  

 Este erro no Azure Machine Learning ocorre quando uma gama de colunas fora de alcance é especificada no Seletor de Colunas. Receberá este erro se o intervalo da coluna no apanhador de colunas não corresponder às colunas do conjunto de dados.  

**Resolução:** Modificar a gama de colunas no apanhador de colunas para corresponder às colunas do conjunto de dados.  

|Mensagens de Exceção|
|------------------------|
|Inválido ou fora do alcance do intervalo de índice de colunas especificado.|
|A gama de colunas "{column_range}" é inválida ou fora do alcance.|


## <a name="error-0061"></a>Erro 0061  
 A exceção ocorre ao tentar adicionar uma linha a uma DataTable que tem um número diferente de colunas do que a tabela.  

 Este erro no Azure Machine Learning ocorre quando se tenta adicionar uma linha a um conjunto de dados que tem um número diferente de colunas do que o conjunto de dados.  Receberá este erro se a linha que está a ser adicionada ao conjunto de dados tiver um número diferente de colunas a partir do conjunto de dados de entrada.  A linha não pode ser anexada ao conjunto de dados se o número de colunas for diferente.  

**Resolução:** Modificar o conjunto de dados de entrada para ter o mesmo número de colunas que a linha adicionada, ou modificar a linha adicionada para ter o mesmo número de colunas que o conjunto de dados.  

|Mensagens de Exceção|
|------------------------|
|Todas as mesas devem ter o mesmo número de colunas.|
|As colunas em pedaço "{chunk_id_1}" são diferentes com o pedaço "{chunk_id_2}" com o tamanho do pedaço: {chunk_size}.|
|A contagem da coluna no ficheiro "{filename_1}" (contagem={column_count_1}) é diferente com o ficheiro "{filename_2}" (contagem={column_count_2}).|


## <a name="error-0062"></a>Erro 0062  
 A exceção ocorre quando se tenta comparar dois modelos com diferentes tipos de alunos.  

 Este erro no Azure Machine Learning é produzido quando as métricas de avaliação de dois conjuntos de dados pontuados diferentes não podem ser comparadas. Neste caso, não é possível comparar a eficácia dos modelos utilizados para produzir os dois conjuntos de dados pontuados.  

**Resolução:** Verifique se os resultados pontuados são produzidos pelo mesmo tipo de modelo de aprendizagem automática (classificação binária, regressão, classificação multi-classe, recomendação, agrupamento, deteção de anomalias, etc.) Todos os modelos que comparar devem ter o mesmo tipo de aluno.  

|Mensagens de Exceção|
|------------------------|
|Todos os modelos devem ter o mesmo tipo de aprendiz.|
|Tem um tipo de aprendiz incompatível: "{atual_learner_type}". Os tipos de aprendizagem esperados são: "{expected_learner_type_list}".|


## <a name="error-0064"></a>Erro 0064  
 A exceção ocorre se o nome da conta de armazenamento azure ou a chave de armazenamento for especificada incorretamente.  

 Este erro no Azure Machine Learning ocorre se o nome ou a chave de armazenamento da conta de armazenamento Azure for especificada incorretamente. Receberá este erro se introduzir um nome de conta ou palavra-passe incorreto para a conta de armazenamento. Isto pode ocorrer se introduzir manualmente o nome da conta ou palavra-passe. Também pode ocorrer se a conta tiver sido eliminada.  

**Resolução:** Verifique se o nome da conta e a palavra-passe foram introduzidos corretamente e que a conta existe.  

|Mensagens de Exceção|
|------------------------|
|O nome da conta de armazenamento Azure ou a chave de armazenamento estão incorretos.|
|O nome da conta de armazenamento Azure "{account_name}" ou a chave de armazenamento para o nome da conta está incorreto.|


## <a name="error-0065"></a>Erro 0065  
 A exceção ocorre se o nome de blob Azure for especificado incorretamente.  

 Este erro na Aprendizagem automática azure ocorre se o nome blob Azure for especificado incorretamente.  Receberá o erro se:  

-   A bolha não pode ser encontrada no recipiente especificado.  

 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->

-   Apenas o recipiente foi especificado como fonte num pedido de [Dados de Importação](import-data.md) quando o formato era Excel ou CSV com codificação; a concatenação do conteúdo de todas as bolhas dentro de um recipiente não é permitida com estes formatos.  
  
-   Um SAS URI não contém o nome de uma bolha válida.  

**Resolução:** Reveja o módulo lançando a exceção. Verifique se a bolha especificada existe no recipiente na conta de armazenamento e que as permissões permitem-lhe ver a bolha. Verifique se a entrada é do **nome/nome** de ficheiro do formulário se tiver Excel ou CSV com formatos de codificação. Verifique se um SAS URI contém o nome de uma bolha válida.  

|Mensagens de Exceção|
|------------------------|
|O nome de bolha de armazenamento Azure está incorreto.|
|O nome de bolha de armazenamento Azure "{blob_name}" está incorreto.|
|O nome de bolha de armazenamento Azure com prefixo "{blob_name_prefix}" não existe.|
|Não encontrou nenhuma mancha de armazenamento Azure debaixo do recipiente "{container_name}".|
|Não conseguiu encontrar bolhas de armazenamento Azure com o caminho wildcard "{blob_wildcard_path}".|


## <a name="error-0066"></a>Erro 0066  
 A exceção ocorre se um recurso não puder ser enviado para um Blob Azure.  

 Este erro no Azure Machine Learning ocorre se um recurso não puder ser enviado para um Blob Azure.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Ambos são guardados na mesma conta de armazenamento Azure que a conta que contém o ficheiro de entrada.  

**Resolução:** Reveja o módulo. Verifique se o nome da conta Azure, a chave de armazenamento e o recipiente estão corretos e que a conta tem permissão para escrever ao recipiente.  

|Mensagens de Exceção|
|------------------------|
|O recurso não pôde ser enviado para o armazenamento do Azure.|
|O ficheiro "{source_path}" não pôde ser enviado para o armazenamento do Azure como "{dest_path}".|


## <a name="error-0067"></a>Erro 0067  
 A exceção ocorre se um conjunto de dados tiver um número diferente de colunas do que o esperado.  

 Este erro no Azure Machine Learning ocorre se um conjunto de dados tiver um número diferente de colunas do que o esperado.  Receberá este erro quando o número de colunas no conjunto de dados for diferente do número de colunas que o módulo espera durante a execução.  

**Resolução:** Modificar o conjunto de dados de entrada ou os parâmetros.  

|Mensagens de Exceção|
|------------------------|
|Número inesperado de colunas na datatable.|
|Número inesperado de colunas no conjunto de dados "{dataset_name}".|
|As colunas "{expected_column_count}", mas encontraram colunas "{atual_column_count}".|
|No conjunto de dados de entrada "{dataset_name}", esperava colunas "{expected_column_count}", mas encontrou colunas "{atual_column_count}".|


## <a name="error-0068"></a>Erro 0068  
 A exceção ocorre se o script da Colmeia especificado não estiver correto.  

 Este erro no Azure Machine Learning ocorre se houver erros de sintaxe num script QL da Hive, ou se o intérprete da Hive encontrar um erro durante a execução da consulta ou script.  

**Resolução:**

A mensagem de erro da Hive é normalmente reportada no Registo de Erros para que possa tomar medidas com base no erro específico. 

+ Abra o módulo e inspecione a consulta por erros.  
+ Verifique se a consulta funciona corretamente fora do Azure Machine Learning, iniciando sessão na consola da Colmeia do seu cluster Hadoop e executando a consulta.  
+ Tente colocar comentários no seu script da Hive numa linha separada em oposição à mistura de declarações e comentários executáveis numa única linha.  

### <a name="resources"></a>Recursos

Consulte os seguintes artigos para obter ajuda com consultas da Hive para aprendizagem automática:

+ [Crie tabelas de colmeias e carregue dados do Armazenamento De Blob Azure](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Explore dados em tabelas com consultas da Hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Criar características para dados num cluster do Hadoop com consultas do Hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Colmeia para Utilizadores SQL Cheat Sheet (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Mensagens de Exceção|
|------------------------|
|O guião da colmeia está incorreto.|


## <a name="error-0069"></a>Erro 0069  
 A exceção ocorre se o script SQL especificado não estiver correto.  

 Este erro no Azure Machine Learning ocorre se o script SQL especificado tiver problemas de sintaxe, ou se as colunas ou tabelas especificadas no script não forem válidas. 

 Receberá este erro se o motor SQL encontrar algum erro durante a execução da consulta ou script. A mensagem de erro SQL é normalmente reportada no Registo de Erro para que possa tomar medidas com base no erro específico.  

**Resolução:** Reveja o módulo e inspecione a consulta SQL por erros.  

 Verifique se a consulta funciona corretamente fora do Azure ML, iniciando sessão no servidor de base de dados diretamente e executando a consulta.  

 Se houver uma mensagem gerada por SQL reportada pela exceção do módulo, tome medidas com base no erro relatado. Por exemplo, as mensagens de erro incluem, por vezes, orientações específicas sobre o erro provável:
+ Nenhuma coluna ou base de *dados em falta,* indicando que pode ter dado um nome de coluna errado. Se tiver a certeza de que o nome da coluna está correto, tente utilizar parênteses ou aspas para encerrar o identificador da coluna.
+ *Erro lógico SQL perto \< Palavra-chave \> SQL*, indicando que pode ter um erro de sintaxe antes da palavra-chave especificada

  
|Mensagens de Exceção|
|------------------------|
|O script SQL está incorreto.|
|A consulta SQL "{sql_query}" não está correta.|
|A consulta SQL "{sql_query}" não está correta. Mensagem de exceção: {exceção}.|


## <a name="error-0070"></a>Erro 0070  
 A exceção ocorre quando se tenta aceder à tabela Azure inexistente.  

 Este erro no Azure Machine Learning ocorre quando se tenta aceder a uma tabela Azure inexistente. Receberá este erro se especificar uma tabela no armazenamento Azure, que não existe ao ler ou escrever para o Armazenamento de Mesa Azure. Isto pode acontecer se escrever mal o nome da tabela desejada, ou se tiver um desfasamento entre o nome alvo e o tipo de armazenamento. Por exemplo, pretendia ler de uma mesa, mas inseriu o nome de uma bolha.  

**Resolução:** Revisite o módulo para verificar se o nome da tabela está correto.  

|Mensagens de Exceção|
|------------------------|
|A mesa azul não existe.|
|A tabela azure "{table_name}" não existe.|


## <a name="error-0072"></a>Erro 0072  
 A exceção ocorre no caso de tempo de ligação.  

 Este erro na Aprendizagem automática azure ocorre quando uma ligação se estem. Receberá este erro se existem atualmente problemas de conectividade com a fonte de dados ou destino, como a conectividade lenta da Internet, ou se o conjunto de dados for grande e/ou a consulta SQL para ler nos dados executa um processamento complicado.  

**Resolução:** Determine se existem atualmente problemas com ligações lentas ao armazenamento Azure ou à internet.  

|Mensagens de Exceção|
|------------------------|
|O tempo de ligação ocorreu.|


## <a name="error-0073"></a>Erro 0073  
 A exceção ocorre se ocorrer um erro ao converter uma coluna para outro tipo.  

 Este erro na Aprendizagem automática azure ocorre quando não é possível converter coluna para outro tipo.  Receberá este erro se um módulo necessitar de um tipo específico e não for possível converter a coluna para o novo tipo.  

**Resolução:** Modifique o conjunto de dados de entrada para que a coluna possa ser convertida com base na exceção interna.  

|Mensagens de Exceção|
|------------------------|
|Falhou em converter a coluna.|
|Falhou em converter a coluna para {target_type}.|


## <a name="error-0075"></a>Erro 0075  
A exceção ocorre quando uma função de fixação inválida é usada ao quantificar um conjunto de dados.  

Este erro no Azure Machine Learning ocorre quando se está a tentar descomar dados utilizando um método não suportado, ou quando as combinações de parâmetros são inválidas.  

**Resolução:**

O manuseamento de erros para este evento foi introduzido numa versão anterior do Azure Machine Learning que permitiu uma maior personalização dos métodos de fixação. Atualmente, todos os métodos de fixação baseiam-se numa seleção de uma lista de abandono, pelo que, tecnicamente, não deverá continuar a ser possível obter este erro.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Microsoft Q&A question page for Azure Machine Learning](https://docs.microsoft.com/answers/topics/azure-machine-learning-studio-classic.html), providing the data types, parameter settings, and the exact error message.  -->

|Mensagens de Exceção|
|------------------------|
|Função de fixação inválida utilizada.|


## <a name="error-0077"></a>Erro 0077  
 A exceção ocorre quando o ficheiro blob desconhecido escreve o modo passado.  

 Este erro no Azure Machine Learning ocorre se um argumento inválido for aprovado nas especificações para um destino ou fonte de ficheiros blob.  

**Resolução:** Em quase todos os módulos que importam ou exportam dados de e para o armazenamento de blob azure, os valores dos parâmetros que controlam o modo de escrita são atribuídos através de uma lista de abandono; portanto, não é possível passar um valor inválido, e este erro não deve aparecer. Este erro será depreciado numa versão posterior.  

|Mensagens de Exceção|
|------------------------|
|Modo de escrita blob não suportado.|
|Modo de escrita blob não suportado: {blob_write_mode}.|


## <a name="error-0078"></a>Erro 0078  
 A exceção ocorre quando a opção HTTP para [Dados de Importação](import-data.md) recebe um código de estado 3xx indicando reorientação.  

 Este erro no Azure Machine Learning ocorre quando a opção HTTP para [Dados de Importação](import-data.md) recebe um código de estado 3xx (301, 302, 304, etc.) indicando reorientação. Receberá este erro se tentar ligar-se a uma fonte http que redireciona o navegador para outra página. Por razões de segurança, o redirecionamento de websites não é permitido como fonte de dados para o Azure Machine Learning.  

**Resolução:** Se o site for um site de confiança, introduza o URL redirecionado diretamente.  

|Mensagens de Exceção|
|------------------------|
|Http redirecionamento não é permitido.|


## <a name="error-0079"></a>Erro 0079  
 A exceção ocorre se o nome do contentor de armazenamento Azure for especificado incorretamente.  

 Este erro na Aprendizagem automática azure ocorre se o nome do recipiente de armazenamento Azure for especificado incorretamente. Receberá este erro se não tiver especificado o nome do recipiente e do blob (ficheiro) utilizando **o Caminho para a bolha a partir da** opção do recipiente ao escrever para o Armazenamento De Blob Azure.  

**Resolução:** Reveja o módulo de Dados de [Exportação](export-data.md) e verifique se o caminho especificado para a bolha contém tanto o recipiente como o nome de ficheiro, no **recipiente/nome**de ficheiro de formato .  

|Mensagens de Exceção|
|------------------------|
|O nome do recipiente de armazenamento Azure está incorreto.|
|O nome do recipiente de armazenamento Azure "{container_name}" está incorreto; Esperava-se um nome de contentor do recipiente/bolha de formato.|


## <a name="error-0080"></a>Erro 0080  
 A exceção ocorre quando a coluna com todos os valores em falta não é permitida pelo módulo.  

 Este erro no Azure Machine Learning é produzido quando uma ou mais colunas consumidas pelo módulo contêm todos os valores em falta. Por exemplo, se um módulo estiver a calcular estatísticas agregadas para cada coluna, não pode funcionar numa coluna que não contenha dados. Nesses casos, a execução do módulo é interrompida com esta exceção.  

**Resolução:** Reveja o conjunto de dados de entrada e remova quaisquer colunas que contenham todos os valores em falta.  

|Mensagens de Exceção|
|------------------------|
|Não são permitidas colunas com todos os valores em falta.|
|Coluna {col_index_or_name} tem todos os valores em falta.|


## <a name="error-0081"></a>Erro 0081  
 A exceção ocorre no módulo PCA se o número de dimensões a reduzir é igual ao número de colunas de características no conjunto de dados de entrada, contendo pelo menos uma coluna de características escassa.  

 Este erro no Azure Machine Learning é produzido se forem satisfeitas as seguintes condições: a O conjunto de dados de entrada tem pelo menos uma coluna escassa e (b) o número final de dimensões solicitadas é o mesmo que o número de dimensões de entrada.  

**Resolução:** Considere a redução do número de dimensões na saída para ser menor do que o número de dimensões na entrada. Isto é típico em aplicações de PCA.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->

|Mensagens de Exceção|
|------------------------|
|Para o conjunto de dados que contém colunas escassas de características, o número de dimensões a reduzir deve ser inferior a um número de colunas de características.|


## <a name="error-0082"></a>Erro 0082  
 A exceção ocorre quando um modelo não pode ser desserializado com sucesso.  

 Este erro no Azure Machine Learning ocorre quando um modelo de aprendizagem automática ou transformação de máquinas guardados não pode ser carregado por uma versão mais recente do tempo de execução do Azure Machine Learning como resultado de uma mudança de rutura.  

**Resolução:** O gasoduto de formação que produziu o modelo ou a transformação deve ser reexecutado e o modelo ou a transformação devem ser resalvos.  

|Mensagens de Exceção|
|------------------------|
|O modelo não podia ser desserializado porque é provavelmente serializado com um formato de serialização mais antigo. Por favor, retreine e salve o modelo.|


## <a name="error-0083"></a>Erro 0083  
 A exceção ocorre se o conjunto de dados utilizado para o treino não puder ser utilizado para um tipo de aprendizagem concreto.  

 Este erro no Azure Machine Learning é produzido quando o conjunto de dados é incompatível com o aprendiz que está a ser treinado. Por exemplo, o conjunto de dados pode conter pelo menos um valor em falta em cada linha, e como resultado, todo o conjunto de dados seria ignorado durante o treino. Noutros casos, alguns algoritmos de aprendizagem automática, como a deteção de anomalias, não esperam que os rótulos estejam presentes e podem lançar esta exceção se os rótulos estiverem presentes no conjunto de dados.  

**Resolução:** Consulte a documentação do aluno que está a ser utilizado para verificar os requisitos para o conjunto de dados de entrada. Examine as colunas para ver que todas as colunas necessárias estão presentes.  

|Mensagens de Exceção|
|------------------------|
|O conjunto de dados utilizado para o treino é inválido.|
|{data_name} contém dados inválidos para treino.|
|{data_name} contém dados inválidos para treino. Tipo de aprendizagem: {learner_type}.|
|{data_name} contém dados inválidos para treino. Tipo de aprendizagem: {learner_type}. Razão: {reason}.|
|Falhou em aplicar a ação "{action_name}" nos dados de treino {data_name}. Razão: {reason}.|


## <a name="error-0084"></a>Erro 0084  
 A exceção ocorre quando as pontuações produzidas a partir de um Script R são avaliadas. Isto não é suportado atualmente.  

 Este erro no Azure Machine Learning ocorre se tentar utilizar um dos módulos para avaliar um modelo com saída a partir de um script R que contenha pontuações.  

**Resolução:**

|Mensagens de Exceção|
|------------------------|
|A avaliação das pontuações produzidas pelo Custom Model não é suportada atualmente.|


## <a name="error-0085"></a>Erro 0085  
 A exceção ocorre quando a avaliação do script falha com um erro.  

 Este erro no Azure Machine Learning ocorre quando está a executar scriptpersonalizado que contém erros de sintaxe.  

**Resolução:** Reveja o seu código num editor externo e verifique se há erros.  

|Mensagens de Exceção|
|------------------------|
|Erro durante a avaliação do script.|
|O seguinte erro ocorreu durante a avaliação do script, por favor, consulte o registo de saída para obter mais informações:<br />---------- Início da mensagem de erro do ---------- script_language} script_language<br />{mensagem}<br />---------- Mensagem final de erro do ---------- intérprete {script_language}|


## <a name="error-0090"></a>Erro 0090  
 A exceção ocorre quando a criação da mesa da Colmeia falha.  

 Este erro no Azure Machine Learning ocorre quando está a utilizar dados de [exportação](export-data.md) ou outra opção para guardar dados para um cluster HDInsight e a tabela hive especificada não pode ser criada.  

**Resolução:** Verifique o nome da conta de armazenamento Azure associado ao cluster e verifique se está a utilizar a mesma conta nas propriedades do módulo.  

|Mensagens de Exceção|
|------------------------|
|A mesa da Colmeia não pôde ser criada. Para um cluster HDInsight, certifique-se de que o nome da conta de armazenamento Azure associado ao cluster é o mesmo que é passado através do parâmetro do módulo.|
|A mesa da Colmeia "{table_name}" não pôde ser criada. Para um cluster HDInsight, certifique-se de que o nome da conta de armazenamento Azure associado ao cluster é o mesmo que é passado através do parâmetro do módulo.|
|A mesa da Colmeia "{table_name}" não pôde ser criada. Para um cluster HDInsight, certifique-se de que o nome da conta de armazenamento Azure associado ao cluster é "{cluster_name}".|


## <a name="error-0102"></a>Erro 0102  
 Lançado quando um ficheiro ZIP não pode ser extraído.  

 Este erro no Azure Machine Learning ocorre quando está a importar um pacote com fecho com a extensão .zip, mas o pacote ou não é um ficheiro zip, ou o ficheiro não utiliza um formato zip suportado.  

**Resolução:** Certifique-se de que o ficheiro selecionado é um ficheiro .zip válido e que foi comprimido utilizando um dos algoritmos de compressão suportados.  

 Se tiver este erro ao importar conjuntos de dados em formato comprimido, verifique se todos os ficheiros contidos utilizam um dos formatos de ficheiros suportados e estão no formato Unicode.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->

 Tente ler os ficheiros desejados numa nova pasta comfecho comprimido e tente adicionar novamente o módulo personalizado.  

|Mensagens de Exceção|
|------------------------|
|Dado que o ficheiro ZIP não está no formato correto.|


## <a name="error-0105"></a>Erro 0105  
 Este erro é apresentado quando um ficheiro de definição de módulo contém um tipo de parâmetro não suportado  
  
 Este erro no Azure Machine Learning é produzido quando se cria uma definição de módulo xml personalizada e o tipo de parâmetro ou argumento na definição não corresponde a um tipo suportado.  
  
**Resolução:** Certifique-se de que a propriedade do tipo de qualquer elemento **Arg** no ficheiro de definição xml do módulo personalizado é um tipo suportado.  
  
|Mensagens de Exceção|  
|------------------------|  
|Tipo de parâmetro não suportado.|  
|Tipo de parâmetro não {0} suportado ' especificado.|  


## <a name="error-0107"></a>Erro 0107  
 Lançado quando um ficheiro de definição de módulo define um tipo de saída não suportado  
  
 Este erro no Azure Machine Learning é produzido quando o tipo de porta de saída num módulo personalizado de definição xml não corresponde a um tipo suportado.  
  
**Resolução:** Certifique-se de que a propriedade do tipo de um elemento De saída no ficheiro de definição xml do módulo personalizado é um tipo suportado.  
  
|Mensagens de Exceção|  
|------------------------|  
|Tipo de saída não suportado.|  
|Tipo de saída não suportado '{output_type}' especificado.|  


## <a name="error-0125"></a>Erro 0125  
 Lançado quando o esquema para vários conjuntos de dados não corresponde.  

**Resolução:**

|Mensagens de Exceção|
|------------------------|
|O esquema de conjunto de dados não corresponde.|


## <a name="error-0127"></a>Erro 0127  
 Tamanho do pixel de imagem excede limite permitido  

 Este erro ocorre se estiver a ler imagens de um conjunto de dados de imagem para classificação e as imagens forem maiores do que o modelo consegue lidar.  

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
|O número de probabilidades condicionais para colunas categóricas excede o limite. As colunas '{column_name_or_index_1}' e '{column_name_or_index_2}' são o par problemático.|


## <a name="error-0129"></a>Erro 0129  
 O número de colunas no conjunto de dados excede o limite permitido.  

**Resolução:**

|Mensagens de Exceção|
|------------------------|
|O número de colunas no conjunto de dados excede o limite permitido.|
|O número de colunas no conjunto de dados em '{dataset_name}' excede.|
|O número de colunas no conjunto de dados em '{dataset_name}' excede o limite permitido de '{component_name}'.|
|O número de colunas no conjunto de dados em '{dataset_name}' excede o limite permitido por '{limit_columns_count}' de '{component_name}'.|


## <a name="error-0134"></a>Erro 0134
A exceção ocorre quando a coluna do rótulo está em falta ou tem um número insuficiente de linhas etiquetadas.  

Este erro ocorre quando o módulo requer uma coluna de etiqueta, mas não incluiu uma na seleção da coluna, ou a coluna de etiquetas está a perder demasiados valores.

Este erro também pode ocorrer quando uma operação anterior altera o conjunto de dados de modo a que as linhas insuficientes estejam disponíveis para uma operação a jusante. Por exemplo, suponha que utilize uma expressão no módulo **De Partilha e Amostra** para dividir um conjunto de dados por valores. Se não forem encontrados fósforos para a sua expressão, um dos conjuntos de dados resultantes da partição estaria vazio.

Resolução: 

 Se incluir uma coluna de etiquetas na seleção da coluna, mas não for reconhecida, utilize o módulo [Dedados editar](edit-metadata.md) para a marcar como uma coluna de etiquetas.

  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->
  Em seguida, pode utilizar o módulo [Clean Missing Data](clean-missing-data.md) para remover linhas com valores em falta na coluna de etiquetas. 

 Verifique os conjuntos de dados de entrada para se certificar de que contêm dados válidos e linhas suficientes para satisfazer os requisitos da operação. Muitos algoritmos gerarão uma mensagem de erro se necessitarem de algumas filas mínimas de dados, mas os dados contêm apenas algumas linhas, ou apenas um cabeçalho.

|Mensagens de Exceção|
|------------------------|
|A exceção ocorre quando a coluna do rótulo está em falta ou tem um número insuficiente de linhas etiquetadas.|
|A exceção ocorre quando falta a coluna de etiquetas ou tem menos de {required_rows_count} linhas rotuladas.|
|A exceção ocorre quando a coluna de etiquetas no conjunto de dados {dataset_name} está em falta ou tem linhas com rótulo {required_rows_count}.|


## <a name="error-0138"></a>Erro 0138  
 A memória foi esgotada, incapaz de completar o funcionamento do módulo. A redução da amostragem do conjunto de dados pode ajudar a aliviar o problema.  

 Este erro ocorre quando o módulo que está em funcionamento requer mais memória do que o disponível no recipiente Azure. Isto pode acontecer se estiver a trabalhar com um grande conjunto de dados e a operação atual não encaixar na memória.  

**Resolução:** Se estiver a tentar ler um conjunto de dados grande e a operação não puder ser concluída, a redução da amostragem do conjunto de dados pode ajudar.  

  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  

 Sometimes transient loads can lead to this error. Machine support also changes over time. 

 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->

|Mensagens de Exceção|
|------------------------|
|A memória foi esgotada, incapaz de completar o funcionamento do módulo.|
|A memória foi esgotada, incapaz de completar o funcionamento do módulo. Detalhes: {detalhes}|


## <a name="error-0141"></a>Erro 0141  
 A exceção ocorre se o número das colunas numéricas selecionadas e valores únicos nas colunas categóricas e de cordas for demasiado pequeno.  

 Este erro no Azure Machine Learning ocorre quando não existem valores únicos suficientes na coluna selecionada para executar a operação.  

**Resolução:** Algumas operações realizam operações estatísticas em colunas de recurso e categóricas, e se não houver valores suficientes, a operação pode falhar ou devolver um resultado inválido. Verifique o seu conjunto de dados para ver quantos valores existem nas colunas de funcionalidade e etiqueta, e determine se a operação que está a tentar executar é estatisticamente válida.  

 Se o conjunto de dados de origem for válido, também poderá verificar se alguma manipulação a montante de dados ou operação de metadados alterou os dados e removeu alguns valores.  

 Se as operações a montante incluira divisão, amostragem ou reprovação, verifique se as saídas contêm o número esperado de linhas e valores.  

|Mensagens de Exceção|
|------------------------|
|O número das colunas numéricas selecionadas e valores únicos nas colunas categóricas e de cordas é demasiado pequeno.|
|O número total das colunas numéricas selecionadas e valores únicos nas colunas categóricas e de cordas (atualmente {atual_num}) deve ser pelo menos {lower_boundary}.|


## <a name="error-0154"></a>Erro 0154  
 A exceção ocorre quando o utilizador tenta juntar dados em colunas-chave com tipo de coluna incompatível.

|Mensagens de Exceção|
|------------------------|
|Os tipos de elementos da coluna de teclas não são compatíveis.|
|Os tipos de elementos da coluna de teclas não são compatíveis. (esquerda: {keys_left}; direita: {keys_right})|


## <a name="error-0155"></a>Erro 0155  
 A exceção ocorre quando os nomes de colunas do conjunto de dados não são cordas.

|Mensagens de Exceção|
|------------------------|
|O nome da coluna do quadro de dados deve ser do tipo de corda. Os nomes das colunas não são cordas.|
|O nome da coluna do quadro de dados deve ser do tipo de corda. Os nomes das colunas {column_names} não são cordas.|


## <a name="error-0156"></a>Erro 0156  
 A exceção ocorre quando não conseguiu ler os dados da Base de Dados Azure SQL.

|Mensagens de Exceção|
|------------------------|
|Não conseguiu ler os dados da Base de Dados Azure SQL.|
|Não conseguiler os dados da Base de Dados Azure SQL: {detailed_message} DB: {database_server_name}:{database_name} Consulta: {sql_statement}|


## <a name="error-0157"></a>Erro 0157  
 Loja de dados não encontrada.

|Mensagens de Exceção|
|------------------------|
|A informação da datastore é inválida.|
|A informação da datastore é inválida. Não conseguiu que a loja de dados AzureML '{datastore_name}' no espaço de trabalho '{workspace_name}'.|


## <a name="error-0158"></a>Erro 0158
 Jogado quando um diretório de transformação é inválido.

|Mensagens de Exceção|
|------------------------------------------------------------|
|Dado que o TransformationDirectory é inválido.|
|O Directory de Transformação "{arg_name}" é inválido. Razão: {reason}. Por favor, refaça a experiência de treino que gera o ficheiro Transform. Se a experiência de treino foi eliminada, recrie e guarde o ficheiro Transform.|
|O Directory de Transformação "{arg_name}" é inválido. Razão: {reason}. {troubleshoot_hint}|


## <a name="error-0159"></a>Erro 0159
 A exceção ocorre se a lista de modelos de módulos for inválida. 

|Mensagens de Exceção|
|------------------------------------------------------------|
|Dado que o ModelDirectory é inválido.|
|ModelDirectory "{arg_name}" é inválido.|
|ModelDirectory "{arg_name}" é inválido. Razão: {reason}.|
|ModelDirectory "{arg_name}" é inválido. Razão: {reason}. {troubleshoot_hint}|


## <a name="error-1000"></a>Erro 1000  
Exceção da biblioteca interna.  

Este erro é fornecido para capturar erros internos do motor de outra forma não manipulados. Portanto, a causa deste erro pode ser diferente dependendo do módulo que gerou o erro.  

Para obter mais ajuda, recomendamos que publique a mensagem detalhada que acompanha o erro no fórum Azure Machine Learning, juntamente com uma descrição do cenário, incluindo os dados utilizados como inputs. Este feedback ajudar-nos-á a dar prioridade aos erros e a identificar as questões mais importantes para mais trabalho.  

|Mensagens de Exceção|
|------------------------|
|Exceção da biblioteca.|
|Exceção da biblioteca: {exceção}.|
|Exceção da biblioteca desconhecida: {exceção}. {customer_support_guidance}.|

