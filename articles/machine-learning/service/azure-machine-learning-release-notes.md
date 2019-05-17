---
title: O que há de novo na versão?
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre as atualizações mais recentes para o serviço Azure Machine Learning e o machine learning e SDKs de Python de preparação de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: Blackmist
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 892b9bc63f9f2d9abc7108587a7bf929473e4648
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65779439"
---
# <a name="azure-machine-learning-service-release-notes"></a>Notas de versão de serviço do Azure Machine Learning

Neste artigo, saiba mais sobre as versões de serviço do Azure Machine Learning.  Para obter uma descrição completa de cada SDK, visite os documentos de referência para:
+ O Azure Machine Learning [ **principal SDK para Python**](https://aka.ms/aml-sdk)
+ O Azure Machine Learning [ **SDK de preparação de dados**](https://aka.ms/data-prep-sdk)

Ver [a lista de problemas conhecidos](resource-known-issues.md) para saber mais sobre erros conhecidos e soluções alternativas.

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK for Python v1.0.39
+ **Alterações**
  + Opção de auto_prepare_environment de configuração de execução está a ser preterida, com automática prepare tornar-se a predefinição.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Portal do Azure

No portal do Azure, pode agora:
+ Criar e executar experimentações automatizadas de ML 
+ Criar uma VM para experimentar os blocos de notas Jupyter do exemplo do bloco de notas ou sua própria.
+ Totalmente nova secção criação de conteúdos (pré-visualização) na área de serviço Machine Learning, que inclui automatizada Machine Learning, a Visual Interface e alojadas as VMs de bloco de notas
    + Criar automaticamente um modelo com o Automated machine learning 
    + Utilize uma operação arrastar e soltar Interface Visual para executar experimentações
    + Crie uma VM de bloco de notas para explorar dados, criar modelos e implementar serviços.
+ Gráfico e a atualização em executar relatórios de métrica do Live e executar as páginas de detalhes
+ Visualizador de ficheiro atualizado para os registos, saídas e instantâneos em páginas de detalhes de execução.
+ Experiência de criação de relatórios novos e aprimorados no separador de experimentações. 
+ Foi adicionada a capacidade para transferir o ficheiro Config JSON a partir da página de descrição geral da área de trabalho de serviço do Azure Machine Learning.
+ Suporta a criação de área de trabalho do Machine Learning service da área de trabalho do Azure Databricks 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Azure Machine Learning SDK for Python v1.0.33
+ **Novos recursos**
  + O _Workspace.create_ método aceita agora configurações de cluster predefinido para clusters GPU e CPU.
  + Se a criação da área de trabalho falhar, os recursos dependentes são limpos.
  + SKU de registo de contentor do padrão do Azure foi mudado para básico.
  + O Azure Container Registry é criado lentamente, quando necessário para a criação de imagem ou de execução.
  + Suporte para ambientes para as execuções de preparações.

### <a name="notebook-virtual-machine"></a>Máquina Virtual de bloco de notas 

Utilize uma VM de bloco de notas como um ambiente de hospedagem seguro e prontas para empresas para blocos de notas do Jupyter no qual pode programar experiências de machine learning, implementar modelos como pontos finais de web e realizar todas as outras operações suportadas pelo Machine Learning SDK do Azure com Python. Ele fornece várias capacidades:
+ [Lance rapidamente um bloco de notas pré-configurada VM](quickstart-run-cloud-notebook.md) que tem a versão mais recente do SDK do Azure Machine Learning e de pacotes relacionados.
+ Acesso é protegido através de tecnologias comprovadas, por exemplo, HTTPS, autorização e autenticação do Azure Active Directory.
+ Armazenamento de cloud fiável de blocos de notas e o código na sua conta de armazenamento de BLOBs de área de trabalho do Azure Machine Learning. Pode eliminar em segurança o bloco de notas VM sem perder o seu trabalho.
+ Pré-instalado blocos de notas de exemplo para explorar e experimentar as funcionalidades do serviço Azure Machine Learning.
+ Capacidades de personalização total de VMs do Azure, qualquer tipo VM, quaisquer pacotes, quaisquer controladores. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure SDK de Aprendizado de máquina para v1.0.33 Python lançados.

+ O Azure ML aceleradas modelos de Hardware no [FPGAs](concept-accelerate-with-fpgas.md) está disponível em geral.
  + Agora, pode [utilizar o pacote de modelos de aceleração azureml](how-to-deploy-fpga-web-service.md) para:
    + Treinar os pesos de uma rede neural profunda suportado (utilizar o ResNet 50, utilizar o ResNet 152, DenseNet 121, VGG-16 e SSD VGG)
    + Utilizar transferência learning com o DNN suportado
    + Registe o modelo com o serviço de gestão de modelos e o modelo de colocar num contentor
    + Implementar o modelo para uma VM do Azure com um FPGA num cluster do Azure Kubernetes Service (AKS)
  + Implementar o contentor para um [Edge de caixa de dados do Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) dispositivo de servidor
  + Classificar os dados com o ponto de final de gRPC com isso [exemplo](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Aprendizagem automática

+ Funcionalidade varrimento para ativar featurizers adicionar dinamicamente para otimização de desempenho. Novo featurizers: funcionam embeddings, peso de evidências, a codificações de destino, a codificação de destino de texto, a distância de cluster
+ CV inteligente para lidar com train/válido divide dentro de ML automatizada
+ Algumas alterações de otimização de memória e melhoria de desempenho de tempo de execução
+ Melhorias de desempenho explicação de modelo
+ Conversão de modelos ONNX para execução local
+ Foi adicionado suporte de Subsampling
+ A parar quando não existem critérios de saída definidos inteligente
+ Árvores empilhadas

+ Previsão de série temporal
  + Prever nova função de previsão   
  + Agora, pode utilizar a origem sem interrupção de validação cruzada nos dados de séries de tempo
  + Nova funcionalidade adicionada ao configurar a hora atrasada de série 
  + Nova funcionalidade adicionada para oferecer suporte a recursos de agregação de janela sem interrupção
  + Definições de experimentação nova deteção de feriado e featurizer quando o código de país é definido no

+ Azure Databricks
  + Ativada a previsão de séries de tempo e modelar a capacidade de explainabilty/interpretability
  + Já pode cancelar e retomar (continuar) ML automatizada experimentações
  + Foi adicionado suporte para processamento de vários núcleos

### <a name="mlops"></a>MLOps
+ **Implementação de local e depuração para a classificação de contentores**<br/> Agora pode implementar um modelo de ML localmente e iterar rapidamente em sua classificação de ficheiros e dependências para garantir que se comportam conforme o esperado.

+ **Introduced InferenceConfig & Model.deploy()**<br/> Implementação de modelo agora suporta especificando uma pasta de origem com um script de entrada, o mesmo que um RunConfig.  Além disso, a implementação de modelo foi simplificada para um único comando.

+ **Referência de Git de controlo**<br/> Os clientes vêm solicitando capacidades de integração básicas do Git para algum tempo à medida que o ajuda a manter uma trilha de auditoria de ponto-a-ponto. Implementamos o rastreamento entre entidades principais no Azure ML para os metadados relacionados com o Git (repositório, consolidação, estado limpo). Estas informações serão recolhidas automaticamente pelo SDK do e da CLI.

+ **Serviço de criação de perfis e validação de modelo**<br/> Os clientes frequentemente reclamam da dificuldade para dimensionar corretamente a computação associada ao seu serviço de inferência de tipos. Com o nosso modelo de serviço de criação de perfis, o cliente pode fornecer entradas de exemplo e irá criar o perfil em 16 de CPU diferentes / configurações de memória para determinar ideal de tamanho para a implementação.

+ **Traga a sua própria imagem de base para inferência de tipos**<br/> Outra reclamação comum foi a dificuldade em mover de experimentação para dependências de partilha de RE de inferência de tipos. Com a nossa imagem base nova capacidade de partilha, agora pode reutilizar as imagens de base de experimentação, dependências e tudo, de inferência de tipos. Isso deve acelerar Implantações e reduzir a lacuna de interno para o loop externo.

+ **Experiência de geração de esquema de Swagger melhorada**<br/> Nossa geração anterior de swagger método foi impossível de automatizar e propensa a erros. Temos uma nova forma de inline de geração de esquemas do swagger a partir de qualquer função de Python via decoradores. Podemos ter aberto esse código e o protocolo de geração de esquema não está integrado na plataforma do Azure ML.

+ **CLI do Azure ML está em disponibilidade geral (GA)**<br/> Modelos, podem agora ser implementados com um único comando da CLI. Temos comuns comentários de clientes que ninguém implementa um modelo de ML a partir de um bloco de notas do Jupyter. O [ **documentação de referência da CLI** ](https://aka.ms/azmlcli) foi atualizado.


## <a name="2019-04-22"></a>2019-04-22

Azure SDK de Aprendizado de máquina para v1.0.30 Python lançados.

O [ `PipelineEndpoint` ](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) foi introduzir para adicionar uma nova versão de um pipeline de publicado, mantendo o mesmo ponto final.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>SDK v1.1.2 de preparação de dados do Azure Machine Learning

Nota: SDK de Python de preparação de dados já não instalará `numpy` e `pandas` pacotes. Ver [atualizado instruções de instalação](https://aka.ms/aml-data-prep-installation).

+ **Novos recursos**
  + Agora, pode utilizar a transformação de Pivot.
    + Guia de procedimentos: [O bloco de notas do pivot](https://aka.ms/aml-data-prep-pivot-nb)
  + Agora pode usar expressões regulares em funções nativas.
    + Exemplos:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + Agora, pode utilizar `to_upper`  e `to_lower`  funções na linguagem de expressão.
  + Agora pode ver o número de valores exclusivos de cada coluna num perfil de dados.
  + Para algumas das etapas usadas leitor, agora pode passar no `infer_column_types` argumento. Se estiver definido como `True`, preparação de dados irá tentar detetar e converter automaticamente os tipos de coluna.
    + `inference_arguments` Agora foi preterido.
  + Pode chamar `Dataflow.shape`.

+ **Melhorias e correções de erros**
  + `keep_columns` agora aceita um argumento opcional adicional `validate_column_exists`, que verifica se o resultado da `keep_columns` irá conter quaisquer colunas.
  + Todos os passos de leitor (que leem a partir de um ficheiro) agora aceita um argumento opcional adicional `verify_exists`.
  + Melhor desempenho de leitura a partir do pandas dataframe e configurar perfis de dados.
  + Foi corrigido um erro em que um único passo de um fluxo de dados de fragmentação falhou com um único índice.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Portal do Azure
  + Agora pode submeter novamente um Script existente ser executado num cluster de computação remota existente. 
  + Agora, pode executar um pipeline publicado com novos parâmetros na guia Pipelines. 
  + Detalhes da execução agora oferece suporte a um novo Visualizador de arquivos de instantâneo. Pode ver um instantâneo do diretório quando é submetida uma execução específica. Também pode transferir o bloco de notas que foi submetido para iniciar a execução.
  + Agora pode cancelar execuções do principal do portal do Azure.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK for Python v1.0.23

+ **Novos recursos**
  + O SDK do Azure Machine Learning suporta agora 3.7 de Python.
  + Avaliadores de DNN de Aprendizado de máquina do Azure oferecem agora o suporte interno a várias versões. Por exemplo, `TensorFlow`  estimator agora aceita um `framework_version` parâmetro e os utilizadores podem especificar a versão '1.10' ou '1.12'. Para obter uma lista das versões suportadas pela sua atual versão do SDK, chame `get_supported_versions()` da classe do framework desejado (por exemplo, `TensorFlow.get_supported_versions()`).
  Para obter uma lista das versões suportadas pela versão mais recente do SDK, consulte a [documentação de estimador de DNN](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>SDK v1.1.1 de preparação de dados do Azure Machine Learning

+ **Novos recursos**
  + É possível ler a várias origens de arquivo de dados/DataPath/DataReference usando transformações read_ *.
  + Pode efetuar as seguintes operações em colunas para criar uma nova coluna: divisão, o chão, módulo, energia, comprimento.
  + Preparação de dados faz agora parte do conjunto de diagnóstico do Azure ML e registrará em log informações de diagnóstico por predefinição.
    + Para desativar esta definição, defina esta variável de ambiente como true: DISABLE_DPREP_LOGGER

+ **Melhorias e correções de erros**
  + Documentação de código aprimorada para classes usadas e funções.
  + Foi corrigido um erro no auto_read_file Falha ao ler os ficheiros do Excel.
  + Adicionada opção para substituir a pasta na read_pandas_dataframe.
  + Desempenho melhorado de instalação da dependência dotnetcore2 e foi adicionado suporte para Fedora 27/28 e Ubuntu 1804.
  + Melhorámos o desempenho de leitura de Blobs do Azure.
  + Deteção do tipo de coluna agora oferece suporte a colunas do tipo de tempo.
  + Foi corrigido um erro em que alguns valores de data estavam a ser apresentados como carimbos de data / em vez de objetos de datetime de Python.
  + Foi corrigido um erro em que alguns contagens de tipo foram a ser apresentadas como duplicatas em vez de números inteiros.

  
## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK for Python v1.0.21

+ **Novos recursos**
  + O *azureml.core.Run.create_children* método permite a criação de baixa latência de vários subordinado é executado com uma única chamada.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>SDK v1.1.0 de preparação de dados do Azure Machine Learning

+ **Alterações recentes**
  + O conceito do pacote de preparação de dados foi preterido e já não é suportado. Em vez de manter vários fluxos de dados num único pacote, pode manter fluxos de dados individualmente.
    + Guia de procedimentos: [Bloco de notas a abrir e salvar fluxos de dados](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Novos recursos**
  + Preparação de dados agora pode reconhecer colunas que correspondem a um determinado tipo de semântica e dividir em conformidade. STypes atualmente suportados incluem: endereços, coordenadas geográficas (latitude e longitude), endereços IPv4 e IPv6, o número de telefone dos EUA e CEP dos EUA do e-mail.
    + Guia de procedimentos: [Bloco de notas de tipos semântico](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Preparação de dados agora suporta as seguintes operações para gerar uma coluna resultante de duas colunas numéricas: subtrair, multiplicar e dividir e módulo.
  + Pode chamar `verify_has_data()` num fluxo de dados para verificar se o fluxo de dados produziria registos se executada.

+ **Melhorias e correções de erros**
  + Agora pode especificar o número de discretizações para utilizar num histograma para perfis de coluna numérica.
  + O `read_pandas_dataframe` transformação requer agora que o pacote de dados para que a cadeia de caracteres - ou byte - digitado nomes de colunas.
  + Foi corrigido um erro no `fill_nulls` transformação, onde valores não foram corretamente preenchidos se a coluna estava em falta.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK for Python v1.0.18

 + **Alterações**
   + O pacote do azureml tensorboard substitui azureml-contrib-tensorboard.
   + Com esta versão, pode configurar uma conta de utilizador no seu cluster de computação gerida (amlcompute), ao criá-lo. Isso pode ser feito ao transmitir essas propriedades na configuração de aprovisionamento. Pode encontrar mais detalhes a [documentação de referência do SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none-).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>SDK v1.0.17 de preparação de dados do Azure Machine Learning

+ **Novos recursos**
  + Agora suporta a adição de duas colunas numéricas para gerar uma coluna resultante usando a linguagem de expressão.

+ **Melhorias e correções de erros**
  + Melhorada a documentação e verificar a existência de random_split de parâmetro.
  
## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>SDK v1.0.16 de preparação de dados do Azure Machine Learning

+ **Correção de erros**
  + Foi corrigido um Principal de serviço o problema de autenticação que foi causado por uma alteração de API.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK for Python v1.0.17

+ **Novos recursos**

  + O Azure Machine Learning fornece agora suporte de primeira classe para o framework DNN popular Chainer. Usando [ `Chainer` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) utilizadores de classe podem facilmente preparar e implementar modelos de Chainer.
    + Saiba como [executar formação distribuída com ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Saiba como [executar a otimização de hiper-parâmetros com Chainer usando HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + O Azure Machine Learning Pipelines adicionado uma execução de Pipeline com base nas alterações de arquivo de dados de Acionador de capacidade. O pipeline [bloco de notas do agendamento](https://aka.ms/pl-schedule) é atualizado para demonstrar esta funcionalidade.

+ **Melhorias e correções de erros**
  + Foi adicionado o suporte do Azure Machine Learning Pipelines para definir a propriedade source_directory_data_store para um arquivo de dados desejado (por exemplo, o armazenamento de BLOBs) no [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) que são fornecidos para o [ PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Por predefinição passos utilizam o armazenamento de ficheiros do Azure como o arquivo de dados do backup, que pode se deparar com problemas de limitação quando um grande número de passos é executado em simultâneo.

### <a name="azure-portal"></a>Portal do Azure

+ **Novos recursos**
  + Experiência de editor para relatórios de tabela nova arrastar e soltar. Os utilizadores podem arrastar uma coluna do poço para a área de tabela em que será apresentada uma pré-visualização da tabela. As colunas podem ser reorganizadas.
  + Novo Visualizador de arquivos de registos
  + Links para experimentar execuções, computação, modelos, imagens e implantações do separador de atividades

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>SDK v1.0.15 de preparação de dados do Azure Machine Learning

+ **Novos recursos**
  + Agora suporta escrever fluxos a partir de um fluxo de dados de ficheiros de preparação de dados. Também fornece a capacidade de manipular os nomes de fluxo de arquivo para criar novos nomes de ficheiro.
    + Guia de procedimentos: [Bloco de notas de trabalhar com fluxos de arquivos](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Melhorias e correções de erros**
  + Desempenho melhorado de t-Digest em grandes conjuntos de dados.
  + Preparação de dados suporta agora a leitura de dados de um DataPath.
  + Agora é uma codificação de acesso frequente funciona em colunas booliana e numéricas.
  + Outras diversas correções de bugs.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Azure Machine Learning SDK for Python v1.0.15

+ **Novos recursos**
  + O Azure Machine Learning Pipelines adicionado AzureBatchStep ([bloco de notas](https://aka.ms/pl-azbatch)), HyperDriveStep (bloco de notas) e a funcionalidade de agendamento baseados no tempo ([bloco de notas](https://aka.ms/pl-schedule)).
  +  DataTranferStep atualizado para trabalhar com o servidor SQL do Azure e base de dados do Azure para PostgreSQL ([bloco de notas](https://aka.ms/pl-data-trans)).

+ **Alterações**
  + Preterida `PublishedPipeline.get_published_pipeline` aposentado `PublishedPipeline.get`.
  + Preterida `Schedule.get_schedule` aposentado `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>SDK v1.0.12 de preparação de dados do Azure Machine Learning

+ **Novos recursos**
  + Dados Prep agora suporta leitura a partir de uma base de dados SQL do Azure com o arquivo de dados.
 
+ **Alterações**
  + Melhorámos o desempenho de memória de determinadas operações nos dados de grande dimensão.
  + `read_pandas_dataframe()` agora requer `temp_folder` seja especificado.
  + O `name` propriedade no `ColumnProfile` foi preterido – utilizar `column_name` em vez disso.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK for Python v1.0.10

+ **Alterações**: 
  + SDK do Azure ML já não tem a dependência de pacotes de cli do azure. Especificamente, as dependências do azure-cli-core e do azure-cli-perfil foram removidas do azureml-core. Estes são o utilizador que afetem as alterações:
    + Se estiver a executar "início de sessão az" e, em seguida, utilizar o sdk do azureml, o SDK irá fazer o registo de código do browser ou dispositivo de mais uma vez. Ele não usa nenhum Estado de credenciais criado por "início de sessão az".
    + Para a autenticação de CLI do Azure, como o uso de "início de sessão az" usar _azureml.core.authentication.AzureCliAuthentication_ classe. Para a autenticação de CLI do Azure, fazer _cli do azure a instalação do pip_ no ambiente de Python em que instalou o sdk do azureml.
    + Se estiver fazendo "az login" com um principal de serviço para a automatização, recomendamos que utilize _azureml.core.authentication.ServicePrincipalAuthentication_ de classe, como o sdk do azureml não utiliza o estado de credenciais criado pela CLI do azure. 

+ **Correções de erros**: Esta versão contém pequenas correções de erros

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>SDK v1.0.8 de preparação de dados do Azure Machine Learning

+ **Correções de erros**
  + Melhorámos o desempenho de obter os perfis de dados.
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
  + Melhorias do arquivo de dados (documentados em [arquivo de dados procedimentos-to-guia](https://aka.ms/aml-data-prep-datastore-nb))
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
