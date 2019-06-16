---
title: Executar experimentações e inferência de tipos numa rede virtual
titleSuffix: Azure Machine Learning service
description: Executar experimentações de machine learning e proteger a inferência de tipos dentro de uma rede virtual do Azure. Saiba como criar destinos de computação de preparação de modelos e como inferência de tipos dentro de uma rede virtual. Saiba mais sobre os requisitos para redes virtuais protegidos, como exigir portas de entrada e saídas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 01/08/2019
ms.openlocfilehash: 48c59ddc1e203030bd967911d536930cb94761d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66356182"
---
# <a name="securely-run-experiments-and-inference-inside-an-azure-virtual-network"></a>Executar com segurança experimentações e inferência dentro de uma rede virtual do Azure

Neste artigo, irá aprender a executar as experimentações e inferência de tipos dentro de uma rede virtual. Uma rede virtual funciona como um limite de segurança, isolamento de recursos do Azure a partir da internet pública. Também pode associar uma rede virtual do Azure à sua rede no local. Permite-lhe preparar seus modelos e aceder aos seus modelos implementados para inferência de tipos de forma segura. Inferência de tipos ou modelo de classificação, é a fase em que o modelo implementado é utilizado para predição, mais comumente nos dados de produção.

O serviço Azure Machine Learning depende de outros serviços do Azure para recursos de computação. Recursos de computação (destinos de computação) são utilizados para preparar e implementar modelos. Estes computação destinos podem ser criados numa rede virtual. Por exemplo, pode utilizar a máquina de Virtual de ciência de dados do Microsoft para preparar um modelo e, em seguida, implementar o modelo para o Azure Kubernetes Service (AKS). Para obter mais informações sobre as redes virtuais, consulte a [descrição geral da rede Virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="prerequisites"></a>Pré-requisitos

Este documento parte do princípio de que está familiarizado com redes virtuais do Azure e o IP de rede em geral. Este documento também assume que criou uma rede virtual e uma sub-rede a utilizar com os seus recursos de computação. Se não estiver familiarizado com as redes virtuais do Azure, leia os artigos seguintes para saber mais sobre o serviço:

* [Endereçamento IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
* [Grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview)
* [Quickstart: Criar uma rede virtual](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
* [Filtrar tráfego de rede](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="storage-account-for-your-workspace"></a>Conta de armazenamento para a área de trabalho

> [!IMPORTANT]
> Pode colocar a conta de armazenamento que está ligada à área de trabalho do Azure Machine Learning serviço por trás da rede virtual apenas durante a execução de experimentação. Inferência de tipos requer acesso sem restrições à conta de armazenamento. Se não tiver a certeza se modificar estas definições ou não, consulte __alterar a regra de acesso de rede predefinida__ na [configurar o armazenamento do Azure firewalls e redes virtuais](https://docs.microsoft.com/azure/storage/common/storage-network-security). Utilize os passos para permitir o acesso de todas as redes durante a inferência de tipos ou modelo de classificação.

Para utilizar as capacidades de experimentação do Azure Machine Learning com o armazenamento do Azure por trás de uma rede virtual, siga os passos abaixo:

1. Crie uma computação de experimentação por ex. Machine Learning de computação por trás de uma rede virtual ou anexar uma computação de experimentação para a área de trabalho por ex. Cluster do HDInsight ou máquina virtual. Para obter mais informações, consulte [computação do uso Machine Learning](#use-machine-learning-compute) e [utilizar uma máquina virtual ou um cluster do HDInsight](#use-a-virtual-machine-or-hdinsight-cluster) secções neste documento
2. Vá para o armazenamento ligado à área de trabalho. ![Imagem do portal do Azure que mostra o armazenamento do Azure que está ligado à área de trabalho do serviço do Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)
3. Na página de armazenamento do Azure, selecione __Firewalls e redes virtuais__. ![Imagem do portal do Azure que mostra os Firewalls e virtual redes secção na página de armazenamento do Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)
4. Sobre o __Firewalls e redes virtuais__ página selecionar as seguintes entradas:
    - Selecione __Redes selecionadas__.
    - Sob __redes virtuais__, selecione __adicionar rede virtual existente__ para adicionar a rede virtual onde reside a computação de experimentação. (Consulte o passo 1.)
    - Selecione __permitir confiável a serviços da Microsoft para aceder a esta conta de armazenamento__.
![Imagem do portal do Azure que mostra os Firewalls e virtual redes página em armazenamento do Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png) 

5. Ao executar a experimentação, no seu código de experimentação, altere a configuração de execução para utilizar o armazenamento de BLOBs:
    ```python
    run_config.source_directory_data_store = "workspaceblobstore"
    ```
    
## <a name="key-vault-for-your-workspace"></a>Cofre de chaves para a área de trabalho
Instância do Key Vault associada com a área de trabalho é utilizada pelo serviço Azure Machine Learning para armazenar credenciais de vários tipos:
* A cadeia de ligação de conta de armazenamento associado
* Palavras-passe para as instâncias de repositório de contentor do Azure
* Ligação de seqüências de caracteres de dados de arquivos. 

Para utilizar a experimentação do Azure Machine Learning capacidades com o Key Vault por trás de uma rede virtual, siga os passos abaixo:
1. Vá para o Cofre de chaves associadas com a área de trabalho. ![Imagem do portal do Azure que mostra o Cofre de chaves que estão associados com a área de trabalho do serviço do Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)
2. Na página do Key Vault, selecione __Firewalls e redes virtuais__ secção. ![Imagem do portal do Azure que mostra os Firewalls e virtual redes secção na página do Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)
3. Sobre o __Firewalls e redes virtuais__ página selecionar as seguintes entradas:
    - Selecione __Redes selecionadas__.
    - Sob o __redes virtuais__, selecione __adicionar redes virtuais existentes__ para adicionar a rede virtual onde reside a computação de experimentação.
    - Selecione __permitir confiável a serviços da Microsoft para ignorar esta firewall__.
![Imagem do portal do Azure que mostra os Firewalls e virtual redes página no Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png) 


## <a name="use-machine-learning-compute"></a>Utilizar o Machine Learning de computação

Para utilizar a computação do Azure Machine Learning numa rede virtual, utilize as seguintes informações sobre os requisitos de rede:

- A rede virtual tem de estar na mesma subscrição e região como a área de trabalho do serviço do Azure Machine Learning.

- A sub-rede especificada para o cluster de computação do Machine Learning tem de ter suficiente não atribuídos endereços IP para acomodar o número de VMs destinada para o cluster. Se a sub-rede não tiver suficiente não atribuídos endereços IP, o cluster será atribuído parcialmente.

- Se pretender proteger a rede virtual, restringindo o tráfego, deixe algumas portas abertas para o serviço de computação do Machine Learning. Para obter mais informações, consulte [necessárias portas](#mlcports).

- Verifique se as políticas de segurança ou bloqueios na subscrição ou grupo de recursos da rede virtual restringem permissões para gerir a rede virtual.

- Se pretender colocar vários clusters de computação do Machine Learning numa rede virtual, terá de pedir um aumento de quota para um ou mais dos seus recursos.

    Computação do Machine Learning aloca automaticamente recursos de rede adicionais no grupo de recursos que contém a rede virtual. Para cada cluster de computação do Machine Learning, o serviço Azure Machine Learning aloca os seguintes recursos:

    - Grupo de segurança de uma rede (NSG)

    - Um endereço IP público

    - Um balanceador de carga

  Estes recursos estão limitados pelas [quotas de recursos](https://docs.microsoft.com/azure/azure-subscription-service-limits) da subscrição.

### <a id="mlcports"></a> Portas necessárias

Computação do Machine Learning utiliza atualmente o serviço Azure Batch para aprovisionar VMs na rede virtual especificada. A sub-rede tem de permitir comunicação de entrada do serviço Batch. Esta comunicação é utilizada para agendar é executado em nós de computação do Machine Learning e para comunicar com o armazenamento do Azure e outros recursos. O batch adiciona NSGs ao nível das interfaces de rede (NICs) que estão anexados a VMs. Estes NSGs configuram automaticamente regras de entrada e saída para permitir o tráfego seguinte:

- Entrada de tráfego TCP em portas 29876 e 29877 de um __etiqueta de serviço__ dos __BatchNodeManagement__.

    ![Imagem do portal do Azure que mostra uma regra de entrada usando a marca de serviço BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)
 
- (opcional) Tráfego TCP de entrada na porta 22 para permitir o acesso remoto. Esta porta só é necessário se pretender ligar através de SSH no IP público.
 
- Tráfego de saída em qualquer porta para a rede virtual.

- Tráfego de saída em qualquer porta para a Internet. 

Tenha cuidado se modifica ou adicionar regras de entrada/saída de NSGs configurados de Batch. Se uma comunicação de blocos NSG para os nós de computação, em seguida, os serviços de computação do Machine Learning define o estado de nós de computação para inutilizável.

Não tem de especificar os NSGs ao nível da sub-rede porque o lote configura o seus próprio NSGs. No entanto, se a sub-rede especificada tiver associados NSGs e/ou uma firewall, configure as regras de segurança de entrada e saída, conforme mencionado anteriormente. 

Captura de ecrã seguinte mostra o aspeto da configuração da regra NSG no portal do Azure:

![Captura de ecrã da entrada de regras NSG para a computação do Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Regras de NSG de captura de ecrã da saída para a computação do Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a> Limitar a conectividade de saída da rede virtual

Se não pretender utilizar as regras de saída predefinido e pretende limitar o acesso de saída da sua rede virtual, siga os passos abaixo:

- Negar a ligação de internet de saída com as regras de NSG 

- Limitar o tráfego de saída para o armazenamento do Azure (usando __etiquetas de serviço__ dos __Storage.Region_Name__ ex. Eastus), Azure Container Registry (usando __etiquetas de serviço__ dos __AzureContainerRegistry.Region_Name__ ex. AzureContainerRegistry.EastUS) e o serviço Azure Machine Learning (usando __etiquetas de serviço__ dos __AzureMachineLearning__)

Captura de ecrã seguinte mostra o aspeto da configuração da regra NSG no portal do Azure:

![Regras de NSG de captura de ecrã da saída para a computação do Machine Learning](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)

### <a name="user-defined-routes-for-forced-tunneling"></a>Rotas definidas pelo utilizador para o túnel forçado

Se estiver a utilizar um túnel forçado com a computação do Azure Machine Learning, tem de adicionar [rotas definidas pelo utilizador (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) para a sub-rede que contém o recurso de computação.

* Uma rota definida pelo utilizador para cada endereço IP utilizado pelo serviço Azure Batch na região onde existem o seus recursos. Estes UDRs ativar o serviço de batch comunicar connosco de computação para agendamento de tarefas. Para obter uma lista dos endereços IP do serviço Batch, contacte o suporte do Azure.

* Tráfego de saída para o armazenamento do Azure (especificamente, os URLs do formulário `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`, e `<account>.blob.core.windows.net`) não tem de ser bloqueado pela sua aplicação de rede no local.

Quando adiciona as rotas definidas pelo utilizador, definir a rota para cada prefixo de endereço IP do Batch relacionado e defina __tipo de próximo salto__ ao __Internet__. A imagem seguinte mostra um exemplo deste UDR no portal do Azure:

![Rota definida pelo utilizador do exemplo para um prefixo de endereço](./media/how-to-enable-virtual-network/user-defined-route.png)

Para obter mais informações, consulte a [criar um conjunto do Batch do Azure numa rede virtual](../../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling) artigo.

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>Criar a computação do Machine Learning numa rede virtual

Para criar um cluster de computação do Machine Learning com o portal do Azure, siga estes passos:

1. Na [portal do Azure](https://portal.azure.com), selecione a sua área de trabalho do serviço do Azure Machine Learning.

1. Na __aplicativo__ secção, selecione __computação__. Em seguida, selecione __adicionar computação__. 

    ![Como adicionar uma computação no serviço Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Para configurar este recurso de computação para utilizar uma rede virtual, utilize estas opções:

    - __Configuração de rede__: Selecione __Avançadas__.

    - __Grupo de recursos__: Selecione o grupo de recursos que contém a rede virtual.

    - __Rede virtual__: Selecione a rede virtual que contém a sub-rede.

    - __Sub-rede__: Selecione a sub-rede a utilizar.

   ![Uma captura de ecrã a mostrar as definições de rede virtual para a computação do machine learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Também pode criar um cluster de computação do Machine Learning com o SDK do Azure Machine Learning. O código seguinte cria um novo cluster de computação do Machine Learning no `default` sub-rede de uma rede virtual com o nome `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")
    
    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name = vnet_resourcegroup_name,
                                                           vnet_name = vnet_name,
                                                           subnet_name = subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    
    # Wait for the cluster to complete, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Quando concluir o processo de criação, pode preparar o seu modelo com o cluster. Para obter mais informações, consulte [selecione e utilize um destino de computação de treinamento](how-to-set-up-training-targets.md).

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Utilizar uma máquina virtual ou um cluster do HDInsight

Para utilizar uma máquina virtual ou o cluster Azure HDInsight numa rede virtual com a sua área de trabalho, siga estes passos:

> [!IMPORTANT]
> O serviço Azure Machine Learning suporta apenas máquinas virtuais que estejam a executar o Ubuntu.

1. Criar um cluster VM ou o HDInsight com o portal do Azure ou a CLI do Azure e colocá-la numa rede virtual do Azure. Para obter mais informações, consulte os seguintes documentos:
    * [Criar e gerir redes virtuais do Azure para VMs do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Expandir o HDInsight com uma rede virtual do Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) 

1. Para permitir que o serviço Azure Machine Learning comunicar com a porta SSH na VM ou cluster, tem de configurar uma entrada de origem para o NSG. A porta SSH é normalmente a porta 22. Para permitir tráfego a partir desta origem, utilize as seguintes informações:

    * __origem__: Selecione __etiqueta de serviço__.

    * __Etiqueta de serviço de origem__: Select __AzureMachineLearning__.

    * __Intervalos de portas de origem__: Selecione __*__ .

    * __Destino__: Selecione __qualquer__.

    * __Intervalos de portas de destino__: Selecione __22__.

    * __Protocolo__: Selecione __qualquer__.

    * __Ação__: Selecione __permitir__.

   ![Captura de ecrã de regras de entrada para fazer a experimentação num cluster de VM ou HDInsight numa rede virtual](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Mantenha a predefinição as regras de saída para o NSG. Para obter mais informações, consulte as regras de segurança predefinidas no [grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Se não pretender utilizar as regras de saída predefinido e pretende limitar o acesso de saída da sua rede virtual, consulte [limitar a conectividade de saída da rede virtual](#limiting-outbound-from-vnet)
    
1. Anexe o cluster VM ou o HDInsight à sua área de trabalho do serviço do Azure Machine Learning. Para obter mais informações, consulte [configurar destinos de computação de preparação de modelos](how-to-set-up-training-targets.md).

## <a name="use-azure-kubernetes-service"></a>Utilizar o serviço Kubernetes do Azure

> [!IMPORTANT]
> Verifique os pré-requisitos e planeie o endereçamento IP para o seu cluster antes de continuar com os passos. Para obter mais informações, consulte [configurar avançadas de rede no Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/configure-advanced-networking).
> 
>
> Mantenha a predefinição as regras de saída para o NSG. Para obter mais informações, consulte as regras de segurança predefinidas no [grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).
>
> Serviço Kubernetes do Azure e a rede virtual do Azure devem estar na mesma região.

Para adicionar o serviço Kubernetes do Azure numa rede virtual à sua área de trabalho, siga estes passos no portal do Azure:

1. Certifique-se de que NSG grupo ativada para utilizar o serviço Azure Machine Learning de regra de entrada de controles que a rede virtual tem __etiquetas de serviço__ de __AzureMachineLearning__

    ![Como adicionar uma computação no serviço Azure Machine Learning](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)     
 
1. Na [portal do Azure](https://portal.azure.com), selecione a sua área de trabalho do serviço do Azure Machine Learning.

1. Na __aplicativo__ secção, selecione __computação__. Em seguida, selecione __adicionar computação__. 

    ![Como adicionar uma computação no serviço Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Para configurar este recurso de computação para utilizar uma rede virtual, utilize estas opções:

    - __Configuração de rede__: Selecione __Avançadas__.

    - __Grupo de recursos__: Selecione o grupo de recursos que contém a rede virtual.

    - __Rede virtual__: Selecione a rede virtual que contém a sub-rede.

    - __Sub-rede__: Selecione a sub-rede.

    - __Intervalo de endereços do serviço do Kubernetes__: Selecione o intervalo de endereços do serviço de Kubernetes. Este intervalo de endereços usa um intervalo IP de notação CIDR para definir os endereços IP disponíveis para o cluster. Ele não pode sobrepor com quaisquer intervalos IP de sub-rede. Por exemplo: 10.0.0.0/16.

    - __Endereço IP do serviço de DNS do Kubernetes__: Selecione o endereço IP do serviço de DNS do Kubernetes. Este endereço IP é atribuído para o serviço DNS do Kubernetes. Tem de estar no intervalo de endereço do serviço de Kubernetes. Por exemplo: 10.0.0.10.

    - __Endereço de bridge do docker__: Selecione o endereço de bridge do Docker. Este endereço IP é atribuído a Bridge do Docker. Não pode estar em quaisquer intervalos IP de sub-rede ou o intervalo de endereços do serviço de Kubernetes. Por exemplo: 172.17.0.1/16.

   ![Azure Machine Learning service: Definições de rede virtual de computação de aprendizagem do computador](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Certifique-se de que NSG grupo ativada para o ponto final de classificação, para que ele pode ser chamado de fora da rede virtual de regra de entrada de controles que a rede virtual tem

    ![Como adicionar uma computação no serviço Azure Machine Learning](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)

    > [!TIP]
    > Se já tiver um cluster do AKS numa rede virtual, pode anexá-lo para a área de trabalho. Para obter mais informações, consulte [como implementar no AKS](how-to-deploy-to-aks.md).

Também pode utilizar o **SDK do Azure Machine Learning** para adicionar o serviço Kubernetes do Azure numa rede virtual. O código seguinte cria uma nova instância de serviço Kubernetes do Azure no `default` sub-rede de uma rede virtual com o nome `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace = ws,
                                  name = "myaks",
                                  provisioning_configuration = config)
```

Quando o processo de criação é concluído, pode inferência de tipos/pontuação num cluster do AKS por trás de uma rede virtual. Para obter mais informações, consulte [como implementar no AKS](how-to-deploy-to-aks.md).

## <a name="next-steps"></a>Passos Seguintes

* [Configurar ambientes de treinamento](how-to-set-up-training-targets.md)
* [Onde pretende implementar modelos](how-to-deploy-and-where.md)
* [Implantar com segurança os modelos com SSL](how-to-secure-web-service.md)

