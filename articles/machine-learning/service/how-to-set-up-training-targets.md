---
title: Criar e usar destinos de computação para a preparação de modelos
titleSuffix: Azure Machine Learning service
description: Configure os ambientes de treinamento (destinos de computação) para a preparação de modelos de aprendizagem automática. Pode alternar facilmente entre ambientes de treinamento. Começar a formação localmente. Se precisar de aumentar horizontalmente, mude para um destino de computação com base na cloud.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/07/2019
ms.custom: seodec18
ms.openlocfilehash: a549a46912b0d60f878a18cae1e70a763afc0243
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56874703"
---
# <a name="set-up-compute-targets-for-model-training"></a>Configurar destinos de computação de preparação de modelos

Com o serviço do Azure Machine Learning, pode preparar o seu modelo numa variedade de recursos ou ambientes, coletivamente denominados [ __destinos de computação__](concept-azure-machine-learning-architecture.md#compute-target). Um destino de computação pode ser um computador local ou um recurso da nuvem, como uma computação do Azure Machine Learning, Azure HDInsight ou uma máquina virtual remota.  Também pode criar destinos de computação para a implementação de modelo conforme descrito em ["onde e como implementar os seus modelos"](how-to-deploy-and-where.md).

Pode criar e gerir um destino de computação com o SDK do Azure Machine Learning, o portal do Azure ou a CLI do Azure. Se tiver de destinos de computação que foram criados por meio de outro serviço (por exemplo, um cluster do HDInsight), pode utilizá-los ao anexá-las para a área de trabalho do serviço do Azure Machine Learning.
 
Neste artigo, irá aprender a utilizar vários destinos de computação para a preparação de modelos.  Os passos para todos os destinos de computação siga o mesmo fluxo de trabalho:
1. __Criar__ um destino de computação se ainda não tiver uma.
2. __Anexar__ o destino de computação à sua área de trabalho.
3. __Configurar__ a computação de destino para que ele contém as dependências de ambiente e o pacote de Python necessárias ao seu script.


>[!NOTE]
> Código neste artigo foi testado com o Azure Machine Learning SDK versão 1.0.6.

## <a name="compute-targets-for-training"></a>Destinos de computação para formação

O serviço do Azure Machine Learning tem suporte variado em destinos de computação diferentes. Inicia um ciclo de vida de desenvolvimento do modelo típico com dev/experimentação numa pequena quantidade de dados. Nesta fase, recomendamos que utilize um ambiente local. Por exemplo, seu computador local ou uma VM com base na cloud. À medida que aumentar verticalmente o seu treinamento em conjuntos de dados maiores ou fazer o treinamento distribuído, recomendamos que utilize a computação do Azure Machine Learning para criar um cluster único ou vários node que é dimensionado automaticamente sempre que submete uma execução. Também pode anexar seus próprios recursos de computação, embora o suporte para vários cenários podem variar como detalhadas abaixo:


|Destino de treinamento de computação| Aceleração por GPU | Automatizada<br/> a otimização de hiper-parâmetros | Automatizada</br> machine learning | O Azure Machine Learning Pipelines |
|----|:----:|:----:|:----:|:----:|
|[Computador local](#local)| Talvez | &nbsp; | ✓ | &nbsp; |
|[Computação do Azure Machine Learning](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[VM remota](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](how-to-create-your-first-pipeline.md#databricks)| &nbsp; | &nbsp; | ✓ | ✓ |
|[Azure Data Lake Analytics](how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | &nbsp; | ✓ |
|[O Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |
|[Azure Batch](#azbatch)| &nbsp; | &nbsp; | &nbsp; | ✓ |

**Todos os computação destinos podem ser reutilizados para várias tarefas de formação**. Por exemplo, depois de anexar uma VM remota à área de trabalho, pode reutilizá-lo para várias tarefas.

> [!NOTE]
> Computação do Azure Machine Learning pode ser criado como um recurso persistente ou criado dinamicamente quando solicita uma execução. Criação baseados em execução remove o destino de computação, após a execução de treinamento estiver concluída, pelo que não é possível reutilizar os destinos de computação criados dessa forma.

## <a name="whats-a-run-configuration"></a>O que é uma configuração de execução?

Quando o treinamento, é comum para iniciar no seu computador local e, posteriormente, executar esse script de treinamento num destino de computação diferentes. Com o serviço do Azure Machine Learning, pode executar o script em vários destinos de computação sem ter de alterar o seu script. 

Tudo o que precisa fazer é definir o ambiente para cada destino de computação com um **execute a configuração**.  Em seguida, quando deseja executar a experimentação de preparação num destino de computação diferente, especifique a configuração de execução para esse computação. 

Saiba mais sobre [submeter experimentações](#submit) no final deste artigo.

### <a name="manage-environment-and-dependencies"></a>Gerir o ambiente e dependências

Quando cria uma configuração de execução, terá de decidir como gerir o ambiente e dependências no destino de computação. 

#### <a name="system-managed-environment"></a>Ambiente de sistema gerido

Utilizar um ambiente de sistema gerido quando desejar [Conda](https://conda.io/docs/) para gerenciar o ambiente de Python e as dependências de script para. Um ambiente de sistema gerido é assumido por predefinição e a escolha mais comum. É útil em destinos de computação remoto, especialmente quando não é possível configurar esse destino. 

Tudo o que precisa fazer é especificar cada dependência de pacote com o [classe CondaDependency](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) Conda, em seguida, cria um arquivo chamado **conda_dependencies.yml** no **aml_config** diretório na sua área de trabalho com sua lista de dependências de pacote e configura o seu ambiente de Python ao submeter a experimentação de preparação. 

A configuração inicial de um novo ambiente pode demorar vários minutos, consoante o tamanho das dependências necessárias. Desde que a lista de pacotes se mantiver inalterada, o tempo de configuração ocorre apenas uma vez.
  
O código seguinte mostra um exemplo para um ambiente de gerenciados pelo sistema de mensagens em fila que requerem scikit-Saiba mais:
    
[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_system_managed)]

#### <a name="user-managed-environment"></a>Ambiente gerenciado por utilizador

Para um ambiente de utilizador gerido, é responsável por configurar o ambiente e a instalação de cada pacote tem do script de treinamento no destino de computação. Se já estiver configurado seu ambiente de treinamento (como no seu computador local), pode ignorar o passo de configuração, definindo `user_managed_dependencies` como True. Conda não irá verificar o seu ambiente ou instalar nada para.

O código seguinte mostra um exemplo de configuração de execuções de preparação para um ambiente de utilizador gerido:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_user_managed)]
  
## <a name="set-up-compute-targets-with-python"></a>Configurar destinos de computação com Python

Utilize as secções abaixo para configurar estes destinos de computação:

* [Computador local](#local)
* [Computação do Azure Machine Learning](#amlcompute)
* [Máquinas virtuais remotas](#vm)
* [O Azure HDInsight](#hdinsight)


### <a id="local"></a>Computador local

1. **Criar e anexar**: Não é necessário para criar ou anexar um destino de computação para utilizar o seu computador local como ambiente de treinamento.  

1. **Configurar**:  Quando utilizar o seu computador local como um destino de computação, o código de treinamento é executado no seu [ambiente de desenvolvimento](how-to-configure-environment.md).  Se esse ambiente já tiver os pacotes de Python que precisa, utilize o ambiente de utilizador gerido.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Agora que já anexados a computação e configurado a sua execução, a próxima etapa é [submeter a execução de treinamento](#submit).

### <a id="amlcompute"></a>Computação do Azure Machine Learning

Computação do Azure Machine Learning é uma infraestrutura de computação gerida que permite ao utilizador criar facilmente uma única ou vários nó de computação. A computação é criada na sua região de área de trabalho como um recurso que pode ser compartilhado com outros utilizadores na sua área de trabalho. A computação verticalmente automaticamente quando for submetida uma tarefa e pode ser colocada numa rede Virtual do Azure. A computação é executado num ambiente em contentores e empacota suas dependências de modelo num [contentor do Docker](https://www.docker.com/why-docker).

Pode usar a computação do Azure Machine Learning para distribuir o processo de treinamento em clusters de nós de computação de CPU ou de GPU na cloud. Para obter mais informações sobre os tamanhos VM que incluem as GPUs, consulte [tamanhos de máquinas de virtuais com otimização de GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Computação do Azure Machine Learning tem limites predefinidos, como o número de núcleos que podem ser alocados. Para obter mais informações, consulte [quotas de pedido e gerir recursos do Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).


Pode criar um ambiente de computação do Azure Machine Learning a pedido quando agendar uma execução ou como um recurso persistente.

#### <a name="run-based-creation"></a>Criação baseados em execução

Pode criar a computação do Azure Machine Learning como um destino de computação no tempo de execução. A computação é criada automaticamente para a execução. As escalas de cluster até ao número de **max_nodes** que especificou na sua configuração de execução. A computação é eliminada automaticamente depois da execução for concluída.

> [!IMPORTANT]
> Criação de computação do Azure Machine Learning baseados em execução está atualmente em pré-visualização. Não utilize a criação com base em execução, se utilizar a otimização de hiper-parâmetros automatizado ou automatizada de aprendizagem automática. Para utilizar a otimização de hiper-parâmetros ou aprendizagem automática, crie uma [computação persistente](#persistent) de destino em vez disso.

1.  **Criar, anexar e configurar**: A criação com base em execução executa todas as etapas necessárias para criar, anexar e configurar o destino de computação com a configuração de execução.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Agora que já anexados a computação e configurado a sua execução, a próxima etapa é [submeter a execução de treinamento](#submit).

#### <a id="persistent"></a>Computação persistente

Um persistente computação do Azure Machine Learning podem ser reutilizada em tarefas. A computação pode ser compartilhada com outros utilizadores na área de trabalho e é mantida entre tarefas.

1. **Criar e anexar**: Para criar um recurso de computação do Azure Machine Learning persistente em Python, especificar os **vm_size** e **max_nodes** propriedades. O Azure Machine Learning utiliza, em seguida, predefinições inteligentes para as outras propriedades. O é dimensionado de computação automaticamente, para baixo para zero nós quando não é utilizado.   São criadas VMs dedicadas para executar os trabalhos, conforme necessário.
    
    * **vm_size**: A família VM de nós criados pela computação do Azure Machine Learning.
    * **max_nodes**: O número máximo de nós para dimensionamento automático até ao executar uma tarefa na computação do Azure Machine Learning.
    
 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

  Também pode configurar várias propriedades avançadas ao criar a computação do Azure Machine Learning. As propriedades permitem-lhe criar um cluster persistente de tamanho fixo, ou dentro de uma rede Virtual do Azure existente na sua subscrição.  Consulte a [AmlCompute classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) para obter detalhes.
    
 Ou pode criar e anexar um recurso de computação do Azure Machine Learning persistente [no portal do Azure](#portal-create).

1. **Configurar**: Crie uma configuração de execução para o destino de computação persistente.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Agora que já anexados a computação e configurado a sua execução, a próxima etapa é [submeter a execução de treinamento](#submit).


### <a id="vm"></a>Máquinas virtuais remotas

O Azure Machine Learning também suporta a colocarem os seus próprios recursos de computação e ligá-la à sua área de trabalho. Um esse tipo de recurso é uma VM remota arbitrária, desde que esteja acessível a partir do serviço Azure Machine Learning. O recurso pode ser uma VM do Azure, um servidor remoto na sua organização ou no local. Especificamente, dado o endereço IP e as credenciais (nome de utilizador e palavra-passe ou chave SSH), pode usar qualquer VM acessível para execuções de remotas.

Pode utilizar um ambiente de sistema criado conda, um ambiente de Python já existente ou um contentor do Docker. Para executar num contentor do Docker, tem de ter um motor do Docker em execução na VM. Esta funcionalidade é especialmente útil quando pretender que um ambiente de dev/experimentação mais flexível, com base na cloud que o seu computador local.

Utilize a Máquina Virtual de ciência de dados do Azure (DSVM) como VM do Azure à escolha para este cenário. Esta VM é um ambiente de desenvolvimento de IA do Azure e de ciência de dados pré-configurados. A VM oferece uma opção organizada de ferramentas e estruturas para o ciclo de vida completo de machine learning de desenvolvimento. Para obter mais informações sobre como utilizar a DSVM com o Azure Machine Learning, consulte [configurar um ambiente de desenvolvimento](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

1. **Criar**: Crie uma DSVM antes de o utilizar para preparar o seu modelo. Para criar este recurso, veja [aprovisionar a máquina de Virtual de ciência de dados para Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > O Azure Machine Learning suporta apenas máquinas virtuais que executar o Ubuntu. Quando criar uma VM ou escolha uma VM existente, tem de selecionar uma VM que utiliza o Ubuntu.

1. **Anexar**: Para anexar uma máquina virtual existente como um destino de computação, tem de fornecer o nome de domínio completamente qualificado (FQDN), o nome de utilizador e a palavra-passe para a máquina virtual. No exemplo, substitua \<fqdn > com o FQDN público da VM ou o endereço IP público. Substitua \<nome de utilizador > e \<palavra-passe > com o nome de utilizador SSH e palavra-passe para a VM.

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

 Ou pode anexar a DSVM a sua área de trabalho [com o portal do Azure](#portal-reuse).

1. **Configurar**: Crie uma configuração de execução para o destino de computação DSVM. Docker e conda são utilizados para criar e configurar o ambiente de treinamento em DSVM.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Agora que já anexados a computação e configurado a sua execução, a próxima etapa é [submeter a execução de treinamento](#submit).

### <a id="hdinsight"></a>O Azure HDInsight 

O Azure HDInsight é uma plataforma popular para análise de macrodados. A plataforma fornece Apache Spark, que pode ser utilizado para preparar o seu modelo.

1. **Criar**:  Crie o cluster do HDInsight, antes de o utilizar para preparar o seu modelo. Para criar Spark num cluster do HDInsight, veja [criar um Cluster do Spark no HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    Quando criar o cluster, tem de especificar um nome de utilizador SSH e a palavra-passe. Tome nota destes valores, sempre que precisar para utilizar o HDInsight como um destino de computação.
    
    Depois do cluster for criado, ligá-lo com o nome do anfitrião \<clustername >-ssh.azurehdinsight.net, onde \<clustername > é o nome que indicou para o cluster. 

1. **Anexar**: Para anexar um cluster do HDInsight como um destino de computação, tem de fornecer o nome de anfitrião, nome de utilizador e palavra-passe para o cluster do HDInsight. O exemplo seguinte utiliza o SDK para anexar um cluster a sua área de trabalho. No exemplo, substitua \<clustername > com o nome do cluster. Substitua \<nome de utilizador > e \<palavra-passe > com o nome de utilizador SSH e palavra-passe para o cluster.

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

  Ou pode anexar o cluster do HDInsight para a área de trabalho [com o portal do Azure](#portal-reuse).

1. **Configurar**: Crie uma configuração de execução para o destino de computação do HDI. 

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Agora que já anexados a computação e configurado a sua execução, a próxima etapa é [submeter a execução de treinamento](#submit).


### <a id="azbatch"></a>O Azure Batch 

O Azure Batch é utilizado para executar aplicações de computação em larga escala paralela e de alto desempenho (HPC) de forma eficaz na cloud. AzureBatchStep pode ser utilizado num Pipeline do Azure Machine Learning para submeter tarefas para um conjunto do Azure Batch de máquinas.

Para anexar o Azure Batch como um destino de computação, tem de utilizar o SDK do Azure Machine Learning e forneça as seguintes informações:

-   **Nome de computação do Azure Batch**: Um nome amigável a ser utilizado para a computação dentro da área de trabalho
-   **Nome da conta do Azure Batch**: O nome da conta do Azure Batch
-   **Grupo de recursos**: O grupo de recursos que contém a conta do Azure Batch.

O código a seguir demonstra como anexar Azure Batch como um destino de computação:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

batch_compute_name = 'mybatchcompute' # Name to associate with new compute in workspace

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>" # Name of the Batch account
batch_resource_group = "<batch_resource_group>" # Name of the resource group which contains this account

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

## <a name="set-up-compute-in-the-azure-portal"></a>Configurar a computação no portal do Azure

Pode acessar os destinos de computação que estão associados a sua área de trabalho no portal do Azure.  Pode utilizar o portal para:

* [Destinos de computação de vista](#portal-view) anexado à sua área de trabalho
* [Criar um destino de computação](#portal-create) na sua área de trabalho
* [Anexar um destino de computação](#portal-reuse) que foi criada fora da área de trabalho

Depois de um destino é criado e ligado à sua área de trabalho, irá utilizá-lo em sua configuração de execução com uma `ComputeTarget` objeto: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>Destinos de computação do Vista


Para ver os destinos de computação para a área de trabalho, utilize os seguintes passos:

1. Navegue para o [portal do Azure](https://portal.azure.com) e abra a área de trabalho. 
1. Sob __aplicativos__, selecione __computação__.

    ![Separador de computação do Vista](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a id="portal-create"></a>Criar um destino de computação

Siga os passos anteriores para ver a lista de destinos de computação. Em seguida, utilize estes passos para criar um destino de computação: 

1. Selecione o sinal de adição (+) para adicionar um destino de computação.

    ![Adicionar um destino de computação](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Introduza um nome para o destino de computação. 

1. Selecione **computação do Machine Learning** como o tipo de computação a utilizar para __treinamento__. 

    >[!NOTE]
    >Computação do Azure Machine Learning é o recurso de computação de gerido só que pode criar no portal do Azure.  Todos os outros recursos de computação podem ser anexados após terem sido criadas.

1. Preencha o formulário. Fornecer valores para as propriedades necessárias, especialmente **família de VM**e o **número máximo de nós** usar para acelerar a computação.  

    ![Preencha o formulário](./media/how-to-set-up-training-targets/add-compute-form.png) 

1. Selecione __Criar__.


1. Ver o estado da operação de criação ao selecionar o destino de computação a partir da lista:

    ![Selecione um destino de computação para ver o estado da operação criar](./media/how-to-set-up-training-targets/View_list.png)

1. , Em seguida, ver os detalhes para o destino de computação: 

    ![Ver os detalhes do computador de destino](./media/how-to-set-up-training-targets/compute-target-details.png) 



### <a id="portal-reuse"></a>Anexar destinos de computação

Para usar destinos de computação criados fora da área de trabalho de serviço do Azure Machine Learning, terá de anexá-los. Anexar um destino de computação disponibiliza-o à sua área de trabalho.

Siga as etapas descritas anteriormente para ver a lista de destinos de computação. Em seguida, utilize os seguintes passos para anexar um destino de computação: 

1. Selecione o sinal de adição (+) para adicionar um destino de computação. 
1. Introduza um nome para o destino de computação. 
1. Selecione o tipo de computação para anexar para __treinamento__:

    > [!IMPORTANT]
    > Nem todos de computação tipos podem ser anexados a partir do portal do Azure. Os tipos de computação que podem ser anexados atualmente para treinamento incluem:
    >
    > * VM remota
    > * O Azure Databricks (para utilização em pipelines de aprendizagem automática)
    > * O Azure Data Lake Analytics (para utilização em pipelines de aprendizagem automática)
    > * Azure HDInsight

1. Preencha o formulário e forneça valores para as propriedades necessárias.

    > [!NOTE]
    > A Microsoft recomenda que utilize chaves SSH, que são mais seguras do que as palavras-passe. As palavras-passe são vulneráveis a ataques de força bruta. Chaves SSH baseiam-se em assinaturas criptográficas. Para obter informações sobre como criar chaves SSH para utilização com máquinas virtuais do Azure, veja os documentos seguintes:
    >
    > * [Criar e utilizar chaves SSH no Linux ou macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Criar e utilizar chaves SSH no Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Selecione __anexar__. 
1. Ver o estado da operação de anexar ao selecionar o destino de computação da lista.

## <a name="set-up-compute-with-the-cli"></a>Configurar computação com a CLI

Pode acessar os destinos de computação associados à área de trabalho utilizando o [extensão da CLI](reference-azure-machine-learning-cli.md) para o serviço Azure Machine Learning.  Pode utilizar a CLI para:

* Criar um destino de computação gerida
* Atualizar um destino de computação gerida
* Anexar um destino de computação não geridos

Para obter mais informações, consulte [gestão de recursos](reference-azure-machine-learning-cli.md#resource-management).


## <a id="submit"></a>Submeter treinamento executar

Depois de criar uma configuração de execução, usá-lo para executar a sua experimentação.  O padrão de código para submeter uma execução de treinamento é o mesmo para todos os tipos de destinos de computação:

1. Criar uma experimentação para execução
1. Submeta a execução.
1. Aguarde a execução concluir.

### <a name="create-an-experiment"></a>Criar uma experimentação

Primeiro, crie uma experimentação na sua área de trabalho.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Submeter a experimentação

Submeter a experimentação com um `ScriptRunConfig` objeto.  Este objeto inclui o:

* **source_directory**: O diretório de origem que contém o script de treinamento
* **script**: Identificar o script de treinamento
* **run_config**: A configuração de execução, que por sua vez, define em que ocorrerá o treinamento.

Quando submete uma execução de treinamento, um instantâneo do diretório que contém os scripts de treinamento é criado e enviado para o destino de computação. Para obter mais informações, consulte [instantâneos](concept-azure-machine-learning-architecture.md#snapshot).

Por exemplo, para utilizar [o local de destino](#local) configuração:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Mude a mesma experiência a executar num destino de computação diferentes com uma configuração de execução diferente, como o [amlcompute destino](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

Em alternativa, pode:

* Submeter a experimentação com um `Estimator` objeto conforme mostrado na [modelos de Train ML com estimadores](how-to-train-ml-models.md). 
* Submeter uma experimentação [com a extensão da CLI](reference-azure-machine-learning-cli.md#experiments).

## <a name="notebook-examples"></a>Exemplos de bloco de notas

Consulte estes blocos de notas para obter exemplos de treinamento com vários destinos de computação:
* [procedimentos-to-use-azureml/treinamento](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutoriais/img-classificação-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos Seguintes

* [Tutorial: Preparar um modelo](tutorial-train-models-with-aml.md) utiliza um destino de computação gerida para preparar um modelo.
* Depois de ter um modelo preparado, saiba [como e onde implementar modelos](how-to-deploy-and-where.md).
* Ver os [RunConfiguration classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) referência do SDK.
* [Utilizar o serviço Azure Machine Learning com redes virtuais do Azure](how-to-enable-virtual-network.md)
