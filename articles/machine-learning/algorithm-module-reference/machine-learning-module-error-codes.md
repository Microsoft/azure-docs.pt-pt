---
title: Solucionar erros de módulo
titleSuffix: Azure Machine Learning service
description: Solucionar problemas de exceções de módulo no Azure Machine Learning Studio usando códigos de erro
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: dc383d302fb3e9920ee8ef2d7d908a5b406ea1da
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128669"
---
# <a name="exceptions-and-error-codes-for-algorithm--module-reference"></a>Exceções e códigos de erro para o algoritmo & referência de módulo

Saiba mais sobre as mensagens de erro e os códigos de exceção que você pode encontrar usando módulos no Azure Machine Learning Studio. 

Para resolver o problema, procure o erro neste artigo para ler sobre as causas comuns. Há duas maneiras de obter o texto completo de uma mensagem de erro no estúdio:  
 
- Clique no link, **exiba o log de saída**, no painel direito e role até a parte inferior. A mensagem de erro detalhada é exibida nas duas últimas linhas da janela.  
  
- Selecione o módulo que tem o erro e clique no X vermelho. Somente o texto de erro pertinente é exibido.  
  
Se o texto da mensagem de erro não for útil, envie-nos informações sobre o contexto e sobre as adições ou alterações desejadas. Você pode enviar comentários sobre o tópico de erro ou visitar o [Fórum do Azure Machine Learning Studio](https://aka.ms/aml-forum-studio) e postar uma pergunta.  


## <a name="error-0001"></a>Erro 0001  
 Ocorrerá uma exceção se uma ou mais colunas especificadas do conjunto de dados não puder ser encontrada.  
  
 Você receberá esse erro se uma seleção de coluna for feita para um módulo, mas as colunas selecionadas não existirem no conjunto de dados de entrada. Esse erro pode ocorrer se você tiver digitado manualmente um nome de coluna ou se o seletor de coluna tiver fornecido uma coluna sugerida que não existia no conjunto de seus quando você executou o experimento.  
  
**Resolução:** Revisite o módulo lançando essa exceção e valide se o nome ou os nomes da coluna estão corretos e se todas as colunas referenciadas existem.  
  
|Mensagens de exceção|  
|------------------------|  
|Uma ou mais colunas especificadas não foram encontradas|  
|Coluna com nome ou índice "{0}" não encontrada|  
|A coluna com o nome ou{0}o índice "" não existe{1}em ""|  
 

## <a name="error-0002"></a>Erro 0002  
 Ocorrerá uma exceção se um ou mais parâmetros não puderem ser analisados ou convertidos do tipo especificado em exigido pelo tipo de método de destino.  
  
 Esse erro ocorre em Azure Machine Learning quando você especifica um parâmetro como entrada e o tipo de valor é diferente do tipo esperado e a conversão implícita não pode ser executada.  
  
**Resolução:** Verifique os requisitos do módulo e determine qual tipo de valor é necessário (cadeia de caracteres, inteiro, duplo, etc.)  
  
|Mensagens de exceção|  
|------------------------|  
|Falha ao analisar o parâmetro|  
|Falha ao analisar o{0}parâmetro ""|  
|Falha ao analisar o parâmetro (Convert{0}) "" para{1}""|  
|Falha ao converter o{0}parâmetro "" de{1}"" em{2}""|  
|Falha ao converter "{0}" valor do parâmetro{1}"" de{2}"" em{3}""|  
|Falha ao converter o valor{0}"" na coluna{1}""{3}de{2}"" em "" com uso do formato "{4}" fornecido|  
  

## <a name="error-0003"></a>Erro 0003  
 Ocorrerá uma exceção se uma ou mais das entradas forem nulas ou estiverem vazias.  
  
 Você receberá esse erro em Azure Machine Learning se quaisquer entradas ou parâmetros para um módulo forem nulos ou vazios.  Esse erro pode ocorrer, por exemplo, quando você não digitou nenhum valor para um parâmetro. Isso também pode acontecer se você escolher um conjunto de um DataSet que tem valores ausentes ou um conjunto de um DataSet vazio.  
  
**Resolução:**
 
+ Abra o módulo que produziu a exceção e verifique se todas as entradas foram especificadas. Certifique-se de que todas as entradas necessárias sejam especificadas. 
+ Verifique se os dados carregados do armazenamento do Azure estão acessíveis e se o nome ou a chave da conta não foi alterado.  
+ Verifique os dados de entrada quanto a valores ausentes ou nulos.
+ Se estiver usando uma consulta em uma fonte de dados, verifique se os dados estão sendo retornados no formato esperado. 
+ Verifique se há erros de digitação ou outras alterações na especificação dos dados.
  
|Mensagens de exceção|  
|------------------------|  
|Uma ou mais entradas são nulas ou estão vazias|  
|A entrada{0}"" é nula ou está vazia|  
  

## <a name="error-0004"></a>Erro 0004  
 Ocorrerá uma exceção se o parâmetro for menor ou igual ao valor específico.  
  
 Você receberá esse erro em Azure Machine Learning se o parâmetro na mensagem estiver abaixo de um valor de limite necessário para o módulo processar os dados.  
  
**Resolução:** Revisite o módulo lançando a exceção e modifique o parâmetro para que seja maior que o valor especificado.  
  
|Mensagens de exceção|  
|------------------------|  
|O parâmetro deve ser maior que o valor de limite.|  
|O valor{0}do parâmetro "" deve ser {1}maior que.|  
|O parâmetro{0}"" tem o{1}valor "", que deve ser maior que{2}|  
  


## <a name="error-0005"></a>Erro 0005  
 Ocorrerá uma exceção se o parâmetro for menor que um valor específico.  
  
 Você receberá esse erro em Azure Machine Learning se o parâmetro na mensagem estiver abaixo ou for igual a um valor de limite necessário para o módulo processar os dados.  
  
**Resolução:** Revisite o módulo lançando a exceção e modifique o parâmetro para que seja maior ou igual ao valor especificado.  
  
|Mensagens de exceção|  
|------------------------|  
|O parâmetro deve ser maior ou igual ao valor de limite.|  
|O valor{0}do parâmetro "" deve ser maior ou igual {1}a.|  
|O parâmetro{0}"" tem o{1}valor "", que deve ser maior ou {2}igual a.|  
  

## <a name="error-0006"></a>Erro 0006  
 Ocorrerá uma exceção se o parâmetro for maior ou igual ao valor especificado.  
  
 Você receberá esse erro em Azure Machine Learning se o parâmetro na mensagem for maior ou igual ao valor de limite necessário para o módulo processar os dados.  
  
**Resolução:** Revisite o módulo lançando a exceção e modifique o parâmetro para ser menor que o valor especificado.  
  
|Mensagens de exceção|  
|------------------------|  
|Parâmetros incompatíveis. Um dos parâmetros deve ser menor que outro.|  
|O valor{0}do parâmetro "" deve ser menor que{1}o valor do parâmetro "".|  
|O parâmetro{0}"" tem o{1}valor "", que deve {2}ser menor que.|  
  

## <a name="error-0007"></a>Erro 0007  
 Ocorrerá uma exceção se o parâmetro for maior que um valor específico.  
  
 Você receberá esse erro em Azure Machine Learning se, nas propriedades do módulo, você tiver especificado um valor maior do que o permitido. Por exemplo, você pode especificar um dado que está fora do intervalo de datas com suporte ou pode indicar que cinco colunas serão usadas quando apenas três colunas estiverem disponíveis. 
 
 Você também poderá ver esse erro se estiver especificando dois conjuntos de dados que precisam corresponder de alguma maneira. Por exemplo, se você estiver renomeando colunas e especificar as colunas por índice, o número de nomes que você fornecer deve corresponder ao número de índices de coluna. Outro exemplo pode ser uma operação matemática que usa duas colunas, em que as colunas devem ter o mesmo número de linhas. 
  
**Resolução:**
 
 + Abra o módulo em questão e revise as configurações de propriedade numérica.
 + Verifique se os valores de parâmetro estão dentro do intervalo de valores com suporte para essa propriedade.
 + Se o módulo usar várias entradas, certifique-se de que as entradas tenham o mesmo tamanho.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Verifique se o DataSet ou a fonte de dados foi alterado. Às vezes, um valor que funcionava com uma versão anterior dos dados falhará depois que o número de colunas, os tipos de dados da coluna ou o tamanho dos dados forem alterados.  
  
|Mensagens de exceção|  
|------------------------|  
|Parâmetros incompatíveis. Um dos parâmetros deve ser menor ou igual a outro.|  
|O valor{0}do parâmetro "" deve ser menor ou igual ao valor{1}do parâmetro "".|  
|O parâmetro{0}"" tem o{1}valor "", que deve ser menor ou {2}igual a.|  
  

## <a name="error-0008"></a>Erro 0008  
 Ocorrerá uma exceção se o parâmetro não estiver no intervalo.  
  
 Você receberá esse erro em Azure Machine Learning se o parâmetro na mensagem estiver fora dos limites necessários para o módulo processar os dados.  
  
 Por exemplo, esse erro será exibido se você tentar usar [adicionar linhas](add-rows.md) para combinar dois conjuntos de valores que têm um número diferente de colunas.  
  
**Resolução:** Revisite o módulo lançando a exceção e modifique o parâmetro para estar dentro do intervalo especificado.  
  
|Mensagens de exceção|  
|------------------------|  
|O valor do parâmetro não está no intervalo especificado.|  
|O valor{0}do parâmetro "" não está no intervalo.|  
|O valor{0}do parâmetro "" deve estar no intervalo de{1}[ {2},].|  
  

## <a name="error-0009"></a>Erro 0009  
 Ocorrerá uma exceção quando o nome da conta de armazenamento do Azure ou o nome do contêiner for especificado incorretamente.  
  
Esse erro ocorre em Azure Machine Learning Studio quando você especifica parâmetros para uma conta de armazenamento do Azure, mas o nome ou a senha não podem ser resolvidos. Erros em senhas ou nomes de conta podem ocorrer por vários motivos:
 
 + A conta é do tipo incorreto. Não há suporte para alguns novos tipos de conta para uso com Machine Learning Studio. Consulte [importar dados](import-data.md) para obter detalhes.
 + Você inseriu o nome da conta incorreta
 + A conta não existe mais
 + A senha da conta de armazenamento está incorreta ou foi alterada
 + Você não especificou o nome do contêiner ou o contêiner não existe
 + Você não especificou totalmente o caminho do arquivo (caminho para o blob)
   
**Resolução:**

Esses problemas geralmente ocorrem quando você tenta inserir manualmente o nome da conta, a senha ou o caminho do contêiner. Recomendamos que você use o novo assistente para o módulo [importar dados](import-data.md) , que ajuda você a Pesquisar e verificar nomes.

Verifique também se a conta, o contêiner ou o blob foram excluídos. Use outro utilitário de armazenamento do Azure para verificar se o nome da conta e a senha foram inseridos corretamente e se o contêiner existe. 

Não há suporte para alguns tipos de conta mais novos pelo Azure Machine Learning. Por exemplo, os novos tipos de armazenamento "quente" ou "frio" não podem ser usados para o aprendizado de máquina. As contas de armazenamento clássicas e as contas de armazenamento criadas como "finalidade geral" funcionam bem.

Se o caminho completo para um blob tiver sido especificado, verifique se o caminho está especificado como **container/blobname**e se o contêiner e o blob existem na conta.  
  
 O caminho não deve conter uma barra à esquerda. Por exemplo, **/container/blob** está incorreto e deve ser inserido como **contêiner/blob**.  

  
|Mensagens de exceção|  
|------------------------|  
|O nome da conta de armazenamento do Azure ou o nome do contêiner está incorreto.|  
|O nome da conta de armazenamento{0}do Azure "" ou{1}o nome do contêiner "" está incorreto; um nome de contêiner do formato contêiner/blob era esperado.|  
  

## <a name="error-0010"></a>Erro 0010  
 Ocorrerá uma exceção se os conjuntos de dados de entrada tiverem nomes de coluna que devem corresponder, mas não.  
  
 Você receberá esse erro em Azure Machine Learning se o índice da coluna na mensagem tiver nomes de coluna diferentes nos dois conjuntos de dados de entrada.  
  
**Resolução:** Use [Editar metadados](edit-metadata.md) ou modificar o conjunto de um original para ter o mesmo nome de coluna para o índice de coluna especificado.  
  
|Mensagens de exceção|  
|------------------------|  
|Colunas com índice correspondente em conjuntos de dados de entrada têm nomes diferentes.|  
|Os nomes de coluna não são os mesmos {0} para a coluna (com base em zero) dos{1} conjuntos {2} de dados de entrada (e, respectivamente).|  
  

## <a name="error-0011"></a>Erro 0011  
 Ocorrerá uma exceção se o argumento de conjunto de colunas aprovado não se aplicar a nenhuma coluna de conjunto de linhas.  
  
 Você receberá esse erro em Azure Machine Learning se a seleção de coluna especificada não corresponder a nenhuma das colunas no conjunto de dados fornecido.  
  
 Você também poderá obter esse erro se não tiver selecionado uma coluna e pelo menos uma coluna for necessária para que o módulo funcione.  
  
**Resolução:** Modifique a seleção de coluna no módulo para que ela seja aplicada às colunas no conjunto de linhas.  
  
 Se o módulo exigir que você selecione uma coluna específica, como uma coluna de rótulo, verifique se a coluna à direita está selecionada.  
  
 Se forem selecionadas colunas inadequadas, remova-as e execute o experimento novamente.  
  
|Mensagens de exceção|  
|------------------------|  
|O conjunto de colunas especificado não se aplica a nenhuma coluna de conjunto de linhas.|  
|O conjunto de colunas{0}especificado "" não se aplica a nenhuma coluna de conjunto de linhas.|  
  

## <a name="error-0012"></a>Erro 0012  
 Ocorrerá uma exceção se a instância da classe não puder ser criada com um conjunto de argumentos aprovado.  
  
**Resolução:** Esse erro não é acionável pelo usuário e será preterido em uma versão futura.  
  
|Mensagens de exceção|  
|------------------------|  
|Modelo não treinado, treinar modelo primeiro.|  
|Modelo não treinado ({0}), use o modelo treinado.|  
  

## <a name="error-0013"></a>Erro 0013  
 Ocorrerá uma exceção se o aprendiz passado para o módulo for um tipo inválido.  
  
 Esse erro ocorre sempre que um modelo treinado é incompatível com o módulo de Pontuação conectado. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the experiment is run.  -->
  
**Resolução:**

Determine o tipo de aprendiz que é produzido pelo módulo de treinamento e determine o módulo de Pontuação apropriado para o aprendiz. 

Se o modelo foi treinado usando qualquer um dos módulos de treinamento especializados, conecte o modelo treinado somente ao módulo de Pontuação especializado correspondente. 


|Tipo de modelo|Módulo de treinamento| Módulo de Pontuação|
|----|----|----|
|qualquer classificador|[Modelo de treinamento](train-model.md) |[Modelo de Pontuação](score-model.md)|
|qualquer modelo de regressão|[Modelo de treinamento](train-model.md) |[Modelo de Pontuação](score-model.md)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The module does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->
  
|Mensagens de exceção|  
|------------------------|  
|Um aprendiz de tipo inválido é passado.|  
|O aprendiz "{0}" tem um tipo inválido.|  


## <a name="error-0014"></a>Erro 0014  
 Ocorrerá uma exceção se a contagem de valores exclusivos de coluna for maior que o permitido.  
  
 Esse erro ocorre quando uma coluna contém um número excessivo de valores exclusivos.  Por exemplo, você poderá ver esse erro se especificar que uma coluna seja tratada como dados categóricos, mas há muitos valores exclusivos na coluna para permitir que o processamento seja concluído. Você também poderá ver esse erro se houver uma incompatibilidade entre o número de valores exclusivos em duas entradas.   
  
**Resolução:**

Abra o módulo que gerou o erro e identifique as colunas usadas como entradas. Para alguns módulos, você pode clicar com o botão direito do mouse na entrada do conjunto de dados e selecionar **Visualizar** para obter estatísticas em colunas individuais, incluindo o número de valores exclusivos e sua distribuição.

Para as colunas que você pretende usar para Agrupamento ou categorização, execute as etapas para reduzir o número de valores exclusivos em colunas. Você pode reduzir de maneiras diferentes, dependendo do tipo de dados da coluna. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Não foi possível encontrar uma resolução que corresponda ao seu cenário? Você pode fornecer comentários sobre este tópico que inclui o nome do módulo que gerou o erro e o tipo de dados e a cardinalidade da coluna. Usaremos as informações para fornecer etapas de solução de problemas mais direcionadas para cenários comuns.   
  
|Mensagens de exceção|  
|------------------------|  
|O número de valores exclusivos de coluna é maior que o permitido.|  
|Número de valores exclusivos na coluna: "{0}" excede a contagem de {1}tupla de.|  
  

## <a name="error-0015"></a>Erro 0015  
 Ocorrerá uma exceção se a conexão do banco de dados falhar.  
  
 Você receberá esse erro se inserir um nome de conta SQL incorreto, uma senha, um servidor de banco de dados ou um nome de banco de dados ou se uma conexão com o banco de dados não puder ser estabelecida devido a problemas com o banco de dados ou servidor.  
  
**Resolução:** Verifique se o nome da conta, a senha, o servidor de banco de dados e o banco de dados foram inseridos corretamente e se a conta especificada tem o nível correto de permissões. Verifique se o banco de dados está acessível no momento.  
  
|Mensagens de exceção|  
|------------------------|  
|Erro ao fazer conexão de banco de dados.|  
|Erro ao fazer conexão de {0}banco de dados:.|  
  


## <a name="error-0016"></a>Erro 0016  
 Ocorrerá uma exceção se os conjuntos de dados de entrada passados para o módulo devem ter tipos de coluna compatíveis, mas não o fazem.  
  
 Você receberá esse erro em Azure Machine Learning se os tipos das colunas passados em dois ou mais conjuntos de valores não forem compatíveis entre si.  
  
**Resolução:** Usar [Editar metadados](edit-metadata.md) ou modificar o conjunto de dados de entrada original<!--, or use [Convert to Dataset](convert-to-dataset.md)--> para garantir que os tipos de colunas sejam compatíveis.  
  
|Mensagens de exceção|  
|------------------------|  
|Colunas com índice correspondente em conjuntos de dados de entrada têm tipos incompatíveis.|  
|Colunas {0} e{1} são incompatíveis.|  
|Os tipos de elemento de coluna não são {0} compatíveis com a coluna (baseada em zero) dos{1} conjuntos {2} de dados de entrada (e, respectivamente).|  
  

## <a name="error-0017"></a>Erro 0017  
 Ocorrerá uma exceção se uma coluna selecionada usar um tipo de dados que não é suportado pelo módulo atual.  
  
 Por exemplo, você pode receber esse erro em Azure Machine Learning se a seleção de coluna incluir uma coluna com um tipo de dados que não pode ser processado pelo módulo, como uma coluna de cadeia de caracteres para uma operação matemática ou uma coluna de pontuação em que uma coluna de recurso categórico é Necessário.  
  
**Resolução:**
 1. Identifique a coluna que é o problema.
 2. Examine os requisitos do módulo.
 3. Modifique a coluna para que ela fique em conformidade com os requisitos. Talvez seja necessário usar vários dos seguintes módulos para fazer alterações, dependendo da coluna e da conversão que você está tentando:
    + Use [Editar metadados](edit-metadata.md) para alterar o tipo de dados de colunas ou para alterar o uso da coluna de recurso para numérico, categórica para não categórica e assim por diante.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Como último recurso, talvez seja necessário modificar o conjunto de dados de entrada original.

> [!TIP]
> Não foi possível encontrar uma resolução que corresponda ao seu cenário? Você pode fornecer comentários sobre este tópico que inclui o nome do módulo que gerou o erro e o tipo de dados e a cardinalidade da coluna. Usaremos as informações para fornecer etapas de solução de problemas mais direcionadas para cenários comuns. 
  
|Mensagens de exceção|  
|------------------------|  
|Não é possível processar a coluna do tipo atual. O módulo não dá suporte ao tipo.|  
|Não é possível processar a {0}coluna do tipo. O módulo não dá suporte ao tipo.|  
|Não é possível processar{1}a coluna " {0}" do tipo. O módulo não dá suporte ao tipo.|  
|Não é possível processar{1}a coluna " {0}" do tipo. O módulo não dá suporte ao tipo. Nome do parâmetro:{2}|  
  

## <a name="error-0018"></a>Erro 0018  
 Ocorrerá uma exceção se o conjunto de dados de entrada não for válido.  
  
**Resolução:** Esse erro no Azure Machine Learning pode aparecer em muitos contextos, portanto, não há uma única resolução. Em geral, o erro indica que os dados fornecidos como entrada para um módulo têm o número incorreto de colunas ou que o tipo de dados não corresponde aos requisitos do módulo. Por exemplo:  
  
-   O módulo requer uma coluna de rótulo, mas nenhuma coluna é marcada como um rótulo ou você ainda não selecionou uma coluna de rótulo.  
  
-   O módulo requer que os dados sejam categóricos, mas seus dados são numéricos.  
  
<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->
  
-   Os dados estão no formato incorreto.  
  
-   Os dados importados contêm caracteres inválidos, valores inválidos ou valores fora do intervalo.  
-   A coluna está vazia ou contém muitos valores ausentes.  
  
 Para determinar os requisitos e como seus dados podem, examine o tópico da ajuda para o módulo que consumirá o DataSet como entrada.  
  
 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  
  
|Mensagens de exceção|  
|------------------------|  
|DataSet não é válido.|  
|{0}contém dados inválidos.|  
|{0}e {1} deve ser uma coluna consistente.|  
  

## <a name="error-0019"></a>Erro 0019  
 Ocorrerá uma exceção se a coluna deve conter valores classificados, mas não tem.  
  
 Você receberá esse erro em Azure Machine Learning se os valores de coluna especificados estiverem fora de ordem.  
  
**Resolução:** Classifique os valores de coluna modificando manualmente o conjunto de dados de entrada e execute o módulo novamente.  
  
|Mensagens de exceção|  
|------------------------|  
|Os valores na coluna não são classificados.|  
|Os valores na coluna{0}"" não são classificados.|  
|Os valores na coluna{0}"" do conjunto{1}de valores "" não estão classificados.|  
  

## <a name="error-0020"></a>Erro 0020  
 Ocorrerá uma exceção se o número de colunas em alguns dos conjuntos de valores passados para o módulo for muito pequeno.  
  
 Você receberá esse erro em Azure Machine Learning se não houver colunas suficientes selecionadas para um módulo.  
  
**Resolução:** Revisite o módulo e verifique se o seletor de coluna tem o número correto de colunas selecionado.  
  
|Mensagens de exceção|  
|------------------------|  
|O número de colunas no conjunto de dados de entrada é menor que o mínimo permitido.|  
|O número de colunas no conjunto de dados de entrada é menor {0} que o mínimo permitido de coluna (s).|  
|O número de colunas no conjunto de{0}dados de entrada "" é menor {1} que o mínimo permitido de coluna (s).|

## <a name="error-0021"></a>Erro 0021  
 Ocorrerá uma exceção se o número de linhas em alguns dos conjuntos de registros passados para o módulo for muito pequeno.  
  
 Esse erro em visto no Azure Machine Learning quando não há linhas suficientes no conjunto de registros para executar a operação especificada. Por exemplo, você poderá ver esse erro se o conjunto de dados de entrada estiver vazio ou se você estiver tentando executar uma operação que exige que um número mínimo de linhas seja válido. Essas operações podem incluir (mas não se limitam a) agrupamento ou classificação com base em métodos estatísticos, determinados tipos de compartimentalização e aprendizado com contagens.  
  
**Resolução:**
 
 + Abra o módulo que retornou o erro e verifique as propriedades do conjunto de dados de entrada e do módulo. 
 + Verifique se o conjunto de dados de entrada não está vazio e se há linhas suficientes para atender aos requisitos descritos na ajuda do módulo.  
 + Se os dados forem carregados de uma fonte externa, verifique se a fonte de dados está disponível e se não há erro ou alteração na definição de dados que faria com que o processo de importação obtivesse menos linhas.
 + Se você estiver executando uma operação no upstream de dados do módulo que pode afetar o tipo de dados ou o número de valores, como operações de limpeza, divisão ou junção, verifique as saídas dessas operações para determinar o número de linhas retornadas.  



## <a name="error-0022"></a>Erro 0022  
 Ocorrerá uma exceção se o número de colunas selecionadas no conjunto de dados de entrada não for igual ao número esperado.  
  
 Esse erro no Azure Machine Learning pode ocorrer quando o módulo downstream ou a operação requer um número específico de colunas ou entradas e você forneceu poucas ou muitas colunas ou entradas. Por exemplo:  
  
-   Você especifica uma única coluna de rótulo ou coluna de chave e selecionou várias colunas acidentalmente.  
  
-   Você está renomeando colunas, mas forneceu mais ou menos nomes do que as colunas.  
  
-   O número de colunas na origem ou no destino foi alterado ou não corresponde ao número de colunas usadas pelo módulo.  
  
-   Você forneceu uma lista de valores separados por vírgulas para entradas, mas o número de valores não corresponde ou não há suporte para várias entradas.  
  
**Resolução:** Revisite o módulo e marque a seleção de coluna para garantir que o número correto de colunas esteja selecionado. Verifique as saídas dos módulos upstream e os requisitos das operações downstream.  
  
 Se você usou uma das opções de seleção de coluna que pode selecionar várias colunas (índices de coluna, todos os recursos, todos os números, etc.), valide o número exato de colunas retornadas pela seleção.  
  
 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->
  
 Verifique se o número ou o tipo de colunas de upstream não foi alterado.  
  
 Se você estiver usando um conjunto de uma recomendação para treinar um modelo, lembre-se de que o recomendador espera um número limitado de colunas, que corresponde a pares de item de usuário ou classificações de item de usuário. Remova colunas adicionais antes de treinar o modelo ou dividir conjuntos de valores de recomendação. Para obter mais informações, consulte [dividir dados](split-data.md).  
  
|Mensagens de exceção|  
|------------------------|  
|O número de colunas selecionadas no conjunto de dados de entrada não é igual ao número esperado.|  
|O número de colunas selecionadas no conjunto de dados de entrada {0}não é igual a.|  
|Padrão de seleção de{0}coluna "" fornece o número de colunas selecionadas no conjunto de {1}dados de entrada diferente de.|  
|O padrão de seleção{0}de coluna "" deve {1} fornecer colunas selecionadas no conjunto de dados de entrada, {2} mas as colunas são fornecidas.|  



## <a name="error-0023"></a>Erro 0023  
 Ocorrerá uma exceção se a coluna de destino do conjunto de dados de entrada não for válida para o módulo do instrutor atual.  
  
 Esse erro em Azure Machine Learning ocorrerá se a coluna de destino (conforme selecionada nos parâmetros do módulo) não for do tipo de dados válido, contiver todos os valores ausentes ou não for categórica como o esperado.  
  
**Resolução:** Reveja a entrada do módulo para inspecionar o conteúdo da coluna rótulo/destino. Verifique se ele não tem todos os valores ausentes. Se o módulo estiver esperando que a coluna de destino seja categórica, verifique se há mais de um valor distinto na coluna de destino.  
  
|Mensagens de exceção|  
|------------------------|  
|O conjunto de dados de entrada tem uma coluna de destino sem suporte.|  
|O conjunto de dados de entrada tem uma coluna{0}de destino não suportada "".|  
|O conjunto de dados de entrada tem uma coluna{0}de destino sem suporte "" {1}para o aprendiz do tipo.|  
 

## <a name="error-0024"></a>Erro 0024  
Ocorrerá uma exceção se o conjunto de um não contiver uma coluna de rótulo.  

 Esse erro em Azure Machine Learning ocorre quando o módulo requer uma coluna de rótulo e o conjunto de um não tem uma coluna de rótulo. Por exemplo, a avaliação de um conjunto de uma pontuação geralmente requer que uma coluna de rótulo esteja presente para computar métricas de precisão.  
 
Também pode acontecer que uma coluna de rótulo esteja presente no conjunto de, mas não seja detectada corretamente pelo Azure Machine Learning.
  
**Resolução:**

+ Abra o módulo que gerou o erro e determine se uma coluna de rótulo está presente. O nome ou tipo de dados da coluna não importa, desde que a coluna contenha um único resultado (ou variável dependente) que você esteja tentando prever. Se você não tiver certeza de qual coluna tem o rótulo, procure um nome genérico, como *classe* ou *destino*. 
+  Se o conjunto de um não incluir uma coluna de rótulo, é possível que a coluna de rótulo tenha sido removida explícita ou acidentalmente upstream. Também pode ser que o conjunto de resultados não seja a saída de um módulo de Pontuação upstream.
+ Para marcar explicitamente a coluna como a coluna de rótulo, adicione o módulo [Editar metadados](edit-metadata.md) e conecte o conjunto de um. Selecione apenas a coluna rótulo e selecione **rótulo** na lista suspensa **campos** . 
+ Se a coluna errada for escolhida como o rótulo, você poderá selecionar **limpar rótulo** nos **campos** para corrigir os metadados na coluna. 
  
|Mensagens de exceção|  
|------------------------|  
|Não há nenhuma coluna de rótulo no DataSet.|  
|Não há nenhuma coluna de rótulo em{0}"".|  
  

## <a name="error-0025"></a>Erro 0025  
 Ocorrerá uma exceção se o conjunto de um não contiver uma coluna de pontuação.  
  
 Esse erro em Azure Machine Learning ocorrerá se a entrada para o modelo de avaliação não contiver colunas de Pontuação válidas. Por exemplo, o usuário tenta avaliar um conjunto de uma antes de ele ter sido pontuado com um modelo treinado correto ou a coluna de pontuação foi explicitamente descartada com upstream. Essa exceção também ocorrerá se as colunas de pontuação nos dois conjuntos de valores forem incompatíveis. Por exemplo, você pode estar tentando comparar a precisão de um regressor linear com o de um classificador binário.  
  
**Resolução:** Reveja a entrada para o modelo de avaliação e examine se ela contém uma ou mais colunas de pontuação. Caso contrário, o conjunto de linhas não foi pontuado ou as colunas de Pontuação foram descartadas em um módulo upstream.  
  
|Mensagens de exceção|  
|------------------------|  
|Não há nenhuma coluna de pontuação no DataSet.|  
|Não há nenhuma coluna de pontuação em{0}"".|  
|Não há nenhuma coluna de pontuação em{0}"" que é produzida por um{1}"". Pontuar o conjunto de informações usando o tipo correto de aprendiz.|  
  

## <a name="error-0026"></a>Erro 0026  
 Ocorrerá uma exceção se as colunas com o mesmo nome não forem permitidas.  
  
 Esse erro em Azure Machine Learning ocorrerá se várias colunas tiverem o mesmo nome. Uma maneira de receber esse erro é se o conjunto de linhas não tiver uma linha de cabeçalho e os nomes de coluna forem atribuídos automaticamente: Col0, Col1, etc.  
  
**Resolução:** Se as colunas tiverem o mesmo nome, insira um módulo [Editar metadados](edit-metadata.md) entre o conjunto de dados de entrada e o módulo. Use o seletor de coluna em [Editar metadados](edit-metadata.md) para selecionar as colunas a serem renomeadas, digitando os novos nomes na caixa de texto **nomes de nova coluna** .  
  
|Mensagens de exceção|  
|------------------------|  
|Os nomes de coluna iguais são especificados em argumentos. Nomes de coluna iguais não são permitidos pelo módulo.|  
|Não são permitidos nomes de coluna{0}iguais nos argumentos{1}"" e "". Especifique nomes diferentes.|  
  

## <a name="error-0027"></a>Erro 0027  
 Ocorrerá uma exceção no caso de dois objetos precisarem ter o mesmo tamanho, mas não forem.  
  
 Esse é um erro comum no Azure Machine Learning e pode ser causado por muitas condições.  
  
**Resolução:** Não há nenhuma resolução específica. No entanto, você pode verificar se há condições como as seguintes:  
  
-   Se você estiver renomeando colunas, certifique-se de que cada lista (as colunas de entrada e a lista de novos nomes) tenha o mesmo número de itens.  
  
-   Se você estiver unindo ou concatenando dois conjuntos de os, verifique se eles têm o mesmo esquema.  
  
-   Se você estiver unindo dois conjuntos de dados que têm várias colunas, verifique se as colunas de chave têm o mesmo tipo de dado e selecione a opção **permitir duplicatas e preservar a ordem das colunas na seleção**.  
  
|Mensagens de exceção|  
|------------------------|  
|O tamanho dos objetos passados é inconsistente.|  
|O tamanho de "{0}" é inconsistente com o tamanho{1}de "".|  
  

## <a name="error-0028"></a>Erro 0028  
 Ocorrerá uma exceção no caso em que o conjunto de colunas contiver nomes de coluna duplicados e não for permitido.  
  
 Esse erro em Azure Machine Learning ocorre quando os nomes de coluna são duplicados; ou seja, não exclusivo.  
  
**Resolução:** Se alguma coluna tiver o mesmo nome, adicione uma instância de [Editar metadados](edit-metadata.md) entre o conjunto de dados de entrada e o módulo gerando o erro. Use o seletor de coluna em [Editar metadados](edit-metadata.md) para selecionar as colunas a serem renomeadas e digite os nomes das novas colunas na caixa de texto **nomes de nova coluna** . Se você estiver renomeando várias colunas, certifique-se de que os valores digitados nos **novos nomes de coluna** sejam exclusivos.  
  
|Mensagens de exceção|  
|------------------------|  
|O conjunto de colunas contém nome (s) de coluna duplicados.|  
|O nome "{0}" está duplicado.|  
|O nome "{0}" está duplicado em "{1}".|  
  

## <a name="error-0029"></a>Erro 0029  
 Ocorrerá uma exceção caso o URI inválido seja passado.  
  
 Esse erro em Azure Machine Learning ocorre no caso quando um URI inválido é passado.  Você receberá esse erro se qualquer uma das seguintes condições for verdadeira: ou.  
  
-   O URI público ou SAS fornecido para o armazenamento de BLOBs do Azure para leitura ou gravação contém um erro.  
  
-   A janela de tempo para a SAS expirou.  
  
-   A URL da Web via origem HTTP representa um arquivo ou um URI de loopback.  
  
-   A URL da Web via HTTP contém uma URL formatada incorretamente.  
  
-   A URL não pode ser resolvida pela origem remota.  
  
**Resolução:** Revisite o módulo e verifique o formato do URI. Se a fonte de dados for uma URL da Web via HTTP, verifique se a origem pretendida não é um arquivo ou um URI de loopback (localhost).  
  
|Mensagens de exceção|  
|------------------------|  
|URI inválido passado.|  
  

## <a name="error-0030"></a>Erro 0030  
 Ocorrerá uma exceção no caso de não ser possível baixar um arquivo.  
  
 Essa exceção no Azure Machine Learning ocorre quando não é possível baixar um arquivo. Você receberá essa exceção quando uma tentativa de ler de uma origem HTTP falhar após três (3) tentativas de repetição.  
  
**Resolução:** Verifique se o URI para a origem HTTP está correto e se o site está acessível no momento pela Internet.  
  
|Mensagens de exceção|  
|------------------------|  
|Não é possível baixar um arquivo.|  
|Erro ao baixar o arquivo: {0}.|  
  

## <a name="error-0031"></a>Erro 0031  
 Ocorrerá uma exceção se o número de colunas no conjunto de colunas for menor que o necessário.  
  
 Esse erro em Azure Machine Learning ocorrerá se o número de colunas selecionado for menor que o necessário.  Você receberá esse erro se o número mínimo necessário de colunas não estiver selecionado.  
  
**Resolução:** Adicione colunas adicionais à seleção de coluna usando o seletor de **coluna**.  
  
|Mensagens de exceção|  
|------------------------|  
|O número de colunas no conjunto de colunas é menor que o necessário.|  
|{0}as colunas devem ser especificadas. O número real de colunas especificadas é {1}.|  

## <a name="error-0032"></a>Erro 0032  
 Ocorrerá uma exceção se o argumento não for um número.  
  
 Você receberá esse erro em Azure Machine Learning se o argumento for double ou NaN.  
  
**Resolução:** Modifique o argumento especificado para usar um valor válido.  
  
|Mensagens de exceção|  
|------------------------|  
|O argumento não é um número.|  
|"{0}" não é um número.|  
  

## <a name="error-0033"></a>Erro 0033  
 Ocorrerá uma exceção se o argumento for infinito.  
  
 Esse erro em Azure Machine Learning ocorrerá se o argumento for infinito. Você receberá esse erro se o argumento for `double.NegativeInfinity` ou. `double.PositiveInfinity`  
  
**Resolução:** Modifique o argumento especificado para que seja um valor válido.  
  
|Mensagens de exceção|  
|------------------------|  
|O argumento deve ser finito.|  
|"{0}" não é finito.|  
  

## <a name="error-0034"></a>Erro 0034  
 Ocorrerá uma exceção se houver mais de uma classificação para um determinado par de item de usuário.  
  
 Esse erro em Azure Machine Learning ocorrerá na recomendação se um par usuário-item tiver mais de um valor de classificação.  
  
**Resolução:** Verifique se o par usuário-item possui apenas um valor de classificação.  
  
|Mensagens de exceção|  
|------------------------|  
|Há mais de uma classificação para o (s) valor (es) no conjunto de informações.|  
|Mais de uma classificação para o {0} usuário e {1} o item na tabela de dados de previsão de classificação.|  
  

## <a name="error-0035"></a>Erro 0035  
 Ocorrerá uma exceção se nenhum recurso tiver sido fornecido para um determinado usuário ou item.  
  
 Esse erro em Azure Machine Learning ocorre que você está tentando usar um modelo de recomendação para pontuação, mas um vetor de recurso não pode ser encontrado.  
  
**Resolução:**

O recomendador Matchbox tem determinados requisitos que devem ser atendidos ao usar recursos de item ou recursos de usuário.  Esse erro indica que um vetor de recurso está ausente para um usuário ou item que você forneceu como entrada.  Você deve garantir que um vetor de recursos esteja disponível nos dados para cada usuário ou item.  
  
 Por exemplo, se você treinasse um modelo de recomendação usando recursos como a idade, o local ou a renda do usuário, mas agora deseja criar pontuações para novos usuários que não foram vistos durante o treinamento, você deve fornecer algum conjunto equivalente de recursos (ou seja, idade, local e valores de renda) para os novos usuários a fim de fazer previsões apropriadas para eles. 
 
 Se você não tiver nenhum recurso para esses usuários, considere a engenharia de recursos para gerar os recursos apropriados.  Por exemplo, se você não tiver valores de idade de usuário ou de renda individuais, poderá gerar valores aproximados a serem usados para um grupo de usuários. 
 
<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).
 
For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->
  
 > [!TIP]
 > A resolução não se aplica ao seu caso? Você é bem-vindo a enviar comentários sobre este artigo e fornecer informações sobre o cenário, incluindo o módulo e o número de linhas na coluna. Usaremos essas informações para fornecer etapas de solução de problemas mais detalhadas no futuro.
   
|Mensagens de exceção|  
|------------------------|  
|Nenhum recurso foi fornecido para um usuário ou item necessário.|  
|{0} Recursos necessários, mas não fornecidos.|  
  

## <a name="error-0036"></a>Erro 0036  
 Ocorrerá uma exceção se vários vetores de recurso foram fornecidos para um determinado usuário ou item.  
  
 Esse erro em Azure Machine Learning ocorrerá se um vetor de recurso for definido mais de uma vez.  
  
**Resolução:** Verifique se o vetor de recurso não está definido mais de uma vez.  
  
|Mensagens de exceção|  
|------------------------|  
|Definição de recurso duplicada para um usuário ou item.|  
|Definição de recurso duplicada para {0}.|  
  

## <a name="error-0037"></a>Erro 0037  
 Ocorrerá uma exceção se várias colunas de rótulo forem especificadas e apenas uma for permitida.  
  
 Esse erro em Azure Machine Learning ocorrerá se mais de uma coluna estiver selecionada para ser a nova coluna de rótulo. A maioria dos algoritmos de aprendizado supervisionados exige que uma única coluna seja marcada como o destino ou o rótulo.  
  
**Resolução:** Certifique-se de selecionar uma única coluna como a nova coluna de rótulo.  
  
|Mensagens de exceção|  
|------------------------|  
|Várias colunas de rótulo são especificadas.|  
  

## <a name="error-0038"></a>Erro 0038  
 Ocorrerá uma exceção se o número de elementos esperado deve ser um valor exato, mas não é.  
  
 Esse erro em Azure Machine Learning ocorre se o número de elementos esperado deve ser um valor exato, mas não é.  Você receberá esse erro se o número de elementos não for igual ao valor esperado válido.  
  
**Resolução:** Modifique a entrada para ter o número correto de elementos.  
  
|Mensagens de exceção|  
|------------------------|  
|O número de elementos não é válido.|  
|O número de elementos em{0}"" não é válido.|  
|O número de elementos em{0}"" não é igual ao número válido {1} de elemento (s).|  
  

## <a name="error-0039"></a>Erro 0039  
 Ocorrerá uma exceção se uma operação tiver falhado.  
  
 Esse erro em Azure Machine Learning ocorre quando uma operação interna não pode ser concluída.  
  
**Resolução:** Esse erro é causado por muitas condições e não há uma solução específica.  
 A tabela a seguir contém mensagens genéricas para esse erro, que são seguidas por uma descrição específica da condição. 
 
 Se não houver detalhes disponíveis, [envie comentários](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) e forneça informações sobre os módulos que geraram o erro e as condições relacionadas.
  
|Mensagens de exceção|  
|------------------------|  
|Falha na operação.|  
|Erro ao concluir a operação {0}:.|  
  

## <a name="error-0040"></a>Erro 0040  
 Ocorre uma exceção ao chamar um módulo preterido.  
  
 Esse erro no Azure Machine Learning é produzido ao chamar um módulo preterido.  
  
**Resolução:** Substitua o módulo preterido por um com suporte. Consulte o log de saída do módulo para obter as informações sobre qual módulo usar em vez disso.  
  
|Mensagens de exceção|  
|------------------------|  
|Acessando o módulo preterido.|  
|O módulo{0}"" foi preterido. Em vez disso{1}, use o módulo "".|  
 

## <a name="error-0041"></a>Erro 0041  
 Ocorre uma exceção ao chamar um módulo preterido.  
  
 Esse erro no Azure Machine Learning é produzido ao chamar um módulo preterido.  
  
**Resolução:** Substitua o módulo preterido por um conjunto de itens com suporte. Essas informações devem estar visíveis no log de saída do módulo.  
  
|Mensagens de exceção|  
|------------------------|  
|Acessando o módulo preterido.|  
|O módulo{0}"" foi preterido. Use os módulos "{1}" para a funcionalidade solicitada.|  
 

## <a name="error-0042"></a>Erro 0042  
 Ocorre uma exceção quando não é possível converter a coluna em outro tipo.  
  
 Esse erro no Azure Machine Learning ocorre quando não é possível converter a coluna para o tipo especificado.  Você receberá esse erro se um módulo exigir um tipo de dados específico, como DateTime, Text, um número de ponto flutuante ou inteiro, mas não for possível converter uma coluna existente para o tipo necessário.  
 
Por exemplo, você pode selecionar uma coluna e tentar convertê-la em um tipo de dados numérico para uso em uma operação matemática e obter esse erro se a coluna contiver dados inválidos. 

Outro motivo para você receber esse erro se tentar usar uma coluna que contém números de ponto flutuante ou muitos valores exclusivos como uma coluna categórica. 
  
**Resolução:**

+ Abra a página de ajuda do módulo que gerou o erro e verifique os requisitos de tipo de dados.
+ Examine os tipos de dados das colunas no conjunto de dado de entrada.
+ Inspecione os dados originados em chamadas de fontes de dados sem esquema.
+ Verifique se há valores ausentes ou caracteres especiais no conjunto de dados que possam bloquear a conversão para o tipo de dado desejado. 
    + Os tipos de dados numéricos devem ser consistentes: por exemplo, verifique se há números de ponto flutuante em uma coluna de inteiros.
    + Procure cadeias de caracteres de texto ou valores na em uma coluna de número. 
    + Valores Boolianos podem ser convertidos em uma representação apropriada dependendo do tipo de dados necessário.
    + Examinar colunas de texto para caracteres não Unicode, caracteres de tabulação ou caracteres de controle
    + Os dados de data e hora devem ser consistentes para evitar erros de modelagem, mas a limpeza pode ser devido complexa para os vários formatos. Considere usar <!--the [Execute R Script](execute-r-script.md) or -->Execute os módulos de [script Python](execute-python-script.md) para executar a limpeza.  
+ Se necessário, modifique os valores no conjunto de dados de entrada para que a coluna possa ser convertida com êxito. A modificação pode incluir compartimentalização, truncamento ou operações de arredondamento, eliminação de exceções ou imputação de valores ausentes. Consulte os seguintes artigos para ver alguns cenários comuns de transformação de dados no aprendizado de máquina:
    + [Limpar dados ausentes](clean-missing-data.md)
    + [Normalizar dados](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->
 
> [!TIP]
> Resolução desclarada ou não aplicável ao seu caso? Você é bem-vindo a enviar comentários sobre este artigo e fornecer informações sobre o cenário, incluindo o módulo e o tipo de dados da coluna. Usaremos essas informações para fornecer etapas de solução de problemas mais detalhadas no futuro.  
  
|Mensagens de exceção|  
|------------------------|  
|Conversão não permitida.|  
|Não foi possível converter a coluna {0} do tipo para a {1}coluna do tipo.|  
|Não foi possível converter a{2}coluna "" {0} do tipo na coluna {1}do tipo.|  
|Não foi possível converter a{2}coluna "" {0} do tipo para{3}a coluna " {1}" do tipo.|  
  

## <a name="error-0043"></a>Erro 0043  
 Ocorre uma exceção quando o tipo de elemento não implementa explicitamente Equals.  
  
 Esse erro em Azure Machine Learning não é usado e será preterido.  
  
**Resolução:** Nenhum.  
  
|Mensagens de exceção|  
|------------------------|  
|Nenhum método explícito acessível é igual a encontrado.|  
|Não é possível comparar valores \\para{0}a coluna " {1}\\" do tipo. Nenhum método explícito acessível é igual a encontrado.|  


## <a name="error-0044"></a>Erro 0044  
 Ocorrerá uma exceção quando não for possível derivar o tipo de elemento da coluna a partir dos valores existentes.  
  
 Esse erro em Azure Machine Learning ocorre quando não é possível inferir o tipo de uma coluna ou colunas em um DataSet. Isso normalmente acontece ao concatenar dois ou mais conjuntos de valores com diferentes tipos de elementos. Se Azure Machine Learning não puder determinar um tipo comum que possa representar todos os valores em uma coluna ou colunas sem perda de informações, ele gerará esse erro.  
  
**Resolução:** Verifique se todos os valores em uma determinada coluna em ambos os conjuntos de dados que estão sendo combinados são do mesmo tipo (Numeric, Boolean, categórico, String, Date, etc.) ou podem ser forçados para o mesmo tipo.  
  
|Mensagens de exceção|  
|------------------------|  
|Não é possível derivar o tipo de elemento da coluna.|  
|Não é possível derivar o tipo{0}de elemento para a coluna ""-todos os elementos são referências nulas.|  
|Não é possível derivar o tipo{0}de elemento para a{1}coluna "" do conjunto de valores ""-todos os elementos são referências nulas.|  
  

## <a name="error-0045"></a>Erro 0045  
 Ocorre uma exceção quando não é possível criar uma coluna devido a tipos de elementos mistos na origem.  
  
 Esse erro no Azure Machine Learning é produzido quando os tipos de elementos de dois conjuntos de valores que estão sendo combinados são diferentes.  
  
**Resolução:** Verifique se todos os valores em uma determinada coluna em ambos os conjuntos de dados que estão sendo combinados são do mesmo tipo (Numeric, Boolean, categórico, String, Date, etc.).  
  
|Mensagens de exceção|  
|------------------------|  
|Não é possível criar coluna com tipos de elementos mistos.|  
|Não é possível criar a coluna{0}com a ID "" de tipos de elementos mistos{1}: \ n\tType {2}de dados [,{3} {0}] {0}é \n\tType {4}de dados [,] é.|  
  

## <a name="error-0046"></a>Erro 0046  
 Ocorrerá uma exceção quando não for possível criar o diretório no caminho especificado.  
  
 Esse erro no Azure Machine Learning ocorre quando não é possível criar um diretório no caminho especificado. Você receberá esse erro se qualquer parte do caminho para o diretório de saída de uma consulta de Hive estiver incorreta ou inacessível.  
  
**Resolução:** Revisite o módulo e verifique se o caminho do diretório está formatado corretamente e se está acessível com as credenciais atuais.  
  
|Mensagens de exceção|  
|------------------------|  
|Especifique um diretório de saída válido.|  
|Diretório: {0} não pode ser criado. Especifique um caminho válido.|  
  

## <a name="error-0047"></a>Erro 0047  
 Ocorrerá uma exceção se o número de colunas de recursos em alguns dos conjuntos de valores passados para o módulo for muito pequeno.  
  
 Esse erro em Azure Machine Learning ocorrerá se o conjunto de dados de entrada para treinamento não contiver o número mínimo de colunas exigidas pelo algoritmo. Normalmente, o conjunto de linhas está vazio ou contém apenas colunas de treinamento.  
  
**Resolução:** Reveja o conjunto de dados de entrada para ter certeza de que há uma ou mais colunas adicionais separadas da coluna de rótulo.  
  
|Mensagens de exceção|  
|------------------------|  
|O número de colunas de recurso no conjunto de dados de entrada é menor que o mínimo permitido.|  
|O número de colunas de recurso no conjunto de dados de entrada é {0} menor que o mínimo permitido de coluna (s).|  
|O número de colunas de recurso no conjunto{0}de dados de entrada "" é {1} menor que o mínimo permitido de coluna (s).|  
  

## <a name="error-0048"></a>Erro 0048  
 Ocorrerá uma exceção no caso de não ser possível abrir um arquivo.  
  
 Esse erro no Azure Machine Learning ocorre quando não é possível abrir um arquivo para leitura ou gravação. Você pode receber esse erro por estes motivos:  
  
-   O contêiner ou o arquivo (BLOB) não existe  
  
-   O nível de acesso do arquivo ou contêiner não permite que você acesse o arquivo  
  
-   O arquivo é muito grande para ler ou para o formato incorreto  
  
**Resolução:** Revisite o módulo e o arquivo que você está tentando ler.  
  
 Verifique se os nomes do contêiner e do arquivo estão corretos.  
  
 Use o portal clássico do Azure ou uma ferramenta de armazenamento do Azure para verificar se você tem permissão para acessar o arquivo.  
  
  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->
  
|Mensagens de exceção|  
|------------------------|  
|Não é possível abrir um arquivo.|  
|Erro ao abrir o arquivo: {0}.|  


## <a name="error-0049"></a>Erro 0049  
 Ocorrerá uma exceção no caso de não ser possível analisar um arquivo.  
  
 Esse erro no Azure Machine Learning ocorre quando não é possível analisar um arquivo. Você receberá esse erro se o formato de arquivo selecionado no módulo [importar dados](import-data.md) não corresponder ao formato real do arquivo ou se o arquivo contiver um caractere irreconhecível.  
  
**Resolução:** Revisite o módulo e corrija a seleção de formato de arquivo se ele não corresponder ao formato do arquivo. Se possível, inspecione o arquivo para confirmar se ele não contém nenhum caractere ilegal.  
  
|Mensagens de exceção|  
|------------------------|  
|Não é possível analisar um arquivo.|  
|Erro ao analisar o arquivo: {0}.|  
  

## <a name="error-0050"></a>Erro 0050  
 Ocorrerá uma exceção no caso de os arquivos de entrada e saída serem os mesmos.  
  
**Resolução:** Esse erro em Azure Machine Learning não é usado e será preterido.  
  
|Mensagens de exceção|  
|------------------------|  
|Os arquivos especificados para entrada e saída não podem ser iguais.|


## <a name="error-0051"></a>Erro 0051  
 Ocorrerá uma exceção no caso de vários arquivos de saída serem iguais.  
  
**Resolução:** Esse erro em Azure Machine Learning não é usado e será preterido.  
  
|Mensagens de exceção|  
|------------------------|  
|Os arquivos especificados para saídas não podem ser iguais.|


## <a name="error-0052"></a>Erro 0052  
 Ocorrerá uma exceção se a chave de conta de armazenamento do Azure for especificada incorretamente.  
  
 Esse erro em Azure Machine Learning ocorrerá se a chave usada para acessar a conta de armazenamento do Azure estiver incorreta. Por exemplo, você poderá ver esse erro se a chave de armazenamento do Azure estava truncada quando copiada e colada, ou se a chave errada foi usada.  
  
 Para obter mais informações sobre como obter a chave para uma conta de armazenamento do Azure, consulte [Exibir, copiar e regenerar chaves de acesso de armazenamento](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/).  
  
**Resolução:** Revisite o módulo e verifique se a chave de armazenamento do Azure está correta para a conta; Copie a chave novamente no portal clássico do Azure, se necessário.  
  
|Mensagens de exceção|  
|------------------------|  
|A chave da conta de armazenamento do Azure está incorreta.|  
  

## <a name="error-0053"></a>Erro 0053  
 Ocorrerá uma exceção no caso de não haver recursos de usuário ou itens para recomendações de Matchbox.  
  
 Esse erro no Azure Machine Learning é produzido quando um vetor de recurso não pode ser encontrado.  
  
**Resolução:** Verifique se um vetor de recurso está presente no conjunto de dados de entrada.  
  
|Mensagens de exceção|  
|------------------------|  
|Recursos de usuário ou/e itens são necessários, mas não fornecidos.|  

## <a name="error-0054"></a>Erro 0054  
 Ocorrerá uma exceção se houver poucos valores distintos na coluna para concluir a operação.  
  
**Resolução:** Esse erro em Azure Machine Learning não é usado e será preterido.  
  
|Mensagens de exceção|  
|------------------------|  
|Os dados têm poucos valores distintos na coluna especificada para concluir a operação.|  
|Os dados têm poucos valores distintos na coluna especificada para concluir a operação. O mínimo necessário é {0} de elementos.|  
|Os dados têm poucos valores distintos na coluna "{1}" para concluir a operação. O mínimo necessário é {0} de elementos.|  
  

## <a name="error-0055"></a>Erro 0055  
 Ocorre uma exceção ao chamar um módulo preterido.  Esse erro em Azure Machine Learning aparecerá se você tentar chamar um módulo que foi preterido.
  
**Resolução:**
  
|Mensagens de exceção|  
|------------------------|  
|Acessando o módulo preterido.|  
|O módulo{0}"" foi preterido.|  

## <a name="error-0056"></a>Erro 0056  
 Ocorrerá uma exceção se as colunas selecionadas para uma operação violarem os requisitos.  
  
 Esse erro em Azure Machine Learning ocorre quando você está escolhendo colunas para uma operação que requer que a coluna seja de um tipo de dados específico. 
 
 Esse erro também pode ocorrer se a coluna for do tipo de dados correto, mas o módulo que você está usando exigir que a coluna também seja marcada como um recurso, rótulo ou coluna categórica.  
  
  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->
  
**Resolução:**
  
1.  Examine o tipo de dados das colunas que estão selecionadas no momento. 

2. Verificar se as colunas selecionadas são categóricos, rótulo ou colunas de recurso.  
  
3.  Examine o tópico da ajuda para o módulo no qual você fez a seleção de coluna, para determinar se há requisitos específicos para o tipo de dados ou o uso da coluna.  
  
3.  Use [Editar metadados](edit-metadata.md) para alterar o tipo de coluna durante essa operação. Certifique-se de alterar o tipo de coluna de volta para seu valor original, usando outra instância de [Editar metadados](edit-metadata.md), se necessário, para operações de downstream.  
  
|Mensagens de exceção|  
|------------------------|  
|Uma ou mais colunas selecionadas não estavam em uma categoria permitida.|  
|A coluna com o{0}nome "" não está em uma categoria permitida.|  
  

## <a name="error-0057"></a>Erro 0057  
 Ocorre uma exceção ao tentar criar um arquivo ou BLOB que já existe.  
  
 Essa exceção ocorre quando você está usando o módulo [exportar dados](export-data.md) ou outro módulo para salvar os resultados de um experimento em Azure Machine Learning ao armazenamento de BLOBs do Azure, mas tenta criar um arquivo ou BLOB que já existe.   
  
**Resolução:**
 
 Você receberá esse erro somente se você tiver definido anteriormente a propriedade **modo de gravação do armazenamento de BLOBs do Azure** para **erro**. Por design, esse módulo gera um erro se você tentar gravar um conjunto de uma em um blob que já existe.
 
 - Abra as propriedades do módulo e altere a propriedade **modo de gravação do armazenamento de BLOBs do Azure** para **substituir**.
 - Como alternativa, você pode digitar o nome de um BLOB ou arquivo de destino diferente e especificar um blob que ainda não existe.  
  
|Mensagens de exceção|  
|------------------------|  
|O arquivo ou BLOB já existe.|  
|O arquivo ou o{0}blob "" já existe.|  
  

## <a name="error-0058"></a>Erro 0058  
 Esse erro em Azure Machine Learning ocorrerá se o conjunto de um não contiver a coluna de rótulo esperada.  
  
 Essa exceção também pode ocorrer quando a coluna de rótulo fornecida não corresponde aos dados ou DataType esperados pelo aprendiz ou tem os valores errados. Por exemplo, essa exceção é produzida ao usar uma coluna de rótulo de valor real ao treinar um classificador binário.  
  
**Resolução:** A resolução depende do aprendiz ou do treinador que você está usando, e os tipos de dados das colunas no seu DataSet. Primeiro, verifique os requisitos do módulo de treinamento ou de algoritmo do Machine Learning.  
  
 Revisite o conjunto de dados de entrada. Verifique se a coluna que você espera que seja tratada como o rótulo tem o tipo de dados correto para o modelo que você está criando.  
  
 Verificar as entradas de valores ausentes e eliminá-los ou substituí-los, se necessário.  
  
 Se necessário, adicione o módulo [Editar metadados](edit-metadata.md) e verifique se a coluna rótulo está marcada como um rótulo.  
  
|Mensagens de exceção|  
|------------------------|  
|A coluna de rótulo não é como esperado|  
|A coluna de rótulo não é como esperado em{0}"".|  
|A coluna de rótulo{0}"" não é esperada{1}em "".|  
  

## <a name="error-0059"></a>Erro 0059  
 Ocorrerá uma exceção se um índice de coluna especificado em um seletor de coluna não puder ser analisado.  
  
 Esse erro em Azure Machine Learning ocorrerá se um índice de coluna especificado ao usar o seletor de coluna não puder ser analisado.  Você receberá esse erro quando o índice de coluna estiver em um formato inválido que não pode ser analisado.  
  
**Resolução:** Modifique o índice de coluna para usar um valor de índice válido.  
  
|Mensagens de exceção|  
|------------------------|  
|Um ou mais índices de coluna ou intervalos de índice especificados não puderam ser analisados.|  
|Não foi possível analisar o{0}índice de coluna ou o intervalo "".|  
  

## <a name="error-0060"></a>Erro 0060  
 Ocorre uma exceção quando um intervalo de coluna fora do intervalo é especificado em um seletor de coluna.  
  
 Esse erro em Azure Machine Learning ocorre quando um intervalo de coluna fora do intervalo é especificado no seletor de coluna. Você receberá esse erro se o intervalo de coluna no seletor de coluna não corresponder às colunas no conjunto de linhas.  
  
**Resolução:** Modifique o intervalo de coluna no seletor de coluna para corresponder às colunas no conjunto de linhas.  
  
|Mensagens de exceção|  
|------------------------|  
|Intervalo de índice de coluna inválido ou fora do intervalo especificado.|  
|O intervalo de{0}colunas "" é inválido ou está fora do intervalo.|  
  

## <a name="error-0061"></a>Erro 0061  
 Ocorre uma exceção ao tentar adicionar uma linha a uma DataTable que tem um número diferente de colunas que a tabela.  
  
 Esse erro em Azure Machine Learning ocorre quando você tenta adicionar uma linha a um conjunto de um DataSet que tem um número diferente de colunas do que o DataSet.  Você receberá esse erro se a linha que está sendo adicionada ao conjunto de dados tiver um número diferente de colunas do conjunto de dados de entrada.  A linha não poderá ser acrescentada ao conjunto de linhas se o número de colunas for diferente.  
  
**Resolução:** Modifique o conjunto de dados de entrada para ter o mesmo número de colunas que a linha adicionada, ou modifique a linha adicionada para ter o mesmo número de colunas que o conjunto de dados.  
  
|Mensagens de exceção|  
|------------------------|  
|Todas as tabelas devem ter o mesmo número de colunas.|  
  

## <a name="error-0062"></a>Erro 0062  
 Ocorre uma exceção ao tentar comparar dois modelos com tipos de aprendiz diferentes.  
  
 Esse erro no Azure Machine Learning é produzido quando as métricas de avaliação para dois conjuntos de valores de DataSet diferentes não podem ser comparadas. Nesse caso, não é possível comparar a eficácia dos modelos usados para produzir os dois conjuntos de valores classificados.  
  
**Resolução:** Verifique se os resultados pontuados são produzidos pelo mesmo tipo de modelo de aprendizado de máquina (classificação binária, regressão, classificação de várias classes, recomendação, clustering, detecção de anomalias, etc.) Todos os modelos que você compara devem ter o mesmo tipo de aprendiz.  
  
|Mensagens de exceção|  
|------------------------|  
|Todos os modelos devem ter o mesmo tipo de aprendiz.|  
  

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
|Erro durante a avaliação do script R.|  
|Ocorreu o seguinte erro durante a avaliação do script r:----------início da mensagem de erro do {0} r---------------------fim da mensagem de erro do r-----------|  
|Durante a avaliação do script R "{1}" ocorreu o seguinte erro:----------início da mensagem de erro do r {0} ---------------------fim da mensagem de erro do r-----------|  
  


## <a name="error-0064"></a>Erro 0064  
 Ocorrerá uma exceção se a chave de armazenamento ou o nome da conta de armazenamento do Azure for especificado incorretamente.  
  
 Esse erro em Azure Machine Learning ocorrerá se o nome da conta de armazenamento do Azure ou a chave de armazenamento for especificada incorretamente. Você receberá esse erro se inserir um nome de conta ou senha incorretos para a conta de armazenamento. Isso pode ocorrer se você inserir manualmente o nome da conta ou a senha. Isso também pode ocorrer se a conta tiver sido excluída.  
  
**Resolução:** Verifique se o nome da conta e a senha foram inseridos corretamente e se a conta existe.  
  
|Mensagens de exceção|  
|------------------------|  
|O nome ou a chave de armazenamento da conta de armazenamento do Azure está incorreta.|  
|O nome da conta de armazenamento{0}do Azure "" ou a chave de armazenamento para o nome da conta está incorreto.|  
  

## <a name="error-0065"></a>Erro 0065  
 Ocorrerá uma exceção se o nome do blob do Azure for especificado incorretamente.  
  
 Esse erro em Azure Machine Learning ocorrerá se o nome do blob do Azure for especificado incorretamente.  Você receberá o erro se:  
  
-   O BLOB não pode ser encontrado no contêiner especificado.  
  
 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->
  
-   Somente o contêiner foi especificado como a origem em uma solicitação de [importação de dados](import-data.md) quando o formato era Excel ou CSV com codificação; a concatenação do conteúdo de todos os BLOBs dentro de um contêiner não é permitida com esses formatos.  
  
-   Um URI SAS não contém o nome de um blob válido.  
  
**Resolução:** Revisite o módulo lançando a exceção. Verifique se o blob especificado existe no contêiner na conta de armazenamento e se as permissões permitem que você veja o blob. Verifique se a entrada está no formato **ContainerName/filename** se você tiver o Excel ou CSV com formatos de codificação. Verifique se um URI de SAS contém o nome de um blob válido.  
  
|Mensagens de exceção|  
|------------------------|  
|O blob de armazenamento do Azure está incorreto.|  
|O nome do blob de armazenamento{0}do Azure "" está incorreto|  
  

## <a name="error-0066"></a>Erro 0066  
 Ocorrerá uma exceção se um recurso não puder ser carregado em um blob do Azure.  
  
 Esse erro em Azure Machine Learning ocorrerá se um recurso não pôde ser carregado em um blob do Azure.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Ambos são salvos na mesma conta de armazenamento do Azure que a conta que contém o arquivo de entrada.  
  
**Resolução:** Revisite o módulo. Verifique se o nome da conta do Azure, a chave de armazenamento e o contêiner estão corretos e se a conta tem permissão para gravar no contêiner.  
  
|Mensagens de exceção|  
|------------------------|  
|Não foi possível carregar o recurso no armazenamento do Azure.|  
|O arquivo "{0}" não pôde ser carregado no armazenamento do Azure {1}como.|  
  

## <a name="error-0067"></a>Erro 0067  
 Ocorrerá uma exceção se um conjunto de linhas tiver um número diferente de colunas do que o esperado.  
  
 Esse erro em Azure Machine Learning ocorrerá se um conjunto de um DataSet tiver um número diferente de colunas do que o esperado.  Você receberá esse erro quando o número de colunas no conjunto de linhas for diferente do número de colunas que o módulo espera durante a execução.  
  
**Resolução:** Modifique o conjunto de dados de entrada ou os parâmetros.  
  
|Mensagens de exceção|  
|------------------------|  
|Número inesperado de colunas na DataTable.|  
|Esperava-{0}se "" colunas,{1}mas foram encontradas "" colunas.|  
  

## <a name="error-0068"></a>Erro 0068  
 Ocorrerá uma exceção se o script do hive especificado não estiver correto.  
  
 Esse erro em Azure Machine Learning ocorrerá se houver erros de sintaxe em um script de QL do hive ou se o intérprete do hive encontrar um erro ao executar a consulta ou o script.  
  
**Resolução:**

A mensagem de erro do hive normalmente é relatada de volta no log de erros para que você possa tomar medidas com base no erro específico. 

+ Abra o módulo e inspecione a consulta para obter erros.  
+ Verifique se a consulta funciona corretamente fora do Azure Machine Learning fazendo logon no console do hive do seu cluster Hadoop e executando a consulta.  
+ Tente inserir comentários no script do hive em uma linha separada, em vez de misturar instruções e comentários executáveis em uma única linha.  

### <a name="resources"></a>Recursos

Consulte os artigos a seguir para obter ajuda com consultas de Hive para aprendizado de máquina:

+ [Criar tabelas do hive e carregar dados do armazenamento de BLOBs do Azure](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Explorar dados em tabelas com consultas de Hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Criar recursos para dados em um cluster Hadoop usando consultas do hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Folha de consulta de usuários do hive para SQL (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Mensagens de exceção|  
|------------------------|  
|O script do hive está incorreto.|  
|O script {0} do hive não está correto.|  
  

## <a name="error-0069"></a>Erro 0069  
 Ocorrerá uma exceção se o script SQL especificado não estiver correto.  
  
 Esse erro em Azure Machine Learning ocorrerá se o script SQL especificado tiver problemas de sintaxe ou se as colunas ou a tabela especificada no script não forem válidas. 
 
 Você receberá esse erro se o mecanismo do SQL encontrar algum erro ao executar a consulta ou o script. A mensagem de erro do SQL normalmente é relatada de volta no log de erros para que você possa tomar medidas com base no erro específico.  
  
**Resolução:** Revisite o módulo e inspecione a consulta SQL para obter erros.  
  
 Verifique se a consulta funciona corretamente fora do Azure ML fazendo logon no servidor de banco de dados diretamente e executando a consulta.  
  
 Se houver uma mensagem gerada pelo SQL relatada pela exceção do módulo, execute uma ação com base no erro relatado. Por exemplo, as mensagens de erro às vezes incluem diretrizes específicas sobre o erro provável:
+ *Nenhuma coluna ou banco de dados ausente*, indicando que você pode ter digitado um nome de coluna errado. Se você tiver certeza de que o nome da coluna está correto, tente usar colchetes ou aspas para colocar o identificador de coluna.
+ *Erro lógico do SQL \<próximo à\>palavra-chave SQL*, indicando que você pode ter um erro de sintaxe antes da palavra-chave especificada

  
|Mensagens de exceção|  
|------------------------|  
|O script SQL está incorreto.|  
|A consulta SQL{0}"" não está correta.|  
|A consulta SQL{0}"" não está correta:{1}|  
  

## <a name="error-0070"></a>Erro 0070  
 Ocorre uma exceção ao tentar acessar uma tabela não existente do Azure.  
  
 Esse erro em Azure Machine Learning ocorre quando você tenta acessar uma tabela não existente do Azure. Você receberá esse erro se especificar uma tabela no armazenamento do Azure, que não existe durante a leitura ou gravação no armazenamento de tabelas do Azure. Isso pode acontecer se você digitar incorretamente o nome da tabela desejada ou se houver uma incompatibilidade entre o nome de destino e o tipo de armazenamento. Por exemplo, você pretende ler em uma tabela, mas inseriu o nome de um blob.  
  
**Resolução:** Revisite o módulo para verificar se o nome da tabela está correto.  
  
|Mensagens de exceção|  
|------------------------|  
|A tabela do Azure não existe.|  
|A tabela do{0}Azure "" não existe.|  
  
## <a name="error-0071"></a>Erro 0071  
 Ocorrerá uma exceção se as credenciais fornecidas estiverem incorretas.  
  
 Esse erro em Azure Machine Learning ocorrerá se as credenciais fornecidas estiverem incorretas.  
  
 Você também poderá receber esse erro se o módulo não puder se conectar a um cluster HDInsight.  
  
**Resolução:** Examine as entradas para o módulo e verifique o nome da conta e a senha.  
  
 Verifique os seguintes problemas que podem causar um erro:  
  
-   O esquema do conjunto de um não corresponde ao esquema da DataTable de destino.  
  
-   Nomes de coluna ausentes ou com ortografia incorreta  
  
-   Você está gravando em uma tabela com nomes de coluna com caracteres ilegais. Normalmente, você pode incluir esses nomes de coluna entre colchetes, mas se isso não funcionar, edite os nomes de coluna para usar apenas letras e sublinhados (_)  
  
-   As cadeias de caracteres que você está tentando gravar contêm aspas simples  
  
 Se você estiver tentando se conectar a um cluster HDInsight, verifique se o cluster de destino está acessível com as credenciais fornecidas.  
  
|Mensagens de exceção|  
|------------------------|  
|Credenciais incorretas são passadas.|  
|Nome de usuário{0}incorreto "" ou a senha é passada|  
  

## <a name="error-0072"></a>Erro 0072  
 Ocorre uma exceção no caso de tempo limite de conexão.  
  
 Esse erro no Azure Machine Learning ocorre quando uma conexão atinge o tempo limite. Você receberá esse erro se houver problemas de conectividade com a fonte de dados ou destino, como conectividade lenta com a Internet, ou se o DataSet for grande e/ou se a consulta SQL a ser lida nos dados executar um processamento complicado.  
  
**Resolução:** Determine se há problemas no momento com conexões lentas com o armazenamento do Azure ou com a Internet.  
  
|Mensagens de exceção|  
|------------------------|  
|O tempo limite da conexão ocorreu.|  
  

## <a name="error-0073"></a>Erro 0073  
 Ocorrerá uma exceção se ocorrer um erro durante a conversão de uma coluna em outro tipo.  
  
 Esse erro no Azure Machine Learning ocorre quando não é possível converter a coluna em outro tipo.  Você receberá esse erro se um módulo exigir um tipo específico e não for possível converter a coluna para o novo tipo.  
  
**Resolução:** Modifique o conjunto de dados de entrada para que a coluna possa ser convertida com base na exceção interna.  
  
|Mensagens de exceção|  
|------------------------|  
|Falha ao converter a coluna.|  
|Falha ao converter a coluna {0}em.|  
  

## <a name="error-0074"></a>Erro 0074  
 A exceção ocorre quando os [metadados de edição](edit-metadata.md) tentam converter uma coluna esparsa em categórico.  
  
 Esse erro no Azure Machine Learning ocorre quando os [metadados de edição](edit-metadata.md) tentam converter uma coluna esparsa em categórico.  Você receberá esse erro ao tentar converter colunas esparsas em categóricos com a opção **tornar categórica** .  O Azure Machine Learning não dá suporte a matrizes categóricos esparsas, portanto, o módulo falhará.  
  
 <!--**Resolution:**
 Make the column dense by using [Convert to Dataset](convert-to-dataset.md) first or do not convert the column to categorical.  -->
  
|Mensagens de exceção|  
|------------------------|  
|Colunas esparsas não podem ser convertidas em categóricos.|  
  

## <a name="error-0075"></a>Erro 0075  
Ocorrerá uma exceção quando uma função compartimentalização inválida for usada ao quantificar um conjunto de um DataSet.  
  
Esse erro em Azure Machine Learning ocorre quando você está tentando desagrupar dados usando um método sem suporte ou quando as combinações de parâmetros são inválidas.  
  
**Resolução:**

O tratamento de erros para esse evento foi introduzido em uma versão anterior do Azure Machine Learning que permitia mais personalização dos métodos compartimentalização. Atualmente, todos os métodos compartimentalização são baseados em uma seleção de uma lista suspensa, portanto, tecnicamente, não deve mais ser possível obter esse erro.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->
  
|Mensagens de exceção|  
|------------------------|  
|Função compartimentalização inválida usada.|  
  

## <a name="error-0077"></a>Erro 0077  
 Ocorrerá uma exceção quando o modo de gravação de arquivo de blob desconhecido for passado.  
  
 Esse erro em Azure Machine Learning ocorrerá se um argumento inválido for passado nas especificações de um destino de arquivo de BLOB ou origem.  
  
**Resolução:** Em quase todos os módulos que importam ou exportam dados de e para o armazenamento de BLOBs do Azure, os valores de parâmetros que controlam o modo de gravação são atribuídos usando uma lista suspensa; Portanto, não é possível passar um valor inválido, e esse erro não deve aparecer. Esse erro será preterido em uma versão posterior.  
  
|Mensagens de exceção|  
|------------------------|  
|Modo de gravações de blob sem suporte.|  
|Modo de gravações de blob sem suporte {0}:.|  
  

## <a name="error-0078"></a>Erro 0078  
 Ocorrerá uma exceção quando a opção HTTP para [importar dados](import-data.md) receber um código de status 3xx indicando redirecionamento.  
  
 Esse erro em Azure Machine Learning ocorre quando a opção HTTP para [importar dados](import-data.md) recebe um código de status 3xx (301, 302, 304, etc.) indicando o redirecionamento. Você receberá esse erro se tentar se conectar a uma origem HTTP que redireciona o navegador para outra página. Por motivos de segurança, o redirecionamento de sites não é permitido como fontes de dados para Azure Machine Learning.  
  
**Resolução:** Se o site for um site confiável, insira a URL redirecionada diretamente.  
  
|Mensagens de exceção|  
|------------------------|  
|Redirecionamento de http não permitido|  
  

## <a name="error-0079"></a>Erro 0079  
 Ocorrerá uma exceção se o nome do contêiner de armazenamento do Azure for especificado incorretamente.  
  
 Esse erro em Azure Machine Learning ocorrerá se o nome do contêiner de armazenamento do Azure for especificado incorretamente. Você receberá esse erro se não tiver especificado o contêiner e o nome do blob (arquivo) usando **a opção caminho para o blob começando com o contêiner** ao gravar no armazenamento de BLOBs do Azure.  
  
**Resolução:** Revisite o módulo [exportar dados](export-data.md) e verifique se o caminho especificado para o BLOB contém o contêiner e o nome do arquivo, no formato **contêiner/nome**de caminho.  
  
|Mensagens de exceção|  
|------------------------|  
|O nome do contêiner de armazenamento do Azure está incorreto.|  
|O nome do contêiner de armazenamento{0}do Azure "" está incorreto; era esperado um nome de contêiner do formato contêiner/BLOB.|  
  

## <a name="error-0080"></a>Erro 0080  
 A exceção ocorre quando a coluna com todos os valores ausentes não é permitida pelo módulo.  
  
 Esse erro no Azure Machine Learning é produzido quando uma ou mais colunas consumidas pelo módulo contêm todos os valores ausentes. Por exemplo, se um módulo estiver Computando estatísticas de agregação para cada coluna, ele não poderá operar em uma coluna que não contenha dados. Nesses casos, a execução do módulo é interrompida com essa exceção.  
  
**Resolução:** Reveja o conjunto de dados de entrada e remova todas as colunas que contêm todos os valores ausentes.  
  
|Mensagens de exceção|  
|------------------------|  
|Colunas com todos os valores ausentes não são permitidas.|  
|A {0} coluna tem todos os valores ausentes.|  
  

## <a name="error-0081"></a>Erro 0081  
 Ocorrerá uma exceção no módulo do PCA se o número de dimensões para reduzir for igual ao número de colunas de recursos no conjunto de dados de entrada, contendo pelo menos uma coluna de recursos esparsos.  
  
 Esse erro no Azure Machine Learning será produzido se as seguintes condições forem atendidas: (a) o conjunto de dados de entrada tem pelo menos uma coluna esparsa e (b) o número final de dimensões solicitadas é o mesmo que o número de dimensões de entrada.  
  
**Resolução:** Considere reduzir o número de dimensões na saída para ser menor que o número de dimensões na entrada. Isso é típico em aplicativos do PCA.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->
  
|Mensagens de exceção|  
|------------------------|  
|Para o conjunto de conteúdo que contém colunas de recursos esparsos, o número de dimensões para reduzir deve ser menor que o número de colunas de recursos.|  
 

## <a name="error-0082"></a>Erro 0082  
 Ocorre uma exceção quando um modelo não pode ser desserializado com êxito.  
  
 Esse erro no Azure Machine Learning ocorre quando um modelo ou uma transformação salva do Machine Learning não pode ser carregada por uma versão mais recente do tempo de execução Azure Machine Learning como resultado de uma alteração significativa.  
  
**Resolução:** O teste de treinamento que produziu o modelo ou a transformação deve ser executado novamente e o modelo ou a transformação deve ser salva novamente.  
  
|Mensagens de exceção|  
|------------------------|  
|Não foi possível desserializar o modelo porque ele provavelmente é serializado com um formato de serialização mais antigo. Readaptação e salve novamente o modelo.|  
  

## <a name="error-0083"></a>Erro 0083  
 Ocorrerá uma exceção se o conjunto de informações usado para treinamento não puder ser usado para o tipo concreto de aprendiz.  
  
 Esse erro no Azure Machine Learning é produzido quando o conjunto de informações é incompatível com o aprendiz sendo treinado. Por exemplo, o conjunto de resultados pode conter pelo menos um valor ausente em cada linha e, como resultado, todo o conjunto de resultados seria ignorado durante o treinamento. Em outros casos, alguns algoritmos de aprendizado de máquina, como a detecção de anomalias, não esperam que os rótulos estejam presentes e possam gerar essa exceção se os rótulos estiverem presentes no conjunto de informações.  
  
**Resolução:** Consulte a documentação do aprendiz que está sendo usado para verificar os requisitos para o conjunto de dados de entrada. Examine as colunas para ver se todas as colunas necessárias estão presentes.  
  
|Mensagens de exceção|  
|------------------------|  
|O DataSet usado para treinamento é inválido.|  
|{0}contém dados inválidos para treinamento.|  
|{0}contém dados inválidos para treinamento. Tipo de aprendiz: {1}.|  
  

## <a name="error-0084"></a>Erro 0084  
 Ocorrerá uma exceção quando as pontuações produzidas de um script R forem avaliadas. Não há suporte para isso no momento.  
  
 Esse erro em Azure Machine Learning ocorrerá se você tentar usar um dos módulos para avaliar um modelo com saída de um script R que contém pontuações.  
  
**Resolução:**
  
|Mensagens de exceção|  
|------------------------|  
|No momento, não há suporte para a avaliação de pontuações produzidas pelo R.|  
  

## <a name="error-0085"></a>Erro 0085  
 Ocorrerá uma exceção quando a avaliação de script falhar com um erro.  
  
 Esse erro em Azure Machine Learning ocorre quando você está executando um script personalizado que contém erros de sintaxe.  
  
**Resolução:** Examine seu código em um editor externo e verifique se há erros.  
  
|Mensagens de exceção|  
|------------------------|  
|Erro durante a avaliação do script.|  
|O seguinte erro ocorreu durante a avaliação do script, exiba o log de saída para obter mais informações:----------início {0} da mensagem de {1} erro do intérprete--------------------fim {0} da mensagem de erro do intérprete---- ------|  
  

## <a name="error-0086"></a>Erro 0086  
 Ocorre uma exceção quando uma transformação de contagem é inválida.  
  
 Esse erro no Azure Machine Learning ocorre quando você seleciona uma transformação com base em uma tabela de contagem, mas a transformação selecionada é incompatível com os dados atuais ou com a nova tabela de contagem.  
  
**Resolução:** O módulo oferece suporte ao salvamento das contagens e regras que compõem a transformação em dois formatos diferentes. Se você estiver mesclando tabelas de contagem, verifique se ambas as tabelas que você pretende mesclar usam o mesmo formato.  
  
Em geral, uma transformação baseada em contagem só pode ser aplicada a conjuntos de valores que têm o mesmo esquema que o conjunto de um no qual a transformação foi criada originalmente.  
  
 <!-- For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Mensagens de exceção|  
|------------------------|  
|Transformação de contagem inválida especificada.|  
|A transformação de contagem na porta de{0}entrada ' ' é inválida.|  
|A transformação de contagem na porta de{0}entrada ' ' não pode ser mesclada com a transformação de contagem{1}na porta de entrada ' '. Marque para verificar os metadados usados para a contagem de correspondências.|  
  

## <a name="error-0087"></a>Erro 0087  
 Ocorre uma exceção quando um tipo de tabela de contagem inválido é especificado para aprender com módulos de contagens.  
  
 Esse erro em Azure Machine Learning ocorre quando você tenta importar uma tabela de contagem existente, mas a tabela é incompatível com os dados atuais ou com a nova tabela de contagem.  
  
**Resolução:** Há formatos diferentes para salvar as contagens e as regras que compõem a transformação. Se você estiver mesclando tabelas de contagem, verifique se ambas usam o mesmo formato.  
  
 Em geral, uma transformação baseada em contagem só pode ser aplicada a conjuntos de valores que têm o mesmo esquema do conjunto de linhas no qual a transformação foi originalmente criada.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). -->
  

## <a name="error-0088"></a>Erro 0088  
 A exceção ocorre quando um tipo de contagem inválido é especificado para aprender com módulos de contagens.  
  
 Esse erro no Azure Machine Learning ocorre quando você tenta usar um método de contagem diferente do que o tem suporte para personalização com base em contagem.  
  
**Resolução:** Em geral, os métodos de contagem são escolhidos em uma lista suspensa, portanto, você não deve ver esse erro.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Mensagens de exceção|  
|------------------------|  
|Tipo de contagem inválido especificado.|  
|O tipo de contagem especificado{0}' ' não é um tipo de contagem válido.|  
  

## <a name="error-0089"></a>Erro 0089  
 A exceção ocorre quando o número especificado de classes é menor que o número real de classes em um conjunto de dados usado para contagem.  
  
 Esse erro em Azure Machine Learning ocorre quando você está criando uma tabela de contagem e a coluna de rótulo contém um número diferente de classes que você especificou nos parâmetros do módulo.  
  
**Resolução:** Verifique seu conjunto de e descubra exatamente quantos valores distintos (classes possíveis) existem na coluna rótulo. Ao criar a tabela de contagem, você deve especificar pelo menos esse número de classes.  
  
 A tabela de contagem não pode determinar automaticamente o número de classes disponíveis.  
  
 Quando você cria a tabela de contagem, não pode especificar 0 ou qualquer número que seja menor do que o número real de classes na coluna de rótulo.  
  
|Mensagens de exceção|  
|------------------------|  
|O número de classes está incorreto. Certifique-se de que o número de classes especificado no painel parâmetro seja maior ou igual ao número de classes na coluna rótulo.|  
|O número de classes especificado é '{0}', que não é maior que um valor de rótulo{1}' ' no conjunto de dados usado para contagem. Certifique-se de que o número de classes especificado no painel parâmetro seja maior ou igual ao número de classes na coluna rótulo.|  
  

## <a name="error-0090"></a>Erro 0090  
 Ocorrerá uma exceção quando a criação da tabela do hive falhar.  
  
 Esse erro no Azure Machine Learning ocorre quando você está usando [dados de exportação](export-data.md) ou outra opção para salvar dados em um cluster HDInsight e a tabela Hive especificada não pode ser criada.  
  
**Resolução:** Verifique o nome da conta de armazenamento do Azure associada ao cluster e verifique se você está usando a mesma conta nas propriedades do módulo.  
  
|Mensagens de exceção|  
|------------------------|  
|Não foi possível criar a tabela Hive. Para um cluster HDInsight, verifique se o nome da conta de armazenamento do Azure associado ao cluster é o mesmo que o passado por meio do parâmetro Module.|  
|Não foi possível criar{0}a tabela de Hive "". Para um cluster HDInsight, verifique se o nome da conta de armazenamento do Azure associado ao cluster é o mesmo que o passado por meio do parâmetro Module.|  
|Não foi possível criar{0}a tabela de Hive "". Para um cluster HDInsight, verifique se o nome da conta de armazenamento do Azure associado{1}ao cluster é "".|  
 

## <a name="error-0100"></a>Erro 0100  
 Ocorrerá uma exceção quando um idioma sem suporte for especificado para um módulo personalizado.  
  
 Esse erro no Azure Machine Learning ocorre ao criar um módulo personalizado e a propriedade Name do elemento **Language** em um arquivo de definição XML de módulo personalizado tem um valor inválido. Atualmente, o único valor válido para essa propriedade é `R`. Por exemplo:  
  
 `<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Resolução:** Verifique se a propriedade Name do elemento **Language** no arquivo de definição XML do módulo personalizado está definida como `R`. Salve o arquivo, atualize o pacote zip do módulo personalizado e tente adicionar o módulo personalizado novamente.  
  
|Mensagens de exceção|  
|------------------------|  
|Linguagem de módulo personalizada sem suporte especificada|  
  

## <a name="error-0101"></a>Erro 0101  
 Todas as IDs de porta e parâmetro devem ser exclusivas.  
  
 Esse erro em Azure Machine Learning ocorre quando uma ou mais portas ou parâmetros recebem o mesmo valor de ID em um arquivo de definição XML de módulo personalizado.  
  
**Resolução:** Verifique se os valores de ID em todas as portas e parâmetros são exclusivos. Salve o arquivo XML, atualize o pacote zip do módulo personalizado e tente adicionar o módulo personalizado novamente.  
  
|Mensagens de exceção|  
|------------------------|  
|Todas as IDs de porta e parâmetro para um módulo devem ser exclusivas|  
|O módulo{0}' ' tem IDs de porta/argumento duplicadas. Todas as IDs de porta/argumento devem ser exclusivas para um módulo.|  
  

## <a name="error-0102"></a>Erro 0102  
 Gerado quando um arquivo ZIP não pode ser extraído.  
  
 Esse erro no Azure Machine Learning ocorre quando você está importando um pacote compactado com a extensão. zip, mas o pacote não é um arquivo zip ou o arquivo não usa um formato zip com suporte.  
  
**Resolução:** Verifique se o arquivo selecionado é um arquivo. zip válido e se ele foi compactado usando um dos algoritmos de compactação com suporte.  
  
 Se você receber esse erro ao importar conjuntos de valores em formato compactado, verifique se todos os arquivos contidos usam um dos formatos de arquivo com suporte e se estão no formato Unicode.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->
  
 Tente adicionar novamente os arquivos desejados a uma nova pasta compactada compactada e tente adicionar o módulo personalizado novamente.  
  
|Mensagens de exceção|  
|------------------------|  
|O arquivo ZIP fornecido não está no formato correto|  


## <a name="error-0103"></a>Erro 0103  
 Gerado quando um arquivo ZIP não contém arquivos. xml  
  
 Esse erro em Azure Machine Learning ocorre quando o pacote zip do módulo personalizado não contém nenhum arquivo de definição de módulo (. xml). Esses arquivos precisam residir na raiz do pacote zip (por exemplo, não dentro de uma subpasta.)  
  
**Resolução:** Verifique se um ou mais arquivos de definição de módulo XML estão na pasta raiz do pacote zip, extraindo-os para uma pasta temporária na unidade de disco. Todos os arquivos XML devem estar diretamente na pasta para a qual você extraiu o pacote zip. Certifique-se de criar o pacote zip que você não selecionou uma pasta que contém arquivos XML para zip, pois isso criará uma subpasta dentro do pacote zip com o mesmo nome da pasta que você selecionou para zip.  
  
|Mensagens de exceção|  
|------------------------|  
|O arquivo ZIP fornecido não contém arquivos de definição de módulo (arquivos. xml)|  


## <a name="error-0104"></a>Erro 0104  
 Gerado quando um arquivo de definição de módulo faz referência a um script que não pode ser localizado  
  
 Esse erro no Azure Machine Learning é gerado quando um arquivo de definição XML de módulo personalizado faz referência a um arquivo de script no elemento de **linguagem** que não existe no pacote zip. O caminho do arquivo de script é definido na propriedade SourceFile do elemento **Language** . O caminho para o arquivo de origem é relativo à raiz do pacote zip (mesmo local que os arquivos de definição XML do módulo). Se o arquivo de script estiver em uma subpasta, o caminho relativo para o arquivo de script deverá ser especificado. Por exemplo, se todos os scripts foram armazenados em uma pasta myscripts dentro do pacote zip, o elemento **Language** teria que adicionar esse caminho à propriedade **SourceFile** , como abaixo. Por exemplo:  
  
 `<Language name="R" sourceFile="myScripts/CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Resolução:** Verifique se o valor da propriedade **SourceFile** no elemento **Language** da definição XML do módulo personalizado está correto e se o arquivo de origem existe no caminho relativo correto no pacote zip.  
  
|Mensagens de exceção|  
|------------------------|  
|O arquivo de script R referenciado não existe.|  
|O arquivo de script R{0}' ' referenciado não foi encontrado. Verifique se o caminho relativo para o arquivo está correto no local de definições.|  


## <a name="error-0105"></a>Erro 0105  
 Esse erro é exibido quando um arquivo de definição de módulo contém um tipo de parâmetro sem suporte  
  
 Esse erro no Azure Machine Learning é produzido quando você cria uma definição XML de módulo personalizado e o tipo de parâmetro ou argumento na definição não corresponde a um tipo com suporte.  
  
**Resolução:** Verifique se a propriedade Type de qualquer elemento **ARG** no arquivo de definição XML do módulo personalizado é um tipo com suporte.  
  
|Mensagens de exceção|  
|------------------------|  
|Tipo de parâmetro sem suporte.|  
|Tipo de parâmetro sem suporte '{0}' especificado.|  


## <a name="error-0106"></a>Erro 0106  
 Gerado quando um arquivo de definição de módulo define um tipo de entrada sem suporte  
  
 Esse erro no Azure Machine Learning é produzido quando o tipo de uma porta de entrada em uma definição XML de módulo personalizado não corresponde a um tipo com suporte.  
  
**Resolução:** Verifique se a propriedade Type de um elemento input no arquivo de definição XML do módulo personalizado é um tipo com suporte.  
  
|Mensagens de exceção|  
|------------------------|  
|Tipo de entrada sem suporte.|  
|Tipo de entrada sem suporte '{0}' especificado.|  


## <a name="error-0107"></a>Erro 0107  
 Gerado quando um arquivo de definição de módulo define um tipo de saída sem suporte  
  
 Esse erro no Azure Machine Learning é produzido quando o tipo de uma porta de saída em uma definição XML de módulo personalizado não corresponde a um tipo com suporte.  
  
**Resolução:** Verifique se a propriedade Type de um elemento Output no arquivo de definição XML do módulo personalizado é um tipo com suporte.  
  
|Mensagens de exceção|  
|------------------------|  
|Tipo de saída sem suporte.|  
|Tipo de saída '{0}' especificado sem suporte.|  


## <a name="error-0108"></a>Erro 0108  
 Gerado quando um arquivo de definição de módulo define mais portas de entrada ou saída do que o suportado  
  
 Esse erro no Azure Machine Learning é produzido quando muitas portas de entrada ou saída são definidas em uma definição XML de módulo personalizado.  
  
**Resolução:** Garante que o número máximo de portas de entrada e saída definidas na definição XML do módulo personalizado não exceda o número máximo de portas com suporte.  
  
|Mensagens de exceção|  
|------------------------|  
|Excedido o número de portas de entrada ou saída com suporte.|  
|O número de portas '{0}' com suporte foi excedido. O número máximo permitido de{0}' ' portas é{1}' '.| 

## <a name="error-0109"></a>Erro 0109  
 Gerado quando um arquivo de definição de módulo define um seletor de coluna incorretamente  
  
 Esse erro no Azure Machine Learning é produzido quando a sintaxe de um argumento de seletor de coluna contém um erro em uma definição XML de módulo personalizado.  
  
**Resolução:** Esse erro é produzido quando a sintaxe de um argumento de seletor de coluna contém um erro em uma definição XML de módulo personalizado.  
  
|Mensagens de exceção|  
|------------------------|  
|Sintaxe sem suporte para o seletor de coluna.|  
  

## <a name="error-0110"></a>Erro 0110  
 Gerado quando um arquivo de definição de módulo define um seletor de coluna que faz referência a uma ID de porta de entrada não existente  
  
 Esse erro em Azure Machine Learning é produzido quando a Propriedade portid dentro do elemento Properties de um ARG do tipo columnpickerfor não corresponde ao valor de ID de uma porta de entrada.  
  
**Resolução:** Certifique-se de que a propriedade portid corresponda ao valor de ID de uma porta de entrada definida na definição XML do módulo personalizado.  
  
|Mensagens de exceção|  
|------------------------|  
|O seletor de coluna faz referência a uma ID de porta de entrada não existente.|  
|O seletor de coluna faz referência a uma ID de{0}porta de entrada não existente ' '.|  
  

## <a name="error-0111"></a>Erro 0111  
 Gerado quando um arquivo de definição de módulo define uma propriedade inválida  
  
 Esse erro no Azure Machine Learning é produzido quando uma propriedade inválida é atribuída a um elemento na definição XML do módulo personalizado.  
  
**Resolução:** Verifique se a propriedade tem suporte no elemento de módulo personalizado.  
  
|Mensagens de exceção|  
|------------------------|  
|Definição de propriedade inválida.|  
|A definição de{0}Propriedade ' ' é inválida.|  
  

## <a name="error-0112"></a>Erro 0112  
 Gerado quando um arquivo de definição de módulo não pode ser analisado  
  
 Esse erro no Azure Machine Learning é produzido quando há um erro no formato XML que impede que a definição XML do módulo personalizado seja analisada como um arquivo XML válido.  
  
**Resolução:** Certifique-se de que cada elemento seja aberto e fechado corretamente. Certifique-se de que não haja erros na formatação XML.  
  
|Mensagens de exceção|  
|------------------------|  
|Não é possível analisar o arquivo de definição de módulo.|  
|Não é possível analisar o arquivo de{0}definição de módulo ' '.|  
  

## <a name="error-0113"></a>Erro 0113  
 Gerado quando um arquivo de definição de módulo contém erros.  
  
 Esse erro no Azure Machine Learning é produzido quando o arquivo de definição XML do módulo personalizado pode ser analisado, mas contém erros, como a definição de elementos sem suporte nos módulos personalizados.  
  
**Resolução:** Verifique se o arquivo de definição de módulo personalizado define elementos e propriedades que são suportados por módulos personalizados.  
  
|Mensagens de exceção|  
|------------------------|  
|O arquivo de definição de módulo contém erros.|  
|O arquivo de definição{0}de módulo ' ' contém erros.|  
|O arquivo de definição{0}de módulo ' ' contém erros. [https://doi.org/10.13012/J8PN93H8]({1})|  
  

## <a name="error-0114"></a>Erro 0114  
 Gerado durante a criação de um módulo personalizado falha.  
  
 Esse erro no Azure Machine Learning é produzido quando uma compilação de módulo personalizada falha. Isso ocorre quando um ou mais erros relacionados ao módulo personalizado são encontrados durante a adição do módulo personalizado. Os erros adicionais são relatados nessa mensagem de erro.  
  
**Resolução:** Resolva os erros relatados nesta mensagem de exceção.  
  
|Mensagens de exceção|  
|------------------------|  
|Falha ao criar o módulo personalizado.|  
|As compilações do módulo personalizado falharam com erro (s):{0}|  
  

## <a name="error-0115"></a>Erro 0115  
 Gerado quando um script padrão de módulo personalizado tem uma extensão sem suporte.  
  
 Esse erro em Azure Machine Learning ocorre quando você fornece um script para um módulo personalizado que usa uma extensão de nome de arquivo desconhecido.  
  
**Resolução:** Verifique o formato e a extensão de nome de arquivo de qualquer arquivo de script incluído no módulo personalizado.  
  
|Mensagens de exceção|  
|------------------------|  
|Extensão sem suporte para o script padrão.|  
|Extensão {0} de arquivo sem suporte para o script padrão.|  
  

## <a name="error-0121"></a>Erro 0121  
 Gerado quando há falha nas gravações do SQL porque a tabela não é gravável  
  
 Esse erro no Azure Machine Learning é produzido quando você está usando o módulo [exportar dados](export-data.md) para salvar os resultados em uma tabela em um banco de dado SQL, e a tabela não pode ser gravada. Normalmente, você verá esse erro se o módulo [exportar dados](export-data.md) estabelecer com êxito uma conexão com a instância de SQL Server, mas, em seguida, não puder gravar o conteúdo do conjunto de dados ml do Azure na tabela.  
  
**Resolução:**
 - Abra o painel Propriedades do módulo [exportar dados](export-data.md) e verifique se os nomes de tabela e de banco de dado foram inseridos corretamente. 
 - Revise o esquema do conjunto de dados que você está exportando e verifique se os dados são compatíveis com a tabela de destino.
 - Verifique se a entrada SQL associada ao nome de usuário e à senha tem permissões para gravar na tabela. 
 - Se a exceção contiver informações de erro adicionais de SQL Server, use essas informações para fazer correções.  
  
|Mensagens de exceção|  
|------------------------|  
|Conectado ao servidor, não é possível gravar na tabela.|  
|Não é possível gravar na tabela SQL:{0}|  


## <a name="error-0122"></a>Erro 0122  
 Ocorrerá uma exceção se várias colunas de peso forem especificadas e apenas uma for permitida.  
  
 Esse erro em Azure Machine Learning ocorre quando muitas colunas foram selecionadas como colunas de peso.  
  
**Resolução:** Examine o conjunto de dados de entrada e seus metadados. Certifique-se de que apenas uma coluna contenha pesos.  
  
|Mensagens de exceção|  
|------------------------|  
|Várias colunas de peso são especificadas.|  


## <a name="error-0123"></a>Erro 0123  
 Ocorrerá uma exceção se a coluna de vetores for especificada para rotular a coluna.  
  
 Esse erro em Azure Machine Learning ocorrerá se você usar um vetor como a coluna de rótulo.  
  
**Resolução:** Altere o formato de dados da coluna, se necessário, ou escolha uma coluna diferente.  
  
|Mensagens de exceção|  
|------------------------|  
|A coluna de vetores é especificada como coluna de rótulo.|  


## <a name="error-0124"></a>Erro 0124  
 Ocorrerá uma exceção se colunas não numéricas forem especificadas como a coluna de peso.  
  
**Resolução:**
  
|Mensagens de exceção|  
|------------------------|  
|A coluna não numérica é especificada como a coluna de peso.|  
  


## <a name="error-0125"></a>Erro 0125  
 Gerado quando o esquema para vários conjuntos de valores não corresponde.  
  
**Resolução:**
  
|Mensagens de exceção|  
|------------------------|  
|O esquema do conjunto de conjuntos não corresponde.|  


## <a name="error-0126"></a>Erro 0126  
 Ocorrerá uma exceção se o usuário especificar um domínio do SQL que não tem suporte no Azure ML.  
  
 Esse erro é produzido quando o usuário especifica um domínio do SQL que não tem suporte no Azure Machine Learning. Você receberá esse erro se estiver tentando se conectar a um servidor de banco de dados em um domínio que não está na lista de permissões. Atualmente, os domínios SQL permitidos são: ". database.windows.net", ". cloudapp.net" ou ". database.secure.windows.net". Ou seja, o servidor deve ser um SQL Server do Azure ou um servidor em uma máquina virtual no Azure.  
  
**Resolução:** Revisite o módulo. Verifique se o servidor do banco de dados SQL pertence a um dos domínios aceitos:  
  
-   .database.windows.net  
  
-   .cloudapp.net  
  
-   .database.secure.windows.net  
  
|Mensagens de exceção|  
|------------------------|  
|Domínio SQL sem suporte.|  
|No momento, {0} o domínio SQL não tem suporte no Azure ml|  
  

## <a name="error-0127"></a>Erro 0127  
 O tamanho do pixel da imagem excede o limite permitido  
  
 Esse erro ocorrerá se você estiver lendo imagens de um conjunto de uma imagem para classificação e as imagens forem maiores do que o modelo pode manipular.  
  
 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  
  
-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->
  
|Mensagens de exceção|  
|------------------------|  
|O tamanho do pixel da imagem excede o limite permitido.|  
|O tamanho do pixel da imagem no{0}arquivo ' ' excede o limite{1}permitido: ' '|  


## <a name="error-0128"></a>Erro 0128  
 O número de probabilidades condicionais para colunas categóricas excede o limite.  
  
**Resolução:**
  
|Mensagens de exceção|  
|------------------------|  
|O número de probabilidades condicionais para colunas categóricas excede o limite.|  
|O número de probabilidades condicionais para colunas categóricas excede o limite. As colunas{0}' ' e{1}' ' são o par problemático.|  


## <a name="error-0129"></a>Erro 0129  
 O número de colunas no conjunto de tempo excede o limite permitido.  
  
**Resolução:**
  
|Mensagens de exceção|  
|------------------------|  
|O número de colunas no conjunto de tempo excede o limite permitido.|  
|O número de colunas no conjunto de um{0}' ' excede permitido. '|  
|O número de colunas no conjunto de um{0}' ' excede o limite permitido{1}de ' '. '|  
|O número de colunas no conjunto de um{0}' ' excede o{1}limite ' ' permitido{2}de ' '. '|  
## <a name="error-0130"></a>Erro 0130  
 Ocorrerá uma exceção quando todas as linhas no conjunto de registros de treinamento contiverem valores ausentes.  
  
 Isso ocorre quando alguma coluna no conjunto de e de treinamento está vazia.  
  
**Resolução:** Use o módulo [limpar dados ausentes](clean-missing-data.md) para remover colunas com todos os valores ausentes.  
  
|Mensagens de exceção|  
|------------------------|  
|Todas as linhas no conjunto de registros de treinamento contêm valores ausentes.  Considere usar o módulo limpar dados ausentes para remover valores ausentes.|  
 

## <a name="error-0131"></a>Erro 0131  
 Ocorrerá uma exceção se um ou mais conjuntos de arquivos em um arquivo zip não puderem ser descompactados e registrados corretamente  
  
 Esse erro é produzido quando um ou mais conjuntos de arquivos em um arquivo zip não são descompactados e lidos corretamente. Você receberá esse erro se o desempacotamento falhar porque o próprio arquivo zip ou um dos arquivos contidos nele está corrompido ou há um erro do sistema ao tentar desempacotar e expandir um arquivo.  
  
**Resolução:** Use os detalhes fornecidos na mensagem de erro para determinar como proceder.  
  
|Mensagens de exceção|  
|------------------------|  
|Falha ao carregar conjuntos de itens compactados|  
|Falha no {0} conjunto de mensagens compactado com a seguinte mensagem:{1}|  
|Falha no {0} conjunto de mensagens {1} compactado com uma exceção com a mensagem:{2}|  
  

## <a name="error-0132"></a>Erro 0132  
 Nenhum nome de arquivo foi especificado para desempacotamento; vários arquivos foram encontrados no arquivo zip.  
  
 Esse erro é produzido quando nenhum nome de arquivo foi especificado para desempacotamento; vários arquivos foram encontrados no arquivo zip. Você receberá esse erro se o arquivo. zip contiver mais de um arquivo compactado, mas você não especificou um arquivo para extração na caixa de texto do conjunto de informações **a ser** desempacotado, no painel de **Propriedades** do módulo. Atualmente, apenas um arquivo pode ser extraído cada vez que o módulo é executado.  
  
**Resolução:** A mensagem de erro fornece uma lista dos arquivos encontrados no arquivo. zip. Copie o nome do arquivo desejado e cole-o na caixa de texto conjunto de um **para** desempacotar.  
  
|Mensagens de exceção|  
|------------------------|  
|O arquivo zip contém vários arquivos; Você deve especificar o arquivo a ser expandido.|  
|O arquivo contém mais de um arquivo. Especifique o arquivo a ser expandido. Os seguintes arquivos foram encontrados:{0}|  
  

## <a name="error-0133"></a>Erro 0133  
 O arquivo especificado não foi encontrado no arquivo zip  
  
 Esse erro é produzido quando o nome de arquivo inserido no campo conjunto de campos **para** desempacotar do painel de **Propriedades** não corresponde ao nome de qualquer arquivo encontrado no arquivo. zip. As causas mais comuns desse erro são um erro de digitação ou a pesquisa do arquivo morto incorreto para expandir o arquivo.  
  
**Resolução:** Revisite o módulo. Se o nome do arquivo que você pretende descompactar aparecer na lista de arquivos encontrados, copie o nome do arquivo e cole-o na caixa de propriedade conjunto de propriedades **para** desempacotar. Se você não vir o nome de arquivo desejado na lista, verifique se você tem o arquivo. zip correto e o nome correto para o arquivo desejado.  
  
|Mensagens de exceção|  
|------------------------|  
|O arquivo especificado não foi encontrado em int o arquivo zip.|  
|O arquivo especificado não foi encontrado. Encontrados os seguintes arquivos:{0}|  
  

## <a name="error-0134"></a>Erro 0134
Ocorre uma exceção quando a coluna de rótulo está ausente ou tem número insuficiente de linhas rotuladas.  
  
Esse erro ocorre quando o módulo requer uma coluna de rótulo, mas você não incluiu uma na seleção de coluna ou a coluna de rótulo está sem muitos valores.

Esse erro também pode ocorrer quando uma operação anterior altera o conjunto de registros, de modo que as linhas insuficientes estejam disponíveis para uma operação downstream. Por exemplo, suponha que você use uma expressão no módulo **partição e exemplo** para dividir um conjunto de um DataSet por valores. Se nenhuma correspondência for encontrada para sua expressão, um dos conjuntos de resultados resultante da partição estaria vazio.

Resolução: 

 Se você incluir uma coluna de rótulo na seleção de coluna, mas ela não for reconhecida, use o módulo [Editar metadados](edit-metadata.md) para marcá-la como uma coluna de rótulo.
  
  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->Em seguida, você pode usar o módulo [limpar dados ausentes](clean-missing-data.md) para remover linhas com valores ausentes na coluna rótulo. 

 Verifique os conjuntos de dados de entrada para certificar-se de que eles contêm um dado válido e linhas suficientes para atender aos requisitos da operação. Muitos algoritmos gerarão uma mensagem de erro se exigirem um número mínimo de linhas de dados, mas os dados contiverem apenas algumas linhas ou apenas um cabeçalho.
  
|Mensagens de exceção|
|------------------------|
|Ocorre uma exceção quando a coluna de rótulo está ausente ou tem número insuficiente de linhas rotuladas.|  
|Ocorre uma exceção quando a coluna de rótulo está ausente ou {0} tem menos de linhas rotuladas|  
  

## <a name="error-0135"></a>Erro 0135  
 Há suporte apenas para o cluster baseado em centróide.  
  
**Resolução:** Você poderá encontrar essa mensagem de erro se tiver tentado avaliar um modelo de clustering baseado em um algoritmo de clustering personalizado que não usa centróides para inicializar o cluster.  
  
  <!--You can use [Evaluate Model](evaluate-model.md) to evaluate clustering models that are based on the  [K-Means Clustering](k-means-clustering.md) module. For custom algorithms, use the [Execute R Script](execute-r-script.md) module to create a custom evaluation script.  -->
  
|Mensagens de exceção|  
|------------------------|  
|Há suporte apenas para o cluster baseado em centróide.|  
  

## <a name="error-0136"></a>Erro 0136  
 Nenhum nome de arquivo foi retornado; Não é possível processar o arquivo como resultado.  
  
**Resolução:**
  
|Mensagens de exceção|  
|------------------------|  
|Nenhum nome de arquivo foi retornado; Não é possível processar o arquivo como resultado.|  
  

## <a name="error-0137"></a>Erro 0137  
 O SDK do armazenamento do Azure encontrou um erro ao converter as propriedades da tabela e do conjunto de linhas durante leitura ou gravação.  
  
**Resolução:**
  
|Mensagens de exceção|  
|------------------------|  
|Erro de conversão entre a propriedade de armazenamento de tabela do Azure e a coluna de conjunto de de|  
|Erro de conversão entre a propriedade de armazenamento de tabela do Azure e a coluna de conjunto de de Informações adicionais:{0}|  

## <a name="error-0138"></a>Erro 0138  
 A memória foi esgotada, não é possível concluir a execução do módulo. A redução do conjunto de os pode ajudar a aliviar o problema.  
  
 Esse erro ocorre quando o módulo que está sendo executado requer mais memória do que está disponível no contêiner do Azure. Isso pode acontecer se você estiver trabalhando com um grande conjunto de grandes e a operação atual não couber na memória.  
  
**Resolução:** Se você estiver tentando ler um grande conjunto de grandes e a operação não puder ser concluída, a redução da resolução do conjunto de um pode ajudar.  
  
  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  
  
 Sometimes transient loads can lead to this error. Machine support also changes over time. 
  
 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->
  
|Mensagens de exceção|  
|------------------------|  
|A memória foi esgotada, não é possível concluir a execução do módulo.|  
  

## <a name="error-0139"></a>Erro 0139  
 Ocorre uma exceção quando não é possível converter uma coluna em outro tipo.  
  
 Esse erro no Azure Machine Learning ocorre quando você tenta converter uma coluna em um tipo de dados diferente, mas esse tipo não é suportado pela operação atual ou pelo módulo.  
  
 O erro também pode aparecer quando um módulo tenta converter dados implicitamente para atender aos requisitos do módulo atual, mas a conversão não é possível.  
  
**Resolução:**

1. Examine os dados de entrada e determine o tipo de dados exato da coluna que você deseja usar e o tipo de dados da coluna que está produzindo o erro. Às vezes, você pode achar que o tipo de dados está correto, mas descobrir que uma operação upstream modificou o tipo de dados ou o uso de uma coluna. Use o módulo [Editar metadados](edit-metadata.md) para redefinir os metadados da coluna para seu estado original. 
2. Examine a página de ajuda do módulo para verificar os requisitos para a operação especificada. Determine quais tipos de dados têm suporte no módulo atual e qual intervalo de valores tem suporte. 
 <!--3. If values need to be truncated, rounded, or outliers removed, use the [Apply Math Operation](apply-math-operation.md) or [Clip Values](clip-values.md) modules to make corrections.-->
4. Considere se é possível converter ou fazer a conversão da coluna em um tipo de dados diferente. Todos os módulos a seguir fornecem flexibilidade e capacidade consideráveis para modificar dados: 
 <!--
   + [Apply SQL Transformation](apply-sql-transformation.md)
   + [Execute R Script](execute-r-script.md)
-->   
   + [Execute o script Python](execute-python-script.md).  

> [!NOTE]
> Ainda não está funcionando? Considere fornecer comentários adicionais sobre o problema para nos ajudar a desenvolver melhores diretrizes de solução de problemas. Basta enviar comentários nesta página e fornecer o nome do módulo que gerou o erro e a conversão de tipo de dados que falhou.
  
|Mensagens de exceção|  
|------------------------|  
|Conversão não permitida.|  
|Não foi possível converter {0}:.|  
|Não foi possível converter {0}:, na {1}linha.|  
|Não foi possível converter a coluna {0} do tipo em coluna {1} do tipo {2}na linha.|  
|Não foi possível converter a{2}coluna "" {0} do tipo em uma {1} coluna do {3}tipo na linha.|  
|Não foi possível converter a{2}coluna "" {0} do tipo para{3}a coluna " {1} " do {4}tipo na linha.| 

## <a name="error-0140"></a>Erro 0140  
 Ocorrerá uma exceção se o argumento de conjunto de colunas aprovado não contiver outras colunas, exceto a coluna de rótulo.  
  
 Esse erro ocorre se você conectou um conjunto de um DataSet a um módulo que requer várias colunas, incluindo recursos, mas você forneceu apenas a coluna rótulo.  
  
**Resolução:** Escolha pelo menos uma coluna de recurso para incluir no conjunto de recursos.  
  
|Mensagens de exceção|  
|------------------------|  
|O conjunto de colunas especificado não contém outras colunas, exceto a coluna de rótulo.|  
  

## <a name="error-0141"></a>Erro 0141  
 Ocorrerá uma exceção se o número das colunas numéricas selecionadas e os valores exclusivos nas colunas categóricas e de cadeia de caracteres forem muito pequenos.  
  
 Esse erro em Azure Machine Learning ocorre quando não há valores exclusivos suficientes na coluna selecionada para executar a operação.  
  
**Resolução:** Algumas operações executam operações estatísticas em colunas de recursos e categóricas e, se não houver valores suficientes, a operação poderá falhar ou retornar um resultado inválido. Verifique o conjunto de recursos para ver quantos valores existem nas colunas de recurso e rótulo e determine se a operação que você está tentando executar é estatisticamente válida.  
  
 Se o conjunto de dados de origem for válido, você também poderá verificar se alguma operação de manipulação ou metadados upstream alterou os dados e removeu alguns valores.  
  
 Se as operações de upstream incluírem divisão, amostragem ou reamostragem, verifique se as saídas contêm o número esperado de linhas e valores.  
  
|Mensagens de exceção|  
|------------------------|  
|O número de colunas numéricas selecionadas e valores exclusivos nas colunas categóricas e de cadeia de caracteres é muito pequeno.|  
|O número total de colunas numéricas selecionadas e valores exclusivos nas colunas categóricas e de cadeia de caracteres {0}(no momento) devem ser pelo menos{1}|  
  

## <a name="error-0142"></a>Erro 0142  
 Ocorrerá uma exceção quando o sistema não puder carregar o certificado para autenticar.  
  
**Resolução:**
  
|Mensagens de exceção|  
|------------------------|  
|Não é possível carregar o certificado.|  
|Não é {0} possível carregar o certificado. Sua impressão digital {1}é.|  
  

## <a name="error-0143"></a>Erro 0143  
 Não é possível analisar a URL fornecida pelo usuário que deveria ser do GitHub.  
  
 Esse erro em Azure Machine Learning ocorre quando você especifica uma URL inválida e o módulo requer uma URL do GitHub válida.  
  
**Resolução:** Verifique se a URL refere-se a um repositório GitHub válido. Não há suporte para outros tipos de site.  
  
|Mensagens de exceção|  
|------------------------|  
|A URL não é de github.com.|  
|A URL não é de github.com:{0}|  

## <a name="error-0144"></a>Erro 0144  
 A URL do GitHub fornecida pelo usuário está sem a parte esperada.  
  
 Esse erro em Azure Machine Learning ocorre quando você especifica uma origem de arquivo do GitHub usando um formato de URL inválido.  
  
**Resolução:** Verifique se a URL do repositório do GitHub é válida e termina com \blob\ ou \tree\\.  
  
|Mensagens de exceção|  
|------------------------|  
|Não é possível analisar a URL do GitHub.|  
|Não é possível analisar a URL do GitHub\\(esperando ' \blob\\' ou ' \tree ' após o nome do repositório):{0}|  

## <a name="error-0145"></a>Erro 0145  
 Não é possível criar o diretório de replicação por algum motivo.  
  
 Esse erro em Azure Machine Learning ocorre quando o módulo falha ao criar o diretório especificado.  
  
**Resolução:**
  
|Mensagens de exceção|  
|------------------------|  
|Não é possível criar diretório de replicação.|  
  

## <a name="error-0146"></a>Erro 0146  
 Quando os arquivos de usuário são descompactados no diretório local, o caminho combinado pode ser muito longo.  
  
 Esse erro no Azure Machine Learning ocorre quando você está extraindo arquivos, mas alguns nomes de arquivo são muito longos quando descompactados.  
  
**Resolução:** Edite os nomes de arquivo de modo que o caminho combinado e o nome do arquivo não tenham mais de 248 caracteres.  
  
|Mensagens de exceção|  
|------------------------|  
|O caminho de replicação tem mais de 248 caracteres, diminua o nome ou o caminho do script.|  

## <a name="error-0147"></a>Erro 0147  
 Não foi possível baixar itens do GitHub por algum motivo  
  
 Esse erro no Azure Machine Learning ocorre quando você não pode ler ou baixar os arquivos especificados do GitHub.  
  
**Resolução:** O problema pode ser temporário; Você pode tentar acessar os arquivos em outro momento. Ou verifique se você tem as permissões necessárias e se a origem é válida.  
  
|Mensagens de exceção|  
|------------------------|  
|Erro de acesso do GitHub.|  
|Erro de acesso do GitHub. [https://doi.org/10.13012/J8PN93H8]({0})|  
  

## <a name="error-0148"></a>Erro 0148  
 Problemas de acesso não autorizado ao extrair dados ou criar diretório.  
  
 Esse erro no Azure Machine Learning ocorre quando você está tentando criar um diretório ou ler dados do armazenamento, mas não tem as permissões necessárias.  
  
**Resolução:**
  
|Mensagens de exceção|  
|------------------------|  
|Exceção de acesso não autorizado ao extrair dados.|  
  

## <a name="error-0149"></a>Erro 0149  
 O arquivo de usuário não existe no pacote do GitHub.  
  
 Esse erro no Azure Machine Learning ocorre quando o arquivo especificado não pode ser encontrado.  
  
Resolução: 
  
|Mensagens de exceção|  
|------------------------|  
|O arquivo GitHub não foi encontrado.|  
|O arquivo GitHub não foi encontrado.:{0}|  
  

## <a name="error-0150"></a>Erro 0150  
 Os scripts provenientes do pacote do usuário não puderam ser descompactados, provavelmente devido a uma colisão com arquivos do GitHub.  
  
 Esse erro em Azure Machine Learning ocorre quando um script não pode ser extraído, normalmente quando há um arquivo existente com o mesmo nome.  
  
Resolução:
  
|Mensagens de exceção|  
|------------------------|  
|Não é possível descompactar o pacote; possível colisão de nomes com arquivos GitHub.|  
  

## <a name="error-0151"></a>Erro 0151  
 Erro ao gravar no armazenamento em nuvem. Verifique a URL.  
  
 Esse erro em Azure Machine Learning ocorre quando o módulo tenta gravar dados no armazenamento em nuvem, mas a URL está indisponível ou é inválida.  
  
Resolução: Verifique a URL e verifique se ela é gravável.  
  
|Mensagens de exceção|  
|------------------------|  
|Erro ao gravar no armazenamento em nuvem (possivelmente uma URL inadequada).|  
|Erro ao gravar no armazenamento em {0}nuvem:. Verifique a URL.|  
  
## <a name="error-0152"></a>Erro 0152  
 O tipo de nuvem do Azure foi especificado incorretamente no contexto do módulo.  
  
|Mensagens de exceção|  
|------------------------|  
|Tipo de nuvem do Azure inadequado|  
|Tipo de nuvem do Azure inadequado:{0}|  
  
## <a name="error-0153"></a>Erro 0153  
 O ponto de extremidade de armazenamento especificado é inválido.  
  
|Mensagens de exceção|  
|------------------------|  
|Tipo de nuvem do Azure inadequado|  
|Ponto de extremidade de armazenamento inadequado:{0}|  

## <a name="error-0154"></a>Erro 0154  
 O nome do servidor especificado não pôde ser resolvido  
  
|Mensagens de exceção|  
|------------------------|  
|O nome do servidor especificado não pôde ser resolvido|  
|O Server {0}. Documents.Azure.com especificado não pôde ser resolvido|

## <a name="error-0155"></a>Erro 0155  
 O cliente DocDb lançou uma exceção  
  
|Mensagens de exceção|  
|------------------------|  
|O cliente DocDb lançou uma exceção|  
|Cliente DocDb:{0}|

## <a name="error-0156"></a>Erro 0156  
 Resposta inadequada para o servidor HCatalog.  
  
|Mensagens de exceção|  
|------------------------|  
|Resposta inadequada para o servidor HCatalog. Verifique se todos os serviços estão em execução.|  
|Resposta inadequada para o servidor HCatalog. Verifique se todos os serviços estão em execução. Detalhes do erro: {0}|

## <a name="error-0157"></a>Erro 0157  
 Erro ao ler de Azure Cosmos DB devido a esquemas de documento inconsistentes ou diferentes. O leitor requer que todos os documentos tenham o mesmo esquema.  
  
|Mensagens de exceção|  
|------------------------|  
|Documentos detectados com esquemas diferentes. Verifique se todos os documentos têm o mesmo esquema|

## <a name="error-1000"></a>Erro 1000  
Exceção de biblioteca interna.  
  
Esse erro é fornecido para capturar erros de mecanismo interno de outra forma sem tratamento. Portanto, a causa desse erro pode ser diferente dependendo do módulo que gerou o erro.  
  
Para obter mais ajuda, recomendamos que você poste a mensagem detalhada que acompanha o erro no fórum de Azure Machine Learning, junto com uma descrição do cenário, incluindo os dados usados como entradas. Esses comentários nos ajudarão a priorizar erros e identificar os problemas mais importantes para um trabalho adicional.  
  
|Mensagens de exceção|  
|------------------------|  
|Exceção de biblioteca.|  
|Exceção de biblioteca:{0}|  
|{0}exceção de biblioteca:{1}|  
