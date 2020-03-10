---
title: Utilize alvos de computação para formação de modelos
titleSuffix: Azure Machine Learning
description: Configure os ambientes de treino (alvos de computação) para a formação de modelos de aprendizagem automática. Pode facilmente alternar entre ambientes de treino. Comece a treinar localmente. Se precisar de escala, mude para um alvo de computação baseado em nuvem.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/16/2020
ms.custom: seodec18
ms.openlocfilehash: c7fd70ca32054b3b25e717c8c7169cf2d30ef9be
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355358"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>Configurar e utilizar alvos de cálculo para formação de modelos 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Com o Azure Machine Learning, pode treinar o seu modelo numa variedade de recursos ou ambientes, coletivamente referidos como alvos de [__cálculo.__ ](concept-azure-machine-learning-architecture.md#compute-targets) Um alvo computacional pode ser uma máquina local ou um recurso em nuvem, como uma Computação de Aprendizagem automática Azure, Azure HDInsight ou uma máquina virtual remota.  Também pode criar alvos de cálculo para a implementação do modelo, conforme descrito em ["Onde e como implementar os seus modelos".](how-to-deploy-and-where.md)

Pode criar e gerir um alvo de cálculo utilizando a extensão Azure Machine Learning SDK, Azure Machine Learning, Azure CLI ou Azure Machine Learning VS Code. Se tiver alvos de cálculo que foram criados através de outro serviço (por exemplo, um cluster HDInsight), pode usá-los ligando-os ao seu espaço de trabalho Azure Machine Learning.
 
Neste artigo, aprende-se a usar vários alvos de computação para a formação de modelos.  Os passos para todos os objetivos da computação seguem o mesmo fluxo de trabalho:
1. __Crie__ um alvo de cálculo se ainda não tiver um.
2. __Fixe__ o alvo da computação ao seu espaço de trabalho.
3. __Configure__ o alvo da computação de modo a conter o ambiente Python e as dependências do pacote necessárias pelo seu script.


>[!NOTE]
> O código neste artigo foi testado com a versão 1.0.74 do Azure Machine Learning SDK.

## <a name="compute-targets-for-training"></a>Metas computadas para a formação

O Azure Machine Learning tem um suporte variado em diferentes alvos de cálculo. Inicia um ciclo de vida de desenvolvimento do modelo típico com dev/experimentação numa pequena quantidade de dados. Nesta fase, recomendamos que utilize um ambiente local. Por exemplo, seu computador local ou uma VM com base na cloud. À medida que aumentar verticalmente o seu treinamento em conjuntos de dados maiores ou fazer o treinamento distribuído, recomendamos que utilize a computação do Azure Machine Learning para criar um cluster único ou vários node que é dimensionado automaticamente sempre que submete uma execução. Também pode anexar seus próprios recursos de computação, embora o suporte para vários cenários podem variar como detalhadas abaixo:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]


> [!NOTE]
> A Azure Machine Learning Compute pode ser criada como um recurso persistente ou criada dinamicamente quando solicita uma execução. A criação baseada em execução remove o alvo da computação após o treino estar concluído, por isso não pode reutilizar os alvos de cálculo criados desta forma.

## <a name="whats-a-run-configuration"></a>O que é uma configuração de execução?

Quando treina, é comum começar no seu computador local, e mais tarde executar esse script de treino em um alvo de computação diferente. Com o Azure Machine Learning, pode executar o seu guião em vários alvos de computação sem ter de alterar o seu script.

Tudo o que precisa fazer é definir o ambiente para cada alvo de cálculo dentro de uma **configuração de execução**.  Em seguida, quando quiser executar a sua experiência de treino num alvo de computação diferente, especifique a configuração de execução para esse cálculo. Para obter detalhes sobre a especificação de um ambiente e encadernando-o à configuração de executar, consulte [Criar e gerir ambientes para treino e implantação.](how-to-use-environments.md)

Saiba mais sobre a apresentação de [experiências](#submit) no final deste artigo.

## <a name="whats-an-estimator"></a>O que é um estimador?

Para facilitar a formação de modelos utilizando quadros populares, o Azure Machine Learning Python SDK fornece uma abstração alternativa de alto nível, a classe estimadora.  Esta classe permite-lhe facilmente construir configurações de execução. Você pode criar e usar um [Estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) genérico para submeter scripts de formação que usam qualquer quadro de aprendizagem que você escolher (como scikit-learn). Recomendamos a utilização de um estimador para treino, uma vez que constrói automaticamente objetos embutidos como um ambiente ou objetos RunConfiguration para si. Se desejar ter mais controlo sobre a forma como estes objetos são criados e especificar quais os pacotes a instalar para a sua experiência, siga [estes passos](#amlcompute) para submeter as suas experiências de treino utilizando um objeto RunConfiguration numa Computação de Aprendizagem automática Azure.

Para as tarefas PyTorch, TensorFlow e Chainer, o Azure Machine Learning também fornece os respetivos estimativas [PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) para simplificar a utilização destas estruturas.

Para mais informações, consulte [os Modelos Train ML com estimadores](how-to-train-ml-models.md).

## <a name="whats-an-ml-pipeline"></a>O que é um oleoduto ML?

Com os gasodutos ML, pode otimizar o seu fluxo de trabalho com simplicidade, velocidade, portabilidade e reutilização. Ao construir oleodutos com O Machine Learning Azure, pode focar-se na sua experiência, aprendizagem automática, em vez de infraestruturas e automação.

Os gasodutos ML são construídos a partir de múltiplas **etapas,** que são unidades computacionais distintas no gasoduto. Cada passo pode funcionar de forma independente e usar recursos computacionais isolados. Isto permite que vários cientistas de dados trabalhem no mesmo oleoduto ao mesmo tempo sem recursos computacionais sobre-impostos, e também facilita a utilização de diferentes tipos/tamanhos de computação para cada passo.

> [!TIP]
> Os pipelines ML podem utilizar a configuração do execução ou os estimadores quando treinam os modelos.

Enquanto os gasodutos ML podem treinar modelos, também podem preparar dados antes de treinar e implementar modelos após o treino. Um dos casos de utilização primária para os gasodutos é a pontuação do lote. Para mais informações, consulte [Pipelines: Otimize](concept-ml-pipelines.md)os fluxos de trabalho de aprendizagem automática.

## <a name="set-up-in-python"></a>Instale-se em Python

Utilize as secções abaixo para configurar estes objetivos de cálculo:

* [Computador local](#local)
* [Computação de aprendizagem automática azure](#amlcompute)
* [Máquinas virtuais remotas](#vm)
* [Azure HDInsight](#hdinsight)


### <a id="local"></a>Computador local

1. **Criar e anexar**: Não há necessidade de criar ou anexar um alvo de cálculo para usar o computador local como ambiente de treino.  

1. **Configurar:** Quando utilizar o computador local como alvo de cálculo, o código de treino é executado no seu ambiente de [desenvolvimento](how-to-configure-environment.md).  Se esse ambiente já tiver os pacotes Python de que necessita, utilize o ambiente gerido pelo utilizador.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Agora que ligou o cálculo e configurou a sua execução, o próximo passo é [submeter o treino.](#submit)

### <a id="amlcompute"></a>Computação de aprendizagem automática azure

A Azure Machine Learning Compute é uma infraestrutura de computação gerida que permite ao utilizador criar facilmente uma computação única ou multi-nó. A computação é criada dentro da sua região do espaço de trabalho como um recurso que pode ser partilhado com outros utilizadores no seu espaço de trabalho. O cálculo escala automaticamente quando um trabalho é submetido, e pode ser colocado numa Rede Virtual Azure. O cálculo executa num ambiente contentorizado e embala as dependências do seu modelo num [recipiente Docker](https://www.docker.com/why-docker).

Pode usar a computação do Azure Machine Learning para distribuir o processo de treinamento em clusters de nós de computação de CPU ou de GPU na cloud. Para obter mais informações sobre os tamanhos vm que incluem GPUs, consulte tamanhos de [máquinavirtual otimizadas por GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

A Azure Machine Learning Compute tem limites de predefinição, como o número de núcleos que podem ser atribuídos. Para mais informações, consulte [Gerir e solicitar quotas para os recursos do Azure.](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas)


Você pode criar um ambiente de computação de Aprendizagem automática Azure a pedido quando você agenda uma corrida, ou como um recurso persistente.

#### <a name="run-based-creation"></a>Criação baseados em execução

Pode criar a Computação de Aprendizagem automática Azure como alvo de cálculo no tempo de execução. O cálculo é automaticamente criado para a sua execução. O cálculo é apagado automaticamente assim que a execução estiver concluída. 

> [!IMPORTANT]
> A criação baseada em run da computação Azure Machine Learning está atualmente em Pré-visualização. Não utilize a criação baseada em corridas se utilizar a finação automatizada de hiperparâmetros ou aprendizagem automática de máquinas. Para utilizar a finação do hiperparâmetro ou a aprendizagem automática de máquinas, crie um alvo [de computação persistente.](#persistent)

1.  **Criar, anexar e configurar:** A criação baseada em execução executa todos os passos necessários para criar, anexar e configurar o alvo computacional com a configuração do funcionar.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Agora que ligou o cálculo e configurou a sua execução, o próximo passo é [submeter o treino.](#submit)

#### <a id="persistent"></a>Computação persistente

Uma computação de aprendizagem automática Azure persistente pode ser reutilizada em todos os trabalhos. A computação pode ser partilhada com outros utilizadores no espaço de trabalho e é mantida entre empregos.

1. **Criar e anexar**: Para criar um recurso de computação de aprendizagem automática Azure persistente em Python, especifique as **propriedades vm_size** e **max_nodes.** O Azure Machine Learning utiliza então predefinições inteligentes para as outras propriedades. A computação automaticamente desce para zero nódosos quando não é usado.   VMs dedicados são criados para gerir os seus trabalhos conforme necessário.
    
    * **vm_size**: A família VM dos nódosos criados pela Azure Machine Learning Compute.
    * **max_nodes**: O número máximo de nós para auto-escala até quando executa um trabalho na Azure Machine Learning Compute.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Também pode configurar várias propriedades avançadas quando criar a Computação de Aprendizagem automática Azure. As propriedades permitem criar um cluster persistente de tamanho fixo, ou dentro de uma Rede Virtual Azure existente na sua subscrição.  Consulte a aula de [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) para obter mais detalhes.
    
   Ou pode criar e anexar um recurso de computação de aprendizagem automática Azure persistente no [estúdio Azure Machine Learning.](#portal-create)

1. **Configurar:** Criar uma configuração de execução para o alvo de cálculo persistente.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Agora que ligou o cálculo e configurou a sua execução, o próximo passo é [submeter o treino.](#submit)


### <a id="vm"></a>Máquinas virtuais remotas

O Azure Machine Learning também suporta a colocarem os seus próprios recursos de computação e ligá-la à sua área de trabalho. Um desses recursos é um VM remoto arbitrário, desde que seja acessível a partir de Azure Machine Learning. O recurso pode ser um Azure VM, um servidor remoto na sua organização, ou no local. Especificamente, dado o endereço IP e credenciais (nome de utilizador e palavra-passe, ou chave SSH), pode utilizar qualquer VM acessível para executagens remotas.

Pode utilizar um ambiente de sistema criado conda, um ambiente de Python já existente ou um contentor do Docker. Para executar num contentor do Docker, deve ter um Motor Docker a funcionar na VM. Esta funcionalidade é especialmente útil quando pretender que um ambiente de dev/experimentação mais flexível, com base na cloud que o seu computador local.

Utilize a Máquina Virtual de Ciência de Dados Azure (DSVM) como o Azure VM de eleição para este cenário. Este VM é um ambiente pré-configurado de ciência de dados e desenvolvimento de IA em Azure. O VM oferece uma escolha com curadoria de ferramentas e quadros para o desenvolvimento de machine learning de ciclo completo. Para obter mais informações sobre como utilizar o DSVM com Aprendizagem automática Azure, consulte [Configurar um ambiente](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm)de desenvolvimento .

1. Criar : **Crie**um DSVM antes de o utilizar para treinar o seu modelo. Para criar este recurso, consulte [Provision the Data Science Virtual Machine for Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > O Azure Machine Learning só suporta máquinas virtuais que executam o Ubuntu. Quando criar um VM ou escolher um VM existente, deve selecionar um VM que utilize Ubuntu.

1. **Anexar**: Para fixar uma máquina virtual existente como alvo de cálculo, deve fornecer o nome de domínio totalmente qualificado (FQDN), nome do utilizador e palavra-passe para a máquina virtual. No exemplo, substitua \<fqdn> com o Público FQDN do VM, ou o endereço IP público. Substitua \<username> e \<password> com o nome de utilizador sSH e palavra-passe para o VM.

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

   Ou pode anexar o DSVM ao seu espaço de trabalho utilizando o [estúdio Azure Machine Learning.](#portal-reuse)

1. **Configurar:** Criar uma configuração de execução para o alvo de cálculo DSVM. Docker e condomínio são usados para criar e configurar o ambiente de treino no DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Agora que ligou o cálculo e configurou a sua execução, o próximo passo é [submeter o treino.](#submit)

### <a id="hdinsight"></a>Azure HDInsight 

O Azure HDInsight é uma plataforma popular para análise de big data. A plataforma fornece Apache Spark, que pode ser usado para treinar o seu modelo.

1. Criar : **Crie**o cluster HDInsight antes de o utilizar para treinar o seu modelo. Para criar um cluster Spark on HDInsight, consulte [Create a Spark Cluster in HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    Quando criar o cluster, deve especificar um nome de utilizador SSH e uma palavra-passe. Tome nota destes valores, pois precisa que eles utilizem o HDInsight como alvo de cálculo.
    
    Depois de criado o cluster, ligue-se ao nome de anfitrião \<clustername>-ssh.azurehdinsight.net, onde \<clustername> é o nome que forneceu para o cluster. 

1. **Anexar:** Para fixar um cluster HDInsight como um alvo de cálculo, deve fornecer o nome de anfitrião, o nome do utilizador e a palavra-passe para o cluster HDInsight. O exemplo seguinte utiliza o SDK para anexar um cluster a sua área de trabalho. No exemplo, substitua \<clustername> com o nome do seu cluster. Substitua \<username> e \<password> com o nome de utilizador sSH e palavra-passe para o cluster.

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase
    attach_config = HDInsightCompute.attach_configuration(address='<clustername>-ssh.azurehdinsight.net', 
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

   Ou pode anexar o cluster HDInsight ao seu espaço de trabalho utilizando o [estúdio Azure Machine Learning](#portal-reuse).

1. **Configurar:** Criar uma configuração de execução para o alvo de computação HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Agora que ligou o cálculo e configurou a sua execução, o próximo passo é [submeter o treino.](#submit)


### <a id="azbatch"></a>Lote Azure 

O Lote Azure é utilizado para executar aplicações de computação paralela e de alta performance (HPC) de grande escala na nuvem. O AzureBatchStep pode ser utilizado num pipeline de aprendizagem automática Azure para submeter postos de trabalho a uma piscina de máquinas Azure Batch.

Para anexar o Lote Azure como um alvo de cálculo, deve utilizar o Azure Machine Learning SDK e fornecer as seguintes informações:

-   **Nome da computação Azure Batch**: Um nome amigável a ser usado para a computação dentro do espaço de trabalho
-   Nome da **conta Azure Batch**: O nome da conta Do Lote Azure
-   **Grupo de Recursos**: O grupo de recursos que contém a conta Do Lote Azure.

O seguinte código demonstra como anexar o Lote Azure como um alvo de cálculo:

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

## <a name="set-up-in-azure-machine-learning-studio"></a>Instale-se no estúdio Azure Machine Learning

Pode aceder aos alvos da computação que estão associados ao seu espaço de trabalho no estúdio Azure Machine Learning.  Pode usar o estúdio para:

* [Ver alvos computacionais ligados](#portal-view) ao seu espaço de trabalho
* [Crie um alvo de computação](#portal-create) no seu espaço de trabalho
* [Anexar um alvo de cálculo](#portal-reuse) que foi criado fora do espaço de trabalho


Depois de um alvo ser criado e ligado ao seu espaço de trabalho, irá usá-lo na configuração do seu funcionar com um `ComputeTarget` objeto: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>Ver alvos de cálculo


Para ver os alvos da computação para o seu espaço de trabalho, utilize os seguintes passos:

1. Navegue para [o estúdio Azure Machine Learning.](https://ml.azure.com)
 
1. Em __Aplicações,__ __selecione Compute__.

    [![Ver separador computacional](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a id="portal-create"></a>Criar um alvo de computação

Siga os passos anteriores para ver a lista de alvos da computação. Em seguida, use estes passos para criar um alvo de cálculo: 

1. Selecione o sinal de mais (+) para adicionar um alvo de cálculo.

    ![Adicione um alvo de cálculo](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Introduza um nome para o alvo da computação. 

1. Selecione **Machine Learning Compute** como o tipo de computação a utilizar para __o Treino__. 

    >[!NOTE]
    >A Azure Machine Learning Compute é o único recurso gerido de computação que se pode criar no estúdio Azure Machine Learning.  Todos os outros recursos computacionais podem ser anexados após a sua criação.

1. Preencha o formulário. Forneça valores para as propriedades necessárias, especialmente a **Família VM,** e os **nós máximos** para usar para girar a computação.  

1. Selecione __Criar__.


1. Ver o estado da operação de criação selecionando o alvo computacional da lista:

    ![Selecione um alvo de cálculo para ver o estado de criação de operação](./media/how-to-set-up-training-targets/View_list.png)

1. Em seguida, você vê os detalhes para o alvo da computação: 

    ![Ver os detalhes do alvo do computador](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a id="portal-reuse"></a>Anexar alvos de cálculo

Para utilizar alvos de cálculo criados fora do espaço de trabalho Azure Machine Learning, deve anexá-los. A fixação de um alvo de cálculo disponibiliza-o ao seu espaço de trabalho.

Siga os passos descritos anteriormente para ver a lista de alvos da computação. Em seguida, utilize os seguintes passos para fixar um objetivo de cálculo: 

1. Selecione o sinal de mais (+) para adicionar um alvo de cálculo. 
1. Introduza um nome para o alvo da computação. 
1. Selecione o tipo de computação a anexar para __a Formação:__

    > [!IMPORTANT]
    > Nem todos os tipos de cálculo podem ser anexados no estúdio Azure Machine Learning. Os tipos de cálculo que podem ser atualmente anexados para formação incluem:
    >
    > * Um VM remoto
    > * Azure Databricks (para utilização em gasodutos de aprendizagem automática)
    > * Azure Data Lake Analytics (para utilização em gasodutos de aprendizagem automática)
    > * Azure HDInsight

1. Preencha o formulário e forneça valores para as propriedades necessárias.

    > [!NOTE]
    > A Microsoft recomenda que utilize as teclas SSH, que são mais seguras do que as palavras-passe. As palavras-passe são vulneráveis a ataques de força bruta. As teclas SSH dependem de assinaturas criptográficas. Para obter informações sobre como criar chaves SSH para utilização com máquinas virtuais Azure, consulte os seguintes documentos:
    >
    > * [Criar e utilizar chaves SSH em Linux ou macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Criar e utilizar chaves SSH no Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Selecione __Anexar__. 
1. Ver o estado da operação anexa, selecionando o alvo computacional da lista.

## <a name="set-up-with-cli"></a>Configurar com CLI

Pode aceder aos alvos computacionais que estão associados ao seu espaço de trabalho utilizando a [extensão CLI](reference-azure-machine-learning-cli.md) para O Machine Learning Azure.  Pode utilizar o CLI para:

* Criar um alvo de computação gerido
* Atualizar um alvo de computação gerido
* Anexar um alvo de cálculo não gerido

Para mais informações, consulte [a gestão de Recursos.](reference-azure-machine-learning-cli.md#resource-management)

## <a name="set-up-with-vs-code"></a>Configurar com código VS

Pode aceder, criar e gerir os alvos da computação que estão associados ao seu espaço de trabalho utilizando a [extensão](tutorial-train-deploy-image-classification-model-vscode.md#configure-compute-targets) do Código VS para o Azure Machine Learning.

## <a id="submit"></a>Submeter execução de formação utilizando O SDK de Aprendizagem automática Azure

Depois de criar uma configuração de execução, usa-a para executar a sua experiência.  O padrão de código para submeter uma execução de formação é o mesmo para todos os tipos de alvos computacionais:

1. Criar uma experiência para executar
1. Submeta a execução.
1. Aguarde a execução concluir.

> [!IMPORTANT]
> Quando submete o treino, uma imagem do diretório que contém os seus scripts de treino é criada e enviada para o alvo da computação. Também é armazenado como parte da experiência no seu espaço de trabalho. Se alterar ficheiros e submeter a execução novamente, apenas os ficheiros alterados serão carregados.
>
> Para evitar que os ficheiros sejam incluídos no instantâneo, crie um ficheiro [.gitignore](https://git-scm.com/docs/gitignore) ou `.amlignore` no diretório e adicione-lhe os ficheiros. O ficheiro `.amlignore` utiliza a mesma sintaxe e padrões que o ficheiro [.gitignore.](https://git-scm.com/docs/gitignore) Se ambos os ficheiros existirem, o ficheiro `.amlignore` tem precedência.
> 
> Para mais informações, consulte [Snapshots](concept-azure-machine-learning-architecture.md#snapshots).

### <a name="create-an-experiment"></a>Criar uma experimentação

Primeiro, crie uma experiência no seu espaço de trabalho.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Submeter a experimentação

Submeta a experiência com um objeto `ScriptRunConfig`.  Este objeto inclui o:

* **source_directory**: O diretório de origem que contém o seu roteiro de treino
* **script**: Identificar o script de treino
* **run_config**: A configuração de execução, que por sua vez define onde o treino irá ocorrer.

Por exemplo, para utilizar a configuração [do alvo local:](#local)

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Altere a mesma experiência para ser executada num alvo de computação diferente utilizando uma configuração de execução diferente, como o [objetivo amlcompute:](#amlcompute)

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> Este exemplo falha a penas que se utilizam apenas um nó do alvo da computação para o treino. Para utilizar mais do que um nó, detete a `node_count` da configuração do funcionar para o número desejado de nós. Por exemplo, o seguinte código define o número de nós utilizados para a formação a quatro:
>
> ```python
> src.run_config.node_count = 4
> ```

Ou pode:

* Submeta a experiência com um objeto `Estimator` como mostrado nos [modelos Train ML com estimadores](how-to-train-ml-models.md).
* Submeta uma corrida hyperDrive para [a finação do hiperparâmetro](how-to-tune-hyperparameters.md).
* Envie uma experiência através da [extensão](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)do Código VS .

Para mais informações, consulte a documentação [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) e [RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Criar configuração de execução e submeter execução utilizando o AZURE Machine Learning CLI

Pode utilizar a [extensão CLI](reference-azure-machine-learning-cli.md) [azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e machine learning para criar configurações de execução e submeter execuções em diferentes alvos de computação. Os exemplos seguintes assumem que você tem um espaço de trabalho de aprendizagem automática Azure existente e você entrou em Azure usando `az login` comando CLI. 

### <a name="create-run-configuration"></a>Criar configuração de execução

A forma mais simples de criar a configuração do execução é navegar na pasta que contém os scripts Python de aprendizagem automática e usar o comando CLI

```azurecli
az ml folder attach
```

Este comando cria uma subpasta `.azureml` que contém ficheiros de configuração de execução de modelos para diferentes alvos de cálculo. Pode copiar e editar estes ficheiros para personalizar a sua configuração, por exemplo, para adicionar pacotes Python ou alterar as definições do Docker.  

### <a name="structure-of-run-configuration-file"></a>Estrutura do ficheiro de configuração de execução

O ficheiro de configuração de execução é formatado YAML, com seguintes secções
 * O guião para executar e os seus argumentos
 * Computao o nome-alvo, "local" ou o nome de um computação sob o espaço de trabalho.
 * Parâmetros para a execução da execução: enquadramento, comunicador para corridas distribuídas, duração máxima e número de nós de cálculo.
 * Secção de ambiente. Consulte [criar e gerir ambientes para treino e implantação](how-to-use-environments.md) para detalhes dos campos desta secção.
   * Para especificar os pacotes Python para instalar para a execução, crie [ficheiro ambiente conda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)e detete teo campo __condaDependenciesFile.__
 * Executar detalhes do histórico para especificar a pasta de ficheiros de registo e para ativar ou desativar a recolha de saída e executar instantâneos de histórico.
 * Detalhes de configuração específicos da estrutura selecionada.
 * Referência de dados e detalhes da loja de dados.
 * Detalhes de configuração específicos para Machine Learning Compute para criar um novo cluster.

Consulte o [exemplo do ficheiro JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) para um esquema completo de runconfig.

### <a name="create-an-experiment"></a>Criar uma experimentação

Primeiro, crie uma experiência para as suas corridas

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>Execução de script

Para submeter uma execução de script, execute um comando

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>Corrida HyperDrive

Pode utilizar o HyperDrive com o Azure CLI para efetuar as correções de afinação do parâmetro. Em primeiro lugar, crie um ficheiro de configuração HyperDrive no seguinte formato. Consulte os [hiperparâmetros tune para o seu](how-to-tune-hyperparameters.md) artigo de modelo para obter detalhes sobre os parâmetros de afinação do hiperparâmetro.

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

Adicione este ficheiro juntamente com os ficheiros de configuração de execução. Em seguida, submeta uma execução HyperDrive usando:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Note a secção *de argumentos* em runconfig e *espaço parametrómetro* em config HyperDrive. Contêm os argumentos da linha de comando para serem passados para o guião de treino. O valor em runconfig permanece o mesmo para cada iteração, enquanto a gama em Config HyperDrive é iterada. Não especifique o mesmo argumento em ambos os ficheiros.

Para mais detalhes sobre estes comandos ```az ml``` CLI e conjunto completo de argumentos, consulte [a documentação de referência](reference-azure-machine-learning-cli.md).

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Rastreio e integração de Git

Quando se inicia uma corrida de formação onde o diretório de origem é um repositório local de Git, a informação sobre o repositório é armazenada na história da execução. Para mais informações, consulte a [integração de Git para Azure Machine Learning.](concept-train-model-git-integration.md)

## <a name="notebook-examples"></a>Exemplos de cadernos

Consulte estes cadernos, por exemplo, de formação com vários alvos de cálculo:
* [como-usar-azureml/formação](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutoriais/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos Seguintes

* [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md) usa um alvo de computação gerido para treinar um modelo.
* Aprenda a [sintonizar eficientemente os hiperparâmetros](how-to-tune-hyperparameters.md) para construir modelos melhores.
* Uma vez que tenha um modelo treinado, aprenda [como e onde implementar modelos.](how-to-deploy-and-where.md)
* Ver referência SDK classe [RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py)
* [Utilize machine learning azure com redes virtuais azure](how-to-enable-virtual-network.md)
