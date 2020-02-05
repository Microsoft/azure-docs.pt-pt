---
title: O que há de novo na versão?
titleSuffix: Azure Machine Learning
description: Saiba mais sobre as atualizações mais recentes para Azure Machine Learning e o aprendizado de máquina e SDKs do Python de preparação de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 963572e19be71bf572357aadeee443696210a262
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984970"
---
# <a name="azure-machine-learning-release-notes"></a>Notas de versão do Azure Machine Learning

Neste artigo, saiba mais sobre as versões de Azure Machine Learning.  Para obter o conteúdo completo de referência do SDK, visite a página de referência do [**SDK principal do Azure Machine Learning para Python**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) .

Ver [a lista de problemas conhecidos](resource-known-issues.md) para saber mais sobre erros conhecidos e soluções alternativas.

## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK para Python v 1.0.85

+ **Novos recursos**
  + **azureml-core**
    + Obtenha o uso principal atual e a limitação de cota para recursos de AmlCompute em um determinado espaço de trabalho e assinatura
  
  + **azureml-contrib-pipeline-etapas**
    + Habilitar o usuário a passar o conjunto de resultados de tabela como resultado intermediário da etapa anterior para parallelrunstep

+ **Correções de bugs e melhorias**
  + **azureml-automl-tempo de execução**
    + Retirou a exigência da coluna y_query no pedido ao serviço de previsão implantado. 
    + O 'y_query' foi removido da secção de pedidos de serviço de portátil Orange Juice do Dominick.
    + Correção do bug que impede a previsão nos modelos implantados, operando em conjuntos de dados com colunas de data e hora.
    + O coeficiente de correlação de Matthews foi adicionado como uma métrica de classificação, para classificação binária e multiclasse.
  + **azureml-contrib-interpret**
    + Os explicadores de texto removidos do azureml-contrib-interprete como explicações de texto foram movidos para o repositório de texto de interpretação que será liberado em breve.
  + **azureml-core**
    + Conjunto de conjuntos: os usos do conjunto de arquivos não dependem mais do numpy e do pandas para serem instalados no Python env.
    + Mudou o LocalWebservice.wait_for_deployment() para verificar o estado do contentor local Docker antes de tentar pingar o seu ponto final de saúde, reduzindo consideravelmente o tempo que leva para reportar uma implementação falhada.
    + Correção da inicialização de uma propriedade interna usada em LocalWebservice. reload () quando o objeto de serviço é criado a partir de uma implantação existente usando o Construtor LocalWebservice ().
    + Mensagem de erro editada para fins de esclarecimento.
    + Adicioneum novo método chamado get_access_token() ao AksWebservice que devolverá o objeto AksServiceAccessToken, que contém token de acesso, atualização após carimbo de tempo, expiração no tempo e tipo de token. 
    + Método de get_token() existente no AksWebservice, à medida que o novo método devolve toda a informação que este método devolve.
    + Saída modificada do comando AZ ml Service Get-Access-token. Token renomeado para accessToken e refreshBy para refreshAfter. Propriedades expiryOn e TokenType adicionadas.
    + get_ative_runs fixa
  + **azureml-explain-model**
    + atualizado o shap para 0.33.0 e interprete-Community para 0,4. *
  + **azureml-interpretar**
    + atualizado o shap para 0.33.0 e interprete-Community para 0,4. *
  + **azureml-Train-automl-Runtime**
    + O coeficiente de correlação de Matthews foi adicionado como uma métrica de classificação, para classificação binária e multiclasse.
    + Substituir o sinalizador de pré-processamento do código e substituído por personalização-personalização está ativado por padrão

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK para Python v 1.0.83

+ **Novos recursos**
  + Conjunto de dados: Adicione duas opções `on_error` e `out_of_range_datetime` para `to_pandas_dataframe` falhar quando os dados têm valores de erro em vez de os encher com `None`.
  + Espaço de trabalho: Adicionou a bandeira `hbi_workspace` para espaços de trabalho com dados sensíveis que permitem encriptação adicional e desativa diagnósticos avançados em espaços de trabalho. Também adicionámos suporte para trazer as suas próprias chaves para a instância cosmos DB associada, especificando os parâmetros `cmk_keyvault` e `resource_cmk_uri` ao criar um espaço de trabalho, que cria uma instância Cosmos DB na sua subscrição enquanto aprovisiona o seu espaço de trabalho. [Leia mais aqui.](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-cosmos-db)

+ **Correções de bugs e melhorias**
  + **azureml-automl-tempo de execução**
    + Correção de uma regressão que causou a geração de um TypeError ao executar AutoML em versões do Python abaixo de 3.5.4.
  + **azureml-core**
    + Inseto fixo em `datastore.upload_files` onde o caminho relativo que não começou com `./` não foi capaz de ser usado.
    + Mensagens de substituição adicionadas para todas as classes de imagem caminhos
    + A construção de URL de Gerenciamento de Modelos fixa para a região mooncake.
    + Problema fixo em que os modelos que usam source_dir não podiam ser embalados para funções Azure.    
    + Adicionou uma opção ao [Ambiente.build_local()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) para empurrar uma imagem para o registo de contentores do espaço de trabalho AzureML
    + O SDK foi atualizado para usar a nova biblioteca de tokens no Azure Synapse de forma compatível.
  + **azureml-interpretar**
    + Corrigido o bug onde nenhum foi retornado quando nenhuma explicação estava disponível para download. Agora gera uma exceção, correspondendo ao comportamento em outro lugar.
  + **azureml-pipeline-steps**
    + Não é permitido passar `DatasetConsumptionConfig`s para `Estimator`parâmetro de `inputs` quando o `Estimator` será usado em um `EstimatorStep`.
  + **azureml-SDK**
    + Adicionado o cliente AutoML ao pacote azureml-SDK, permitindo que as execuções de AutoML remotas sejam enviadas sem instalar o pacote completo de AutoML.
  + **azureml-Train-automl-Client**
    + Alinhamento corrigido na saída do console para execuções de automl
    + Corrigido um bug em que a versão incorreta do pandas pode ser instalada em amlcompute remotas.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK para Python v 1.0.81

+ **Correções de bugs e melhorias**
  + **azureml-contrib-interpret**
    + adiar dependência de shap para interpretação-Community do azureml-interpret
  + **azureml-core**
    + O destino de computação agora pode ser especificado como um parâmetro para os objetos de configuração de implantação correspondentes. Isso é especificamente o nome do destino de computação no qual implantar, não o objeto do SDK.
    + Adicionadas informações de CreatedBy a objetos de modelo e de serviço. Pode ser acedido através de <var>.created_by
    + Foi corrigido ContainerImage. Run (), que não configurava corretamente a porta HTTP do contêiner do Docker.
    + Tornar `azureml-dataprep` opcional para `az ml dataset register` comando da CLI
  + **azureml-dataprep**
    + Fixou um bug onde TabularDataset.to_pandas_dataframe recairia incorretamente para um leitor alternativo e imprimiria um aviso.
  + **azureml-explain-model**
    + adiar dependência de shap para interpretação-Community do azureml-interpret
  + **azureml-pipeline-core**
    + Adicionada nova etapa de pipeline `NotebookRunnerStep`, para executar um bloco de anotações local como uma etapa no pipeline.
    + Funções de get_all deprecatadas removidas para Pipelines, Horários e PipelineEndpoints
  + **azureml-Train-automl-Client**
    + Começou a depreciação de data_script como entrada para a AutoML.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK para Python v 1.0.79

+ **Correções de bugs e melhorias**
  + **azureml-automl-Core**
    + FeaturizationConfig removidas para registro em log
      + Atualizado o log para o log "auto"/"off"/"personalizado" apenas.
  + **azureml-automl-tempo de execução**
    + Adicionado suporte para pandas. Série e pandas. Categórico para detectar o tipo de dados da coluna. Anteriormente, só há suporte para numpy. ndarray
      + Foram adicionadas alterações de código relacionadas para lidar com dtype categóricos corretamente.
    + A interface da função de previsão foi melhorada: o parâmetro de y_pred foi opcional. -Os docstrings foram aprimorados.
  + **azureml-contrib-DataSet**
    + Correção de um bug em que não foi possível montar os conjuntos de itens rotulados.
  + **azureml-core**
    + Correção de erros para `Environment.from_existing_conda_environment(name, conda_environment_name)`. O usuário pode criar uma instância do ambiente que é a réplica exata do ambiente local
    + Alterou os métodos de Datasets relacionados com séries de tempo para `include_boundary=True` por padrão.
  + **azureml-Train-automl-Client**
    + Corrigido o problema em que os resultados da validação não são impressos quando mostrar saída está definido como false.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK para Python v 1.0.76

+ **Alterações recentes**
  + Problemas de atualização do Azureml-Train-AutoML
    + Atualizando para o azureml-Train-automl > = 1.0.76 do azureml-Train-automl < 1.0.76 pode causar instalações parciais, fazendo com que algumas importações de automl falhem. Para resolver isto, pode executar o script de configuração encontrado em https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd. Ou, se você estiver usando o Pip diretamente, poderá:
      + "instalação de Pip--atualizar o azureml-Train-automl"
      + "instalação de Pip--ignorar-instalado azureml-Train-automl-Client"
    + ou você pode desinstalar a versão antiga antes de atualizar
      + "desinstalação de Pip azureml-Train-automl"
      + "instalação de Pip azureml-Train-automl"

+ **Correções de bugs e melhorias**
  + **azureml-automl-tempo de execução**
    + AutoML agora levará em conta as classes true e false ao calcular as métricas escalares médias para tarefas de classificação binária.
    + O aprendizado de máquina e o código de treinamento foram movidos no AzureML-AutoML-Core para um novo pacote AzureML-AutoML-Runtime.
  + **azureml-contrib-DataSet**
    + Ao ligar `to_pandas_dataframe` num conjunto de dados rotulado com a opção de descarregamento, pode agora especificar se deve ou não substituir os ficheiros existentes.
    + Ao ligar para `keep_columns` ou `drop_columns` que resulte na queda de uma série de tempo, etiqueta ou coluna de imagem, as capacidades correspondentes serão também eliminadas para o conjunto de dados.
    + Corrigido um problema com o carregador de pytorch para a tarefa de detecção de objeto.
  + **azureml-contrib-interpret**
    + Widget de painel de explicação removido de azureml-contrib-interpret, alterou o pacote para referência ao novo em interpret_community
    + Versão atualizada da interpretação-Community para 0.2.0
  + **azureml-core**
    + Melhorar o desempenho de `workspace.datasets`.
    + Foi adicionada a capacidade de registrar o armazenamento de dados do Azure SQL usando o nome de usuário e a autenticação de senha
    + Correção para carregar RunConfigurations de caminhos relativos.
    + Ao ligar `keep_columns` ou `drop_columns` que resulte na queda de uma coluna de séries de tempo, as capacidades correspondentes serão também eliminadas para o conjunto de dados.
  + **azureml-interpretar**
    + Versão atualizada da interpretação-Community para 0.2.0
  + **azureml-pipeline-steps**
    + Valores suportados documentados para `runconfig_pipeline_params` para passos de gasoduto de aprendizagem automática azul.
  + **azureml-pipeline-core**
    + Adicionada a opção de CLI para baixar a saída no formato JSON para comandos de pipeline.
  + **azureml-train-automl**
    + Divida o AzureML-Train-AutoML em 2 pacotes, um pacote do cliente AzureML-Train-AutoML-Client e um pacote de treinamento de ML AzureML-Train-AutoML-Runtime
  + **azureml-Train-automl-Client**
    + Adicionou um cliente fino para enviar experimentos AutoML sem a necessidade de instalar nenhuma dependência de Machine Learning localmente.
    + Registro de log fixo de atraso detectado, tamanhos de janela sem interrupção e horizontes máximos em execuções remotas detectadas automaticamente.
  + **azureml-Train-automl-Runtime**
    + Foi adicionado um novo pacote AutoML para isolar os componentes de aprendizado de máquina e tempo de execução do cliente.
  + **azureml-contrib-Train-RL**
    + Adicionado suporte do reforço Learning no SDK.
    + Adicionado suporte a AmlWindowsCompute no RL SDK.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK para Python v 1.0.74

  + **Recursos de visualização**
    + **azureml-contrib-DataSet**
      + Depois de importar o conjunto de dados azureml-contrib, pode ligar para `Dataset.Labeled.from_json_lines` em vez de `._Labeled` para criar um conjunto de dados rotulado.
      + Ao ligar `to_pandas_dataframe` num conjunto de dados rotulado com a opção de descarregamento, pode agora especificar se deve ou não substituir os ficheiros existentes.
      + Ao ligar `keep_columns` ou `drop_columns` que resulte na queda de uma série de tempo, etiqueta ou coluna de imagem, as capacidades correspondentes serão também eliminadas para o conjunto de dados.
      + Problemas fixos com carregador PyTorch ao ligar para `dataset.to_torchvision()`.

+ **Correções de bugs e melhorias**
  + **azure-cli-ml**
    + Adicionada a criação de perfil de modelo à CLI de visualização.
    + Corrige alterações significativas no armazenamento do Azure fazendo com que a CLI do AzureML falhe.
    + Adicionado Load Balancer tipo a MLC para tipos AKS
  + **azureml-automl-Core**
    + Corrigido o problema com a detecção de horizonte máximo na série temporal, tendo valores ausentes e vários refinamentos.
    + Corrigido o problema com falhas durante a geração de divisões de validação cruzada.
    + Substitua esta seção por uma mensagem no formato de redução para aparecer nas notas de versão: tratamento aprimorado de refinamentos em conjuntos de dados de previsão.
    + Correção do problema com o mascaramento de algumas informações do usuário durante o registro em log. -Registro em log dos erros aprimorados durante a previsão de execuções.
    + Adicionar psutil como uma dependência Conda ao arquivo de implantação yml gerado automaticamente.
  + **azureml-contrib-Mir**
    + Corrige alterações significativas no armazenamento do Azure fazendo com que a CLI do AzureML falhe.
  + **azureml-core**
    + Corrige um bug que causou a implantação de modelos em Azure Functions para produzir o 500S.
    + Corrigido um problema em que o arquivo amlignore não foi aplicado em instantâneos.
    + Adicionou um novo API amlcompute.get_ative_runs que devolve um gerador para funcionamento e corridas em fila numa dada amlcompute.
    + Adicionado Load Balancer tipo a MLC para tipos AKS.
    + Acrescentou append_prefix parâmetro bool para download_files em run.py e download_artifacts_from_prefix em artifacts_client. Esta bandeira é utilizada para aplainar seletivamente o ficheiro de origem, pelo que apenas o nome do ficheiro ou da pasta é adicionado ao output_directory
    + Corrija o problema de desserialização para `run_config.yml` com a utilização do conjunto de dados.
    + Ao ligar `keep_columns` ou `drop_columns` que resulte na queda de uma coluna de séries temporais, as capacidades correspondentes serão também eliminadas para o conjunto de dados.
  + **azureml-interpretar**
    + Atualizada a versão de interpretação da Comunidade para 0.1.0.3
  + **azureml-train-automl**
    + Corrigiu um problema em que automl_step pode não imprimir problemas de validação.
    + Os register_model fixos para ter sucesso mesmo que o ambiente do modelo esteja a faltar dependências localmente.
    + Corrigido um problema em que algumas execuções remotas não estavam habilitadas para o Docker.
    + Adicione o log da exceção que está causando a falha prematura de uma execução local.
  + **azureml-train-core**
    + Considere resume_from corre no cálculo da afinação automática de hiperparâmetros melhores corridas para crianças.
  + **azureml-pipeline-core**
    + Manipulação de parâmetro fixo na construção de argumento de pipeline.
    + Adicionada a descrição de pipeline e o parâmetro de tipo de etapa YAML.
    + Novo formato YAML para etapa de pipeline e aviso de substituição adicionado para o formato antigo.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Experiência na Web

A página de aterrissagem do espaço de trabalho colaborativo na [https://ml.azure.com](https://ml.azure.com) foi aprimorada e remarcada como o Azure Machine Learning Studio (versão prévia).

No estúdio, você pode treinar, testar, implantar e gerenciar Azure Machine Learning ativos, como conjuntos de informações, pipelines, modelos, pontos de extremidade e muito mais.

Acesse as seguintes ferramentas de criação baseadas na Web no estúdio:

| Ferramenta baseada na Web | Descrição | Edição |
|-|-|-|
| VM do notebook (visualização) | Estação de trabalho baseada em nuvem totalmente gerenciada | Básico & Enterprise |
| [Machine Learning automatizado](tutorial-first-experiment-automated-ml.md) (visualização) | Não há experiência de código para automatizar o desenvolvimento de modelos do Machine Learning | Enterprise |
| [Designer](concept-designer.md) (versão prévia) | Ferramenta de modelagem de aprendizado de máquina de arrastar e soltar anteriormente conhecida como o designer | Enterprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Aprimoramentos do designer de Azure Machine Learning

+ Anteriormente conhecido como interface visual 
+   11 novos [módulos](algorithm-module-reference/module-reference.md) , incluindo recomendados, classificadores e utilitários de treinamento, incluindo engenharia de recursos, validação cruzada e transformação de dados.

### <a name="r-sdk"></a>SDK para R 
 
Os cientistas de dados e os desenvolvedores de ia usam o [SDK do Azure Machine Learning para R](tutorial-1st-r-experiment.md) para criar e executar fluxos de trabalho de aprendizado de máquina com Azure Machine Learning.

O SDK do Azure Machine Learning para R usa o pacote `reticulate` para associar ao SDK do Python. Ligando diretamente ao Python, o SDK para R permite que você acesse os principais objetos e métodos implementados no SDK do Python de qualquer ambiente de R que você escolher.

Os principais recursos do SDK incluem:

+   Gerir recursos de cloud para monitorizar, registar e organizar as experimentações de machine learning.
+   Treine modelos usando recursos de nuvem, incluindo treinamento de modelo com aceleração de GPU.
+   Implante seus modelos como WebServices em ACI (instâncias de contêiner do Azure) e AKS (serviço kubernetes do Azure).

Consulte o [site do pacote](https://azure.github.io/azureml-sdk-for-r) para obter a documentação completa.

### <a name="azure-machine-learning-integration-with-event-grid"></a>Integração do Azure Machine Learning com a grade de eventos 

Azure Machine Learning agora é um provedor de recursos para a grade de eventos, você pode configurar eventos de Machine Learning por meio do portal do Azure ou CLI do Azure. Os usuários podem criar eventos para conclusão de execução, registro de modelo, implantação de modelo e descompasso de dados detectados. Esses eventos podem ser roteados para manipuladores de eventos com suporte na grade de eventos para consumo. Consulte o [esquema](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning)de eventos do Machine Learning, os [conceitos](https://docs.microsoft.com/azure/machine-learning/concept-event-grid-integration) e artigos do [tutorial](https://docs.microsoft.com/azure/machine-learning/how-to-use-event-grid) para obter mais detalhes.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK para Python v 1.0.72

+ **Novos recursos**
  + Os monitores de conjunto de dados adicionados por meio do pacote [**azureml-descompasso**](https://docs.microsoft.com/python/api/azureml-datadrift) , permitindo o monitoramento de conjuntos de dados de série temporal para descompasso ou outras alterações estatísticas ao longo do tempo. Alertas e eventos podem ser disparados se o descompasso for detectado ou se outras condições nos dados forem atendidas. Consulte [nossa documentação](https://aka.ms/datadrift) para obter detalhes.
  + Anunciando duas novas edições (também chamadas de SKU intercambiáveis) em Azure Machine Learning. Com esta versão, agora você pode criar um espaço de trabalho básico ou de Azure Machine Learning empresarial. Todos os espaços de trabalho existentes serão padronizados para a edição básica e você poderá ir para a portal do Azure ou para o estúdio para atualizar o espaço de trabalho a qualquer momento. Você pode criar um espaço de trabalho básico ou empresarial do portal do Azure. Leia [nossa documentação](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) para saber mais. No SDK, a edição do seu espaço de trabalho pode ser determinada usando a propriedade "SKU" do seu objeto de espaço de trabalho.
  + Também fizemos aprimoramentos para Azure Machine Learning computação. agora você pode exibir métricas para seus clusters (como nós totais, nós em execução, cota de núcleo total) em Azure Monitor, além de exibir os logs de diagnóstico para depuração. Além disso, você também pode exibir execuções em fila ou atualmente em execução no cluster e detalhes como os IPs dos vários nós no cluster. Você pode exibi-los no portal ou usando funções correspondentes no SDK ou na CLI.

  + **Recursos de visualização**
    + Estamos liberando o suporte de visualização para a criptografia de disco do SSD local no Azure Machine Learning computação. Gere um tíquete de suporte técnico para que sua assinatura seja exibida na lista de permissões para usar esse recurso.
    + Visualização pública de inferência de Azure Machine Learning lote. Azure Machine Learning inferência de lote tem como alvo grandes trabalhos de inferência que não diferenciam o tempo. A inferência de lote fornece o dimensionamento de computação de inferência econômica, com taxa de transferência incomparável para aplicativos assíncronos. Ele é otimizado para inferência de alta taxa de transferência, incêndio e esqueci sobre grandes coleções de dados.
    + [**azureml-contrib-DataSet**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
        + Funcionalidades habilitadas para DataSet rotulado
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

+ **Correções de bugs e melhorias**
  + **azure-cli-ml**
    + A CLI agora dá suporte ao empacotamento de modelo.
    + Adicionada a CLI de DataSet. Para obter mais informações: `az ml dataset --help`
    + Adicionado suporte para implantação e empacotamento de modelos com suporte (ONNX, scikit-Learn e TensorFlow) sem uma instância InferenceConfig.
    + Foi adicionado o sinalizador de substituição para a implantação do serviço (ACI e AKS) no SDK e na CLI. Se fornecido, substituirá o serviço existente se o serviço com o nome já existir. Se o serviço não existir, o criará um novo serviço.
    + Os modelos podem ser registrados com duas novas estruturas, Onnx e Tensorflow. -O registro de modelo aceita dados de entrada de exemplo, dados de saída de exemplo e configuração de recurso para o modelo.
  + **azureml-automl-Core**
    + O treinamento de uma iteração seria executado em um processo filho somente quando as restrições de tempo de execução estiverem sendo definidas.
    + Adicione um guarda-costas para as tarefas de previsão, para verificar se um max_horizon especificado causará ou não um problema de memória na máquina dada. Se for, uma mensagem Guardrail será exibida.
    + Suporte adicionado para frequências complexas, como 2 anos e 1 mês. -Abrangente mensagem de erro adicionada se a frequência não puder ser determinada.
    + Adicionar azureml-padrões para o Conda env gerado automaticamente para resolver a falha de implantação do modelo
    + Permitir que os dados intermediários no pipeline de Azure Machine Learning sejam convertidos no conjunto de dados tabular e usados em `AutoMLStep`.
    + Implementada atualização de finalidade de coluna para streaming.
    + Implementada atualização de parâmetro do transformador para imputer e HashOneHotEncoder para streaming.
    + Adicionou o tamanho de dados atual e o tamanho mínimo de dados exigido para as mensagens de erro de validação.
    + Atualizado o tamanho mínimo de dados necessários para validação cruzada para garantir um mínimo de dois exemplos em cada dobra de validação.
  + **azureml-CLI-comum**
    + A CLI agora dá suporte ao empacotamento de modelo.
    + Os modelos podem ser registrados com duas novas estruturas, Onnx e Tensorflow.
    + O registro de modelo aceita dados de entrada de exemplo, dados de saída de exemplo e configuração de recurso para o modelo.
  + **azureml-contrib-GBDT**
    + Corrigido o canal de liberação para o bloco de anotações
    + Foi adicionado um aviso para o destino de computação não AmlCompute para o qual não há suporte
    + Foi adicionado um estimador de LightGMB para o pacote azureml-contrib-GBDT
  + [**azureml-núcleo**](https://docs.microsoft.com/python/api/azureml-core)
    + A CLI agora dá suporte ao empacotamento de modelo.
    + Adicionar aviso de reprovação para APIs de conjunto de Consulte aviso de alteração da API do conjunto de notícias em https://aka.ms/tabular-dataset.
    + Alterar [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) para devolver o nome e a versão do registo se o conjunto de dados estiver registado.
    + Corrija um bug de que ScriptRunConfig com DataSet as argument não pode ser usado repetidamente para enviar a execução do experimento.
    + Os conjuntos de dados recuperados durante uma corrida serão rastreados e podem ser vistos na página de detalhes da execução ou ligando [para`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) após a execução estar completa.
    + Permitir que os dados intermediários no pipeline de Azure Machine Learning sejam convertidos no conjunto de dados tabular e usados em [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep).
    + Adicionado suporte para implantação e empacotamento de modelos com suporte (ONNX, scikit-Learn e TensorFlow) sem uma instância InferenceConfig.
    + Foi adicionado o sinalizador de substituição para a implantação do serviço (ACI e AKS) no SDK e na CLI. Se fornecido, substituirá o serviço existente se o serviço com o nome já existir. Se o serviço não existir, o criará um novo serviço.
    +  Os modelos podem ser registrados com duas novas estruturas, Onnx e Tensorflow. O registro de modelo aceita dados de entrada de exemplo, dados de saída de exemplo e configuração de recurso para o modelo.
    + Adição de novo armazenamento de dados para o Azure Database para MySQL. Exemplo adicionado para usar o banco de dados do Azure para MySQL no DataTransferStep em pipelines de Azure Machine Learning.
    + Funcionalidade adicionada para adicionar e remover marcas de experimentos com funcionalidade adicionada para remover marcas de execuções
    + Foi adicionado o sinalizador de substituição para a implantação do serviço (ACI e AKS) no SDK e na CLI. Se fornecido, substituirá o serviço existente se o serviço com o nome já existir. Se o serviço não existir, o criará um novo serviço.
  + [**azureml-descompasso**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + Movido de `azureml-contrib-datadrift` para `azureml-datadrift`
    + Adicionado suporte para monitoramento de conjuntos de data de série temporal para descompasso e outras medidas estatísticas
    + Novos métodos `create_from_model()` e `create_from_dataset()` para a aula [de`DataDriftDetector`.](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) O método `create()` será preterido.
    + Ajustes nas visualizações no Python e na interface do usuário no Azure Machine Learning Studio.
    + Suporte a agendamento de monitor semanal e mensal, além de diários para monitores de conjuntos de conjunto de DataSet.
    + Suporte a aterramento de métricas de monitor de dados para analisar dados históricos de monitores de conjunto de dados.
    + Várias correções de bugs
  + [**azureml-pipeline-Core**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + o azureml-dataprep não é mais necessário para enviar uma execução de pipeline de Azure Machine Learning do arquivo de pipeline `yaml`.
  + [**azureml-Train-automl**](/python/api/azureml-train-automl-runtime/)
    + Adicionar azureml-padrões para o Conda env gerado automaticamente para resolver a falha de implantação do modelo
    + O treinamento remoto do AutoML agora inclui os padrões do azureml para permitir a reutilização de Training env para inferência.
  + **azureml-train-core**
    + Adicionado suporte a PyTorch 1,3 no estimador de [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch)

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Interface visual (visualização)

+ A interface visual do Azure Machine Learning (versão prévia) foi revisada para ser executada em [pipelines Azure Machine Learning](concept-ml-pipelines.md). Os pipelines (anteriormente conhecidos como experimentos) criados na interface visual agora estão totalmente integrados com a experiência de Azure Machine Learning principal.
  + Experiência de gerenciamento unificada com ativos do SDK
  + Controle de versão e acompanhamento de modelos de interface visual, pipelines e pontos de extremidade
  + IU reprojetada
  + Implantação de inferência de lote adicionada
  + Suporte ao Azure kubernetes Service (AKS) adicionado para os destinos de computação de inferência
  + Fluxo de trabalho de criação do pipeline de etapa do novo Python
  + Nova [página de aterrissagem](https://ml.azure.com) para ferramentas de criação Visual

+ **Novos módulos**
  + Aplicar operação matemática
  + Aplicar transformação SQL
  + Valores de clipe
  + Resumir dados
  + Importar do banco de dados SQL

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK para Python v 1.0.69

+ **Correções de bugs e melhorias**
  + **azureml-automl-Core**
    + Limitação de explicações de modelo para a melhor execução em vez de calcular explicações para cada execução. Fazendo esse comportamento mudar para local, remoto e ADB.
    + Suporte adicionado para explicações de modelo sob demanda para a interface do usuário
    + Adicionou psutil como uma dependência de `automl` e incluiu psutil como uma dependência Conda no amlcompute.
    + Corrigido o problema com tamanhos de janela lentos e contínuos nos conjuntos de dados de previsão, algumas séries que podem causar erros de algebra linear
      + Impressão adicional adicionada para os parâmetros que são determinados de heurística nas execuções de previsão.
  + **azureml-contrib-descompasso**
    + Proteção adicionada ao criar métricas de saída se o descompasso de nível de conjunto de resultados não estiver na primeira seção.
  + **azureml-contrib-interpret**
    + o pacote azureml-contrib-explique-Model foi renomeado para azureml-contrib-interpret
  + **azureml-core**
    + API adicionada para cancelar o registro de conjuntos de os. `dataset.unregister_all_versions()`
    + o pacote azureml-contrib-explique-Model foi renomeado para azureml-contrib-interpret.
  + **[azureml-núcleo](https://docs.microsoft.com/python/api/azureml-core)**
    + API adicionada para cancelar o registro de conjuntos de os. conjunto de dados. [unregister_all_versions()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Adicionada a API do conjunto de dados para verificar a hora da alteração do dado. `dataset.data_changed_time`.
    + Poder consumir `FileDataset` e `TabularDataset` como entradas para `PythonScriptStep`, `EstimatorStep`e `HyperDriveStep` no pipeline Azure Machine Learning
    + O desempenho do `FileDataset.mount` foi melhorado para pastas com um grande número de arquivos
    + Ser capaz de consumir [Filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset) e [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset) como entradas para [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)e [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) no pipeline de Azure Machine Learning.
    + Desempenho do filedataset. a [montagem ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none-) foi aprimorada para pastas com um grande número de arquivos
    + URL adicionada a recomendações de erro conhecidas em detalhes da execução.
    + Fixou um inseto em run.get_metrics onde os pedidos falhariam se uma corrida tivesse muitas crianças
    + Fixou um inseto em [run.get_metrics](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) onde os pedidos falhariam se uma corrida tivesse muitas crianças
    + Suporte adicionado para autenticação no cluster Arcadia.
    + Criar um objeto de teste Obtém ou cria o experimento no espaço de trabalho Azure Machine Learning para acompanhamento do histórico de execução. A ID do experimento e o tempo arquivado são populados no objeto experimento na criação. Exemplo: experiência = Experiência (espaço de trabalho, "Nova Experiência") experiment_id = experiment.id arquivo() e reativar() são funções que podem ser chamadas numa experiência para esconder e restaurar a experiência de ser mostrada no UX ou devolvida por padrão numa chamada para listar experiências. Se um novo experimento for criado com o mesmo nome de um experimento arquivado, você poderá renomear o teste arquivado ao reativar o passando um novo nome. Só pode haver um experimento ativo com um determinado nome. Exemplo: experiment1 = experimento (espaço de trabalho, "teste ativo") experiment1. Archive () # criar novo experimento ativo com o mesmo nome que o arquivado. experiment2. = Experimento (espaço de trabalho, experiência "Experiência Ativa") experimento1.reativar(new_name="Experiência Ativa Anterior") A lista de métodos estáticos() no Experimento pode ter um filtro de nome e filtro ViewType. Os valores do ViewType são "ATIVE_ONLY", "ARCHIVED_ONLY" e "ALL" Exemplo: archived_experiments = Experimento.list (workspace, view_type="ARCHIVED_ONLY") all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
    + Suporte ao uso do ambiente para implantação de modelo e atualização de serviço
  + **azureml-descompasso**
    + O atributo da classe DataDriftDector já não suporta argumentos opcionais 'with_details'. O atributo show só apresentará o coeficiente de descompasso de dados e a contribuição de descompasso de dados das colunas de recursos.
    + DataDriftDetector atribui alterações de comportamento 'get_output':
      + Parâmetro de entrada start_time, end_time são opcionais em vez de obrigatórios;
      + A entrada específica start_time e/ou end_time com um run_id específico na mesma invocação resultará em exceção de erro de valor por serem mutuamente exclusivas
      + Por entrada específica start_time e/ou end_time, apenas serão devolvidos os resultados das corridas programadas;
      + O parâmetro 'daily_latest_only' está deprecida.
    + Suporte à recuperação de saídas de descompasso de dados baseados em DataSet.
  + **azureml-explain-model**
    + Renomeia o pacote AzureML-explique-Model para o AzureML-interpret, mantendo o pacote antigo para compatibilidade com versões anteriores agora
    + correção de `automl` bug com explicações brutas definidas para tarefa de classificação em vez de regressão por padrão no download de ExplanationClient
    + Adicionar suporte para `ScoringExplainer` ser criado diretamente usando `MimicWrapper`
  + **azureml-pipeline-core**
    + Desempenho aprimorado para criação de pipeline grande
  + **azureml-train-core**
    + Adicionado suporte TensorFlow 2,0 no estimador TensorFlow
  + **azureml-train-automl**
    + Criar um objeto de [teste](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment) Obtém ou cria o experimento no espaço de trabalho Azure Machine Learning para acompanhamento do histórico de execução. A ID do experimento e o tempo arquivado são populados no objeto experimento na criação. Exemplo:

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [arquivo morto ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--) e [reativar ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) são funções que podem ser chamadas em um experimento para ocultar e restaurar o experimento de ser mostrado na UX ou retornado por padrão em uma chamada para experimentos de lista. Se um novo experimento for criado com o mesmo nome de um experimento arquivado, você poderá renomear o teste arquivado ao reativar o passando um novo nome. Só pode haver um experimento ativo com um determinado nome. Exemplo:

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        A lista de métodos estáticos [()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) no experimento pode usar um filtro de nome e um filtro ViewType. Os valores do ViewType são "ATIVE_ONLY", "ARCHIVED_ONLY" e "ALL". Exemplo:

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Suporte ao uso do ambiente para implantação de modelo e atualização de serviço.
  + **[azureml-descompasso](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + O atributo da classe [DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) já não suporta argumentos opcionais 'with_details'. O atributo show só apresentará o coeficiente de descompasso de dados e a contribuição de descompasso de dados das colunas de recursos.
    + Função DataDriftDetector [get_output]https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) alterações de comportamento:
      + Parâmetro de entrada start_time, end_time são opcionais em vez de obrigatórios;
      + A entrada específica start_time e/ou end_time com um run_id específico na mesma invocação resultará numa exceção de erro de valor por serem mutuamente exclusivas;
      + Por entrada específica start_time e/ou end_time, apenas serão devolvidos os resultados das corridas programadas;
      + O parâmetro 'daily_latest_only' está deprecida.
    + Suporte à recuperação de saídas de descompasso de dados baseados em DataSet.
  + **[azureml-explicação-modelo](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Renomeia o pacote AzureML-explique-Model para o AzureML-interpret, mantendo o pacote antigo para compatibilidade com versões anteriores por enquanto.
    + correção do bug AutoML com explicações brutas definidas para tarefa de classificação em vez de regressão por padrão no download de ExplanationClient.
    + Adicionar suporte para [ScoringExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.scoring.scoring_explainer.scoringexplainer) a ser criado diretamente usando [MimicWrapper](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic_wrapper.mimicwrapper)
  + **[azureml-pipeline-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Desempenho aprimorado para criação de pipeline grande.
  + **[azureml-Train-Core](https://docs.microsoft.com/python/api/azureml-train-core)**
    + Adicionado suporte do TensorFlow 2,0 no estimador do [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow) .
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + A execução do pai não será mais possível quando a iteração da instalação falhar, pois a orquestração já cuida dela.
    + Adicionado suporte local-Docker e Conda local para experimentos de AutoML
    + Adicionado suporte local-Docker e Conda local para experimentos de AutoML.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nova experiência na Web (versão prévia) para espaços de trabalho Azure Machine Learning

A guia experimento no [novo portal de espaço de trabalho](https://ml.azure.com) foi atualizada para que os cientistas de dados possam monitorar experimentos de forma mais eficaz. Você pode explorar os seguintes recursos:
+ Teste os metadados para filtrar e classificar facilmente sua lista de experimentos
+ Páginas de detalhes do experimento simplificado e de alto desempenho que permitem visualizar e comparar suas execuções
+ Novo design para executar páginas de detalhes para entender e monitorar as execuções de treinamento

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK para Python v 1.0.65

  + **Novos recursos**
    + Adicionados ambientes organizados. Esses ambientes foram pré-configurados com bibliotecas para tarefas comuns de aprendizado de máquina e foram previamente compilados e armazenados em cache como imagens do Docker para uma execução mais rápida. Eles aparecem por padrão na lista de ambiente do espaço de trabalho, com o prefixo "AzureML".
    + Adicionados ambientes organizados. Esses ambientes foram pré-configurados com bibliotecas para tarefas comuns de aprendizado de máquina e foram previamente compilados e armazenados em cache como imagens do Docker para uma execução mais rápida. Eles aparecem por padrão na lista de ambiente do [espaço de trabalho](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29), com o prefixo "AzureML".

  + **azureml-train-automl**
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + Adicionado o suporte de conversão de ONNX para o ADB e o HDI

+ **Recursos de visualização**
  + **azureml-train-automl**
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + BERT e BiLSTM com suporte como Text featurizer (somente visualização)
    + Personalização de personalização com suporte para parâmetros de finalidade de coluna e transformador (somente visualização)
    + Explicações brutas com suporte quando o usuário habilita a explicação do modelo durante o treinamento (somente visualização)
    + Adição de Prophet para `timeseries` previsão como um pipeline treinado (somente visualização)

  + **azureml-contrib-descompasso**
    + Pacotes realocados de azureml-contrib-datalogoff para azureml-datalogoff; o pacote de `contrib` será removido em uma versão futura

+ **Correções de bugs e melhorias**
  + **azureml-automl-Core**
    + Introduziu o FeaturizationConfig em AutoMLConfig e AutoMLBaseSettings
    + Introduziu o FeaturizationConfig em [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) e AutoMLBaseSettings
      + Substituir a finalidade da coluna para personalização com a coluna especificada e o tipo de recurso
      + Substituir parâmetros do transformador
    + Mensagem de depreciação acrescida para explain_model() e retrieve_model_explanations()
    + Prophet adicionado como um pipeline treinado (somente visualização)
    + Mensagem de depreciação adicional para explain_model() e retrieve_model_explanations().
    + Prophet adicionado como um pipeline treinado (somente visualização).
    + Suporte adicionado para detecção automática de atraso de destino, tamanho de janela sem interrupção e horizonte máximo. Se um dos target_lags, target_rolling_window_size ou max_horizon estiver definido para 'auto', a heurística será aplicada para estimar o valor do parâmetro correspondente com base em dados de formação.
    + Correção de previsão no caso em que o conjunto de dados contém uma coluna de refinamento, essa granulação é de um tipo numérico e há uma lacuna entre o treinamento e o conjunto de teste
    + Correção da mensagem de erro sobre o índice duplicado na execução remota em tarefas de previsão
    + Correção de previsão no caso em que o conjunto de dados contém uma coluna de refinamento, essa granulação é de um tipo numérico e há uma lacuna entre o treinamento e o conjunto de teste.
    + Correção da mensagem de erro sobre o índice duplicado na execução remota em tarefas de previsão.
    + Adicionou um Guardrail para verificar se um conjunto de um DataSet está desequilibrado ou não. Se for, uma mensagem Guardrail seria gravada no console.
  + **azureml-core**
    + Foi adicionada a capacidade de recuperar a URL da SAS para o modelo no armazenamento por meio do objeto de modelo. Ex: modelo.get_sas_url()
    + Introduzir `run.get_details()['datasets']` para obter conjuntos de dados associados à execução submetida
    + Adicione a API `Dataset.Tabular.from_json_lines_files` para criar um Conjunto TabularDataa a partir de ficheiros JSON Lines. Para saber mais sobre esses dados tabulares em arquivos de linhas JSON em TabularDataset, visite https://aka.ms/azureml-data para obter a documentação.
    + Adicionar amiúr adicional de campos de tamanho VM (Disco OS, número de GPUs) à função supported_vmsizes ()
    + Acrescentou campos adicionais à função list_nodes () para mostrar a execução, o IP privado e público, o porto, etc.
    + Capacidade de especificar um novo campo durante o fornecimento de clusters -- remotelogin_port_public_access que pode ser configurado para ativado ou desativado dependendo se você gostaria de deixar a porta SSH aberta ou fechada no momento da criação do cluster. Se você não especificá-lo, o serviço abrirá de forma inteligente ou fechará a porta dependendo se você estiver implantando o cluster dentro de uma VNet.
  + **azureml-explain-model**
  + **[azureml-núcleo](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + Foi adicionada a capacidade de recuperar a URL da SAS para o modelo no armazenamento por meio do objeto de modelo. Ex: modelo. [get_sas_url()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Introduza a corrida. [get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)['datasets'] para obter conjuntos de dados associados à execução submetida
    + Adicione `Dataset.Tabular`API. [from_json_lines_files()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) criar um Conjunto TabularDataset a partir de ficheiros JSON Lines. Para saber mais sobre esses dados tabulares em arquivos de linhas JSON em TabularDataset, visite https://aka.ms/azureml-data para obter a documentação.
    + Adicionados campos de tamanho VM adicionais (Disco OS, número de GPUs) à função [supported_vmsizes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + Acrescentou campos adicionais à função [list_nodes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) para mostrar a execução, o IP privado e público, o porto, etc.
    + Capacidade de especificar um novo campo durante [o fornecimento](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) de cluster`--remotelogin_port_public_access` que pode ser configurado para ativado ou desativado dependendo se você gostaria de deixar a porta SSH aberta ou fechada no momento da criação do cluster. Se você não especificá-lo, o serviço abrirá de forma inteligente ou fechará a porta dependendo se você estiver implantando o cluster dentro de uma VNet.
  + **[azureml-explicação-modelo](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Documentação aprimorada para obter as saídas de explicação no cenário de classificação.
    + Adicionada a capacidade de carregar os valores y previstos na explicação para os exemplos de avaliação. O desbloqueia visualizações mais úteis.
    + Adicionada a propriedade de explicador para MimicWrapper para habilitar a obtenção do MimicExplainer subjacente.
  + **azureml-pipeline-core**
    + Bloco de anotações adicionado para descrever o módulo, ModuleVersion e ModuleStep
  + **azureml-pipeline-steps**
    + Adição de RScriptStep para dar suporte ao script R executado via pipeline AML.
    + Parâmetros de metadados corrigidos analisando em AzureBatchStep que estava causando a mensagem de erro "a atribuição para o parâmetro SubscriptionId não foi especificada".
  + **azureml-train-automl**
    + Suporte training_data, validation_data, label_column_name, weight_column_name como formato de entrada de dados
    + Mensagem de depreciação acrescida para explain_model() e retrieve_model_explanations()
  + **[azureml-pipeline-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Adicionado um [bloco de anotações](https://aka.ms/pl-modulestep) para descrever [Module](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class)), [ModuleVersion](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.moduleversion) e [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep).
  + **[azureml-pipeline-etapas](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + Adição de [RScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) para dar suporte ao script R executado via pipeline AML.
    + Parâmetros de metadados corrigidos analisando em [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) que estava causando a mensagem de erro "a atribuição para o parâmetro SubscriptionId não foi especificada".
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + Suportado training_data, validation_data, label_column_name, weight_column_name como formato de entrada de dados.
    + Mensagem de depreciação acrescida para [explain_model()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) e [retrieve_model_explanations()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-).


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK para Python v 1.0.62

+ **Novos recursos**
  + Introduziu a característica `timeseries` em TabularDataset. Essa característica permite uma fácil filtragem de carimbo de data/hora nos dados de uma TabularDataset, como a obtenção de todos os dados entre um intervalo de tempo ou os dados mais recentes. Para saber mais sobre essa característica `timeseries` no TabularDataset, visite https://aka.ms/azureml-data para obter a documentação ou https://aka.ms/azureml-tsd-notebook para obter um exemplo de bloco de anotações.
  + Habilitado o treinamento com TabularDataset e filedataset. Visite https://aka.ms/dataset-tutorial para obter um exemplo de bloco de anotações.

  + **azureml-train-core**
    + Adição de suporte a `Nccl` e `Gloo` no estimador de PyTorch

+ **Correções de bugs e melhorias**
  + **azureml-automl-Core**
    + Deprectei a definição autoML 'lag_length' e o LaggingTransformer.
    + Correção correta da validação de dados de entrada se elas forem especificadas em um formato de Dataflow
    + Modificou o fit_pipeline.py para gerar o gráfico json e carregar para artefactos.
    + Renderizado o grafo em `userrun` usando `Cytoscape`.
  + **azureml-core**
    + Revisitoumos o tratamento de exceção no código ADB e fazemos alterações de acordo com o tratamento de erro por nova
    + Autenticação MSI automática adicionada para VMs do notebook.
    + Corrige o bug em que modelos corrompidos ou vazios podem ser carregados devido a tentativas com falha.
    + Fixação do bug sempre que o `DataReference` nome se mude quando o modo `DataReference` muda (por exemplo, quando se liga para `as_upload`, `as_download`ou `as_mount`).
    + Torne `mount_point` e `target_path` opcionais para `FileDataset.mount` e `FileDataset.download`.
    + A exceção de que a coluna de carimbo de data/hora não pode ser encontrada será lançada se a API relacionada à hora de série for chamada sem a coluna de carimbo de data/hora correta atribuída ou as colunas de carimbo de data/hora
    + As colunas de série de tempo devem ser atribuídas com a coluna cujo tipo é Date, caso contrário a exceção é esperada
    + As colunas de séries de tempo que designam a API 'with_timestamp_columns' podem ter nenhum nome de coluna de carimbo sinuoso/grosso, que irá limpar as colunas de carimbos de tempo previamente atribuídas.
    + A exceção será descartada quando for largada a coluna de carimbos de grãos grossos ou de grãos finos, indicando ao utilizador que a queda pode ser feita após excluir a coluna de carimbos de tempo na lista de queda ou ligar para with_time_stamp com nenhum valor para libertar o timestamp colunas
    + A exceção será descartada quando não estiver incluído grãos grossos ou colunas de carimbos de grãos finos na lista de colunas com indicação para o utilizador de que a manutenção pode ser feita após incluir a coluna de carimbos de tempo na lista de colunas ou chamar with_time_stamp com nenhuma valor para libertar colunas de carimbos de tempo.
    + Adicionado log para o tamanho de um modelo registrado.
  + **azureml-explain-model**
    + Correção de aviso impresso no console quando o pacote do Python "empacotamento" não está instalado: "usando a versão mais antiga do que com suporte do lightgbm, atualize para a versão superior à 2.2.1"
    + Corrigida a explicação do modelo de download com a fragmentação para obter explicações globais com muitos recursos
    + O explicador de imitação corrigida não contém exemplos de inicialização na explicação de saída
    + Corrigido o erro imutável nas propriedades definidas ao carregar com o cliente de explicação usando dois tipos diferentes de modelos
    + Adicione um get_raw param para pontuar explicador .explicar () para que um explicador de pontuação possa devolver valores projetados e brutos.
  + **azureml-train-automl**
    + Introduziu as APIs públicas do AutoML para obter explicações de suporte de `automl` explicar o SDK – a maneira mais recente de dar suporte a explicações de AutoML ao desacoplar AutoML personalização e explicar o suporte à explicação bruta integrada ao SDK do azureml explicar SDK para modelos de AutoML.
    + Removendo o azureml-padrões de ambientes de treinamento remoto.
    + Local do repositório de cache padrão alterado de FileCacheStore com base em um para AzureFileCacheStore um para AutoML no caminho de código Azure Databricks.
    + Correção correta da validação de dados de entrada se elas forem especificadas em um formato de Dataflow
  + **azureml-train-core**
    + Revertido source_directory_data_store depreciação.
    + Foi adicionada a capacidade de substituir as versões de pacote instaladas do azureml.
    + Suporte de estivador adicionado no `environment_definition` parâmetro em estimadores.
    + Parâmetros de treinamento distribuídos simplificados em estimadores.

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nova experiência na Web (versão prévia) para espaços de trabalho Azure Machine Learning
A nova experiência na Web permite que cientistas de dados e engenheiros de dados concluam seu ciclo de vida de aprendizado de máquina de ponta a ponta de preparando e visualizando dados para treinar e implantar modelos em um único local.

![Interface do usuário do Azure Machine Learning Workspace (versão prévia)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Principais recursos:**

Usando essa nova interface de Azure Machine Learning, agora você pode:
+ Gerencie seus notebooks ou conecte-se ao Jupyter
+ [Executar experimentos de ML automatizados](tutorial-first-experiment-automated-ml.md)
+ [Criar conjuntos de itens de arquivos locais, repositórios de armazenamento & arquivos da Web](how-to-create-register-datasets.md)
+ Explorar & preparar conjuntos de valores para a criação do modelo
+ Monitorar descompasso de dados para seus modelos
+ Exibir os recursos recentes de um painel

No momento desta versão, há suporte para os seguintes navegadores: Chrome, Firefox, Safari e Microsoft Edge Preview.

**Questões conhecidas:**

1. Atualize seu navegador se você vir "algo deu errado! Erro ao carregar arquivos de parte "quando a implantação está em andamento.

1. Não é possível excluir ou renomear o arquivo em arquivos e blocos de anotações. Durante a visualização pública, você pode usar a interface do usuário do Jupyter ou terminal na VM do notebook para executar operações de atualização de arquivo. Como é um sistema de arquivos de rede montado, todas as alterações feitas na VM do notebook são refletidas imediatamente no espaço de trabalho do bloco de anotações.

1. Para SSH na VM do bloco de anotações:
   1. Localize as chaves SSH que foram criadas durante a configuração da VM. Ou localize as chaves no espaço de trabalho Azure Machine Learning > guia abrir computação > localizar a VM do bloco de anotações na lista > Abra suas propriedades: copie as chaves da caixa de diálogo.
   1. Importe as chaves SSH públicas e privadas para o computador local.
   1. Use-os para SSH na VM do notebook.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK para Python v 1.0.60

+ **Novos recursos**
  + Introduziu filedataset, que faz referência a um ou vários arquivos em seus armazenamentos de dados ou URLs públicas. Os arquivos podem ser de qualquer formato. O filedataset fornece a capacidade de baixar ou montar os arquivos em sua computação. Para saber mais sobre filedataset, visite https://aka.ms/file-dataset.
  + Adicionado suporte de YAML de pipeline para a etapa PythonScript, etapa adla, databricks etapa, DataTransferStep e etapa AzureBatch

+ **Correções de bugs e melhorias**
  + **azureml-automl-Core**
    + AutoArima agora é um pipeline sugerido para visualização apenas.
    + Relatório de erros aprimorado para previsão.
    + Melhorou o registro em log usando exceções personalizadas em vez de genéricas nas tarefas de previsão.
    + Removeu o controlo max_concurrent_iterations ser inferior ao número total de iterações.
    + Os modelos AutoML agora retornam AutoMLExceptions
    + Esta versão melhora o desempenho de execução das execuções locais do Machine Learning automatizado.
  + **azureml-core**
    + Introduza dataset.get_all (espaço de trabalho), que devolve um dicionário de objetos `TabularDataset` e `FileDataset` com o nome de registo.

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Introduza `parition_format` como argumento para `Dataset.Tabular.from_delimited_files` e `Dataset.Tabular.from_parquet.files`. As informações de partição de cada caminho de dados serão extraídas em colunas com base no formato especificado. '{column_name}' cria coluna de cordas, e '{column_name:yyyy/MM/dd/HH/mm/ss}' cria coluna de data, onde 'yyyy', 'MM', 'dd', 'HH', 'mm' e 'ss' são utilizados para extrair ano, mês, dia, hora, minuto e segundo para o tipo de data. O partition_format deve partir da posição da primeira chave de partição até ao fim do caminho do ficheiro. Por exemplo, dado o caminho '. /USA/2019/01/01/data.csv' onde a partição é por país e tempo, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' cria coluna de cordas 'País' com valor 'EUA' e coluna de data 'PartitionDate' com valor '2019-01-01'.
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + Introduza `partition_format` como argumento para `Dataset.Tabular.from_delimited_files` e `Dataset.Tabular.from_parquet.files`. As informações de partição de cada caminho de dados serão extraídas em colunas com base no formato especificado. '{column_name}' cria coluna de cordas, e '{column_name:yyyy/MM/dd/HH/mm/ss}' cria coluna de data, onde 'yyyy', 'MM', 'dd', 'HH', 'mm' e 'ss' são utilizados para extrair ano, mês, dia, hora, minuto e segundo para o tipo de data. O partition_format deve partir da posição da primeira chave de partição até ao fim do caminho do ficheiro. Por exemplo, dado o caminho '. /USA/2019/01/01/data.csv' onde a partição é por país e tempo, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' cria coluna de cordas 'País' com valor 'EUA' e coluna de data 'PartitionDate' com valor '2019-01-01'.
    + `to_csv_files` e `to_parquet_files` métodos foram adicionados à `TabularDataset`. Esses métodos habilitam a conversão entre um `TabularDataset` e um `FileDataset` convertendo os dados em arquivos do formato especificado.
    + Faça logon automaticamente no registro da imagem base ao salvar um Dockerfile gerado por Model. Package ().
    + «gpu_support» já não é necessário; A AML deteta e utiliza automaticamente a extensão do estivador nvidia quando está disponível. Ele será removido em uma versão futura.
    + Adicionado suporte para criar, atualizar e usar PipelineDrafts.
    + Esta versão melhora o desempenho de execução das execuções locais do Machine Learning automatizado.
    + Os usuários podem consultar métricas do histórico de execuções por nome.
    + Melhorou o registro em log usando exceções personalizadas em vez de genéricas nas tarefas de previsão.
  + **azureml-explain-model**
    + Adicione feature_maps parâmetro ao novo MimicWrapper, permitindo que os utilizadores obtem explicações de funcionalidades cruas.
    + Os uploads do conjunto de dados estão agora desligados por padrão para upload de explicações, e podem ser reativados com upload_datasets=True
    + Adicioneos parâmetros de filtragem "is_law" à lista de explicações e às funções de descarregamento.
    + Adiciona método `get_raw_explanation(feature_maps)` a objetos de explicação globais e locais.
    + Adicionada a verificação de versão para lightgbm com aviso impresso, se abaixo da versão com suporte
    + Uso otimizado de memória no envio em lote de explicações
    + Os modelos AutoML agora retornam AutoMLExceptions
  + **azureml-pipeline-core**
    + Suporte adicionado para criar, atualizar e usar PipelineDrafts-pode ser usado para manter definições de pipeline mutáveis e usá-las interativamente para executar
  + **azureml-train-automl**
    + Recurso criado para instalar versões específicas do pytorch v 1.1.0 com capacidade de GPU, :::no-loc text="cuda"::: Toolkit 9,0, pytorch-transformadores, que é necessário para habilitar o BERT/XLNet no ambiente de tempo de execução do Python remoto.
  + **azureml-train-core**
    + Falha antecipada de alguns erros de definição de espaço de hiperparâmetro diretamente no SDK, em vez de no lado do servidor.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>SDK v 1.1.14 do Azure Machine Learning data Prep
+ **Correções de bugs e melhorias**
  + Habilitado gravação em ADLS/ADLSGen2 usando caminho e credenciais brutos.
  + Fixou um inseto que fez com que `include_path=True` não funcionasse durante `read_parquet`.
  + Falha de `to_pandas_dataframe()` fixa causada pela exceção "Valor imobiliário inválido: hostSecret".
  + Correção de um bug em que os arquivos não puderam ser lidos em DBFS no modo Spark.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK para Python v 1.0.57
+ **Novos recursos**
  + `TabularDataset` habilitados para serem consumidos pelo AutomatedML. Para saber mais sobre `TabularDataset`, visite https://aka.ms/azureml/howto/createdatasets.

+ **Correções de bugs e melhorias**
  + **automl-Client-Core-nativeClient**
    + Fixao erro, levantado quando as etiquetas de treino e/ou validação (y e y_valid) são fornecidas sob a forma de quadro de dados pandas, mas não como matriz entorpecida.
    + Interface atualizada para criar um `RawDataContext` apenas para exigir os dados e o objeto `AutoMLBaseSettings`.
    +  Permitir que os usuários do AutoML descartam a série de treinamento que não são longas o suficiente ao prever. -Permitir que usuários do AutoML removam granulares do conjunto de teste que não existe no conjunto de treinamento ao prever.
  + **azure-cli-ml**
    + Agora você pode atualizar o certificado SSL para o ponto de extremidade de Pontuação implantado no cluster AKS para o Microsoft generated e o certificado do cliente.
  + **azureml-automl-Core**
    + Corrigido um problema em AutoML em que as linhas com rótulos ausentes não foram removidas corretamente.
    + Aprimoramento do log de erros no AutoML; as mensagens de erro completas agora serão sempre gravadas no arquivo de log.
    + O AutoML atualizou sua fixação de pacote para incluir `azureml-defaults`, `azureml-explain-model`e `azureml-dataprep`. O AutoML não avisará mais sobre incompatibilidades de pacotes (exceto para o pacote de `azureml-train-automl`).
    + Corrigido um problema em `timeseries` em que as divisões de CV são de tamanho diferente, causando falha no cálculo do compartimento.
    + Ao executar a iteração de Ensemble para o tipo de treinamento de validação cruzada, se acabarmos tendo problemas para baixar os modelos treinados em todo o conjunto de ativos, estamos tendo uma inconsistência entre os pesos do modelo e os modelos que estavam sendo inseridos na votação Ensemble.
    + Fixao erro, levantado quando as etiquetas de treino e/ou validação (y e y_valid) são fornecidas sob a forma de quadro de dados pandas, mas não como matriz entorpecida.
    + Corrigido o problema com as tarefas de previsão quando nenhuma foi encontrada nas colunas booleanas das tabelas de entrada.
    + Permitir que os usuários do AutoML descartam a série de treinamento que não são longas o suficiente ao prever. -Permitir que usuários do AutoML removam granulares do conjunto de teste que não existe no conjunto de treinamento ao prever.
  + **azureml-core**
    + Problema fixo com blob_cache_timeout pedido de parâmetro.
    + Adicionados tipos de exceção de ajuste externo e de transformação a erros do sistema.
    + Suporte adicionado para segredos de Key Vault para execuções remotas. Adicione uma classe azureml. Core. keyvault. keyvault para adicionar, obter e listar segredos do keyvault associado ao seu espaço de trabalho. As operações com suporte são:
      + azureml.core.workspace.workspace.get_default_keyvault()
      + azureml.core.keyvault.keyvault.set_secret (nome, valor)
      + azureml.core.keyvault.keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.keyvault.get_secret (nome)
      + azureml.core.keyvault.keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.keyvault.list_secrets()
    + Métodos adicionais para obter o keyvault padrão e obter segredos durante a execução remota:
      + azureml.core.workspace.workspace.get_default_keyvault()
      + azureml.core.run.run.get_secret (nome)
      + azureml.core.run.run.get_secrets(secrets_list)
    + Foram adicionados parâmetros adicionais de substituição para o comando de CLI enviar-hyperdrive.
    + Melhorar a confiabilidade das chamadas de API, expandindo repetições para exceções de biblioteca de solicitações comuns.
    + Adicione suporte para envio de execuções de uma execução enviada.
    + Corrigido o problema de token SAS de expiração no arquivo de monitoramento, que fez com que os arquivos pararam de ser carregados depois que seu token inicial tivesse expirado.
    + Suporte à importação de arquivos HTTP CSV/TSV no SDK do Python do conjunto de
    + O método Workspace. Setup () foi substituído. A mensagem de aviso mostrada aos utilizadores sugere a utilização de criar() ou obter ()/from_config() () em vez disso.
    + Ambiente adicionado.add_private_pip_wheel(), que permite carregar pacotes privados de pitão personalizados `whl`para o espaço de trabalho e usá-los de forma segura para construir/materializar o ambiente.
    + Agora você pode atualizar o certificado SSL para o ponto de extremidade de Pontuação implantado no cluster AKS para o Microsoft generated e o certificado do cliente.
  + **azureml-explain-model**
    + Parâmetro adicionado para adicionar uma ID de modelo a explicações no carregamento.
    + Acrescentou `is_raw` marcação de explicações na memória e upload.
    + Adicionado suporte e testes do pytorch para o pacote azureml-explique-Model.
  + **azureml-opendatasets**
    + Suporte para detectar e registrar em log o ambiente de teste automático.
    + Classes adicionadas para nos colocar população por município e zip.
  + **azureml-pipeline-core**
    + Adicionada a propriedade de rótulo às definições de porta de entrada e saída.
  + **azureml – telemetria**
    + Correção de configuração de telemetria incorreta.
  + **azureml-train-automl**
    + Correção do bug em que ocorreu uma falha na instalação, o erro não estava sendo registrado no campo "erros" para a execução da instalação e, portanto, não foi armazenado em "erros" da execução do pai.
    + Corrigido um problema em AutoML em que as linhas com rótulos ausentes não foram removidas corretamente.
    + Permitir que os usuários do AutoML descartam a série de treinamento que não são longas o suficiente ao prever.
    + Permita que os usuários do AutoML removam granulares do conjunto de teste que não existem no conjunto de treinamento ao prever.
    + Agora, o AutoMLStep passa pelo `automl` config para o back-end para evitar problemas de alterações ou adições de novos parâmetros de configuração.
    + O AutoML data Guardrail agora está em visualização pública. O usuário verá um relatório de guardrail de dados (para tarefas de classificação/regressão) após o treinamento e também poderá acessá-lo por meio da API do SDK.
  + **azureml-train-core**
    + Adicionado suporte do Torch 1,2 no estimador do PyTorch.
  + **azureml-widgets**
    + Gráficos de matriz de confusão aprimorados para treinamento de classificação.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>SDK v 1.1.12 do Azure Machine Learning data Prep
+ **Novos recursos**
  + As listas de cordas podem agora ser passadas como contributo para `read_*` métodos.

+ **Correções de bugs e melhorias**
  + O desempenho do `read_parquet` foi significativamente melhorado ao correr em Spark.
  + Corrigiu uma questão em que `column_type_builder` falhou no caso de uma única coluna com formatos de data ambígua.

### <a name="azure-portal"></a>Portal do Azure
+ **Recurso de visualização**
  + O streaming do arquivo de log e saída agora está disponível para páginas de detalhes de execução. Os arquivos transmitirão atualizações em tempo real quando a alternância de visualização estiver ativada.
  + A capacidade de definir a cota em um nível de espaço de trabalho é liberada na versão prévia. As cotas de AmlCompute são alocadas no nível da assinatura, mas agora podemos distribuir essa cota entre espaços de trabalho e alocá-la para compartilhamento e governança justas. Basta clicar na folha **usos + cotas** na barra de navegação à esquerda do espaço de trabalho e selecionar a guia **configurar cotas** . Observe que você deve ser um administrador de assinatura para poder definir cotas no nível do espaço de trabalho, uma vez que essa é uma operação entre espaços de trabalho.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK para Python v 1.0.55

+ **Novos recursos**
  + A autenticação baseada em token agora tem suporte para as chamadas feitas para o ponto de extremidade de Pontuação implantado em AKS. Continuaremos a dar suporte à autenticação baseada em chave atual e os usuários podem usar um desses mecanismos de autenticação de cada vez.
  + Capacidade de registrar um armazenamento de BLOBs que está atrás da rede virtual (VNet) como um repositório de armazenamento.

+ **Correções de bugs e melhorias**
  + **azureml-automl-Core**
    + Corrige um bug em que o tamanho de validação para as divisões de CV é pequeno e resulta em gráficos de previsão inválidas versus reais para regressão e previsões.
    + O registro em log de tarefas de previsão nas execuções remotas é melhorado. agora, o usuário receberá uma mensagem de erro abrangente se a execução tiver falhado.
    + Correção de falhas de `Timeseries` se o sinalizador de pré-processamento for verdadeiro.
    + Foram feitas algumas mensagens de erro de validação de dados de previsão mais acionáveis.
    + Redução do consumo de memória das execuções de AutoML ao descartar e/ou carregar o carregamento lento de conjuntos de valores, especialmente entre as execuções de processo
  + **azureml-contrib-explicação-modelo**
    + Acrescentou model_task bandeira aos explicadores para permitir que o utilizador sobrepusesse a lógica de inferência automática padrão para o tipo de modelo
    + Alterações do widget: o instala automaticamente com o `contrib`, não há mais `nbextension` instalação/habilitação-explicação de suporte com apenas a importância global do recurso (por exemplo, permutal)
    + Alterações no painel: gráficos de caixas e plotagens violino além de `beeswarm` plotagem na página de resumo-reprocessamento muito mais rápido de `beeswarm` plotagem na alteração do controle deslizante ' top-k ' – mensagem útil explicando como as primeiras k são mensagens personalizáveis úteis em vez de gráficos quando os dados não são fornecidos
  + **azureml-core**
    + Adicionado o método Model. Package () para criar imagens do Docker e Dockerfiles que encapsulam modelos e suas dependências.
    + Os WebServices locais foram atualizados para aceitar o InferenceConfigs que contém objetos de ambiente.
    + Modelo Fixo.register() produzindo modelos inválidos quando '.' (para o atual diretório) é passado como parâmetro model_path.
    + Adicione Run.submit_child, a funcionalidade espelha Experiment.submit enquanto especifica a execução como o pai da execução da criança submetida.
    + Opções de configuração de suporte do Model.register em Run.register_model.
    + Capacidade de executar trabalhos JAR no cluster existente.
    + Agora a apoiar os parâmetros instance_pool_id e cluster_log_dbfs_path.
    + Adicionado suporte para usar um objeto de ambiente ao implantar um modelo em um WebService. O objeto de ambiente agora pode ser fornecido como parte do objeto InferenceConfig.
    + Adicionar mapeamento de appinsifht para novas regiões-centralus-oesteus-northcentralus
    + Adicionada documentação para todos os atributos em todas as classes de armazenamento de Datastore.
    + Adicionei blob_cache_timeout parâmetro para `Datastore.register_azure_blob_container`.
    + Adicione save_to_directory e métodos load_from_directory para azureml.core.environment.environment.
    + Foram adicionados os comandos "AZ ml Environment download" e "AZ ml Environment Register" à CLI.
    + Método de ambiente adicionado.add_private_pip_wheel.
  + **azureml-explain-model**
    + Adicionado controle de conjunto de alterações a explicações usando o serviço de conjunto de serviços (versão prévia).
    + Tamanho de lote padrão reduzido ao transmitir explicações globais de 10K para 100.
    + Adicione model_task bandeira aos explicadores para permitir que o utilizador sobrepor a lógica de inferência automática padrão para o tipo de modelo.
  + **azureml-mlflow**
    + Inseto fixo em mlflow.azureml.build_image onde os diretórios aninhados são ignorados.
  + **azureml-pipeline-steps**
    + Foi adicionada a capacidade de executar trabalhos JAR no cluster Azure Databricks existente.
    + Instance_pool_id de suporte adicionado e parâmetros cluster_log_dbfs_path para databricksStep passo.
    + Adicionado suporte para parâmetros de pipeline na etapa DatabricksStep.
  + **azureml-train-automl**
    + Adicionado `docstrings` para os arquivos relacionados ao Ensemble.
    + Docs atualizados para linguagem mais adequada para `max_cores_per_iteration` e `max_concurrent_iterations`
    + O registro em log de tarefas de previsão nas execuções remotas é melhorado. agora, o usuário receberá uma mensagem de erro abrangente se a execução tiver falhado.
    + Removido get_data do oleoduto `automlstep` caderno.
    + Iniciado o suporte `dataprep` no `automlstep`.

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>SDK v 1.1.10 do Azure Machine Learning data Prep

+ **Novos recursos**
  + Agora você pode solicitar para executar inspetores específicos (por exemplo, histograma, gráfico de dispersão, etc.) em colunas específicas.
  + Acrescentou um argumento paralelo à `append_columns`. Se for true, os dados serão carregados na memória, mas a execução será executada em paralelo; Se for false, a execução será transmitida, mas de thread único.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK para Python v 1.0.53

+ **Novos recursos**
  + O Machine Learning automatizado agora dá suporte a modelos de ONNX de treinamento no destino de computação remota
  + Azure Machine Learning agora fornece a capacidade de retomar o treinamento de uma execução anterior, um ponto de verificação ou arquivos de modelo.
    + Saiba como [usar os estimadores para retomar o treinamento de uma execução anterior](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Correções de bugs e melhorias**
  + **automl-Client-Core-nativeClient**
    + Corrigir o bug sobre os tipos de colunas perder após a transformação (bug vinculado);
    + Deixe y_query um tipo de objeto que contenha nenhum(s) no início (#459519).
  + **azure-cli-ml**
    + Os comandos da CLI "implantação de modelo" e "atualização de serviço" agora aceitam parâmetros, arquivos de configuração ou uma combinação dos dois. Parâmetros têm precedência sobre atributos em arquivos.
    + A descrição do modelo agora pode ser atualizada após o registro
  + **azureml-automl-Core**
    + Atualize a dependência NimbusML para a versão 1.2.0 (atual mais recente).
    + Adicionar suporte para NimbusML estimados & pipelines a serem usados em estimadores AutoML.
    + Corrigindo um bug no procedimento de seleção de Ensemble que estava aumentando desnecessariamente o Ensemble resultante, mesmo se as pontuações permaneceram constantes.
    + Habilite a reutilização de algumas featurizations em divisões de CV para tarefas de previsão. Isto acelera o tempo de execução da configuração executado por aproximadamente um fator de n_cross_validations para características caras como lags e janelas rolantes.
    + Resolver um problema se o tempo estiver fora do intervalo de tempo com suporte do pandas. Agora, geramos uma DataException se o tempo for menor que PD. Timestamp. min ou maior que PD. Timestamp. Max
    + A previsão agora permite diferentes frequências em conjuntos de treinamento e teste se eles puderem ser alinhados. Por exemplo, "começando trimestralmente em Janeiro" e em "a partir de outubro", a cada trimestre, pode ser alinhado.
    + A propriedade "Parameters" foi adicionada ao TimeSeriesTransformer.
    + Remova as classes de exceção antigas.
    + Nas tarefas de previsão, o parâmetro `target_lags` aceita agora um único valor inteiro ou uma lista de inteiros. Se o inteiro tiver sido fornecido, apenas uma latência será criada. Se uma lista for fornecida, os valores exclusivos de retardo serão feitos. target_lags=[1, 2, 2, 4] criará lags de um, 2 e 4 períodos.
    + Corrigir o bug sobre a perda de tipos de colunas após a transformação (bug vinculado);
    + No `model.forecast(X, y_query)`, permita que y_query seja um tipo de objeto que contenha Nenhum(s) no início (#459519).
    + Adicionar valores esperados à saída `automl`
  + **azureml-contrib-descompasso**
    +  Melhorias no notebook de exemplo, incluindo alternar para o azureml-opendatasets em vez de azureml-contrib-opendatasets e melhorias de desempenho ao aprimorar dados
  + **azureml-contrib-explicação-modelo**
    + Argumento de transformações fixas para o explicador de verde-limão para importância de recurso bruto no azureml-contrib-explique-Package
    + Segmentações adicionadas a explicações de imagem no explicador de imagem para o pacote AzureML-contrib-explique-Model
    + Adicionar suporte esparso scipy para LimeExplainer
    + Acrescentou `batch_size` para imitar explicador quando `include_local=False`, para transmitir explicações globais em lotes para melhorar o tempo de execução do DecisionTreeExplainableModel
  + **azureml-contrib-featureengineering**
    + Correção para chamar set_featurizer_timeseries_params(): alteração do tipo de valor ditado e verificação nula - Adicione o caderno para `timeseries` featurizer
    + Atualize a dependência NimbusML para a versão 1.2.0 (atual mais recente).
  + **azureml-core**
    + Adicionada a capacidade de anexar armazenamentos de DBFS na CLI do AzureML
    + Fixou o bug com o upload de uma loja de dados onde é criada uma pasta vazia se `target_path` começado com `/`
    + Correção de `deepcopy` problema em ServicePrincipalAuthentication.
    + Adicionados os comandos "AZ ml Environment show" e "AZ ml Environment List" à CLI.
    + Os ambientes agora apoiam a especção de uma base_dockerfile como alternativa a uma base_image já construída.
    + A definição de Configuração de Configuração de Execução não utilizada auto_prepare_environment foi marcada como depreciada.
    + A descrição do modelo agora pode ser atualizada após o registro
    + Bugfix: o modelo e a exclusão de imagem agora fornecem mais informações sobre como recuperar objetos upstream que dependem deles se a exclusão falhar devido a uma dependência upstream.
    + Correção do bug que imprimiu a duração em branco para implantações que ocorrem ao criar um espaço de trabalho para alguns ambientes.
    + Melhores exceções de falha na criação do espaço de trabalho. De modo que os usuários não vejam "não é possível criar o espaço de trabalho. Não é possível localizar... " como a mensagem e, em vez disso, veja a falha de criação real.
    + Adicione suporte para autenticação de token em WebServices AKS.
    + Adicione `get_token()` método a `Webservice` objetos.
    + Adicionado suporte à CLI para gerenciar os conjuntos de computadores de aprendizado de máquina.
    + `Datastore.register_azure_blob_container` agora opcionalmente leva um valor `blob_cache_timeout` (em segundos) que configura os parâmetros de montagem da Blobfuse para permitir a expiração da cache para esta loja de dados. O padrão é sem tempo limite, ou seja, quando um blob é lido, ele permanecerá no cache local até que o trabalho seja concluído. A maioria dos trabalhos preferirá essa configuração, mas alguns trabalhos precisarão ler mais dados de um grande DataSet do que caberá em seus nós. Para esses trabalhos, o ajuste desse parâmetro irá ajudá-lo a ter sucesso. Tome cuidado ao ajustar esse parâmetro: definir o valor muito baixo pode resultar em baixo desempenho, pois os dados usados em uma época podem expirar antes de serem usados novamente. Isso significa que todas as leituras serão feitas do armazenamento de BLOBs (ou seja, a rede) em vez do cache local, o que afeta negativamente os tempos de treinamento.
    + A descrição do modelo agora pode ser atualizada corretamente após o registro
    + A exclusão de modelo e imagem agora fornece mais informações sobre objetos upstream que dependem deles, o que causa a falha da exclusão
    + Melhorar a utilização de recursos de execuções remotas usando o azureml. mlflow.
  + **azureml-explain-model**
    + Argumento de transformações fixas para o explicador de verde-limão para importância de recurso bruto no azureml-contrib-explique-Package
    + Adicionar suporte esparso scipy para LimeExplainer
    + wrapper de explicador linear de forma adicionada, bem como outro nível para explicador tabular para explicar modelos lineares
    + para explicador de imitação na biblioteca modelo explicar, erro fixo ao include_local=Falso para entrada de dados escassos
    + Adicionar valores esperados à saída `automl`
    + correção do recurso de permutação fixa quando o argumento de transformações foi fornecido para obter a importância do recurso bruto
    + acrescentou `batch_size` para imitar explicador quando `include_local=False`, para transmitir explicações globais em lotes para melhorar o tempo de execução do DecisionTreeExplainableModel
    + para a biblioteca de explicabilidade de modelo, os explicadores Blackbox fixos em que a entrada do dataframe do pandas é necessária para previsão
    + Fixou um inseto onde `explanation.expected_values` às vezes devolveva um carro alegórico em vez de uma lista com um carro alegórico.
  + **azureml-mlflow**
    + Melhorar o desempenho do mlflow.set_experiment(experiment_name)
    + Fixe o bug em utilização da Autenticação InteractiveLogin para tracking_uri de mlflow
    + Melhorar a utilização de recursos de execuções remotas usando o azureml. mlflow.
    + Melhorar a documentação do pacote azureml-mlflow
    + Patch bug onde mlflow.log_artifacts ("my_dir") salvaria artefactos em "my_dir/<artifact-paths>" em vez de "<artifact-paths>"
  + **azureml-opendatasets**
    + Fixe `pyarrow` de `opendatasets` para versões antigas (< 0.14.0) devido a um problema de memória introduzido recentemente.
    + Mova o azureml-contrib-opendatasets para o azureml-opendatasets.
    + Permita que as classes Open DataSet sejam registradas no espaço de trabalho Azure Machine Learning e aproveitem as funcionalidades do conjunto de recursos AML diretamente.
    + Melhore significativamente o desempenho do NoaaIsdWeather enriquecer na versão que não seja do SPARK.
  + **azureml-pipeline-steps**
    + O DBFS datastore agora tem suporte para entradas e saídas em DatabricksStep.
    + Documentação atualizada para a etapa do lote do Azure com relação a entradas/saídas.
    + Em AzureBatchStep, alterou *delete_batch_job_after_finish* valor padrão para *verdadeiro*.
  + **azureml – telemetria**
    +  Mova o azureml-contrib-opendatasets para o azureml-opendatasets.
    + Permita que as classes Open DataSet sejam registradas no espaço de trabalho Azure Machine Learning e aproveitem as funcionalidades do conjunto de recursos AML diretamente.
    + Melhore significativamente o desempenho do NoaaIsdWeather enriquecer na versão que não seja do SPARK.
  + **azureml-train-automl**
    + Documentação atualizada sobre get_output para refletir o tipo real de retorno e fornecer notas adicionais sobre a recuperação de propriedades-chave.
    + Atualize a dependência NimbusML para a versão 1.2.0 (atual mais recente).
    + Adicionar valores esperados à saída `automl`
  + **azureml-train-core**
    + As cadeias de caracteres agora são aceitas como destino de computação para ajuste de hiperparâmetro automatizado
    + A definição de Configuração de Configuração de Execução não utilizada auto_prepare_environment foi marcada como depreciada.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>SDK v 1.1.9 do Azure Machine Learning data Prep

+ **Novos recursos**
  + Adicionado suporte para ler um arquivo diretamente de uma URL http ou HTTPS.

+ **Correções de bugs e melhorias**
  + Mensagem de erro aprimorada ao tentar ler um conjunto de parquet de uma fonte remota (que não tem suporte no momento).
  + Corrigido um bug ao gravar no formato de arquivo parquet no ADLS Gen 2 e atualizar o nome do contêiner ADLS Gen 2 no caminho.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Interface visual
+ **Recursos de visualização**
  + Módulo "executar script R" adicionado na interface visual.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK para Python v 1.0.48

+ **Novos recursos**
  + **azureml-opendatasets**
    + o **azureml-contrib-opendatasets** agora está disponível como **azureml-opendatasets**. O pacote antigo ainda pode funcionar, mas é recomendável usar o **azureml-opendatasets** avançando para obter recursos e aprimoramentos mais avançados.
    + Esse novo pacote permite que você registre conjuntos de registros abertos como DataSet no espaço de trabalho Azure Machine Learning e aproveite quaisquer funcionalidades oferecidas pelo conjunto de recursos.
    + Ele também inclui recursos existentes, como o consumo de conjuntos de valores abertos como o pandas/SPARK dataframes e junções de local para alguns conjuntos de um conjunto de recursos, como clima.

+ **Recursos de visualização**
    + HyperDriveConfig agora pode aceitar o objeto de pipeline como um parâmetro para dar suporte ao ajuste de hiperparâmetro usando um pipeline.

+ **Correções de bugs e melhorias**
  + **azureml-train-automl**
    + Corrigido o bug sobre a perda de tipos de colunas após a transformação.
    + Fixou o inseto para permitir que y_query seja um tipo de objeto que contenha nenhum ou s no início.
    + Corrigido o problema no procedimento de seleção de Ensemble que estava aumentando desnecessariamente o Ensemble resultante, mesmo se as pontuações permaneceram constantes.
    + Corrigiu o problema com as definições de whitelist_models e blacklist_models no AutoMLStep.
    + Correção do problema que impedia o uso de pré-processamento quando AutoML teria sido usado no contexto de pipelines do Azure ML.
  + **azureml-opendatasets**
    + Você moveu o azureml-contrib-opendatasets para o azureml-opendatasets.
    + As classes Open DataSet permitidas foram registradas para Azure Machine Learning espaço de trabalho e aproveitam as funcionalidades do conjunto de recursos AML diretamente.
    + Desempenho aprimorado do NoaaIsdWeather enriquecer com uma versão não SPARK significativamente.
  + **azureml-explain-model**
    + Documentação online atualizada para objetos de interpretação.
    + Acrescentou `batch_size` para imitar explicadores quando `include_local=False`, para transmitir explicações globais em lotes para melhorar o tempo de execução do DecisionTreeExplainableModel para a biblioteca de explicabilidade de modelos.
    + Corrigiu a questão em que `explanation.expected_values` às vezes devolveva um carro alegórico em vez de uma lista com um carro alegórico.
    + Foram adicionados valores esperados para `automl` saída para o explicador de imitação em explicar a biblioteca de modelos.
    + Correção do recurso de permutação corrigida quando o argumento de transformações foi fornecido para obter a importância do recurso bruto.
  + **azureml-core**
    + Adicionada a capacidade de anexar armazenamentos de DBFS na CLI do AzureML.
    + Corrigiu o problema com o upload da datastore onde é criada uma pasta vazia se `target_path` começado com `/`.
    + Comparação habilitada de dois conjuntos de valores.
    + O modelo e a exclusão de imagem agora fornecem mais informações sobre como recuperar objetos upstream que dependem deles se a exclusão falhar devido a uma dependência upstream.
    + Deprecendo a definição de Configuração de Execução não utilizada em auto_prepare_environment.
  + **azureml-mlflow**
    + Melhor utilização de recursos de execuções remotas que usam o azureml. mlflow.
    + A documentação do pacote azureml-mlflow foi aprimorada.
    + Fixou a questão em que o mlflow.log_artifacts ("my_dir") salvaria artefactos em "my_dir/artefactos-caminhos" em vez de "artefactos-caminhos".
  + **azureml-pipeline-core**
    + O hash_paths para a para-quedista para todas as etapas do gasoduto é depreciado e será removido no futuro. Por predefinição, o conteúdo do source_directory é hashed (exceto ficheiros listados em .amlignore ou .gitignore)
    + Melhorando o módulo e o ModuleStep para dar suporte a módulos de tipo de computação específicos, para se preparar para a integração do RunConfiguration e outras alterações para desbloquear o uso do módulo específico do tipo de computação em pipelines.
  + **azureml-pipeline-steps**
    + AzureBatchStep: documentação aprimorada com relação a entradas/saídas.
    + AzureBatchStep: Mudou delete_batch_job_after_finish valor padrão para verdadeiro.
  + **azureml-train-core**
    + As cadeias de caracteres agora são aceitas como destino de computação para ajuste de hiperparâmetro automatizado.
    + Deprecendo a definição de Configuração de Execução não utilizada em auto_prepare_environment.
    + Parâmetros pré-catados `conda_dependencies_file_path` e `pip_requirements_file_path` a favor de `conda_dependencies_file` e `pip_requirements_file` respectivamente.
  + **azureml-opendatasets**
    + Melhore significativamente o desempenho do NoaaIsdWeather enriquecer na versão que não seja do SPARK.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>SDK v 1.1.8 do Azure Machine Learning data Prep

+ **Novos recursos**
 + Os objetos Dataflow agora podem ser iterados, produzindo uma sequência de registros. Consulte a documentação para `Dataflow.to_record_iterator`.
  + Os objetos Dataflow agora podem ser iterados, produzindo uma sequência de registros. Consulte a documentação para `Dataflow.to_record_iterator`.

+ **Correções de bugs e melhorias**
 + Aumentou a robustez do dataprep SDK.
 + Tratamento aprimorado de quadros de bits pandas com índices de coluna que não são de cadeia de caracteres.
 + Melhorou o desempenho dos `to_pandas_dataframe` em Conjuntos de Dados.
 + Correção de um bug em que a execução do Spark de conjuntos de valores falhou quando executada em um ambiente com vários nós.
  + Aumentou a robustez do dataprep SDK.
  + Tratamento aprimorado de quadros de bits pandas com índices de coluna que não são de cadeia de caracteres.
  + Melhorou o desempenho dos `to_pandas_dataframe` em Conjuntos de Dados.
  + Correção de um bug em que a execução do Spark de conjuntos de valores falhou quando executada em um ambiente com vários nós.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>SDK v 1.1.7 do Azure Machine Learning data Prep

Revertemos uma alteração que melhorou o desempenho, pois ela estava causando problemas para alguns clientes usando Azure Databricks. Se você recebeu um problema em Azure Databricks, você pode atualizar para a versão 1.1.7 usando um dos métodos abaixo:
1. Execute este script para atualizar: `%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Recrie o cluster, que instalará a versão mais recente do SDK de preparação de dados.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Azure Machine Learning SDK para Python v 1.0.45

+ **Novos recursos**
  + Adicionar modelo substituto de árvore de decisão ao explicador imitador em azureml-explique-pacote de modelo
  + Capacidade de especificar uma versão do CUDA a ser instalada em imagens do inferência. Suporte para CUDA 9,0, 9,1 e 10,0.
  + As informações sobre as imagens de base de treinamento do Azure ML agora estão disponíveis no [repositório do GitHub de contêineres do Azure ml](https://github.com/Azure/AzureML-Containers) e [DockerHub](https://hub.docker.com/_/microsoft-azureml)
  + Adicionado suporte à CLI para o agendamento do pipeline. Execute "AZ ml pipeline-h" para saber mais
  + Adicionado o parâmetro namespace kubernetes personalizado à CLI e à configuração de implantação do AKS WebService.
  + Parâmetro de hash_paths deprecatado para todos os passos do gasoduto
  + O Model.register suporta agora o registo de múltiplos ficheiros individuais como um único modelo com a utilização do parâmetro `child_paths`.

+ **Recursos de visualização**
    + Os explicadores de Pontuação agora podem, opcionalmente, salvar informações de Conda e Pip para serialização e desserialização mais confiáveis.
    + Correção de bug do seletor de recurso automático.
    + Mlflow.azureml.build_image para a nova api, bugs remendados expostos pela nova implementação.

+ **Correções de bugs e melhorias**
  + Removida a dependência de `paramiko` do azureml-Core. Foram adicionados avisos de substituição para métodos de anexação de destino de computação herdados.
  + Melhorar o desempenho do run.create_children
  + Em explicador de imitação com classificador binário, corrija a ordem das probabilidades quando a probabilidade de professor for usada para dimensionar valores de forma.
  + Tratamento de erros e mensagem aprimorados para o aprendizado de máquina automatizado.
  + Correção do problema de tempo limite de iteração para o aprendizado de máquina automatizado.
  + Melhorou o desempenho de transformação da série temporal para o aprendizado de máquina automatizado.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>SDK v 1.1.6 do Azure Machine Learning data Prep

+ **Novos recursos**
  + Funções de resumo adicionadas para os valores principais (`SummaryFunction.TOPVALUES`) e os valores inferiores (`SummaryFunction.BOTTOMVALUES`).

+ **Correções de bugs e melhorias**
  + Melhorou significativamente o desempenho da `read_pandas_dataframe`.
  + Fixou um bug que faria com que `get_profile()` num Fluxo de Dados apontando para a falha dos ficheiros binários.
  + Expostos `set_diagnostics_collection()` permitir ativação/desativação programática da coleção de telemetria.
  + Mudou o comportamento de `get_profile()`. Os valores NaN agora são ignorados por min, mean, STD e Sum, que se alinha com o comportamento de pandas.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Azure Machine Learning SDK para Python v 1.0.43

+ **Novos recursos**
  + O Azure Machine Learning agora fornece suporte de primeira classe para aprendizado de máquina e estrutura de análise de dados populares Scikit. Usando [`SKLearn` estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py), os usuários podem facilmente treinar e implantar modelos Scikit-learn.
    + Saiba como [executar o ajuste de hiperparâmetro com Scikit-Learn usando o hyperdrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + Adição de suporte para a criação de ModuleStep em pipelines junto com classes de módulo e ModuleVersion para gerenciar unidades de computação reutilizáveis.
  + Os serviços web da ACI suportam agora scoring_uri persistentes através de atualizações. O scoring_uri passará de IP para FQDN. A etiqueta de nome Dns para FQDN pode ser configurada definindo o dns_name_label no deploy_configuration.
  + Novos recursos do Machine Learning automatizados:
    + Featurizer STL para previsão
    + O clustering do KMeans está habilitado para a limpeza de recursos
  + As aprovações de cota AmlCompute se tornaram mais rápidas! Agora, automatizamos o processo para aprovar suas solicitações de cota dentro de um limite. Para obter mais informações sobre como as cotas funcionam, saiba [como gerenciar cotas](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas).

+ **Recursos de visualização**
    + Integração com o [MLflow](https://mlflow.org) 1.0.0 Tracking por meio do pacote azureml-MLflow ([notebooks de exemplo](https://aka.ms/azureml-mlflow-examples)).
    + Envie o bloco de anotações Jupyter como uma execução. [Documentação de referência de API](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Visualização pública do [detector de descompasso de dados](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) por meio do pacote azureml-contrib-descompasso ([notebooks de exemplo](https://aka.ms/azureml-datadrift-example)). A descompasso de dados é uma das principais razões em que a precisão do modelo diminui com o passar do tempo. Acontece quando os dados servidos para o modelo na produção são diferentes dos dados em que o modelo foi treinado. O detector de descompasso de dados AML ajuda o cliente a monitorar descompasso de dados e envia alerta sempre que o descompasso é detectado.

+ **Alterações recentes**

+ **Correções de bugs e melhorias**
  + O RunConfiguration Load e Save dá suporte à especificação de um caminho de arquivo completo com compatibilidade total para o comportamento anterior.
  + Cache adicionado em ServicePrincipalAuthentication, desativado por padrão.
  + Habilite o registro em log de várias plotagens com o mesmo nome de métrica.
  + A classe de modelo agora é adequadamente importável do azureml. Core (`from azureml.core import Model`).
  + Nas etapas do gasoduto, `hash_path` parâmetro está agora depreciado. O novo comportamento é para o hash completo source_directory, exceto ficheiros listados em .amlignore ou .gitignore.
  + Nos pacotes de gasodutos, várias `get_all` e métodos `get_all_*` foram depreciados a favor de `list` e `list_*`, respectivamente.
  + azureml.core.get_run já não exige que as classes sejam importadas antes de devolver o tipo de execução original.
  + Corrigido um problema em que algumas chamadas para a atualização do WebService não dispararam uma atualização.
  + O tempo limite de pontuação em WebServices AKS deve estar entre 5 ms e 300000ms. Max permitiu scoring_timeout_ms para pedidos de pontuação foi aumentado de 1 min para 5 min.
  + Os objetos localWebservice têm agora propriedades `scoring_uri` e `swagger_uri`.
  + A criação do diretório de saídas movidos e o carregamento do diretório de saídas do processo do usuário. O SDK do histórico de execução habilitado deve ser executado em cada processo do usuário. Isso deve resolver alguns problemas de sincronização experientes por execuções de treinamento distribuídas.
  + O nome do log do azureml gravado no nome do processo do usuário agora incluirá o nome do processo (somente para treinamento distribuído) e PID.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>SDK v 1.1.5 do Azure Machine Learning data Prep

+ **Correções de bugs e melhorias**
  + Para valores de data e hora interpretados que têm um formato de ano de 2 dígitos, o intervalo de anos válidos foi atualizado para corresponder ao Windows pode ser liberado. O intervalo foi alterado de 1930-2029 para 1950-2049.
  + Ao ler um arquivo e definir `handleQuotedLineBreaks=True`, `\r` será tratado como uma nova linha.
  + Fixou um inseto que fez com que `read_pandas_dataframe` falhasse em alguns casos.
  + Melhor desempenho da `get_profile`.
  + Mensagens de erro aprimoradas.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>SDK v 1.1.4 do Azure Machine Learning data Prep

+ **Novos recursos**
  + Agora você pode usar as seguintes funções de linguagem de expressão para extrair e analisar valores DateTime em novas colunas.
    + `RegEx.extract_record()` extrai elementos de data numa nova coluna.
    + `create_datetime()` cria objetos de data a partir de elementos de data separados.
  + Ao ligar `get_profile()`, pode agora ver que as colunas quânticas estão rotuladas como (est.) para indicar claramente que os valores são aproximações.
  + Agora você pode usar o * * mascaramento ao ler do armazenamento de BLOBs do Azure.
    + por exemplo, `dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Correções de erros**
  + Correção de um bug relacionado à leitura de um arquivo parquet de uma origem remota (blob do Azure).

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK para Python v 1.0.39
+ **For**
  + A configuração de execução auto_prepare_environment opção está a ser depreciada, com o preparação automática tornando-se o padrão.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>SDK v 1.1.3 do Azure Machine Learning data Prep

+ **Novos recursos**
  + Suporte adicionado para ler a partir de uma base de dados PostgresSQL, quer ligando para read_postgresql ou usando uma Datastore.
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
+ Seção de criação de nova marca (versão prévia) no espaço de trabalho Azure Machine Learning, que inclui Machine Learning automatizado, interface visual e VMs de notebook hospedadas
    + Criar automaticamente um modelo usando o Machine Learning automatizado
    + Usar uma interface visual de arrastar e soltar para executar experimentos
    + Crie uma VM de notebook para explorar dados, criar modelos e implantar serviços.
+ Gráfico dinâmico e atualização de métrica em executar relatórios e páginas de detalhes de execução
+ Atualizado o Visualizador de arquivos para logs, saídas e instantâneos em páginas de detalhes de execução.
+ Nova e aprimorada experiência de criação de relatórios na guia experimentos.
+ Foi adicionada a capacidade de baixar o arquivo config. JSON da página Visão geral do espaço de trabalho Azure Machine Learning.
+ Suporte à criação de Azure Machine Learning espaço de trabalho no espaço de trabalho Azure Databricks.

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
+ [Crie rapidamente uma VM de notebook pré-configurada](tutorial-1st-experiment-sdk-setup.md) que tenha a versão mais recente do SDK do Azure Machine Learning e dos pacotes relacionados.
+ O acesso é protegido por meio de tecnologias comprovadas, como HTTPS, autenticação e autorização de Azure Active Directory.
+ Armazenamento em nuvem confiável de blocos de anotações e código em sua conta de armazenamento de BLOBs Workspace do Azure Machine Learning. Você pode excluir com segurança sua VM do bloco de anotações sem perder seu trabalho.
+ Blocos de anotações de exemplo pré-instalados para explorar e experimentar Azure Machine Learning recursos.
+ Recursos de personalização completa de VMs do Azure, qualquer tipo de VM, qualquer pacote, qualquer driver. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK para Python v 1.0.33 lançado.

+ O Modelos de Aceleração de Hardware do Azure ML no [FPGAs](how-to-deploy-fpga-web-service.md) está disponível para o público em geral.
  + Agora você pode [usar o pacote azureml-da aceleração extra-Models](how-to-deploy-fpga-web-service.md) para:
    + Treinar os pesos de uma rede neural profunda com suporte (ResNet 50, ResNet 152, DenseNet-121, VGG-16 e SSD-VGG)
    + Usar o aprendizado de transferência com o DNN com suporte
    + Registrar o modelo com o Serviço Gerenciamento de Modelos e colocar o modelo em contêiner
    + Implantar o modelo em uma VM do Azure com um FPGA em um cluster do AKS (serviço kubernetes do Azure)
  + Implantar o contêiner em um dispositivo de servidor [Azure data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)
  + Pontuar seus dados com o ponto de extremidade gRPC com este [exemplo](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Aprendizagem automática de máquinas

+ Limpeza de recursos para habilitar a adição dinâmica de :::no-loc text="featurizers"::: para otimização de desempenho. Novos :::no-loc text="featurizers":::: incorporações de trabalho, peso de evidência, codificações de destino, codificação de destino de texto, distância do cluster
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

Observação: o SDK do Python de preparação de dados não instalará mais pacotes `numpy` e `pandas`. Consulte [as instruções de instalação atualizadas](https://github.com/Microsoft/AMLDataPrepDocs).

+ **Novos recursos**
  + Agora você pode usar a transformação dinâmica.
    + Guia de instruções: [bloco de anotações dinâmico](https://aka.ms/aml-data-prep-pivot-nb)
  + Agora você pode usar expressões regulares em funções nativas.
    + Exemplos:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + Agora pode utilizar `to_upper` e `to_lower` funções na linguagem de expressão.
  + Agora você pode ver o número de valores exclusivos de cada coluna em um perfil de dados.
  + Para alguns dos passos de leitor comumente usados, você pode agora passar no argumento `infer_column_types`. Se estiver definido como `True`, a preparação de dados tentará detectar e converter automaticamente os tipos de coluna.
    + `inference_arguments` está agora deprecida.
  + Agora você pode chamar `Dataflow.shape`.

+ **Correções de bugs e melhorias**
  + `keep_columns` aceita agora um argumento facultativo adicional `validate_column_exists`, que verifica se o resultado de `keep_columns` conterá colunas.
  + Todos os passos do leitor (que lêem de um ficheiro) aceitam agora um argumento opcional adicional `verify_exists`.
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
  + Azure Machine Learning os estimadores DNN agora fornecem suporte interno a várias versões. Por exemplo, `TensorFlow`estimador  aceita agora um parâmetro `framework_version`, e os utilizadores podem especificar a versão '1.10' ou '1.12'. Para obter uma lista das versões suportadas pela sua versão SDK atual, ligue `get_supported_versions()` na classe-quadro desejada (por exemplo, `TensorFlow.get_supported_versions()`).
  Para obter uma lista das versões com suporte da versão mais recente do SDK, consulte a [documentação do estimador do DNN](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>SDK v 1.1.1 do Azure Machine Learning data Prep

+ **Novos recursos**
  + Pode ler várias fontes datastore/DataPath/DataReference utilizando read_* transforma.
  + Você pode executar as seguintes operações em colunas para criar uma nova coluna: divisão, piso, módulo, potência, comprimento.
  + A preparação de dados agora faz parte do pacote de diagnóstico do Azure ML e registrará em log as informações de diagnóstico por padrão.
    + Para desativar isto, desloque esta variável ambiental para verdade: DISABLE_DPREP_LOGGER

+ **Correções de bugs e melhorias**
  + Documentação de código aprimorada para classes e funções usadas com frequência.
  + Fixou um bug em auto_read_file que não leu ficheiros Excel.
  + Opção adicional para substituir a pasta em read_pandas_dataframe.
  + Desempenho aprimorado da instalação de dependência do dotnetcore2 e adição de suporte para Fedora 27/28 e Ubuntu 1804.
  + Melhorou o desempenho da leitura de BLOBs do Azure.
  + A detecção de tipo de coluna agora dá suporte a colunas do tipo Long.
  + Correção de um bug em que alguns valores de data estavam sendo exibidos como carimbos de hora em vez de objetos DateTime de Python.
  + Corrigido um bug em que algumas contagens de tipo estavam sendo exibidas como duplos em vez de inteiros.


## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK para Python v 1.0.21

+ **Novos recursos**
  + O método *azureml.core.Run.create_children* permite a criação de baixa latência de várias crianças com uma única chamada.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>SDK da preparação de dados do Azure Machine Learning v 1.1.0

+ **Alterações recentes**
  + O conceito do pacote de preparação de dados foi preterido e não tem mais suporte. Em vez de persistir vários fluxos de entrada em um pacote, você pode persistir os fluxos de entrada individualmente.
    + Guia de instruções: [abrindo e salvando o notebook de fluxos de anotações](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Novos recursos**
  + A preparação de dados agora pode reconhecer colunas que correspondem a um tipo semântico específico e divididas de acordo. Os STypes atualmente suportados incluem: endereço de email, coordenadas geográficas (Latitude & longitude), endereços IPv4 e IPv6, número de telefone dos EUA e CEP dos EUA.
    + Guia de instruções: [bloco de anotações de tipos semânticos](https://aka.ms/aml-data-prep-semantic-types-nb)
  + A preparação de dados agora dá suporte às seguintes operações para gerar uma coluna resultante de duas colunas numéricas: subtrair, multiplicar, dividir e módulo.
  + Pode ligar para `verify_has_data()` num Fluxo de Dados para verificar se o Fluxo de Dados produziria registos se fosse executado.

+ **Correções de bugs e melhorias**
  + Agora você pode especificar o número de compartimentos a serem usados em um histograma para perfis de coluna numérica.
  + A transformação `read_pandas_dataframe` agora requer que o DataFrame tenha nomes de colunas dactilografadas por cordas ou bytes.
  + Fixou um inseto na `fill_nulls` transformar, onde os valores não foram corretamente preenchidos se a coluna estivesse em falta.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK para Python v 1.0.18

 + **For**
   + O pacote azureml-tensorboard substitui o azureml-contrib-tensorboard.
   + Com esta versão, você pode configurar uma conta de usuário em seu amlcompute (cluster de computação gerenciada), ao criá-la. Isso pode ser feito passando essas propriedades na configuração de provisionamento. Você pode encontrar mais detalhes na [documentação de referência do SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--).

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

  + Azure Machine Learning agora fornece suporte de primeira classe para o DNN Framework popular. Usar [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) classe os usuários pode facilmente treinar e implantar modelos de encadeamento.
    + Saiba como [executar o treinamento distribuído com o ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Saiba como [executar o ajuste de hiperparâmetro com o encadeamento usando hyperdrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning pipelines adicionou a capacidade de disparar uma execução de pipeline com base nas modificações do repositório de armazenamento. O [notebook de agendamento](https://aka.ms/pl-schedule) de pipeline é atualizado para demonstrar esse recurso.

+ **Correções de bugs e melhorias**
  + Adicionámos suporte nos oleodutos Azure Machine Learning para definir a propriedade source_directory_data_store para uma loja de dados desejada (como um armazenamento de blob) em [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) que são fornecidas ao [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Por padrão, as etapas usam o repositório de arquivos do Azure como o repositório de armazenamento de backup, que pode ter problemas de limitação quando um grande número de etapas é executado simultaneamente.

### <a name="azure-portal"></a>Portal do Azure

+ **Novos recursos**
  + Nova experiência de editor de tabela de arrastar e soltar para relatórios. Os usuários podem arrastar uma coluna do bem para a área de tabela em que uma visualização da tabela será exibida. As colunas podem ser reorganizadas.
  + Novo Visualizador de arquivos de logs
  + Links para execuções de teste, computação, modelos, imagens e implantações na guia atividades

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>SDK v 1.0.15 do Azure Machine Learning data Prep

+ **Novos recursos**
  + A preparação de dados agora dá suporte à gravação de fluxos de arquivo de um fluxo de arquivos. Também fornece a capacidade de manipular os nomes de fluxo de arquivo para criar novos nomes de arquivo.
    + Guia de instruções: [trabalhando com o bloco de anotações de fluxos de arquivos](https://aka.ms/aml-data-prep-file-stream-nb)

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
  + `PublishedPipeline.get_published_pipeline` depreciada a favor de `PublishedPipeline.get`.
  + `Schedule.get_schedule` depreciada a favor de `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>SDK v 1.0.12 do Azure Machine Learning data Prep

+ **Novos recursos**
  + A preparação de dados agora dá suporte à leitura de um banco de dado SQL do Azure usando o repositório de armazenamento.

+ **For**
  + Melhorou o desempenho da memória de determinadas operações em dados grandes.
  + `read_pandas_dataframe()` agora requer `temp_folder` a especificar.
  + A propriedade `name` em `ColumnProfile` foi depreciada - use `column_name` em vez disso.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK para Python v 1.0.10

+ **Alterações**:
  + O SDK do Azure ML não tem mais pacotes do Azure-CLI como dependência. Especificamente, as dependências do Azure-CLI-Core e do Azure-CLI-Profile foram removidas do azureml-Core. Essas são as alterações que afetam o usuário:
    + Se você estiver executando "AZ login" e usando o azureml-SDK, o SDK fará o navegador ou o log de código do dispositivo em mais uma vez. Ele não usará nenhum estado de credenciais criado por "AZ login".
    + Para CLI do Azure autenticação, como usar "AZ login", use a classe _azureml. Core. Authentication. AzureCliAuthentication_ . Para CLI do Azure autenticação, faça a _instalação do Pip Azure-CLI_ no ambiente do Python em que você instalou o azureml-SDK.
    + Se você estiver fazendo "AZ login" usando uma entidade de serviço para automação, é recomendável usar a classe _azureml. Core. Authentication. ServicePrincipalAuthentication_ , pois o azureml-SDK não usará o estado de credenciais criado pela CLI do Azure.

+ **Correções de bugs**: esta versão contém principalmente correções de bugs secundárias

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>SDK v 1.0.8 do Azure Machine Learning data Prep

+ **Correções de erros**
  + Melhorou o desempenho da obtenção de perfis de dados.
  + Correção de bugs secundários relacionados ao relatório de erros.

### <a name="azure-portal-new-features"></a>Portal do Azure: novos recursos
+ Nova experiência de criação de gráficos de arrastar e soltar para relatórios. Os usuários podem arrastar uma coluna ou atributo do bem para a área do gráfico, em que o sistema selecionará automaticamente um tipo de gráfico apropriado para o usuário com base no tipo de dados. Os usuários podem alterar o tipo de gráfico para outros tipos aplicáveis ou adicionar atributos adicionais.

    Tipos de gráfico com suporte:
    - Gráfico de linha
    - Histograma
    - Gráfico de barras empilhadas
    - Gráfico de caixa
    - Lote de dispersão
    - Gráfico de bolhas
+ O Portal agora gera relatórios dinamicamente para experimentos. Quando um usuário envia uma execução para um experimento, um relatório será gerado automaticamente com as métricas registradas e grafos para permitir a comparação entre diferentes execuções.

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning SDK para Python v 1.0.8

+ **Correções de bugs**: esta versão contém principalmente correções de bugs secundárias

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
+ **Correções de bugs**: esta versão contém principalmente correções de bugs secundárias

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>SDK v 1.0.4 do Azure Machine Learning data Prep

+ **Novos recursos**
  + `to_bool` função agora permite converter valores desajustados para valores de erro. Este é o novo comportamento de desfasamento padrão para `to_bool` e `set_column_types`, enquanto o comportamento padrão anterior foi converter valores desajustados para Falso.
  + Ao chamar `to_pandas_dataframe`, há uma nova opção para interpretar valores nulos/em falta em colunas numéricas como NaN.
  + Capacidade adicional de verificar o tipo de retorno de algumas expressões para garantir a consistência do tipo e a falha antecipadamente.
  + Pode agora chamar `parse_json` para analisar valores numa coluna como objetos JSON e expandi-los em várias colunas.

+ **Correções de erros**
  + Fixou um inseto que se despenhou `set_column_types` em Python 3.5.2.
  + Correção de um bug que falhou ao se conectar ao repositório de armazenamento usando uma imagem AML.

+ **Atualizações**
  * [Notebooks de exemplo](https://aka.ms/aml-data-prep-notebooks) para obter tutoriais de introdução, estudos de caso e guias de instruções.

## <a name="2018-12-04-general-availability"></a>2018-12-04: disponibilidade geral

Azure Machine Learning agora está disponível para o público geral.

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

+ **Novidade**
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
  * Use o token de execução do AML mais recente ao ler e gravar em repositórios de armazenamento em execuções remotas. Anteriormente, se o Token AML de executar é atualizado no Python, o tempo de execução de preparação de dados não será atualizado com o Token AML de executar atualizado.
  * Mensagens de erro mais claras adicionais
  * to_spark_dataframe() não se despenhará mais quando Spark usa `Kryo` serialização
  * Inspetor de contagem de valores agora pode mostrar mais de 1000 valores exclusivos
  * Divisão aleatória já não falha se o fluxo de dados original não tiver um nome

+ **Mais informações**
  * [SDK de Preparação de Dados do Azure Machine Learning](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Documentos e blocos de notas
+ Pipelines de ML
  + Exemplos de transferência de blocos de notas novo e atualizados para começar a trabalhar com pipelines, controlo de âmbito do batch e estilo: https://aka.ms/aml-pipeline-notebooks
  + Saiba como [criar seu primeiro pipeline](how-to-create-your-first-pipeline.md)
  + Saiba como [predições de batch com pipelines de execução](how-to-use-parallel-run-step.md)
+ Destino de computação Azure Machine Learning
  + Os [blocos de anotações de exemplo](https://aka.ms/aml-notebooks) agora são atualizados para usar a nova computação gerenciada.
  + [Saiba mais sobre este computação](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Portal do Azure: novos recursos
+ Criar e gerir [computação do Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) tipos no portal.
+ Monitorizar a utilização de quota e [quota de pedido](how-to-manage-quotas.md) para computação do Azure Machine Learning.
+ Exiba Azure Machine Learning status do cluster de computação em tempo real.
+ Foi adicionado suporte de rede virtual para a criação de computação do Azure Machine Learning e o Azure Kubernetes Service.
+ Execute novamente os pipelines publicados com os parâmetros existentes.
+ Novos [automatizada de gráficos do machine learning](how-to-understand-automated-ml.md) para modelos de classificação (comparação de precisão, ganhos, calibração, gráfico de importância da funcionalidade com explainability de modelo) e modelos de regressão (residuals e gráfico de importância da funcionalidade com modelo explainability).
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
   * Pode obter todos os tipos de dados de coluna de um Dataflow ou de um perfil de dados chamando `.dtypes`
   * Pode obter a contagem de linhas a partir de um fluxo de dados ou de um perfil de dados, chamando `.row_count`

+ **Correções de erros**
   * Fixo de tempo de conversão duplo
   * Foi corrigido---vyhodnocení depois de adicionar qualquer coluna
   * Foi corrigido um problema com FuzzyGrouping, onde ele não detectaria grupos em alguns casos
   * Função de tipo fixo para respeitar a ordem de classificação de várias colunas
   * Fixo e/ou expressões para serem semelhantes ao modo como `pandas` os manipula
   * Foi corrigido ler a partir do caminho de dbfs
   * Feitas as mensagens de erro mais compreensível
   * Agora não apresenta mais falha ao ler em destino de computação remota usando um token AML
   * Agora já não ocorre uma falha do DSVM do Linux
   * Agora já não é interrompida ao valores de cadeia de caracteres não são em predicados de cadeia de caracteres
   * Agora manipula erros de asserção quando o fluxo de dados se falhar corretamente
   * Suporta agora a localizações de armazenamento dbutils montado no Azure Databricks

## <a name="2018-11-05"></a>11-05 de 2018

### <a name="azure-portal"></a>Portal do Azure
O portal do Azure para Azure Machine Learning tem as seguintes atualizações:
  * Um novo **Pipelines** separador para pipelines publicados.
  * Foi adicionado suporte para anexar um cluster do HDInsight existente como um destino de computação.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning SDK for Python v0.1.74

+ **Alterações recentes**
  * *Workspace.compute_targets, datastores, experiências, imagens, modelos e *webservices* são propriedades em vez de métodos. Por exemplo, substitua *Workspace.compute_targets()* com *Workspace.compute_targets*.
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
  * Correção de problemas de iteração de :::no-loc text="ensembling":::.
  * Treinamento fixo pendurado bug no MAC OS.
  * Downsampling macro média PR/curva cor MULTICLASSE no cenário de validação personalizada.
  * Remover a lógica de índice extra.
  * Remover o filtro de get_output API.

+ **Pipelines**
  * Adicionar um método Pipeline.publish() para publicar um pipeline diretamente, sem a necessidade de uma execução de executar primeira.
  * Adicionou um método PipelineRun.get_pipeline_runs() para obter as condutas de gasoduto que foram geradas a partir de um oleoduto publicado.

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


## <a name="next-steps"></a>Passos seguintes

Leia a descrição geral do [Azure Machine Learning](overview-what-is-azure-ml.md).
