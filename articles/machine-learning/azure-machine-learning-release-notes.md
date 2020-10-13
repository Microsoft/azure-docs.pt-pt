---
title: O que há de novo no lançamento?
titleSuffix: Azure Machine Learning
description: Conheça as últimas atualizações para Azure Machine Learning e a machine learning e data prep Python SDKs.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 09/10/2020
ms.openlocfilehash: 189d6a57a17172f181e7375265960fe4f25f8ed1
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940247"
---
# <a name="azure-machine-learning-release-notes"></a>Notas de lançamento do Azure Machine Learning

Neste artigo, saiba mais sobre os lançamentos da Azure Machine Learning.  Para obter o conteúdo completo de referência SDK, visite a página de referência principal do Azure Machine Learning para a página de referência [**python.**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)

Veja [a lista de questões conhecidas](resource-known-issues.md) para conhecer bugs e soluções alternativas conhecidas.

## <a name="2020-09-28"></a>2020-09-28

### <a name="azure-machine-learning-sdk-for-python-v1150"></a>Azure Machine Learning SDK para Python v1.15.0
+ **Correções e melhorias de bugs**
  + **azureml-contrib-interpret**
    + O explicador de LIME passou de azureml-contrib-interpret para interpret-community package and image explainer removido do pacote de interpretação azureml-contrib
    + painel de visualização removido do pacote de interpretação de azureml-contrib, cliente explicativo mudou-se para pacote de interpretação de azureml e precotado em pacote de interpretação de azureml-contrib-interpret e cadernos atualizados para refletir a API melhorada
    + corrigir descrições de pacotes de pypi para azureml-interpret, azureml-explain-model, azureml-contrib-interpret e azureml-tensorboard
  + **azureml-contrib-notebook**
    + Pin nbcovert dependência para < 6 para que a fábrica de papel 1.x continue a funcionar.
  + **azureml-core**
    + Parâmetros adicionados ao construtor tensorflowConfiguration e MpiConfiguration para permitir uma inicialização mais simplificada dos atributos de classe sem exigir que o utilizador desafie cada atributo individual. Adicionou uma aula de Configuração PyTorch para configurar empregos PyTorch distribuídos no ScriptRunConfig.
    + Fixar a versão do recurso azure-mgmt para corrigir o erro de autenticação.
    + Suporte Tritão Sem Implementação de Código
    + os diretórios de saídas especificados em Run.start_logging() serão agora rastreados quando utilizarem a execução em cenários interativos. Os ficheiros rastreados serão visíveis no ML Studio ao chamar Run.complete()
    + A codificação de ficheiros pode agora ser especificada durante a criação do conjunto de dados com `Dataset.Tabular.from_delimited_files` e `Dataset.Tabular.from_json_lines_files` passando o `encoding` argumento. As codificações apoiadas são 'utf8', 'iso88591', 'latin1', 'ascii', utf16', 'utf32', 'utf8bom' e 'windows1252'.
    + Correção de erros quando o objeto ambiente não é passado para o construtor ScriptRunConfig.
    + Run.cancel atualizado para permitir o cancelamento de uma execução local de outra máquina.
  + **azureml-dataprep**
    +  Problemas de tempo de montagem de conjunto de dados fixos.
  + **azureml-explain-modelo**
    + corrigir descrições de pacotes de pypi para azureml-interpret, azureml-explain-model, azureml-contrib-interpret e azureml-tensorboard
  + **azureml-interpret**
    + painel de visualização removido do pacote de interpretação de azureml-contrib, cliente explicativo mudou-se para pacote de interpretação de azureml e precotado em pacote de interpretação de azureml-contrib-interpret e cadernos atualizados para refletir a API melhorada
    + pacote de interpretação azureml atualizado para depender da comunidade interpretação 0.15.0
    + corrigir descrições de pacotes de pypi para azureml-interpret, azureml-explain-model, azureml-contrib-interpret e azureml-tensorboard
  + **azureml-pipeline-core**
    +  Problema de gasoduto fixo com `OutputFileDatasetConfig` o local onde o sistema pode parar de responder quando é chamado com o parâmetro definido para `register_on_complete` um nome de conjunto de `name` dados pré-existente.
  + **azureml-pipeline-steps**
    + Removidos cadernos de dados velhos.
  + **azureml-tensorboard**
    + corrigir descrições de pacotes de pypi para azureml-interpret, azureml-explain-model, azureml-contrib-interpret e azureml-tensorboard
  + **azureml-train-automl-runtime**
    + painel de visualização removido do pacote de interpretação de azureml-contrib, cliente explicativo mudou-se para pacote de interpretação de azureml e precotado em pacote de interpretação de azureml-contrib-interpret e cadernos atualizados para refletir a API melhorada
  + **azureml-widgets**
    + painel de visualização removido do pacote de interpretação de azureml-contrib, cliente explicativo mudou-se para pacote de interpretação de azureml e precotado em pacote de interpretação de azureml-contrib-interpret e cadernos atualizados para refletir a API melhorada

## <a name="2020-09-21"></a>2020-09-21

### <a name="azure-machine-learning-sdk-for-python-v1140"></a>Azure Machine Learning SDK para Python v1.14.0
+ **Correções e melhorias de bugs**
  + **azure-cli-ml**
    + Grelha De Perfis removidos do SDK e já não é suportado.
  + **azureml-accel-models**
    + pacote de modelos azureml-accel agora suporta Tensorflow 2.x
  + **azureml-automl-core**
    + Tratamento adicional de erros em get_output para casos em que as versões locais de pandas/sklearn não correspondem às utilizadas durante o treino
  + **azureml-automl-runtime**
    + Corrigiu um bug onde as iterações AutoArima falhariam com uma PrevisãoExcepção e a mensagem: "Falha silenciosa ocorreu durante a previsão."
  + **azureml-cli-comum**
    + Grelha De Perfis removidos do SDK e já não é suportado.
  + **azureml-contrib-servidor**
    + Atualizar a descrição do pacote para a página geral do pypi.
  + **azureml-core**
    + Grid Profiling removido do SDK e já não é suportado.
    + Reduza o número de mensagens de erro quando a recuperação do espaço de trabalho falhar.
    + Não mostre aviso ao ir buscar metadados falha
    + Novo Kusto Step e Kusto Compute Target.
    + Atualizar documento para o parâmetro sku. Remova sku na funcionalidade de atualização do espaço de trabalho em CLI e SDK.
    + Atualizar a descrição do pacote para a página geral do pypi.
    + Documentação atualizada para Ambientes AzureML.
    + Expor as definições de recursos geridos pelo serviço AML no SDK.
  + **azureml-dataprep**
    + Ativar a permissão de execução em ficheiros para a montagem do Conjunto de Dados.
  + **azureml-mlflow**
    + Documentação atualizada do AzureML MLflow e amostras de cadernos 
    + Novo suporte para projetos MLflow com backend AzureML
    + Suporte ao registo do modelo MLflow
    + Apoio adicional do RBAC para operações de AzureML-MLflow 
    
  + **azureml-pipeline-core**
    + Melhorou a documentação dos métodos PipelineOutputFileDataset.parse_*.
    + Novo Kusto Step e Kusto Compute Target.
    + Desde que a propriedade da Swaggerurl para a entidade de ponta de gasoduto através desse utilizador possa ver a definição de esquema para o ponto final do gasoduto publicado.
  + **azureml-pipeline-steps**
    + Novo Kusto Step e Kusto Compute Target.
  + **azureml-telemetry**
    + Atualizar a descrição do pacote para a página geral do pypi.
  + **azureml-train**
    + Atualizar a descrição do pacote para a página geral do pypi.
  + **azureml-train-automl-client**
    + Tratamento adicional de erros em get_output para casos em que as versões locais de pandas/sklearn não correspondem às utilizadas durante o treino
  + **azureml-train-core**
    + Atualizar a descrição do pacote para a página geral do pypi.
    
## <a name="2020-08-31"></a>2020-08-31

### <a name="azure-machine-learning-sdk-for-python-v1130"></a>Azure Machine Learning SDK para Python v1.13.0
+ **Funcionalidades de pré-visualização**
  + **azureml-core** Com a nova capacidade de conjuntos de dados de saída, pode voltar a escrever para o armazenamento em nuvem, incluindo Blob, ADLS Gen 1, ADLS Gen 2 e FileShare. Pode configurar onde deverir dados de produção, como obter dados de produção (através de montagem ou upload), se registar os dados de saída para futura reutilização e partilha e passar dados intermédios entre etapas de pipeline sem problemas. Isto permite a reprodutibilidade, partilha, impede a duplicação de dados, e resulta em eficiência de custos e ganhos de produtividade. [Saiba como usá-lo](https://docs.microsoft.com/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true)
    
+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + Adicionado validated_{plataforma}_requirements.txt ficheiro para fixar todas as dependências de pip para AutoML.
    + Esta versão suporta modelos superiores a 4 Gb.
    + Dependências automl melhoradas: `scikit-learn` (agora 0.22.1), `pandas` (agora 0.25.1), `numpy` (agora 1.18.2).
  + **azureml-automl-runtime**
    + Desaperte a horovod para o texto DNN utilizar sempre a compressão fp16.
    + Esta versão suporta modelos superiores a 4 Gb.
    + Problema fixo em que a AutoML falha com o ImportError: não pode importar `RollingOriginValidator` nome.
    + Dependências automl melhoradas: `scikit-learn` (agora 0.22.1), `pandas` (agora 0.25.1), `numpy` (agora 1.18.2).
  + **azureml-contrib-automl-dnn-previsão**
    + Dependências automl melhoradas: `scikit-learn` (agora 0.22.1), `pandas` (agora 0.25.1), `numpy` (agora 1.18.2).
  + **azureml-contrib-fairness**
    + Forneça uma breve descrição para a azureml-contrib-fairness.
  + **azureml-contrib-pipeline-steps**
    + A mensagem adicionada indicando que este pacote é depreciada e o utilizador deve utilizar passos de gasoduto azureml.
  + **azureml-core**
    + Comando chave da lista adicionado para espaço de trabalho.
    + Adicione o parâmetro tags no Workspace SDK e no CLI.
    + Fixar o erro onde submeter uma criança com dataset falhará devido a `TypeError: can't pickle _thread.RLock objects` .
    + Adicionar page_count predefinido/documentação para a lista de modelos().
    + Modifique o CLI&SDK para tomar o parâmetro do espaço de trabalho e adicione o adb lin/unlink runner do espaço de trabalho.
    + Corrigir bug em Dataset.update que fez com que a versão mais recente do Dataset fosse atualizada e não a versão da atualização do Dataset foi chamada. 
    + Corrija o bug em Dataset.get_by_name que mostraria as tags para a versão mais recente do Dataset mesmo quando uma versão específica mais antiga foi recuperada.
  + **azureml-interpret**
    + Saídas de probabilidade adicionadas a explicadores de pontuação shap em azureml-interpret com base em shap_values_output parâmetro do explicador original.
  + **azureml-pipeline-core**
    + Melhora `PipelineOutputAbstractDataset.register` documentação.
  + **azureml-train-automl-client**
    + Dependências automl melhoradas: `scikit-learn` (agora 0.22.1), `pandas` (agora 0.25.1), `numpy` (agora 1.18.2).
  + **azureml-train-automl-runtime**
    + Dependências automl melhoradas: `scikit-learn` (agora 0.22.1), `pandas` (agora 0.25.1), `numpy` (agora 1.18.2).
  + **azureml-train-core**
    + Os utilizadores devem agora fornecer um hyperparameter_sampling arg válido ao criar um HyperDriveConfig. Além disso, a documentação do HyperDriveRunConfig foi editada para informar os utilizadores da depreciação do HyperDriveRunConfig.
    + Reverter a versão padrão do PyTorch para 1.4.
    + Adicionando PyTorch 1.6 & Tensorflow 2.2 imagens e ambiente curado.

### <a name="azure-machine-learning-studio-notebooks-experience-august-update"></a>Experiência de cadernos do estúdio de aprendizagem de máquinas Azure (atualização de agosto)
+ **Novas funcionalidades**
  + Nova Página de aterragem começada 
  
+ **Funcionalidades de pré-visualização**
    + Reúna o recurso em Cadernos. Com a funcionalidade [Recolha,](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks#clean-your-notebook-preview)   os utilizadores podem agora limpar facilmente os cadernos com, o Gather utiliza uma análise automatizada de dependência do seu caderno, garantindo que o código essencial é mantido, mas removendo quaisquer peças irrelevantes.

+ **Correções e melhorias de bugs**
  + Melhoria da velocidade e fiabilidade
  + Bugs de modo escuro fixos
  + Bugs de deslocação de saída fixos
  + A Pesquisa de Amostras procura agora todo o conteúdo de todos os ficheiros do Azure Machine Learning amostra cadernos repo
  + As células R multi-linha já podem funcionar
  + "Confio no conteúdo deste ficheiro" é agora verificado automaticamente após a primeira vez
  + Diálogo de resolução de conflitos melhorado, com nova opção "Faça uma cópia"
  
## <a name="2020-08-17"></a>2020-08-17

### <a name="azure-machine-learning-sdk-for-python-v1120"></a>Azure Machine Learning SDK para Python v1.12.0

+ **Correções e melhorias de bugs**
  + **azure-cli-ml**
    + Adicione image_name e image_label parâmetros ao Modelo.pacote() para permitir renomear a imagem do pacote construído.
  + **azureml-automl-core**
    + O AutoML levanta um novo código de erro a partir do dataprep quando o conteúdo é modificado durante a leitura.
  + **azureml-automl-runtime**
    + Alertas adicionados para o utilizador quando os dados contêm valores em falta, mas a exibição é desligada.
    + Falhas de execução de crianças fixas quando os dados contêm nan e a exibição é desligada.
    + O AutoML levanta um novo código de erro a partir do dataprep quando o conteúdo é modificado durante a leitura.
    + Normalização atualizada para as métricas de previsão ocorrerem por grão.
    + Melhor cálculo dos quantiles de previsão quando as características de reeguardamento são desativadas.
    + Manuseamento de matriz de sparse bool fixo ao calcular explicações após AutoML.
  + **azureml-core**
    + Um novo método `run.get_detailed_status()` mostra agora a explicação detalhada do estado atual da execução. Atualmente, apenas mostra explicações para `Queued` o estatuto.
    + Adicione image_name e image_label parâmetros ao Modelo.pacote() para permitir renomear a imagem do pacote construído.
    + Novo método `set_pip_requirements()` para definir toda a secção de pip de uma [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py&preserve-view=true) vez.
    + Ativar o registo de datas ADLS Gen2 sem credenciais.
    + Mensagem de erro melhorada ao tentar descarregar ou montar um tipo de conjunto de dados incorreto.
    + Atualizar o bloco de dados da série de tempo filtrar o caderno com mais exemplos de partition_timestamp que fornece otimização de filtros.
    + Altere o sdk e o CLI para aceitar subscriçãoId, grupo de recursos, workspaceName, peConnectionName como parâmetros em vez de ArmResourceId ao eliminar a ligação privada de ponto final.
    + Decorador Experimental mostra nome de classe para uma identificação mais fácil.
    + As descrições para os Ativos dentro dos Modelos já não são geradas automaticamente com base numa Execução.
  + **azureml-datadrift**
    + Mark create_from_model API em DataDriftDetector a ser depreciada.
  + **azureml-dataprep**
    + Mensagem de erro melhorada ao tentar descarregar ou montar um tipo de conjunto de dados incorreto.
  + **azureml-pipeline-core**
    + Erro fixo ao desseecializar gráfico de gasoduto que contém conjuntos de dados registados.
  + **azureml-pipeline-steps**
    + O RScriptStep suporta a RSection de azureml.core.environment.
    + Retirou o parâmetro passthru_automl_config da `AutoMLStep` API pública e converteu-o num parâmetro interno.
  + **azureml-train-automl-client**
    + Removido ambiente assíncronos local, gerido corre a partir de AutoML. Todas as corridas locais vão correr no ambiente de onde a corrida foi lançada.
    + Problemas de instantâneo fixos ao submeter o AutoML funciona sem scripts fornecidos pelo utilizador.
    + Falhas de execução de crianças fixas quando os dados contêm nan e a exibição é desligada.
  + **azureml-train-automl-runtime**
    + O AutoML levanta um novo código de erro a partir do dataprep quando o conteúdo é modificado durante a leitura.
    + Problemas de instantâneo fixos ao submeter o AutoML funciona sem scripts fornecidos pelo utilizador.
    + Falhas de execução de crianças fixas quando os dados contêm nan e a exibição é desligada.
  + **azureml-train-core**
    + Suporte adicional para especificar opções de pip (por exemplo --extra-index-url) no ficheiro de requisitos do pip passado para um [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py&preserve-view=true) `pip_requirements_file` parâmetro através.


## <a name="2020-08-03"></a>2020-08-03

### <a name="azure-machine-learning-sdk-for-python-v1110"></a>Azure Machine Learning SDK para Python v1.11.0

+ **Correções e melhorias de bugs**
  + **azure-cli-ml**
    + Fixar quadro de modelo e quadro de modelo não passado em objeto executado na trajetória de registo do modelo CLI
    + Fixe o comando de exibição de identidade amlcompute CLI para mostrar iD do inquilino e iD principal 
  + **azureml-train-automl-client**
    + Adicionado get_best_child () ao AutoMLRun para obter a melhor corrida para crianças para uma Corrida AutoML sem descarregar o modelo associado.
    + Objeto ModelProxy adicionado que permite prever ou prever ser executado em um ambiente de treino remoto sem descarregar o modelo localmente.
    + Exceções não manipuladas na AutoML apontam agora para uma página HTTP de questões conhecidas, onde podem ser encontradas mais informações sobre os erros.
  + **azureml-core**
    + Os nomes dos modelos podem ter 255 caracteres de comprimento.
    + Environment.get_image_details() tipo de objeto de retorno alterado. `DockerImageDetails` substituição de `dict` classes, detalhes de imagem estão disponíveis a partir das novas propriedades da classe. As mudanças são compatíveis com retroativo.
    + Corrigir bug para Environment.from_pip_requirements() para preservar a estrutura das dependências
    + Corrigiu um bug onde log_list falharia se um int e um duplo fossem incluídos na mesma lista.
    + Ao mesmo tempo que permite uma ligação privada num espaço de trabalho existente, note que se houver metas de computação associadas ao espaço de trabalho, esses alvos não funcionarão se não estiverem atrás da mesma rede virtual que o ponto final privado do espaço de trabalho.
    + Tornado `as_named_input` opcional ao utilizar conjuntos de dados em experiências e `as_mount` adicionados e a `as_download` `FileDataset` . O nome de entrada será gerado automaticamente se `as_mount` ou `as_download` for chamado.
  + **azureml-automl-core**
    + Exceções não manipuladas na AutoML apontam agora para uma página HTTP de questões conhecidas, onde podem ser encontradas mais informações sobre os erros.
    + Adicionado get_best_child () ao AutoMLRun para obter a melhor corrida para crianças para uma Corrida AutoML sem descarregar o modelo associado.
    + Objeto ModelProxy adicionado que permite prever ou prever ser executado em um ambiente de treino remoto sem descarregar o modelo localmente.
  + **azureml-pipeline-steps**
    + Adicionado `enable_default_model_output` e `enable_default_metrics_output` bandeiras a `AutoMLStep` . Estas bandeiras podem ser utilizadas para ativar/desativar as saídas predefinidos.


## <a name="2020-07-20"></a>2020-07-20

### <a name="azure-machine-learning-sdk-for-python-v1100"></a>Azure Machine Learning SDK para Python v1.10.0

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + Ao utilizar o AutoML, se um caminho for passado para o objeto AutoMLConfig e já não existir, será automaticamente criado.
    + Os utilizadores podem agora especificar uma frequência de série sonora para as tarefas de previsão utilizando o `freq` parâmetro.
  + **azureml-automl-runtime**
    + Ao utilizar o AutoML, se um caminho for passado para o objeto AutoMLConfig e já não existir, será automaticamente criado.
    + Os utilizadores podem agora especificar uma frequência de série sonora para as tarefas de previsão utilizando o `freq` parâmetro.
    + A AutoML Forecasting suporta agora a avaliação de rolamento, que se aplica ao caso de utilização de que o comprimento de um conjunto de teste ou validação é mais longo do que o horizonte de entrada, e conhecido y_pred valor é usado como contexto de previsão.
  + **azureml-core**
    + As mensagens de aviso serão impressas se nenhum ficheiro for descarregado da datastore numa corrida.
    + Documento adicional `skip_validation` para o `Datastore.register_azure_sql_database method` .
    + Os utilizadores são obrigados a fazer upgrade para o Sdk v1.10.0 ou superior para criar um ponto final privado aprovado por automático. Isto inclui o recurso Notebook que é utilizável por trás do VNet.
    + Expor NotebookInfo na resposta do get workspace.
    + As alterações para ter chamadas para listar alvos de computação e obter o alvo do cálculo têm sucesso numa execução remota. As funções do Sdk para obter metas de computação e listar alvos de computação de espaço de trabalho vão agora funcionar em execuções remotas.
    + Adicione mensagens de depreciação às descrições da classe para aulas azureml.core.image.
    + Atire a exceção e limpe o espaço de trabalho e os recursos dependentes se a criação de ponto final privado do espaço de trabalho falhar.
    + Suporte a atualização do sku do espaço de trabalho no método de atualização do espaço de trabalho.
  + **azureml-datadrift**
    + Atualize a versão matplotlib de 3.0.2 a 3.2.1 para suportar a pitão 3.8.
  + **azureml-dataprep**
    + Suporte adicionado de fontes de dados de url web com `Range` ou `Head` solicitar. 
    + Melhoria da estabilidade para o conjunto de dados de ficheiros e para o download.
  + **azureml-train-automl-client**
    + Questões fixas relacionadas com a remoção dos `RequirementParseError` instalações.
    + Use estivador em vez de conda para corridas locais submetidas usando "compute_target='local'"
    + A duração da iteração impressa na consola foi corrigida. Anteriormente, a duração da iteração era por vezes impressa como tempo final de execução menos tempo de criação de execução. Foi corrigido para igual prazo de fim de tempo menos tempo de início de execução.
    + Ao utilizar o AutoML, se um caminho for passado para o objeto AutoMLConfig e já não existir, será automaticamente criado.
    + Os utilizadores podem agora especificar uma frequência de série sonora para as tarefas de previsão utilizando o `freq` parâmetro.
  + **azureml-train-automl-runtime**
    + Melhor produção da consola quando as melhores explicações do modelo falham.
    + Renomeado parâmetro de entrada "backlist_models" para "blocked_models".
      + Renomeado parâmetro de entrada "whitelist_models" para "allowed_models".
    + Os utilizadores podem agora especificar uma frequência de série sonora para as tarefas de previsão utilizando o `freq` parâmetro.

  
## <a name="2020-07-06"></a>2020-07-06

### <a name="azure-machine-learning-sdk-for-python-v190"></a>Azure Machine Learning SDK para Python v1.9.0

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + Substituído get_model_path() por AZUREML_MODEL_DIR variável ambiente no script de pontuação autogerado por AutoML. Também adicionou telemetria para rastrear falhas durante o init().
    + Removido a capacidade de especificar `enable_cache` como parte do AutoMLConfig
    + Corrigiu um bug onde as corridas podem falhar com erros de serviço durante as operações de previsão específicas
    + Melhor manuseamento de erros em torno de modelos específicos durante `get_output`
    + Chamada fixa para fitted_model.fit (X, y) para classificação com transformador y
    + Imputer de preenchimento personalizado para a frente ativado para tarefas de previsão
    + Uma nova classe de Parâmetros de Previsão será usada em vez de prever parâmetros num formato dict
    + Melhor desdetectação do lag do alvo
    + Disponibilidade limitada adicionada de multi-acenar, multi-gpu distribuída com BERT
  + **azureml-automl-runtime**
    + Profeta agora faz modelo de sazonalidade aditiva em vez de multiplicativo.
    + Corrigiu o problema quando os grãos curtos, tendo frequências diferentes das dos grãos longos resultará em execuções falhadas.
  + **azureml-contrib-automl-dnn-vision**
    + Recolher estatísticas de sistema/gpu e médias de registo para treino e pontuação
  + **azureml-contrib-mir**
    + Suporte adicional para a bandeira de insights de aplicações ativas em ManagedInferencing
  + **azureml-core**
    + Um parâmetro validado a estas APIs, permitindo que a validação seja ignorada quando a fonte de dados não está acessível a partir do cálculo atual.
      + TabularDataset.time_before(end_time, include_boundary=Verdadeiro, valide=Verdadeiro)
      + TabularDataset.time_after(start_time, include_boundary=Verdadeiro, valide=Verdadeiro)
      + TabularDataset.time_recent(time_delta, include_boundary=Verdadeiro, valide=Verdadeiro)
      + TabularDataset.time_between(start_time, end_time, include_boundary=Verdadeiro, valide=Verdadeiro)
    + Suporte de filtragem de quadro adicionado para lista de modelos, e adicionado amostra NCD AutoML no portátil de volta
    + Para Datastore.register_azure_blob_container e Datastore.register_azure_file_share (apenas opções que suportam o token SAS), atualizámos as cordas do doc para o `sas_token` campo para incluir requisitos mínimos de permissões para cenários típicos de leitura e escrita.
    + Depreciação _with_auth param em ws.get_mlflow_tracking_uri()
  + **azureml-mlflow**
    + Adicione suporte para implantar modelos locais de file:// com AzureML-MLflow
    + Depreciação _with_auth param em ws.get_mlflow_tracking_uri()
  + **azureml-opendatasets**
    + Os conjuntos de dados de rastreio Covid-19 recentemente publicados estão agora disponíveis com o SDK
  + **azureml-pipeline-core**
    + Faça um aviso quando "azureml-defaults" não estiver incluído como parte da dependência do pip
    + Melhore a renderização de notas.
    + Suporte adicional para quebras de linha citadas ao analisar ficheiros delimitados para PipelineOutputFileDataset.
    + A classe PipelineDataset está depreciada. Para obter mais informações, consulte https://aka.ms/dataset-deprecation. Saiba como utilizar o conjunto de dados com o pipeline, veja https://aka.ms/pipeline-with-dataset .
  + **azureml-pipeline-steps**
    + Doc atualiza para azureml-pipeline-steps.
    +  Suporte adicionado em ParallelRunConfig para `load_yaml()` que os utilizadores definam Ambientes em linha com o resto da config ou em um ficheiro separado
  + **azureml-train-automl-cliente.**
    + Removido a capacidade de especificar `enable_cache` como parte do AutoMLConfig
  + **azureml-train-automl-runtime**
    + Acrescentou disponibilidade limitada de multi-acenar, multi-gpu distribuído com BERT.
    + Tratamento de erros adicionado para pacotes incompatíveis em machine learning automatizado baseado em ADB funciona.
  + **azureml-widgets**
    + Doc atualiza para azureml-widgets.

  
## <a name="2020-06-22"></a>2020-06-22

### <a name="azure-machine-learning-sdk-for-python-v180"></a>Azure Machine Learning SDK para Python v1.8.0
  
  + **Funcionalidades de pré-visualização**
    + **azureml-contrib-fairness** O `azureml-contrib-fairness` pacote proporciona integração entre a avaliação da equidade de código aberto e o pacote de mitigação da injustiça [Fairlearn](https://fairlearn.github.io) e o estúdio Azure Machine Learning. Em particular, o pacote permite que os dashboards de avaliação de justiça do modelo sejam carregados como parte de uma AzureML Run e apareçam no estúdio Azure Machine Learning

+ **Correções e melhorias de bugs**
  + **azure-cli-ml**
    + Suporte a obter registos de recipiente init.
    + Adicionou novos comandos CLI para gerir o ComputeInstance
  + **azureml-automl-core**
    + Os utilizadores são agora capazes de ativar a iteração do conjunto de pilhas para tarefas da série Time com um aviso de que poderia potencialmente sobreajustar.
    + Acrescentou um novo tipo de exceção do utilizador que é levantada se o conteúdo da loja cache tiver sido adulterado
  + **azureml-automl-runtime**
    + A varredura de equilíbrio de classes deixará de ser ativada se o utilizador desativar a caracterização.  
  + **azureml-contrib-itp**
    + O tipo de computação CmAks é suportado. Você pode anexar o seu próprio cluster AKS ao espaço de trabalho para trabalho de treino.
  + **azureml-contrib-notebook**
    + Doc melhora o pacote de cadernos azureml-contrib.
  + **azureml-contrib-pipeline-steps**
    + Doc melhora a azureml-contrib-- pipeline-steps package.
  + **azureml-core**
    + Adicione set_connection, get_connection, list_connections, delete_connection funções para o cliente operar em recursos de conexão do espaço de trabalho
    + Atualizações de documentação para pacote azureml-coore/azureml.exceptions.
    + Atualizações de documentação para o pacote azureml-core.
    + Doc atualiza para a classe ComputeInstance.
    + Doc melhorias no pacote azureml-core/azureml.core.compute.
    + Melhorias do doc para classes relacionadas com webservice em azureml-core.
    + Suporte à loja de dados selecionada pelo utilizador para armazenar dados de perfis
    + Propriedade de expansão e page_count adicionada para lista de modelos API
    + Erro fixo onde a remoção da propriedade sobredebrulhada fará com que a execução submetida falhe com erro de deserialização.
    + Estrutura de pasta inconsistente fixa ao descarregar ou montar um FicheiroDataset referenciando-o a um único ficheiro.
    + Carregar um conjunto de dados de ficheiros parquet to_spark_dataframe é agora mais rápido e suporta todos os tipos de dados parquet e Spark SQL.
    + Suporte a obter registos de recipiente init.
    + As corridas autoML estão agora marcadas como a execução infantil do Passo de Execução Paralela.
  + **azureml-datadrift**
    + Doc melhora o pacote de cadernos azureml-contrib.
  + **azureml-dataprep**
    + Carregar um conjunto de dados de ficheiros parquet to_spark_dataframe é agora mais rápido e suporta todos os tipos de dados parquet e Spark SQL.
    + Melhor tratamento de memória para o problema outOfMemory para to_pandas_dataframe.
  + **azureml-interpret**
    + Azureml-interpretado atualizado para usar a versão interpreta-comunidade 0.12.*
  + **azureml-mlflow**
    + Doc melhorias no azureml-mlflow.
    + Adiciona suporte para o registo do modelo AML com MLFlow.
  + **azureml-opendatasets**
    + Apoio adicional para Python 3.8
  + **azureml-pipeline-core**
    + A documentação atualizada `PipelineDataset` para deixar claro que é uma classe interna.
    + ParallelRunStep atualiza para aceitar vários valores para um argumento, por exemplo: "-group_column_names", "Col1", "Col2", "Col3"
    + Removido o requisito de passthru_automl_config para a utilização intermédia de dados com AutoMLStep em Pipelines.
  + **azureml-pipeline-steps**
    + Doc melhora o pacote de passos de gasoduto azureml.
    + Removido o requisito de passthru_automl_config para a utilização intermédia de dados com AutoMLStep em Pipelines.
  + **azureml-telemetry**
    + Doc melhora a azureml-telemetria.
  + **azureml-train-automl-client**
    + Consertado um bug onde `experiment.submit()` chamado duas vezes num `AutoMLConfig` objeto resultou em comportamento diferente.
    + Os utilizadores são agora capazes de ativar a iteração do conjunto de pilhas para tarefas da série Time com um aviso de que poderia potencialmente sobreajustar.
    + Alterado Comportamento de execução autoML para aumentar o UserErrorExcepção se o serviço lançar erro do utilizador
    + Corrige um bug que fez com que azureml_automl.log não fosse gerado ou faltasse registos ao realizar uma experiência AutoML num alvo de computação remota.
    + Para conjuntos de dados de classificação com classes desequilibradas, aplicaremos o Equilíbrio de Peso, se o varredor de recurso determinar que para dados subsmaltados, o Equilíbrio de Peso melhora o desempenho da tarefa de classificação por um determinado limiar.
    + As corridas autoML estão agora marcadas como a execução infantil do Passo de Execução Paralela.
  + **azureml-train-automl-runtime**
    + Alterado Comportamento de execução autoML para aumentar o UserErrorExcepção se o serviço lançar erro do utilizador
    + As corridas autoML estão agora marcadas como a execução infantil do Passo de Execução Paralela.

  
## <a name="2020-06-08"></a>2020-06-08

### <a name="azure-machine-learning-sdk-for-python-v170"></a>Azure Machine Learning SDK para Python v1.7.0

+ **Correções e melhorias de bugs**
  + **azure-cli-ml**
    + Concluída a remoção do perfil do modelo do mir contrib através da limpeza dos comandos CLI e das dependências dos pacotes, o perfil do modelo está disponível no núcleo.
    + Atualiza a versão min Azure CLI para 2.3.0
  + **azureml-automl-core**
    + Melhor mensagem de exceção no passo de aposição fit_transform() devido aos parâmetros do transformador personalizado.
    + Adicione suporte para várias línguas para modelos de transformadores de aprendizagem profunda, como BERT em ML automatizado.
    + Remova o parâmetro de lag_length prevadido da documentação.
    + A documentação dos parâmetros de previsão foi melhorada. O parâmetro lag_length foi depreciado.
  + **azureml-automl-runtime**
    + Corrigiu o erro levantado quando uma das colunas categóricas está vazia no tempo de previsão/teste.
    + Corrija as falhas de execução que ocorrem quando as funcionalidades de recuo estão ativadas e os dados contêm grãos curtos.
    + Corrigiu o problema com uma mensagem de erro duplicada do índice de tempo quando os lags ou janelas rolantes foram definidos como 'auto'.
    + Corrigiu o problema com os modelos Profeta e Arima em conjuntos de dados, contendo as funcionalidades de recuo.
    + Apoio adicional de datas antes de 1677-09-21 ou depois de 2262-04-11 em colunas outras datas então data nas tarefas de previsão. Mensagens de erro melhoradas.
    + A documentação dos parâmetros de previsão foi melhorada. O parâmetro lag_length foi depreciado.
    + Melhor mensagem de exceção no passo de aposição fit_transform() devido aos parâmetros do transformador personalizado.
    + Adicione suporte para várias línguas para modelos de transformadores de aprendizagem profunda, como BERT em ML automatizado.
    + As operações de cache que resultam em alguns OSErrors levantarão o erro do utilizador.
    + Verificações adicionais para garantir que os dados de formação e validação têm o mesmo número e conjunto de colunas
    + Problema fixo com o script de pontuação autogerado autoML quando os dados contêm aspas
    + Habilitando explicações para o Profeta AutoML e modelos conjuntos que contêm modelo Profeta.
    + Um recente problema com o cliente revelou um bug ao vivo em que registamos mensagens ao longo da classificação-balanceamento-sweeping mesmo quando a lógica de Equilíbrio de Classes não está devidamente ativada. Removendo os registos/mensagens com este PR.
  + **azureml-cli-comum**
    + Concluída a remoção do perfil do modelo do mir contrib através da limpeza dos comandos CLI e das dependências dos pacotes, o perfil do modelo está disponível no núcleo.
  + **azureml-contrib-reinforcementlearning**
    + Ferramenta de teste de carga
  + **azureml-core**
    + Alterações de documentação em Script_run_config.py
    + Corrige um bug com a impressão da saída do pipeline de submissão de corrida CLI
    + Melhorias de documentação para azureml-core/azureml.data
    + Correções problemas recuperando conta de armazenamento usando o comando hdfs getconf
    + Melhor register_azure_blob_container e documentação register_azure_file_share
  + **azureml-datadrift**
    + Melhor implementação para desativar e permitir monitores de deriva do conjunto de dados
  + **azureml-interpret**
    + Em explicação cliente, remova NaNs ou Infs antes da serialização json no upload de artefactos
    + Atualização para a versão mais recente da interpret-community para melhorar os erros de memória para explicações globais com muitas funcionalidades e classes
    + Adicione true_ys parâmetro opcional ao upload de explicação para permitir funcionalidades adicionais no estúdio UI
    + Melhorar download_model_explanations e list_model_explanations() desempenho
    + Pequenos ajustes aos cadernos, para ajudar na depuragem
  + **azureml-opendatasets**
    + os conjuntos de dados azureml-open precisam da versão 1.4.0 ou superior a dados azureml.0. Aviso adicionado se a versão inferior for detetada
  + **azureml-pipeline-core**
    + Esta alteração permite ao utilizador fornecer um runconfig opcional ao móduloVersion ao chamar módulo. Publish_python_script.
    + Ativar a conta de nó pode ser um parâmetro de pipeline em ParallelRunStep em azureml.pipeline.steps
  + **azureml-pipeline-steps**
    + Esta alteração permite ao utilizador fornecer um runconfig opcional ao móduloVersion ao chamar módulo. Publish_python_script.
  + **azureml-train-automl-client**
    + Adicione suporte para várias línguas para modelos de transformadores de aprendizagem profunda, como BERT em ML automatizado.
    + Remova o parâmetro de lag_length prevadido da documentação.
    + A documentação dos parâmetros de previsão foi melhorada. O parâmetro lag_length foi depreciado.
  + **azureml-train-automl-runtime**
    + Habilitando explicações para o Profeta AutoML e modelos conjuntos que contêm modelo Profeta.
    + Atualizações de documentação para pacotes azureml-train-automl-* .
  + **azureml-train-core**
    + Suporte à versão 2.1 do TensorFlow no Estimador PyTorch
    + Melhorias no pacote azureml-train-core.
  
## <a name="2020-05-26"></a>2020-05-26

### <a name="azure-machine-learning-sdk-for-python-v160"></a>Azure Machine Learning SDK para Python v1.6.0

+ **Novas funcionalidades**
  + **azureml-automl-runtime**
    + A AutoML Forecasting suporta agora os clientes previstos para além do horizonte máximo pré-especificado sem retreinar o modelo. Quando o destino de previsão estiver mais longe no futuro do que o horizonte máximo especificado, a função de previsão continuará a fazer previsões pontuais para a data posterior utilizando um modo de funcionamento recursivo. Para ilustrar a nova funcionalidade, consulte a secção "Previsão mais longe do que o horizonte máximo" do caderno "previsão-previsão-função" na [pasta](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)."
  
  + **azureml-pipeline-steps**
    + ParallelRunStep é agora lançado e faz parte do pacote de **passos de gasoduto azureml.** O ParallelRunStep existente no pacote **de passos azureml-contrib-pipeline** é precotado. Alterações da versão de pré-visualização pública:
      + Parâmetro `run_max_try` configurável opcional adicionado para controlar a chamada máxima para executar o método para qualquer lote dado, o valor predefinido é 3.
      + Já não há PipelineParameters autogerados. Os valores configuráveis podem ser definidos como PipelineParameter explicitamente.
        + mini_batch_size
        + node_count
        + process_count_per_node
        + logging_level
        + run_invocation_timeout
        + run_max_try
      + O valor predefinido para process_count_per_node é alterado para 1. O utilizador deve sintonizar este valor para um melhor desempenho. As melhores práticas são definir como o número de gPU ou nó CPU tem.
      + ParallelRunStep não injeta nenhum pacote, o utilizador precisa incluir pacotes **azureml-core** e **azureml-dataprep [pandas, fusíveis]** na definição ambiental. Se a imagem de estivador personalizada for utilizada com user_managed_dependencies, o utilizador terá de instalar conda na imagem.
      
+ **Alterações interruptivas**
  + **azureml-pipeline-steps**
    + Depreifed o uso de azureml.dprep.Dataflow como um tipo válido de entrada para AutoMLConfig
  + **azureml-train-automl-client**
    + Depreifed o uso de azureml.dprep.Dataflow como um tipo válido de entrada para AutoMLConfig

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + Corrigiu o bug onde um aviso pode ser impresso durante `get_output` o pedido ao utilizador para desvalorizar o cliente.
    + Mac atualizado para confiar no cudatoolkit=9.0, uma vez que ainda não está disponível na versão 10.
    + Eliminação de restrições aos modelos profetas e xgboost quando treinados em computação remota.
    + Registo melhorado em AutoML
    + O tratamento de erros para a caracterização personalizada nas tarefas de previsão foi melhorado.
    + Funcionalidade adicional para permitir que os utilizadores incluam funcionalidades com atraso para gerar previsões.
    + Atualizações para mensagem de erro para visualizar corretamente o erro do utilizador.
    + Apoio a cv_split_column_names a utilizar com training_data
    + Atualize registar a mensagem de exceção e rastrear.
  + **azureml-automl-runtime**
    + Permitir guarda-costas para a previsão de imputações de valor em falta.
    + Registo melhorado em AutoML
    + Adição de tratamento de erros de grão fino para exceções de preparação de dados
    + Eliminação de restrições nos modelos phrophet e xgboost quando treinados em computação remota.
    + `azureml-train-automl-runtime` e `azureml-automl-runtime` têm dependências atualizadas para `pytorch` , e `scipy` `cudatoolkit` . agora apoiamos `pytorch==1.4.0` `scipy>=1.0.0,<=1.3.1` , e . `cudatoolkit==10.1.243` .
    + O tratamento de erros para a caracterização personalizada nas tarefas de previsão foi melhorado.
    + O mecanismo de deteção de frequências de conjunto de dados de previsão foi melhorado.
    + Problema fixo com a formação de modelos profeta em alguns conjuntos de dados.
    + A deteção automática do horizonte máximo durante a previsão foi melhorada.
    + Funcionalidade adicional para permitir que os utilizadores incluam funcionalidades com atraso para gerar previsões.
    +  Adiciona funcionalidade na função de previsão para permitir fornecer previsões para além do horizonte treinado sem retreinar o modelo de previsão.
    + Apoio a cv_split_column_names a utilizar com training_data
  + **azureml-contrib-automl-dnn-previsão**
    + Registo melhorado em AutoML
  + **azureml-contrib-mir**
    + Suporte adicional para serviços Windows em ManagedInferencing
    + Remova os antigos fluxos de trabalho mir tais como anexar mir compute, classe SingleModelMirWebservice - Limpar o perfil do modelo colocado no pacote contrib-mir
  + **azureml-contrib-pipeline-steps**
    + Pequena correção para suporte YAML
    + ParallelRunStep é lançado para Disponibilidade Geral - azureml.contrib.pipeline.steps tem um aviso de depreciação e é mover-se para azureml.pipeline.steps
  + **azureml-contrib-reinforcementlearning**
    + Ferramenta de teste de carga RL
    + O estimador RL tem incumprimentos inteligentes
  + **azureml-core**
    + Remova os antigos fluxos de trabalho mir tais como anexar mir compute, classe SingleModelMirWebservice - Limpar o perfil do modelo colocado no pacote contrib-mir
    + Fixo as informações fornecidas ao utilizador em caso de falha de perfis: incluiu o ID do pedido e reformula a mensagem para ser mais significativa. Adicionou um novo fluxo de trabalho de perfis a corredores de perfis
    + Texto de erro melhorado em caso de falhas na execução do Dataset.
    + Suporte CLI de ligação privada de espaço de trabalho adicionado.
    + Adicione um parâmetro opcional `invalid_lines` `Dataset.Tabular.from_json_lines_files` ao que permite especificar como lidar com linhas que contenham JSON inválido.
    + Vamos depreciar a criação de computação baseada em execução no próximo lançamento. Recomendamos a criação de um cluster Amlcompute real como alvo de computação persistente, e usar o nome do cluster como alvo de computação na sua configuração de execução. Consulte o caderno de exemplo aqui: aka.ms/amlcomputenb
    + Mensagens de erro melhoradas em caso de falhas na execução do Dataset.
  + **azureml-dataprep**
    + Deixou o aviso para atualizar a versão de pyarrow mais explícita.
    + Melhor tratamento de erros e mensagem devolvida em caso de falha na execução do fluxo de dados.
  + **azureml-interpret**
    + Atualizações de documentação para pacote de interpretação azureml.
    + Pacotes de interpretação fixa e cadernos compatíveis com a mais recente atualização de sklearn
  + **azureml-opendatasets**
    + devolva nenhum quando não houver dados devolvidos.
    + Melhorar o desempenho da to_pandas_dataframe.
  + **azureml-pipeline-core**
    + Correção rápida para ParallelRunStep onde o carregamento da YAML foi quebrado
    + ParallelRunStep é lançado para Disponibilidade Geral - azureml.contrib.pipeline.steps tem um aviso de depreciação e é mover-se para azureml.pipeline.steps - novas funcionalidades incluem: 1. Conjuntos de dados como PipelineParameter 2. Novo parâmetro run_max_retry 3. Nome de ficheiro de saída de saída append_row configurável
  + **azureml-pipeline-steps**
    + Precatado azureml.dprep.Dataflow como um tipo válido para dados de entrada.
    + Correção rápida para ParallelRunStep onde o carregamento da YAML foi quebrado
    + ParallelRunStep é lançado para Disponibilidade Geral - azureml.contrib.pipeline.steps tem um aviso de depreciação e é mover-se para azureml.pipeline.steps - novas funcionalidades incluem:
      + Conjuntos de dados como PipelineParameter
      + Novo run_max_retry de parâmetro
      + Nome de ficheiro de saída de saída append_row configurável
  + **azureml-telemetry**
    + Atualize registar a mensagem de exceção e rastrear.
  + **azureml-train-automl-client**
    + Registo melhorado em AutoML
    + Atualizações para mensagem de erro para visualizar corretamente o erro do utilizador.
    + Apoio a cv_split_column_names a utilizar com training_data
    + Precatado azureml.dprep.Dataflow como um tipo válido para dados de entrada.
    + Mac atualizado para confiar no cudatoolkit=9.0, uma vez que ainda não está disponível na versão 10.
    + Eliminação de restrições nos modelos phrophet e xgboost quando treinados em computação remota.
    + `azureml-train-automl-runtime` e `azureml-automl-runtime` têm dependências atualizadas para `pytorch` , e `scipy` `cudatoolkit` . agora apoiamos `pytorch==1.4.0` `scipy>=1.0.0,<=1.3.1` , e . `cudatoolkit==10.1.243` .
    + Funcionalidade adicional para permitir que os utilizadores incluam funcionalidades com atraso para gerar previsões.
  + **azureml-train-automl-runtime**
    + Registo melhorado em AutoML
    + Adição de tratamento de erros de grão fino para exceções de preparação de dados
    + Eliminação de restrições nos modelos phrophet e xgboost quando treinados em computação remota.
    + `azureml-train-automl-runtime` e `azureml-automl-runtime` têm dependências atualizadas para `pytorch` , e `scipy` `cudatoolkit` . agora apoiamos `pytorch==1.4.0` `scipy>=1.0.0,<=1.3.1` , e . `cudatoolkit==10.1.243` .
    + Atualizações para mensagem de erro para visualizar corretamente o erro do utilizador.
    + Apoio a cv_split_column_names a utilizar com training_data
  + **azureml-train-core**
    + Adicionou um novo conjunto de exceções específicas do HyperDrive. azureml.train.hyperdrive irá agora lançar exceções detalhadas.
  + **azureml-widgets**
    + AzureML Widgets não está a ser exibido no JupyterLab
  

## <a name="2020-05-11"></a>2020-05-11

### <a name="azure-machine-learning-sdk-for-python-v150"></a>Azure Machine Learning SDK para Python v1.5.0

+ **Novas funcionalidades**
  + **Funcionalidades de pré-visualização**
    + **azureml-contrib-reinforcementlearning**
        + O Azure Machine Learning está a lançar suporte de pré-visualização para a aprendizagem de reforço utilizando a estrutura [Ray.](https://ray.io) Permite `ReinforcementLearningEstimator` a formação de agentes de aprendizagem de reforço em toda a GPU e CPU metas de computação em Azure Machine Learning.

+ **Correções e melhorias de bugs**
  + **azure-cli-ml**
    + Corrige um registo de aviso acidentalmente deixado no meu pr anterior. O tronco foi usado para depurar e acidentalmente foi deixado para trás.
    + Correção de bugs: informar os clientes sobre falha parcial durante o perfis
  + **azureml-automl-core**
    + Acelere o modelo Profeta/AutoArima na previsão AutoML, permitindo a montagem paralela para as séries de tempo quando os conjuntos de dados têm séries de tempo múltiplas. Para beneficiar desta nova funcionalidade, recomenda-se definir "max_cores_per_iteration = -1" (isto é, utilizando todos os núcleos cpu disponíveis) em AutoMLConfig.
    + Fixe o KeyError na impressão de guarda-costas na interface da consola
    + Mensagem de erro fixa para experimentation_timeout_hours
    + Modelos de tensorflow precados para AutoML.
  + **azureml-automl-runtime**
    + Mensagem de erro fixa para experimentation_timeout_hours
    + Exceção não classificada fixa ao tentar deserializar da loja cache
    + Acelere o modelo Profeta/AutoArima na previsão AutoML, permitindo a montagem paralela para as séries de tempo quando os conjuntos de dados têm séries de tempo múltiplas.
    + Fixar a previsão com janela de rolamento ativada nos conjuntos de dados onde o conjunto de teste/previsão não contém um dos grãos do conjunto de treino.
    + Melhor tratamento dos dados em falta
    + Problema fixo com intervalos de previsão durante a previsão em conjuntos de dados, contendo séries de tempo, que não estão alinhadas no tempo.
    + Adicionou uma melhor validação da forma de dados para as tarefas de previsão.
    + Melhorou a deteção de frequência.
    + Criou uma melhor mensagem de erro se a validação cruzada dobrar para as tarefas de previsão não puder ser gerada.
    + Fixe a interface da consola para imprimir o guarda-costas de valor em falta corretamente.
    + A aplicação do tipo de dados verifica cv_split_indices entrada no AutoMLConfig.
  + **azureml-cli-comum**
    + Correção de bugs: informar os clientes sobre falha parcial durante o perfis
  + **azureml-contrib-mir**
    + Adiciona uma classe azureml.contrib.mir.revisionStatus que transmite informações sobre a revisão MIR atualmente implementada e a versão mais recente especificada pelo utilizador. Esta classe está incluída no objeto MirWebservice sob o atributo 'deployment_status'.
    + Permite atualização em Webservices do tipo MirWebservice e sua classe infantil SingleModelMirWebservice.
  + **azureml-contrib-reinforcementlearning**
    + Apoio adicional para Ray 0.8.3
    + AmlWindowsCompute só suporta ficheiros Azure como armazenamento montado
    + Rebatizado health_check_timeout para health_check_timeout_seconds
    + Corrigi algumas descrições de classe/método.
  + **azureml-core**
    + Conversões de blob de > ativadas em nuvens do Governo de Azure e da China.
    + Corrige bug para permitir que as funções do Leitor utilizem comandos CLI executados az ml para obter informações de execução
    + Removido registos desnecessários durante Azure ML Remote Runs com conjuntos de dados de entrada.
    + O RCranPackage suporta agora o parâmetro "versão" para a versão do pacote CRAN.
    + Correção de bugs: informar os clientes sobre falha parcial durante o perfis
    + Acrescentou o manuseamento de boias de estilo europeu para o núcleo de azureml.
    + Funcionalidades de ligação privada de espaço de trabalho ativadas em Azure ml sdk.
    + Ao criar um SeparadorDataset `from_delimited_files` utilizando, pode especificar se os valores vazios devem ser carregados como Nenhum ou como cadeia vazia definindo o argumento boolean `empty_as_string` .
    + Adicionou o manuseamento de boias de estilo europeu para conjuntos de dados.
    + Melhores mensagens de erro nas falhas de montagem do conjunto de dados.
  + **azureml-datadrift**
    + A consulta de resultados da Data Drift do SDK tinha um bug que não diferenciava as métricas mínimas, máximas e médias de características, resultando em valores duplicados. Corrigimos este bug prefixando o alvo ou a linha de base para os nomes métricos. Antes: duplicar min, máx. Depois: target_min, target_max, target_mean, baseline_min, baseline_max, baseline_mean.
  + **azureml-dataprep**
    + Melhore o manuseamento de ambientes de python restritos ao garantir .NET Dependências necessárias para a entrega de dados.
    + Criação de fluxo de dados fixos em ficheiro com registos vazios principais.
    + Opções de manuseamento de erros adicionadas `to_partition_iterator` para semelhantes a `to_pandas_dataframe` .
  + **azureml-interpret**
    + Limites reduzidos de comprimento do caminho da explicação para reduzir probabilidade de ultrapassar limite do Windows
    + Bugfix para explicações escassas criadas com o explicador de imitação usando um modelo linear de substituição.
  + **azureml-opendatasets**
    + A correção das colunas do MNIST é analisada como corda, que deve ser int.
  + **azureml-pipeline-core**
    + Permitindo que a opção regenerate_outputs ao utilizar um módulo que está incorporado num ModuleStep.
  + **azureml-train-automl-client**
    + Modelos de tensorflow precados para AutoML.
    + Os utilizadores de correção permitem a listagem de algoritmos não suportados em modo local
    + Doc corre para AutoMLConfig.
    + A aplicação do tipo de dados verifica cv_split_indices entrada no AutoMLConfig.
    + Problema fixo com autoML corre falhando em show_output
  + **azureml-train-automl-runtime**
    + A correção de um bug nas iterações do Ensemble que estava a impedir que o tempo de descarregamento do modelo fosse feito com sucesso.
  + **azureml-train-core**
    + Fixe tipografia em classe azureml.train.dnn.Nccl.
    + Suportando a versão 1.5 do PyTorch no Estimador PyTorch
    + Corrigir a questão de que a imagem-quadro não pode ser recolhida na região do Governo de Azure ao usar os estimadores-quadro de formação

  
## <a name="2020-05-04"></a>2020-05-04
**Nova experiência de caderno**

Agora pode criar, editar e partilhar cadernos e ficheiros de machine learning diretamente dentro da experiência web do estúdio de Azure Machine Learning. Você pode usar todas as aulas e métodos disponíveis em [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) de dentro destes cadernos Começar [aqui](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks)

**Novas funcionalidades introduzidas:**

+ Editor melhorado (editor do Mónaco) usado por VS Code 
+ Melhorias uI/UX
+ Barra de ferramentas celular
+ Novo portátil Toolbar e Controlos computativos
+ Barra de Estado do Caderno 
+ Comutação de Kernel Inline
+ Apoio r
+ Melhorias de acessibilidade e localização
+ Paleta de comando
+ Atalhos adicionais de teclado
+ Autosave
+ Melhoria do desempenho e fiabilidade

Aceda às seguintes ferramentas de autoria baseadas na web do estúdio:
    
| Ferramenta baseada na web  |     Descrição  |
|---|---|
| Cadernos do estúdio Azure ML   |     Primeira autoria em classe para ficheiros de portátil e suporte a toda a operação disponível no Azure ML Python SDK. | 

## <a name="2020-04-27"></a>2020-04-27

### <a name="azure-machine-learning-sdk-for-python-v140"></a>Azure Machine Learning SDK para Python v1.4.0

+ **Novas funcionalidades**
  + Os clusters AmlCompute suportam agora a criação de uma identidade gerida no cluster no momento do provisionamento. Basta especificar se gostaria de usar uma identidade atribuída ao sistema ou uma identidade atribuída ao utilizador, e passar um ID de identidade para este último. Em seguida, pode configurar permissões para aceder a vários recursos, como Armazenamento ou ACR, de forma a que a identidade do computação seja usada para aceder de forma segura aos dados, em vez de uma abordagem baseada em fichas que a AmlCompute emprega hoje em dia. Consulte a nossa referência SDK para obter mais informações sobre os parâmetros.
  

+ **Alterações interruptivas**
  + Os clusters AmlCompute apoiaram uma funcionalidade de Pré-visualização em torno da criação baseada em execução, que estamos planejando depreciar em duas semanas. Pode continuar a criar metas de computação persistentes, como sempre, utilizando a classe Amlcompute, mas a abordagem específica de especificar o identificador "amlcompute" como o alvo de computação em run config não será suportada num futuro próximo. 

+ **Correções e melhorias de bugs**
  + **azureml-automl-runtime**
    + Ativar o suporte para o tipo de identificação ao calcular o número de valores únicos numa coluna.
  + **azureml-core**
    + Melhor estabilidade ao ler a partir do Azure Blob Storage utilizando um SeparadorDataset.
    + Melhor documentação para o `grant_workspace_msi` parâmetro para `Datastore.register_azure_blob_store` .
    + Bug fixo com `datastore.upload` para suportar o argumento `src_dir` terminando com um ou `/` `\` .
    + Adicione uma mensagem de erro acionável ao tentar fazer upload para uma loja de dados de armazenamento Azure Blob que não tem uma chave de acesso ou ficha SAS.
  + **azureml-interpret**
    + Adicionado limite superior ao tamanho do ficheiro para os dados de visualização em explicações carregadas.
  + **azureml-train-automl-client**
    + Verificar explicitamente se label_column_name & weight_column_name parâmetros para que o AutoMLConfig seja de tipo de cadeia.
  + **azureml-contrib-pipeline-steps**
    + ParallelRunStep agora suporta o conjunto de dados como parâmetro de pipeline. O utilizador pode construir o pipeline com conjunto de dados de amostra e pode alterar o conjunto de dados de entrada do mesmo tipo (ficheiro ou tabular) para a execução de novo pipeline.

  
## <a name="2020-04-13"></a>2020-04-13

### <a name="azure-machine-learning-sdk-for-python-v130"></a>Azure Machine Learning SDK para Python v1.3.0

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + Adicionou telemetria adicional em torno de operações pós-treino.
    + Acelera o treino automático arima utilizando a soma condicional de treinamento quadrados (CSS) para uma série de comprimento superior a 100. O comprimento utilizado é armazenado como a ARIMA_TRIGGER_CSS_TRAINING_LENGTH constante c/na classe TimeSeriesInternal em /src/azureml-automl-core/azureml/automl/core/shared/constants.py
    + O registo de registo de previsões foi melhorado, agora mais informações sobre a fase em curso serão mostradas no registo
    + Não permitido target_rolling_window_size a ser definido para valores inferiores a 2
  + **azureml-automl-runtime**
    + Melhorou a mensagem de erro mostrada quando são encontrados os tempos duplicados.
    + Não permitido target_rolling_window_size a ser definido para valores inferiores a 2.
    + Corrigi a falha de imputação do lag. A questão foi causada pelo número insuficiente de observações necessárias para decompor sazonalmente uma série. Os dados "desaquealizados" são utilizados para calcular uma função de autocorrelação parcial (PACF) para determinar o comprimento do lag.
    + Finalização da coluna ativada com personalização para tarefas de previsão por config de exibição. Numérico e Categórico como propósito de coluna para as tarefas de previsão é agora suportado.
    + Coluna de queda ativada apresentando a personalização para tarefas de previsão por config de exibição.
    + Habilitar a personalização da imputação para tarefas de previsão através da caracterização config. A imputação de valor constante para coluna-alvo e média, mediana, most_frequent e a imputação constante de valor para os dados de formação são agora suportadas.
  + **azureml-contrib-pipeline-steps**
    + Aceite nomes de cálculo de cordas a serem passados para ParallelRunConfig
  + **azureml-core**
    +  API (new_name) do Ambiente Adicionado (new_name) para criar uma cópia do objeto ambiente
    +  Environment.docker.base_dockerfile aceita o filepath. Se for possível resolver um ficheiro, o conteúdo será lido em base_dockerfile propriedade ambiental
    + Repor automaticamente valores mutuamente exclusivos para base_image e base_dockerfile quando o utilizador define manualmente um valor em Environment.docker
    + Adicionado user_managed bandeira em RSection que indica se o ambiente é gerido pelo utilizador ou pela AzureML.
    + Conjunto de dados: Falha de descarregamento de conjunto de dados fixo se a trajetória de dados conter caracteres unicódigo.
    + Conjunto de dados: Mecanismo de cache de conjunto de dados melhorado para respeitar o requisito mínimo de espaço do disco no Azure Machine Learning Compute, o que evita tornar o nó inutilizável e fazer com que o trabalho seja cancelado.
    + Conjunto de dados: Adicionamos um índice para a coluna de séries de tempo quando acede a um conjunto de dados da série de tempo como um dataframe de pandas, que é usado para acelerar o acesso ao acesso de dados baseado em séries de tempo.  Anteriormente, o índice recebeu o mesmo nome que a coluna timetamp, confundindo os utilizadores sobre qual é a coluna de marca de tempo real e qual é o índice. Agora não damos nenhum nome específico ao índice, uma vez que não deve ser usado como coluna. 
    + Conjunto de dados: Emissão de autenticação de conjunto de dados fixo na nuvem soberana.
    + Conjunto de dados: Falha fixa `Dataset.to_spark_dataframe` dos conjuntos de dados criados a partir das datas do Azure PostgreSQL.
  + **azureml-interpret**
    + Acrescente-se pontuações globais à visualização se os valores de importância local forem escassos
    + Azureml-interprete atualizado para usar a interpret-community 0.9.*
    + Problema fixo com a explicação de descarregamento que tinha escassos dados de avaliação
    + Suporte adicional do formato escasso do objeto de explicação em AutoML
  + **azureml-pipeline-core**
    + Apoiar o ComputeInstance como alvo de computação em oleodutos
  + **azureml-train-automl-client**
    + Adicionou telemetria adicional em torno de operações pós-treino.
    + Fixou a regressão na paragem precoce
    + Precatado azureml.dprep.Dataflow como um tipo válido para dados de entrada.
    +  Alterar o tempo de experiência autoML predefinido para seis dias.
  + **azureml-train-automl-runtime**
    + Adicionou telemetria adicional em torno de operações pós-treino.
    + acrescentou escasso autoML final para o suporte final
  + **azureml-opendatasets**
    + Adicione telemetria adicional para monitor de serviço.
    + Ativar a porta da frente para o blob aumentar a estabilidade 

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Azure Machine Learning SDK para Python v1.2.0

+ **Alterações interruptivas**
  + Suporte de queda para python 2.7

+ **Correções e melhorias de bugs**
  + **azure-cli-ml**
    + Adiciona "--subscrição-id" aos `az ml model/computetarget/service` comandos no CLI
    + Adicionar suporte para a passagem de chaves geridas pelo cliente (CMK) vault_url, key_name e key_version para a implantação de ACI
  + **azureml-automl-core** 
    + Ativação de imputação personalizada com valor constante para tarefas de previsão de dados X e y.
    + Corrigiu o problema com a apresentação de mensagens de erro ao utilizador.    
  + **azureml-automl-runtime**
    + Corrigiu o problema com a previsão nos conjuntos de dados, contendo grãos com apenas uma linha
    + Diminuiu a quantidade de memória exigida pelas tarefas de previsão.
    + Adicione melhores mensagens de erro se a coluna de tempo tiver um formato incorreto.
    + Ativação de imputação personalizada com valor constante para tarefas de previsão de dados X e y.
  + **azureml-core**
    + Apoio adicional ao carregamento do ServicePrincipal a partir de variáveis ambientais: AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID e AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + Introduziu um novo parâmetro `support_multi_line` `Dataset.Tabular.from_delimited_files` para: Por padrão ( `support_multi_line=False` ), todas as quebras de linha, incluindo as dos valores de campo citados, serão interpretadas como um recorde. Ler dados desta forma é mais rápido e otimizado para execução paralela em vários núcleos de CPU. No entanto, pode resultar na produção silenciosa de mais registos com valores de campo desalinhados. Isto deve ser definido `True` para quando os ficheiros delimitados são conhecidos por conter quebras de linha citadas.
    + Adicionou a capacidade de registar a ADLS Gen2 no Azure Machine Learning CLI
    + O parâmetro renomeado 'fine_grain_timestamp' para 'timetamp' e parâmetro 'coarse_grain_timestamp' para 'partition_timestamp' para o método with_timestamp_columns no SeparadorSet para melhor refletir a utilização dos parâmetros.
    + Aumentou o comprimento do nome da experiência máxima para 255.
  + **azureml-interpret**
    + Azureml-interprete atualizado para interpretar a comunidade 0.7.*
  + **azureml-sdk**
    + Alterando para dependências com versão compatível Tilde para o suporte de remendação em lançamentos pré-lançamento e estáveis.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Azure Machine Learning SDK para Python v1.1.5

+ **Depreciação de recurso**
  + **Python 2.7**
    + Última versão para apoiar python 2.7

+ **Alterações interruptivas**
  + **Semantic Versioning 2.0.0** (Controlo de Versões Semântico 2.0.0)
    + Começando pela versão 1.1 Azure ML Python SDK adota a Versão Semântica 2.0.0. [Leia mais aqui.](https://semver.org/) Todas as versões subsequentes seguirão o novo esquema de numeração e o contrato de versão semântica. 

+ **Correções e melhorias de bugs**
  + **azure-cli-ml**
    + Mude o nome de comando CLI do ponto final de 'az ml endpoint aks' para 'az ml endpoint real time' para obter consistência.
    + atualizar instruções de instalação do CLI para cli de ramo estável e experimental
    + O perfil de instância única foi fixado para produzir uma recomendação e foi disponibilizado no core sdk.
  + **azureml-automl-core**
    + Ativado a inferência do modo Batch (tomando várias linhas uma vez) para modelos AutoML ONNX
    + Melhorou a deteção da frequência nos conjuntos de dados, faltando dados ou contendo pontos de dados irregulares
    + Adicionou a capacidade de remover pontos de dados que não estão em conformidade com a frequência dominante.
    + Alterou a entrada do construtor para fazer uma lista de opções para aplicar as opções de imputação para as colunas correspondentes.
    + A verificação de erros foi melhorada.
  + **azureml-automl-runtime**
    + Corrigiu o problema com o erro lançado se o grão não estivesse presente no conjunto de treino apareceu no conjunto de testes
    + Removido o requisito de y_query durante a pontuação no serviço de previsão
    + Corrigiu o problema com a previsão quando o conjunto de dados contém grãos curtos com lacunas de longo tempo.
    + Corrigiu o problema quando o horizonte de max automático é ligado e a coluna de data contém datas sob a forma de cordas. Foram adicionadas mensagens de conversão e erro adequadas para quando a conversão até à data não for possível
    + Utilização de NumPy e SciPy nativos para serializar e deserizar dados intermédios para fileCacheStore (usado para corridas autoML locais)
    + Consertado um inseto onde as corridas de crianças falhadas podem ficar presas no estado de running.
    + Maior velocidade de alocização.
    + Fixou a verificação de frequência durante a pontuação, agora as tarefas de previsão não requerem uma equivalência rigorosa de frequência entre o conjunto de comboios e testes.
    + Alterou a entrada do construtor para fazer uma lista de opções para aplicar as opções de imputação para as colunas correspondentes.
    + Erros fixos relacionados com a seleção do tipo lag.
    + Corrigiu o erro não classificado levantado nos conjuntos de dados, tendo grãos com a única linha
    + Corrigiu o problema com lentidão de deteção de frequência.
    + Corrige um erro no manuseamento de exceções AutoML que causou a verdadeira razão para a falta de treino ser substituída por um AttributeError.
  + **azureml-cli-comum**
    + O perfil de instância única foi fixado para produzir uma recomendação e foi disponibilizado no core sdk.
  + **azureml-contrib-mir**
    + Adiciona funcionalidade na classe MirWebservice para recuperar o Token de Acesso
    + Use auth token para MirWebservice por padrão durante chamada MirWebservice.run () - Apenas refresque se a chamada falhar
    + A implementação do serviço web Mir requer agora Skus adequados [Standard_DS2_v2, Standard_F16, Standard_A2_v2] em vez de [Ds2v2, A2v2 e F16] respectivamente.
  + **azureml-contrib-pipeline-steps**
    + Parâmetro opcional side_inputs adicionado ao ParallelRunStep. Este parâmetro pode ser utilizado para montar pasta no recipiente. Os tipos suportados atualmente são DataReference e PipelineData.
    + Os parâmetros passados em ParallelRunConfig podem ser substituídos passando os parâmetros do gasoduto agora. Novos parâmetros de gasoduto suportados aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count e aml_process_count_per_node já fazem parte de lançamentos anteriores).
  + **azureml-core**
    + Os Webservices AzureML implantados passarão a estar por defeito no `INFO` registo. Isto pode ser controlado definindo a `AZUREML_LOG_LEVEL` variável ambiente no serviço implantado.
    + Python sdk usa serviço de descoberta para usar o ponto final 'api' em vez de 'pipelines'.
    + Troque para as novas rotas em todas as chamadas da SDK.
    + Alterou o encaminhamento de chamadas para o ModelManagementService para uma nova estrutura unificada.
      + Tornou público o método de atualização do espaço de trabalho.
      + Adicionado image_build_compute parâmetro no método de atualização do espaço de trabalho para permitir ao utilizador atualizar o cálculo para a construção de imagens.
    + Adicionou mensagens de depreciação ao velho fluxo de trabalho de perfis. Perfis fixos cpu e limites de memória.
    + Acrescentou rsecção como parte do Ambiente para gerir empregos R.
    + Validação adicionada `Dataset.mount` para aumentar o erro quando a origem do conjunto de dados não está acessível ou não contém quaisquer dados.
    + Adicionado `--grant-workspace-msi-access` como um parâmetro adicional para o CLI da Datastore para registar o Recipiente Azure Blob que lhe permitirá registar o Blob Container que está por trás de um VNet.
    + O perfil de instância única foi fixado para produzir uma recomendação e foi disponibilizado no core sdk.
    + Corrigiu o problema em aks.py _deploy.
    + Valida a integridade dos modelos que estão a ser carregados para evitar falhas de armazenamento silenciosos.
    + O utilizador pode agora especificar um valor para a chave auth ao regenerar chaves para webservices.
    + Bug fixo onde as letras maiúsculas não podem ser usadas como o nome de entrada do conjunto de dados.
  + **azureml-padrão**
    + `azureml-dataprep` será agora instalado como parte de `azureml-defaults` . Já não é necessário instalar manualmente a preparação de dados [fusível] em alvos de computação para montar conjuntos de dados.
  + **azureml-interpret**
    + Azureml-interprete atualizado para interpretar-comunidade 0.6.*
    + Azureml-interpret atualizado para depender da comunidade interpretação 0.5.0
    + Adicionou exceções ao estilo azureml ao azureml-interpret
    + Serialização de explicação deepScoring fixa para modelos de keras
  + **azureml-mlflow**
    + Adicione suporte para nuvens soberanas azureml.mlflow
  + **azureml-pipeline-core**
    + O caderno de pontuação do lote de pipeline agora usa ParallelRunStep
    + Corrigiu um bug onde os resultados do PythonScriptStep poderiam ser incorretamente reutilizados apesar de alterar a lista de argumentos
    + Adicionou a capacidade de definir o tipo de colunas ao chamar os métodos parse_* em `PipelineOutputFileDataset`
  + **azureml-pipeline-steps**
    + Mudou o `AutoMLStep` `azureml-pipeline-steps` pacote. Depreceu o `AutoMLStep` `azureml-train-automl-runtime` interior.
    + Exemplo de documentação adicionado para conjunto de dados como entrada PythonScriptStep
  + **azureml-tensorboard**
    + atualizado azureml-tensorboard para suportar tensorflow 2.0
    + Mostre o número correto da porta ao utilizar uma porta de tensorboard personalizada numa Instância computetiva
  + **azureml-train-automl-client**
    + Corrigiu um problema em que determinadas embalagens podem ser instaladas em versões incorretas em execuções remotas.
    + fixo FeaturizationConfig que se sobrepuse que filtra a configuração personalizada.
  + **azureml-train-automl-runtime**
    + Corrigiu o problema com a deteção de frequências nos percursos remotos
    + Mudou o `AutoMLStep` `azureml-pipeline-steps` pacote. Depreceu o `AutoMLStep` `azureml-train-automl-runtime` interior.
  + **azureml-train-core**
    + Suportando a versão 1.4 do PyTorch no Estimador PyTorch
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning SDK para Python v1.1.2rc0 (Pré-lançamento)

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + Ativado a inferência do modo Batch (tomando várias linhas uma vez) para modelos AutoML ONNX
    + Melhorou a deteção da frequência nos conjuntos de dados, faltando dados ou contendo pontos de dados irregulares
    + Adicionou a capacidade de remover pontos de dados que não estão em conformidade com a frequência dominante.
  + **azureml-automl-runtime**
    + Corrigiu o problema com o erro lançado se o grão não estivesse presente no conjunto de treino apareceu no conjunto de testes
    + Removido o requisito de y_query durante a pontuação no serviço de previsão
  + **azureml-contrib-mir**
    + Adiciona funcionalidade na classe MirWebservice para recuperar o Token de Acesso
  + **azureml-core**
    + Os Webservices AzureML implantados passarão a estar por defeito no `INFO` registo. Isto pode ser controlado definindo a `AZUREML_LOG_LEVEL` variável ambiente no serviço implantado.
    + Corrija a itering `Dataset.get_all` para devolver todos os conjuntos de dados registados no espaço de trabalho.
    + Melhorar a mensagem de erro quando o tipo inválido é passado para `path` o argumento de APIs de criação de conjuntos de dados.
    + Python sdk usa serviço de descoberta para usar o ponto final 'api' em vez de 'pipelines'.
    + Troque para as novas rotas em todas as chamadas da SDK
    + Altera o encaminhamento de chamadas para o ModelManagementService para uma nova estrutura unificada
      + Tornou público o método de atualização do espaço de trabalho.
      + Parâmetro image_build_compute adicionado no método de atualização do espaço de trabalho para permitir ao utilizador atualizar o cálculo para a construção de imagens
    +  Adicionou mensagens de depreciação ao velho fluxo de trabalho de perfis. Limites de perfis fixos e de memória
  + **azureml-interpret**
    + atualizar azureml-interpret para interpret-community 0.6.*
  + **azureml-mlflow**
    + Adicione suporte para nuvens soberanas azureml.mlflow
  + **azureml-pipeline-steps**
    + Mudou `AutoMLStep` `azureml-pipeline-steps package` o. Depreceu o `AutoMLStep` `azureml-train-automl-runtime` interior.
  + **azureml-train-automl-client**
    + Corrigiu um problema em que determinadas embalagens podem ser instaladas em versões incorretas em execuções remotas.
  + **azureml-train-automl-runtime**
    + Corrigiu o problema com a deteção de frequências nos percursos remotos
    + Mudou `AutoMLStep` `azureml-pipeline-steps package` o. Depreceu o `AutoMLStep` `azureml-train-automl-runtime` interior.
  + **azureml-train-core**
    + Mudou `AutoMLStep` `azureml-pipeline-steps package` o. Depreceu o `AutoMLStep` `azureml-train-automl-runtime` interior.

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Azure Machine Learning SDK para Python v1.1.1rc0 (Pré-lançamento)

+ **Correções e melhorias de bugs**
  + **azure-cli-ml**
    + O perfil de instância única foi fixado para produzir uma recomendação e foi disponibilizado no core sdk.
  + **azureml-automl-core**
    + A verificação de erros foi melhorada.
  + **azureml-automl-runtime**
    + Corrigiu o problema com a previsão quando o conjunto de dados contém grãos curtos com lacunas de longo tempo.
    + Corrigiu o problema quando o horizonte de max automático é ligado e a coluna de data contém datas sob a forma de cordas. Acrescentámos uma conversão adequada e um erro sensato se a conversão até à data não for possível
    + Utilização de NumPy e SciPy nativos para serializar e deserizar dados intermédios para fileCacheStore (usado para corridas autoML locais)
    + Consertado um inseto onde as corridas de crianças falhadas podem ficar presas no estado de running.
  + **azureml-cli-comum**
    + O perfil de instância única foi fixado para produzir uma recomendação e foi disponibilizado no core sdk.
  + **azureml-core**
    + Adicionado `--grant-workspace-msi-access` como um parâmetro adicional para o CLI da Datastore para registar o Contentor Azure Blob que lhe permitirá registar o Blob Container que está por trás de um VNet
    + O perfil de instância única foi fixado para produzir uma recomendação e foi disponibilizado no core sdk.
    + Corrigiu o problema em aks.py _deploy
    + Valida a integridade dos modelos que estão a ser carregados para evitar falhas de armazenamento silenciosos.
  + **azureml-interpret**
    + acrescentou exceções de estilo azureml para azureml-interpret
    + serialização de explicações deepScoring fixa para modelos de keras
  + **azureml-pipeline-core**
    + O caderno de pontuação do lote de pipeline agora usa ParallelRunStep
  + **azureml-pipeline-steps**
    + Mudou o `AutoMLStep` `azureml-pipeline-steps` pacote. Depreceu o `AutoMLStep` `azureml-train-automl-runtime` interior.
  + **azureml-contrib-pipeline-steps**
    + Parâmetro opcional side_inputs adicionado ao ParallelRunStep. Este parâmetro pode ser utilizado para montar pasta no recipiente. Os tipos suportados atualmente são DataReference e PipelineData.
  + **azureml-tensorboard**
    + atualizado azureml-tensorboard para suportar tensorflow 2.0
  + **azureml-train-automl-client**
    + fixo FeaturizationConfig que se sobrepuse que filtra a configuração personalizada.
  + **azureml-train-automl-runtime**
    + Mudou o `AutoMLStep` `azureml-pipeline-steps` pacote. Depreceu o `AutoMLStep` `azureml-train-automl-runtime` interior.
  + **azureml-train-core**
    + Suportando a versão 1.4 do PyTorch no Estimador PyTorch
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Azure Machine Learning SDK para Python v1.1.0rc0 (Pré-lançamento)

+ **Alterações interruptivas**
  + **Semantic Versioning 2.0.0** (Controlo de Versões Semântico 2.0.0)
    + Começando pela versão 1.1 Azure ML Python SDK adota a Versão Semântica 2.0.0. [Leia mais aqui.](https://semver.org/) Todas as versões subsequentes seguirão o novo esquema de numeração e o contrato de versão semântica. 
  
+ **Correções e melhorias de bugs**
  + **azureml-automl-runtime**
    + Maior velocidade de alocização.
    + Fixo a verificação de frequência durante a pontuação, agora nas tarefas de previsão não exigimos uma equivalência rigorosa de frequência entre o conjunto de comboios e testes.
  + **azureml-core**
    + O utilizador pode agora especificar um valor para a chave auth ao regenerar chaves para webservices.
  + **azureml-interpret**
    + Azureml-interpret atualizado para depender da comunidade interpretação 0.5.0
  + **azureml-pipeline-core**
    + Corrigiu um bug onde os resultados do PythonScriptStep poderiam ser incorretamente reutilizados apesar de alterar a lista de argumentos
  + **azureml-pipeline-steps**
    + Exemplo de documentação adicionado para conjunto de dados como entrada PythonScriptStep
  + **azureml-contrib-pipeline-steps**
    + Os parâmetros passados em ParallelRunConfig podem ser substituídos passando os parâmetros do gasoduto agora. Novos parâmetros de gasoduto suportados aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count e aml_process_count_per_node já fazem parte de lançamentos anteriores).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK para Python v1.0.85

+ **Novas funcionalidades**
  + **azureml-core**
    + Obtenha a atual limitação de utilização e quota de quota para os recursos AmlCompute num dado espaço de trabalho e subscrição
  
  + **azureml-contrib-pipeline-steps**
    + Permitir ao utilizador passar o conjunto de dados tabulares como resultado intermédio do passo anterior para o passo de corrida paralelo

+ **Correções e melhorias de bugs**
  + **azureml-automl-runtime**
    + Removido a exigência da coluna y_query no pedido ao serviço de previsão implantado. 
    + O "y_query" foi removido da secção de pedidos de serviço de portátil do Dominick's Orange Juice.
    + Corrigiu o erro impedindo a previsão nos modelos implantados, operando em conjuntos de dados com colunas de hora de data.
    + Acrescentou o Coeficiente de Correlação Matthews como uma métrica de classificação, tanto para a classificação binária como multiclasse.
  + **azureml-contrib-interpret**
    + Os explicadores de texto removidos do azureml-contrib-interpret como explicação de texto foi movido para o repo interpret-text que será lançado em breve.
  + **azureml-core**
    + Conjunto de dados: as utilizações para conjunto de dados de ficheiros já não dependem de numpy e pandas a serem instalados no env python.
    + Alterada LocalWebservice.wait_for_deployment para verificar o estado do contentor local do Docker antes de tentar localizar o seu ponto final de saúde, reduzindo consideravelmente o tempo necessário para reportar uma implantação falhada.
    + Fixo a inicialização de uma propriedade interna utilizada no LocalWebservice.reload() quando o objeto de serviço é criado a partir de uma implementação existente usando o construtor LocalWebservice().
    + Mensagem de erro editada para esclarecimento.
    + Adicionou um novo método chamado get_access_token() ao AksWebservice que devolverá o objeto AksServiceAccessToken, que contém o token de acesso, atualização após o tempo, expiração no tempotamp e tipo de token. 
    + Depreifed método de get_token existente no AksWebservice à medida que o novo método devolve toda a informação que este método devolve.
    + Saída modificada do serviço az ml obter acesso-token comando. Rebatizado token to accessToken and refreshBy to refreshAfter. Propriedades de expiração e tokenType adicionadas.
    + get_ative_runs fixo
  + **azureml-explain-modelo**
    + shap atualizado para 0.33.0 e interpretação-comunidade para 0.4.*
  + **azureml-interpret**
    + shap atualizado para 0.33.0 e interpretação-comunidade para 0.4.*
  + **azureml-train-automl-runtime**
    + Acrescentou o Coeficiente de Correlação Matthews como uma métrica de classificação, tanto para a classificação binária como multiclasse.
    + Deprecar a bandeira pré-processamento do código e substituído por aguardização -aguarde é on por padrão

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK para Python v1.0.83

+ **Novas funcionalidades**
  + Conjunto de dados: Adicione duas opções `on_error` e `out_of_range_datetime` `to_pandas_dataframe` falhe quando os dados têm valores de erro em vez de os encher com `None` .
  + Espaço de trabalho: Adicionou a `hbi_workspace` bandeira para espaços de trabalho com dados sensíveis que permitem encriptação adicional e desativa diagnósticos avançados em espaços de trabalho. Também adicionamos suporte para trazer as suas próprias chaves para a instância de DB do Cosmos associada, especificando os `cmk_keyvault` parâmetros e `resource_cmk_uri` parâmetros ao criar um espaço de trabalho, o que cria uma instância de DB cosmos na sua subscrição enquanto fornece o seu espaço de trabalho. [Leia mais aqui.](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-cosmos-db)

+ **Correções e melhorias de bugs**
  + **azureml-automl-runtime**
    + Corrigiu uma regressão que fez com que um TypeError fosse levantado ao executar o AutoML nas versões Python abaixo de 3.5.4.
  + **azureml-core**
    + O bug fixo `datastore.upload_files` dentro era um caminho relativo que não começou com não foi capaz de ser `./` usado.
    + Mensagens de depreciação adicionadas para todos os caminhos de código de classe de imagem
    + Construção de URL de gestão de modelos fixos para a região Azure China 21Vianet.
    + Problema fixo em que os modelos que utilizam source_dir não podiam ser embalados para funções Azure.    
    + Acrescentou uma opção para [Environment.build_local()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true) para empurrar uma imagem para o registo do espaço de trabalho AzureML
    + Atualize o SDK para utilizar a nova biblioteca simbólica na sinapse Azure de forma compatível com as costas.
  + **azureml-interpret**
    + Bug fixo onde Nenhum foi devolvido quando não havia explicações disponíveis para download. Agora aumenta uma exceção, combinando o comportamento em outro lugar.
  + **azureml-pipeline-steps**
    + Não permitida a passagem `DatasetConsumptionConfig` s para o parâmetro quando o será usado em um `Estimator` `inputs` `Estimator` `EstimatorStep` .
  + **azureml-sdk**
    + Adicionado cliente AutoML ao pacote azureml-sdk, permitindo que as corridas remotas de AutoML sejam submetidas sem instalar o pacote AutoML completo.
  + **azureml-train-automl-client**
    + Alinhamento corrigido na saída da consola para corre a AutoML
    + Corrigiu um bug onde a versão incorreta dos pandas pode ser instalada em amlcompute remoto.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK para Python v1.0.81

+ **Correções e melhorias de bugs**
  + **azureml-contrib-interpret**
    + deferir a dependência shap para interpretar-comunidade de azureml-interpret
  + **azureml-core**
    + O alvo do cálculo pode agora ser especificado como um parâmetro para os objetos config de implementação correspondentes. Este é especificamente o nome do alvo de computação para implementar, não o objeto SDK.
    + Adicionou informações criadas por informações a objetos de modelo e serviço. Pode ser acedido through.created_by
    + ContainerImage.run fixo,, que não estava a configurar corretamente a porta HTTP do contentor Docker.
    + Tornar `azureml-dataprep` opcional para o comando `az ml dataset register` CLI
    + Corrigiu um erro onde `TabularDataset.to_pandas_dataframe` voltaria incorretamente a um leitor alternativo e imprimisse um aviso.
  + **azureml-explain-modelo**
    + deferir a dependência shap para interpretar-comunidade de azureml-interpret
  + **azureml-pipeline-core**
    + Acrescentou um novo passo de `NotebookRunnerStep` oleoduto, para executar um caderno local como um passo em pipeline.
    + Removido funções de get_all prevadizadas para Funções, Horários e PipelineEndpoints publicados
  + **azureml-train-automl-client**
    + Começou a depreciação de data_script como entrada para o AutoML.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK para Python v1.0.79

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + Removido a caracterizaçãoConfig a ser registado
      + Registo atualizado para registar apenas "auto"/"off"/"customized".
  + **azureml-automl-runtime**
    + Apoio adicional aos pandas. Séries e pandas. Categórico para detetar o tipo de dados da coluna. Anteriormente apenas suportava numpy.ndarray
      + Alterações de código relacionadas adicionadas para lidar corretamente com o imótipo categórico.
    + A interface de função prevista foi melhorada: o parâmetro y_pred foi tornado facultativo. - Os docstrings foram melhorados.
  + **azureml-contrib-dataset**
    + Corrigiu um erro onde os conjuntos de dados rotulados não podiam ser montados.
  + **azureml-core**
    + Correção de insetos para `Environment.from_existing_conda_environment(name, conda_environment_name)` . O utilizador pode criar uma instância de Ambiente que é uma réplica exata do ambiente local
    + Alterou os métodos de datasets relacionados com séries de tempo `include_boundary=True` por padrão.
  + **azureml-train-automl-client**
    + Problema fixo em que os resultados da validação não são impressos quando a saída do show é definida como falsa.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK para Python v1.0.76

+ **Alterações interruptivas**
  + Problemas de upgrade Azureml-Train-AutoML
    + A atualização para>azureml-train-automl =1.0.76 de azureml-train-automl<1.0.76 pode causar instalações parciais, fazendo com que algumas importações automl falhem. Para resolver isto, pode executar o script de configuração encontrado em https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd . Ou se estiver a usar o pip diretamente, pode:
      + "pip install --upgrade azureml-train-automl"
      + "pip install --ignore-instalado azureml-train-automl-client"
    + ou pode desinstalar a versão antiga antes de atualizar
      + "pip desinstalar azureml-train-automl"
      + "pip instalar azureml-train-automl"

+ **Correções e melhorias de bugs**
  + **azureml-automl-runtime**
    + A AutoML terá agora em conta as classes verdadeiras e falsas no cálculo das métricas médias de escala para tarefas de classificação binária.
    + Moveu o código de aprendizagem e formação da máquina em AzureML-AutoML-Core para um novo pacote AzureML-AutoML-Runtime.
  + **azureml-contrib-dataset**
    + Ao ligar `to_pandas_dataframe` para um conjunto de dados rotulado com a opção de descarregamento, pode agora especificar se deve substituir ou não os ficheiros existentes.
    + Ao ligar `keep_columns` ou resultar em uma série de `drop_columns` tempo, etiqueta ou coluna de imagem ser largada, as capacidades correspondentes também serão largadas para o conjunto de dados.
    + Corrigiu um problema com o carregador de pytorch para a tarefa de deteção de objetos.
  + **azureml-contrib-interpret**
    + Removido o widget do painel de explicação do azureml-contrib-interpret, alterado pacote para referenciar o novo em interpret_community
    + Versão atualizada da comunidade interpretação para 0.2.0
  + **azureml-core**
    + Melhorar o desempenho de `workspace.datasets` .
    + Adicionou a capacidade de registar a Azure SQL Database Datastore usando o nome de utilizador e a autenticação de senha
    + Correção para carregamento De Configurações de Execução de caminhos relativos.
    + Ao ligar `keep_columns` ou resultar na queda de uma coluna de `drop_columns` séries de tempo, as capacidades correspondentes também serão retiradas para o conjunto de dados.
  + **azureml-interpret**
    + versão atualizada da interpretação-comunidade para 0.2.0
  + **azureml-pipeline-steps**
    + Valores suportados documentados para `runconfig_pipeline_params` os passos do gasoduto de aprendizagem de máquinas Azure.
  + **azureml-pipeline-core**
    + Adicionou opção CLI para descarregar a saída em formato json para comandos Pipeline.
  + **azureml-train-automl**
    + Divida a AzureML-Train-AutoML em dois pacotes, um pacote de clientes AzureML-Train-AutoML-Client e um pacote de formação ML AzureML-Train-AutoML-Runtime
  + **azureml-train-automl-client**
    + Adicionou um cliente fino para submeter experiências AutoML sem precisar de instalar quaisquer dependências de aprendizagem de máquinas localmente.
    + Registo fixo de lags automaticamente detetados, tamanhos das janelas rolantes e horizontes máximos nos percursos remotos.
  + **azureml-train-automl-runtime**
    + Adicionou um novo pacote AutoML para isolar a aprendizagem automática e os componentes de tempo de funcionamento do cliente.
  + **azureml-contrib-train-rl**
    + Acrescentou apoio à aprendizagem de reforço na SDK.
    + Adicionou suporte AmlWindowsCompute em RL SDK.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK para Python v1.0.74

  + **Funcionalidades de pré-visualização**
    + **azureml-contrib-dataset**
      + Depois de importar conjunto de dados azureml-contrib, pode ligar `Dataset.Labeled.from_json_lines` em vez de criar um conjunto de `._Labeled` dados rotulado.
      + Ao ligar `to_pandas_dataframe` para um conjunto de dados rotulado com a opção de descarregamento, pode agora especificar se deve substituir ou não os ficheiros existentes.
      + Ao ligar `keep_columns` ou resultar em uma série de `drop_columns` tempo, etiqueta ou coluna de imagem ser largada, as capacidades correspondentes também serão largadas para o conjunto de dados.
      + Problemas fixos com o carregador PyTorch ao ligar `dataset.to_torchvision()` .

+ **Correções e melhorias de bugs**
  + **azure-cli-ml**
    + Model Profiling adicionado ao CLI de pré-visualização.
    + Correções que quebram a mudança no Azure Storage fazendo com que o CLI AzureML falhe.
    + Tipo de balançador de carga adicionado a MLC para tipos AKS
  + **azureml-automl-core**
    + Corrigiu o problema com a deteção do horizonte máximo nas séries temporais, tendo em falta valores e múltiplos grãos.
    + Corrigiu o problema com falhas durante a geração de divisões de validação cruzada.
    + Substitua esta secção por uma mensagem no formato de marcação a aparecer nas notas de lançamento: -Melhor manuseamento de grãos curtos nos conjuntos de dados de previsão.
    + Corrigiu o problema com a máscara de algumas informações do utilizador durante a sessão de registo. -Registo melhorado dos erros durante as operações de previsão.
    + Adicionar psutil como uma dependência conda ao ficheiro de implantação yml autogerado.
  + **azureml-contrib-mir**
    + Correções que quebram a mudança no Azure Storage fazendo com que o CLI AzureML falhe.
  + **azureml-core**
    + Corrige um bug que fez com que os modelos implantados nas Funções Azure produzissem 500s.
    + Corrigiu um problema em que o ficheiro amlignore não foi aplicado em instantâneos.
    + Adicionou um novo amlcompute.get_ative_runs da API que devolve um gerador para correr e fazer fila num dado amlcompute.
    + Tipo de balançador de carga adicionado a MLC para tipos AKS.
    + Adicione append_prefix parâmetro bool a download_files em run.py e download_artifacts_from_prefix em artifacts_client. Esta bandeira é usada para aplainar seletivamente o filepath de origem, pelo que apenas o nome do ficheiro ou da pasta é adicionado ao output_directory
    + Corrija o problema de deserialização para `run_config.yml` com a utilização do conjunto de dados.
    + Ao ligar `keep_columns` ou resultar na queda de uma coluna de `drop_columns` séries de tempo, as capacidades correspondentes também serão retiradas para o conjunto de dados.
  + **azureml-interpret**
    + Versão interpretação-comunidade atualizada para 0.1.0.3
  + **azureml-train-automl**
    + Corrigiu um problema em que automl_step pode não imprimir problemas de validação.
    + Fixo register_model para ter sucesso mesmo que o ambiente do modelo esteja a perder dependências localmente.
    + Corrigi um problema em que alguns percursos remotos não estavam ativados.
    + Adicione o registo da exceção que está a causar a falha prematura de uma corrida local.
  + **azureml-train-core**
    + Considere resume_from funciona no cálculo da melhor execução automática de hiperparímetro para crianças.
  + **azureml-pipeline-core**
    + Manuseamento de parâmetros fixos na construção de argumentos de gasoduto.
    + Adiu descrição do gasoduto e parâmetro yaml do tipo passo.
    + Novo formato yaml para passo pipeline e aviso de depreciação adicionado para formato antigo.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Experiência web

A página colaborativa de aterragem no espaço de trabalho [https://ml.azure.com](https://ml.azure.com) foi reforçada e renomeada como o estúdio Azure Machine Learning.

A partir do estúdio, você pode treinar, testar, implementar e gerir ativos de Azure Machine Learning, tais como conjuntos de dados, oleodutos, modelos, pontos finais, e muito mais.

Aceda às seguintes ferramentas de autoria baseadas na web do estúdio:

| Ferramenta baseada na web | Descrição | 
|-|-|-|
| VM do portátil (pré-visualização) | Estação de trabalho totalmente gerida em nuvem | 
| [Aprendizagem automática de máquinas](tutorial-first-experiment-automated-ml.md) (pré-visualização) | Nenhuma experiência de código para automatizar o desenvolvimento de modelos de aprendizagem automática | 
| [Estruturador](concept-designer.md) | Ferramenta de modelagem de machine learning drag-and-drop anteriormente conhecida como interface visual | 


### <a name="azure-machine-learning-designer-enhancements"></a>Melhorias do designer de machine learning do Azure

+ Anteriormente conhecida como interface visual 
+    11 novos [módulos,](algorithm-module-reference/module-reference.md) incluindo recomendadores, classificadores e utilitários de formação, incluindo engenharia de recursos, validação cruzada e transformação de dados.

### <a name="r-sdk"></a>SDK para R 
 
Cientistas de dados e desenvolvedores de IA usam o [Azure Machine Learning SDK para R](tutorial-1st-r-experiment.md) para construir e executar fluxos de trabalho de aprendizagem automática com Azure Machine Learning.

O Azure Machine Learning SDK para R utiliza o `reticulate` pacote para ligar ao Python SDK. Ao ligar-se diretamente à Python, o SDK para R permite-lhe aceder a objetos e métodos principais implementados no Python SDK a partir de qualquer ambiente R que escolher.

As principais capacidades do SDK incluem:

+    Gerir recursos de cloud para monitorizar, registar e organizar as experimentações de machine learning.
+    Treina modelos usando recursos em nuvem, incluindo treino de modelo acelerado pela GPU.
+    Implemente os seus modelos como webservices em Azure Container Instances (ACI) e Azure Kubernetes Service (AKS).

Consulte o site do [pacote](https://azure.github.io/azureml-sdk-for-r) para obter documentação completa.

### <a name="azure-machine-learning-integration-with-event-grid"></a>Integração de Machine Learning Azure com Grade de Eventos 

O Azure Machine Learning é agora um fornecedor de recursos para a Grade de Eventos, pode configurar eventos de aprendizagem automática através do portal Azure ou do Azure CLI. Os utilizadores podem criar eventos para a conclusão do processo, registo de modelos, implementação de modelos e deriva de dados detetadas. Estes eventos podem ser encaminhados para manipuladores de eventos apoiados pela Event Grid para consumo. Consulte o [esquema de](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning) eventos de machine learning e artigos [tutoriais](how-to-use-event-grid.md) para mais detalhes.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK para Python v1.0.72

+ **Novas funcionalidades**
  + Monitores de conjuntos de dados adicionados através do pacote [**azureml-datadrift,**](https://docs.microsoft.com/python/api/azureml-datadrift) permitindo monitorizar conjuntos de dados de séries de tempo para deriva de dados ou outras alterações estatísticas ao longo do tempo. Os alertas e eventos podem ser desencadeados se forem detetados desvios ou se forem satisfeitas outras condições nos dados. Consulte a [nossa documentação](how-to-monitor-datasets.md) para mais detalhes.
  + Anunciando duas novas edições (também referidas como um SKU intercambiavelmente) em Azure Machine Learning. Com esta versão, pode agora criar um espaço de trabalho básico ou enterprise Azure Machine Learning. Todos os espaços de trabalho existentes serão incumpridos para a edição Basic, e você pode ir ao portal Azure ou ao estúdio para atualizar o espaço de trabalho a qualquer momento. Pode criar um espaço de trabalho Básico ou Empresarial a partir do portal Azure. Leia a [nossa documentação](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) para saber mais. A partir do SDK, a edição do seu espaço de trabalho pode ser determinada usando a propriedade "sku" do seu objeto de espaço de trabalho.
  + Também fizemos melhorias no Azure Machine Learning Compute - agora pode ver métricas para os seus clusters (como nós totais, nós de corrida, quota de núcleo total) no Azure Monitor, além de visualizar registos de diagnóstico para depuração. Além disso, também pode ver corridas em execução ou fila no seu cluster e detalhes como os IPs dos vários nós no seu cluster. Pode vê-las no portal ou utilizando funções correspondentes no SDK ou CLI.

  + **Funcionalidades de pré-visualização**
    + Estamos a lançar suporte de pré-visualização para encriptação de disco do seu SSD local no Azure Machine Learning Compute. Crie um bilhete de suporte técnico para obter a sua subscrição, permita que a sua inscrição utilize esta funcionalidade.
    + Pré-visualização pública da inferência do lote de aprendizagem da máquina Azure. A azure Machine Learning Batch Inference visa grandes inferências que não são sensíveis ao tempo. A Inferência do Lote proporciona uma escala de cálculo de inferência rentável, com uma produção inigualável para aplicações assíncronos. Está otimizado para inferência de alta produção, fogo-e-esquecimento sobre grandes recolhas de dados.
    + [**azureml-contrib-dataset**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
        + Funcionalidades ativadas para conjunto de dados rotulado
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask

        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)

        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()

        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD)
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)

        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **Correções e melhorias de bugs**
  + **azure-cli-ml**
    + O CLI suporta agora a embalagem do modelo.
    + CLI do conjunto de dados adicionado. Para mais informações: `az ml dataset --help`
    + Suporte adicional para a implantação e embalagem de modelos suportados (ONNX, scikit-learn e TensorFlow) sem uma instância InferenceConfig.
    + Bandeira de substituição adicionada para implantação de serviço (ACI e AKS) em SDK e CLI. Se for fornecido, substituirá o serviço existente se já existir serviço com nome. Se o serviço não existir, criará um novo serviço.
    + Os modelos podem ser registados com duas novas estruturas, Onnx e Tensorflow. - O registo do modelo aceita dados de entrada de amostra, dados de saída de amostra e configuração de recursos para o modelo.
  + **azureml-automl-core**
    + Treinar uma iteração só seria executado num processo infantil quando os constrangimentos de tempo de funcionamento estão a ser definidos.
    + Adicione um guarda-costas para as tarefas de previsão, para verificar se um max_horizon especificado causará ou não um problema de memória na determinada máquina. Se assim for, será exibida uma mensagem de guarda-costas.
    + Apoio adicional para frequências complexas como dois anos e um mês. -Mensagem de erro compreensível adicionada se não for possível determinar a frequência.
    + Adicione azureml-defaults a conda env gerados por automóveis para resolver a falha de implementação do modelo
    + Permitir que os dados intermédios no Pipeline de Aprendizagem da Máquina de Azure sejam convertidos para conjunto de dados tabulares e utilizados em `AutoMLStep` .
    + Atualização de finalidade de coluna implementada para streaming.
    + Atualização de parâmetros de transformador implementado para Imputer e HashOneHotEncoder para streaming.
    + Adicionou o tamanho atual dos dados e o tamanho mínimo de dados exigido às mensagens de erro de validação.
    + Atualize o tamanho mínimo de dados necessário para validação cruzada para garantir um mínimo de duas amostras em cada dobra de validação.
  + **azureml-cli-comum**
    + O CLI suporta agora a embalagem do modelo.
    + Os modelos podem ser registados com duas novas estruturas, Onnx e Tensorflow.
    + O registo do modelo aceita dados de entrada de amostra, dados de saída de amostra e configuração de recursos para o modelo.
  + **azureml-contrib-gbdt**
    + fixou o canal de libertação para o caderno
    + Acrescentou um aviso para o alvo de computação não-AmlCompute que não apoiamos
    + Adicionado Estimativa LightGMB ao pacote azureml-contrib-gbdt
  + [**azureml-core**](https://docs.microsoft.com/python/api/azureml-core)
    + O CLI suporta agora a embalagem do modelo.
    + Adicione aviso de depreciação para APIs de datamento preceducado. Consulte o aviso de alteração da API do Dataset em https://aka.ms/tabular-dataset .
    + Altere [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) para devolver o nome e versão do registo se o conjunto de dados estiver registado.
    + Corrija um bug que o ScriptRunConfig com o conjunto de dados não pode ser usado repetidamente para submeter a execução de experiências.
    + Os conjuntos de dados recuperados durante uma execução serão rastreados e podem ser vistos na página de detalhes de execução ou ligando [`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) após a execução estar concluída.
    + Permitir que os dados intermédios no Pipeline de Aprendizagem da Máquina de Azure sejam convertidos para conjunto de dados tabulares e utilizados em [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep) .
    + Suporte adicional para a implantação e embalagem de modelos suportados (ONNX, scikit-learn e TensorFlow) sem uma instância InferenceConfig.
    + Bandeira de substituição adicionada para implantação de serviço (ACI e AKS) em SDK e CLI. Se for fornecido, substituirá o serviço existente se já existir serviço com nome. Se o serviço não existir, criará um novo serviço.
    +  Os modelos podem ser registados com duas novas estruturas, Onnx e Tensorflow. O registo do modelo aceita dados de entrada de amostra, dados de saída de amostra e configuração de recursos para o modelo.
    + Adicionou uma nova loja de dados para a Azure Database para o MySQL. Exemplo adicional para a utilização da Base de Dados Azure para o MySQL em DataTransferStep em Pipelines de Aprendizagem de Máquinas Azure.
    + Funcionalidade adicionada para adicionar e remover tags de experiências Funcionalidade adicional para remover tags de execuções
    + Bandeira de substituição adicionada para implantação de serviço (ACI e AKS) em SDK e CLI. Se for fornecido, substituirá o serviço existente se já existir serviço com nome. Se o serviço não existir, criará um novo serviço.
  + [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + Mudou-se de `azureml-contrib-datadrift` para `azureml-datadrift`
    + Apoio adicional à monitorização de conjuntos de dados de séries de tempo para deriva e outras medidas estatísticas
    + Novos métodos `create_from_model()` e `create_from_dataset()` para a [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector%28class%29) classe. O `create()` método será depretado.
    + Ajustes nas visualizações em Python e UI no estúdio Azure Machine Learning.
    + Apoiar o agendamento semanal e mensal do monitor, além de diariamente para monitores de conjuntos de dados.
    + Suporte o backfill das métricas do monitor de dados para analisar dados históricos para monitores de conjuntos de dados.
    + Várias correções de bugs
  + [**azureml-pipeline-core**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + azureml-dataprep já não é necessário para apresentar um Pipeline de Aprendizagem de Máquinas Azure executado a partir do ficheiro do `yaml` pipeline.
  + [**azureml-train-automl**](/python/api/azureml-train-automl-runtime/)
    + Adicione azureml-defaults a conda env gerados por automóveis para resolver a falha de implementação do modelo
    + O treino remoto autoML inclui agora padrão azureml para permitir a reutilização do env de treino para inferência.
  + **azureml-train-core**
    + Apoio PyTorch 1.3 no [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch) estimador

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Interface visual (pré-visualização)

+ A interface visual Azure Machine Learning (pré-visualização) foi revista para funcionar em [pipelines Azure Machine Learning](concept-ml-pipelines.md). Os oleodutos (anteriormente conhecidos como experiências) da autoria na interface visual estão agora totalmente integrados com a experiência de aprendizagem automática Azure.
  + Experiência de gestão unificada com ativos da SDK
  + Versão e rastreio para modelos de interface visual, oleodutos e pontos finais
  + UI redesenhado
  + Implantação de inferência de lote adicionada
  + Apoio adicional do Serviço Azure Kubernetes (AKS) para alvos de cálculo de inferência
  + Novo gasoduto python-step autoria do fluxo de trabalho
  + Nova [página de aterragem](https://ml.azure.com) para ferramentas de autoria visual

+ **Novos módulos**
  + Aplicar operação matemática
  + Aplicar a transformação SQL
  + Valores de clip
  + Resumir dados
  + Importação da Base de Dados SQL

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK para Python v1.0.69

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + Limitando as explicações do modelo para melhor executar em vez de computar explicações para cada corrida. Fazendo esta mudança de comportamento para local, remoto e ADB.
    + Apoio adicional às explicações do modelo a pedido para a UI
    + Acrescentou psutil como uma dependência `automl` de e incluiu psutil como uma dependência conda em amlcompute.
    + Corrigiu o problema com lags heurísticos e tamanhos de janelas rolantes nos conjuntos de dados de previsão algumas séries das quais podem causar erros de álgebra linear
      + Impressão adicionada para os parâmetros heuristicamente determinados nas corridas de previsão.
  + **azureml-contrib-datadrift**
    + Proteção adicional ao criar métricas de saída se a deriva do nível de conjunto de dados não estiver na primeira secção.
  + **azureml-contrib-interpret**
    + azureml-contrib-explain-model foi renomeado para azureml-contrib-interpret
  + **azureml-core**
    + Adicionou API a conjuntos de dados não registados. `dataset.unregister_all_versions()`
    + azureml-contrib-explain-model package foi renomeado para azureml-contrib-interpret.
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core)**
    + Adicionou API a conjuntos de dados não registados. conjunto de dados. [unregister_all_versions()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + A API do Dataset adicionado para verificar a hora alterada de dados. `dataset.data_changed_time`.
    + Ser capaz de consumir `FileDataset` e `TabularDataset` como entradas `PythonScriptStep` `EstimatorStep` para, e em `HyperDriveStep` Azure Machine Learning Pipeline
    + Desempenho de `FileDataset.mount` foi melhorado para pastas com um grande número de ficheiros
    + Ser capaz de consumir [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset) e [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset) como entradas para [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)e [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) no Pipeline de Aprendizagem de Máquinas Azure.
    + Desempenho do FileDataset. [montagem()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) foi melhorado para pastas com um grande número de ficheiros
    + URL adicionado às recomendações de erro conhecidas em detalhes de execução.
    + Consertado um bug em run.get_metrics onde os pedidos falhariam se uma corrida tivesse muitos filhos
    + Consertado um bug em [run.get_metrics](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) onde os pedidos falhariam se uma corrida tivesse muitos filhos
    + Apoio adicional à autenticação no aglomerado de Arcadia.
    + Criar um objeto Experiment obtém ou cria a experiência no espaço de trabalho Azure Machine Learning para executar rastreamento de história. A experiência ID e o tempo arquivado são povoados no objeto experimento sobre a criação. Exemplo: experimento = Experimento (espaço de trabalho, "New Experiment") experiment_id = experiment.id arquivo() e reativar() são funções que podem ser chamadas numa experiência para ocultar e restaurar a experiência de ser mostrada no UX ou devolvida por padrão numa chamada para listar experiências. Se uma nova experiência for criada com o mesmo nome de uma experiência arquivada, pode renomear a experiência arquivada ao reativar passando um novo nome. Só pode haver uma experiência ativa com um nome próprio. Exemplo: experimento1 = Experiment (workspace, "Ative Experiment") experimentou1.archive() # Crie uma nova experiência ativa com o mesmo nome que o arquivado. experiência2. = Experimento (espaço de trabalho, "Ative Experiment") experimentou1.reativar(new_name="Experiência Ativa Anterior") A lista de métodos estáticos() em Experiment pode levar um filtro de nome e filtro ViewType. Os valores do ViewType são "ATIVE_ONLY", "ARCHIVED_ONLY" e "ALL" Exemplo: archived_experiments = Experiment.list (workspace, view_type="ARCHIVED_ONLY") all_first_experiments = Experiment.list (workspace, name="First Experiment", view_type="ALL")
    + Suporte usando ambiente para implementação de modelos e atualização de serviço
  + **azureml-datadrift**
    + O atributo de show da classe DataDriftDector não suporta mais argumentos opcionais de "with_details". O atributo show apresentará apenas coeficiente de deriva de dados e contribuição de deriva de dados de colunas de recursos.
    + DataDriftDetector atribui alterações de comportamento 'get_output':
      + Parâmetro de entrada start_time, end_time são opcionais em vez de obrigatórios;
      + A start_time específica e/ou end_time com um run_id específico na mesma invocação resultará em exceção de erro de valor, uma vez que são mutuamente exclusivas
      + Por entrada específica start_time e/ou end_time, apenas serão devolvidos os resultados das execuções programadas;
      + O parâmetro "daily_latest_only" é precotado.
    + Suporte a recuperar as saídas dataset-based Data Drift.
  + **azureml-explain-modelo**
    + Renomea pacote de modelo de explicação AzureML para AzureML-interpret, mantendo o pacote antigo para retrocompatibilidade por enquanto
    + bug fixo `automl` com explicações brutas definidas para tarefa de classificação em vez de regressão por padrão no download de ExplanationClient
    + Adicionar suporte `ScoringExplainer` para ser criado diretamente usando `MimicWrapper`
  + **azureml-pipeline-core**
    + Melhor desempenho para a criação de grandes gasodutos
  + **azureml-train-core**
    + Suporte de TensorFlow 2.0 adicionado no Estimador tensorFlow
  + **azureml-train-automl**
    + Criar um objeto [Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment) obtém ou cria a experiência no espaço de trabalho Azure Machine Learning para executar rastreamento de história. A experiência ID e o tempo arquivado são povoados no objeto experimento sobre a criação. Exemplo:

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [arquivo()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--) e [reativação()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) são funções que podem ser chamadas numa experiência para ocultar e restaurar a experiência de ser mostrada no UX ou devolvida por padrão numa chamada para listar experiências. Se uma nova experiência for criada com o mesmo nome de uma experiência arquivada, pode renomear a experiência arquivada ao reativar passando um novo nome. Só pode haver uma experiência ativa com um nome próprio. Exemplo:

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        A lista de métodos [estáticos()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) em Experiment pode levar um filtro de nome e filtro ViewType. Os valores do ViewType são "ATIVE_ONLY", "ARCHIVED_ONLY" e "ALL". Exemplo:

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Suporte usando ambiente para implementação de modelos e atualização de serviço.
  + **[azureml-datadrift](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + O atributo de show da classe [DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) não suporta mais argumentos opcionais 'with_details'. O atributo show apresentará apenas coeficiente de deriva de dados e contribuição de deriva de dados de colunas de recursos.
    + Função DataDriftDetector [get_output]python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector.datadriftdetector#get-output-start-time-none-end-time-none-run-id-none-) alterações de comportamento:
      + Parâmetro de entrada start_time, end_time são opcionais em vez de obrigatórios;
      + A start_time específica e/ou end_time com um run_id específico na mesma invocação resultará numa exceção de erro de valor por serem mutuamente exclusivas;
      + Por entrada específica start_time e/ou end_time, apenas serão devolvidos os resultados das execuções programadas;
      + O parâmetro "daily_latest_only" é precotado.
    + Suporte a recuperar as saídas dataset-based Data Drift.
  + **azureml-explain-modelo**
    + Adicione suporte para [ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer?view=azure-ml-py&preserve-view=true) a ser criado diretamente usando MimicWrapper
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Melhor desempenho para a criação de grandes gasodutos.
  + **[azureml-train-core](https://docs.microsoft.com/python/api/azureml-train-core)**
    + Suporte tensorFlow 2.0 adicionado no [Estimador TensorFlow.](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow)
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + A corrida dos pais deixará de ser falhada quando a iteração de configuração falhou, uma vez que a orquestração já trata dela.
    + Acrescentou apoio local-estivador e conda local para experiências AutoML
    + Adicionou apoio local-docker e conda local para experiências AutoML.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nova experiência web (pré-visualização) para espaços de trabalho de Aprendizagem de Máquinas Azure

O separador Experiment no novo portal do [espaço de trabalho](https://ml.azure.com) foi atualizado para que os cientistas de dados possam monitorizar as experiências de uma forma mais performante. Pode explorar as seguintes funcionalidades:
+ Experimente metadados para filtrar e classificar facilmente a sua lista de experiências
+ Páginas de detalhes de experiências simplificadas e performantes que lhe permitem visualizar e comparar as suas correções
+ Novo design para executar páginas de detalhes para entender e monitorizar as suas corridas de treino

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK para Python v1.0.65

  + **Novas funcionalidades**
    + Ambientes com curadoria adicionada. Estes ambientes foram pré-configurados com bibliotecas para tarefas comuns de aprendizagem automática, e foram pré-construídos e em cache como imagens docker para uma execução mais rápida. Aparecem por padrão na lista de ambiente do Workspace, com prefixo "AzureML".
    + Ambientes com curadoria adicionada. Estes ambientes foram pré-configurados com bibliotecas para tarefas comuns de aprendizagem automática, e foram pré-construídos e em cache como imagens docker para uma execução mais rápida. Aparecem por padrão na lista de ambiente do [Workspace,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29)com prefixo "AzureML".

  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Adicionou o suporte de conversão ONNX para o ADB e o HDI

+ **Funcionalidades de pré-visualização**
  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + BERT e BiLSTM suportados como alodador de texto (apenas pré-visualização)
    + Personalizado de apresentação suportada para fins de coluna e parâmetros do transformador (apenas pré-visualização)
    + Explicações cruas suportadas quando o utilizador permite a explicação do modelo durante o treino (apenas pré-visualização)
    + Profeta adicionado para `timeseries` previsão como um gasoduto treinável (apenas pré-visualização)

  + **azureml-contrib-datadrift**
    + Pacotes transferidos de azureml-contrib-datadrift para azureml-datadrift; o `contrib` pacote será removido em uma libertação futura

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + Apresentados Comconfig para AutoMLConfig e AutoMLBaseSettings
    + Apresentados Comconfig para [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) e AutoMLBaseSettings
      + Final da Coluna de Substituição para a Exibição com coluna e tipo de características
      + Sobrepor parâmetros de transformador
    + Mensagem de depreciação adicionada para explain_model e retrieve_model_explanations())
    + Profeta adicionado como um gasoduto treinável (apenas pré-visualização)
    + Mensagem de depreciação adicionada para explain_model e retrieve_model_explanations().
    + Profeta adicionado como um gasoduto treinável (apenas pré-visualização).
    + Suporte adicional para deteção automática de lags de alvo, tamanho da janela rolante e horizonte máximo. Se um dos target_lags, target_rolling_window_size ou max_horizon for definido como 'auto', a heurística será aplicada para estimar o valor do parâmetro correspondente com base em dados de formação.
    + Previsão fixa no caso de o conjunto de dados conter uma coluna de cereais, este grão é de um tipo numérico e há uma lacuna entre o conjunto de comboios e testes
    + Corrigi a mensagem de erro sobre o índice duplicado no percurso remoto em tarefas de previsão
    + Previsão fixa no caso de o conjunto de dados conter uma coluna de cereais, este grão é de tipo numérico e existe uma lacuna entre o conjunto de comboios e de ensaio.
    + Corrigi a mensagem de erro sobre o índice duplicado no percurso remoto em tarefas de previsão.
    + Adicionei um guarda-costas para verificar se um conjunto de dados está ou não desequilibrado. Se for, uma mensagem de guarda-costas seria escrita para a consola.
  + **azureml-core**
    + Capacidade adicional de recuperar o URL SAS para modelar no armazenamento através do objeto do modelo. Ex: model.get_sas_url()
    + Introduzir `run.get_details()['datasets']` para obter conjuntos de dados associados à execução submetida
    + Adicione API `Dataset.Tabular.from_json_lines_files` para criar um Separador De Dados a partir de ficheiros JSON Lines. Para saber mais sobre estes dados tabulares nos ficheiros da JSON Lines no TabularDataset, visite [este artigo](how-to-create-register-datasets.md) para obter documentação.
    + Adicionados campos de tamanho VM adicionais (DISCO DE SO, número de GPUs) à função supported_vmsizes ()
    + Acrescentou campos adicionais à função list_nodes () para mostrar a execução, o IP privado e público, a porta, etc.
    + Capacidade de especificar um novo campo durante o fornecimento de clusters -- remotelogin_port_public_access que pode ser configurado para ativar ou desativar dependendo se você gostaria de deixar a porta SSH aberta ou fechada no momento da criação do cluster. Se não o especificar, o serviço abrirá ou fechará inteligentemente a porta dependendo se está a implantar o cluster dentro de um VNet.
  + **azureml-explain-modelo**
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + Capacidade adicional de recuperar o URL SAS para modelar no armazenamento através do objeto do modelo. Ex: modelo. [get_sas_url()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Introduza a corrida. [get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)['datasets'] para obter conjuntos de dados associados à execução submetida
    + Adicionar API `Dataset.Tabular` .[ from_json_lines_files criar](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) um Separador De Ficheiros JSON. Para saber mais sobre estes dados tabulares nos ficheiros da JSON Lines no TabularDataset, visite https://aka.ms/azureml-data para obter documentação.
    + Adicionados campos de tamanho VM adicionais (DISCO DEV, número de GPUs) à função [supported_vmsizes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + Acrescentou campos adicionais à função [list_nodes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) para mostrar a execução, o privado, e o IP público, a porta, etc.
    + Capacidade de especificar um novo campo durante [o provisionamento do](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)  cluster que pode ser definido para ativar ou desativar dependendo se você gostaria de deixar a porta SSH aberta ou fechada no momento de criar o cluster. Se não o especificar, o serviço abrirá ou fechará inteligentemente a porta dependendo se está a implantar o cluster dentro de um VNet.
  + **azureml-explain-modelo**
    + Melhor documentação para as saídas de explicação no cenário de classificação.
    + Acrescentou a capacidade de carregar os valores y previstos na explicação para os exemplos de avaliação. Desbloqueia visualizações mais úteis.
    + Propriedade explicadora adicionada à MimicWrapper para permitir obter o MimicExplainer subjacente.
  + **azureml-pipeline-core**
    + Notebook adicionado para descrever Módulo, Modusion e ModuleStep
  + **azureml-pipeline-steps**
    + Adicionado RScriptStep para suportar script R executado através do pipeline AML.
    + Parâmetros de metadados fixos analisados no AzureBatchStep que estava a causar a mensagem de erro "a atribuição do parâmetro SubscriptionId não é especificada."
  + **azureml-train-automl**
    + Training_data suportado, validation_data, label_column_name, weight_column_name como formato de entrada de dados
    + Mensagem de depreciação adicionada para explain_model e retrieve_model_explanations())
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Adicionei um [caderno](https://aka.ms/pl-modulestep) para descrever [o Módulo](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module%28class%29), [ModVersion, e [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep).
  + **[azureml-pipeline-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + Adicionado [RScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) para suportar script R executado através do pipeline AML.
    + Parâmetros de metadados fixos em [AzureBatchStep que estava a causar a mensagem de erro "a atribuição do parâmetro SubscriptionId não é especificada".
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Suportado training_data, validation_data, label_column_name, weight_column_name como formato de entrada de dados.
    + Mensagem de depreciação adicionada para [explain_model e](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) [retrieve_model_explanations()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-).


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK para Python v1.0.62

+ **Novas funcionalidades**
  + Introduziu o `timeseries`  traço no SeparadorDataset. Este traço permite uma filtragem de tempo fácil nos dados de um SeparadorDataset, como por exemplo, a recolha de todos os dados entre um intervalo de tempo ou os dados mais recentes.  https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb por exemplo, um caderno.
  + Formação ativada com SeparadorDataset e FileDataset. 

  + **azureml-train-core**
      + Adicionado `Nccl` e apoio no `Gloo` estimador PyTorch

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + Depreceu a definição autoML 'lag_length' e o LaggingTransformer.
    + Validação correta fixa dos dados de entrada se forem especificados num formato de Dataflow
    + Modificou o fit_pipeline.py para gerar o json gráfico e carregar para artefactos.
    + Tornou o gráfico `userrun` `Cytoscape` utilizado.
  + **azureml-core**
    + Revisitei o tratamento de exceções no código ADB e escovemos as alterações de acordo com o novo tratamento de erros
    + Aderiu a autenticação automática de MSI para VMs de portátil.
    + Corrige bug onde modelos corruptos ou vazios podem ser carregados devido a recauchutagens falhadas.
    + Corrigiu o erro onde `DataReference` o nome muda quando o modo muda `DataReference` (por exemplo, ao ligar `as_upload` , ou `as_download` `as_mount` .
    + Fazer `mount_point` e opcional para `target_path` `FileDataset.mount` `FileDataset.download` e.
    + A exceção de que a coluna de relógios não pode ser encontrada será deitada fora se a API relacionada com séries de tempo for chamada sem uma coluna de relógios finas atribuída ou se as colunas de relógios atribuídas forem largadas.
    + As colunas de séries de tempo devem ser atribuídas com coluna cujo tipo é Data, caso contrário, espera-se exceção
    + As colunas de séries de tempo que atribuem 'with_timestamp_columns' da API podem tomar Nenhum nome de coluna de tempo fino/grosso, que irá limpar as colunas de relógios previamente atribuídas.
    + A exceção será descartada quando quer o grão grosso quer a coluna de tempos de grão fino é largada com indicação para o utilizador de que a queda pode ser feita após a exclusão da coluna de tempotamp na lista de queda ou chamada with_time_stamp com nenhum valor para libertar colunas de tempotamp
    + A exceção será desativada quando não estiver incluída na lista de colunas de colunas de retenção de colunas com indicação para o utilizador de que a manutenção pode ser feita após incluir a coluna de tempotamp na lista de colunas de manutenção ou a chamada with_time_stamp com nenhum valor para libertar colunas de tempotamp.
    + Registos adicionais para o tamanho de um modelo registado.
  + **azureml-explain-modelo**
    + Aviso fixo impresso para consola quando não estiver instalado o pacote python "packaging": "Utilizando a versão mais antiga do que suportada de lightgbm, por favor atualize para a versão superior a 2.2.1"
    + Explicação de modelo de descarregamento fixo com fragmento para explicações globais com muitas funcionalidades
    + Explicador de imitação fixa em falta exemplos de inicialização na explicação da saída
    + Erro imutável fixo nas propriedades definidas ao carregar com o cliente de explicação usando dois tipos diferentes de modelos
    + Adicionou um get_raw param ao explicador de pontuação.explicando() para que um explicador de pontuação possa devolver valores projetados e brutos.
  + **azureml-train-automl**
    + Introduziu APIs públicas da AutoML para apoiar explicações de explicações da `automl` explicação SDK - forma mais recente de apoiar explicações AutoML, dissociando a caracterização autoML e explicando o suporte de explicação bruta integrada da Azureml explicar SDK para modelos AutoML.
    + Remoção de azureml-padrão de ambientes de treino remoto.
    + Altere a localização da loja de cache padrão do FileCacheStore com base num para AzureFileCacheStore um para AutoML na via de código Azure Databricks.
    + Validação correta fixa dos dados de entrada se forem especificados num formato de Dataflow
  + **azureml-train-core**
    + Reverteu source_directory_data_store depreciação.
    + Capacidade adicional de sobrepor as versões de pacote instaladas azureml.
    + Apoio de ficheiro de estivado adicionado em `environment_definition` parâmetro nos estimadores.
    + Parâmetros de formação distribuídos simplificados em estimadores.

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nova experiência web (pré-visualização) para espaços de trabalho de Aprendizagem de Máquinas Azure
A nova experiência web permite que cientistas de dados e engenheiros de dados completem o seu ciclo de vida de aprendizagem automática de ponta a ponta, desde a preparação e visualização de dados até à formação e implantação de modelos num único local.

![Azure Machine Learning WorkSpace UI (pré-visualização)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Principais características:**

Utilizando esta nova interface Azure Machine Learning, pode agora:
+ Gerencie os seus cadernos ou ligue-se ao Jupyter
+ [Executar experiências automatizadas de ML](tutorial-first-experiment-automated-ml.md)
+ [Criar conjuntos de dados a partir de ficheiros locais, datastores, & ficheiros web](how-to-create-register-datasets.md)
+ Explore & preparar conjuntos de dados para a criação de modelos
+ Monitorize a deriva de dados para os seus modelos
+ Ver recursos recentes de um dashboard

No momento desta versão, os seguintes navegadores são suportados: Chrome, Firefox, Safari e Microsoft Edge Preview.

**Questões conhecidas:**

1. Refresque o seu navegador se vir "Algo correu mal! Erros de carregamento de ficheiros de pedaços" quando a implementação está em curso.

1. Não é possível eliminar ou renomear ficheiro em Cadernos e Ficheiros. Durante a visualização pública, pode utilizar o Jupyter UI ou Terminal em VM de portátil para executar operações de ficheiro de atualização. Por se trata de um sistema de ficheiros de rede montado, todas as alterações, que faça no Notebook VM são imediatamente refletidas no espaço de trabalho do Portátil.

1. Para SSH no VM do Caderno:
   1. Encontre as teclas SSH que foram criadas durante a configuração do VM. Ou, encontre as chaves no espaço de trabalho Azure Machine Learning > separador Compute aberto > localizar o VM do Caderno na lista > abrir as suas propriedades: copiar as chaves do diálogo.
   1. Importe as chaves SSH públicas e privadas da sua máquina local.
   1. Use-os para SSH no VM do caderno.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK para Python v1.0.60

+ **Novas funcionalidades**
  + Introduzido FileDataset, que faz referência a ficheiros únicos ou múltiplos nas suas datastores ou urls públicos. Os ficheiros podem ser de qualquer formato. O FileDataset fornece-lhe a capacidade de descarregar ou montar os ficheiros no seu cálculo. 
  + Pipeline Yaml Suporte adicionado para PythonScript Step, Adla Step, Databricks Step, DataTransferStep e AzureBatch Step

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + O AutoArima é agora um gasoduto sugestivo apenas para pré-visualização.
    + Relatório de erro melhorado para a previsão.
    + Melhorou o registo utilizando exceções personalizadas em vez de genéricas nas tarefas de previsão.
    + Removido o controlo do max_concurrent_iterations ser inferior ao número total de iterações.
    + Os modelos AutoML agora devolvem AutoMLExceptions
    + Esta versão melhora o desempenho de execução de machine learning funcionando localmente.
  + **azureml-core**
    + Introduza Dataset.get_all (espaço de trabalho), que devolve um dicionário `TabularDataset` de `FileDataset` e objetos com o seu nome de registo.

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Introduzir `parition_format` como argumento para e `Dataset.Tabular.from_delimited_files` `Dataset.Tabular.from_parquet.files` . A informação de partição de cada caminho de dados será extraída em colunas com base no formato especificado. '{column_name}' cria coluna de cordas, e '{column_name:yyyy/MM/dd/HH/mm/mm}' cria coluna de datas, onde 'yyyyy', 'MM', 'dd', 'HH', 'mm' e 'ss' são usados para extrair ano, mês, dia, hora, minuto e segundo para o tipo de data. O partition_format deve partir-se da posição da primeira chave de partição até ao fim do caminho do ficheiro. Por exemplo, dado o caminho. /USA/2019/01/01/data.csv' onde a partição é por país e tempo, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' cria coluna de cordas 'Country' com valor 'EUA' e coluna de datatime 'PartitionDate' com o valor '2019-01-01'.
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + Introduzir `partition_format` como argumento para e `Dataset.Tabular.from_delimited_files` `Dataset.Tabular.from_parquet.files` . A informação de partição de cada caminho de dados será extraída em colunas com base no formato especificado. '{column_name}' cria coluna de cordas, e '{column_name:yyyy/MM/dd/HH/mm/mm}' cria coluna de datas, onde 'yyyyy', 'MM', 'dd', 'HH', 'mm' e 'ss' são usados para extrair ano, mês, dia, hora, minuto e segundo para o tipo de data. O partition_format deve partir-se da posição da primeira chave de partição até ao fim do caminho do ficheiro. Por exemplo, dado o caminho. /USA/2019/01/01/data.csv' onde a partição é por país e tempo, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' cria coluna de cordas 'Country' com valor 'EUA' e coluna de datatime 'PartitionDate' com o valor '2019-01-01'.
    + `to_csv_files` e `to_parquet_files` métodos foram adicionados a `TabularDataset` . Estes métodos permitem a conversão entre a `TabularDataset` e a `FileDataset` convertendo os dados em ficheiros do formato especificado.
    + Inicie automaticamente o registo de imagem base ao guardar um Dockerfile gerado pelo Modelo.pacote().
    + "gpu_support" já não é necessário; A AML deteta e utiliza automaticamente a extensão do estivador nvidia quando está disponível. Será removido numa futura libertação.
    + Suporte adicional para criar, atualizar e utilizar PipelineDrafts.
    + Esta versão melhora o desempenho de execução de machine learning funcionando localmente.
    + Os utilizadores podem consultar métricas do histórico executado pelo nome.
    + Melhorou o registo utilizando exceções personalizadas em vez de genéricas nas tarefas de previsão.
  + **azureml-explain-modelo**
    + Adicionado feature_maps parâmetro ao novo MimicWrapper, permitindo que os utilizadores obtenham explicações de funcionalidades cruas.
    + Os uploads do conjunto de dados estão agora desligados por padrão para upload de explicações, e podem ser re-activados com upload_datasets=Verdadeiro
    + Adicione parâmetros de filtragem "is_law" à lista de explicações e funções de descarregamento.
    + Adiciona o método `get_raw_explanation(feature_maps)` aos objetos de explicação global e local.
    + Verificação de versão adicionada a lightgbm com aviso impresso se abaixo versão suportada
    + Utilização otimizada da memória ao lotar explicações
    + Os modelos AutoML agora devolvem AutoMLExceptions
  + **azureml-pipeline-core**
    + Suporte adicional para criar, atualizar e usar PipelineDrafts - pode ser usado para manter definições de gasoduto mutável e usá-los interativamente para executar
  + **azureml-train-automl**
    + Funcionalidade criada para instalar versões específicas de pytorch v1.1.0, :::no-loc text="cuda"::: toolkit 9.0, pytorch-transformers, que é necessário para ativar BERT/XLNet no ambiente remoto de tempo de execução de python.
  + **azureml-train-core**
    + Falha precoce de alguns erros de definição de espaço hiperparímetro diretamente no sdk em vez do lado do servidor.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning Data Prep SDK v1.1.14
+ **Correções e melhorias de bugs**
  + Habilitado a escrever para ADLS/ADLSGen2 usando caminho e credenciais brutos.
  + Corrigi um erro que fez com que `include_path=True` não funcionasse. `read_parquet`
  + Falha fixa `to_pandas_dataframe()` causada pela exceção "Valor de propriedade inválida: hostSecret".
  + Corrigiu um bug onde os ficheiros não podiam ser lidos no DBFS no modo Spark.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK para Python v1.0.57
+ **Novas funcionalidades**
  + Habilitado `TabularDataset` a ser consumido porML automatizado. Para saber mais `TabularDataset` sobre, https://aka.ms/azureml/howto/createdatasets visite.

+ **Correções e melhorias de bugs**
  + **azure-cli-ml**
    + Agora pode atualizar o certificado TLS/SSL para o ponto final de pontuação implantado no cluster AKS tanto para o certificado gerado pela Microsoft como para o certificado de cliente.
  + **azureml-automl-core**
    + Corrigido um problema na AutoML foram as filas com etiquetas em falta não foram removidas corretamente.
    + Melhor registo de erros em AutoML; as mensagens de erro completas serão agora sempre escritas para o ficheiro de registo.
    + A AutoML atualizou o seu pacote de fixação para incluir `azureml-defaults` `azureml-explain-model` , e `azureml-dataprep` . O AutoML deixará de avisar sobre as incompatibilidades do pacote (exceto a `azureml-train-automl` embalagem).
    + Corrigiu um problema em `timeseries`  que as divisões cv são de tamanho desigual, fazendo com que o cálculo do caixote do lixo falhe.
    + Ao executar a iteração do conjunto para o tipo de treino cross-validation, se tivéssemos dificuldade em descarregar os modelos treinados em todo o conjunto de dados, estávamos a ter uma inconsistência entre os pesos dos modelos e os modelos que estavam a ser alimentados no conjunto de votação.
    + Corrigiu o erro, levantado quando as etiquetas de treino e/ou validação (y e y_valid) são fornecidas sob a forma de dataframe pandas, mas não como matriz numpiada.
    + Corrigiu o problema com as tarefas de previsão quando nenhum foi encontrado nas colunas booleanas das tabelas de entrada.
    + Permitir que os utilizadores de AutoML abandonem séries de treino que não são suficientemente longas quando se prevê. - Permitir que os utilizadores de AutoML retirem os grãos do conjunto de ensaios que não existe no conjunto de treinos durante a previsão.
  + **azureml-core**
    + Problema fixo com blob_cache_timeout pedido de parâmetro.
    + Adicionou ajuste externo e transforme tipos de exceção a erros do sistema.
    + Suporte adicional para segredos do Key Vault para execuções remotas. Adicione uma classe azureml.core.keyvault.Keyvault para adicionar, obter e listar segredos do keyvault associado ao seu espaço de trabalho. As operações apoiadas são:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.keyvault.Keyvault.set_secret(nome, valor)
      + azureml.core.keyvault.Keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret(nome)
      + azureml.core.keyvault.Keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets()
    + Métodos adicionais para obter o teste de chave padrão e obter segredos durante a execução remota:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.run.Run.get_secret(nome)
      + azureml.core.run.Run.get_secrets(secrets_list)
    + Adicionou parâmetros adicionais de sobreposição ao comando CLI de sub-hiperdrive.
    + Melhorar a fiabilidade das chamadas da API está a expandir as retrações para pedidos comuns de exceções à biblioteca.
    + Adicione suporte para submeter runs a partir de uma execução submetida.
    + Problema de token SAS expirado fixo no FileWatcher, o que fez com que os ficheiros deixassem de ser carregados após o seu token inicial ter expirado.
    + Suportado a importação de ficheiros HTTP csv/tsv em dataset python SDK.
    + Depreceu o método Workspace.setup(). A mensagem de aviso mostrada aos utilizadores sugere a utilização de criar() ou obter()/from_config() em vez disso.
    + Adicionado Environment.add_private_pip_wheel(), que permite carregar pacotes de pitões personalizados privados `whl` para o espaço de trabalho e usá-los de forma segura para construir/materializar o ambiente.
    + Agora pode atualizar o certificado TLS/SSL para o ponto final de pontuação implantado no cluster AKS tanto para o certificado gerado pela Microsoft como para o certificado de cliente.
  + **azureml-explain-modelo**
    + Parâmetro adicionado para adicionar um ID do modelo às explicações no upload.
    + Adicionou `is_raw` marcação às explicações na memória e upload.
    + Suporte de pytorch adicionado e testes para pacote modelo azureml-explain.
  + **azureml-opendatasets**
    + Suporte a detetar e registar o ambiente de teste automático.
    + Mais classes para obter população dos EUA por concelho e zip.
  + **azureml-pipeline-core**
    + Adicionou propriedade de etiqueta às definições de porta de entrada e saída.
  + **azureml-telemetry**
    + Corrigiu uma configuração de telemetria incorreta.
  + **azureml-train-automl**
    + Corrigiu o bug onde, na falha de configuração, o erro não estava a ser registado no campo "erros" para a execução da configuração e, por isso, não foi armazenado em "erros" de execução dos pais.
    + Corrigido um problema na AutoML foram as filas com etiquetas em falta não foram removidas corretamente.
    + Permitir que os utilizadores de AutoML abandonem séries de treino que não são suficientemente longas quando se prevê.
    + Deixe que os utilizadores de AutoML retirem os grãos do conjunto de testes que não existe no conjunto de treino ao prever.
    + Agora o AutoMLStep passa através `automl` do config para recuar para evitar quaisquer problemas sobre alterações ou adições de novos parâmetros config.
    + AutoML Data Guardrail está agora em pré-visualização pública. O utilizador irá consultar um relatório de Data Guardrail (para tarefas de classificação/regressão) após o treino e também poderá aceder-lhe através da API da SDK.
  + **azureml-train-core**
    + Suporte à tocha 1.2 adicionado no estimador PyTorch.
  + **azureml-widgets**
    + Tabelas de matrizes de confusão melhoradas para o treino de classificação.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning Data Prep SDK v1.1.12
+ **Novas funcionalidades**
  + As listas de cordas podem agora ser passadas como entrada para `read_*` os métodos.

+ **Correções e melhorias de bugs**
  + O desempenho `read_parquet` foi melhorado quando corre em Spark.
  + Corrigiu um problema em `column_type_builder` caso de falha no caso de uma única coluna com formatos de data ambíguas.

### <a name="azure-portal"></a>Portal do Azure
+ **Recurso de pré-visualização**
  + O streaming de ficheiros de registo e saída está agora disponível para páginas de detalhes de execução. Os ficheiros transmitirão atualizações em tempo real quando o toggle de pré-visualização estiver ligado.
  + A capacidade de definir quota a um nível de espaço de trabalho é lançada na pré-visualização. As quotas AmlCompute são atribuídas ao nível de subscrição, mas agora permitimos que distribua essa quota entre espaços de trabalho e aloque-a para uma partilha e governação justas. Basta clicar na lâmina **Utilizações+Quotas** na barra de navegação esquerda do seu espaço de trabalho e selecione o **separador Quotas de Configuração.** Você deve ser um administrador de subscrição para ser capaz de definir quotas ao nível do espaço de trabalho, uma vez que esta é uma operação de espaço de trabalho transversal.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK para Python v1.0.55

+ **Novas funcionalidades**
  + A autenticação baseada em token é agora suportada para as chamadas feitas para o ponto final de pontuação implantado na AKS. Continuaremos a apoiar a autenticação baseada na chave atual e os utilizadores podem utilizar um destes mecanismos de autenticação de cada vez.
  + Capacidade de registar um armazenamento de bolhas que está por trás da rede virtual (VNet) como uma datastore.

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + Corrige um bug onde o tamanho de validação para divisões cv é pequeno e resulta em gráficos maus previstos vs. gráficos verdadeiros para regressão e previsão.
    + O registo de tarefas de previsão nas execuções remotas melhorou, agora o utilizador é fornecido com uma mensagem de erro completa se a execução tiver falhado.
    + Falhas fixas de `Timeseries` se a bandeira pré-processo é verdadeira.
    + Tornou algumas mensagens de erro de validação de dados de previsão mais acccáveis.
    + O consumo reduzido de memória de AutoML funciona baixando e/ou carregado preguiçoso de conjuntos de dados, especialmente entre desovas de processos
  + **azureml-contrib-explain-model**
    + A bandeira de model_task adicionada aos explicadores para permitir que o utilizador substitua a lógica de inferência automática predefinido para o tipo de modelo
    + Alterações widget: Instala-se automaticamente com `contrib` , sem mais `nbextension` instalação/ativação - explicação de suporte com importância global de recurso (por exemplo, Permutativo)
    + Mudanças no painel de instrumentos: - Enredos de caixa e enredos de violino para além `beeswarm` de traçar em página de resumo - Reenendere de enredo muito mais rápido na mudança de `beeswarm` slider 'Top -k' - mensagem útil explicando como o top-k é computado - Mensagens personalizáveis úteis no lugar de gráficos quando os dados não fornecidos
  + **azureml-core**
    + Método de modelo.pacote adicionado para criar imagens Docker e Dockerfiles que encapsulam os modelos e suas dependências.
    + Serviços web locais atualizados para aceitar InferenceConfigs contendo objetos ambientais.
    + Modelo Fixo.registo() que produz modelos inválidos quando '.' (para o diretório atual) é passado como o parâmetro model_path.
    + Adicione Run.submit_child, os espelhos de funcionalidade Experiment.sub-, enquanto especifica a execução como o progenitor da corrida da criança submetida.
    + Opções de configuração de suporte a partir de Model.register em Run.register_model.
    + Capacidade de gerir trabalhos jar em cluster existente.
    + Agora apoiando instance_pool_id e cluster_log_dbfs_path parâmetros.
    + Suporte adicional para a utilização de um objeto Ambiente ao implementar um Modelo num Webservice. O objeto Ambiente pode agora ser fornecido como parte do objeto InferenceConfig.
    + Adicione o mapeamento appinsifht para novas regiões - central - westus - northcentralus
    + Documentação adicionada para todos os atributos em todas as classes Datastore.
    + Adicionado blob_cache_timeout parâmetro a `Datastore.register_azure_blob_container` .
    + Adicionados save_to_directory e load_from_directory métodos azureml.core.environment.Environment.
    + Acrescentou o "download do ambiente az ml" e "az ml environment register" ao CLI.
    + Método Environment.add_private_pip_wheel adicionado.
  + **azureml-explain-modelo**
    + Rastreio de conjunto de dados adicionado às explicações utilizando o serviço Dataset (pré-visualização).
    + Diminuição do tamanho do lote padrão ao transmitir explicações globais de 10k a 100.
    + Adicionado model_task bandeira aos explicadores para permitir que o utilizador substitua a lógica de inferência automática predefinido para o tipo de modelo.
  + **azureml-mlflow**
    + Bug fixo em mlflow.azureml.build_image onde os diretórios aninhados são ignorados.
  + **azureml-pipeline-steps**
    + Capacidade acrescida de executar trabalhos jar no cluster Azure Databricks existente.
    + Apoio adicionado instance_pool_id e cluster_log_dbfs_path parâmetros para passo DatabricksStep.
    + Suporte adicionado para parâmetros de pipeline na etapa DatabricksStep.
  + **azureml-train-automl**
    + Adicionado `docstrings` para os ficheiros relacionados com o Ensemble.
    + Documentos atualizados para linguagem mais apropriada para `max_cores_per_iteration` e `max_concurrent_iterations`
    + O registo de tarefas de previsão nas execuções remotas melhorou, agora o utilizador é fornecido com uma mensagem de erro completa se a execução tiver falhado.
    + Removido get_data do caderno do `automlstep` gasoduto.
    + Começou a apoiar `dataprep` em `automlstep` .

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning Data Prep SDK v1.1.10

+ **Novas funcionalidades**
  + Pode agora solicitar a execução de inspetores específicos (por exemplo, histograma, enredo de dispersão, etc.) em colunas específicas.
  + Adicionei um argumento paralelista a `append_columns` . Se for verdade, os dados serão carregados na memória, mas a execução será executada em paralelo; se for falsa, a execução será transmitida, mas de um só fio.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK para Python v1.0.53

+ **Novas funcionalidades**
  + Machine Learning automatizado agora suporta a formação de modelos ONNX no alvo de computação remota
  + O Azure Machine Learning oferece agora a capacidade de retomar o treino a partir de uma execução anterior, de um ponto de verificação ou de ficheiros de modelos.
    + Saiba como [usar os estimadores para retomar o treino de uma corrida anterior](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Correções e melhorias de bugs**
  + **azure-cli-ml**
    + O CLI comanda "model deploy" e "service update" agora aceitam parâmetros, ficheiros config ou uma combinação dos dois. Os parâmetros têm precedência sobre atributos em ficheiros.
    + A descrição do modelo pode agora ser atualizada após o registo
  + **azureml-automl-core**
    + Atualize a dependência nimbusML para a versão 1.2.0 (mais recente).
    + A adição de suporte aos estimadores NimbusML & oleodutos a utilizar dentro dos estimadores AutoML.
    + Fixar um bug no procedimento de seleção do Ensemble que estava a crescer desnecessariamente o conjunto resultante, mesmo que as pontuações permanecessem constantes.
    + Permitir a reutilização de algumas ações através de CV Splits para tarefas de previsão. Isto acelera o tempo de execução da configuração executado por aproximadamente um fator de n_cross_validations para ações caras como lags e janelas rolantes.
    + Abordar um problema se o tempo estiver fora do intervalo de tempo suportado por pandas. Agora levantamos uma DataException se o tempo for menor que o pd. Timetamp.min ou maior que o pd. Timetamp.max
    + A previsão permite agora diferentes frequências em conjuntos de comboios e testes se puderem ser alinhados. Por exemplo, "trimestralmente a partir de janeiro" e "trimestralmente a partir de outubro" pode ser alinhado.
    + Os "parâmetros" da propriedade foram adicionados ao TimeSeriesTransformer.
    + Remova as velhas aulas de exceção.
    + Nas tarefas de previsão, o `target_lags` parâmetro aceita agora um único valor inteiro ou uma lista de inteiros. Se o inteiro foi fornecido, apenas um lag será criado. Se for fornecida uma lista, serão tomados os valores únicos dos lags. target_lags=[1, 2, 2, 4] criará lags de um, dois e quatro períodos.
    + Fixar o bug sobre a perda de tipos de colunas após a transformação (bug ligado);
    + Em `model.forecast(X, y_query)` , permitir y_query ser um tipo de objeto que contenha Nenhum(s) no início (#459519).
    + Adicione valores esperados à `automl` saída
  + **azureml-contrib-datadrift**
    +  Melhorias no portátil, incluindo a mudança para conjuntos de dados abertos azureml em vez de conjuntos de dados abertos azureml-contrib e melhorias de desempenho ao enriquecer dados
  + **azureml-contrib-explain-model**
    + Argumento de transformações fixas para explicador lime para a importância bruta de recurso no pacote azureml-contrib-explain-model
    + Segmentações adicionadas a explicações de imagem no explicador de imagem para o pacote AzureML-contrib-explain-model
    + Adicione suporte scipy sparse para LimeExplainer
    + Adicionado `batch_size` ao explicador de imitação quando `include_local=False` , para streaming de explicações globais em lotes para melhorar o tempo de execução do DecisionTreeExpicinableModel
  + **azureml-contrib-featureengineering**
    + Correção para chamar set_featurizer_timeseries_params(): alteração do tipo de valor dict e verificação nula - Adicione caderno para `timeseries`  o aperitivo
    + Atualize a dependência nimbusML para a versão 1.2.0 (mais recente).
  + **azureml-core**
    + Adicionou a capacidade de anexar datas da DBFS no CLI AzureML
    + Corrigiu o bug com o upload da loja de dados onde é criada uma pasta vazia se `target_path` iniciada com `/`
    + Problema fixo na Autorização de `deepcopy` ServiçoPrincipal.
    + Acrescentou o "az ml environment show" e "az ml environment list" comandos para o CLI.
    + Os ambientes agora apoiam especificar um base_dockerfile como alternativa a um base_image já construído.
    + A definição de RunConfiguration não-used auto_prepare_environment foi marcada como prefeita.
    + A descrição do modelo pode agora ser atualizada após o registo
    + Bugfix: O modelo e a eliminação de imagem agora fornecem mais informações sobre a recuperação de objetos a montante que dependem deles se a eliminação falhar devido a uma dependência a montante.
    + Bug fixo que imprimiu a duração em branco para implementações que ocorrem ao criar um espaço de trabalho para alguns ambientes.
    + Melhoria das exceções ao insucesso para a criação de espaço de trabalho. De tal forma que os utilizadores não vêem "Incapaz de criar espaço de trabalho. Incapaz de encontrar..." como a mensagem e, em vez disso, ver o fracasso real da criação.
    + Adicione suporte para autenticação simbólica em webservices AKS.
    + Adicione `get_token()` o método aos `Webservice` objetos.
    + Suporte CLI adicionado para gerir conjuntos de dados de aprendizagem automática.
    + `Datastore.register_azure_blob_container` agora opcionalmente leva um `blob_cache_timeout` valor (em segundos) que configura os parâmetros de montagem blobfuse para permitir a expiração da cache para esta datastore. O padrão não é um tempo limite, como quando uma bolha é lida, ele permanecerá na cache local até que o trabalho esteja terminado. A maioria dos postos de trabalho preferirá este cenário, mas alguns empregos precisam de ler mais dados de um grande conjunto de dados do que caberão nos seus nós. Para estes trabalhos, a afinação deste parâmetro irá ajudá-los a ter sucesso. Tenha cuidado ao sintonizar este parâmetro: definir o valor demasiado baixo pode resultar num mau desempenho, uma vez que os dados utilizados numa época podem expirar antes de serem novamente utilizados. Todas as leituras serão feitas a partir de armazenamento/rede blob em vez da cache local, que impacta negativamente os tempos de treino.
    + A descrição do modelo pode agora ser corretamente atualizada após o registo
    + A eliminação de modelos e imagem agora fornece mais informações sobre objetos a montante que dependem deles, o que faz com que a eliminação falhe
    + Melhorar a utilização de recursos de execuções remotas utilizando azureml.mlflow.
  + **azureml-explain-modelo**
    + Argumento de transformações fixas para explicador lime para a importância bruta de recurso no pacote azureml-contrib-explain-model
    + adicionar suporte scipy sparse para LimeExplainer
    + invólucro de explicador linear de forma adicionada, bem como outro nível para explicador tabular para explicar modelos lineares
    + para imitar explicador na biblioteca de modelos, erro fixo quando include_local=Falso para a entrada de dados escassos
    + adicionar valores esperados à `automl` saída
    + permuta fixa característica importância quando o argumento de transformações fornecido para obter importância de recurso bruto
    + adicionado `batch_size` ao imitador quando `include_local=False` , para streaming de explicações globais em lotes para melhorar o tempo de execução do DecisionTreeExpicinableModel
    + para a biblioteca de explicações de modelo, explicadores fixos blackbox onde a entrada de dataframe pandas é necessária para a previsão
    + Consertado um inseto onde `explanation.expected_values` às vezes devolveva um carro alegórico em vez de uma lista com um carro alegórico.
  + **azureml-mlflow**
    + Melhorar o desempenho dos mlflow.set_experiment(experiment_name)
    + Corrigir bugs na utilização de InteractiveLoginAuthentication para mlflow tracking_uri
    + Melhorar a utilização de recursos de execuções remotas utilizando azureml.mlflow.
    + Melhorar a documentação do pacote azureml-mlflow
    + Bug de remendos onde mlflow.log_artifacts("my_dir") salvaria artefactos sob "my_dir/<> de artefactos" em vez de "> <artefactos".
  + **azureml-opendatasets**
    + Pin `pyarrow` of to old `opendatasets` vers (<0.14.0) por causa do problema de memória recentemente introduzido lá.
    + Mova os conjuntos de dados azureml-contrib-open para conjuntos de dados abertos azureml.
    + Permitir que as aulas de conjunto de dados abertas sejam registadas no espaço de trabalho Azure Machine Learning e aproveite perfeitamente as capacidades do Conjunto de Dados AML.
    + Melhore o desempenho noaaIsdWeather enriquece significativamente o desempenho na versão não-SPARK.
  + **azureml-pipeline-steps**
    + A DBFS Datastore está agora suportada para entradas e saídas em DatabricksStep.
    + Documentação atualizada para o Azure Batch Step no que diz respeito a entradas/saídas.
    + Em AzureBatchStep, alterou *delete_batch_job_after_finish* valor padrão para *verdadeiro*.
  + **azureml-telemetria**
    +  Mova os conjuntos de dados azureml-contrib-open para conjuntos de dados abertos azureml.
    + Permitir que as aulas de conjunto de dados abertas sejam registadas no espaço de trabalho Azure Machine Learning e aproveite perfeitamente as capacidades do Conjunto de Dados AML.
    + Melhore o desempenho noaaIsdWeather enriquece significativamente o desempenho na versão não-SPARK.
  + **azureml-train-automl**
    + A documentação atualizada sobre get_output para refletir o tipo real de devolução e fornecer notas adicionais sobre a recuperação de propriedades-chave.
    + Atualize a dependência nimbusML para a versão 1.2.0 (mais recente).
    + adicionar valores esperados à `automl` saída
  + **azureml-train-core**
    + As cordas são agora aceites como alvo de computação para a sintonização de hiperparímetro automatizado
    + A definição de RunConfiguration não-used auto_prepare_environment foi marcada como prefeita.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning Data Prep SDK v1.1.9

+ **Novas funcionalidades**
  + Suporte adicionado para ler um ficheiro diretamente a partir de um url http ou https.

+ **Correções e melhorias de bugs**
  + Mensagem de erro melhorada ao tentar ler um Conjunto de Dados Parquet a partir de uma fonte remota (que não é suportada atualmente).
  + Corrigiu um bug ao escrever no formato de ficheiro Parquet na ADLS Gen 2 e actualizáu o nome do contentor ADLS Gen 2 no caminho.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Visual Interface
+ **Funcionalidades de pré-visualização**
  + Módulo "Executar script R" na interface visual.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK para Python v1.0.48

+ **Novas funcionalidades**
  + **azureml-opendatasets**
    + **azureml-contrib-opendatasets** está agora disponível como **conjuntos de dados abertos azureml**. O antigo pacote ainda pode funcionar, mas recomendamos que utilize **conjuntos de dados abertos azureml** para avançar para capacidades e melhorias mais ricas.
    + Este novo pacote permite-lhe registar conjuntos de dados abertos como Dataset no espaço de trabalho Azure Machine Learning e aproveitar todas as funcionalidades que o Dataset oferece.
    + Também inclui capacidades existentes, tais como o consumo de conjuntos de dados abertos como os dataframes pandas/SPARK, e a localização junta-se para alguns conjuntos de dados como o tempo.

+ **Funcionalidades de pré-visualização**
    + O HyperDriveConfig pode agora aceitar o objeto do gasoduto como parâmetro para suportar a afinação do hiperparímetro utilizando um oleoduto.

+ **Correções e melhorias de bugs**
  + **azureml-train-automl**
    + Corrigiu o bug sobre a perda de tipos de colunas após a transformação.
    + Corrigiu o erro para permitir que y_query seja um tipo de objeto que contenha nenhum(s) no início.
    + Corrigiu o problema no processo de seleção do Ensemble que estava a crescer desnecessariamente o conjunto resultante, mesmo que as pontuações permanecessem constantes.
    + Corrigiu o problema com permitir list_models e bloquear as definições de list_models em AutoMLStep.
    + Corrigiu o problema que impedia a utilização do pré-processamento quando a AutoML teria sido utilizada no contexto dos Gasodutos Azure ML.
  + **azureml-opendatasets**
    + Moveu osdatas azureml-contrib-open para conjuntos de dados abertos azureml.
    + Permitiu que as aulas de conjunto de dados abertas fossem registadas no espaço de trabalho Azure Machine Learning e alavancar as capacidades do Conjunto de Dados AML de forma perfeita.
    + Melhorada NoaaIsdWeather enriquece significativamente o desempenho na versão não-SPARK.
  + **azureml-explain-modelo**
    + Documentação on-line atualizada para objetos de interpretação.
    + Adicionado `batch_size` ao explicador de imitação quando `include_local=False` , para streaming de explicações globais em lotes para melhorar o tempo de execução de DecisionTreeExpicinableModel para biblioteca de explicações de modelos.
    + Corrigiu a questão `explanation.expected_values` onde, por vezes, devolveva um carro alegórico em vez de uma lista com um carro alegórico.
    + Acrescentou valores esperados à `automl` saída para o explicador de imitação na biblioteca do modelo de explicação.
    + A permutação fixa tem importância quando o argumento das transformações fornecido para obter uma importância bruta de recurso.
  + **azureml-core**
    + Adicionou a capacidade de anexar as datas da DBFS no CLI AzureML.
    + Corrigiu o problema com o upload da datastore onde é criada uma pasta vazia se `target_path` for iniciada com `/` .
    + Comparação ativada de dois conjuntos de dados.
    + O Modelo e a Imagem eliminam agora mais informações sobre a recuperação de objetos a montante que dependem deles se a eliminação falhar devido a uma dependência a montante.
    + Depreceu o cenário de RunConfiguration não-used em auto_prepare_environment.
  + **azureml-mlflow**
    + Melhor utilização de recursos de execuções remotas que utilizam fluxo azureml.ml.
    + Melhorou a documentação do pacote azureml-mlflow.
    + Corrigiu a questão em que mlflow.log_artifacts("my_dir") salvaria artefactos em "my_dir/caminhos de artefactos" em vez de "caminhos de artefactos".
  + **azureml-pipeline-core**
    + O parâmetro hash_paths para todas as etapas do gasoduto é depreciado e será removido no futuro. Por predefinição, o conteúdo do source_directory é hashed (exceto ficheiros listados em `.amlignore` ou `.gitignore` )
    + Continuar a melhorar o Módulo e o ModuleStep para suportar módulos específicos do tipo computacional, para preparar a integração runConfiguration e outras alterações para desbloquear a utilização de módulos específicos do tipo computacional em oleodutos.
  + **azureml-pipeline-steps**
    + AzureBatchStep: Documentação melhorada no que diz respeito a entradas/saídas.
    + AzureBatchStep: Alterado delete_batch_job_after_finish valor padrão para verdadeiro.
  + **azureml-train-core**
    + As cordas são agora aceites como alvo de computação para a sintonização de hiperparaítmetro automatizado.
    + Depreceu o cenário de RunConfiguration não-used em auto_prepare_environment.
    + Parâmetros precitados `conda_dependencies_file_path` e a favor e `pip_requirements_file_path` `conda_dependencies_file` `pip_requirements_file` respectivamente.
  + **azureml-opendatasets**
    + Melhore o desempenho noaaIsdWeather enriquece significativamente o desempenho na versão não-SPARK.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK para Python v1.0.33 lançado.

+ Azure ML Hardware Modelos Acelerados em [FPGAs](how-to-deploy-fpga-web-service.md) estão geralmente disponíveis.
  + Agora pode [utilizar o pacote de modelos azureml-accel](how-to-deploy-fpga-web-service.md) para:
    + Treine os pesos de uma rede neural profunda suportada (ResNet 50, ResNet 152, DenseNet-121, VGG-16 e SSD-VGG)
    + Utilize a aprendizagem de transferência com o DNN suportado
    + Registe o modelo com o Serviço de Gestão de Modelos e containerize o modelo
    + Implemente o modelo num Azure VM com um FPGA num cluster Azure Kubernetes Service (AKS)
  + Implementar o recipiente num dispositivo de servidor [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)
  + Marque os seus dados com o ponto final do GRPC com esta [amostra](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Machine Learning Automatizado

+ Varredura de funcionalidades para permitir a adição dinâmica :::no-loc text="featurizers"::: para otimização de desempenho. Novo :::no-loc text="featurizers"::: : incorporações de trabalho, peso de evidência, codificações de alvo, codificação do alvo de texto, distância do cluster
+ Cv inteligente para lidar com divisões de comboio/válido dentro do ML automatizado
+ Poucas alterações na otimização da memória e melhoria do desempenho em tempo de execução
+ Melhoria do desempenho na explicação do modelo
+ Conversão do modelo ONNX para execução local
+ Suporte adicional de subsampling
+ Paragem Inteligente quando não definidos critérios de saída
+ Conjuntos empilhados

+ Previsão de Série Temporal
  + Nova função de previsão de previsão
  + Agora pode utilizar a validação cruzada de origem rolante nos dados da série de tempo
  + Nova funcionalidade adicionada à configuração de séries de tempo lags
  + Nova funcionalidade adicionada para suportar funcionalidades agregadas de janelas rolantes
  + Nova deteção e afetante de férias quando o código do país é definido em configurações de experiências

+ Azure Databricks
  + Previsão de séries de tempo ativadas e capacidade de explicação/interpretação do modelo
  + Agora pode cancelar e retomar (continuar) experiências automatizadas de ML
  + Suporte adicional para processamento multicore

### <a name="mlops"></a>MLOps
+ **Destacamento local & depuração para contentores de pontuação**<br/> Agora pode implementar um modelo ML localmente e iterar rapidamente no seu ficheiro de pontuação e dependências para garantir que se comportam como esperado.

+ **Introduzido InferenceConfig & Modelo.deploy()**<br/> A implementação do modelo suporta agora especificar uma pasta de origem com um script de entrada, o mesmo que um RunConfig.  Além disso, a implementação do modelo foi simplificada para um único comando.

+ **Rastreio de referência de Git**<br/> Os clientes têm vindo a solicitar capacidades básicas de integração do Git há algum tempo, uma vez que ajudam a manter um rasto completo de auditoria. Implementamos o rastreio em todas as principais entidades do Azure ML para metadados relacionados com Git (repo, cometer, estado limpo). Estas informações serão recolhidas automaticamente pelo SDK e pelo CLI.

+ **Serviço de validação de & de perfis de modelos**<br/> Os clientes queixam-se frequentemente da dificuldade de dimensionar adequadamente o cálculo associado ao seu serviço de inferência. Com o nosso serviço de perfis de modelo, o cliente pode fornecer entradas de amostra e vamos perfilar-nos em 16 configurações diferentes de CPU/memória para determinar o tamanho ideal para a implementação.

+ **Traga a sua própria imagem base para inferência**<br/> Outra queixa comum foi a dificuldade em passar da experimentação para a inferência RE partilhar dependências. Com a nossa nova capacidade de partilha de imagens base, pode agora reutilizar as suas imagens base de experimentação, dependências e tudo, para inferência. Isto deve acelerar as implementações e reduzir a distância do interior para o laço exterior.

+ **Experiência melhorada da geração de esquemas de Swagger**<br/> O nosso método anterior de geração swagger era propenso a erros e impossível de automatizar. Temos uma nova forma em linha de gerar esquemas de swagger de qualquer função Python através de decoradores. Temos este código de código aberto e o nosso protocolo de geração de esquemas não está acopdo à plataforma Azure ML.

+ **Azure ML CLI está geralmente disponível (GA)**<br/> Os modelos podem agora ser implantados com um único comando CLI. Temos feedback comum dos clientes que ninguém implementa um modelo ML de um caderno Jupyter. A [**documentação de referência do CLI**](https://aka.ms/azmlcli) foi atualizada.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK para Python v1.0.30 lançado.

Foi [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py&preserve-view=true) introduzido para adicionar uma nova versão de um oleoduto publicado, mantendo o mesmo ponto final.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Portal do Azure
  + Agora pode reenviar uma execução de Script existente num cluster de computação remota existente.
  + Agora pode executar um oleoduto publicado com novos parâmetros no separador Pipelines.
  + Os detalhes da execução suportam agora um novo visualizador de ficheiros Snapshot. Pode ver uma imagem do diretório quando submeteu uma execução específica. Também pode descarregar o caderno que foi submetido para iniciar a corrida.
  + Agora pode cancelar as corridas dos pais a partir do portal Azure.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK para Python v1.0.23

+ **Novas funcionalidades**
  + O Azure Machine Learning SDK agora suporta Python 3.7.
  + Os estimadores Azure Machine Learning DNN agora fornecem suporte multi-versão incorporado. Por exemplo, `TensorFlow`   o estimador agora aceita um `framework_version` parâmetro, e os utilizadores podem especificar as versões '1.10' ou '1.12'. Para obter uma lista das versões suportadas pela sua versão atual do SDK, ligue `get_supported_versions()` para a classe-quadro desejada (por exemplo, `TensorFlow.get_supported_versions()` ).
  Para obter uma lista das versões suportadas pela mais recente versão SDK, consulte a documentação do [Estimador DNN](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py&preserve-view=true).

## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK para Python v1.0.21

+ **Novas funcionalidades**
  + O método *azureml.core.Run.create_children* permite a criação de baixa latência de múltiplas corridas infantis com uma única chamada.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK para Python v1.0.18

 + **Alterações**
   + O pacote azureml-tensorboard substitui o azureml-contrib-tensorboard.
   + Com esta versão, pode configurar uma conta de utilizador no seu cluster de computação gerido (amlcompute), enquanto a cria. Isto pode ser feito passando estas propriedades na configuração de provisionamento. Pode encontrar mais detalhes na documentação de referência da [SDK.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning Data Prep SDK v1.0.17

+ **Novas funcionalidades**
  + Agora suporta a adição de duas colunas numéricas para gerar uma coluna resultante usando a linguagem de expressão.

+ **Correções e melhorias de bugs**
  + Melhorou a documentação e a verificação de parâmetros para random_split.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning Data Prep SDK v1.0.16

+ **Correção de insetos**
  + Corrigiu um problema de autenticação principal de serviço que foi causado por uma alteração da API.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK para Python v1.0.17

+ **Novas funcionalidades**
  + A Azure Machine Learning agora fornece suporte de primeira classe para o popular DNN framework Chainer. A utilização de [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py&preserve-view=true) utilizadores de classe pode facilmente treinar e implementar modelos Chainer.
    + Saiba como [executar o treino distribuído com chainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/distributed-chainer/distributed-chainer.ipynb)
    + Aprenda a [executar afinação de hiperparímetro com Chainer usando HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/b881f78e4658b4e102a72b78dbd2129c24506980/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + A azure Machine Learning Pipelines adicionou capacidade de desencadear uma execução de Pipeline com base em modificações de datastore. O [caderno de horários do](https://aka.ms/pl-schedule) pipeline é atualizado para mostrar esta funcionalidade.

+ **Correções e melhorias de bugs**
  + Adicionámos suporte em oleodutos Azure Machine Learning para definir a propriedade source_directory_data_store para uma loja de dados desejada (como um armazenamento de [bolhas) em RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py&preserve-view=true) que são fornecidas ao [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py&preserve-view=true). Por predefinição, utilize a loja de ficheiros Azure como a loja de dados de suporte, que pode colidir com problemas de estrangulamento quando um grande número de passos são executados simultaneamente.

### <a name="azure-portal"></a>Portal do Azure

+ **Novas funcionalidades**
  + Nova experiência de drag e drop table editor para relatórios. Os utilizadores podem arrastar uma coluna do poço para a área da mesa onde será exibida uma pré-visualização da tabela. As colunas podem ser reorganizadas.
  + Novo visualizador de ficheiros de Logs
  + Links para execuções experimentais, cálculo, modelos, imagens e implementações a partir do separador de atividades

## <a name="next-steps"></a>Passos seguintes

Leia a descrição geral do [Azure Machine Learning](overview-what-is-azure-ml.md).
