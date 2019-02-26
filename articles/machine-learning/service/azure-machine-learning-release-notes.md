---
title: O que há de novo na versão?
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre as atualizações mais recentes para o serviço Azure Machine Learning e o machine learning e SDKs de Python de preparação de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: hning86
ms.author: haining
ms.reviewer: j-martens
ms.date: 2/25/2019
ms.custom: seodec18
ms.openlocfilehash: 899e33db242a34d09df6109c2e195bdff9c65262
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56823608"
---
# <a name="azure-machine-learning-service-release-notes"></a>Notas de versão de serviço do Azure Machine Learning

Neste artigo, saiba mais sobre as versões de serviço do Azure Machine Learning.  Para obter uma descrição completa de cada SDK, visite os documentos de referência para:
+ O Azure Machine Learning [ **principal SDK para Python**](https://aka.ms/aml-sdk)
+ O Azure Machine Learning [ **SDK de preparação de dados**](https://aka.ms/data-prep-sdk)

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK for Python v1.0.17

+ **Novos recursos**

  + O Azure Machine Learning SDK agora suporta uma [ `Chainer` ](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) classe estimator para simplificar o processo de preparação e implementação de um modelo usando código personalizado do Chainer.

### <a name="azure-portal"></a>Portal do Azure
+ **Novos recursos**
    + Experiência de editor para relatórios de tabela nova arrastar e soltar. Os utilizadores podem arrastar uma coluna do poço para a área de tabela em que será apresentada uma pré-visualização da tabela. As colunas podem ser reorganizadas.
    + Novo botão de execução para execuções submetido com ScriptRunConfig. Os utilizadores podem executar novamente existente ser executado num cluster de cálculo remoto de sua preferência.
    + Novo Visualizador de arquivos de registos
    + Links para experimentação é executada, computação, modelos, imagens e implantações do separador de atividades

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Azure Machine Learning SDK for Python v1.0.15

+ **Novos recursos**
  + O Azure Machine Learning Pipelines adicionado AzureBatchStep ([bloco de notas](https://aka.ms/pl-azbatch)), HyperDriveStep ([bloco de notas](https://aka.ms/pl-hyperdrive)) e a funcionalidade de agendamento baseados no tempo ([bloco de notas](https://aka.ms/pl-schedule)).
  +  DataTranferStep atualizado para trabalhar com o servidor SQL do Azure e base de dados do Azure para PostgreSQL ([bloco de notas](https://aka.ms/pl-data-trans)).

+ **Alterações**
  + Preterida `PublishedPipeline.get_published_pipeline` aposentado `PublishedPipeline.get`.
  + Preterida `Schedule.get_schedule` aposentado `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>SDK v1.0.12 de preparação de dados do Azure Machine Learning

+ **Novos recursos**
  + Dados Prep agora suporta leitura a partir de uma base de dados SQL do Azure com o arquivo de dados.
 
+ **Alterações**
  + Melhoraram significativamente o desempenho de memória de determinadas operações nos dados de grande dimensão.
  + `read_pandas_dataframe()` agora requer `temp_folder` seja especificado.
  + O `name` propriedade no `ColumnProfile` foi preterido – utilizar `column_name` em vez disso.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK for Python v1.0.10

+ **Alterações**: 
  + SDK do Azure ML já não tem a dependência de pacotes de cli do azure. Especificamente, as dependências do azure-cli-core e do azure-cli-perfil foram removidas do azureml-core. Estes são o utilizador que afetem as alterações:
    + Se estiver a executar "início de sessão az" e, em seguida, utilizar o sdk do azureml, o SDK fará o browser ou o início de sessão de código de dispositivo mais uma vez. Ele não usa nenhum Estado de credenciais criado por "início de sessão az".
    + Para a autenticação de CLI do Azure, como o uso de "início de sessão az" usar _azureml.core.authentication.AzureCliAuthentication_ classe. Para a autenticação de CLI do Azure, fazer _cli do azure a instalação do pip_ no ambiente de Python em que instalou o sdk do azureml.
    + Se estiver fazendo "az login" com um principal de serviço para a automatização, recomendamos que utilize _azureml.core.authentication.ServicePrincipalAuthentication_ de classe, como o sdk do azureml não utiliza o estado de credenciais criado pela CLI do azure. 

+ **Correções de erros**: Esta versão contém pequenas correções de erros

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>SDK v1.0.8 de preparação de dados do Azure Machine Learning

+ **Correções de erros**
  + Melhoraram significativamente o desempenho de obter os perfis de dados.
  + Foram corrigidos erros secundários relacionadas com relatórios de erro.
  
### <a name="azure-portal-new-features"></a>Portal do Azure: novos recursos
+ Novo arrastar e soltar a experiência de criação de gráficos para relatórios. Os utilizadores podem arrastar uma coluna ou o atributo do poço para a área de gráfico em que o sistema irá selecionar automaticamente um tipo de gráfico adequada para o utilizador com base no tipo de dados. Os utilizadores podem alterar o tipo de gráfico para outros tipos de aplicável ou adicionar atributos adicionais.

    Tipos de gráfico com suporte:
    - Gráfico de Linhas
    - Histograma
    - Gráfico de barras empilhadas
    - Gráfico de caixa
    - Gráfico de Dispersão
    - Gráfico de bolhas
+ Agora o portal do gera dinamicamente relatórios para experimentações. Quando um utilizador submete uma execução de uma experimentação, será automaticamente gerado um relatório com métricas com sessão iniciada e gráficos para permitir que a comparação entre diferentes execuções. 

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning SDK for Python v1.0.8

+ **Correções de erros**: Esta versão contém pequenas correções de erros

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>SDK v1.0.7 de preparação de dados do Azure Machine Learning

+ **Novos recursos**
  + Melhorias do arquivo de dados (documentados em [arquivo de dados procedimentos-to-guia](https://github.com/Microsoft/AMLDataPrepDocs/tree/master/how-to-guides/datastore.ipynb))
    + Foi adicionada a capacidade para ler e gravar a partilha de ficheiros do Azure e arquivos de dados do ADLS na vertical.
    + Ao usar arquivos de dados, preparação de dados suporta agora a utilizar a autenticação principal de serviço em vez da autenticação interativa.
    + Foi adicionado suporte para urls de wasb/wasbs.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>SDK v1.0.6 de preparação de dados do Azure Machine Learning

+ **Correções de erros**
  + Foi corrigido o erro com a leitura de contentores de Blobs do Azure legíveis públicos no Spark

## <a name="2018-12-20"></a>2018-12-20 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK for Python v1.0.6
+ **Correções de erros**: Esta versão contém pequenas correções de erros

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>A v1.0.4 do SDK de preparação de dados do Azure Machine Learning

+ **Novos recursos**
  + `to_bool` função agora permite que os valores sem correspondência ser convertido para valores de erro. Este é o novo comportamento de erro de correspondência de padrão para `to_bool` e `set_column_types`, ao passo que o comportamento de padrão anterior era converter valores sem correspondência como False.
  + Ao chamar `to_pandas_dataframe`, há uma nova opção para interpretar os valores de nulo/em falta nas colunas numéricas como NaN.
  + Foi adicionada a capacidade para verificar o tipo de retorno de algumas expressões para garantir a consistência de tipo e reprovar no início.
  + Pode chamar `parse_json` analisar os valores numa coluna como objetos JSON e expanda-os em várias colunas.

+ **Correções de erros**
  + Foi corrigido um erro que caiu `set_column_types` em Python 3.5.2.
  + Foi corrigido um erro que falhou ao ligar ao arquivo de dados através de uma imagem AML.

+ **Atualizações**
  * [Blocos de notas do exemplo](https://aka.ms/aml-data-prep-notebooks) para obter tutoriais de introdução, estudos de caso e guias de procedimentos.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Disponibilidade Geral

O serviço do Azure Machine Learning está agora em disponibilidade geral.

### <a name="azure-machine-learning-compute"></a>Computação do Azure Machine Learning
Com esta versão, estamos a anunciar uma nova experiência de computação gerida através da [computação do Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute). Este destino de computação substitui a computação do Azure Batch AI para o Azure Machine Learning. 

Este destino de computação:
+ É utilizada para a inferência de treinamento e em lote de modelo
+ É a computação de único - para várias - node
+ Faz a gestão de cluster e de agendamento para o utilizador de tarefas
+ É dimensionado automaticamente por predefinição
+ Suporte para recursos de CPU e GPU 
+ Permite a utilização de VMs de baixa prioridade de custo reduzido

Computação do Azure Machine Learning podem ser criado em Python, através do portal do Azure ou a CLI. Tem de ser criado na região da sua área de trabalho e não pode ser ligado a outra área de trabalho. Este destino de computação utiliza um contentor do Docker para sua execução e suas dependências para replicar o ambiente mesmo em todos os nós de pacotes.

> [!Warning]
> Recomendamos que crie uma nova área de trabalho para utilizar a computação do Azure Machine Learning. Há uma chance remota que os utilizadores a tentar criar a computação do Azure Machine Learning a partir de uma área de trabalho existente poderão ver um erro. Computação existente na sua área de trabalho deve continuar a trabalhar afetada.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning SDK for Python v1.0.2
+ **Alterações recentes**
  + Com esta versão, estamos a remover suporte para criar uma VM do Azure Machine Learning. Ainda pode anexar uma VM de nuvem existente ou remoto no servidor local. 
  + Estamos também a remover o suporte para BatchAI, todos os quais devem ser suportados por meio de computação do Azure Machine Learning agora.

+ **Novo**
  + Para pipelines de aprendizagem:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Atualizado**
  + Para pipelines de aprendizagem:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) agora aceita runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) agora copia de e para uma origem de dados SQL
    + Agendar funcionalidades no SDK para criar e atualizar agendas para a execução de pipelines publicados

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>SDK v0.5.2 de preparação de dados do Azure Machine Learning
+ **Alterações recentes** 
  * `SummaryFunction.N` nome foi mudado para `SummaryFunction.Count`.
  
+ **Correções de erros**
  * Utilize mais recente AML executar Token quando leitura e gravação para arquivos de dados em execuções remotas. Anteriormente, se o Token AML de executar é atualizado no Python, o tempo de execução de preparação de dados não será atualizado com o Token AML de executar atualizado.
  * Mensagens de erro mais claras adicionais
  * to_spark_dataframe() já não irá falhar quando utiliza o Spark `Kryo` serialização
  * Inspetor de contagem de valores agora pode mostrar mais de 1000 valores exclusivos
  * Divisão aleatória já não falha se o fluxo de dados original não tiver um nome  

+ **Mais informações**
  * [SDK de Preparação de Dados do Azure Machine Learning](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Documentos e blocos de notas
+ Pipelines de ML
  + Exemplos de transferência de blocos de notas novo e atualizados para começar a trabalhar com pipelines, controlo de âmbito do batch e estilo: https://aka.ms/aml-pipeline-notebooks
  + Saiba como [criar seu primeiro pipeline](how-to-create-your-first-pipeline.md)
  + Saiba como [predições de batch com pipelines de execução](how-to-run-batch-predictions.md)
+ Destino de computação do Azure Machine Learning
  + [Blocos de notas de exemplo](https://aka.ms/aml-notebooks) são atualizadas para utilizar a nova computação gerida.
  + [Saiba mais sobre este computação](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Portal do Azure: novos recursos
+ Criar e gerir [computação do Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) tipos no portal.
+ Monitorizar a utilização de quota e [quota de pedido](how-to-manage-quotas.md) para computação do Azure Machine Learning.
+ Ver o estado do cluster de computação do Azure Machine Learning em tempo real.
+ Foi adicionado suporte de rede virtual para a criação de computação do Azure Machine Learning e o Azure Kubernetes Service.
+ Execute novamente os seus pipelines publicados com parâmetros existentes.
+ Novos [automatizada de gráficos do machine learning](how-to-track-experiments.md#auto) para modelos de classificação (comparação de precisão, ganhos, calibração, gráfico de importância da funcionalidade com explainability de modelo) e modelos de regressão (residuals e gráfico de importância da funcionalidade com modelo explainability). 
+ Pipelines podem ser visualizados no portal do Azure




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning SDK for Python v0.1.80

+ **Alterações recentes** 
  * *azureml.Train.widgets* espaço de nomes foi movido para *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* pretere as seguintes classes - *azureml.core.compute.BatchAICompute* e *azureml.core.compute.DSVMCompute*. A segunda classe será removida em versões subsequentes. A classe AmlCompute tem uma definição mais fácil agora, simplesmente precisa de um vm_size e o max_nodes e irá dimensionar automaticamente o cluster de 0 para o max_nodes quando é submetida uma tarefa. Nosso [blocos de notas de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) foram atualizados com essas informações e deverá dar-lhe exemplos de utilização. Esperamos que, como essa simplificação e muitos dos recursos mais interessantes para entrar numa versão posterior!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>SDK v0.5.1 de preparação de dados do Azure Machine Learning 

Saiba mais sobre o SDK de preparação de dados, lendo [referenciar docs](https://aka.ms/data-prep-sdk).
+ **Novos recursos**
   * Criar uma nova CLI de DataPrep para executar pacotes DataPrep e visualizar o perfil de dados para um conjunto de dados ou o fluxo de dados
   * API de SetColumnType reestruturada para melhorar a usabilidade
   * Smart_read_file nome mudado para auto_read_file
   * Agora inclui skew e kurtosis no perfil de dados
   * Pode exemplo com amostragem stratified
   * Pode ler a partir de arquivos zip que contêm ficheiros CSV
   * Pode dividir os conjuntos de dados inter-relacionam com divisão aleatório (por exemplo, para conjuntos de formação de teste).
   * Pode obter todos os a coluna de tipos de dados de um fluxo de dados ou um perfil de dados ao chamar `.dtypes`
   * Pode obter a contagem de linhas de um fluxo de dados ou um perfil de dados ao chamar `.row_count`

+ **Correções de erros**
   * Fixo de tempo de conversão duplo 
   * Foi corrigido---vyhodnocení depois de adicionar qualquer coluna 
   * Foi corrigido um problema com FuzzyGrouping, onde ele não detectaria grupos em alguns casos
   * Função de tipo fixo para respeitar a ordem de classificação de várias colunas
   * Foi corrigido e/ou expressões para ser semelhante ao procedimento `pandas` lida com eles
   * Foi corrigido ler a partir do caminho de dbfs
   * Feitas as mensagens de erro mais compreensível 
   * Agora já não falha durante a leitura no destino de computação remota com o token de AML
   * Agora já não ocorre uma falha do DSVM do Linux
   * Agora já não é interrompida ao valores de cadeia de caracteres não são em predicados de cadeia de caracteres
   * Agora manipula erros de asserção quando o fluxo de dados se falhar corretamente
   * Suporta agora a localizações de armazenamento dbutils montado no Azure Databricks

## <a name="2018-11-05"></a>11-05 de 2018

### <a name="azure-portal"></a>Portal do Azure 
O portal do Azure para o serviço Azure Machine Learning tem as seguintes atualizações:
  * Um novo **Pipelines** separador para pipelines publicados.
  * Foi adicionado suporte para anexar um cluster do HDInsight existente como um destino de computação.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning SDK for Python v0.1.74

+ **Alterações recentes** 
  * * Workspace.compute_targets, arquivos de dados, experiências, imagens, modelos, e *webservices* são propriedades em vez de métodos. Por exemplo, substitua *Workspace.compute_targets()* com *Workspace.compute_targets*.
  * *Run.get_context* pretere *Run.get_submitted_run*. Este segundo método será removido em versões subsequentes.
  * *PipelineData* classe espera agora um objeto de arquivo de dados como um parâmetro em vez de datastore_name. Da mesma forma, *Pipeline* aceita default_datastore em vez de default_datastore_name.

+ **Novos recursos**
  * O Azure Machine Learning Pipelines [bloco de notas do exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) utiliza agora as etapas MPI.
  * O widget RunDetails para blocos de notas do Jupyter é atualizado para mostrar uma visualização do pipeline.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>SDK v0.4.0 de preparação de dados do Azure Machine Learning 
 
+ **Novos recursos**
  * Contagem do tipo adicionado ao perfil de dados 
  * Contagem de valores e histograma já está disponível
  * Mais percentis no perfil de dados
  * O valor mediano está disponível no Summarize
  * Python 3.7 é agora suportado
  * Quando guarda um fluxo de dados que contém os arquivos de dados a um pacote de DataPrep, as informações do arquivo de dados serão mantidas como parte do pacote DataPrep
  * Escrever no arquivo de dados é agora suportado 
        
+ **Bugs corrigidos**
  * os estouros de número inteiro não assinado de 64 bits agora são manipulados corretamente no Linux
  * Etiqueta de texto incorreto fixo para ficheiros de texto sem formatação na smart_read
  * Tipo de coluna de cadeia de caracteres, agora aparece na vista de métricas
  * Contagem do tipo agora é fixo para mostrar ValueKinds mapeado para FieldType único em vez de itens individuais
  * Write_to_csv já não está a falhar quando o caminho é fornecido como uma cadeia de caracteres
  * Ao utilizar a substituição, deixar "encontrar" em branco já não irá falhar 

## <a name="2018-10-12"></a>2018-10 a 12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning SDK for Python v0.1.68

+ **Novos recursos**
  * Suporte de multi-inquilino ao criar nova área de trabalho.

+ **Erros corrigidos**
  * Já não tem de fixar a versão da biblioteca de pynacl ao implementar o serviço web.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>SDK v0.3.0 de preparação de dados do Azure Machine Learning

+ **Novos recursos**
  * Adicionado o método transform_partition_with_file(script_path), que permite que os utilizadores passem o caminho de um ficheiro de Python para executar

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning SDK for Python v0.1.65
[Versão 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) inclui novos recursos, mais documentação, correções de erros e muito mais [blocos de notas de exemplo](https://aka.ms/aml-notebooks).

Ver [a lista de problemas conhecidos](resource-known-issues.md) para saber mais sobre erros conhecidos e soluções alternativas.

+ **Alterações recentes**
  * Workspace.experiments, Workspace.models, Workspace.compute_targets, Workspace.images, dicionário de retorno Workspace.web_services, anteriormente devolvido lista. Ver [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) documentação da API.

  * Aprendizagem automática removido normalizado média quadrática erro as métricas principais.

+ **HyperDrive**
  * Várias correções de erros de HyperDrive para Bayesianos, melhorias de desempenho para receber chamadas de métricas. 
  * Atualização do Tensorflow 1.10 de 1,9 
  * Otimização de imagem de docker para o arranque a frio. 
  * Tarefas agora comunicam o estado correto, mesmo que eles sair com o código de erro diferente de 0. 
  * Validação do atributo de RunConfig no SDK. 
  * Objeto de HyperDrive executar suporta Cancelar semelhante a uma execução regular: há necessidade de passar todos os parâmetros. 
  * Melhorias de widget para manter o estado de valores de lista pendente para execuções distribuídas e HyperDrive execuções. 
  * TensorBoard e outro ficheiros de suporte de registo fixo para o servidor de parâmetros. 
  * Suporte a MPI Intel (r) no lado do serviço. 
  * Bugfix para otimização de parâmetro para distribuído correção execução durante a validação no BatchAI. 
  * Gestor de contexto agora identifica a instância primária. 

+ **Experiência do portal do Azure**
  * log_table() e log_row() são suportadas nos detalhes de execução. 
  * Crie automaticamente os gráficos para tabelas e linhas com 1, 2 ou 3 colunas numéricas e uma coluna de categórica opcional.

+ **Aprendizagem automática**
  * Documentação e melhorado processamento de erros 
  * Corrigido problemas de desempenho de obtenção de propriedade de execução. 
  * Foi corrigido problema de execução de continuar. 
  * Foram corrigidos ensembling erros de iteração.
  * Treinamento fixo pendurado bug no MAC OS.
  * Downsampling macro média PR/curva cor MULTICLASSE no cenário de validação personalizada.
  * Remover a lógica de índice extra.
  * Remover o filtro de get_output API.

+ **Pipelines**
  * Adicionar um método Pipeline.publish() para publicar um pipeline diretamente, sem a necessidade de uma execução de executar primeira.   
  * Adicionar um método PipelineRun.get_pipeline_runs() para buscar as execuções de pipeline que foram geradas a partir de um pipeline publicado.

+ **Project Brainwave**
  * Suporte atualizado para novos modelos de IA, disponíveis no FPGAs.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>SDK v0.2.0 de preparação de dados do Azure Machine Learning
[Versão 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) inclui seguintes funcionalidades e correções de erros:

+ **Novos recursos**
  * Suporte para acesso frequente uma codificação
  * Suporte para a transformação de quantile
   
+ **Erro corrigido:**
  * Funciona com qualquer versão de Tornado, sem a necessidade de mudar a sua versão de Tornado
  * Contagens de valores para todos os valores, não apenas o três de principais

## <a name="2018-09-public-preview-refresh"></a>2018-09 (atualização da pré-visualização pública)

Uma versão nova e atualizada do Azure Machine Learning: Leia mais sobre esta versão: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Passos Seguintes

Leia a visão geral para [serviço Azure Machine Learning](../service/overview-what-is-azure-ml.md).
