---
title: Ambientes de formação seguros com redes virtuais
titleSuffix: Azure Machine Learning
description: Utilize uma Rede Virtual Azure isolada para garantir o seu ambiente de treino de aprendizagem de máquinas Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 07/16/2020
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: 81a6a6aeb826ba4a61fd4f0a4abdb63fe3674bc4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103573732"
---
# <a name="secure-an-azure-machine-learning-training-environment-with-virtual-networks"></a>Garantir um ambiente de formação Azure Machine Learning com redes virtuais

Neste artigo, você aprende a garantir ambientes de formação com uma rede virtual em Azure Machine Learning.

Este artigo é parte três de uma série de cinco partes que o acompanha através da garantia de um fluxo de trabalho de Aprendizagem automática Azure. Recomendamos vivamente que leia a [primeira parte: visão geral do VNet](how-to-network-security-overview.md) para entender primeiro a arquitetura geral. 

Veja os outros artigos desta série:

[1. Visão geral da VNet](how-to-network-security-overview.md)  >  [2. Proteja o espaço de trabalho](how-to-secure-workspace-vnet.md)  >  **3. Proteja o ambiente de treino**  >  [4. Fixe o ambiente de inferencção](how-to-secure-inferencing-vnet.md)   >  [5. Ativar a funcionalidade do estúdio](how-to-enable-studio-virtual-network.md)

Neste artigo aprende-se a garantir os seguintes recursos de computação de formação numa rede virtual:
> [!div class="checklist"]
> - Aglomerado de computação Azure Machine Learning
> - Instância de computação do Azure Machine Learning
> - Azure Databricks
> - Máquina Virtual
> - Cluster do HDInsight

## <a name="prerequisites"></a>Pré-requisitos

+ Leia o artigo [de visão geral de segurança da Rede](how-to-network-security-overview.md) para entender cenários de rede virtual comuns e arquitetura de rede virtual geral.

+ Uma rede virtual existente e uma sub-rede para usar com os seus recursos de computação.

+ Para implantar recursos numa rede virtual ou numa sub-rede, a sua conta de utilizador deve ter permissões para as seguintes ações no controlo de acesso baseado em funções Azure (Azure RBAC):

    - "Microsoft.Network/virtualNetworks/join/action" no recurso de rede virtual.
    - "Microsoft.Network/virtualNetworks/subnet/join/action" no recurso sub-rede.

    Para obter mais informações sobre o Azure RBAC com networking, consulte as [funções embutidas em rede](../role-based-access-control/built-in-roles.md#networking)


## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Clusters de computação & instâncias 

Para utilizar um [ __alvo de computação__ Azure Machine Learning gerido](concept-compute-target.md#azure-machine-learning-compute-managed) ou um [ __caso__ de computação Azure Machine Learning](concept-compute-instance.md) numa rede virtual, devem ser cumpridos os seguintes requisitos de rede:

> [!div class="checklist"]
> * A rede virtual deve estar na mesma subscrição e região que o espaço de trabalho Azure Machine Learning.
> * A sub-rede especificada para a instância de computação ou cluster deve ter endereços IP não atribuídos suficientes para acomodar o número de VMs que são alvo. Se a sub-rede não tiver endereços IP não atribuídos suficientes, um cluster de cálculo será parcialmente atribuído.
> * Verifique se as suas políticas de segurança ou bloqueios na subscrição da rede virtual ou no grupo de recursos restringem permissões para gerir a rede virtual. Se planeia proteger a rede virtual restringindo o tráfego, deixe algumas portas abertas para o serviço de computação. Para mais informações, consulte a secção [de portas necessárias.](#mlcports)
> * Se vai colocar várias instâncias de computação ou clusters numa rede virtual, poderá ter de solicitar um aumento de quota para um ou mais dos seus recursos.
> * Se a conta de armazenamento Azure para o espaço de trabalho também estiver protegida numa rede virtual, devem estar na mesma rede virtual que o azure machine learning ou cluster. 
> * Para que a funcionalidade do Jupyter funcione, certifique-se de que a comunicação da tomada web não está desativada. Certifique-se de que a sua rede permite ligações websocket a *.instances.azureml.net e *.instances.azureml.ms. 
> * Quando a instância de computação é implantada num espaço de trabalho de ligação privada, só pode ser acedida a partir de uma rede virtual. Se estiver a utilizar o ficheiro DNS ou hostis personalizado, por favor adicione uma entrada `<instance-name>.<region>.instances.azureml.ms` com o endereço IP privado do ponto final privado do espaço de trabalho. Para mais informações consulte o artigo [DNS personalizado.](./how-to-custom-dns.md)
> * A sub-rede utilizada para implantar cluster/instância computacional não deve ser delegada em qualquer outro serviço como o ACI
> * As políticas de ponto final do serviço de rede virtual não funcionam para contas de armazenamento de cluster/caso de cálculo

    
> [!TIP]
> A instância ou cluster do cálculo machine learning aloca automaticamente recursos adicionais de rede __no grupo de recursos que contém a rede virtual.__ Para cada instância de cálculo ou cluster, o serviço aloca os seguintes recursos:
> 
> * Um grupo de segurança de rede
> * Um endereço IP público. Se tiver uma política do Azure que proíba a criação de IP público, a implantação de cluster/instâncias falhará
> * Um equilibrador de carga
> 
> No caso dos clusters, estes recursos são eliminados (e recriados) sempre que o cluster se reduz a 0 nós, no entanto, por exemplo, os recursos são mantidos até que a instância seja completamente eliminada (parar não retira os recursos). 
> Estes recursos estão limitados pelas [quotas de recursos](../azure-resource-manager/management/azure-subscription-service-limits.md) da subscrição. Se o grupo de recursos de rede virtual estiver bloqueado, a supressão do cluster/instância do cálculo falhará. O balançador de carga não pode ser eliminado até que o cluster/instância computacional seja eliminado. Além disso, certifique-se de que não existe uma política do Azure que proíba a criação de grupos de segurança em rede.


### <a name="required-ports"></a><a id="mlcports"></a> Portos necessários

Se planeia proteger a rede virtual restringindo o tráfego de rede de/para a internet pública, deve permitir comunicações de entrada a partir do serviço Azure Batch.

O serviço Batch adiciona grupos de segurança de rede (NSGs) ao nível das interfaces de rede (NICs) que estão ligadas aos VMs. Estes NSGs configuram automaticamente regras de entrada e saída para permitir o tráfego seguinte:

- Tráfego TCP de entrada nos portos 29876 e 29877 a partir de uma etiqueta de __serviço__ de __BatchNodeManagement__. O tráfego sobre estas portas é encriptado e é usado pelo Azure Batch para comunicação agendador/nó.

    ![Uma regra de entrada que usa a etiqueta de serviço BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (Opcional) Tráfego TCP de entrada na porta 22 para permitir o acesso remoto. Utilize esta porta apenas se pretender ligar utilizando sSH no IP público.

- Tráfego de saída em qualquer porta para a rede virtual.

- Tráfego de saída em qualquer porta para a Internet.

- Para o porputação, o tráfego TCP no porto 44224 a partir de uma etiqueta de __serviço__ da __AzureMachineLearning__. O tráfego sobre esta porta é encriptado e é usado pela Azure Machine Learning para comunicação com aplicações em execução em Instâncias computacional.

> [!IMPORTANT]
> Tenha cuidado se modificar ou adicionar regras de entrada ou saída em NSGs configurados pelo Batch. Se um NSG bloqueia a comunicação aos nós de computação, o serviço de computação define o estado dos nós de computação para inutilizável.
>
> Não precisa de especificar NSGs ao nível da sub-rede, porque o serviço Azure Batch configura os seus próprios NSGs. No entanto, se a sub-rede que contém o cálculo Azure Machine Learning tiver associado NSGs ou uma firewall, também deve permitir o tráfego listado anteriormente.

A configuração da regra NSG no portal Azure é mostrada nas seguintes imagens:

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="As regras NSG de entrada para o Cálculo de Aprendizagem automática" border="true":::



![Regras NSG de entrada para o cálculo de aprendizagem automática](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a> Limitar a conectividade de saída da rede virtual

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

### <a name="forced-tunneling"></a>Túnel forçado

Se estiver a utilizar [um túnel forçado](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) com o cálculo Azure Machine Learning, deve permitir a comunicação com a internet pública a partir da sub-rede que contém o recurso computacional. Esta comunicação é utilizada para agendamento de tarefas e acesso ao Armazenamento Azure.

Há duas maneiras de conseguir isto:

* Utilize um [NAT de rede virtual.](../virtual-network/nat-overview.md) Um gateway NAT fornece conectividade de saída da Internet para uma ou mais sub-redes na sua rede virtual. Para obter informações, consulte [conceber redes virtuais com recursos de gateway NAT.](../virtual-network/nat-gateway-resource.md)

* Adicione [as rotas definidas pelo utilizador (UDRs)](../virtual-network/virtual-networks-udr-overview.md) à sub-rede que contém o recurso compute. Estabeleça uma UDR para cada endereço IP que seja utilizado pelo serviço Azure Batch na região onde os seus recursos existem. Estes UDRs permitem ao serviço Batch comunicar com nós computacional para agendamento de tarefas. Adicione também o endereço IP para o serviço Azure Machine Learning, uma vez que este é necessário para o acesso a Instâncias computacional. Ao adicionar o IP para o serviço de Aprendizagem automática Azure, deve adicionar o IP para as regiões __Azure primária e secundária.__ A região primária é aquela onde o seu espaço de trabalho está localizado.

    Para encontrar a região secundária, consulte a garantia de [continuidade do negócio & recuperação de desastres utilizando regiões emparelhadas Azure.](../best-practices-availability-paired-regions.md#azure-regional-pairs) Por exemplo, se o seu serviço de Machine Learning Azure estiver no Leste dos EUA 2, a região secundária é a Central EUA. 

    Para obter uma lista de endereços IP do serviço Batch e do serviço Azure Machine Learning, utilize um dos seguintes métodos:

    * Faça o download das [Gamas IP E Tags de Serviço Azure](https://www.microsoft.com/download/details.aspx?id=56519) e procure no ficheiro `BatchNodeManagement.<region>` `AzureMachineLearning.<region>` e, onde `<region>` fica a sua região Azure.

    * Utilize o [CLI Azure](/cli/azure/install-azure-cli) para descarregar as informações. O exemplo a seguir descarrega as informações do endereço IP e filtra as informações para a região leste dos EUA 2 (primária) e central dos EUA (secundária):

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        # Get primary region IPs
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        # Get secondary region IPs
        az network list-service-tags -l "Central US" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='centralus']"
        ```

        > [!TIP]
        > Se estiver a utilizar as regiões EUA-Virgínia, US-Arizona ou China-Leste-2, estes comandos não devolvem endereços IP. Em vez disso, utilize um dos seguintes links para descarregar uma lista de endereços IP:
        >
        > * [Gamas IP Azure e tags de serviço para o Governo Azure](https://www.microsoft.com/download/details.aspx?id=57063)
        > * [Gamas Azure IP e tags de serviço para Azure China](https://www.microsoft.com//download/details.aspx?id=57062)
    
    Quando adicionar os UDRs, defina a rota para cada prefixo de endereço IP do lote relacionado e defina __o tipo de próximo lúpulo__ para a __Internet__. A imagem a seguir mostra um exemplo desta UDR no portal Azure:

    ![Exemplo de um UDR para um prefixo de endereço](./media/how-to-enable-virtual-network/user-defined-route.png)

    > [!IMPORTANT]
    > Os endereços IP podem ser alterados ao longo do tempo.

    Além de quaisquer UDRs que defina, o tráfego de saída para o Azure Storage deve ser permitido através do seu aparelho de rede no local. Especificamente, os URLs para este tráfego estão nas seguintes formas: `<account>.table.core.windows.net` `<account>.queue.core.windows.net` , e `<account>.blob.core.windows.net` . 

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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="access-data-in-a-compute-instance-notebook"></a>Aceder a dados num caderno de computação

Se estiver a utilizar cadernos numa instância do Azure Compute, tem de se certificar de que o seu caderno está a funcionar num recurso computacional por trás da mesma rede virtual e sub-redes que os seus dados. 

Tem de configurar a sua Instância computacional para estar na mesma rede virtual durante a criação em **Configurações Avançadas**  >  **Configurar rede virtual**. Não é possível adicionar uma Instância de Computação existente a uma rede virtual.

## <a name="azure-databricks"></a>Azure Databricks

Para utilizar a Azure Databricks numa rede virtual com o seu espaço de trabalho, os seguintes requisitos devem ser cumpridos:

> [!div class="checklist"]
> * A rede virtual deve estar na mesma subscrição e região que o espaço de trabalho Azure Machine Learning.
> * Se a conta de armazenamento Azure para o espaço de trabalho também estiver protegida numa rede virtual, devem estar na mesma rede virtual que o cluster Azure Databricks.
> * Além das __sub-redes databricks-private__ e __databricks-public__ utilizadas pela Azure Databricks, a sub-rede __padrão__ criada para a rede virtual também é necessária.

Para obter informações específicas sobre a utilização de databricks Azure com uma rede virtual, consulte [Implementar databricks no seu Azure Virtual Network](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Máquina virtual ou cluster HDInsight

> [!IMPORTANT]
> A Azure Machine Learning suporta apenas máquinas virtuais que estão a executar Ubuntu.

Nesta secção aprende-se a utilizar uma máquina virtual ou um cluster Azure HDInsight numa rede virtual com o seu espaço de trabalho.

### <a name="create-the-vm-or-hdinsight-cluster"></a>Criar o cluster VM ou HDInsight

Crie um cluster VM ou HDInsight utilizando o portal Azure ou o CLI Azure, e coloque o cluster numa rede virtual Azure. Para obter mais informações, veja os seguintes artigos:
* [Criar e gerir redes virtuais Azure para Os VMs Linux](../virtual-machines/linux/tutorial-virtual-network.md)

* [Estender o HDInsight usando uma rede virtual Azure](../hdinsight/hdinsight-plan-virtual-network-deployment.md)

### <a name="configure-network-ports"></a>Configure portas de rede 

Permitir que o Azure Machine Learning comunique com a porta SSH no VM ou cluster, configurar uma entrada de origem para o grupo de segurança da rede. A porta SSH é normalmente a porta 22. Para permitir o tráfego a partir desta fonte, faça as seguintes ações:

1. Na  lista de drop-down Source, selecione __Tag de Serviço__.

1. Na lista de drop-down de __marca de serviço Source,__ selecione __AzureMachineLearning__.

    ![Regras de entrada para fazer experimentação num cluster VM ou HDInsight dentro de uma rede virtual](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

1. Na lista de pontos de entrega da __porta Source,__ selecione __*__ .

1. Na lista de drop-down do __Destino,__ selecione __Any__.

1. Na lista de pontos de entrega das __gamas destination,__ selecione __22__.

1. Ao abrigo __do Protocolo__, selecione __Qualquer__.

1. Em __Ação__, __selecione Permitir__.

Mantenha as regras de saída padrão para o grupo de segurança da rede. Para obter mais informações, consulte as regras de segurança em [grupos de segurança.](../virtual-network/network-security-groups-overview.md#default-security-rules)

Se não quiser utilizar as regras de saída predefinidos e pretender limitar o acesso de saída da sua rede virtual, consulte a conectividade de saída limite limite da secção [de rede virtual.](#limiting-outbound-from-vnet)

### <a name="attach-the-vm-or-hdinsight-cluster"></a>Anexar o cluster VM ou HDInsight

Fixe o cluster VM ou HDInsight ao seu espaço de trabalho de aprendizagem de máquinas Azure. Para obter mais informações, consulte [Configurar metas de computação para a formação de modelos.](how-to-set-up-training-targets.md)

## <a name="next-steps"></a>Passos seguintes

Este artigo é parte três de uma série de rede virtual de cinco partes. Veja o resto dos artigos para aprender a proteger uma rede virtual:

* [Parte 1: Visão geral da rede virtual](how-to-network-security-overview.md)
* [Parte 2: Garantir os recursos do espaço de trabalho](how-to-secure-workspace-vnet.md)
* [Parte 4: Assegurar o ambiente de inferencção](how-to-secure-inferencing-vnet.md)
* [Parte 5: Ativar a funcionalidade do estúdio](how-to-enable-studio-virtual-network.md)

Consulte também o artigo sobre a utilização [de DNS personalizados](how-to-custom-dns.md) para resolução de nomes.