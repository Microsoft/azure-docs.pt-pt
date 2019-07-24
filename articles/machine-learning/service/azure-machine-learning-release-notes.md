---
title: O que há de novo na versão?
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre as atualizações mais recentes para Azure Machine Learning serviço e os SDKs de aprendizado de máquina e preparação de dados do Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: Blackmist
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 2568d2213d15faf66ecf606e56ea6b82bacafc3e
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233672"
---
# <a name="azure-machine-learning-service-release-notes"></a>Notas de versão de serviço do Azure Machine Learning

Neste artigo, saiba mais sobre as versões de serviço do Azure Machine Learning.  Para obter uma descrição completa de cada SDK, visite os documentos de referência para:
+ O [ **SDK principal do Azure Machine Learning para Python**](https://aka.ms/aml-sdk)
+ O [ **SDK de preparação de dados** Azure Machine Learning](https://aka.ms/data-prep-sdk)

Ver [a lista de problemas conhecidos](resource-known-issues.md) para saber mais sobre erros conhecidos e soluções alternativas.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Interface visual
+ **Recursos de visualização**
  + Módulo "executar script R" adicionado na interface visual.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK para Python v 1.0.48

+ **Novos recursos**
  + **azureml-opendatasets**
    + o **azureml-contrib-opendatasets** agora está disponível como **azureml-opendatasets**. O pacote antigo ainda pode funcionar, mas é recomendável usar o **azureml-opendatasets** avançando para obter recursos e aprimoramentos mais avançados.
    + Esse novo pacote permite que você registre conjuntos de registros abertos como DataSet no espaço de trabalho AML e aproveite quaisquer funcionalidades oferecidas pelo conjunto de recursos.
    + Ele também inclui recursos existentes, como o consumo de conjuntos de valores abertos como o pandas/SPARK dataframes e junções de local para alguns conjuntos de um conjunto de recursos, como clima.

+ **Recursos de visualização**
    + HyperDriveConfig agora pode aceitar o objeto de pipeline como um parâmetro para dar suporte ao ajuste de hiperparâmetro usando um pipeline.

+ **Correções de bugs e melhorias**
  + **azureml-train-automl**
    + Corrigido o bug sobre a perda de tipos de colunas após a transformação.
    + Foi corrigido o bug para permitir que y_query seja um tipo de objeto que contém nenhum (s) no início. 
    + Corrigido o problema no procedimento de seleção de Ensemble que estava aumentando desnecessariamente o Ensemble resultante, mesmo se as pontuações permaneceram constantes.
    + Corrigido o problema com as configurações whitelist_models e blacklist_models no AutoMLStep.
    + Correção do problema que impedia o uso de pré-processamento quando AutoML teria sido usado no contexto de pipelines do Azure ML.
  + **azureml-opendatasets**
    + Você moveu o azureml-contrib-opendatasets para o azureml-opendatasets.
    + As classes Open DataSet permitidas foram registradas no espaço de trabalho AML e aproveitam as funcionalidades do conjunto de recursos AML diretamente.
    + Desempenho aprimorado do NoaaIsdWeather enriquecer com uma versão não SPARK significativamente.
  + **azureml-explain-model**
    + Documentação online atualizada para objetos de interpretação.
    + Adição de batch_size ao explicador de imitação quando include_local = false para transmitir explicações globais em lotes para melhorar o tempo de execução de DecisionTreeExplainableModel.
    + Correção do problema em `explanation.expected_values` que às vezes retornaria um float em vez de uma lista com um float.
    + Adicionados valores esperados à saída automl para o explicador de imitação em explicar a biblioteca de modelos.
    + Correção do recurso de permutação corrigida quando o argumento de transformações foi fornecido para obter a importância do recurso bruto.
    + Adição de batch_size para simular explicador quando include_local = false para transmitir explicações globais em lotes para melhorar o tempo de execução de DecisionTreeExplainableModel para a biblioteca de explicabilidade de modelo.
  + **azureml-core**
    + Adicionada a capacidade de anexar armazenamentos de DBFS na CLI do AzureML.
    + Corrigido o problema com o carregamento do repositório de armazenamento em que uma pasta `target_path` vazia é `/`criada, se iniciada com.
    + Comparação habilitada de dois conjuntos de valores.
    + O modelo e a exclusão de imagem agora fornecem mais informações sobre como recuperar objetos upstream que dependem deles se a exclusão falhar devido a uma dependência upstream.
    + Preterida a configuração RunConfiguration não utilizada em auto_prepare_environment.
  + **azureml-mlflow**
    + Melhor utilização de recursos de execuções remotas que usam o azureml. mlflow.
    + A documentação do pacote azureml-mlflow foi aprimorada.
    + Correção do problema em que mlflow. log _artifacts ("my_dir") salvaria artefatos em "my_dir/artefato-Paths" em vez de "artefato-Paths".
  + **azureml-pipeline-core**
    + O parâmetro hash_paths para todas as etapas de pipeline foi preterido e será removido no futuro. Por padrão, o conteúdo de pasta_de_origem tem hash (exceto os arquivos listados em. amlignore ou. gitignore)
    + Continuando melhorando o módulo e o ModuleStep para dar suporte a módulos específicos do tipo de computação, em preparação para a integração do RunConfiguration e outras alterações para desbloquear seu uso em pipelines.
  + **azureml-pipeline-steps**
    + AzureBatchStep: Documentação aprimorada com relação a entradas/saídas.
    + AzureBatchStep: Valor padrão delete_batch_job_after_finish alterado para true.
  + **azureml-train-core**
    + As cadeias de caracteres agora são aceitas como destino de computação para ajuste de hiperparâmetro automatizado.
    + Preterida a configuração RunConfiguration não utilizada em auto_prepare_environment.
    + Parâmetros `conda_dependencies_file_path` preteridos `pip_requirements_file_path` e em favor `conda_dependencies_file` de `pip_requirements_file` e, respectivamente.
  + **azureml-opendatasets**
    + Melhore significativamente o desempenho do NoaaIsdWeather enriquecer na versão que não seja do SPARK.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>SDK v 1.1.8 do Azure Machine Learning data Prep

+ **Novos recursos**
 + Os objetos Dataflow agora podem ser iterados, produzindo uma sequência de registros. Consulte a documentação `Dataflow.to_record_iterator`do.

+ **Correções de bugs e melhorias**
 + Aumentou a robustez do dataprep SDK.
 + Tratamento aprimorado de quadros de bits pandas com índices de coluna que não são de cadeia de caracteres.
 + Melhorou o desempenho `to_pandas_dataframe` do em conjuntos de os.
 + Correção de um bug em que a execução do Spark de conjuntos de valores falhou quando executada em um ambiente com vários nós.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>SDK v 1.1.7 do Azure Machine Learning data Prep

Revertemos uma alteração que melhorou o desempenho, pois ela estava causando problemas para alguns clientes usando Azure Databricks. Se você recebeu um problema em Azure Databricks, você pode atualizar para a versão 1.1.7 usando um dos métodos abaixo:
1. Execute este script para atualizar:`%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Recrie o cluster, que instalará a versão mais recente do SDK de preparação de dados.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Azure Machine Learning SDK para Python v 1.0.45

+ **Novos recursos**
  + Adicionar modelo substituto de árvore de decisão ao explicador imitador em azureml-explique-pacote de modelo
  + Capacidade de especificar uma versão do CUDA a ser instalada em imagens do inferência. Suporte para CUDA 9,0, 9,1 e 10,0.
  + As informações sobre as imagens de base de treinamento do Azure ML agora estão disponíveis no [repositório do GitHub de contêineres do Azure ml](https://github.com/Azure/AzureML-Containers) e [DockerHub](https://hub.docker.com/_/microsoft-azureml)
  + Adicionado suporte à CLI para o agendamento do pipeline. Execute "AZ ml pipeline-h" para saber mais
  + Adicionado o parâmetro namespace kubernetes personalizado à CLI e à configuração de implantação do AKS WebService.
  + Parâmetro hash_paths preterido para todas as etapas de pipeline
  + Model. Register agora dá suporte ao registro de vários arquivos individuais como um único modelo com o `child_paths` uso do parâmetro.
  
+ **Recursos de visualização**
    + Os explicadores de Pontuação agora podem, opcionalmente, salvar informações de Conda e Pip para serialização e desserialização mais confiáveis.
    + Correção de bug do seletor de recurso automático.
    + Atualizado mlflow. azureml. build_image para a nova API, bugs com patches expostos pela nova implementação.

+ **Correções de bugs e melhorias**
  + A dependência paramiko foi removida do azureml-Core. Foram adicionados avisos de substituição para métodos de anexação de destino de computação herdados.
  + Melhorar o desempenho de Run. create_children
  + Em explicador de imitação com classificador binário, corrija a ordem das probabilidades quando a probabilidade de professor for usada para dimensionar valores de shap
  + Tratamento de erros e mensagem aprimorados para o aprendizado de máquina automatizado. 
  + Correção do problema de tempo limite de iteração para o aprendizado de máquina automatizado.
  + Melhorou o desempenho de transformação da série temporal para o aprendizado de máquina automatizado.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>SDK v 1.1.6 do Azure Machine Learning data Prep

+ **Novos recursos**
  + Funções de resumo adicionadas para valores`SummaryFunction.TOPVALUES`principais () e valores`SummaryFunction.BOTTOMVALUES`inferiores ().

+ **Correções de bugs e melhorias**
  + Melhorou significativamente o desempenho `read_pandas_dataframe`do.
  + Foi corrigido um bug que causava `get_profile()` uma falha em um fluxo de arquivos que apontasse para arquivo binário.
  + Exposto `set_diagnostics_collection()` para permitir a habilitação/desabilitação programática da coleção de telemetria.
  + Alterou o comportamento `get_profile()`de. Os valores NaN agora são ignorados por min, mean, STD e Sum, que se alinha com o comportamento de pandas.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Azure Machine Learning SDK para Python v 1.0.43

+ **Novos recursos**
  + O Azure Machine Learning agora fornece suporte de primeira classe para aprendizado de máquina e estrutura de análise de dados populares Scikit. Usando o estimador, os usuários podem facilmente treinar e implantar modelos Scikit-learn. [ `SKLearn` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)
    + Saiba como [executar o ajuste de hiperparâmetro com Scikit-Learn usando o hyperdrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + Adição de suporte para a criação de ModuleStep em pipelines junto com classes de módulo e ModuleVersion para gerenciar unidades de computação reutilizáveis.
  + Os WebServices ACI agora dão suporte a scoring_uri persistentes por meio de atualizações. O scoring_uri será alterado de IP para FQDN. O rótulo de nome DNS para FQDN pode ser configurado definindo o dns_name_label em deploy_configuration. 
  + Novos recursos do Machine Learning automatizados:
    + Featurizer STL para previsão
    + O clustering do KMeans está habilitado para a limpeza de recursos
  + As aprovações de cota AmlCompute se tornaram mais rápidas! Agora, automatizamos o processo para aprovar suas solicitações de cota dentro de um limite. Para obter mais informações sobre como as cotas funcionam, saiba [como gerenciar cotas](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).

+ **Recursos de visualização**
    + Integração com o [MLflow](https://mlflow.org) 1.0.0 Tracking por meio do pacote azureml-MLflow (notebooks de[exemplo](https://aka.ms/azureml-mlflow-examples)).
    + Envie o bloco de anotações Jupyter como uma execução. [Documentação de referência de API](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Visualização pública do [detector](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift?view=azure-ml-py) de descompasso de dados por meio do pacote azureml-contrib-descompasso (notebooks de[exemplo](https://aka.ms/azureml-datadrift-example)). A descompasso de dados é uma das principais razões em que a precisão do modelo diminui com o passar do tempo. Acontece quando os dados servidos para o modelo na produção são diferentes dos dados em que o modelo foi treinado. O detector de descompasso de dados AML ajuda o cliente a monitorar descompasso de dados e envia alerta sempre que o descompasso é detectado. 

+ **Alterações recentes**

+ **Correções de bugs e melhorias**
  + O RunConfiguration Load e Save dá suporte à especificação de um caminho de arquivo completo com compatibilidade total para o comportamento anterior.
  + Cache adicionado em ServicePrincipalAuthentication, desativado por padrão.
  + Habilite o registro em log de várias plotagens com o mesmo nome de métrica.
  + A classe de modelo agora é adequadamente importável de azureml`from azureml.core import Model`. Core ().
  + Nas etapas do pipeline `hash_path` , o parâmetro agora é preterido. O novo comportamento é o hash completo de pasta_de_origem, exceto os arquivos listados em. amlignore ou. gitignore.
  + Em pacotes de pipeline, `get_all` vários `get_all_*` métodos e foram preteridos em favor `list` de `list_*`e, respectivamente.
  + o azureml. Core. Get _run não requer mais classes a serem importadas antes de retornar o tipo de execução original.
  + Corrigido um problema em que algumas chamadas para a atualização do WebService não dispararam uma atualização.
  + O tempo limite de pontuação em WebServices AKS deve estar entre 5 ms e 300000ms. O scoring_timeout_ms máximo permitido para solicitações de pontuação foi aumentado de 1 min a 5 min.
  + Os objetos LocalWebservice agora `scoring_uri` têm `swagger_uri` Propriedades e.
  + A criação do diretório de saídas movidos e o carregamento do diretório de saídas do processo do usuário. O SDK do histórico de execução habilitado deve ser executado em cada processo do usuário. Isso deve resolver alguns problemas de sincronização experientes por execuções de treinamento distribuídas.
  + O nome do log do azureml gravado no nome do processo do usuário agora incluirá o nome do processo (somente para treinamento distribuído) e PID.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>SDK v 1.1.5 do Azure Machine Learning data Prep

+ **Correções de bugs e melhorias**
  + Para valores de data e hora interpretados que têm um formato de ano de 2 dígitos, o intervalo de anos válidos foi atualizado para corresponder ao Windows pode ser liberado. O intervalo foi alterado de 1930-2029 para 1950-2049.
  + Ao ler um arquivo e configurar `handleQuotedLineBreaks=True`, `\r` será tratado como uma nova linha.
  + Corrigido um bug que causou `read_pandas_dataframe` a falha em alguns casos.
  + Desempenho aprimorado `get_profile`do.
  + Mensagens de erro aprimoradas.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>SDK v 1.1.4 do Azure Machine Learning data Prep

+ **Novos recursos**
  + Agora você pode usar as seguintes funções de linguagem de expressão para extrair e analisar valores DateTime em novas colunas.
    + `RegEx.extract_record()`extrai elementos DateTime em uma nova coluna.
    + `create_datetime()`cria objetos DateTime a partir de elementos DateTime separados.
  + Ao chamar `get_profile()`, agora você pode ver que as colunas Quantil são rotuladas como (est.) para indicar claramente que os valores são aproximações.
  + Agora você pode usar o * * mascaramento ao ler do armazenamento de BLOBs do Azure.
    + p.`dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Correções de erros**
  + Correção de um bug relacionado à leitura de um arquivo parquet de uma origem remota (blob do Azure).

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK para Python v 1.0.39
+ **For**
  + A opção executar auto_prepare_environment de configuração está sendo preterida, com a preparação automática se tornando o padrão.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>SDK v 1.1.3 do Azure Machine Learning data Prep

+ **Novos recursos**
  + Adicionado suporte para leitura de um banco de dados PostgresSQL, seja chamando read_postgresql ou usando um armazenamento.
    + Consulte os exemplos nos guias de instruções:
      + [Bloco de anotações de ingestão de dados](https://aka.ms/aml-data-prep-ingestion-nb)
      + [Bloco de anotações do repositório de armazenamento](https://aka.ms/aml-data-prep-datastore-nb)

+ **Correções de bugs e melhorias**
  + Problemas corrigidos com a conversão de tipo de coluna:
  + Agora, converte corretamente uma coluna Booleana ou numérica em uma coluna Booleana.
  + Agora não falha ao tentar definir uma coluna de data como tipo de data.
  + Os tipos de JoinType aprimorados e a documentação de referência acompanhada. Ao unir dois fluxos de entrada, agora você pode especificar um destes tipos de junção:
    + NENHUM, CORRESPONDER, INTERNO, UNMATCHLEFT, LEFTANTI, LEFTOUTER, UNMATCHRIGHT, RIGHTANTI, RIGHTOUTER, FULLANTI, FULL.
  + Tipo de dados aprimorado inferência para reconhecer mais formatos de data.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Portal do Azure

No portal do Azure, agora você pode:
+ Criar e executar experimentos de ML automatizados 
+ Crie uma VM do notebook para experimentar os notebooks Jupyter de exemplo ou seus próprios.
+ Seção de criação de nova marca (versão prévia) no espaço de trabalho Machine Learning serviço, que inclui Machine Learning automatizado, interface visual e VMs de notebooks hospedados
    + Criar automaticamente um modelo usando o Machine Learning automatizado 
    + Usar uma interface visual de arrastar e soltar para executar experimentos
    + Crie uma VM de notebook para explorar dados, criar modelos e implantar serviços.
+ Gráfico dinâmico e atualização de métrica em executar relatórios e páginas de detalhes de execução
+ Atualizado o Visualizador de arquivos para logs, saídas e instantâneos em páginas de detalhes de execução.
+ Nova e aprimorada experiência de criação de relatórios na guia experimentos. 
+ Foi adicionada a capacidade de baixar o arquivo config. JSON da página Visão geral do espaço de trabalho do serviço de Azure Machine Learning.
+ Suporte à criação de Machine Learning espaço de trabalho de serviço do espaço de trabalho Azure Databricks 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Azure Machine Learning SDK para Python v 1.0.33
+ **Novos recursos**
  + O método _Workspace. Create_ agora aceita configurações de cluster padrão para clusters de CPU e GPU.
  + Se a criação do espaço de trabalho falhar, os recursos dependentes serão limpos.
  + O SKU do registro de contêiner do Azure padrão foi alternado para básico.
  + O registro de contêiner do Azure é criado lentamente, quando necessário para a execução ou criação de imagem.
  + Suporte para ambientes para execuções de treinamento.

### <a name="notebook-virtual-machine"></a>Máquina virtual do notebook 

Use uma VM de notebook como um ambiente de hospedagem seguro e pronto para a empresa para notebooks Jupyter nos quais você pode programar experimentos de aprendizado de máquina, implantar modelos como pontos de extremidade da Web e executar todas as outras operações com suporte pelo SDK Azure Machine Learning usando Python. Ele fornece vários recursos:
+ [Crie rapidamente uma VM](quickstart-run-cloud-notebook.md) de notebook pré-configurada que tenha a versão mais recente do SDK do Azure Machine Learning e os pacotes relacionados.
+ O acesso é protegido por meio de tecnologias comprovadas, como HTTPS, autenticação e autorização de Azure Active Directory.
+ Armazenamento em nuvem confiável de blocos de anotações e código em sua conta de armazenamento de BLOBs Workspace do Azure Machine Learning. Você pode excluir com segurança sua VM do bloco de anotações sem perder seu trabalho.
+ Blocos de anotações de exemplo pré-instalados para explorar e experimentar Azure Machine Learning recursos de serviço.
+ Recursos de personalização completa de VMs do Azure, qualquer tipo de VM, qualquer pacote, qualquer driver. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK para Python v 1.0.33 lançado.

+ O Modelos de Aceleração de Hardware do Azure ML no [FPGAs](concept-accelerate-with-fpgas.md) está disponível para o público em geral.
  + Agora você pode [usar o pacote azureml-da aceleração extra-Models](how-to-deploy-fpga-web-service.md) para:
    + Treinar os pesos de uma rede neural profunda com suporte (ResNet 50, ResNet 152, DenseNet-121, VGG-16 e SSD-VGG)
    + Usar o aprendizado de transferência com o DNN com suporte
    + Registrar o modelo com o Serviço Gerenciamento de Modelos e colocar o modelo em contêiner
    + Implantar o modelo em uma VM do Azure com um FPGA em um cluster do AKS (serviço kubernetes do Azure)
  + Implantar o contêiner em um dispositivo de servidor [Azure data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)
  + Pontuar seus dados com o ponto de extremidade gRPC com este [exemplo](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Machine Learning automatizado

+ Limpeza de recursos para habilitar a adição dinâmica de featurizers para otimização de desempenho. Novos featurizers: incorporações de trabalho, peso de evidência, codificações de destino, codificação de destino de texto, distância do cluster
+ Smart currículo para lidar com as divisões de treinamento/válidas dentro do ML automatizado
+ Poucas alterações de otimização de memória e melhoria no desempenho de tempo de execução
+ Melhoria de desempenho na explicação do modelo
+ Conversão de modelo ONNX para execução local
+ Suporte adicionado à subamostragem
+ Parando inteligente quando não há critérios de saída definidos
+ Conjuntos empilhados

+ Previsão de série temporal
  + Nova função prever previsão   
  + Agora você pode usar a validação cruzada de origem sem interrupção em dados de série temporal
  + Nova funcionalidade adicionada para configurar o tempo de retardo da série temporal 
  + Nova funcionalidade adicionada para dar suporte a recursos agregados de janela sem interrupção
  + Nova detecção de feriados e featurizer quando o código do país é definido nas configurações do experimento

+ Azure Databricks
  + Funcionalidade de previsão de série temporal e modelo de explainabilty/interpretação habilitada
  + Agora você pode cancelar e retomar (continuar) experimentos de ML automatizados
  + Suporte adicionado para processamento de vários núcleos

### <a name="mlops"></a>MLOps
+ **Implantação local & depuração para contêineres de Pontuação**<br/> Agora você pode implantar um modelo de ML localmente e iterar rapidamente em seu arquivo de Pontuação e dependências para garantir que eles se comportem conforme o esperado.

+ **Introduziu o modelo de & InferenceConfig. Deploy ()**<br/> A implantação de modelo agora dá suporte à especificação de uma pasta de origem com um script de entrada, o mesmo que um RunConfig.  Além disso, a implantação do modelo foi simplificada para um único comando.

+ **Acompanhamento de referência do git**<br/> Os clientes estão solicitando recursos básicos de integração do git por algum tempo, pois ele ajuda a manter uma trilha de auditoria de ponta a ponta. Implementamos o rastreamento em entidades principais no Azure ML para metadados relacionados ao git (repositório, confirmação, estado de limpeza). Essas informações serão coletadas automaticamente pelo SDK e pela CLI.

+ **Criação de perfil do modelo & serviço de validação**<br/> Os clientes freqüentemente reclamam da dificuldade de dimensionar corretamente a computação associada ao seu serviço de inferência. Com nosso serviço de criação de perfil de modelo, o cliente pode fornecer entradas de exemplo e criarei um perfil em 16 diferentes configurações de CPU/memória para determinar o dimensionamento ideal para a implantação.

+ **Traga sua própria imagem base para inferência**<br/> Outra reclamação comum foi a dificuldade de migrar da experimentação para a inferência recompartilhamento de dependências. Com nosso novo recurso de compartilhamento de imagem base, agora você pode reutilizar suas imagens base de experimentação, dependências e todas, para inferência. Isso deve acelerar as implantações e reduzir a lacuna do loop interno para o exterior.

+ **Experiência de geração de esquema do Swagger aprimorada**<br/> Nosso método de geração Swagger anterior era propenso a erros e impossível de ser automatizado. Temos uma nova maneira embutida de gerar esquemas do Swagger de qualquer função do Python por meio de decoradores. Nós abrimos esse código e nosso protocolo de geração de esquema não está acoplado à plataforma ML do Azure.

+ **O CLI do Azure ML está geralmente disponível (GA)**<br/> Os modelos agora podem ser implantados com um único comando da CLI. Temos comentários comuns do cliente que ninguém implanta um modelo de ML de um notebook Jupyter. A [**documentação de referência da CLI**](https://aka.ms/azmlcli) foi atualizada.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK para Python v 1.0.30 lançado.

O [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) foi introduzido para adicionar uma nova versão de um pipeline publicado enquanto mantém o mesmo ponto de extremidade.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning o SDK da preparação de dados v 1.1.2

Nota: O SDK do Python de preparação de dados `numpy` não `pandas` será mais instalado e empacotado. Consulte [as instruções de instalação atualizadas](https://aka.ms/aml-data-prep-installation).

+ **Novos recursos**
  + Agora você pode usar a transformação dinâmica.
    + Guia de instruções: [Bloco de anotações dinâmico](https://aka.ms/aml-data-prep-pivot-nb)
  + Agora você pode usar expressões regulares em funções nativas.
    + Exemplos:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + Agora você pode usar `to_upper`  o `to_lower` e o Functions na linguagem de expressão.
  + Agora você pode ver o número de valores exclusivos de cada coluna em um perfil de dados.
  + Para algumas das etapas mais usadas do leitor, agora você pode passar o `infer_column_types` argumento. Se estiver definido como, `True`a preparação de dados tentará detectar e converter automaticamente os tipos de coluna.
    + `inference_arguments`Agora está preterido.
  + Agora você pode chamar `Dataflow.shape`.

+ **Correções de bugs e melhorias**
  + `keep_columns` Agora aceita um argumento `validate_column_exists`opcional adicional, que verifica se o resultado de `keep_columns` conterá qualquer coluna.
  + Todas as etapas do leitor (que lêem de um arquivo) agora aceitam um `verify_exists`argumento opcional adicional.
  + Desempenho aprimorado de leitura do data frame do pandas e obtenção de perfis de dados.
  + Correção de um bug em que houve falha na divisão de uma única etapa de um Dataflow com um único índice.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Portal do Azure
  + Agora você pode reenviar um script existente executado em um cluster de computação remota existente. 
  + Agora você pode executar um pipeline publicado com novos parâmetros na guia pipelines. 
  + Os detalhes da execução agora dão suporte a um novo Visualizador de arquivos de instantâneo. Você pode exibir um instantâneo do diretório ao enviar uma execução específica. Você também pode baixar o bloco de anotações que foi enviado para iniciar a execução.
  + Agora você pode cancelar as execuções pai do portal do Azure.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK para Python v 1.0.23

+ **Novos recursos**
  + O SDK do Azure Machine Learning agora dá suporte ao Python 3,7.
  + Azure Machine Learning os estimadores DNN agora fornecem suporte interno a várias versões. Por exemplo, `TensorFlow`  o estimador agora `framework_version` aceita um parâmetro e os usuários podem especificar a versão ' 1,10 ' ou ' 1,12 '. Para obter uma lista das versões com suporte na versão atual do SDK, `get_supported_versions()` chame na classe da estrutura desejada (por exemplo `TensorFlow.get_supported_versions()`,).
  Para obter uma lista das versões com suporte da versão mais recente do SDK, consulte a documentação do estimador do [DNN](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>SDK v 1.1.1 do Azure Machine Learning data Prep

+ **Novos recursos**
  + Você pode ler várias fontes de datastore/caminho de email/datareferente usando transformações de read_ *.
  + Você pode executar as seguintes operações em colunas para criar uma nova coluna: divisão, piso, módulo, potência, comprimento.
  + A preparação de dados agora faz parte do pacote de diagnóstico do Azure ML e registrará em log as informações de diagnóstico por padrão.
    + Para desativar essa opção, defina essa variável de ambiente como true: DISABLE_DPREP_LOGGER

+ **Correções de bugs e melhorias**
  + Documentação de código aprimorada para classes e funções usadas com frequência.
  + Correção de um bug em auto_read_file que falhou ao ler arquivos do Excel.
  + Opção adicionada para substituir a pasta em read_pandas_dataframe.
  + Desempenho aprimorado da instalação de dependência do dotnetcore2 e adição de suporte para Fedora 27/28 e Ubuntu 1804.
  + Melhorou o desempenho da leitura de BLOBs do Azure.
  + A detecção de tipo de coluna agora dá suporte a colunas do tipo Long.
  + Correção de um bug em que alguns valores de data estavam sendo exibidos como carimbos de hora em vez de objetos DateTime de Python.
  + Corrigido um bug em que algumas contagens de tipo estavam sendo exibidas como duplos em vez de inteiros.

  
## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK para Python v 1.0.21

+ **Novos recursos**
  + O método *azureml. Core. Run. create_children* permite a criação de baixa latência de várias execuções filhas com uma única chamada.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>SDK da preparação de dados do Azure Machine Learning v 1.1.0

+ **Alterações recentes**
  + O conceito do pacote de preparação de dados foi preterido e não tem mais suporte. Em vez de persistir vários fluxos de entrada em um pacote, você pode persistir os fluxos de entrada individualmente.
    + Guia de instruções: [Abrindo e salvando o notebook de fluxos de anotações](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Novos recursos**
  + A preparação de dados agora pode reconhecer colunas que correspondem a um tipo semântico específico e divididas de acordo. Os STypes atualmente suportados incluem: endereço de email, coordenadas geográficas (Latitude & longitude), endereços IPv4 e IPv6, número de telefone dos EUA e CEP dos EUA.
    + Guia de instruções: [Bloco de anotações de tipos semânticos](https://aka.ms/aml-data-prep-semantic-types-nb)
  + A preparação de dados agora dá suporte às seguintes operações para gerar uma coluna resultante de duas colunas numéricas: subtrair, multiplicar, dividir e módulo.
  + Você pode chamar `verify_has_data()` em um Dataflow para verificar se o Dataflow produziria registros, se executado.

+ **Correções de bugs e melhorias**
  + Agora você pode especificar o número de compartimentos a serem usados em um histograma para perfis de coluna numérica.
  + Agora `read_pandas_dataframe` , a transformação requer que o dataframe tenha nomes de coluna com tipo de cadeia de caracteres ou de byte.
  + Corrigido um bug na `fill_nulls` transformação, em que os valores não foram preenchidos corretamente se a coluna estava ausente.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK para Python v 1.0.18

 + **For**
   + O pacote azureml-tensorboard substitui o azureml-contrib-tensorboard.
   + Com esta versão, você pode configurar uma conta de usuário em seu amlcompute (cluster de computação gerenciada), ao criá-la. Isso pode ser feito passando essas propriedades na configuração de provisionamento. Você pode encontrar mais detalhes na [documentação de referência do SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none-).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>SDK v 1.0.17 do Azure Machine Learning data Prep

+ **Novos recursos**
  + Agora dá suporte à adição de duas colunas numéricas para gerar uma coluna resultante usando a linguagem de expressão.

+ **Correções de bugs e melhorias**
  + Melhorou a documentação e a verificação de parâmetros para random_split.
  
## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>SDK v 1.0.16 do Azure Machine Learning data Prep

+ **Correção de bug**
  + Correção de um problema de autenticação de entidade de serviço que foi causado por uma alteração de API.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK para Python v 1.0.17

+ **Novos recursos**

  + Azure Machine Learning agora fornece suporte de primeira classe para o DNN Framework popular. Usar [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) usuários de classe pode facilmente treinar e implantar modelos de encadeamento.
    + Saiba como [executar o treinamento distribuído com o ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Saiba como [executar o ajuste de hiperparâmetro com o encadeamento usando hyperdrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning pipelines adicionou a capacidade disparam uma execução de pipeline com base em modificações no armazenamento de Datastore. O [notebook de agendamento](https://aka.ms/pl-schedule) de pipeline é atualizado para demonstrar esse recurso.

+ **Correções de bugs e melhorias**
  + Adicionamos suporte Azure Machine Learning pipelines para definir a propriedade source_directory_data_store para um repositório de armazenamento desejado (como um armazenamento de BLOBs) no [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) que são fornecidos para o [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Por padrão, as etapas usam o repositório de arquivos do Azure como o repositório de armazenamento de backup, que pode ter problemas de limitação quando um grande número de etapas é executado simultaneamente.

### <a name="azure-portal"></a>Portal do Azure

+ **Novos recursos**
  + Nova experiência de editor de tabela de arrastar e soltar para relatórios. Os usuários podem arrastar uma coluna do bem para a área de tabela em que uma visualização da tabela será exibida. As colunas podem ser reorganizadas.
  + Novo Visualizador de arquivos de logs
  + Links para execuções de teste, computação, modelos, imagens e implantações na guia atividades

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>SDK v 1.0.15 do Azure Machine Learning data Prep

+ **Novos recursos**
  + A preparação de dados agora dá suporte à gravação de fluxos de arquivo de um fluxo de arquivos. Também fornece a capacidade de manipular os nomes de fluxo de arquivo para criar novos nomes de arquivo.
    + Guia de instruções: [Trabalhando com o bloco de anotações de fluxos de arquivos](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Correções de bugs e melhorias**
  + Desempenho aprimorado de t-Digest em conjuntos de dados grandes.
  + A preparação de dados agora dá suporte à leitura de dados de um caminho de data.
  + Uma codificação ativa agora funciona em colunas booleanas e numéricas.
  + Outras correções de bugs diversas.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Azure Machine Learning SDK para Python v 1.0.15

+ **Novos recursos**
  + Azure Machine Learning pipelines adicionou AzureBatchStep ([Notebook](https://aka.ms/pl-azbatch)), HyperDriveStep (Notebook) e a funcionalidade de agendamento baseada em tempo ([Notebook](https://aka.ms/pl-schedule)).
  +  DataTranferStep atualizado para trabalhar com o Azure SQL Server e o banco de dados do Azure para PostgreSQL ([Notebook](https://aka.ms/pl-data-trans)).

+ **For**
  + Preterido `PublishedPipeline.get`emfavordo `PublishedPipeline.get_published_pipeline` .
  + Preterido `Schedule.get`emfavordo `Schedule.get_schedule` .

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>SDK v 1.0.12 do Azure Machine Learning data Prep

+ **Novos recursos**
  + A preparação de dados agora dá suporte à leitura de um banco de dado SQL do Azure usando o repositório de armazenamento.
 
+ **For**
  + Melhorou o desempenho da memória de determinadas operações em dados grandes.
  + `read_pandas_dataframe()`Agora, `temp_folder` é necessário especificar para ser especificado.
  + A `name` Propriedade on `ColumnProfile` foi preterida. em `column_name` vez disso, use.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK para Python v 1.0.10

+ **Alterações**: 
  + O SDK do Azure ML não tem mais pacotes do Azure-CLI como dependência. Especificamente, as dependências do Azure-CLI-Core e do Azure-CLI-Profile foram removidas do azureml-Core. Essas são as alterações que afetam o usuário:
    + Se você estiver executando "AZ login" e usando o azureml-SDK, o SDK fará o navegador ou o log de código do dispositivo em mais uma vez. Ele não usará nenhum estado de credenciais criado por "AZ login".
    + Para CLI do Azure autenticação, como usar "AZ login", use a classe _azureml. Core. Authentication. AzureCliAuthentication_ . Para CLI do Azure autenticação, faça a _instalação do Pip Azure-CLI_ no ambiente do Python em que você instalou o azureml-SDK.
    + Se você estiver fazendo "AZ login" usando uma entidade de serviço para automação, é recomendável usar a classe _azureml. Core. Authentication. ServicePrincipalAuthentication_ , pois o azureml-SDK não usará o estado de credenciais criado pela CLI do Azure. 

+ **Correções de bugs**: Esta versão contém principalmente correções de bugs secundárias

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>SDK v 1.0.8 do Azure Machine Learning data Prep

+ **Correções de erros**
  + Melhorou o desempenho da obtenção de perfis de dados.
  + Correção de bugs secundários relacionados ao relatório de erros.
  
### <a name="azure-portal-new-features"></a>Portal do Azure: novos recursos
+ Nova experiência de criação de gráficos de arrastar e soltar para relatórios. Os usuários podem arrastar uma coluna ou atributo do bem para a área do gráfico, em que o sistema selecionará automaticamente um tipo de gráfico apropriado para o usuário com base no tipo de dados. Os usuários podem alterar o tipo de gráfico para outros tipos aplicáveis ou adicionar atributos adicionais.

    Tipos de gráfico com suporte:
    - Gráfico de linhas
    - Histograma
    - Gráfico de barras empilhadas
    - Gráfico de caixa
    - Gráfico de dispersão
    - Gráfico de bolhas
+ O Portal agora gera relatórios dinamicamente para experimentos. Quando um usuário envia uma execução para um experimento, um relatório será gerado automaticamente com as métricas registradas e grafos para permitir a comparação entre diferentes execuções. 

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning SDK para Python v 1.0.8

+ **Correções de bugs**: Esta versão contém principalmente correções de bugs secundárias

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>SDK v 1.0.7 do Azure Machine Learning data Prep

+ **Novos recursos**
  + Aprimoramentos do repositório de armazenamento (documentados no [Guia de instruções de armazenamento de repositório](https://aka.ms/aml-data-prep-datastore-nb))
    + Foi adicionada a capacidade de ler e gravar no compartilhamento de arquivos do Azure e nos repositórios de armazenamento do ADLS em expansão.
    + Ao usar os armazenamentos de dados, a preparação do dado agora dá suporte ao uso da autenticação de entidade de serviço em vez de autenticação interativa.
    + Adicionado suporte para URLs WASB e wasbs.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>SDK v 1.0.6 do Azure Machine Learning data Prep

+ **Correções de erros**
  + Corrigido o bug com a leitura de contêineres de blob do Azure legíveis no Spark

## <a name="2018-12-20"></a>2018-12-20 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK para Python v 1.0.6
+ **Correções de bugs**: Esta versão contém principalmente correções de bugs secundárias

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>SDK v 1.0.4 do Azure Machine Learning data Prep

+ **Novos recursos**
  + `to_bool`a função agora permite que valores incompatíveis sejam convertidos em valores de erro. Esse é o novo comportamento de incompatibilidade `to_bool` padrão `set_column_types`para e, enquanto que o comportamento padrão anterior era converter valores incompatíveis para false.
  + Ao chamar `to_pandas_dataframe`, há uma nova opção para interpretar valores nulos/ausentes em colunas numéricas como NaN.
  + Capacidade adicional de verificar o tipo de retorno de algumas expressões para garantir a consistência do tipo e a falha antecipadamente.
  + Agora você pode chamar `parse_json` para analisar valores em uma coluna como objetos JSON e expandi-los em várias colunas.

+ **Correções de erros**
  + Correção de um bug que `set_column_types` falhou no Python 3.5.2.
  + Correção de um bug que falhou ao se conectar ao repositório de armazenamento usando uma imagem AML.

+ **Atualizações**
  * [Notebooks de exemplo](https://aka.ms/aml-data-prep-notebooks) para obter tutoriais de introdução, estudos de caso e guias de instruções.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Disponibilidade Geral

O serviço do Azure Machine Learning está agora em disponibilidade geral.

### <a name="azure-machine-learning-compute"></a>Computação do Azure Machine Learning
Com esta versão, anunciamos uma nova experiência de computação gerenciada por meio da [computação Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute). Esse destino de computação substitui a computação de ia do lote do Azure para Azure Machine Learning. 

Este destino de computação:
+ É usado para treinamento de modelo e inferência/Pontuação de lote
+ É computação de único a vários nós
+ O gerenciamento de cluster e o agendamento de trabalho para o usuário
+ Autoescalas por padrão
+ Suporte para recursos de CPU e GPU 
+ Permite o uso de VMs de baixa prioridade para reduzir o custo

Computação do Azure Machine Learning podem ser criado em Python, através do portal do Azure ou a CLI. Tem de ser criado na região da sua área de trabalho e não pode ser ligado a outra área de trabalho. Esse destino de computação usa um contêiner do Docker para sua execução e empacota suas dependências para replicar o mesmo ambiente em todos os seus nós.

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
  * to_spark_dataframe () não falhará mais quando o Spark `Kryo` usar a serialização
  * Inspetor de contagem de valores agora pode mostrar mais de 1000 valores exclusivos
  * Divisão aleatória já não falha se o fluxo de dados original não tiver um nome  

+ **Mais informações**
  * [SDK de Preparação de Dados do Azure Machine Learning](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Documentos e blocos de notas
+ Pipelines de ML
  + Exemplos de transferência de blocos de notas novo e atualizados para começar a trabalhar com pipelines, controlo de âmbito do batch e estilo: https://aka.ms/aml-pipeline-notebooks
  + Saiba como [criar seu primeiro pipeline](how-to-create-your-first-pipeline.md)
  + Saiba como [predições de batch com pipelines de execução](how-to-run-batch-predictions.md)
+ Destino de computação Azure Machine Learning
  + Os [blocos de anotações de exemplo](https://aka.ms/aml-notebooks) agora são atualizados para usar a nova computação gerenciada.
  + [Saiba mais sobre este computação](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Portal do Azure: novos recursos
+ Criar e gerir [computação do Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) tipos no portal.
+ Monitorizar a utilização de quota e [quota de pedido](how-to-manage-quotas.md) para computação do Azure Machine Learning.
+ Exiba Azure Machine Learning status do cluster de computação em tempo real.
+ Foi adicionado suporte de rede virtual para a criação de computação do Azure Machine Learning e o Azure Kubernetes Service.
+ Execute novamente os pipelines publicados com os parâmetros existentes.
+ Novos [automatizada de gráficos do machine learning](how-to-track-experiments.md#auto) para modelos de classificação (comparação de precisão, ganhos, calibração, gráfico de importância da funcionalidade com explainability de modelo) e modelos de regressão (residuals e gráfico de importância da funcionalidade com modelo explainability). 
+ Pipelines podem ser visualizados no portal do Azure




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning SDK for Python v0.1.80

+ **Alterações recentes** 
  * *azureml.Train.widgets* espaço de nomes foi movido para *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* pretere as seguintes classes - *azureml.core.compute.BatchAICompute* e *azureml.core.compute.DSVMCompute*. A segunda classe será removida em versões subsequentes. A classe AmlCompute tem uma definição mais fácil agora, simplesmente precisa de um vm_size e o max_nodes e irá dimensionar automaticamente o cluster de 0 para o max_nodes quando é submetida uma tarefa. Nossos [notebooks de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) foram atualizados com essas informações e devem fornecer exemplos de uso. Esperamos que, como essa simplificação e muitos dos recursos mais interessantes para entrar numa versão posterior!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>SDK v0.5.1 de preparação de dados do Azure Machine Learning 

Saiba mais sobre o SDK de preparação de dados, lendo [referenciar docs](https://aka.ms/data-prep-sdk).
+ **Novos recursos**
   * Criar uma nova CLI de DataPrep para executar pacotes DataPrep e visualizar o perfil de dados para um conjunto de dados ou o fluxo de dados
   * API de SetColumnType reestruturada para melhorar a usabilidade
   * Smart_read_file nome mudado para auto_read_file
   * Agora inclui skew e kurtosis no perfil de dados
   * Pode exemplo com amostragem stratified
   * Pode ler a partir de arquivos zip que contêm ficheiros CSV
   * Pode dividir conjuntos de linhas de linha com divisão aleatória (por exemplo, em conjuntos de treinamento de teste)
   * Pode obter todos os tipos de dados de coluna de um Dataflow ou de um perfil de dados chamando`.dtypes`
   * Pode obter a contagem de linhas de um Dataflow ou um perfil de dados chamando`.row_count`

+ **Correções de erros**
   * Fixo de tempo de conversão duplo 
   * Foi corrigido---vyhodnocení depois de adicionar qualquer coluna 
   * Foi corrigido um problema com FuzzyGrouping, onde ele não detectaria grupos em alguns casos
   * Função de tipo fixo para respeitar a ordem de classificação de várias colunas
   * Fixo e/ou expressões para serem semelhantes ao modo `pandas` como os manipula
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
  * \* Workspace. compute_targets, repositórios de armazenamento, experimentos, imagens, modelos  e WebServices são propriedades em vez de métodos. Por exemplo, substitua *Workspace.compute_targets()* com *Workspace.compute_targets*.
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
  * estouros inteiros sem sinal de 64 bits agora são tratados adequadamente no Linux
  * Etiqueta de texto incorreto fixo para ficheiros de texto sem formatação na smart_read
  * Tipo de coluna de cadeia de caracteres, agora aparece na vista de métricas
  * Contagem do tipo agora é fixo para mostrar ValueKinds mapeado para FieldType único em vez de itens individuais
  * Write_to_csv já não está a falhar quando o caminho é fornecido como uma cadeia de caracteres
  * Ao utilizar a substituição, deixar "encontrar" em branco já não irá falhar 

## <a name="2018-10-12"></a>2018-10 a 12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning SDK for Python v0.1.68

+ **Novos recursos**
  * Suporte a vários locatários ao criar novo espaço de trabalho.

+ **Erros corrigidos**
  * Você não precisa mais fixar a versão da biblioteca pynacl ao implantar o serviço Web.

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
  * Os trabalhos agora relatam o status correto mesmo se eles forem encerrados com um código de erro diferente de 0. 
  * Validação do atributo de RunConfig no SDK. 
  * Objeto de HyperDrive executar suporta Cancelar semelhante a uma execução regular: há necessidade de passar todos os parâmetros. 
  * Melhorias de widget para manter o estado de valores de lista pendente para execuções distribuídas e HyperDrive execuções. 
  * TensorBoard e outro ficheiros de suporte de registo fixo para o servidor de parâmetros. 
  * Suporte a MPI Intel (r) no lado do serviço. 
  * Bugfix para otimização de parâmetro para distribuído correção execução durante a validação no BatchAI. 
  * Gestor de contexto agora identifica a instância primária. 

+ **Experiência do portal do Azure**
  * log_table() e log_row() são suportadas nos detalhes de execução. 
  * Crie automaticamente gráficos para tabelas e linhas com 1, 2 ou 3 colunas numéricas e uma coluna categórica opcional.

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
  * Um método PipelineRun. Get _pipeline_runs () foi adicionado para buscar as execuções de pipeline que foram geradas de um pipeline publicado.

+ **Project Brainwave**
  * Suporte atualizado para novos modelos de IA, disponíveis no FPGAs.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>SDK v0.2.0 de preparação de dados do Azure Machine Learning
A [versão 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) inclui os seguintes recursos e correções de bugs:

+ **Novos recursos**
  * Suporte para acesso frequente uma codificação
  * Suporte para a transformação de quantile
   
+ **Erro corrigido:**
  * Funciona com qualquer versão de Tornado, sem a necessidade de mudar a sua versão de Tornado
  * Contagens de valores para todos os valores, não apenas o três de principais

## <a name="2018-09-public-preview-refresh"></a>2018-09 (atualização da pré-visualização pública)

Uma nova versão atualizada do Azure Machine Learning: Leia mais sobre esta versão: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Passos Seguintes

Leia a visão geral do [serviço de Azure Machine Learning](../service/overview-what-is-azure-ml.md).
