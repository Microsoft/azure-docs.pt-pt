---
title: Quais as novidades no lançamento?
titleSuffix: Azure Machine Learning
description: Conheça as mais recentes atualizações para o Azure Machine Learning e a aprendizagem automática e a preparação de dados Python SDKs.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 11b8ade765a2b1c1ee25421073983b96c34e5d15
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462178"
---
# <a name="azure-machine-learning-release-notes"></a>Notas de lançamento de Azure Machine Learning

Neste artigo, conheça os lançamentos de Azure Machine Learning.  Para obter o conteúdo completo de referência do SDK, visite o [**principal SDK**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) da Azure Machine Learning para a página de referência python.

Consulte [a lista de questões conhecidas](resource-known-issues.md) para conhecer bugs conhecidos e salões.

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0"></a>Azure Machine Learning SDK para Python v1.1.1rc0

+ **Correções e melhorias de bugs**
  + **azure-cli-ml**
    + O perfil de instância única foi fixado para elaborar uma recomendação e foi disponibilizado no núcleo sdk.
  + **azureml-automl-core**
    + O registo de erros foi melhorado.
  + **azureml-automl-runtime**
    + Corrigiu o problema com a previsão quando o conjunto de dados contém grãos curtos com lacunas de longo tempo.
    + Fixou a questão quando o horizonte automático max é ligado e a coluna de data contém datas em forma de cordas. Adicionámos uma conversão adequada e um erro sensato se a conversão até à data não for possível
    + Utilização de Numpy e SciPy nativos para serializar e desserializar dados intermédios para FileCacheStore (utilizado para executagens automl locais)
    + Fixou um inseto onde as corridas de crianças falhadas podem ficar presas no estado de corrida.
  + **azureml-cli-comum**
    + O perfil de instância única foi fixado para elaborar uma recomendação e foi disponibilizado no núcleo sdk.
  + **núcleo azureml**
    + Adicionado `--grant-workspace-msi-access` como um parâmetro adicional para o Datastore CLI para registar o Contentor Blob Azure que lhe permitirá registar o Blob Container que está por trás de um VNet
    + O perfil de instância única foi fixado para elaborar uma recomendação e foi disponibilizado no núcleo sdk.
    + Corrigiu a questão em _deploy aks.py
    + Valida a integridade dos modelos que estão a ser carregados para evitar falhas de armazenamento silenciosos.
  + **azureml-interpretar**
    + acrescentou exceções em estilo azureml para interpretação em azureml
    + Série de deepScoringExplainer fixa para modelos keras
  + **azureml-pipeline-core**
    + O bloco de pontuação do lote do pipeline agora usa ParallelRunStep
  + **azureml-pipeline-steps**
    + Mudou a `AutoMLStep` no pacote de `azureml-pipeline-steps`. Deprecei o `AutoMLStep` dentro `azureml-train-automl-runtime`.
  + **azureml-contrib-pipeline-steps**
    + O parâmetro opcional side_inputs adicionado ao ParallelRunStep. Este parâmetro pode ser utilizado para montar pasta no recipiente. Os tipos atualmente suportados são DataReference e PipelineData.
  + **azureml-tensorboard**
    + azureml-tensorboard atualizado para suportar o tensorflow 2.0
  + **azureml-train-automl-cliente**
    + problema de destaque da FeaturizationConfig que filtra a config de características personalizadas.
  + **azureml-train-automl-runtime**
    + Mudou a `AutoMLStep` no pacote de `azureml-pipeline-steps`. Deprecei o `AutoMLStep` dentro `azureml-train-automl-runtime`.
  + **azureml-train-core**
    + Apoiar a versão 1.4 do PyTorch no Estimativador pyTorch
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0"></a>Azure Machine Learning SDK para Python v1.1.0rc0

+ **Quebrar alterações**
  + **Versonia semântica 2.0.0**
    + Começando com a versão 1.1 Azure ML Python SDK adota versão semântica 2.0.0. [Leia mais aqui](https://semver.org/). Todas as versões subsequentes seguirão um novo esquema de numeração e um contrato de versão semântica. 
  
+ **Correções e melhorias de bugs**
  + **azureml-automl-runtime**
    + Aumento da velocidade da caracterização.
    + Fixação da verificação de frequênciadurante a pontuação, agora nas tarefas de previsão não exigimos uma equivalência de frequência rigorosa entre o comboio e o conjunto de testes.
  + **núcleo azureml**
    + O utilizador pode agora especificar um valor para a tecla auth ao regenerar as chaves para os serviços web.
  + **azureml-interpretar**
    + Interpretação azureml atualizada para depender da comunidade interpretativa 0.5.0
  + **azureml-pipeline-core**
    + Fixou um bug onde os resultados do PythonScriptStep podem ser reutilizados incorretamente, apesar de alterar a lista de argumentos
  + **azureml-pipeline-steps**
    + Exemplo de documentação adicionada para conjunto de dados como entrada PythonScriptStep
  + **azureml-contrib-pipeline-steps**
    + Os parâmetros passados em ParallelRunConfig podem ser substituídos passando agora os parâmetros do gasoduto. Novos parâmetros de gasoduto suportados aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count e aml_process_count_per_node já fazem parte da libertação anterior).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK para Python v 1.0.85

+ **Novas funcionalidades**
  + **núcleo azureml**
    + Obtenha o uso principal atual e a limitação de cota para recursos de AmlCompute em um determinado espaço de trabalho e assinatura
  
  + **azureml-contrib-pipeline-steps**
    + Habilitar o usuário a passar o conjunto de resultados de tabela como resultado intermediário da etapa anterior para parallelrunstep

+ **Correções e melhorias de bugs**
  + **azureml-automl-runtime**
    + Retirou a exigência da coluna y_query no pedido ao serviço de previsão implantado. 
    + O 'y_query' foi removido da secção de pedidos de serviço de portátil Orange Juice do Dominick.
    + Correção do bug que impede a previsão nos modelos implantados, operando em conjuntos de dados com colunas de data e hora.
    + O coeficiente de correlação de Matthews foi adicionado como uma métrica de classificação, para classificação binária e multiclasse.
  + **azureml-contrib-interpret**
    + Os explicadores de texto removidos do azureml-contrib-interprete como explicações de texto foram movidos para o repositório de texto de interpretação que será liberado em breve.
  + **núcleo azureml**
    + Conjunto de conjuntos: os usos do conjunto de arquivos não dependem mais do numpy e do pandas para serem instalados no Python env.
    + Mudou o LocalWebservice.wait_for_deployment() para verificar o estado do contentor local Docker antes de tentar pingar o seu ponto final de saúde, reduzindo consideravelmente o tempo que leva para reportar uma implementação falhada.
    + Correção da inicialização de uma propriedade interna usada em LocalWebservice. reload () quando o objeto de serviço é criado a partir de uma implantação existente usando o Construtor LocalWebservice ().
    + Mensagem de erro editada para fins de esclarecimento.
    + Adicioneum novo método chamado get_access_token() ao AksWebservice que devolverá o objeto AksServiceAccessToken, que contém token de acesso, atualização após carimbo de tempo, expiração no tempo e tipo de token. 
    + Método de get_token() existente no AksWebservice, à medida que o novo método devolve toda a informação que este método devolve.
    + Saída modificada do comando AZ ml Service Get-Access-token. Token renomeado para accessToken e refreshBy para refreshAfter. Propriedades expiryOn e TokenType adicionadas.
    + get_ative_runs fixa
  + **modelo azureml-explicação**
    + atualizado o shap para 0.33.0 e interprete-Community para 0,4. *
  + **azureml-interpretar**
    + atualizado o shap para 0.33.0 e interprete-Community para 0,4. *
  + **azureml-train-automl-runtime**
    + O coeficiente de correlação de Matthews foi adicionado como uma métrica de classificação, para classificação binária e multiclasse.
    + Substituir o sinalizador de pré-processamento do código e substituído por personalização-personalização está ativado por padrão

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK para Python v1.0.83

+ **Novas funcionalidades**
  + Conjunto de dados: Adicione duas opções `on_error` e `out_of_range_datetime` para `to_pandas_dataframe` falhar quando os dados têm valores de erro em vez de os encher com `None`.
  + Espaço de trabalho: Adicionou a bandeira `hbi_workspace` para espaços de trabalho com dados sensíveis que permitem encriptação adicional e desativa diagnósticos avançados em espaços de trabalho. Também adicionámos suporte para trazer as suas próprias chaves para a instância cosmos DB associada, especificando os parâmetros `cmk_keyvault` e `resource_cmk_uri` ao criar um espaço de trabalho, que cria uma instância Cosmos DB na sua subscrição enquanto aprovisiona o seu espaço de trabalho. [Leia mais aqui.](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-cosmos-db)

+ **Correções e melhorias de bugs**
  + **azureml-automl-runtime**
    + Fixou uma regressão que fez com que um TypeError fosse levantado ao executar o AutoML nas versões Python abaixo de 3.5.4.
  + **núcleo azureml**
    + Inseto fixo em `datastore.upload_files` onde o caminho relativo que não começou com `./` não foi capaz de ser usado.
    + Mensagens de depreciação adicionadas para todos os códigos de classe de imagem
    + Construção de URL de Gestão de Modelos Fixos para a região de Mooncake.
    + Problema fixo em que os modelos que usam source_dir não podiam ser embalados para funções Azure.    
    + Adicionou uma opção ao [Ambiente.build_local()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) para empurrar uma imagem para o registo de contentores do espaço de trabalho AzureML
    + Atualizou o SDK para usar uma nova biblioteca simbólica em sinapse azul de uma forma compatível com as costas.
  + **azureml-interpretar**
    + Bug fixo onde Nenhum foi devolvido quando não havia explicações disponíveis para download. Agora levanta uma exceção, combinando comportamento em outro lugar.
  + **azureml-pipeline-steps**
    + A passagem proibida `DatasetConsumptionConfig`para o parâmetro de `inputs` da `Estimator`quando o `Estimator` será utilizado num `EstimatorStep`.
  + **azureml-sdk**
    + Adicionou o cliente AutoML ao pacote azureml-sdk, permitindo que as execuções automl remotas sejam submetidas sem instalar o pacote autoML completo.
  + **azureml-train-automl-cliente**
    + Alinhamento corrigido na saída da consola para corridas de automl
    + Fixe um bug onde a versão incorreta dos pandas possa ser instalada em amlcompute remoto.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK para Python v1.0.81

+ **Correções e melhorias de bugs**
  + **azureml-contrib-interpret**
    + adiar a dependência shap para interpretar a comunidade de azureml-interpret
  + **núcleo azureml**
    + O alvo computacional pode agora ser especificado como parâmetro para os objetos config de implantação correspondentes. Este é especificamente o nome do alvo computacional para, não para o objeto SDK.
    + Acrescentou informações criadas por objetos de modelo e serviço. Pode ser acedido através de <var>.created_by
    + Recipiente FixoImage.run(), que não estava corretamente a montar a porta HTTP do contentor Docker.
    + Torne `azureml-dataprep` opcional para `az ml dataset register` comando cli
    + Fixou um bug onde `TabularDataset.to_pandas_dataframe` recaía incorretamente para um leitor alternativo e imprimia um aviso.
  + **modelo azureml-explicação**
    + adiar a dependência shap para interpretar a comunidade de azureml-interpret
  + **azureml-pipeline-core**
    + Acrescentou novo passo de oleoduto `NotebookRunnerStep`, para executar um caderno local como um passo em pipeline.
    + Funções de get_all deprecatadas removidas para Pipelines, Horários e PipelineEndpoints
  + **azureml-train-automl-cliente**
    + Começou a depreciação de data_script como entrada para a AutoML.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK para Python v1.0.79

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + Removido caracterizaçãoConfig a ser registrado
      + Registo atualizado para registar "auto"/"off"/"personalizado".
  + **azureml-automl-runtime**
    + Apoio adicional aos pandas. Séries e pandas. Categórico para detetar o tipo de dados da coluna. Anteriormente apenas suportado numpy.ndarray
      + Adicione alterações de código relacionadas para lidar corretamente com dtype categórico.
    + A interface da função de previsão foi melhorada: o parâmetro de y_pred foi opcional. \- Os docstrings foram melhorados.
  + **conjunto de dados azureml-contrib**
    + Fixou um inseto onde os conjuntos de dados rotulados não podiam ser montados.
  + **núcleo azureml**
    + Correção de erros para `Environment.from_existing_conda_environment(name, conda_environment_name)`. O utilizador pode criar uma instância de Ambiente que é uma réplica exata do ambiente local
    + Alterou os métodos de Datasets relacionados com séries de tempo para `include_boundary=True` por padrão.
  + **azureml-train-automl-cliente**
    + Problema fixo onde os resultados da validação não são impressos quando a saída do espetáculo está definida como falsa.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK para Python v1.0.76

+ **Quebrar alterações**
  + Problemas de atualização Azureml-Train-AutoML
    + A atualização para azureml-train-automl>=1.0.76 do azureml-train-automl<1.0.76 pode causar instalações parciais, fazendo com que algumas importações de automl falhem. Para resolver isto, pode executar o script de configuração encontrado em https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd. Ou se estiver a usar o pip diretamente, pode:
      + "pip instalar -upgrade azureml-train-automl"
      + "pip install --ignore-instalação azureml-train-automl-cliente"
    + ou pode desinstalar a versão antiga antes de atualizar
      + "pip desinstalar azureml-train-automl"
      + "pip instalar azureml-train-automl"

+ **Correções e melhorias de bugs**
  + **azureml-automl-runtime**
    + A AutoML terá agora em conta as classes verdadeiras e falsas ao calcular as métricas médias de escalar para tarefas de classificação binária.
    + Código de aprendizagem e formação de máquinas movidas em AzureML-AutoML-Core para um novo pacote AzureML-AutoML-Runtime.
  + **conjunto de dados azureml-contrib**
    + Ao ligar `to_pandas_dataframe` num conjunto de dados rotulado com a opção de descarregamento, pode agora especificar se deve ou não substituir os ficheiros existentes.
    + Ao ligar para `keep_columns` ou `drop_columns` que resulte na queda de uma série de tempo, etiqueta ou coluna de imagem, as capacidades correspondentes serão também eliminadas para o conjunto de dados.
    + Corrigiu um problema com o carregador de maçarico para a tarefa de deteção de objetos.
  + **azureml-contrib-interpret**
    + Widget de painel de explicação removido de azureml-contrib-interpret, alterou o pacote para referência ao novo em interpret_community
    + Versão atualizada da comunidade interpretativa para 0.2.0
  + **núcleo azureml**
    + Melhorar o desempenho do `workspace.datasets`.
    + Acrescentou a capacidade de registar a Datastore de Dados da Base de Dados Azure SQL utilizando o nome de utilizador e a autenticação de senhas
    + Correção para carregar Configurações run de caminhos relativos.
    + Ao ligar `keep_columns` ou `drop_columns` que resulte na queda de uma coluna de séries de tempo, as capacidades correspondentes serão também eliminadas para o conjunto de dados.
  + **azureml-interpretar**
    + Versão atualizada da comunidade interpretativa para 0.2.0
  + **azureml-pipeline-steps**
    + Valores suportados documentados para `runconfig_pipeline_params` para passos de gasoduto de aprendizagem automática azul.
  + **azureml-pipeline-core**
    + Adicionou a opção CLI para descarregar a saída em formato json para comandos Pipeline.
  + **azureml-train-automl**
    + Split AzureML-Train-AutoML em 2 pacotes, um pacote de cliente AzureML-Train-AutoML-Client e um pacote de treino ML AzureML-Train-AutoML-Runtime
  + **azureml-train-automl-cliente**
    + Adicionou um cliente fino para submeter experiências AutoML sem precisar de instalar quaisquer dependências de aprendizagem automática localmente.
    + Registo fixo de lags automaticamente detetados, tamanhos de janela sinuosos e horizontes máximos nas corridas remotas.
  + **azureml-train-automl-runtime**
    + Adicione iu a um novo pacote AutoML para isolar os componentes de machine learning e runtime do cliente.
  + **azureml-contrib-train-rl**
    + Acrescentou apoio de aprendizagem de reforço no SDK.
    + Adicionei suporte AmlWindowsCompute em RL SDK.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK para Python v1.0.74

  + **Funcionalidades de pré-visualização**
    + **conjunto de dados azureml-contrib**
      + Depois de importar o conjunto de dados azureml-contrib, pode ligar para `Dataset.Labeled.from_json_lines` em vez de `._Labeled` para criar um conjunto de dados rotulado.
      + Ao ligar `to_pandas_dataframe` num conjunto de dados rotulado com a opção de descarregamento, pode agora especificar se deve ou não substituir os ficheiros existentes.
      + Ao ligar `keep_columns` ou `drop_columns` que resulte na queda de uma série de tempo, etiqueta ou coluna de imagem, as capacidades correspondentes serão também eliminadas para o conjunto de dados.
      + Problemas fixos com carregador PyTorch ao ligar para `dataset.to_torchvision()`.

+ **Correções e melhorias de bugs**
  + **azure-cli-ml**
    + Adicionou Model Profiling à pré-visualização CLI.
    + Correções quebrando a mudança no Armazenamento Azure fazendo com que o AzureML CLI falhe.
    + Tipo de balanceor de carga adicionado ao MLC para tipos AKS
  + **azureml-automl-core**
    + Corrigiu a questão com a deteção do horizonte máximo em séries temporais, tendo valores em falta e vários grãos.
    + Corrigiu a questão com falhas durante a geração de divisões de validação cruzada.
    + Substitua esta secção por uma mensagem em formato de marcação para aparecer nas notas de lançamento: -Melhor manuseamento de grãos curtos nos conjuntos de dados de previsão.
    + Corrigiu o problema com a máscara de algumas informações do utilizador durante o registo. -Registo melhorado dos erros durante as previsões.
    + Adicionando o psutil como uma dependência de conda ao ficheiro de implantação yml gerado automaticamente.
  + **azureml-contrib-mir**
    + Correções quebrando a mudança no Armazenamento Azure fazendo com que o AzureML CLI falhe.
  + **núcleo azureml**
    + Corrige um bug que fez com que os modelos implantados nas Funções Azure produzissem 500s.
    + Corrigiu uma questão em que o ficheiro amlignore não foi aplicado em instantâneos.
    + Adicionou um novo API amlcompute.get_ative_runs que devolve um gerador para funcionamento e corridas em fila numa dada amlcompute.
    + Adicionou o tipo de balanceor de carga ao MLC para tipos AKS.
    + Acrescentou append_prefix parâmetro bool para download_files em run.py e download_artifacts_from_prefix em artifacts_client. Esta bandeira é utilizada para aplainar seletivamente o ficheiro de origem, pelo que apenas o nome do ficheiro ou da pasta é adicionado ao output_directory
    + Corrija o problema de desserialização para `run_config.yml` com a utilização do conjunto de dados.
    + Ao ligar `keep_columns` ou `drop_columns` que resulte na queda de uma coluna de séries temporais, as capacidades correspondentes serão também eliminadas para o conjunto de dados.
  + **azureml-interpretar**
    + Versão interpretativa atualizada para 0.1.0.3
  + **azureml-train-automl**
    + Corrigiu um problema em que automl_step pode não imprimir problemas de validação.
    + Os register_model fixos para ter sucesso mesmo que o ambiente do modelo esteja a faltar dependências localmente.
    + Corrigiu um problema em que algumas operações remotas não estavam ativadas.
    + Adicione o registo da exceção que está a fazer com que uma corrida local falhe prematuramente.
  + **azureml-train-core**
    + Considere resume_from corre no cálculo da afinação automática de hiperparâmetros melhores corridas para crianças.
  + **azureml-pipeline-core**
    + Manuseamento fixo do parâmetro na construção de argumentos de gasoduto.
    + Acrescentou a descrição do gasoduto e o parâmetro de yaml tipo passo.
    + Novo formato de yaml para passo pipeline e aviso de depreciação adicionado para formato antigo.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Experiência web

A página de aterragem colaborativa no espaço de trabalho em [https://ml.azure.com](https://ml.azure.com) foi melhorada e renomeada como estúdio Azure Machine Learning (pré-visualização).

A partir do estúdio, pode treinar, testar, implementar e gerir ativos de Machine Learning Azure, tais como conjuntos de dados, pipelines, modelos, pontos finais e muito mais.

Aceda às seguintes ferramentas de autoria baseadas na Web do estúdio:

| Ferramenta baseada na Web | Descrição | Edição |
|-|-|-|
| VM do caderno (pré-visualização) | Estação de trabalho totalmente gerida em nuvem | Básico e Empresa |
| [Aprendizagem automática de máquinas](tutorial-first-experiment-automated-ml.md) (pré-visualização) | Nenhuma experiência de código para automatizar o desenvolvimento do modelo de aprendizagem automática | Enterprise |
| [Designer](concept-designer.md) (pré-visualização) | Ferramenta de modelação de máquinas de drag-and-drop anteriormente conhecida como o designer | Enterprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Melhorias do designer de aprendizagem automática Azure

+ Anteriormente conhecida como interface visual 
+   11 novos [módulos,](algorithm-module-reference/module-reference.md) incluindo recomendadores, classificadores e serviços de formação, incluindo engenharia de recursos, validação cruzada e transformação de dados.

### <a name="r-sdk"></a>SDK para R 
 
Os cientistas de dados e desenvolvedores de IA usam o [Azure Machine Learning SDK para R](tutorial-1st-r-experiment.md) para construir e executar fluxos de trabalho de aprendizagem automática com azure machine learning.

O Azure Machine Learning SDK for R utiliza o pacote `reticulate` para se ligar ao Python SDK. Ao ligar-se diretamente à Python, o SDK para R permite-lhe aceder a objetos e métodos fundamentais implementados no Python SDK a partir de qualquer ambiente R que escolha.

As principais capacidades do SDK incluem:

+   Gerir recursos de cloud para monitorizar, registar e organizar as experimentações de machine learning.
+   Modelos de comboio que utilizam recursos em nuvem, incluindo treino de modelo acelerado por GPU.
+   Implemente os seus modelos como serviços web em Casos de Contentores Azure (ACI) e Serviço Azure Kubernetes (AKS).

Consulte o site do [pacote](https://azure.github.io/azureml-sdk-for-r) para obter documentação completa.

### <a name="azure-machine-learning-integration-with-event-grid"></a>Integração de Machine Learning Azure com Grelha de Eventos 

O Azure Machine Learning é agora um fornecedor de recursos para a Rede de Eventos, pode configurar eventos de aprendizagem automática através do portal Azure ou do Azure CLI. Os utilizadores podem criar eventos para a conclusão de execução, registo de modelos, implementação de modelos e deriva de dados detetada. Estes eventos podem ser encaminhados para manipuladores de eventos apoiados pela Event Grid para consumo. Consulte o [esquema](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning)do evento de machine learning, [conceitos](https://docs.microsoft.com/azure/machine-learning/concept-event-grid-integration) e artigos [tutoriais](https://docs.microsoft.com/azure/machine-learning/how-to-use-event-grid) para mais detalhes.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK para Python v1.0.72

+ **Novas funcionalidades**
  + Os monitores adicionados do conjunto de dados através do pacote de deriva de [**dados azureml,** ](https://docs.microsoft.com/python/api/azureml-datadrift) permitindo monitorizar os conjuntos de dados da série de tempo para deriva de dados ou outras alterações estatísticas ao longo do tempo. Os alertas e eventos podem ser desencadeados se a deriva for detetada ou se forem satisfeitas outras condições nos dados. Consulte a [nossa documentação](https://aka.ms/datadrift) para mais detalhes.
  + Anunciando duas novas edições (também referidas como Um SKU intercambiavelmente) em Azure Machine Learning. Com este lançamento pode agora criar um espaço de trabalho Basic ou Enterprise Azure Machine Learning. Todos os espaços de trabalho existentes estarão em incumprimento na edição Basic, e você pode ir ao portal Azure ou ao estúdio para atualizar o espaço de trabalho a qualquer momento. Pode criar um espaço de trabalho Basic ou Enterprise a partir do portal Azure. Por favor, leia a [nossa documentação](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) para saber mais. A partir do SDK, a edição do seu espaço de trabalho pode ser determinada usando a propriedade "sku" do seu objeto espaço de trabalho.
  + Também fizemos melhorias na Computação de Aprendizagem automática Azure - agora pode ver métricas para os seus clusters (como nós totais, nós de corrida, quota total de núcleo) no Monitor Azure, além de ver registos de diagnóstico para depuração. Além disso, também pode ver corridas em execução ou filas no seu cluster e detalhes como os IPs dos vários nós no seu cluster. Pode vê-las no portal ou utilizando as funções correspondentes no SDK ou CLI.

  + **Funcionalidades de pré-visualização**
    + Estamos a lançar suporte de pré-visualização para encriptação de discos do seu SSD local em Azure Machine Learning Compute. Por favor, levante um bilhete de suporte técnico para obter a sua subscrição whitelist para usar esta funcionalidade.
    + Pré-visualização pública da Inferência do Lote de Aprendizagem automática Azure. A Inferência do Lote de Aprendizagem automática Azure tem como alvo grandes inferências que não são sensíveis ao tempo. A Inferência do Lote fornece uma escala de inferência eficaz em termos de custos, com uma produção incomparável para aplicações assíncronas. Está otimizado para inferência de alta-adgente, fogo e esquecimento sobre grandes coleções de dados.
    + [**conjunto de dados azureml-contrib**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
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
    + ClI conjunto de dados adicionado. Para mais informações: `az ml dataset --help`
    + Suporte adicional para modelos suportados pela implementação e embalagem (ONNX, scikit-learn e TensorFlow) sem uma instância inferenceConfig.
    + Bandeira de sobreutilização adicionada para implantação de serviços (ACI e AKS) em SDK e CLI. Se fornecido, substituirá o serviço existente se o serviço com nome já existir. Se o serviço não existir, criará um novo serviço.
    + Os modelos podem ser registados com dois novos quadros, Onnx e Tensorflow. - O registo do modelo aceita dados de entrada de amostras, dados de saída de amostras e configuração de recursos para o modelo.
  + **azureml-automl-core**
    + Treinar uma iteração só funcionaria num processo infantil quando se estabelecem restrições de tempo de corrido.
    + Adicione um guarda-costas para as tarefas de previsão, para verificar se um max_horizon especificado causará ou não um problema de memória na máquina dada. Se assim for, será exibida uma mensagem de guarda-costas.
    + Suporte adicional para frequências complexas como 2 anos e 1 mês. -Mensagem de erro compreensível adicionada se a frequência não puder ser determinada.
    + Adicione falhas em azureml ao condomínio gerado automaticamente para resolver a falha de implementação do modelo
    + Permitir que os dados intermédios no Pipeline de Aprendizagem automática Azure sejam convertidos para conjunto de dados tabular e utilizados em `AutoMLStep`.
    + Atualização de propósito de coluna implementada para streaming.
    + Implementou a atualização do parâmetro do transformador para o Imputer e o HashOneHotEncoder para streaming.
    + Adicionou o tamanho atual dos dados e o tamanho mínimo exigido dos dados às mensagens de erro de validação.
    + Atualizou o tamanho mínimo exigido para a validação cruzada para garantir um mínimo de duas amostras em cada dobra de validação.
  + **azureml-cli-comum**
    + O CLI suporta agora a embalagem do modelo.
    + Os modelos podem ser registados com dois novos quadros, Onnx e Tensorflow.
    + O registo do modelo aceita dados de entrada de amostra, dados de saída de amostras e configuração de recursos para o modelo.
  + **azureml-contrib-gbdt**
    + fixou o canal de lançamento para o caderno
    + Adicionei um aviso para o alvo de computação não AmlCompute que não apoiamos
    + Estimativa LightGMB adicionada ao pacote azureml-contrib-gbdt
  + [**núcleo azureml**](https://docs.microsoft.com/python/api/azureml-core)
    + O CLI suporta agora a embalagem do modelo.
    + Adicione um aviso de depreciação para APIs de conjunto de dados precatiados. Consulte o aviso de alteração da Dataset API em https://aka.ms/tabular-dataset.
    + Alterar [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) para devolver o nome e a versão do registo se o conjunto de dados estiver registado.
    + Corrija um bug que scriptRunConfig com conjunto de dados como argumento não pode ser usado repetidamente para submeter a execução de experiências.
    + Os conjuntos de dados recuperados durante uma corrida serão rastreados e podem ser vistos na página de detalhes da execução ou ligando [para`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) após a execução estar completa.
    + Permitir que os dados intermédios no Pipeline de Aprendizagem automática Azure sejam convertidos para conjunto de dados tabular e utilizados em [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep).
    + Suporte adicional para modelos suportados pela implementação e embalagem (ONNX, scikit-learn e TensorFlow) sem uma instância inferenceConfig.
    + Bandeira de sobreutilização adicionada para implantação de serviços (ACI e AKS) em SDK e CLI. Se fornecido, substituirá o serviço existente se o serviço com nome já existir. Se o serviço não existir, criará um novo serviço.
    +  Os modelos podem ser registados com dois novos quadros, Onnx e Tensorflow. O registo do modelo aceita dados de entrada de amostra, dados de saída de amostras e configuração de recursos para o modelo.
    + Adicionei uma nova loja de dados para a Base de Dados Azure para o MySQL. Exemplo adicional para a utilização da Base de Dados Azure para MySQL em DataTransferStep em Pipelines de Aprendizagem automática Azure.
    + Funcionalidade adicionada para adicionar e remover tags de experiências Adicionar funcionalidade adicional para remover tags de executões
    + Bandeira de sobreutilização adicionada para implantação de serviços (ACI e AKS) em SDK e CLI. Se fornecido, substituirá o serviço existente se o serviço com nome já existir. Se o serviço não existir, criará um novo serviço.
  + [**deriva de dados azureml**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + Mudou-se de `azureml-contrib-datadrift` para `azureml-datadrift`
    + Apoio acrescido à monitorização dos conjuntos de dados da série de tempo para deriva e outras medidas estatísticas
    + Novos métodos `create_from_model()` e `create_from_dataset()` para a aula [de`DataDriftDetector`.](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) O método `create()` será depreciado.
    + Ajustes nas visualizações em Python e UI no estúdio Azure Machine Learning.
    + Suporte semanal e mensal de programação de monitores, além de diariamente para monitores de conjunto de dados.
    + Suporte o backfill das métricas de monitor de dados para analisar dados históricos para monitores de conjuntos de dados.
    + Várias correções de bugs
  + [**azureml-pipeline-core**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + A azureml-dataprep já não é necessária para apresentar um Pipeline de Aprendizagem automática Azure executado a partir do gasoduto `yaml` ficheiro.
  + [**azureml-train-automl**](/python/api/azureml-train-automl-runtime/)
    + Adicione falhas em azureml ao condomínio gerado automaticamente para resolver a falha de implementação do modelo
    + O treino remoto AutoML inclui agora incumprimentos em azureml para permitir a reutilização do env de treino para inferência.
  + **azureml-train-core**
    + Suporte pyTorch 1.3 adicionado no [estimamr`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch)

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Interface visual (pré-visualização)

+ A interface visual Azure Machine Learning (pré-visualização) foi revista para ser executada em [pipelines de Aprendizagem automática Azure](concept-ml-pipelines.md). Os gasodutos (anteriormente conhecidos como experiências) da autoria da interface visual estão agora totalmente integrados com a experiência core Azure Machine Learning.
  + Experiência de gestão unificada com ativos SDK
  + Versão e rastreio para modelos de interface visual, oleodutos e pontos finais
  + UI redesenhado
  + Implantação de inferência de lote adicionado
  + Suporte ao Serviço Azure Kubernetes (AKS) para alvos de computação de inferência
  + Novo gasoduto python-step que autoriza fluxo de trabalho
  + Nova [página de aterragem](https://ml.azure.com) para ferramentas de autoria visual

+ **Novos módulos**
  + Aplicar operação matemática
  + Aplicar transformação SQL
  + Valores de clipe
  + Resumir dados
  + Importação a partir da base de dados SQL

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK para Python v1.0.69

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + Limitando as explicações do modelo para melhor executar em vez de calcular explicações para cada execução. Fazer esta mudança de comportamento para locais, remotos e ADB.
    + Apoio acrescido a explicações de modelos a pedido para a UI
    + Acrescentou o PSUTIL como dependência de `automl` e incluiu o psutil como dependência de conda em amlcompute.
    + Corrigiu o problema com lags heurísticos e tamanhos de janela sinuosos nos conjuntos de dados de previsão algumas séries que podem causar erros lineares de álgebra
      + Impressão adicionada para os parâmetros heuristicamente determinados nas corridas de previsão.
  + **deriva de dados azureml-contrib**
    + Proteção adicionada ao mesmo tempo que cria métricas de saída se a deriva do nível de conjunto de dados não estiver na primeira secção.
  + **azureml-contrib-interpret**
    + pacote azureml-contrib-explica-modelo foi renomeado para azureml-contrib-interpret
  + **núcleo azureml**
    + Adicionou API para desregistar conjuntos de dados. `dataset.unregister_all_versions()`
    + O pacote azureml-contrib-explain-model foi renomeado para interpretação azureml-contrib.
  + **[núcleo azureml](https://docs.microsoft.com/python/api/azureml-core)**
    + Adicionou API para desregistar conjuntos de dados. conjunto de dados. [unregister_all_versions()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + A API de Dataset adicionado para verificar os dados alterados de tempo. `dataset.data_changed_time`.
    + Poder consumir `FileDataset` e `TabularDataset` como inputs para `PythonScriptStep`, `EstimatorStep`e `HyperDriveStep` no Pipeline de Aprendizagem automática Azure
    + O desempenho do `FileDataset.mount` foi melhorado para pastas com um grande número de ficheiros
    + Ser capaz de consumir [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset) e [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset) como inputs para [PythonScriptStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)e [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) no Pipeline de Aprendizagem automática Azure.
    + Desempenho do FileDataset. [montagem()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none-) foi melhorado para pastas com um grande número de ficheiros
    + Adicione URL a recomendações de erro conhecidas em detalhes de execução.
    + Fixou um inseto em run.get_metrics onde os pedidos falhariam se uma corrida tivesse muitas crianças
    + Fixou um inseto em [run.get_metrics](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) onde os pedidos falhariam se uma corrida tivesse muitas crianças
    + Suporte adicional para autenticação no cluster Arcadia.
    + A criação de um objeto Experiment obtém ou cria a experiência no espaço de trabalho azure machine learning para rastreio de histórico. A experiência ID e o tempo arquivado são povoados no objeto experimento sobre a criação. Exemplo: experiência = Experiência (espaço de trabalho, "Nova Experiência") experiment_id = experiment.id arquivo() e reativar() são funções que podem ser chamadas numa experiência para esconder e restaurar a experiência de ser mostrada no UX ou devolvida por padrão numa chamada para listar experiências. Se uma nova experiência for criada com o mesmo nome que uma experiência arquivada, pode renomear a experiência arquivada ao reativar passando um novo nome. Só pode haver uma experiência ativa com um dado nome. Exemplo: experiment1 = Experiment (espaço de trabalho, experiência "Experiência Ativa") experiência1.archive() # Criar uma nova experiência ativa com o mesmo nome que o arquivado. experiência2. = Experimento (espaço de trabalho, experiência "Experiência Ativa") experimento1.reativar(new_name="Experiência Ativa Anterior") A lista de métodos estáticos() no Experimento pode ter um filtro de nome e filtro ViewType. Os valores do ViewType são "ATIVE_ONLY", "ARCHIVED_ONLY" e "ALL" Exemplo: archived_experiments = Experimento.list (workspace, view_type="ARCHIVED_ONLY") all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
    + Suporte utilizando o ambiente para implementação de modelos e atualização de serviço
  + **deriva de dados azureml**
    + O atributo da classe DataDriftDector já não suporta argumentos opcionais 'with_details'. O atributo do show só apresentará coeficiente de deriva de dados e contribuição de deriva de dados de colunas de funcionalidades.
    + DataDriftDetector atribui alterações de comportamento 'get_output':
      + Parâmetro de entrada start_time, end_time são opcionais em vez de obrigatórios;
      + A entrada específica start_time e/ou end_time com um run_id específico na mesma invocação resultará em exceção de erro de valor por serem mutuamente exclusivas
      + Por entrada específica start_time e/ou end_time, apenas serão devolvidos os resultados das corridas programadas;
      + O parâmetro 'daily_latest_only' está deprecida.
    + Suporte a recuperação de saídas dataset-based.
  + **modelo azureml-explicação**
    + Rebatiza o pacote de modelo sinuoso azureML para a interpretação da AzureML, mantendo por agora o pacote antigo para retrocompatibilidade
    + bug `automl` fixo com explicações brutas definidas para tarefa de classificação em vez de regressão por padrão no download do ExplanationClient
    + Adicione suporte para `ScoringExplainer` a criar diretamente usando `MimicWrapper`
  + **azureml-pipeline-core**
    + Melhor desempenho para a criação de grandes gasodutos
  + **azureml-train-core**
    + Suporte TensorFlow 2.0 adicionado no Estimativador TensorFlow
  + **azureml-train-automl**
    + A criação de um objeto [Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment) obtém ou cria a experiência no espaço de trabalho azure machine learning para rastreio de histórico. A experiência ID e o tempo arquivado são povoados no objeto experimento sobre a criação. Exemplo:

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [arquivo()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--) e [reativar()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) são funções que podem ser chamadas numa experiência para esconder e restaurar a experiência de ser mostrada no UX ou devolvida por padrão numa chamada para listar experiências. Se uma nova experiência for criada com o mesmo nome que uma experiência arquivada, pode renomear a experiência arquivada ao reativar passando um novo nome. Só pode haver uma experiência ativa com um dado nome. Exemplo:

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        A lista de métodos [estáticos()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) no Experimento pode ter um filtro de nome e filtro ViewType. Os valores do ViewType são "ATIVE_ONLY", "ARCHIVED_ONLY" e "ALL". Exemplo:

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Suporte utilizando o ambiente para a implementação de modelos e atualização de serviço.
  + **[deriva de dados azureml](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + O atributo da classe [DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) já não suporta argumentos opcionais 'with_details'. O atributo do show só apresentará coeficiente de deriva de dados e contribuição de deriva de dados de colunas de funcionalidades.
    + Função DataDriftDetector [get_output]https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) alterações de comportamento:
      + Parâmetro de entrada start_time, end_time são opcionais em vez de obrigatórios;
      + A entrada específica start_time e/ou end_time com um run_id específico na mesma invocação resultará numa exceção de erro de valor por serem mutuamente exclusivas;
      + Por entrada específica start_time e/ou end_time, apenas serão devolvidos os resultados das corridas programadas;
      + O parâmetro 'daily_latest_only' está deprecida.
    + Suporte a recuperação de saídas dataset-based.
  + **[modelo azureml-explicação](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Rebatiza o pacote de modelo sinuoso azureML para a interpretação do AzureML, mantendo por agora o pacote antigo para retrocompatibilidade.
    + bug autoML fixo com explicações brutas definidas para tarefa de classificação em vez de regressão por padrão no download do ExplanationClient.
    + Adicione suporte para [scoringExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.scoring.scoring_explainer.scoringexplainer) a ser criado diretamente usando [MimicWrapper](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic_wrapper.mimicwrapper)
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Melhor desempenho para a grande criação de Pipeline.
  + **[azureml-train-core](https://docs.microsoft.com/python/api/azureml-train-core)**
    + Suporte TensorFlow 2.0 no [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow) Estimador.
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + A execução dos pais deixará de ser falhada quando a iteração da configuração falhou, uma vez que a orquestração já toma conta dela.
    + Acrescentou apoio local-estivador e conda local para experiências AutoML
    + Acrescentou apoio local-estivador e conda local para experiências AutoML.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nova experiência web (pré-visualização) para espaços de trabalho de Aprendizagem automática Azure

O separador Experiment no novo portal do [espaço de trabalho](https://ml.azure.com) foi atualizado para que os cientistas de dados possam monitorizar as experiências de uma forma mais performativa. Pode explorar as seguintes funcionalidades:
+ Experimente metadados para filtrar e classificar facilmente a sua lista de experiências
+ Simplificada e performante experiência detalhes páginas que permitem visualizar e comparar as suas execuções
+ Novo design para executar páginas de detalhes para entender e monitorizar os seus treinos

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK para Python v1.0.65

  + **Novas funcionalidades**
    + Acrescentou ambientes com curadoria. Estes ambientes foram pré-configurados com bibliotecas para tarefas comuns de aprendizagem automática, e foram pré-construídos e cached como imagens docker para uma execução mais rápida. Aparecem por defeito na lista de ambiente do Workspace, com prefixo "AzureML".
    + Acrescentou ambientes com curadoria. Estes ambientes foram pré-configurados com bibliotecas para tarefas comuns de aprendizagem automática, e foram pré-construídos e cached como imagens docker para uma execução mais rápida. Aparecem por defeito na lista de ambiente do [Workspace,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29)com prefixo "AzureML".

  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Acrescentou o suporte de conversão ONNX para o ADB e O HDI

+ **Funcionalidades de pré-visualização**
  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Suportado BERT e BiLSTM como recurso de texto (apenas pré-visualização)
    + Personalização de recursos suportado para fins de coluna e parâmetros de transformador (apenas pré-visualização)
    + Explicações cruas suportadas quando o utilizador permite explicações do modelo durante o treino (apenas pré-visualização)
    + Profeta adicionado para `timeseries` previsão como um gasoduto treinável (apenas pré-visualização)

  + **deriva de dados azureml-contrib**
    + Pacotes transferidos de dados azureml-contrib para deriva de dados azureml; o pacote `contrib` será removido em uma libertação futura

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + Apresentou a FeaturizationConfig à AutoMLConfig e autoMLBaseSettings
    + Apresentou a FeaturizationConfig à [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) e autoMLBaseSettings
      + Sobrepor o propósito da coluna para a caracterização com a coluna e o tipo de características
      + Parâmetros de transformação de sobreposição
    + Mensagem de depreciação acrescida para explain_model() e retrieve_model_explanations()
    + Profeta Adicionado como um gasoduto treinável (apenas pré-visualização)
    + Mensagem de depreciação adicional para explain_model() e retrieve_model_explanations().
    + Acrescentou profeta como um gasoduto treinável (apenas pré-visualização).
    + Suporte adicional para deteção automática de lags-alvo, tamanho da janela rolante e horizonte máximo. Se um dos target_lags, target_rolling_window_size ou max_horizon estiver definido para 'auto', a heurística será aplicada para estimar o valor do parâmetro correspondente com base em dados de formação.
    + Previsão fixa no caso em que o conjunto de dados contém uma coluna de grãos, este grão é de um tipo numérico e existe uma lacuna entre o conjunto de comboios e de ensaios
    + Fixou a mensagem de erro sobre o índice duplicado no ensaio remoto nas tarefas de previsão
    + Previsão fixa no caso em que o conjunto de dados contém uma coluna de grãos, este grão é de um tipo numérico e existe uma lacuna entre o conjunto de comboios e de ensaios.
    + Fixou a mensagem de erro sobre o índice duplicado no ensaio remoto nas tarefas de previsão.
    + Adicione um guarda-costas para verificar se um conjunto de dados está desequilibrado ou não. Se for, uma mensagem de guarda-costas seria escrita para a consola.
  + **núcleo azureml**
    + Capacidade acrescida de recuperar URL SAS para modelar no armazenamento através do objeto modelo. Ex: modelo.get_sas_url()
    + Introduzir `run.get_details()['datasets']` para obter conjuntos de dados associados à execução submetida
    + Adicione a API `Dataset.Tabular.from_json_lines_files` para criar um Conjunto TabularDataa a partir de ficheiros JSON Lines. Para saber mais sobre estes dados tabular em ficheiros JSON Lines no TabularDataset, visite https://aka.ms/azureml-data para obter documentação.
    + Adicionar amiúr adicional de campos de tamanho VM (Disco OS, número de GPUs) à função supported_vmsizes ()
    + Acrescentou campos adicionais à função list_nodes () para mostrar a execução, o IP privado e público, o porto, etc.
    + Capacidade de especificar um novo campo durante o fornecimento de clusters -- remotelogin_port_public_access que pode ser configurado para ativado ou desativado dependendo se você gostaria de deixar a porta SSH aberta ou fechada no momento da criação do cluster. Se não especificar, o serviço abrirá ou fechará a porta de forma inteligente, dependendo se está a implantar o cluster dentro de um VNet.
  + **modelo azureml-explicação**
  + **[núcleo azureml](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + Capacidade acrescida de recuperar URL SAS para modelar no armazenamento através do objeto modelo. Ex: modelo. [get_sas_url()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Introduza a corrida. [get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)['datasets'] para obter conjuntos de dados associados à execução submetida
    + Adicione `Dataset.Tabular`API. [from_json_lines_files()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) criar um Conjunto TabularDataset a partir de ficheiros JSON Lines. Para saber mais sobre estes dados tabular em ficheiros JSON Lines no TabularDataset, visite https://aka.ms/azureml-data para obter documentação.
    + Adicionados campos de tamanho VM adicionais (Disco OS, número de GPUs) à função [supported_vmsizes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + Acrescentou campos adicionais à função [list_nodes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) para mostrar a execução, o IP privado e público, o porto, etc.
    + Capacidade de especificar um novo campo durante [o fornecimento](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) de cluster`--remotelogin_port_public_access` que pode ser configurado para ativado ou desativado dependendo se você gostaria de deixar a porta SSH aberta ou fechada no momento da criação do cluster. Se não especificar, o serviço abrirá ou fechará a porta de forma inteligente, dependendo se está a implantar o cluster dentro de um VNet.
  + **[modelo azureml-explicação](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Documentação melhorada para saídas de explicação no cenário de classificação.
    + Acrescentou a capacidade de carregar os valores y previstos na explicação para os exemplos de avaliação. Desbloqueia visualizações mais úteis.
    + Adicionei a propriedade explicadora ao MimicWrapper para permitir obter o MimicExplainer subjacente.
  + **azureml-pipeline-core**
    + Caderno adicionado para descrever Módulo, ModuleVersion e ModuleStep
  + **azureml-pipeline-steps**
    + Adicionado RScriptStep para suportar a execução do script R através do pipeline AML.
    + Os parâmetros de metadados fixos que analisam o AzureBatchStep, o que estava a causar a "atribuição da mensagem de erro para o parâmetro SubscriptionId" não está especificado.
  + **azureml-train-automl**
    + Suporte training_data, validation_data, label_column_name, weight_column_name como formato de entrada de dados
    + Mensagem de depreciação acrescida para explain_model() e retrieve_model_explanations()
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Adicionei um [caderno](https://aka.ms/pl-modulestep) para descrever [módulo,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class)) [ModuleVersion](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.moduleversion) e [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep).
  + **[azureml-pipeline-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + Adicionado [RScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) para suportar a execução do script R através do pipeline AML.
    + Os parâmetros de metadados fixos que analisam o [AzureBatchStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) o que estava a causar a mensagem de erro "a atribuição de um parâmetro de subscrição Id não está especificada".
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Suportado training_data, validation_data, label_column_name, weight_column_name como formato de entrada de dados.
    + Mensagem de depreciação acrescida para [explain_model()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) e [retrieve_model_explanations()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-).


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK para Python v1.0.62

+ **Novas funcionalidades**
  + Introduziu o traço `timeseries` no TabularDataset. Este traço permite a filtragem fácil da marca de tempo em dados um TabularDataset, como tomar todos os dados entre um intervalo de tempo ou os dados mais recentes. Para saber sobre este tema o traço `timeseries` no TabularDataset, visite https://aka.ms/azureml-data para documentação ou https://aka.ms/azureml-tsd-notebook para um por exemplo por exemplo.
  + Formação habilitada com TabularDataset e FileDataset. Visite https://aka.ms/dataset-tutorial por exemplo.

  + **azureml-train-core**
    + Suporte `Nccl` e `Gloo` adicionado no estimador pyTorch

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + Deprectei a definição autoML 'lag_length' e o LaggingTransformer.
    + Validação corrigida correta dos dados de entrada se forem especificados num formato dataflow
    + Modificou o fit_pipeline.py para gerar o gráfico json e carregar para artefactos.
    + Renderizado o gráfico sob `userrun` utilizando `Cytoscape`.
  + **núcleo azureml**
    + Revisitei o tratamento de exceções no código ADB e efaz alterações de acordo com o novo manuseamento de erros
    + Adicionou a autenticação automática de MSI para VMs de caderno.
    + Corrige o bug onde modelos corruptos ou vazios podem ser carregados devido a tentativas falhadas.
    + Fixação do bug sempre que o `DataReference` nome se mude quando o modo `DataReference` muda (por exemplo, quando se liga para `as_upload`, `as_download`ou `as_mount`).
    + Torne `mount_point` e `target_path` opcionais para `FileDataset.mount` e `FileDataset.download`.
    + Exceção de que a coluna de carimbos de tempo não pode ser encontrada será descartada se o tempo relacionado com a API for chamado sem coluna de carimbo sinuoso atribuído ou se as colunas de carimbos de tempo atribuídos forem retiradas.
    + As colunas de séries de tempo devem ser atribuídas com coluna cujo tipo é Data, caso contrário, a exceção é esperada
    + As colunas de séries de tempo que designam a API 'with_timestamp_columns' podem ter nenhum nome de coluna de carimbo sinuoso/grosso, que irá limpar as colunas de carimbos de tempo previamente atribuídas.
    + A exceção será descartada quando for largada a coluna de carimbos de grãos grossos ou de grãos finos, indicando ao utilizador que a queda pode ser feita após excluir a coluna de carimbos de tempo na lista de queda ou ligar para with_time_stamp com nenhum valor para libertar o timestamp colunas
    + A exceção será descartada quando não estiver incluído grãos grossos ou colunas de carimbos de grãos finos na lista de colunas com indicação para o utilizador de que a manutenção pode ser feita após incluir a coluna de carimbos de tempo na lista de colunas ou chamar with_time_stamp com nenhuma valor para libertar colunas de carimbos de tempo.
    + Extraído registo para o tamanho de um modelo registado.
  + **modelo azureml-explicação**
    + Aviso fixo impresso para consolar quando não for instalado o pacote python "embalagem": "Utilizando versão mais antiga do que suportada de lightgbm, por favor atualize para versão superior a 2.2.1"
    + Explicação fixa do modelo de descarregamento com sharding para explicações globais com muitas funcionalidades
    + Explicador de imitação fixo que falta exemplos de inicialização na explicação da saída
    + Erro imutável fixo nas propriedades definidas ao carregar com cliente explicação usando dois tipos diferentes de modelos
    + Adicione um get_raw param para pontuar explicador .explicar () para que um explicador de pontuação possa devolver valores projetados e brutos.
  + **azureml-train-automl**
    + Introduziu APIs públicas da AutoML para apoiar explicações de `automl` explicar SDK - forma mais recente de apoiar explicações automl, dissociando a funcionalidade AutoML e explicando o Suporte de Explicação Bruta Integrado do Azureml explicar sDK para os modelos AutoML.
    + Eliminação de incumprimentos em azul de ambientes de treino remoto.
    + Alterou a localização da loja de cache padrão da FileCacheStore baseada numa para a AzureFileCacheStore uma para AutoML na via de código De Tijolos Azure.
    + Validação corrigida correta dos dados de entrada se forem especificados num formato dataflow
  + **azureml-train-core**
    + Revertido source_directory_data_store depreciação.
    + Capacidade adicional de sobrepor as versões de pacotes instalados em Azureml.
    + Suporte de estivador adicionado no `environment_definition` parâmetro em estimadores.
    + Parâmetros de treino distribuídos simplificados em estimadores.

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nova experiência web (pré-visualização) para espaços de trabalho de Aprendizagem automática Azure
A nova experiência web permite que cientistas de dados e engenheiros de dados completem o seu ciclo de vida de aprendizagem automática de ponta a ponta, desde a preparação e visualização de dados até à formação e implementação de modelos num único local.

![Espaço de trabalho Azure Machine Learning UI (pré-visualização)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Principais características:**

Utilizando esta nova interface azure machine learning, já pode:
+ Gerencie os seus cadernos ou ligue-se a Jupyter
+ [Executar experiências automatizadas de ML](tutorial-first-experiment-automated-ml.md)
+ [Criar conjuntos de dados a partir de ficheiros locais, lojas de dados e ficheiros web](how-to-create-register-datasets.md)
+ Explore e prepare conjuntos de dados para a criação de modelos
+ Monitorize a deriva de dados para os seus modelos
+ Ver recursos recentes a partir de um dashboard

No momento desta versão, os seguintes navegadores são suportados: Chrome, Firefox, Safari e Microsoft Edge Preview.

**Questões conhecidas:**

1. Refresque o seu navegador se vir "Algo correu mal! Error loading ficheiros de pedaços" quando a implementação está em curso.

1. Não é possível excluir ou renomear ficheiros em Cadernos e Ficheiros. Durante a Pré-Visualização Pública pode utilizar jupyter UI ou Terminal em VM portátil para efetuar operações de ficheiros de atualização. Por se trata de um sistema de ficheiros de rede montado, todas as alterações que fizer no Notebook VM são imediatamente refletidas no Espaço de Trabalho do Caderno.

1. Para ssh no Bloco de Notas VM:
   1. Encontre as teclas SSH que foram criadas durante a configuração da VM. Ou, encontre as chaves no espaço de trabalho Azure Machine Learning > abra o separador Compute > localize o Notebook VM na lista > abra as suas propriedades : copiar as chaves do diálogo.
   1. Importe as chaves SSH públicas e privadas para a sua máquina local.
   1. Use-os para SSH no Bloco de Notas VM.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK para Python v1.0.60

+ **Novas funcionalidades**
  + Introduzido FileDataset, que refere ficheiros únicos ou múltiplos nas suas lojas de dados ou urls públicos. Os ficheiros podem ser de qualquer formato. FileDataset fornece-lhe a capacidade de descarregar ou montar os ficheiros para a sua computação. Para saber mais sobre o FileDataset, visite https://aka.ms/file-dataset.
  + Suporte yaml de pipeline adicionado para Passo PythonScript, Passo Adla, Passo de Databricks, DataTransferStep e Passo AzureBatch

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + AutoArima é agora um oleoduto sugestível apenas para pré-visualização.
    + Relatório de erro melhorado para previsão.
    + Melhorou a exploração madeireira utilizando exceções personalizadas em vez de genéricas nas tarefas de previsão.
    + Removeu o controlo max_concurrent_iterations ser inferior ao número total de iterações.
    + Modelos AutoML agora devolvem AutoMLExceps
    + Esta versão melhora o desempenho da execução de corridas locais de aprendizagem automática de máquinas.
  + **núcleo azureml**
    + Introduza dataset.get_all (espaço de trabalho), que devolve um dicionário de objetos `TabularDataset` e `FileDataset` com o nome de registo.

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Introduza `parition_format` como argumento para `Dataset.Tabular.from_delimited_files` e `Dataset.Tabular.from_parquet.files`. As informações de partilha de cada caminho de dados serão extraídas em colunas com base no formato especificado. '{column_name}' cria coluna de cordas, e '{column_name:yyyy/MM/dd/HH/mm/ss}' cria coluna de data, onde 'yyyy', 'MM', 'dd', 'HH', 'mm' e 'ss' são utilizados para extrair ano, mês, dia, hora, minuto e segundo para o tipo de data. O partition_format deve partir da posição da primeira chave de partição até ao fim do caminho do ficheiro. Por exemplo, dado o caminho '. /USA/2019/01/01/data.csv' onde a partição é por país e tempo, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' cria coluna de cordas 'País' com valor 'EUA' e coluna de data 'PartitionDate' com valor '2019-01-01'.
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + Introduza `partition_format` como argumento para `Dataset.Tabular.from_delimited_files` e `Dataset.Tabular.from_parquet.files`. As informações de partilha de cada caminho de dados serão extraídas em colunas com base no formato especificado. '{column_name}' cria coluna de cordas, e '{column_name:yyyy/MM/dd/HH/mm/ss}' cria coluna de data, onde 'yyyy', 'MM', 'dd', 'HH', 'mm' e 'ss' são utilizados para extrair ano, mês, dia, hora, minuto e segundo para o tipo de data. O partition_format deve partir da posição da primeira chave de partição até ao fim do caminho do ficheiro. Por exemplo, dado o caminho '. /USA/2019/01/01/data.csv' onde a partição é por país e tempo, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' cria coluna de cordas 'País' com valor 'EUA' e coluna de data 'PartitionDate' com valor '2019-01-01'.
    + `to_csv_files` e `to_parquet_files` métodos foram adicionados à `TabularDataset`. Estes métodos permitem a conversão entre um `TabularDataset` e um `FileDataset`, convertendo os dados em ficheiros do formato especificado.
    + Inicie automaticamente o registo de imagem base ao guardar um Dockerfile gerado pelo Model.package().
    + «gpu_support» já não é necessário; A AML deteta e utiliza automaticamente a extensão do estivador nvidia quando está disponível. Será removido numa futura libertação.
    + Suporte adicionado para criar, atualizar e utilizar PipelineDrafts.
    + Esta versão melhora o desempenho da execução de corridas locais de aprendizagem automática de máquinas.
    + Os utilizadores podem consultar métricas do histórico de execução pelo nome.
    + Melhorou a exploração madeireira utilizando exceções personalizadas em vez de genéricas nas tarefas de previsão.
  + **modelo azureml-explicação**
    + Adicione feature_maps parâmetro ao novo MimicWrapper, permitindo que os utilizadores obtem explicações de funcionalidades cruas.
    + Os uploads do conjunto de dados estão agora desligados por padrão para upload de explicações, e podem ser reativados com upload_datasets=True
    + Adicioneos parâmetros de filtragem "is_law" à lista de explicações e às funções de descarregamento.
    + Adiciona método `get_raw_explanation(feature_maps)` a objetos de explicação globais e locais.
    + Verificação da versão adicionada ao lightgbm com aviso impresso se abaixo da versão suportada
    + Uso otimizado da memória ao lotear explicações
    + Modelos AutoML agora devolvem AutoMLExceps
  + **azureml-pipeline-core**
    + Suporte adicional para criar, atualizar e usar PipelineDrafts - pode ser usado para manter definições de gasoduto mutáveis e usá-los interativamente para executar
  + **azureml-train-automl**
    + Foi criada a funcionalidade para instalar versões específicas do maçarico pnquichal v1.1.0, :::no-loc text="cuda"::: conjunto de ferramentas 9.0, transformadores de maçarico, que é necessário para ativar o BERT/XLNet no ambiente remoto de tempo de execução da pitão.
  + **azureml-train-core**
    + Falha precoce de alguns erros de definição de espaço hiperparâmetro diretamente no sdk em vez do lado do servidor.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning Data Prep SDK v1.1.14
+ **Correções e melhorias de bugs**
  + Escrita habilitada para ADLS/ADLSGen2 utilizando caminho sonâmlo e credenciais.
  + Fixou um inseto que fez com que `include_path=True` não funcionasse durante `read_parquet`.
  + Falha de `to_pandas_dataframe()` fixa causada pela exceção "Valor imobiliário inválido: hostSecret".
  + Fixou um bug onde os ficheiros não podiam ser lidos no DBFS no modo Spark.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK para Python v1.0.57
+ **Novas funcionalidades**
  + Habilitado `TabularDataset` ser consumido sem edição. Para saber mais sobre `TabularDataset`, visite https://aka.ms/azureml/howto/createdatasets.

+ **Correções e melhorias de bugs**
  + **automl-cliente-core-nativocliente**
    + Fixao erro, levantado quando as etiquetas de treino e/ou validação (y e y_valid) são fornecidas sob a forma de quadro de dados pandas, mas não como matriz entorpecida.
    + Interface atualizada para criar uma `RawDataContext` apenas para exigir os dados e o objeto `AutoMLBaseSettings`.
    +  Permitir que os utilizadores da AutoML abandonem séries de treino que não são suficientemente longas na previsão. - Permitir que os utilizadores da AutoML retirem grãos do conjunto de ensaios que não existe no conjunto de treinos durante a previsão.
  + **azure-cli-ml**
    + Agora pode atualizar o certificado SSL para o ponto final de pontuação implantado no cluster AKS tanto para o certificado gerado pela Microsoft como para o certificado de cliente.
  + **azureml-automl-core**
    + Corrigiu um problema na AutoML onde as filas com etiquetas em falta não foram removidas corretamente.
    + Registo de erros melhorados no AutoML; as mensagens de erro completas serão agora sempre escritas no ficheiro de registo.
    + A AutoML atualizou o seu pacote de fixação para incluir `azureml-defaults`, `azureml-explain-model`e `azureml-dataprep`. A AutoML deixará de alertar para as incompatibilidades dos pacotes (exceto `azureml-train-automl` embalagem).
    + Corrigiu um problema em `timeseries` em que as divisões de CV são de tamanho desigual, fazendo com que o cálculo do lixo falhe.
    + Ao executar a iteração do conjunto para o tipo de treino de Validação Cruzada, se acabámos por ter dificuldades em descarregar os modelos treinados em todo o conjunto de dados, estávamos a ter uma inconsistência entre os pesos do modelo e os modelos que estavam a ser alimentados na votação conjunto.
    + Fixao erro, levantado quando as etiquetas de treino e/ou validação (y e y_valid) são fornecidas sob a forma de quadro de dados pandas, mas não como matriz entorpecida.
    + Corrigiu a questão com as tarefas de previsão quando nenhuma foi encontrada nas colunas booleanas das tabelas de entrada.
    + Permitir que os utilizadores da AutoML abandonem séries de treino que não são suficientemente longas na previsão. - Permitir que os utilizadores da AutoML retirem grãos do conjunto de ensaios que não existe no conjunto de treinos durante a previsão.
  + **núcleo azureml**
    + Problema fixo com blob_cache_timeout pedido de parâmetro.
    + Adicione ajuste externo e transforme tipos de exceção em erros do sistema.
    + Acrescentou apoio aos segredos do Cofre chave para corridas remotas. Adicione uma aula de cofre em azureml.core.keyvault para adicionar, obter e listar segredos do cofre associado ao seu espaço de trabalho. As operações apoiadas são:
      + azureml.core.workspace.workspace.get_default_keyvault()
      + azureml.core.keyvault.keyvault.set_secret (nome, valor)
      + azureml.core.keyvault.keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.keyvault.get_secret (nome)
      + azureml.core.keyvault.keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.keyvault.list_secrets()
    + Métodos adicionais para obter o cofre padrão e obter segredos durante o ensaio remoto:
      + azureml.core.workspace.workspace.get_default_keyvault()
      + azureml.core.run.run.get_secret (nome)
      + azureml.core.run.run.get_secrets(secrets_list)
    + Adicione parâmetros adicionais de sobreposição ao comando CLI de hiperdrive.
    + Melhorar a fiabilidade das chamadas API e expandir as tentativas de tentativas para exceções comuns da biblioteca de pedidos.
    + Adicione suporte para submeter as corridas de uma execução submetida.
    + Problema de ficha sas de expiração fixa no FileWatcher, que fez com que os ficheiros deixassem de ser carregados após o seu token inicial ter expirado.
    + Ficheiros HTTP csv/tsv suportados em conjunto de dados python SDK.
    + Deprecated o método Workspace.setup() A mensagem de aviso mostrada aos utilizadores sugere a utilização de criar() ou obter ()/from_config() () em vez disso.
    + Ambiente adicionado.add_private_pip_wheel(), que permite carregar pacotes privados de pitão personalizados `whl`para o espaço de trabalho e usá-los de forma segura para construir/materializar o ambiente.
    + Agora pode atualizar o certificado SSL para o ponto final de pontuação implantado no cluster AKS tanto para o certificado gerado pela Microsoft como para o certificado de cliente.
  + **modelo azureml-explicação**
    + Adicione um parâmetro para adicionar um ID modelo às explicações no upload.
    + Acrescentou `is_raw` marcação de explicações na memória e upload.
    + Suporte de maçarico adicionado e testes para pacote de modelo de explicação azureml.
  + **conjuntos de dados azureml-open**
    + Suporte de deteção e exploração automática ambiente de teste automático.
    + Aulas adicionadas para obter população americana por condado e zip.
  + **azureml-pipeline-core**
    + Propriedade de etiqueta adicionada às definições de entrada e saída da porta.
  + **azureml-telemetria**
    + Fixou uma configuração de telemetria incorreta.
  + **azureml-train-automl**
    + Fixou o bug onde, na falha de configuração, o erro não foi registado no campo de "erros" para a execução da configuração e, por conseguinte, não foi armazenado em "erros" de execução dos pais.
    + Corrigiu um problema na AutoML onde as filas com etiquetas em falta não foram removidas corretamente.
    + Permitir que os utilizadores da AutoML abandonem séries de treino que não são suficientemente longas na previsão.
    + Permitir que os utilizadores da AutoML retirem grãos do conjunto de ensaios que não existem no conjunto de treinos durante a previsão.
    + Agora, a AutoMLStep passa por `automl` config para evitar quaisquer problemas sobre alterações ou adições de novos parâmetros de config.
    + AutoML Data Guardrail está agora em pré-visualização pública. O utilizador verá um relatório do Data Guardrail (para tarefas de classificação/regressão) após o treino e também poderá aceder-lhe através da API SDK.
  + **azureml-train-core**
    + Suporte adicionado da tocha 1.2 no Estimativador PyTorch.
  + **azureml-widgets**
    + Gráficos de matriz de confusão melhorados para o treino de classificação.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning Data Prep SDK v1.1.12
+ **Novas funcionalidades**
  + As listas de cordas podem agora ser passadas como contributo para `read_*` métodos.

+ **Correções e melhorias de bugs**
  + O desempenho do `read_parquet` foi significativamente melhorado ao correr em Spark.
  + Corrigiu uma questão em que `column_type_builder` falhou no caso de uma única coluna com formatos de data ambígua.

### <a name="azure-portal"></a>Portal do Azure
+ **Recurso de pré-visualização**
  + O streaming de ficheiros de registo e saída está agora disponível para páginas de detalhes de execução. Os ficheiros transmitirão atualizações em tempo real quando o toggle de pré-visualização estiver ligado.
  + A capacidade de definir a quota a nível de espaço de trabalho é lançada na pré-visualização. As quotas AmlCompute são atribuídas ao nível da subscrição, mas agora permitimos que distribua essa quota entre espaços de trabalho e alocá-la para uma partilha e governação justas. Basta clicar na lâmina **Usages+Quotas** na barra de navegação esquerda do seu espaço de trabalho e selecione o separador **Quotas Configurar.**

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK para Python v1.0.55

+ **Novas funcionalidades**
  + A autenticação baseada em token é agora suportada para as chamadas feitas para o ponto final de pontuação implantado no AKS. Continuaremos a apoiar a autenticação baseada na chave atual e os utilizadores podem utilizar um destes mecanismos de autenticação de cada vez.
  + Capacidade de registar um armazenamento blob que está por trás da rede virtual (VNet) como uma loja de dados.

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + Corrige um bug onde o tamanho da validação para divisões de CV é pequeno e resulta em gráficos previsíveis vs. gráficos verdadeiros para regressão e previsão.
    + O registo de tarefas de previsão nas operações remotas melhorou, agora o utilizador é fornecido com uma mensagem de erro abrangente se a execução tiver falhado.
    + Falhas fixas de `Timeseries` se a bandeira pré-processada for verdadeira.
    + Tornou algumas mensagens de erro de validação de dados de previsão mais acionáveis.
    + O consumo reduzido de memória da AutoML é executado por queda e/ou carregamento preguiçoso de conjuntos de dados, especialmente entre os desovas do processo
  + **modelo azureml-contrib-explicar**
    + Acrescentou model_task bandeira aos explicadores para permitir que o utilizador sobrepusesse a lógica de inferência automática padrão para o tipo de modelo
    + Mudanças de widget: Instala-se automaticamente com `contrib`, sem mais `nbextension` instalação/ativação - explicação de suporte apenas com a importância global da funcionalidade (por exemplo Permutativo)
    + Mudanças no painel de instrumentos: - Enredos de caixas e enredos de violino para além de `beeswarm` enredo na página sumária - Rerenderização muito mais rápida do enredo `beeswarm` na mudança de slider 'Top-k' - mensagem útil explicando como top-k é computado - Mensagens personalizáveis úteis no lugar dos gráficos quando os dados não fornecidos
  + **núcleo azureml**
    + Método de Model.package() para criar imagens Docker e Dockerfiles que encapsulam modelos e suas dependências.
    + Serviços web locais atualizados para aceitar InferenceConfigs contendo objetos ambientais.
    + Modelo Fixo.register() produzindo modelos inválidos quando '.' (para o atual diretório) é passado como parâmetro model_path.
    + Adicione Run.submit_child, a funcionalidade espelha Experiment.submit enquanto especifica a execução como o pai da execução da criança submetida.
    + Opções de configuração de suporte do Model.register em Run.register_model.
    + Capacidade de executar empregos jar em cluster existente.
    + Agora a apoiar os parâmetros instance_pool_id e cluster_log_dbfs_path.
    + Suporte adicionado para a utilização de um objeto Ambiente ao implementar um Modelo para um Serviço Web. O objeto Ambiente pode agora ser fornecido como parte do objeto InferenceConfig.
    + Adicione mapeamento appinsifht para novas regiões - centralus - westus - centro-norte
    + Acrescentou documentação para todos os atributos em todas as classes datastore.
    + Adicionei blob_cache_timeout parâmetro para `Datastore.register_azure_blob_container`.
    + Adicione save_to_directory e métodos load_from_directory para azureml.core.environment.environment.
    + Acrescentou ao CLI os comandos "az ml de descarregamento ambiental" e "az ml de registo ambiental".
    + Método de ambiente adicionado.add_private_pip_wheel.
  + **modelo azureml-explicação**
    + Adicionou rastreio de conjunto de dados a Explicações utilizando o serviço Dataset (pré-visualização).
    + Diminuição do tamanho do lote padrão ao transmitir explicações globais de 10k para 100.
    + Adicione model_task bandeira aos explicadores para permitir que o utilizador sobrepor a lógica de inferência automática padrão para o tipo de modelo.
  + **azureml-mlflow**
    + Inseto fixo em mlflow.azureml.build_image onde os diretórios aninhados são ignorados.
  + **azureml-pipeline-steps**
    + Capacidade acrescida de executar empregos JAR no aglomerado de Databricks Azure existente.
    + Instance_pool_id de suporte adicionado e parâmetros cluster_log_dbfs_path para databricksStep passo.
    + Suporte adicional para parâmetros de gasoduto na etapa DatabricksStep.
  + **azureml-train-automl**
    + Acrescentou `docstrings` para os ficheiros relacionados com o Ensemble.
    + Docs atualizados para linguagem mais adequada para `max_cores_per_iteration` e `max_concurrent_iterations`
    + O registo de tarefas de previsão nas operações remotas melhorou, agora o utilizador é fornecido com uma mensagem de erro abrangente se a execução tiver falhado.
    + Removido get_data do oleoduto `automlstep` caderno.
    + Começou a apoiar `dataprep` em `automlstep`.

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning Data Prep SDK v1.1.10

+ **Novas funcionalidades**
  + Pode agora solicitar a execução de inspetores específicos (por exemplo, histograma, enredo de dispersão, etc.) em colunas específicas.
  + Acrescentou um argumento paralelo à `append_columns`. Se Verdadeiro, os dados serão carregados na memória, mas a execução será executada em paralelo; se False, a execução será transmitida, mas de um fio único.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK para Python v1.0.53

+ **Novas funcionalidades**
  + Machine Learning Automatizado agora suporta a formação de modelos ONNX no alvo da computação remota
  + O Azure Machine Learning oferece agora capacidade para retomar a formação a partir de um anterior ensaio, checkpoint ou ficheiros de modelos.
    + Aprenda a [usar os estimadores para retomar](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb) a formação de uma corrida anterior

+ **Correções e melhorias de bugs**
  + **automl-cliente-core-nativocliente**
    + Fixe o bug sobre tipos de colunas de loosing após a transformação (ligado sintetizador);
    + Deixe y_query um tipo de objeto que contenha nenhum(s) no início (#459519).
  + **azure-cli-ml**
    + O CLI comanda "model deploy" e "service update" agora aceitam parâmetros, ficheiros config ou uma combinação dos dois. Os parâmetros têm precedência sobre atributos em ficheiros.
    + A descrição do modelo pode agora ser atualizada após o registo
  + **azureml-automl-core**
    + Atualizar a dependência do NimbusML para a versão 1.2.0 (as últimas atuais).
    + Adicionar suporte para os estimadores e oleodutos NimbusML a utilizar dentro dos estimadores AutoML.
    + A fixação de um bug no procedimento de seleção do Ensemble, que estava a aumentar desnecessariamente o conjunto resultante, mesmo que as pontuações permanecessem constantes.
    + Permitir a reutilização de algumas características através de CV Splits para tarefas de previsão. Isto acelera o tempo de execução da configuração executado por aproximadamente um fator de n_cross_validations para características caras como lags e janelas rolantes.
    + Abordar um problema se o tempo estiver fora do intervalo de tempo suportado por pandas. Agora angariamos uma DataException se o tempo for inferior ao da PD. Carimbo de tempo.min ou maior que o pd. Carimbo de tempo.max
    + A previsão agora permite diferentes frequências em conjuntos de comboios e testes se puderem ser alinhadas. Por exemplo, "trimestralmente a partir de janeiro" e em "início trimestral a partir de outubro" pode ser alinhada.
    + Os "parâmetros" da propriedade foram adicionados ao TimeSeriesTransformer.
    + Remova as velhas aulas de exceção.
    + Nas tarefas de previsão, o parâmetro `target_lags` aceita agora um único valor inteiro ou uma lista de inteiros. Se o inteiro foi fornecido, apenas um atraso será criado. Se for fornecida uma lista, serão tomados os valores únicos dos lags. target_lags=[1, 2, 2, 4] criará lags de um, 2 e 4 períodos.
    + Fixe o bug sobre a perda de tipos de colunas após a transformação (ligado ao bug);
    + No `model.forecast(X, y_query)`, permita que y_query seja um tipo de objeto que contenha Nenhum(s) no início (#459519).
    + adicionar valores esperados para `automl` saída
  + **deriva de dados azureml-contrib**
    +  Melhorias no portátil exemplo, incluindo a mudança para conjuntos de dados em aberto em vez de conjuntos de dados abertos em azureml-contrib e melhorias de desempenho ao enriquecer dados
  + **modelo azureml-contrib-explicar**
    + Argumento de transformações fixas para explicador de LIMA para a importância da característica bruta no pacote de modelo azureml-contrib-explicação
    + Segmentações adicionadas a explicações de imagem em explicador de imagem para o pacote azureml-contrib-explica-modelo
    + Adicione suporte escasso para LimeExplainer
    + acrescentou `batch_size` para imitar explicador quando `include_local=False`, para transmitir explicações globais em lotes para melhorar o tempo de execução do DecisionTreeExplainableModel
  + **engenharia de recurso azureml-contrib**
    + Correção para chamar set_featurizer_timeseries_params(): alteração do tipo de valor ditado e verificação nula - Adicione o caderno para `timeseries` featurizer
    + Atualizar a dependência do NimbusML para a versão 1.2.0 (as últimas atuais).
  + **núcleo azureml**
    + Acrescentou a capacidade de anexar as lojas de dados DBFS no CLI AzureML
    + Fixou o bug com o upload de uma loja de dados onde é criada uma pasta vazia se `target_path` começado com `/`
    + Problema de `deepcopy` fixo saca do serviçoDeSPrincipalAuthentication.
    + Acrescentou ao CLI os comandos "az ml ambiente" e "az ml environment list".
    + Os ambientes agora apoiam a especção de uma base_dockerfile como alternativa a uma base_image já construída.
    + A definição de Configuração de Configuração de Execução não utilizada auto_prepare_environment foi marcada como depreciada.
    + A descrição do modelo pode agora ser atualizada após o registo
    + Bugfix: O modelo e a imagem eliminam agora fornece mais informações sobre a recuperação de objetos a montante que dependem deles se a eliminação falhar devido a uma dependência a montante.
    + Bug fixo que imprimiu a duração em branco para implementações que ocorrem ao criar um espaço de trabalho para alguns ambientes.
    + O espaço de trabalho melhorado cria exceções à falha. De tal forma que os utilizadores não vêem "Incapazes de criar espaço de trabalho. Incapaz de encontrar..." como a mensagem e, em vez disso, ver a verdadeira falha da criação.
    + Adicione suporte para autenticação simbólica em serviços web AKS.
    + Adicione `get_token()` método a `Webservice` objetos.
    + Suporte CLI adicionado para gerir conjuntos de dados de aprendizagem automática.
    + `Datastore.register_azure_blob_container` agora opcionalmente leva um valor `blob_cache_timeout` (em segundos) que configura os parâmetros de montagem da Blobfuse para permitir a expiração da cache para esta loja de dados. O padrão não é um intervalo, ou seja, quando uma bolha é lida, permanecerá na cache local até que o trabalho esteja terminado. A maioria dos postos de trabalho prefere este cenário, mas alguns empregos precisam de ler mais dados a partir de um conjunto de dados grande do que cabernos nos seus nós. Para estes trabalhos, a afinação deste parâmetro irá ajudá-los a ter sucesso. Tenha cuidado ao afinar este parâmetro: definir o valor demasiado baixo pode resultar num mau desempenho, uma vez que os dados utilizados numa época podem expirar antes de serem novamente utilizados. Isto significa que todas as leituras serão feitas a partir do armazenamento de blob (isto é, a rede) em vez da cache local, que impacta negativamente os tempos de treino.
    + A descrição do modelo pode agora ser devidamente atualizada após o registo
    + A eliminação do modelo e da imagem fornece agora mais informações sobre objetos a montante que dependem deles, o que faz com que o apagamento falhe
    + Melhorar a utilização de recursos de ensaios remotos utilizando o fluxo em azureml.mlflow.
  + **modelo azureml-explicação**
    + Argumento de transformações fixas para explicador de LIMA para a importância da característica bruta no pacote de modelo azureml-contrib-explicação
    + adicionar suporte escasso para LimeExplainer
    + invólucro explicador linear de forma adicionada, bem como outro nível para explicador tabular para explicar modelos lineares
    + para explicador de imitação na biblioteca modelo explicar, erro fixo ao include_local=Falso para entrada de dados escassos
    + adicionar valores esperados para `automl` saída
    + permutação fixa característica importância quando o argumento de transformações fornecido para obter importância de característica bruta
    + acrescentou `batch_size` para imitar explicador quando `include_local=False`, para transmitir explicações globais em lotes para melhorar o tempo de execução do DecisionTreeExplainableModel
    + para a biblioteca de explicabilidade do modelo, explicadores de caixa preta fixas onde a entrada de dados pandas é necessária para previsão
    + Fixou um inseto onde `explanation.expected_values` às vezes devolveva um carro alegórico em vez de uma lista com um carro alegórico.
  + **azureml-mlflow**
    + Melhorar o desempenho do mlflow.set_experiment(experiment_name)
    + Fixe o bug em utilização da Autenticação InteractiveLogin para tracking_uri de mlflow
    + Melhorar a utilização de recursos de ensaios remotos utilizando o fluxo em azureml.mlflow.
    + Melhorar a documentação do pacote de fluxo de mílum-azureml
    + Patch bug onde mlflow.log_artifacts ("my_dir") salvaria artefactos em "my_dir/<artifact-paths>" em vez de "<artifact-paths>"
  + **conjuntos de dados azureml-open**
    + Pin `pyarrow` de `opendatasets` para versões antigas (<0.14.0) devido à questão da memória recentemente introduzida lá.
    + Mova conjuntos de dados abertos em azureml-contrib para conjuntos de dados abertos em azureml.
    + Permitir que as aulas abertas de conjunto de dados sejam registadas no espaço de trabalho de Aprendizagem automática azure e alavancar as capacidades aml Dataset sem problemas.
    + Melhorar o noaaIsdWeather enriquecer significativamente o desempenho na versão não-SPARK.
  + **azureml-pipeline-steps**
    + A DBFS Datastore é agora suportada para Entradas e Saídas em DatabricksStep.
    + Documentação atualizada para o Passo do Lote Azure no que diz respeito a entradas/saídas.
    + Em AzureBatchStep, alterou *delete_batch_job_after_finish* valor padrão para *verdadeiro*.
  + **azureml-telemetria**
    +  Mova conjuntos de dados abertos em azureml-contrib para conjuntos de dados abertos em azureml.
    + Permitir que as aulas abertas de conjunto de dados sejam registadas no espaço de trabalho de Aprendizagem automática azure e alavancar as capacidades aml Dataset sem problemas.
    + Melhorar o noaaIsdWeather enriquecer significativamente o desempenho na versão não-SPARK.
  + **azureml-train-automl**
    + Documentação atualizada sobre get_output para refletir o tipo real de retorno e fornecer notas adicionais sobre a recuperação de propriedades-chave.
    + Atualizar a dependência do NimbusML para a versão 1.2.0 (as últimas atuais).
    + adicionar valores esperados para `automl` saída
  + **azureml-train-core**
    + As cordas são agora aceites como alvo de cálculo para a finação automatizada do hiperparâmetro
    + A definição de Configuração de Configuração de Execução não utilizada auto_prepare_environment foi marcada como depreciada.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning Data Prep SDK v1.1.9

+ **Novas funcionalidades**
  + Suporte adicionado para ler um ficheiro diretamente de um url http ou https.

+ **Correções e melhorias de bugs**
  + Mensagem de erro melhorada ao tentar ler um Conjunto de Dados Parquet a partir de uma fonte remota (que não é suportada atualmente).
  + Fixou um bug ao escrever para o formato de ficheiro Parquet na ADLS Gen 2 e atualizou o nome do recipiente ADLS Gen 2 no caminho.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Visual Interface
+ **Funcionalidades de pré-visualização**
  + Adicione ido módulo "Executar script R" na interface visual.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK para Python v1.0.48

+ **Novas funcionalidades**
  + **conjuntos de dados azureml-open**
    + Os conjuntos de **dados azureml-contrib-open** estão agora disponíveis como **conjuntos de dados abertos em azureml**. O pacote antigo ainda pode funcionar, mas recomendamos que utilize **conjuntos de dados abertos em azureml,** avançando para capacidades e melhorias mais ricas.
    + Este novo pacote permite-lhe registar conjuntos de dados abertos como Dataset no espaço de trabalho de Aprendizagem Automática Azure, e aproveitar quaisquer funcionalidades que o Dataset ofereça.
    + Também inclui capacidades existentes, tais como consumir conjuntos de dados abertos como quadros de dados Pandas/SPARK, e a localização junta-se para algum conjunto de dados como o tempo.

+ **Funcionalidades de pré-visualização**
    + A HyperDriveConfig pode agora aceitar o objeto do gasoduto como parâmetro para suportar a afinação do hiperparâmetro utilizando um gasoduto.

+ **Correções e melhorias de bugs**
  + **azureml-train-automl**
    + Fixou o bug sobre a perda de tipos de colunas após a transformação.
    + Fixou o inseto para permitir que y_query seja um tipo de objeto que contenha nenhum ou s no início.
    + Corrigiu a questão no procedimento de seleção do Ensemble, que estava a aumentar desnecessariamente o conjunto resultante, mesmo que as pontuações permanecessem constantes.
    + Corrigiu o problema com as definições de whitelist_models e blacklist_models no AutoMLStep.
    + Corrigiu a questão que impedia a utilização do pré-processamento quando a AutoML teria sido utilizada no contexto dos gasodutos Azure ML.
  + **conjuntos de dados azureml-open**
    + Conjuntos de dados azureml-contrib-open para conjuntos de dados abertos em azureml.
    + Permitiu que as aulas abertas de conjunto de dados fossem registadas no espaço de trabalho de Aprendizagem automática azure e alavancar as capacidades aml Dataset sem problemas.
    + NoaaIsdWeather melhorado enriquece significativamente o desempenho na versão não-SPARK.
  + **modelo azureml-explicação**
    + Documentação online atualizada para objetos de interpretação.
    + Acrescentou `batch_size` para imitar explicadores quando `include_local=False`, para transmitir explicações globais em lotes para melhorar o tempo de execução do DecisionTreeExplainableModel para a biblioteca de explicabilidade de modelos.
    + Corrigiu a questão em que `explanation.expected_values` às vezes devolveva um carro alegórico em vez de uma lista com um carro alegórico.
    + Acrescentou valores esperados para `automl` saída para explicador na biblioteca de modelos de explicação.
    + Permutação fixa característica de importância quando o argumento de transformações fornecido para obter importância bruta de características.
  + **núcleo azureml**
    + Acrescentou a capacidade de anexar as lojas de dados DBFS no CLI AzureML.
    + Corrigiu o problema com o upload da datastore onde é criada uma pasta vazia se `target_path` começado com `/`.
    + Comparação ativada de dois conjuntos de dados.
    + O modelo e a imagem apagam agora mais informações sobre a recuperação de objetos a montante que dependem deles se a eliminação falhar devido a uma dependência a montante.
    + Deprecendo a definição de Configuração de Execução não utilizada em auto_prepare_environment.
  + **azureml-mlflow**
    + Utilização melhorada dos recursos de ensaios remotos que utilizam o fluxo em azureml.mlflow.
    + Melhorou a documentação do pacote de fluxo de mílmica em azul.
    + Fixou a questão em que o mlflow.log_artifacts ("my_dir") salvaria artefactos em "my_dir/artefactos-caminhos" em vez de "artefactos-caminhos".
  + **azureml-pipeline-core**
    + O hash_paths para a para-quedista para todas as etapas do gasoduto é depreciado e será removido no futuro. Por predefinição, o conteúdo do source_directory é hashed (exceto ficheiros listados em .amlignore ou .gitignore)
    + Continuando a melhorar o Módulo e o MóduloStep para suportar módulos específicos do tipo computacional, para preparar a integração runConfiguration e outras alterações para desbloquear a utilização específica do módulo do tipo computacional em pipelines.
  + **azureml-pipeline-steps**
    + AzureBatchStep: Documentação melhorada no que diz respeito a entradas/saídas.
    + AzureBatchStep: Mudou delete_batch_job_after_finish valor padrão para verdadeiro.
  + **azureml-train-core**
    + As cordas são agora aceites como alvo de cálculo para a afinação automatizada do hiperparâmetro.
    + Deprecendo a definição de Configuração de Execução não utilizada em auto_prepare_environment.
    + Parâmetros pré-catados `conda_dependencies_file_path` e `pip_requirements_file_path` a favor de `conda_dependencies_file` e `pip_requirements_file` respectivamente.
  + **conjuntos de dados azureml-open**
    + Melhorar o noaaIsdWeather enriquecer significativamente o desempenho na versão não-SPARK.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure Machine Learning Data Prep SDK v1.1.8

+ **Novas funcionalidades**
 + Os objetos de fluxo de dados podem agora ser iterados, produzindo uma sequência de registos. Consulte a documentação para `Dataflow.to_record_iterator`.
  + Os objetos de fluxo de dados podem agora ser iterados, produzindo uma sequência de registos. Consulte a documentação para `Dataflow.to_record_iterator`.

+ **Correções e melhorias de bugs**
 + Aumentou a robustez do DataPrep SDK.
 + Melhor manuseamento dos pandas DataFrames com índices de colunas não-string.
 + Melhorou o desempenho dos `to_pandas_dataframe` em Conjuntos de Dados.
 + Fixou um bug onde a execução de Conjuntos de Dados falhou quando executado num ambiente multi-nó.
  + Aumentou a robustez do DataPrep SDK.
  + Melhor manuseamento dos pandas DataFrames com índices de colunas não-string.
  + Melhorou o desempenho dos `to_pandas_dataframe` em Conjuntos de Dados.
  + Fixou um bug onde a execução de Conjuntos de Dados falhou quando executado num ambiente multi-nó.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning Data Prep SDK v1.1.7

Revertemos uma mudança que melhorou o desempenho, uma vez que estava a causar problemas a alguns clientes que utilizavam os Azure Databricks. Se tiver sofrido um problema nos Bricks Azure, pode fazer upgrade para a versão 1.1.7 utilizando um dos métodos abaixo:
1. Execute este script para atualizar: `%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Recrie o cluster, que irá instalar a mais recente versão SDK de Preparação de Dados.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Azure Machine Learning SDK para Python v1.0.45

+ **Novas funcionalidades**
  + Adicione modelo de substituto de árvore de decisão para imitar explicador em pacote de modelo de explicação azureml
  + Capacidade de especificar uma versão CUDA a ser instalada em imagens de inferência. Apoio para CUDA 9.0, 9.1 e 10.0.
  + Informações sobre imagens da base de treino Azure ML estão agora disponíveis no [Azure ML Containers GitHub Repository](https://github.com/Azure/AzureML-Containers) e [DockerHub](https://hub.docker.com/_/microsoft-azureml)
  + Suporte CLI adicionado para o calendário do gasoduto. Executar "az ml pipeline -h" para saber mais
  + Adicionou um parâmetro de espaço de nome personalizado kubernetes à configuração de implementação de serviço web e CLI.
  + Parâmetro de hash_paths deprecatado para todos os passos do gasoduto
  + O Model.register suporta agora o registo de múltiplos ficheiros individuais como um único modelo com a utilização do parâmetro `child_paths`.

+ **Funcionalidades de pré-visualização**
    + Os explicadores de pontuação podem agora, opcionalmente, guardar informações de conda e pip para uma serialização e desserialização mais fiáveis.
    + Correção de erros para seletor de funcionalidades automáticas.
    + Mlflow.azureml.build_image para a nova api, bugs remendados expostos pela nova implementação.

+ **Correções e melhorias de bugs**
  + Removido `paramiko` dependência do núcleo azureml. Avisos de depreciação adicionados para métodos de fixação de objetivos de computação legado.
  + Melhorar o desempenho do run.create_children
  + Em mimic explainer com classificador binário, corrija a ordem das probabilidades quando a probabilidade do professor é usada para escalonar valores de forma.
  + Melhor manuseamento de erros e mensagem para aprendizagem automática de máquinas.
  + Corrigiu o problema do tempo de paragem da iteração para a aprendizagem automática de máquinas.
  + Melhorou o desempenho de transformação da série de tempo para aprendizagem automática de máquinas.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning Data Prep SDK v1.1.6

+ **Novas funcionalidades**
  + Funções sumárias adicionadas para valores superiores (`SummaryFunction.TOPVALUES`) e valores inferiores (`SummaryFunction.BOTTOMVALUES`).

+ **Correções e melhorias de bugs**
  + Melhorou significativamente o desempenho da `read_pandas_dataframe`.
  + Fixou um bug que faria com que `get_profile()` num Fluxo de Dados apontando para a falha dos ficheiros binários.
  + Expostos `set_diagnostics_collection()` permitir ativação/desativação programática da coleção de telemetria.
  + Mudou o comportamento de `get_profile()`. Os valores da NaN são agora ignorados por Min, Mean, Std e Sum, que se alinha com o comportamento dos Pandas.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Azure Machine Learning SDK para Python v1.0.43

+ **Novas funcionalidades**
  + O Azure Machine Learning oferece agora suporte de primeira classe para o popular quadro de aprendizagem automática e análise de dados Scikit-learn. Utilizando [`SKLearn` estimador,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)os utilizadores podem facilmente treinar e implementar modelos de aprendizagem de Scikit.
    + Aprenda a executar a finação do [hiperparâmetro com scikit-learn usando HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + Suporte adicional para a criação de ModuleStep em oleodutos juntamente com classes de Módulo solo e ModuleVersion para gerir unidades computacionais reutilizáveis.
  + Os serviços web da ACI suportam agora scoring_uri persistentes através de atualizações. O scoring_uri passará de IP para FQDN. A etiqueta de nome Dns para FQDN pode ser configurada definindo o dns_name_label no deploy_configuration.
  + Novas funcionalidades automatizadas de aprendizagem automática:
    + STL para previsão
    + O clustering KMeans está ativado para varrer a funcionalidade
  + As aprovações da AmlCompute Quota tornaram-se mais rápidas! Agora automatizamos o processo para aprovar os seus pedidos de quota dentro de um limiar. Para obter mais informações sobre como funcionam as quotas, aprenda [a gerir as quotas.](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas)

+ **Funcionalidades de pré-visualização**
    + Integração com [mLflow](https://mlflow.org) 1.0.0 rastreando através do pacote azureml-mlflow[(por exemplo cadernos).](https://aka.ms/azureml-mlflow-examples)
    + Submeta o caderno Jupyter como uma corrida. [Documentação de Referência da API](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Pré-visualização pública do [Detetor de Deriva](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) de Dados através do pacote azureml-contrib-datadrift[(por exemplo, cadernos).](https://aka.ms/azureml-datadrift-example) Data Drift é uma das principais razões para a precisão do modelo se degradar ao longo do tempo. Acontece quando os dados servidos para modelar na produção é diferente dos dados em que o modelo foi treinado. O detetor AML Data Drift ajuda o cliente a monitorizar a deriva dos dados e envia alerta sempre que a deriva é detetada.

+ **Quebrar alterações**

+ **Correções e melhorias de bugs**
  + Carga de Configuração de execução e suportes de poupança especificando um caminho completo de ficheiro com total compatde de volta para comportamento anterior.
  + Acréscimo de cache no ServiçoDeAutenticação, desligado por defeito.
  + Ativar o registo de vários enredos com o mesmo nome métrico.
  + Classe modelo agora devidamente importada de azureml.core (`from azureml.core import Model`).
  + Nas etapas do gasoduto, `hash_path` parâmetro está agora depreciado. O novo comportamento é para o hash completo source_directory, exceto ficheiros listados em .amlignore ou .gitignore.
  + Nos pacotes de gasodutos, várias `get_all` e métodos `get_all_*` foram depreciados a favor de `list` e `list_*`, respectivamente.
  + azureml.core.get_run já não exige que as classes sejam importadas antes de devolver o tipo de execução original.
  + Corrigiu um problema em que algumas chamadas para a WebService Update não desencadearam uma atualização.
  + O tempo de pontuação nos serviços web da AKS deve ser entre 5 ms e 300000ms. Max permitiu scoring_timeout_ms para pedidos de pontuação foi aumentado de 1 min para 5 min.
  + Os objetos localWebservice têm agora propriedades `scoring_uri` e `swagger_uri`.
  + A criação de diretórios de saídas movia o diretório de saídas e o diretório de saídas enviado para fora do processo de utilizador. Histórico de execução habilitado SDK para executar em todos os processos de utilizador. Isto deve resolver alguns problemas de sincronização experimentados por treinos distribuídos.
  + O nome do registo emazureml escrito a partir do nome do processo do utilizador passará a incluir o nome do processo (apenas para treino distribuído) e PID.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning Data Prep SDK v1.1.5

+ **Correções e melhorias de bugs**
  + Para valores de data sinuosa interpretados que têm um formato de 2 dígitos, o intervalo de anos válidos foi atualizado para corresponder ao Lançamento do Windows May. A gama foi alterada de 1930-2029 para 1950-2049.
  + Ao ler num ficheiro e configurar `handleQuotedLineBreaks=True`, `\r` será tratada como uma nova linha.
  + Fixou um inseto que fez com que `read_pandas_dataframe` falhasse em alguns casos.
  + Melhor desempenho da `get_profile`.
  + Mensagens de erro melhoradas.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure Machine Learning Data Prep SDK v1.1.4

+ **Novas funcionalidades**
  + Pode agora utilizar as seguintes funções linguísticas de expressão para extrair e analisar valores de data-data em novas colunas.
    + `RegEx.extract_record()` extrai elementos de data numa nova coluna.
    + `create_datetime()` cria objetos de data a partir de elementos de data separados.
  + Ao ligar `get_profile()`, pode agora ver que as colunas quânticas estão rotuladas como (est.) para indicar claramente que os valores são aproximações.
  + Agora pode usar ** globbing ao ler a partir do Armazenamento De Blob Azure.
    + por exemplo, `dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Correções de bugs**
  + Fixou um bug relacionado com a leitura de um ficheiro Parquet a partir de uma fonte remota (Azure Blob).

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK para Python v1.0.39
+ **Alterações**
  + A configuração de execução auto_prepare_environment opção está a ser depreciada, com o preparação automática tornando-se o padrão.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure Machine Learning Data Prep SDK v1.1.3

+ **Novas funcionalidades**
  + Suporte adicionado para ler a partir de uma base de dados PostgresSQL, quer ligando para read_postgresql ou usando uma Datastore.
    + Consulte exemplos em guias de como fazer:
      + [Caderno de ingestão de dados](https://aka.ms/aml-data-prep-ingestion-nb)
      + [Caderno de datastore](https://aka.ms/aml-data-prep-datastore-nb)

+ **Correções e melhorias de bugs**
  + Problemas fixos com conversão do tipo coluna:
  + Agora converte corretamente uma coluna booleana ou numérica para uma coluna booleana.
  + Agora não falha ao tentar definir uma coluna de data para ser o tipo de data.
  + Tipos de JoinType melhorados e documentação de referência que acompanha. Ao juntar dois fluxos de dados, pode agora especificar um destes tipos de adesão:
    + NENHUM, MATCH, INTERIOR, UNMATCHLEFT, LEFTANTI, LEFTOUTER, UNMATCHRIGHT, RIGHTANTI, RIGHTOUTER, FULLANTI, FULLANTI, FULL.
  + Inferência do tipo de dados melhorada para reconhecer mais formatos de data.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Portal do Azure

No portal Azure, já pode:
+ Criar e executar experiências automatizadas de ML
+ Crie um VM portátil para experimentar os cadernos Jupyter ou os seus.
+ Nova secção de autoria (Pré-visualização) no espaço de trabalho azure machine learning, que inclui Machine Learning Automatizado, Interface Visual e VMs de caderno hospedados
    + Criar automaticamente um modelo utilizando machine learning automatizado
    + Use um arrastar e deixe cair interface visual para executar experiências
    + Criar um VM portátil para explorar dados, criar modelos e implementar serviços.
+ Gráfico ao vivo e atualização métrica em relatórios de execução e páginas de detalhes de execução
+ Visualizador de ficheiros atualizado para registos, saídas e instantâneos nas páginas de detalhes do Run.
+ Nova e melhorada experiência de criação de relatório sintetiza no separador Experimentos.
+ Capacidade adicional de descarregar o ficheiro config.json a partir da página de visão geral do espaço de trabalho Azure Machine Learning.
+ Apoiar a criação do espaço de trabalho Azure Machine Learning a partir do espaço de trabalho Azure Databricks.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Azure Machine Learning SDK para Python v1.0.33
+ **Novas funcionalidades**
  + O método _Workspace.create_ aceita agora configurações de cluster padrão para clusters CPU e GPU.
  + Se a criação do Espaço de Trabalho falhar, os recursos dependem da sua limpeza.
  + O Registo de Contentores DePrepreto Azure SKU foi mudado para o básico.
  + O Registo de Contentores Azure é criado preguiçosamente, quando necessário para a criação de corridas ou imagens.
  + Apoio a Ambientes para treinos.

### <a name="notebook-virtual-machine"></a>Máquina Virtual do Caderno 

Utilize um VM portátil como um ambiente de hospedagem seguro e pronto para a empresa para os cadernos Jupyter em que pode programar experiências de aprendizagem automática, implementar modelos como pontos finais web e executar todas as outras operações suportadas pelo Azure Machine Learning SDK usando python. Fornece várias capacidades:
+ [Rode rapidamente um vM de bloco supônto ](tutorial-1st-experiment-sdk-setup.md) que tenha a versão mais recente do Azure Machine Learning SDK e pacotes relacionados.
+ O acesso é assegurado através de tecnologias comprovadas, como HTTPS, autenticação e autorização do Diretório Ativo Azure.
+ Armazenamento fiável em nuvem de cadernos e código na sua conta de armazenamento de blob space de aprendizagem automática Azure. Pode eliminar com segurança o seu VM de caderno sem perder o seu trabalho.
+ Cadernos de amostrapré-instalados para explorar e experimentar com funcionalidades de Aprendizagem automática de Azure.
+ Capacidades completas de personalização de VMs Azure, qualquer tipo VM, quaisquer pacotes, quaisquer controladores. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK para Python v1.0.33 lançado.

+ Os modelos acelerados de hardware Azure ML em [FPGAs](how-to-deploy-fpga-web-service.md) estão geralmente disponíveis.
  + Agora pode utilizar o pacote de [modelos de gama azureml-accel](how-to-deploy-fpga-web-service.md) para:
    + Treine os pesos de uma rede neural profunda suportada (ResNet 50, ResNet 152, DenseNet-121, VGG-16 e SSD-VGG)
    + Utilize a aprendizagem de transferência com o DNN suportado
    + Registe o modelo com o Serviço de Gestão de Modelos e containerize o modelo
    + Desloque o modelo para um Azure VM com uma FPGA num cluster azure Kubernetes Service (AKS)
  + Desdobrar o recipiente para um dispositivo de servidor [De Caixa de Dados Azure Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)
  + Marque os seus dados com o ponto final do gRPC com esta [amostra](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Aprendizagem automática de máquinas

+ Recurso abrangente para permitir adicionar dinamicamente :::no-loc text="featurizers"::: para otimização de desempenho. Nova :::no-loc text="featurizers":::: incorporações de trabalho, peso das provas, codificação de alvos, codificação do alvo de texto, distância de cluster
+ Cv inteligente para lidar com divisões de comboio/válidodentro de ML automatizado
+ Poucas alterações na otimização da memória e melhoria do desempenho do tempo de execução
+ Melhoria do desempenho na explicação do modelo
+ Conversão do modelo ONNX para execução local
+ Suporte de subamostragem adicionado
+ Paragem Inteligente quando não há critérios de saída definidos
+ Conjuntos empilhados

+ Previsão de séries temporais
  + Nova função de previsão de previsão
  + Agora pode usar validação cruzada de origem rolante em dados da série time
  + Nova funcionalidade adicionada à configuração de atrasos na série de tempo
  + Nova funcionalidade adicionada para suportar funcionalidades agregados de janelas rolantes
  + Nova deteção e característica de Férias quando o código do país é definido em definições de experiência

+ Azure Databricks
  + Capacidade de previsão e explicabilidade de séries de tempo habilitadas
  + Agora pode cancelar e retomar (continuar) experiências ml automatizadas
  + Suporte adicional para processamento multicore

### <a name="mlops"></a>MLOps
+ **Implantação local e depuração para contentores de pontuação**<br/> Agora pode implantar um modelo ML localmente e iterar rapidamente no seu ficheiro de pontuação e dependências para garantir que se comportam como esperado.

+ **Introduced InferenceConfig & Model.deploy()**<br/> A implementação do modelo suporta agora especificar uma pasta de origem com um script de entrada, o mesmo que um RunConfig.  Além disso, a implantação do modelo foi simplificada para um único comando.

+ **Rastreio de referência git**<br/> Os clientes têm vindo a solicitar capacidades básicas de integração git há algum tempo, uma vez que ajuda a manter um rasto de auditoria de ponta a ponta. Implementámos o rastreio entre as principais entidades do Azure ML para metadados relacionados com git (repo, commit, clean state). Estas informações serão recolhidas automaticamente pelo SDK e pelo CLI.

+ **Serviço de perfis e validação de modelos**<br/> Os clientes queixam-se frequentemente da dificuldade em dimensionar adequadamente a computação associada ao seu serviço de inferência. Com o nosso serviço de perfis de modelo, o cliente pode fornecer entradas de amostra e vamos perfilar-se em 16 configurações diferentes de CPU/memória para determinar o tamanho ideal para a implementação.

+ **Traga a sua própria imagem base para inferência**<br/> Outra queixa comum foi a dificuldade em passar da experimentação para a inferência re partilhando dependências. Com a nossa nova capacidade de partilha de imagens base, pode agora reutilizar as imagens base de experimentação, dependências e tudo, para inferência. Isto deve acelerar as implantações e reduzir a distância do interior para o laço exterior.

+ **Experiência melhorada de geração de esquemas swagger**<br/> O nosso método anterior de geração swagger foi propenso a erros e impossível de automatizar. Temos uma nova forma em linha de gerar esquemos de qualquer função Python através de decoradores. Abrimos este código e o nosso protocolo de geração de esquemas não está acoplado à plataforma Azure ML.

+ **O AZURE ML CLI está geralmente disponível (GA)**<br/> Os modelos podem agora ser implantados com um único comando CLI. Temos feedback comum do cliente que ninguém implementa um modelo ML de um caderno Jupyter. A documentação de [**referência do CLI**](https://aka.ms/azmlcli) foi atualizada.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK para Python v1.0.30 lançado.

O [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) foi introduzido para adicionar uma nova versão de um oleoduto publicado, mantendo o mesmo ponto final.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning Data Prep SDK v1.1.2

Nota: A Preparação de Dados Python SDK deixará de instalar pacotes `numpy` e `pandas`. Consulte as instruções de [instalação atualizadas](https://github.com/Microsoft/AMLDataPrepDocs).

+ **Novas funcionalidades**
  + Agora pode usar a transformação pivot.
    + Como guiar: [Caderno pivot](https://aka.ms/aml-data-prep-pivot-nb)
  + Agora pode usar expressões regulares em funções nativas.
    + Exemplos:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + Agora pode utilizar `to_upper` e `to_lower` funções na linguagem de expressão.
  + Pode agora ver o número de valores únicos de cada coluna num perfil de dados.
  + Para alguns dos passos de leitor comumente usados, você pode agora passar no argumento `infer_column_types`. Se for definido para `True`, a Data Prep tentará detetar e converter automaticamente os tipos de colunas.
    + `inference_arguments` está agora deprecida.
  + Agora pode chamá`Dataflow.shape`.

+ **Correções e melhorias de bugs**
  + `keep_columns` aceita agora um argumento facultativo adicional `validate_column_exists`, que verifica se o resultado de `keep_columns` conterá colunas.
  + Todos os passos do leitor (que lêem de um ficheiro) aceitam agora um argumento opcional adicional `verify_exists`.
  + Melhor desempenho da leitura a partir do dataframe pandas e obtenção de perfis de dados.
  + Fixou um inseto onde cortar um único passo de um Fluxo de Dados falhou com um único índice.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Portal do Azure
  + Agora pode reenviar uma execução de Script existente num aglomerado de computação remota existente.
  + Agora pode executar um pipeline publicado com novos parâmetros no separador Pipelines.
  + Os detalhes da execução suportam agora um novo espectador de ficheiros Snapshot. Pode ver uma imagem do diretório quando submeteu uma execução específica. Também pode descarregar o caderno que foi submetido para iniciar a execução.
  + Agora pode cancelar as corridas dos pais do portal Azure.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK para Python v1.0.23

+ **Novas funcionalidades**
  + O Azure Machine Learning SDK suporta agora python 3.7.
  + Os Estimadores DNN de Aprendizagem automática Azure fornecem agora suporte multi-versão incorporado. Por exemplo, `TensorFlow`estimador  aceita agora um parâmetro `framework_version`, e os utilizadores podem especificar a versão '1.10' ou '1.12'. Para obter uma lista das versões suportadas pela sua versão SDK atual, ligue `get_supported_versions()` na classe-quadro desejada (por exemplo, `TensorFlow.get_supported_versions()`).
  Para obter uma lista das versões suportadas pela mais recente versão do SDK, consulte a [documentação do Estimativador DNN](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning Data Prep SDK v1.1.1

+ **Novas funcionalidades**
  + Pode ler várias fontes datastore/DataPath/DataReference utilizando read_* transforma.
  + Pode realizar as seguintes operações em colunas para criar uma nova coluna: divisão, piso, modulo, potência, comprimento.
  + A Data Prep faz agora parte da suite de diagnóstico Azure ML e registará informações de diagnóstico por defeito.
    + Para desativar isto, desloque esta variável ambiental para verdade: DISABLE_DPREP_LOGGER

+ **Correções e melhorias de bugs**
  + Melhor documentação de código para classes e funções comumente usadas.
  + Fixou um bug em auto_read_file que não leu ficheiros Excel.
  + Opção adicional para substituir a pasta em read_pandas_dataframe.
  + Melhor desempenho da instalação de dependência dodotnetcore2, e suporte adicional para Fedora 27/28 e Ubuntu 1804.
  + Melhorou o desempenho da leitura de Azure Blobs.
  + A deteção do tipo de coluna suporta agora colunas de tipo Long.
  + Fixou um bug onde alguns valores de data estavam sendo exibidos como carimbos temporais em vez de objetos de data Python.
  + Fixou um bug onde alguns tipos de contagens estavam sendo exibidos como duplos em vez de inteiros.


## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK para Python v1.0.21

+ **Novas funcionalidades**
  + O método *azureml.core.Run.create_children* permite a criação de baixa latência de várias crianças com uma única chamada.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning Data Prep SDK v1.1.0

+ **Quebrar alterações**
  + O conceito do Pacote de Preparação de Dados foi depreciado e já não é suportado. Em vez de persistir vários fluxos de dados num pacote, pode persistir os Fluxos de Dados individualmente.
    + Como guiar: Caderno de [fluxos](https://aka.ms/aml-data-prep-open-save-dataflows-nb) de dados de abertura e poupança

+ **Novas funcionalidades**
  + A Preparação de Dados pode agora reconhecer colunas que correspondam a um tipo semântico específico, e divididas em conformidade. Os STypes atualmente suportados incluem: endereço de e-mail, coordenadas geográficas (latitude e longitude), endereços IPv4 e IPv6, número de telefone dos EUA e código postal dos EUA.
    + Como guiar: [Caderno semânticos](https://aka.ms/aml-data-prep-semantic-types-nb)
  + A Preparação de Dados suporta agora as seguintes operações para gerar uma coluna resultante a partir de duas colunas numéricas: subtrair, multiplicar, dividir e modulo.
  + Pode ligar para `verify_has_data()` num Fluxo de Dados para verificar se o Fluxo de Dados produziria registos se fosse executado.

+ **Correções e melhorias de bugs**
  + Agora pode especificar o número de caixotes a utilizar num histograma para perfis de colunanuméricas.
  + A transformação `read_pandas_dataframe` agora requer que o DataFrame tenha nomes de colunas dactilografadas por cordas ou bytes.
  + Fixou um inseto na `fill_nulls` transformar, onde os valores não foram corretamente preenchidos se a coluna estivesse em falta.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK para Python v1.0.18

 + **Alterações**
   + O pacote de tensorboard azureml substitui o azureml-contrib-tensorboard.
   + Com esta versão, pode configurar uma conta de utilizador no seu cluster de cálculo gerido (amlcompute), enquanto a cria. Isto pode ser feito passando estas propriedades na configuração de provisionamento. Pode encontrar mais detalhes na documentação de referência do [SDK.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning Data Prep SDK v1.0.17

+ **Novas funcionalidades**
  + Agora suporta adicionar duas colunas numéricas para gerar uma coluna resultante usando a linguagem de expressão.

+ **Correções e melhorias de bugs**
  + Melhorou a documentação e a verificação de parâmetros para random_split.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning Data Prep SDK v1.0.16

+ **Correção de insetos**
  + Fixou um problema de autenticação do Diretor de Serviço que foi causado por uma alteração da API.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK para Python v1.0.17

+ **Novas funcionalidades**

  + A Azure Machine Learning oferece agora suporte de primeira classe para a popular cadeia de quadros DNN. A utilização [de`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) utilizadores de classe pode facilmente treinar e implementar modelos Chainer.
    + Saiba como [executar treino distribuído com chainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Aprenda a executar a finação do [hiperparâmetro com o Chainer usando o HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Os pipelines de aprendizagem automática Azure adicionaram capacidade de desencadear uma execução de Pipeline com base em modificações de datastore. O caderno de [horários](https://aka.ms/pl-schedule) do pipeline é atualizado para mostrar esta funcionalidade.

+ **Correções e melhorias de bugs**
  + Adicionámos suporte nos oleodutos Azure Machine Learning para definir a propriedade source_directory_data_store para uma loja de dados desejada (como um armazenamento de blob) em [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) que são fornecidas ao [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Por padrão, passos usam a loja De Ficheiros Azure como a loja de dados de suporte, que pode encontrar problemas de estrangulamento quando um grande número de passos são executados simultaneamente.

### <a name="azure-portal"></a>Portal do Azure

+ **Novas funcionalidades**
  + Nova experiência de editor de drag e drop table para relatórios. Os utilizadores podem arrastar uma coluna do poço para a área da mesa onde será apresentada uma pré-visualização da tabela. As colunas podem ser reorganizadas.
  + Novo visualizador de ficheiros Logs
  + Links para experiências executa, computação, modelos, imagens e implementações do separador de atividades

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning Data Prep SDK v1.0.15

+ **Novas funcionalidades**
  + A Data Prep suporta agora a escrita de fluxos de ficheiros a partir de um fluxo de dados. Também fornece a capacidade de manipular os nomes de fluxo de ficheiros para criar novos nomes de ficheiros.
    + Como guiar: [Trabalhar com caderno](https://aka.ms/aml-data-prep-file-stream-nb) de streams de ficheiros

+ **Correções e melhorias de bugs**
  + Melhor desempenho do t-Digest em grandes conjuntos de dados.
  + A Data Prep suporta agora dados de leitura de um DataPath.
  + Uma codificação quente agora funciona em colunas booleanas e numéricas.
  + Outras correções de insetos diversos.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Azure Machine Learning SDK para Python v1.0.15

+ **Novas funcionalidades**
  + Os gasodutos de aprendizagem automática Azure adicionaram AzureBatchStep[(caderno),](https://aka.ms/pl-azbatch)HyperDriveStep (caderno) e funcionalidade de agendamento baseada no tempo[(caderno).](https://aka.ms/pl-schedule)
  +  DataTranferStep atualizado para trabalhar com o Azure SQL Server e a base de dados Azure para PostgreSQL[(portátil).](https://aka.ms/pl-data-trans)

+ **Alterações**
  + `PublishedPipeline.get_published_pipeline` depreciada a favor de `PublishedPipeline.get`.
  + `Schedule.get_schedule` depreciada a favor de `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning Data Prep SDK v1.0.12

+ **Novas funcionalidades**
  + A Data Prep suporta agora a leitura de uma base de dados Azure SQL utilizando datastore.

+ **Alterações**
  + Melhorou o desempenho da memória de certas operações em grandes dados.
  + `read_pandas_dataframe()` agora requer `temp_folder` a especificar.
  + A propriedade `name` em `ColumnProfile` foi depreciada - use `column_name` em vez disso.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK para Python v1.0.10

+ **Alterações:**
  + O Azure ML SDK já não tem como dependência os pacotes azure-cli. Especificamente, as dependências do perfil azure-cli-core e azure-cli foram removidas do núcleo azureml. Estas são as alterações de impacto do utilizador:
    + Se estiver a realizar o "az login" e depois utilizar o azureml-sdk, o SDK fará o registo de código de navegador ou dispositivo mais uma vez. Não usará nenhuma credencial de estado criado por "az login".
    + Para autenticação Azure CLI, como a utilização de "az login", utilize a classe _azureml.core.authentication.AzureCliAuthentication._ Para a autenticação Azure CLI, _instale o azure-cli_ no ambiente Python onde instalou o azureml-sdk.
    + Se estiver a fazer "az login" utilizando um diretor de serviço para automação, recomendamos a utilização de _azureml.core.authentication.ServicePrincipalAuthentication_ class, uma vez que o azureml-sdk não utilizará credenciais de estado criado sacado pelo AZURI.

+ **Correções do bug**: Esta versão contém principalmente pequenas correções de bugs

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning Data Prep SDK v1.0.8

+ **Correções de bugs**
  + Melhorou o desempenho de obter perfis de dados.
  + Bugs menores fixos relacionados com relatórios de erros.

### <a name="azure-portal-new-features"></a>Portal do Azure: novos recursos
+ Nova experiência de drag e drop charting para relatórios. Os utilizadores podem arrastar uma coluna ou atributo do poço para a área do gráfico onde o sistema selecionará automaticamente um tipo de gráfico apropriado para o utilizador com base no tipo de dados. Os utilizadores podem alterar o tipo de gráfico para outros tipos aplicáveis ou adicionar atributos adicionais.

    Tipos de gráficos suportados:
    - Gráfico de linha
    - Histograma
    - Gráfico de bar empilhado
    - Lote de caixa
    - Lote de dispersão
    - Enredo de bolha
+ O portal gera agora dinamicamente relatórios para experiências. Quando um utilizador submete uma corrida a uma experiência, um relatório será automaticamente gerado com métricas e gráficos registados para permitir a comparação entre diferentes execuções.

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning SDK para Python v1.0.8

+ **Correções do bug**: Esta versão contém principalmente pequenas correções de bugs

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning Data Prep SDK v1.0.7

+ **Novas funcionalidades**
  + Melhorias na loja de dados (documentadas na [Datastore como guia)](https://aka.ms/aml-data-prep-datastore-nb)
    + Capacidade adicional de ler e escrever para Azure File Share e ADLS Datastores em escala-up.
    + Ao utilizar datastores, o Data Prep suporta agora a utilização da autenticação principal do serviço em vez da autenticação interativa.
    + Suporte adicionado para wasb e wasbs urls.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning Data Prep SDK v1.0.6

+ **Correções de bugs**
  + Bug fixo com leitura de recipientes públicos legíveis de Blob Azure em Spark

## <a name="2018-12-20"></a>2018-12-20

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK para Python v1.0.6
+ **Correções do bug**: Esta versão contém principalmente pequenas correções de bugs

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning Data Prep SDK v1.0.4

+ **Novas funcionalidades**
  + `to_bool` função agora permite converter valores desajustados para valores de erro. Este é o novo comportamento de desfasamento padrão para `to_bool` e `set_column_types`, enquanto o comportamento padrão anterior foi converter valores desajustados para Falso.
  + Ao chamar `to_pandas_dataframe`, há uma nova opção para interpretar valores nulos/em falta em colunas numéricas como NaN.
  + Capacidade adicional de verificar o tipo de retorno de algumas expressões para garantir a consistência do tipo e falhar cedo.
  + Pode agora chamar `parse_json` para analisar valores numa coluna como objetos JSON e expandi-los em várias colunas.

+ **Correções de bugs**
  + Fixou um inseto que se despenhou `set_column_types` em Python 3.5.2.
  + Fixou um bug que se despenhou ao ligar-se à Datastore utilizando uma imagem AML.

+ **Atualizações**
  * [Exemplo de cadernos](https://aka.ms/aml-data-prep-notebooks) para começar tutoriais, estudos de caso e guias de como fazer.

## <a name="2018-12-04-general-availability"></a>2018-12-04: disponibilidade geral

O Azure Machine Learning está agora geralmente disponível.

### <a name="azure-machine-learning-compute"></a>Computação do Azure Machine Learning
Com este lançamento, estamos a nunciando uma nova experiência de computação gerida através da Computação de [Machine Learning Azure.](how-to-set-up-training-targets.md#amlcompute) Este alvo computacional substitui o cálculo da Azure Batch AI para o Azure Machine Learning.

Este alvo computacional:
+ É utilizado para treino de modelos e inferência/pontuação de lote
+ É um cálculo de um só a multi-nó
+ A gestão do cluster e o agendamento de trabalho para o utilizador
+ Escalas automáticas por padrão
+ Apoio tanto aos recursos da CPU como da GPU
+ Permite a utilização de VMs de baixa prioridade para custos reduzidos

Computação do Azure Machine Learning podem ser criado em Python, através do portal do Azure ou a CLI. Tem de ser criado na região da sua área de trabalho e não pode ser ligado a outra área de trabalho. Este alvo computacional usa um recipiente Docker para a sua execução, e embala as suas dependências para replicar o mesmo ambiente em todos os seus nós.

> [!Warning]
> Recomendamos que crie uma nova área de trabalho para utilizar a computação do Azure Machine Learning. Há uma chance remota que os utilizadores a tentar criar a computação do Azure Machine Learning a partir de uma área de trabalho existente poderão ver um erro. Computação existente na sua área de trabalho deve continuar a trabalhar afetada.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning SDK for Python v1.0.2
+ **Quebrar alterações**
  + Com esta versão, estamos a remover suporte para criar uma VM do Azure Machine Learning. Ainda pode anexar uma VM de nuvem existente ou remoto no servidor local.
  + Estamos também a remover o suporte para BatchAI, todos os quais devem ser suportados por meio de computação do Azure Machine Learning agora.

+ **Novo**
  + Para pipelines de aprendizagem:
    + [EstimativaSStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Atualizado**
  + Para pipelines de aprendizagem:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) agora aceita runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) agora copia de e para uma fonte de dados SQL
    + Agendar funcionalidades no SDK para criar e atualizar agendas para a execução de pipelines publicados

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>SDK v0.5.2 de preparação de dados do Azure Machine Learning
+ **Quebrar alterações**
  * `SummaryFunction.N` foi renomeado para `SummaryFunction.Count`.

+ **Correções de bugs**
  * Utilize o mais recente AmL Run Token ao ler e escrever para datastores em execuções remotas. Anteriormente, se o Token AML de executar é atualizado no Python, o tempo de execução de preparação de dados não será atualizado com o Token AML de executar atualizado.
  * Mensagens de erro mais claras adicionais
  * to_spark_dataframe() não se despenhará mais quando Spark usa `Kryo` serialização
  * Inspetor de contagem de valores agora pode mostrar mais de 1000 valores exclusivos
  * Divisão aleatória já não falha se o fluxo de dados original não tiver um nome

+ **Mais informações**
  * [SDK de Preparação de Dados do Azure Machine Learning](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Documentos e blocos de notas
+ Pipelines de ML
  + Novos e atualizados cadernos para começar com oleodutos, scoping de lotes e exemplos de transferência de estilo: https://aka.ms/aml-pipeline-notebooks
  + Aprenda a [criar o seu primeiro oleoduto](how-to-create-your-first-pipeline.md)
  + Saiba como executar previsões de [lotes usando oleodutos](how-to-use-parallel-run-step.md)
+ Meta de computação de aprendizagem automática azure
  + [Os cadernos de amostras](https://aka.ms/aml-notebooks) são agora atualizados para utilizar o novo cálculo gerido.
  + [Conheça este cálculo](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Portal do Azure: novos recursos
+ Crie e gerencie os tipos de Computação de [Machine Learning Azure](how-to-set-up-training-targets.md#amlcompute) no portal.
+ Monitorize a utilização das quotas e [solicite quota](how-to-manage-quotas.md) para a Computação de Aprendizagem automática Azure.
+ Veja o estado do cluster da Computação de Aprendizagem automática Azure em tempo real.
+ Foi adicionado suporte de rede virtual para a criação de computação do Azure Machine Learning e o Azure Kubernetes Service.
+ Recorra os seus oleodutos publicados com parâmetros existentes.
+ Novos [gráficos automatizados](how-to-understand-automated-ml.md) de machine learning para modelos de classificação (lift, gains, calibração, gráfico de importância de características com explicabilidade do modelo) e modelos de regressão (residuais e gráfico de importância de características com explicabilidade do modelo).
+ Pipelines podem ser visualizados no portal do Azure




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning SDK for Python v0.1.80

+ **Quebrar alterações**
  * *azureml.train.widgets* namespace mudou-se para *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* deprecia as seguintes classes - *azureml.core.compute.BatchAICompute* e *azureml.core.compute.DSVMCompute*. A segunda classe será removida em versões subsequentes. A classe AmlCompute tem uma definição mais fácil agora, simplesmente precisa de um vm_size e o max_nodes e irá dimensionar automaticamente o cluster de 0 para o max_nodes quando é submetida uma tarefa. Os [nossos cadernos](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) de amostras foram atualizados com esta informação e devem dar-lhe exemplos de utilização. Esperamos que, como essa simplificação e muitos dos recursos mais interessantes para entrar numa versão posterior!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>SDK v0.5.1 de preparação de dados do Azure Machine Learning

Saiba mais sobre o Data Prep SDK lendo [os docs](https://aka.ms/data-prep-sdk)de referência .
+ **Novas Funcionalidades**
   * Criar uma nova CLI de DataPrep para executar pacotes DataPrep e visualizar o perfil de dados para um conjunto de dados ou o fluxo de dados
   * API de SetColumnType reestruturada para melhorar a usabilidade
   * Smart_read_file nome mudado para auto_read_file
   * Agora inclui skew e kurtosis no perfil de dados
   * Pode exemplo com amostragem stratified
   * Pode ler a partir de arquivos zip que contêm ficheiros CSV
   * Pode dividir conjuntos de dados em linha com Random Split (por exemplo, em conjuntos de treinadores de teste)
   * Pode obter todos os tipos de dados de colunas a partir de um fluxo de dados ou um perfil de dados, chamando `.dtypes`
   * Pode obter a contagem de linhas a partir de um fluxo de dados ou de um perfil de dados, chamando `.row_count`

+ **Correções de bugs**
   * Fixo de tempo de conversão duplo
   * Foi corrigido---vyhodnocení depois de adicionar qualquer coluna
   * Foi corrigido um problema com FuzzyGrouping, onde ele não detectaria grupos em alguns casos
   * Função de tipo fixo para respeitar a ordem de classificação de várias colunas
   * Fixas e/ou expressões semelhantes à forma como `pandas` as lida
   * Foi corrigido ler a partir do caminho de dbfs
   * Feitas as mensagens de erro mais compreensível
   * Agora já não falha ao ler no alvo de computação remota usando um símbolo AML
   * Agora já não ocorre uma falha do DSVM do Linux
   * Agora já não é interrompida ao valores de cadeia de caracteres não são em predicados de cadeia de caracteres
   * Agora manipula erros de asserção quando o fluxo de dados se falhar corretamente
   * Suporta agora a localizações de armazenamento dbutils montado no Azure Databricks

## <a name="2018-11-05"></a>11-05 de 2018

### <a name="azure-portal"></a>Portal do Azure
O portal Azure para O Machine Learning Azure tem as seguintes atualizações:
  * Um novo separador **Pipelines** para oleodutos publicados.
  * Foi adicionado suporte para anexar um cluster do HDInsight existente como um destino de computação.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning SDK for Python v0.1.74

+ **Quebrar alterações**
  * *Workspace.compute_targets, datastores, experiências, imagens, modelos e *webservices* são propriedades em vez de métodos. Por exemplo, substitua *workspace.compute_targets()* por *Workspace.compute_targets*.
  * *Run.get_context* deprecia *Run.get_submitted_run*. Este segundo método será removido em versões subsequentes.
  * A classe *PipelineData* espera agora um objeto de datastore como parâmetro e não datastore_name. Da mesma forma, *pipeline* aceita default_datastore em vez de default_datastore_name.

+ **Novas funcionalidades**
  * O caderno de [amostras](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) Azure Machine Learning Pipelines utiliza agora passos DEMPI.
  * O widget RunDetails para blocos de notas do Jupyter é atualizado para mostrar uma visualização do pipeline.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>SDK v0.4.0 de preparação de dados do Azure Machine Learning

+ **Novas funcionalidades**
  * Contagem do tipo adicionado ao perfil de dados
  * Contagem de valores e histograma já está disponível
  * Mais percentis no perfil de dados
  * O valor mediano está disponível no Summarize
  * Python 3.7 é agora suportado
  * Quando guarda um fluxo de dados que contém os arquivos de dados a um pacote de DataPrep, as informações do arquivo de dados serão mantidas como parte do pacote DataPrep
  * Escrever no arquivo de dados é agora suportado

+ **Bug fixo**
  * Transbordos inteiros não assinados de 64 bits são agora manuseados corretamente em Linux
  * Etiqueta de texto incorreto fixo para ficheiros de texto sem formatação na smart_read
  * Tipo de coluna de cadeia de caracteres, agora aparece na vista de métricas
  * Contagem do tipo agora é fixo para mostrar ValueKinds mapeado para FieldType único em vez de itens individuais
  * Write_to_csv já não está a falhar quando o caminho é fornecido como uma cadeia de caracteres
  * Ao utilizar a substituição, deixar "encontrar" em branco já não irá falhar

## <a name="2018-10-12"></a>2018-10 a 12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning SDK for Python v0.1.68

+ **Novas funcionalidades**
  * Apoio multi-inquilino na criação de novo espaço de trabalho.

+ **Insetos fixos**
  * Já não é necessário fixar a versão da biblioteca pynacl ao implementar o serviço web.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>SDK v0.3.0 de preparação de dados do Azure Machine Learning

+ **Novas funcionalidades**
  * Adicionado o método transform_partition_with_file(script_path), que permite que os utilizadores passem o caminho de um ficheiro de Python para executar

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning SDK for Python v0.1.65
[A versão 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) inclui novas funcionalidades, mais documentação, correções de bugs e mais [cadernos de amostras.](https://aka.ms/aml-notebooks)

Consulte [a lista de questões conhecidas](resource-known-issues.md) para conhecer bugs conhecidos e salões.

+ **Quebrar alterações**
  * Workspace.experiments, Workspace.models, Workspace.compute_targets, Workspace.images, dicionário de retorno Workspace.web_services, anteriormente devolvido lista. Consulte a documentação da API [do espaço de trabalho azureml.core.Workspace.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py)

  * Aprendizagem automática removido normalizado média quadrática erro as métricas principais.

+ **HiperDrive**
  * Várias correções de erros de HyperDrive para Bayesianos, melhorias de desempenho para receber chamadas de métricas.
  * Atualização do Tensorflow 1.10 de 1,9
  * Otimização de imagem de docker para o arranque a frio.
  * Os empregos reportam agora o estado correto mesmo que saiam com código de erro diferente de 0.
  * Validação do atributo de RunConfig no SDK.
  * Objeto de HyperDrive executar suporta Cancelar semelhante a uma execução regular: há necessidade de passar todos os parâmetros.
  * Melhorias de widget para manter o estado de valores de lista pendente para execuções distribuídas e HyperDrive execuções.
  * TensorBoard e outro ficheiros de suporte de registo fixo para o servidor de parâmetros.
  * Suporte a MPI Intel (r) no lado do serviço.
  * Bugfix para otimização de parâmetro para distribuído correção execução durante a validação no BatchAI.
  * Gestor de contexto agora identifica a instância primária.

+ **Experiência do portal Azure**
  * log_table() e log_row() são suportadas nos detalhes de execução.
  * Crie automaticamente gráficos para tabelas e linhas com 1, 2 ou 3 colunas numéricas e uma coluna categórica opcional.

+ **Aprendizagem automática de máquinas**
  * Documentação e melhorado processamento de erros
  * Corrigido problemas de desempenho de obtenção de propriedade de execução.
  * Foi corrigido problema de execução de continuar.
  * Problemas de iteração :::no-loc text="ensembling"::: fixos.
  * Treinamento fixo pendurado bug no MAC OS.
  * Downsampling macro média PR/curva cor MULTICLASSE no cenário de validação personalizada.
  * Remover a lógica de índice extra.
  * Remover o filtro de get_output API.

+ **Pipelines**
  * Adicionar um método Pipeline.publish() para publicar um pipeline diretamente, sem a necessidade de uma execução de executar primeira.
  * Adicionou um método PipelineRun.get_pipeline_runs() para obter as condutas de gasoduto que foram geradas a partir de um oleoduto publicado.

+ **Projeto Brainwave**
  * Suporte atualizado para novos modelos de IA, disponíveis no FPGAs.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>SDK v0.2.0 de preparação de dados do Azure Machine Learning
[A versão 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) inclui as seguintes funcionalidades e correções de bugs:

+ **Novas funcionalidades**
  * Suporte para acesso frequente uma codificação
  * Suporte para a transformação de quantile

+ **Bug fixo:**
  * Funciona com qualquer versão de Tornado, sem a necessidade de mudar a sua versão de Tornado
  * Contagens de valores para todos os valores, não apenas o três de principais

## <a name="2018-09-public-preview-refresh"></a>2018-09 (atualização da pré-visualização pública)

Um novo e renovado lançamento de Azure Machine Learning: Leia mais sobre este lançamento: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Passos seguintes

Leia a descrição geral do [Azure Machine Learning](overview-what-is-azure-ml.md).
