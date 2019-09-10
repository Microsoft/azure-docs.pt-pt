---
title: Criar e usar destinos de computação para a preparação de modelos
titleSuffix: Azure Machine Learning service
description: Configure os ambientes de treinamento (destinos de computação) para treinamento de modelo do Machine Learning. Você pode alternar facilmente entre ambientes de treinamento. Inicie o treinamento localmente. Se você precisar escalar horizontalmente, alterne para um destino de computação baseado em nuvem.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 06/12/2019
ms.custom: seodec18
ms.openlocfilehash: 0a34ccf5201b81a2c74c2eccd0ec3f311a1158ab
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860535"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>Configurar e usar destinos de computação para treinamento de modelo 

Com o serviço Azure Machine Learning, você pode treinar seu modelo em uma variedade de recursos ou ambientes, coletivamente chamados de [__destinos de computação__](concept-azure-machine-learning-architecture.md#compute-targets). Um destino de computação pode ser um computador local ou um recurso de nuvem, como um Azure Machine Learning computação, Azure HDInsight ou uma máquina virtual remota.  Você também pode criar destinos de computação para implantação de modelo, conforme descrito em ["onde e como implantar seus modelos"](how-to-deploy-and-where.md).

Você pode criar e gerenciar um destino de computação usando o SDK do Azure Machine Learning, portal do Azure, sua página de aterrissagem do espaço de trabalho (versão prévia), CLI do Azure ou Azure Machine Learning VS Code extensão. Se tiver de destinos de computação que foram criados por meio de outro serviço (por exemplo, um cluster do HDInsight), pode utilizá-los ao anexá-las para a área de trabalho do serviço do Azure Machine Learning.
 
Neste artigo, você aprenderá a usar vários destinos de computação para treinamento de modelo.  As etapas para todos os destinos de computação seguem o mesmo fluxo de trabalho:
1. __Crie__ um destino de computação se você ainda não tiver um.
2. __Anexe__ o destino de computação ao seu espaço de trabalho.
3. __Configure__ o destino de computação para que ele contenha o ambiente Python e as dependências de pacote necessárias para seu script.


>[!NOTE]
> O código deste artigo foi testado com Azure Machine Learning SDK versão 1.0.39.

## <a name="compute-targets-for-training"></a>Destinos de computação para treinamento

O serviço de Azure Machine Learning tem suporte variado entre diferentes destinos de computação. Inicia um ciclo de vida de desenvolvimento do modelo típico com dev/experimentação numa pequena quantidade de dados. Nesta fase, recomendamos que utilize um ambiente local. Por exemplo, seu computador local ou uma VM com base na cloud. À medida que aumentar verticalmente o seu treinamento em conjuntos de dados maiores ou fazer o treinamento distribuído, recomendamos que utilize a computação do Azure Machine Learning para criar um cluster único ou vários node que é dimensionado automaticamente sempre que submete uma execução. Também pode anexar seus próprios recursos de computação, embora o suporte para vários cenários podem variar como detalhadas abaixo:

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Azure Machine Learning computação pode ser criada como um recurso persistente ou criada dinamicamente quando você solicita uma execução. A criação baseada em execução remove o destino de computação após a conclusão da execução de treinamento, portanto, não é possível reutilizar os destinos de computação criados dessa maneira.

## <a name="whats-a-run-configuration"></a>O que é uma configuração de execução?

Ao treinar, é comum iniciar em seu computador local e, posteriormente, executar esse script de treinamento em um destino de computação diferente. Com o serviço Azure Machine Learning, você pode executar o script em vários destinos de computação sem precisar alterar o script.

Tudo o que você precisa fazer é definir o ambiente para cada destino de computação em uma **configuração de execução**.  Em seguida, quando você quiser executar o teste de treinamento em um destino de computação diferente, especifique a configuração de execução para essa computação. Para obter detalhes de como especificar um ambiente e associá-lo para executar a configuração, consulte [criar e gerenciar ambientes para treinamento e implantação](how-to-use-environments.md).

Saiba mais sobre como [Enviar experimentos](#submit) no final deste artigo.

## <a name="whats-an-estimator"></a>O que é um estimador?

Para facilitar o treinamento de modelo usando estruturas populares, o SDK do Python Azure Machine Learning fornece uma abstração alternativa de nível superior, a classe do estimador. Essa classe permite que você construa facilmente configurações de execução. Você pode criar e usar um [estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) genérico para enviar scripts de treinamento que usam qualquer estrutura de aprendizado que você escolher (como scikit-learn).

Para as tarefas PyTorch, TensorFlow e Chainer, Azure Machine Learning também fornece os respectivos estimadores [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) para simplificar o uso dessas estruturas.

Para obter mais informações, consulte [treinar modelos de ml com estimadores](how-to-train-ml-models.md).

## <a name="whats-an-ml-pipeline"></a>O que é um pipeline de ML?

Com pipelines de ML, você pode otimizar seu fluxo de trabalho com simplicidade, velocidade, portabilidade e reutilização. Ao criar pipelines com Azure Machine Learning, você pode se concentrar em sua experiência, aprendizado de máquina, em vez de infraestrutura e automação.

Os pipelines ML são construídos a partir de várias **etapas**, que são unidades computacionais distintas no pipeline. Cada etapa pode ser executada de forma independente e usar recursos de computação isolados. Isso permite que vários cientistas de dados funcionem no mesmo pipeline ao mesmo tempo sem sobrecarregar recursos de computação, além de facilitar o uso de diferentes tipos/tamanhos de computação para cada etapa.

> [!TIP]
> Os pipelines ML podem usar a configuração de execução ou os estimadores ao treinar modelos.

Embora os pipelines ML possam treinar modelos, eles também podem preparar dados antes de treinar e implantar modelos após o treinamento. Um dos principais casos de uso para pipelines é a pontuação em lote. Para obter mais informações, [consulte pipelines: Otimize os fluxos de trabalho](concept-ml-pipelines.md)do Machine Learning.

## <a name="set-up-in-python"></a>Configurar no Python

Use as seções abaixo para configurar esses destinos de computação:

* [Computador local](#local)
* [Computação do Azure Machine Learning](#amlcompute)
* [Máquinas virtuais remotas](#vm)
* [O Azure HDInsight](#hdinsight)


### <a id="local"></a>Computador local

1. **Criar e anexar**: Não é necessário criar ou anexar um destino de computação para usar o computador local como o ambiente de treinamento.  

1. **Configurar**:  Quando você usa seu computador local como um destino de computação, o código de treinamento é executado em seu [ambiente de desenvolvimento](how-to-configure-environment.md).  Se esse ambiente já tiver os pacotes python de que você precisa, use o ambiente gerenciado pelo usuário.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Agora que você anexou a computação e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](#submit).

### <a id="amlcompute"></a>Computação do Azure Machine Learning

Azure Machine Learning computação é uma infraestrutura de computação gerenciada que permite ao usuário criar facilmente uma computação de vários nós ou um único nó. A computação é criada dentro de sua região de espaço de trabalho como um recurso que pode ser compartilhado com outros usuários em seu espaço de trabalho. A computação é dimensionada automaticamente quando um trabalho é enviado e pode ser colocada em uma rede virtual do Azure. A computação é executada em um ambiente em contêiner e empacota suas dependências de modelo em um [contêiner do Docker](https://www.docker.com/why-docker).

Pode usar a computação do Azure Machine Learning para distribuir o processo de treinamento em clusters de nós de computação de CPU ou de GPU na cloud. Para obter mais informações sobre os tamanhos de VM que incluem GPUs, consulte [tamanhos de máquina virtual com otimização de GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Azure Machine Learning computação tem limites padrão, como o número de núcleos que podem ser alocados. Para obter mais informações, consulte [gerenciar e solicitar cotas para recursos do Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).


Você pode criar um ambiente de computação Azure Machine Learning sob demanda ao agendar uma execução ou como um recurso persistente.

#### <a name="run-based-creation"></a>Criação baseados em execução

Você pode criar Azure Machine Learning computação como um destino de computação em tempo de execução. A computação é criada automaticamente para sua execução. A computação é excluída automaticamente quando a execução é concluída. 

> [!NOTE]
> Para especificar o número máximo de nós a serem usados, você normalmente definiria `node_count` como o número de nós. Atualmente, há (04/04/2019) um bug que impede que isso funcione. Como alternativa, use a `amlcompute._cluster_max_node_count` propriedade da configuração de execução. Por exemplo, `run_config.amlcompute._cluster_max_node_count = 5`.

> [!IMPORTANT]
> A criação baseada em execução do Azure Machine Learning computação está atualmente em visualização. Não use a criação baseada em execução se usar o ajuste de hiperparâmetro automatizado ou o aprendizado de máquina automatizado. Para usar o ajuste de hiperparâmetro ou o aprendizado de máquina automatizado, crie um destino de [computação persistente](#persistent) em vez disso.

1.  **Criar, anexar e configurar**: A criação baseada em execução executa todas as etapas necessárias para criar, anexar e configurar o destino de computação com a configuração de execução.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Agora que você anexou a computação e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](#submit).

#### <a id="persistent"></a>Computação persistente

Uma computação persistente de Azure Machine Learning pode ser reutilizada entre trabalhos. A computação pode ser compartilhada com outros usuários no espaço de trabalho e é mantida entre os trabalhos.

1. **Criar e anexar**: Para criar um recurso de computação persistente Azure Machine Learning em Python, especifique as propriedades **vm_size** e **max_nodes** . Azure Machine Learning, em seguida, usa padrões inteligentes para as outras propriedades. A computação é ajustada à escala vertical para zero nós quando ela não é usada.   As VMs dedicadas são criadas para executar seus trabalhos conforme necessário.
    
    * **vm_size**: A família de VMs dos nós criados por Azure Machine Learning computação.
    * **max_nodes**: O número máximo de nós para fazer o dimensionamento automático para quando você executa um trabalho em Azure Machine Learning computação.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Você também pode configurar várias propriedades avançadas ao criar Azure Machine Learning computação. As propriedades permitem que você crie um cluster persistente de tamanho fixo ou dentro de um Entrada na Rede virtual do Azure existente em sua assinatura.  Consulte a [classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) AmlCompute para obter detalhes.
    
   Ou você pode criar e anexar um recurso de computação persistente Azure Machine Learning [no portal do Azure](#portal-create).

1. **Configurar**: Crie uma configuração de execução para o destino de computação persistente.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Agora que você anexou a computação e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](#submit).


### <a id="vm"></a>Máquinas virtuais remotas

O Azure Machine Learning também suporta a colocarem os seus próprios recursos de computação e ligá-la à sua área de trabalho. Um desses tipos de recurso é uma VM remota arbitrária, desde que seja acessível do serviço Azure Machine Learning. O recurso pode ser uma VM do Azure, um servidor remoto em sua organização ou local. Especificamente, dado o endereço IP e as credenciais (nome de usuário e senha ou chave SSH), você pode usar qualquer VM acessível para execuções remotas.

Pode utilizar um ambiente de sistema criado conda, um ambiente de Python já existente ou um contentor do Docker. Para executar em um contêiner do Docker, você deve ter um mecanismo do Docker em execução na VM. Esta funcionalidade é especialmente útil quando pretender que um ambiente de dev/experimentação mais flexível, com base na cloud que o seu computador local.

Use o Máquina Virtual de Ciência de Dados do Azure (DSVM) como a VM do Azure escolhida para esse cenário. Essa VM é um ambiente de desenvolvimento pré-configurado de ciência de dados e de ia no Azure. A VM oferece uma opção organizada de ferramentas e estruturas para desenvolvimento de aprendizado de máquina em todo o ciclo de vida. Para obter mais informações sobre como usar o DSVM com Azure Machine Learning, consulte [configurar um ambiente de desenvolvimento](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

1. **Criar**: Crie um DSVM antes de usá-lo para treinar seu modelo. Para criar esse recurso, consulte [provisionar o máquina virtual de ciência de dados para Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Azure Machine Learning só dá suporte a máquinas virtuais que executam o Ubuntu. Ao criar uma VM ou escolher uma VM existente, você deve selecionar uma VM que usa o Ubuntu.

1. **Anexar**: Para anexar uma máquina virtual existente como um destino de computação, você deve fornecer o FQDN (nome de domínio totalmente qualificado), o nome de usuário e a senha para a máquina virtual. No exemplo, substitua \<o FQDN > pelo FQDN público da VM ou pelo endereço IP público. Substitua \<username > e \<password > pelo nome de usuário e senha ssh para a VM.

   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   attach_config = RemoteCompute.attach_configuration(address = "<fqdn>",
                                                    ssh_port=22,
                                                    username='<username>',
                                                    password="<password>")

   # If you authenticate with SSH keys instead, use this code:
   #                                                  ssh_port=22,
   #                                                  username='<username>',
   #                                                  password=None,
   #                                                  private_key_file="<path-to-file>",
   #                                                  private_key_passphrase="<passphrase>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Ou você pode anexar o DSVM ao seu espaço de trabalho [usando o portal do Azure](#portal-reuse).

1. **Configurar**: Crie uma configuração de execução para o destino de computação DSVM. O Docker e o Conda são usados para criar e configurar o ambiente de treinamento no DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Agora que você anexou a computação e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](#submit).

### <a id="hdinsight"></a>O Azure HDInsight 

O Azure HDInsight é uma plataforma popular para análise de Big Data. A plataforma fornece Apache Spark, que pode ser usada para treinar seu modelo.

1. **Criar**:  Crie o cluster HDInsight antes de usá-lo para treinar seu modelo. Para criar um Spark no cluster HDInsight, consulte [criar um cluster Spark no hdinsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    Ao criar o cluster, você deve especificar um nome de usuário e uma senha SSH. Anote esses valores, pois você precisa que eles usem o HDInsight como um destino de computação.
    
    Após a criação do cluster, conecte-se a ele com \<o nome de host ClusterName > \<-SSH.azurehdinsight.net, em que ClusterName > é aquele que você forneceu para o cluster. 

1. **Anexar**: Para anexar um cluster do HDInsight como um destino de computação, você deve fornecer o nome de host, o nomes de usuário e a senha para o cluster HDInsight. O exemplo seguinte utiliza o SDK para anexar um cluster a sua área de trabalho. No exemplo, substitua \<ClusterName > pelo nome do cluster. Substitua \<username > e \<password > pelo nome de usuário e senha ssh para o cluster.

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase
    attach_config = HDInsightCompute.attach_configuration(address='<clustername>-ssh.azureinsight.net', 
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   Ou você pode anexar o cluster HDInsight ao seu espaço de trabalho [usando o portal do Azure](#portal-reuse).

1. **Configurar**: Crie uma configuração de execução para o destino de computação HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Agora que você anexou a computação e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](#submit).


### <a id="azbatch"></a>Lote do Azure 

O lote do Azure é usado para executar aplicativos de HPC (computação de alto desempenho) e paralelos em grande escala com eficiência na nuvem. AzureBatchStep pode ser usado em um pipeline Azure Machine Learning para enviar trabalhos para um pool de computadores do lote do Azure.

Para anexar o lote do Azure como um destino de computação, você deve usar o SDK do Azure Machine Learning e fornecer as seguintes informações:

-   **Nome de computação do lote do Azure**: Um nome amigável a ser usado para a computação no espaço de trabalho
-   **Nome da conta do lote do Azure**: O nome da conta do lote do Azure
-   **Grupo de recursos**: O grupo de recursos que contém a conta do lote do Azure.

O código a seguir demonstra como anexar o lote do Azure como um destino de computação:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

## <a name="set-up-in-azure-portal"></a>Configurar no portal do Azure

Você pode acessar os destinos de computação associados ao seu espaço de trabalho no portal do Azure.  Você pode usar o portal para:

* [Exibir destinos de computação](#portal-view) anexados ao seu espaço de trabalho
* [Criar um destino de computação](#portal-create) em seu espaço de trabalho
* [Anexar um destino de computação](#portal-reuse) que foi criado fora do espaço de trabalho


Depois que um destino é criado e anexado ao seu espaço de trabalho, você o usará em sua configuração `ComputeTarget` de execução com um objeto: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>Exibir destinos de computação


Para ver os destinos de computação para seu espaço de trabalho, use as seguintes etapas:

1. Navegue até a [portal do Azure](https://portal.azure.com) e abra seu espaço de trabalho. Você também pode acessar essas mesmas etapas na [página de aterrissagem do espaço de trabalho (versão prévia)](https://ml.azure.com), embora as imagens abaixo mostrem o portal do Azure.
 
1. Em __aplicativos__, selecione __computação__.

    [![Exibir guia de computação](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a id="portal-create"></a>Criar um destino de computação

Siga as etapas anteriores para exibir a lista de destinos de computação. Em seguida, use estas etapas para criar um destino de computação: 

1. Selecione o sinal de adição (+) para adicionar um destino de computação.

    ![Adicionar um destino de computação](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Insira um nome para o destino de computação. 

1. Selecione **computação do Machine Learning** como o tipo de computação a ser usado para __treinamento__. 

    >[!NOTE]
    >Azure Machine Learning computação é o único recurso de computação gerenciada que você pode criar na portal do Azure.  Todos os outros recursos de computação podem ser anexados depois de criados.

1. Preencha o formulário. Forneça valores para as propriedades necessárias, especialmente a **família de VMs**e o máximo de **nós** a serem usados para criar a computação.  

1. Selecione __Criar__.


1. Exiba o status da operação de criação selecionando o destino de computação na lista:

    ![Selecione um destino de computação para exibir o status da operação de criação](./media/how-to-set-up-training-targets/View_list.png)

1. Em seguida, você verá os detalhes do destino de computação: 

    ![Exibir os detalhes de destino do computador](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a id="portal-reuse"></a>Anexar destinos de computação

Para usar os destinos de computação criados fora do espaço de trabalho do Azure Machine Learning Service, você deve anexá-los. Anexar um destino de computação torna-o disponível para seu espaço de trabalho.

Siga as etapas descritas anteriormente para exibir a lista de destinos de computação. Em seguida, use as seguintes etapas para anexar um destino de computação: 

1. Selecione o sinal de adição (+) para adicionar um destino de computação. 
1. Insira um nome para o destino de computação. 
1. Selecione o tipo de computação a ser anexado para __treinamento__:

    > [!IMPORTANT]
    > Nem todos os tipos de computação podem ser anexados do portal do Azure. Os tipos de computação que podem ser anexados atualmente para treinamento incluem:
    >
    > * Uma VM remota
    > * Azure Databricks (para uso em pipelines de aprendizado de máquina)
    > * Azure Data Lake Analytics (para uso em pipelines de aprendizado de máquina)
    > * Azure HDInsight

1. Preencha o formulário e forneça valores para as propriedades necessárias.

    > [!NOTE]
    > A Microsoft recomenda que você use chaves SSH, que são mais seguras do que senhas. As senhas são vulneráveis a ataques de força bruta. As chaves SSH dependem de assinaturas criptográficas. Para obter informações sobre como criar chaves SSH para uso com máquinas virtuais do Azure, consulte os seguintes documentos:
    >
    > * [Criar e utilizar chaves SSH no Linux ou macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Criar e utilizar chaves SSH no Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Selecione __anexar__. 
1. Exiba o status da operação de anexação selecionando o destino de computação na lista.

## <a name="set-up-with-cli"></a>Configurar com a CLI

Você pode acessar os destinos de computação associados ao seu espaço de trabalho usando a [extensão da CLI](reference-azure-machine-learning-cli.md) para o serviço Azure Machine Learning.  Você pode usar a CLI para:

* Criar um destino de computação gerenciado
* Atualizar um destino de computação gerenciado
* Anexar um destino de computação não gerenciado

Para obter mais informações, consulte [Gerenciamento de recursos](reference-azure-machine-learning-cli.md#resource-management).

## <a name="set-up-with-vs-code"></a>Configurar com VS Code

Você pode acessar, criar e gerenciar os destinos de computação associados ao seu espaço de trabalho usando a [extensão de vs Code](how-to-vscode-tools.md#create-and-manage-compute-targets) para Azure Machine Learning Service.

## <a id="submit"></a>Enviar execução de treinamento usando Azure Machine Learning SDK

Depois de criar uma configuração de execução, use-a para executar o experimento.  O padrão de código para enviar uma execução de treinamento é o mesmo para todos os tipos de destinos de computação:

1. Criar um experimento para executar
1. Submeta a execução.
1. Aguarde a execução concluir.

> [!IMPORTANT]
> Quando você envia a execução de treinamento, um instantâneo do diretório que contém seus scripts de treinamento é criado e enviado para o destino de computação. Ele também é armazenado como parte do experimento em seu espaço de trabalho. Se você alterar os arquivos e enviar a execução novamente, somente os arquivos alterados serão carregados.
>
> Para impedir que arquivos sejam incluídos no instantâneo, crie um arquivo [. gitignore](https://git-scm.com/docs/gitignore) ou `.amlignore` no diretório e adicione os arquivos a ele. O `.amlignore` arquivo usa a mesma sintaxe e padrões que o arquivo [. gitignore](https://git-scm.com/docs/gitignore) . Se ambos os arquivos existirem `.amlignore` , o arquivo terá precedência.
> 
> Para obter mais informações, [](concept-azure-machine-learning-architecture.md#snapshots)consulte instantâneos.

### <a name="create-an-experiment"></a>Criar uma experimentação

Primeiro, crie um experimento em seu espaço de trabalho.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Submeter a experimentação

Envie o experimento com um `ScriptRunConfig` objeto.  Esse objeto inclui:

* **pasta_de_origem**: O diretório de origem que contém o script de treinamento
* **script**: Identificar o script de treinamento
* **run_config**: A configuração de execução, que, por sua vez, define onde o treinamento ocorrerá.

Por exemplo, para usar [a configuração de destino local](#local) :

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Alterne o mesmo experimento para ser executado em um destino de computação diferente usando uma configuração de execução diferente, como o [destino amlcompute](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> Este exemplo usa como padrão apenas um nó do destino de computação para treinamento. Para usar mais de um nó, defina a `node_count` configuração de execução para o número de nós desejado. Por exemplo, o código a seguir define o número de nós usados para treinamento para quatro:
>
> ```python
> src.run_config.node_count = 4
> ```

Ou você pode:

* Envie o experimento com um `Estimator` objeto, conforme mostrado em [treinar modelos ml com estimações](how-to-train-ml-models.md).
* Envie uma execução HyperDrive para [ajuste de hiperparâmetro](how-to-tune-hyperparameters.md).
* Envie um experimento por meio da [extensão vs Code](how-to-vscode-tools.md#train-and-tune-models).

Para obter mais informações, consulte a documentação do [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) e do [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) .

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Criar configuração de execução e enviar execução usando Azure Machine Learning CLI

Você pode usar [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e [Machine Learning extensão da CLI](reference-azure-machine-learning-cli.md) para criar configurações de execução e enviar execuções em diferentes destinos de computação. Os exemplos a seguir pressupõem que você tenha um Workspace do Azure Machine Learning existente e que tenha feito logon no `az login` Azure usando o comando da CLI. 

### <a name="create-run-configuration"></a>Criar configuração de execução

A maneira mais simples de criar a configuração de execução é navegar na pasta que contém os scripts do Python do Machine Learning e usar o comando da CLI

```azurecli
az ml folder attach
```

Este comando cria uma subpasta `.azureml` que contém arquivos de configuração de execução de modelo para destinos de computação diferentes. Você pode copiar e editar esses arquivos para personalizar sua configuração, por exemplo, para adicionar pacotes do Python ou alterar as configurações do Docker.  

### <a name="structure-of-run-configuration-file"></a>Estrutura do arquivo de configuração de execução

O arquivo de configuração de execução é YAML formatado, com as seções a seguir
 * O script a ser executado e seus argumentos
 * Nome de destino de computação, "local" ou nome de uma computação no espaço de trabalho.
 * Parâmetros para executar a execução: Framework, Communicator para execuções distribuídas, duração máxima e número de nós de computação.
 * Seção de ambiente. Consulte [criar e gerenciar ambientes para treinamento e implantação](how-to-use-environments.md) para obter detalhes dos campos nesta seção.
   * Para especificar os pacotes do Python a serem instalados para a execução, crie o [arquivo de ambiente Conda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)e defina o campo __condaDependenciesFile__ .
 * Execute os detalhes do histórico para especificar a pasta do arquivo de log e para habilitar ou desabilitar a coleta de saída e instantâneos de histórico de execução.
 * Detalhes de configuração específicos para a estrutura selecionada.
 * Referência de dados e detalhes do armazenamento de dados.
 * Detalhes de configuração específicos para Computação do Machine Learning para criar um novo cluster.

### <a name="create-an-experiment"></a>Criar uma experimentação

Primeiro, crie um experimento para suas execuções

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>Execução de script

Para enviar uma execução de script, execute um comando

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>HyperDrive executar

Você pode usar HyperDrive com CLI do Azure para executar as execuções de ajuste de parâmetro. Primeiro, crie um arquivo de configuração HyperDrive no formato a seguir. Consulte [ajustar hiperparâmetros para seu](how-to-tune-hyperparameters.md) artigo de modelo para obter detalhes sobre parâmetros de ajuste de hiperparâmetro.

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Adicione esse arquivo junto com os arquivos de configuração de execução. Em seguida, envie uma execução de HyperDrive usando:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Observe a seção *argumentos* em runconfig e o *espaço de parâmetro* em hyperdrive config. Eles contêm os argumentos de linha de comando a serem passados para o script de treinamento. O valor em runconfig permanece o mesmo para cada iteração, enquanto o intervalo na configuração de HyperDrive é iterado. Não especifique o mesmo argumento em ambos os arquivos.

Para obter mais detalhes sobre ```az ml``` esses comandos da CLI e conjunto completo de argumentos, consulte [a documentação de referência](reference-azure-machine-learning-cli.md).

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Acompanhamento e integração de git

Quando você inicia uma execução de treinamento onde o diretório de origem é um repositório git local, as informações sobre o repositório são armazenadas no histórico de execuções. Por exemplo, a ID de confirmação atual para o repositório é registrada como parte do histórico.

## <a name="notebook-examples"></a>Exemplos de notebook

Consulte estes blocos de anotações para obter exemplos de treinamento com vários destinos de computação:
* [procedimentos-to-use-azureml/treinamento](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutoriais/img-classificação-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos Seguintes

* [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md) usa um destino de computação gerenciado para treinar um modelo.
* Saiba como [ajustar os hiperparâmetros com eficiência](how-to-tune-hyperparameters.md) para criar modelos melhores.
* Depois de ter um modelo treinado, saiba [como e onde implantar modelos](how-to-deploy-and-where.md).
* Exiba a referência do SDK da [classe RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) .
* [Usar o serviço de Azure Machine Learning com redes virtuais do Azure](how-to-enable-virtual-network.md)
