---
title: Isolamento de rede & privacidade
titleSuffix: Azure Machine Learning
description: Utilize uma rede virtual Azure isolada com Azure Machine Learning para garantir a experimentação/formação, bem como trabalhos de inferência/pontuação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 06/22/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: 3189fec114ca68dfd862c0973b289b9eff25fed5
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85445564"
---
# <a name="network-isolation-during-training--inference-with-private-virtual-networks"></a>Isolamento de rede durante treino & inferência com redes virtuais privadas
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a proteger os seus ciclos de vida de aprendizagem automática isolando a Azure Machine Learning e inferências de trabalhos dentro de uma Rede Virtual Azure (vnet). O Azure Machine Learning conta com outros serviços Azure para recursos compute, também conhecidos como [metas de computação,](concept-compute-target.md)para formar e implementar modelos. Os alvos podem ser criados numa rede virtual. Por exemplo, pode utilizar o computamento Azure Machine Learning para treinar um modelo e, em seguida, implementar o modelo para o Serviço Azure Kubernetes (AKS). 

Uma **rede virtual** funciona como uma fronteira de segurança, isolando os seus recursos Azure da internet pública. Também pode aderir a uma rede virtual Azure à sua rede no local. Ao juntar redes, pode treinar os seus modelos de forma segura e aceder aos seus modelos implantados para inferência.

Se o seu **armazenamento subjacente estiver numa rede virtual, os utilizadores não poderão utilizar a experiência web do estúdio da Azure Machine Learning**, incluindo o designer drag-n-drop ou o UI para aprendizagem automática de máquinas, rotulagem de dados e conjuntos de dados, ou cadernos integrados.  Se tentar, receberá uma mensagem semelhante ao seguinte erro:`__Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.__`

## <a name="prerequisites"></a>Pré-requisitos

+ Um espaço de [trabalho](how-to-manage-workspace.md)de aprendizagem automática Azure.

+ Conhecimento geral de trabalho tanto do [serviço de Rede Virtual Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) como da rede [IP.](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)

+ Uma rede virtual pré-existente e uma sub-rede para utilizar com os seus recursos de computação.

## <a name="private-endpoints"></a>Pontos finais privados

Também pode [ativar o Azure Private Link](how-to-configure-private-link.md) para ligar ao seu espaço de trabalho utilizando um ponto final privado. O ponto final privado é um conjunto de endereços IP privados dentro da sua rede virtual. [Aprenda a configurar este ponto final privado.](how-to-configure-private-link.md)



> [!TIP]
> Pode combinar rede virtual e Private Link em conjunto para proteger a comunicação entre o seu espaço de trabalho e outros recursos Azure. No entanto, algumas combinações requerem um espaço de trabalho de edição da Enterprise. Use a seguinte tabela para entender que cenários requerem edição enterprise:
>
> | Cenário | Enterprise</br>edição | Básica</br>edição |
> | ----- |:-----:|:-----:| 
> | Sem rede virtual ou Link Privado | ✔ | ✔ |
> | Espaço de trabalho sem Ligação Privada. Outros recursos (exceto registo de contentores Azure) numa rede virtual | ✔ | ✔ |
> | Espaço de trabalho sem Ligação Privada. Outros recursos com Ligação Privada | ✔ | |
> | Espaço de trabalho com Ligação Privada. Outros recursos (exceto registo de contentores Azure) numa rede virtual | ✔ | ✔ |
> | Espaço de trabalho e qualquer outro recurso com Private Link | ✔ | |
> | Espaço de trabalho com Ligação Privada. Outros recursos sem Ligação Privada ou rede virtual | ✔ | ✔ |
> | Registo de contentores Azure em uma rede virtual | ✔ | |
> | Chaves geridas pelo cliente para espaço de trabalho | ✔ | |
> 

> [!WARNING]
> A pré-visualização de instâncias computacional de aprendizagem automática Azure não é suportada num espaço de trabalho onde o Private Link está ativado.
> 
> O Azure Machine Learning não suporta a utilização de um Serviço Azure Kubernetes que tenha uma ligação privada ativada. Em vez disso, pode utilizar o Serviço Azure Kubernetes numa rede virtual. Para obter mais informações, consulte [secure Azure ML experimentação e inferências de trabalhos dentro de uma Rede Virtual Azure](how-to-enable-virtual-network.md).


<a id="amlcompute"></a>

## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Clusters de computação & instâncias 

Para utilizar um [ **alvo de computação** Azure Machine Learning gerido](concept-compute-target.md#azure-machine-learning-compute-managed) ou um [ **caso** de computação Azure Machine Learning](concept-compute-instance.md) numa rede virtual, devem ser cumpridos os seguintes requisitos de rede:

> [!div class="checklist"]
> * A rede virtual deve estar na mesma subscrição e região que o espaço de trabalho Azure Machine Learning.
> * A sub-rede especificada para a instância de computação ou cluster deve ter endereços IP não atribuídos suficientes para acomodar o número de VMs que são alvo. Se a sub-rede não tiver endereços IP não atribuídos suficientes, um cluster de cálculo será parcialmente atribuído.
> * Verifique se as suas políticas de segurança ou bloqueios na subscrição da rede virtual ou no grupo de recursos restringem permissões para gerir a rede virtual. Se planeia proteger a rede virtual restringindo o tráfego, deixe algumas portas abertas para o serviço de computação. Para mais informações, consulte a secção [de portas necessárias.](#mlcports)
> * Se vai colocar várias instâncias de computação ou clusters numa rede virtual, poderá ter de solicitar um aumento de quota para um ou mais dos seus recursos.
> * Se a conta de armazenamento Azure para o espaço de trabalho também estiver protegida numa rede virtual, devem estar na mesma rede virtual que o azure machine learning ou cluster. 
> * Para que a funcionalidade do Jupyter funcione, certifique-se de que a comunicação da tomada web não está desativada.

> [!TIP]
> A instância ou cluster do cálculo machine learning aloca automaticamente recursos adicionais de rede __no grupo de recursos que contém a rede virtual.__ Para cada instância de cálculo ou cluster, o serviço aloca os seguintes recursos:
> 
> * Um grupo de segurança de rede
> * Um endereço IP público
> * Um equilibrador de carga
> 
> No caso dos clusters, estes recursos são eliminados (e recriados) sempre que o cluster se reduz a 0 nós, no entanto, por exemplo, os recursos são mantidos até que a instância seja completamente eliminada (parar não retira os recursos). 
> Estes recursos estão limitados pelas [quotas de recursos](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) da subscrição.


### <a name="required-ports"></a><a id="mlcports"></a>Portos necessários

Se planeia proteger a rede virtual restringindo o tráfego de rede de/para a internet pública, deve permitir comunicações de entrada a partir do serviço Azure Batch.

O serviço Batch adiciona grupos de segurança de rede (NSGs) ao nível das interfaces de rede (NICs) que estão ligadas aos VMs. Estes NSGs configuram automaticamente regras de entrada e saída para permitir o tráfego seguinte:

- Tráfego TCP de entrada nos portos 29876 e 29877 a partir de uma etiqueta de __serviço__ de __BatchNodeManagement__.

    ![Uma regra de entrada que usa a etiqueta de serviço BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (Opcional) Tráfego TCP de entrada na porta 22 para permitir o acesso remoto. Utilize esta porta apenas se pretender ligar utilizando sSH no IP público.

- Tráfego de saída em qualquer porta para a rede virtual.

- Tráfego de saída em qualquer porta para a Internet.

- Para o porputação, o tráfego TCP no porto 44224 a partir de uma etiqueta de __serviço__ da __AzureMachineLearning__.

> [!IMPORTANT]
> Tenha cuidado se modificar ou adicionar regras de entrada ou saída em NSGs configurados pelo Batch. Se um NSG bloqueia a comunicação aos nós de computação, o serviço de computação define o estado dos nós de computação para inutilizável.
>
> Não precisa de especificar NSGs ao nível da sub-rede, porque o serviço Azure Batch configura os seus próprios NSGs. No entanto, se a sub-rede que contém o cálculo Azure Machine Learning tiver associado NSGs ou uma firewall, também deve permitir o tráfego listado anteriormente.

A configuração da regra NSG no portal Azure é mostrada nas seguintes imagens:

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="As regras NSG de entrada para o Cálculo de Aprendizagem automática" border="true":::



![As regras nSG de saída para o Cálculo de Aprendizagem automática](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a>Limitar a conectividade de saída da rede virtual

Se não quiser utilizar as regras de saída predefinidos e pretender limitar o acesso de saída da sua rede virtual, utilize os seguintes passos:

- Negar a ligação à Internet de saída utilizando as regras NSG.

- Para uma __instância de computação__ ou um __cluster de cálculo,__ limite o tráfego de saída para os seguintes itens:
   - Armazenamento Azure, utilizando __a Etiqueta__ de Serviço de __Armazenamento.RegionName__. Onde `{RegionName}` está o nome de uma região de Azure.
   - Registo do Contentor Azure, utilizando __a etiqueta__ de serviço da __AzureContainerRegistry.RegionName__. Onde `{RegionName}` está o nome de uma região de Azure.
   - Azure Machine Learning, utilizando __a etiqueta__ de serviço da __AzureMachineLearning__
   - Gestor de Recursos Azure, utilizando __a Tag__ de Serviço da __AzureResourceManager__
   - Diretório Ativo Azure, utilizando __a Tag__ de Serviço da __AzureActiveDirectory__

A configuração da regra NSG no portal Azure é mostrada na seguinte imagem:

[![As regras nSG de saída para o Cálculo de Aprendizagem automática](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Se planeia utilizar imagens padrão do Docker fornecidas pela Microsoft e permitir dependências geridas pelo utilizador, também deve utilizar as __seguintes Tags de Serviço__:
>
> * __MicrosoftContainerRegistry__
> * __AzureFrontDoor.FirstParty__
>
> Esta configuração é necessária quando tem código semelhante aos seguintes snippets como parte dos seus scripts de treino:
>
> __Treino RunConfig__
> ```python
> # create a new runconfig object
> run_config = RunConfiguration()
> 
> # configure Docker 
> run_config.environment.docker.enabled = True
> # For GPU, use DEFAULT_GPU_IMAGE
> run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
> run_config.environment.python.user_managed_dependencies = True
> ```
>
> __Formação de estimadores__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="user-defined-routes-for-forced-tunneling"></a>Vias definidas pelo utilizador para túneis forçados

Se estiver a utilizar um túnel forçado com o Machine Learning Compute, adicione [rotas definidas pelo utilizador (UDRs)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) à sub-rede que contém o recurso computacional.

* Estabeleça uma UDR para cada endereço IP que seja utilizado pelo serviço Azure Batch na região onde os seus recursos existem. Estes UDRs permitem ao serviço Batch comunicar com nós computacional para agendamento de tarefas. Adicione também o endereço IP para o serviço de Aprendizagem automática Azure onde existem os recursos, uma vez que este é necessário para o acesso a Instâncias computacional. Para obter uma lista de endereços IP do serviço Batch e do serviço Azure Machine Learning, utilize um dos seguintes métodos:

    * Faça o download das [Gamas IP E Tags de Serviço Azure](https://www.microsoft.com/download/details.aspx?id=56519) e procure no ficheiro `BatchNodeManagement.<region>` `AzureMachineLearning.<region>` e, onde `<region>` fica a sua região Azure.

    * Utilize o [CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para descarregar as informações. O exemplo a seguir descarrega as informações do endereço IP e filtra as informações para a região leste dos EUA 2:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        ```

* O tráfego de saída para o Azure Storage não deve ser bloqueado pelo seu aparelho de rede no local. Especificamente, os URLs estão na `<account>.table.core.windows.net` `<account>.queue.core.windows.net` forma, e `<account>.blob.core.windows.net` .

Quando adicionar os UDRs, defina a rota para cada prefixo de endereço IP do lote relacionado e defina __o tipo de próximo lúpulo__ para a __Internet__. A imagem a seguir mostra um exemplo desta UDR no portal Azure:

![Exemplo de um UDR para um prefixo de endereço](./media/how-to-enable-virtual-network/user-defined-route.png)

Para obter mais informações, consulte [criar uma piscina Azure Batch numa rede virtual.](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Criar um cluster compute numa rede virtual

Para criar um cluster machine learning compute, utilize os seguintes passos:

1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com/)e, em seguida, selecione a sua subscrição e espaço de trabalho.

1. __Selecione Compute__ à esquerda.

1. Selecione __agrupamentos__ de treino a partir do centro e, em seguida, selecione __+__ .

1. No diálogo __New Training Cluster,__ expanda a secção __de definições Avançadas.__

1. Para configurar este recurso computacional para utilizar uma rede virtual, execute as seguintes ações na secção __de rede virtual Configure:__

    1. Na lista de down-down do __grupo de recursos,__ selecione o grupo de recursos que contém a rede virtual.
    1. Na lista de drop-down da __rede Virtual,__ selecione a rede virtual que contém a sub-rede.
    1. Na lista de down-down __sub-rede,__ selecione a sub-rede a utilizar.

   ![As definições de rede virtual para Machine Learning Compute](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Também pode criar um cluster machine learning compute utilizando o Azure Machine Learning SDK. O seguinte código cria um novo cluster Machine Learning Compute na `default` sub-rede de uma rede virtual chamada `mynetwork` :

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
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Quando o processo de criação termina, treina o seu modelo usando o cluster numa experiência. Para obter mais informações, consulte [Select e use um alvo de computação para treinar.](how-to-set-up-training-targets.md)

## <a name="use-a-storage-account-for-your-workspace"></a>Use uma conta de armazenamento para o seu espaço de trabalho

Para utilizar uma conta de armazenamento Azure para o espaço de trabalho numa rede virtual, utilize os seguintes passos:

1. Crie um recurso compute (por exemplo, uma instância de cálculo de Machine Learning ou cluster) por trás de uma rede virtual, ou anexe um recurso de computação ao espaço de trabalho (por exemplo, um cluster HDInsight, máquina virtual ou cluster de serviço Azure Kubernetes). O recurso computacional pode ser para experimentação ou implantação de modelos.

   Para obter mais informações, consulte o [computação Use a Machine Learning](#amlcompute), Utilize uma máquina virtual ou um cluster [HDInsight](#vmorhdi)e utilize as secções [de Serviço Azure Kubernetes](#aksvnet) neste artigo.

1. No portal Azure, vá ao armazenamento que está ligado ao seu espaço de trabalho.

   [![O armazenamento que está ligado ao espaço de trabalho Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Na página **de Armazenamento Azure,** selecione __Firewalls e redes virtuais__.

   ![A área "Firewalls e redes virtuais" na página de Armazenamento Azure no portal Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Na página __firewalls e redes virtuais,__ faça as seguintes ações:
    - Selecione __Redes selecionadas__.
    - Nas __redes Virtuais,__ selecione a ligação __de rede virtual Add existente.__ Esta ação adiciona a rede virtual onde reside o seu cálculo (ver passo 1).

        > [!IMPORTANT]
        > A conta de armazenamento deve estar na mesma rede virtual e sub-rede que as instâncias de computação ou clusters utilizados para treino ou inferência.

    - Selecione os __serviços da Microsoft fidedignos para aceder a esta__ caixa de verificação de conta de armazenamento.

    > [!IMPORTANT]
    > Ao trabalhar com o Azure Machine Learning SDK, o seu ambiente de desenvolvimento deve ser capaz de se conectar à Conta de Armazenamento Azure. Quando a conta de armazenamento está dentro de uma rede virtual, a firewall deve permitir o acesso a partir do endereço IP do ambiente de desenvolvimento.
    >
    > Para permitir o acesso à conta de armazenamento, visite as __Firewalls e redes virtuais__ para a conta de armazenamento *a partir de um navegador web no cliente de desenvolvimento.* Em seguida, utilize a caixa de verificação __ip do seu cliente__ para adicionar o endereço IP do cliente à GAMA DE __ENDEREÇOS__. Também pode utilizar o campo __ADDRESS RANGE__ para introduzir manualmente o endereço IP do ambiente de desenvolvimento. Uma vez adicionado o endereço IP do cliente, pode aceder à conta de armazenamento utilizando o SDK.

   [![O painel "Firewalls e redes virtuais" no portal Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> Pode colocar a _conta de armazenamento predefinida_ para Azure Machine Learning ou _contas de armazenamento não padrão_ numa rede virtual.
>
> A conta de armazenamento predefinida é automaticamente a provisionada quando cria um espaço de trabalho.
>
> Para contas de armazenamento não padrão, o `storage_account` parâmetro na [ `Workspace.create()` função](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) permite especificar uma conta de armazenamento personalizada por ID de recurso Azure.

## <a name="machine-learning-studio"></a>Estúdio de aprendizagem automática

Ao aceder ao estúdio a partir de um recurso dentro de uma rede virtual (por exemplo, uma instância de computação ou máquina virtual), deve permitir o tráfego de saída da rede virtual para o estúdio. 

Por exemplo, se estiver a utilizar grupos de segurança de rede (NSG) para restringir o tráfego de saída, adicione uma regra a um destino de __marcação__ de serviço de __AzureFrontDoor.Frontend__.

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Para adicionar o Serviço Azure Kubernetes (AKS) numa rede virtual ao seu espaço de trabalho, utilize os seguintes passos:

> [!IMPORTANT]
> Antes de iniciar o seguinte procedimento, siga os pré-requisitos na [rede avançada Configure em Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites) como fazer e planeie o endereço IP para o seu cluster.
>
> A instância AKS e a rede virtual Azure devem estar na mesma região. Se proteger a Conta de Armazenamento Azure utilizada pelo espaço de trabalho numa rede virtual, deve estar na mesma rede virtual que a aks.

> [!WARNING]
> O Azure Machine Learning não suporta a utilização de um Serviço Azure Kubernetes que tenha uma ligação privada ativada.

1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com/)e, em seguida, selecione a sua subscrição e espaço de trabalho.

1. __Selecione Compute__ à esquerda.

1. Selecione __os agrupamentos__ de inferência do centro e, em seguida, selecione __+__ .

1. No __diálogo New Inference Cluster,__ selecione __Advanced__ in __Network .__

1. Para configurar este recurso computacional para utilizar uma rede virtual, execute as seguintes ações:

    1. Na lista de down-down do __grupo de recursos,__ selecione o grupo de recursos que contém a rede virtual.
    1. Na lista de drop-down da __rede Virtual,__ selecione a rede virtual que contém a sub-rede.
    1. Na lista de drop-down __sub-rede,__ selecione a sub-rede.
    1. Na caixa __de intervalo de endereços do Serviço Kubernetes,__ insira a gama de endereços de serviço Kubernetes. Esta gama de endereços utiliza uma gama IP de encaminhamento inter-domínio sem classe (CIDR) para definir os endereços IP disponíveis para o cluster. Não deve sobrepor-se a quaisquer gamas IP da sub-rede (por exemplo, 10.0.0.0/16).
    1. Na caixa de __endereço IP do serviço Deps de serviço Kubernetes,__ insira o endereço IP de serviço DE SERVIÇO Kubernetes. Este endereço IP é atribuído ao serviço DNS da Kubernetes. Deve estar dentro da gama de endereços de serviço Kubernetes (por exemplo, 10.0.0.10).
    1. Na caixa de endereços da __ponte Docker,__ insira o endereço da ponte Docker. Este endereço IP é atribuído à Ponte Docker. Não deve estar em nenhuma gama IP de sub-rede, nem na gama de endereços de serviço Kubernetes (por exemplo, 172.17.0.1/16).

   ![Azure Machine Learning: Machine Learning Compute configurações de rede virtual](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Certifique-se de que o grupo NSG que controla a rede virtual tem uma regra de segurança de entrada ativada para o ponto final de pontuação para que possa ser chamada de fora da rede virtual.
   > [!IMPORTANT]
   > Mantenha as regras de saída padrão para o NSG. Para obter mais informações, consulte as regras de segurança em [grupos de segurança.](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)

   [![Uma regra de segurança de entrada](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

Também pode utilizar o Azure Machine Learning SDK para adicionar o Serviço Azure Kubernetes numa rede virtual. Se já tiver um cluster AKS numa rede virtual, prenda-o ao espaço de trabalho descrito como descrito em [Como implantar para AKS](how-to-deploy-and-where.md). O seguinte código cria uma nova instância AKS na `default` sub-rede de uma rede virtual chamada `mynetwork` :

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
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

Quando o processo de criação estiver concluído, pode executar inferência, ou pontuação de modelos, num cluster AKS por trás de uma rede virtual. Para mais informações, consulte [Como implementar para a AKS](how-to-deploy-and-where.md).

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Use IPs privados com serviço Azure Kubernetes

Por predefinição, um endereço IP público é atribuído a implementações AKS. Ao utilizar a AKS dentro de uma rede virtual, pode utilizar um endereço IP privado. Os endereços IP privados só são acessíveis a partir de dentro da rede virtual ou redes aderidas.

Um endereço IP privado é ativado configurando a AKS para usar um _equilibrador de carga interno_. 

> [!IMPORTANT]
> Não é possível ativar o IP privado ao criar o cluster de Serviço Azure Kubernetes. Deve ser ativado como uma atualização para um cluster existente.

O seguinte corte de código demonstra como **criar um novo cluster AKS**e, em seguida, atualizá-lo para usar um equilibrador de carga IP/interno privado:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__CLI do Azure__

```azurecli-interactive
az rest --method put --uri https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace>/computes/<compute>?api-version=2018-11-19 --body @body.json
```

O conteúdo do `body.json` ficheiro referenciado pelo comando é semelhante ao seguinte documento JSON:

```json
{ 
    "location": "<region>", 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-name>", 
        "computeType": "AKS", 
        "provisioningState": "Succeeded", 
        "properties": { 
            "loadBalancerType": "InternalLoadBalancer", 
            "agentCount": <agent-count>, 
            "agentVmSize": "vm-size", 
            "clusterFqdn": "<cluster-fqdn>" 
        } 
    } 
} 
```

> [!NOTE]
> Atualmente, não é possível configurar o equilibrador de carga ao efetuar uma operação __de anexação__ num cluster existente. Primeiro deve fixar o cluster e, em seguida, executar uma operação de atualização para alterar o balançador de carga.

Para obter mais informações sobre a utilização do balançador de carga interno com AKS, consulte [utilizar o balançador interno de carga com o Serviço Azure Kubernetes](/azure/aks/internal-lb).

## <a name="use-azure-container-instances-aci"></a>Utilizar instâncias de contentores Azure (ACI)

As instâncias do recipiente Azure são criadas dinamicamente ao implementar um modelo. Para permitir que o Azure Machine Learning crie ACI dentro da rede virtual, deve ativar a delegação de __sub-redes__ para a sub-rede utilizada pela implantação.

> [!WARNING]
> Para utilizar as instâncias do contentor Azure dentro da rede virtual, o Registo de Contentores Azure (ACR) para o seu espaço de trabalho também não pode estar na rede virtual.

Para utilizar o ACI numa rede virtual para o seu espaço de trabalho, utilize os seguintes passos:

1. Para ativar a delegação de sub-redes na sua rede virtual, utilize as informações no Add ou remova um artigo [de delegação de sub-rede.](../virtual-network/manage-subnet-delegation.md) Pode ativar a delegação ao criar uma rede virtual ou adicioná-la a uma rede existente.

    > [!IMPORTANT]
    > Ao ativar a delegação, utilize `Microsoft.ContainerInstance/containerGroups` como __sub-rede delegado o__ valor de serviço.

2. Implementar o modelo utilizando [a AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-), utilize os `vnet_name` parâmetros e `subnet_name` parâmetros. Deslo sente estes parâmetros no nome e na sub-rede de rede virtuais onde permitiu a delegação.

## <a name="azure-firewall"></a>Azure Firewall

Para obter informações sobre a utilização de Azure Machine Learning com Azure Firewall, consulte [o espaço de trabalho de aprendizagem da máquina Azure por trás do Azure Firewall](how-to-access-azureml-behind-firewall.md).

## <a name="azure-container-registry"></a>Registo de Contentores do Azure

> [!IMPORTANT]
> O Registo do Contentor de Azure (ACR) pode ser colocado dentro de uma rede virtual, no entanto deve cumprir os seguintes requisitos:
>
> * O seu espaço de trabalho Azure Machine Learning deve ser a edição da Enterprise. Para obter informações sobre a atualização, consulte [a edição "Upgrade to Enterprise".](how-to-manage-workspace.md#upgrade)
> * O seu registo de contentores Azure deve ser versão Premium . Para obter mais informações sobre a atualização, consulte [a Alteração de SKUs](/azure/container-registry/container-registry-skus#changing-skus).
> * O registo do seu contentor Azure deve estar na mesma rede virtual e sub-rede que a conta de armazenamento e os alvos de cálculo utilizados para treino ou inferência.
> * O seu espaço de trabalho de aprendizagem automática Azure deve conter um [cluster de cálculo de aprendizagem automática Azure.](how-to-set-up-training-targets.md#amlcompute)
>
>     Quando o ACR está por detrás de uma rede virtual, o Azure Machine Learning não pode usá-lo para construir diretamente imagens do Docker. Em vez disso, o cluster computacional é usado para construir as imagens.

1. Para encontrar o nome do Registo do Contentor Azure para o seu espaço de trabalho, utilize um dos seguintes métodos:

    __Portal do Azure__

    A partir da secção de visão geral do seu espaço de trabalho, o valor do __registo__ liga-se ao Registo do Contentor de Azure.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Registo do contentor de Azure para o espaço de trabalho" border="true":::

    __CLI do Azure__

    Se [tiver instalado a extensão machine learning para Azure CLI,](reference-azure-machine-learning-cli.md)pode utilizar o comando para mostrar as `az ml workspace show` informações do espaço de trabalho.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Este comando devolve um valor semelhante a `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` . A última parte da cadeia é o nome do Registo do Contentor Azure para o espaço de trabalho.

1. Para limitar o acesso à sua rede virtual, utilize os passos no acesso à [rede Configure para registo.](../container-registry/container-registry-vnet.md#configure-network-access-for-registry) Ao adicionar a rede virtual, selecione a rede virtual e a sub-rede para os seus recursos de Aprendizagem de Máquinas Azure.

1. Use o Azure Machine Learning Python SDK para configurar um cluster de computação para construir imagens de estivadores. O seguinte código snippet demonstra como fazê-lo:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > A sua conta de armazenamento, cluster compute e registo de contentores Azure devem estar todos na mesma sub-rede da rede virtual.
    
    Para obter mais informações, consulte a referência do método [de atualização.).](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-)

1. Deve aplicar o seguinte modelo de Gestor de Recursos Azure. Este modelo permite que o seu espaço de trabalho comunique com a ACR.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "enterprise",
            "name": "enterprise"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```
    
## <a name="azure-data-lake-storage"></a>Armazenamento do Azure Data Lake

Azure Data Lake Storage Gen 2 é um conjunto de capacidades para análise de big data, construído no armazenamento de Azure Blob. Pode ser usado para armazenar dados usados para treinar modelos com Azure Machine Learning. 

Para utilizar o Data Lake Storage Gen 2 dentro da rede virtual do seu espaço de trabalho Azure Machine Learning, utilize os seguintes passos:

1. Crie uma conta Azure Data Lake Storage gen 2. Para obter mais informações, consulte [Criar uma conta de armazenamento Azure Data Lake Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

1. Utilize os passos 2-4 na secção anterior, [Utilize uma conta de armazenamento para o seu espaço de trabalho](#use-a-storage-account-for-your-workspace), para colocar a conta na rede virtual.

Quando utilizar a Azure Machine Learning com data lake storage Gen 2 dentro de uma rede virtual, utilize as seguintes orientações:

* Se utilizar o __SDK para criar um conjunto de dados__, e o sistema que executa o código __não estiver na rede virtual,__ utilize o `validate=False` parâmetro. Este parâmetro ignora a validação, que falha se o sistema não estiver na mesma rede virtual que a conta de armazenamento. Para mais informações, consulte o método [from_files().](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-)

* Ao utilizar o Azure Machine Learning Compute Instance ou o cluster compute para formar um modelo utilizando o conjunto de dados, deve estar na mesma rede virtual que a conta de armazenamento.

## <a name="key-vault-instance"></a>Caso do cofre chave 

A instância chave do cofre que está associada ao espaço de trabalho é usada pela Azure Machine Learning para armazenar as seguintes credenciais:
* A cadeia de ligação de conta de armazenamento associada
* Palavras-passe para instâncias de repositório de contentores Azure
* Cadeias de ligação a lojas de dados

Para utilizar as capacidades de experimentação de aprendizagem automática Azure com cofre de chaves Azure por trás de uma rede virtual, utilize os seguintes passos:

1. Vá ao cofre que está associado ao espaço de trabalho.

   [![O cofre-chave que está associado ao espaço de trabalho Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. Na página **Key Vault,** no painel esquerdo, selecione __Firewalls e redes virtuais__.

   ![A secção "Firewalls e redes virtuais" no painel do Cofre de Chaves](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. Na página __firewalls e redes virtuais,__ faça as seguintes ações:
    - Ao __Permitir o acesso a redes__ __selecionadas.__
    - Nas __redes Virtuais,__ selecione __Adicionar as redes virtuais existentes__ para adicionar a rede virtual onde reside o seu compute de experimentação.
    - Em __'Permitir que os serviços fidedignos da Microsoft' contornem esta firewall__, selecione __Sim__.

   [![A secção "Firewalls e redes virtuais" no painel do Cofre de Chaves](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)


## <a name="azure-databricks"></a>Azure Databricks

Para utilizar a Azure Databricks numa rede virtual com o seu espaço de trabalho, os seguintes requisitos devem ser cumpridos:

> [!div class="checklist"]
> * A rede virtual deve estar na mesma subscrição e região que o espaço de trabalho Azure Machine Learning.
> * Se a conta de armazenamento Azure para o espaço de trabalho também estiver protegida numa rede virtual, devem estar na mesma rede virtual que o cluster Azure Databricks.
> * Além das __sub-redes databricks-private__ e __databricks-public__ utilizadas pela Azure Databricks, a sub-rede __padrão__ criada para a rede virtual também é necessária.

Para obter informações específicas sobre a utilização de databricks Azure com uma rede virtual, consulte [Implementar databricks no seu Azure Virtual Network](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Máquina virtual ou cluster HDInsight

> [!IMPORTANT]
> A Azure Machine Learning suporta apenas máquinas virtuais que estão a executar Ubuntu.

Para utilizar uma máquina virtual ou cluster Azure HDInsight numa rede virtual com o seu espaço de trabalho, utilize os seguintes passos:

1. Crie um cluster VM ou HDInsight utilizando o portal Azure ou o CLI Azure, e coloque o cluster numa rede virtual Azure. Para obter mais informações, veja os seguintes artigos:
    * [Criar e gerir redes virtuais Azure para Os VMs Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Estender o HDInsight usando uma rede virtual Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Para permitir que o Azure Machine Learning comunique com a porta SSH no VM ou cluster, configure uma entrada de origem para o grupo de segurança da rede. A porta SSH é normalmente a porta 22. Para permitir o tráfego a partir desta fonte, faça as seguintes ações:

    * Na __Source__ lista de drop-down Source, selecione __Tag de Serviço__.

    * Na lista de drop-down de __marca de serviço Source,__ selecione __AzureMachineLearning__.

    * Na lista de pontos de entrega da __porta Source,__ selecione __*__ .

    * Na lista de drop-down do __Destino,__ selecione __Any__.

    * Na lista de pontos de entrega das __gamas destination,__ selecione __22__.

    * Ao abrigo __do Protocolo__, selecione __Qualquer__.

    * Em __Ação__, __selecione Permitir__.

   ![Regras de entrada para fazer experimentação num cluster VM ou HDInsight dentro de uma rede virtual](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Mantenha as regras de saída padrão para o grupo de segurança da rede. Para obter mais informações, consulte as regras de segurança em [grupos de segurança.](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)

    Se não quiser utilizar as regras de saída predefinidos e pretender limitar o acesso de saída da sua rede virtual, consulte a conectividade de saída limite limite da secção [de rede virtual.](#limiting-outbound-from-vnet)

1. Fixe o cluster VM ou HDInsight ao seu espaço de trabalho de aprendizagem de máquinas Azure. Para obter mais informações, consulte [Configurar metas de computação para a formação de modelos.](how-to-set-up-training-targets.md)


## <a name="next-steps"></a>Passos seguintes

* [Configurar ambientes de preparação](how-to-set-up-training-targets.md)
* [Configurar pontos finais privados](how-to-configure-private-link.md)
* [Onde implementar os modelos](how-to-deploy-and-where.md)
* [Utilize o TLS para garantir um serviço web através do Azure Machine Learning](how-to-secure-web-service.md)
