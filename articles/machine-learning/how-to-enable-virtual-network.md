---
title: Experiências e inferências seguras na rede virtual
titleSuffix: Azure Machine Learning
description: Saiba como proteger trabalhos de experimentação/treinamento e trabalhos de inferência/pontuação no Azure Machine Learning em uma rede virtual do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 01/13/2020
ms.openlocfilehash: 53644066276aa8e9fb57b4802142bca3fe4b342f
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760861"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>Proteger trabalhos de experimentação e de inferência do Azure ML em uma rede virtual do Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a proteger trabalhos de experimentação/treinamento e trabalhos de inferência/pontuação no Azure Machine Learning em uma vnet (rede virtual) do Azure.

Uma **rede virtual** age como um limite de segurança, isolando os recursos do Azure da Internet pública. Você também pode unir uma rede virtual do Azure à sua rede local. Ao ingressar em redes, você pode treinar seus modelos com segurança e acessar seus modelos implantados para inferência.

Azure Machine Learning se baseia em outros serviços do Azure para recursos de computação. Os recursos de computação ou os [destinos de computação](concept-compute-target.md)são usados para treinar e implantar modelos. Os destinos podem ser criados em uma rede virtual. Por exemplo, você pode usar o Microsoft Máquina Virtual de Ciência de Dados para treinar um modelo e, em seguida, implantar o modelo no AKS (serviço kubernetes do Azure). Para obter mais informações sobre redes virtuais, consulte [visão geral da rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Este artigo também fornece informações detalhadas sobre *configurações de segurança avançadas*, informações que não são necessárias para casos de uso básico ou experimental. Determinadas seções deste artigo fornecem informações de configuração para uma variedade de cenários. Você não precisa concluir as instruções em ordem ou em sua totalidade.

> [!TIP]
> A menos que especificamente chamado, o uso de recursos como contas de armazenamento ou destinos de computação dentro de uma rede virtual funcionará com pipelines de aprendizado de máquina e fluxos de trabalho sem pipeline, como execuções de script.

> [!WARNING]
> A Microsoft não dá suporte ao uso do designer de Azure Machine Learning ou do Machine Learning automatizado (do estúdio) com recursos dentro de uma rede virtual.

## <a name="prerequisites"></a>Pré-requisitos

+ Um [espaço de trabalho](how-to-manage-workspace.md)Azure Machine Learning.

+ Conhecimento geral de trabalho do [serviço de rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) e da [rede IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Uma rede virtual e sub-rede pré-existentes para usar com seus recursos de computação.

## <a name="use-a-storage-account-for-your-workspace"></a>Usar uma conta de armazenamento para seu espaço de trabalho

Para usar uma conta de armazenamento do Azure para o espaço de trabalho em uma rede virtual, use as seguintes etapas:

1. Crie um recurso de computação (por exemplo, um Machine Learning cluster ou instância de computação) atrás de uma rede virtual ou anexe um recurso de computação ao espaço de trabalho (por exemplo, um cluster HDInsight, uma máquina virtual ou um cluster do serviço kubernetes do Azure). O recurso de computação pode ser para a experimentação ou implantação de modelo.

   Para obter mais informações, consulte as seções [usar uma Machine Learning computação](#amlcompute), [usar uma máquina virtual ou um cluster HDInsight](#vmorhdi)e [usar o serviço kubernetes do Azure](#aksvnet) neste artigo.

1. No portal do Azure, vá para o armazenamento que está anexado ao seu espaço de trabalho.

   [![O armazenamento que está ligado ao espaço de trabalho azure machine learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Na página **armazenamento do Azure** , selecione __firewalls e redes virtuais__.

   ![A área "firewalls e redes virtuais" na página de armazenamento do Azure no portal do Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Na página __firewalls e redes virtuais__ , execute as seguintes ações:
    - Selecione __Redes selecionadas__.
    - Em __redes virtuais__, selecione o link __Adicionar rede virtual existente__ . Essa ação adiciona a rede virtual onde sua computação reside (consulte a etapa 1).

        > [!IMPORTANT]
        > A conta de armazenamento deve estar na mesma rede virtual que as instâncias de computação ou clusters usados para treinamento ou inferência.

    - Marque a caixa de seleção __permitir que os serviços confiáveis da Microsoft acessem esta conta de armazenamento__ .

    > [!IMPORTANT]
    > Ao trabalhar com o SDK do Azure Machine Learning, seu ambiente de desenvolvimento deve ser capaz de se conectar à conta de armazenamento do Azure. Quando a conta de armazenamento está dentro de uma rede virtual, o firewall deve permitir o acesso do endereço IP do ambiente de desenvolvimento.
    >
    > Para habilitar o acesso à conta de armazenamento, visite __firewalls e redes virtuais__ para a conta de armazenamento em *um navegador da Web no cliente de desenvolvimento*. Em seguida, use a caixa de seleção __Adicionar seu endereço IP do cliente__ para adicionar o endereço IP do cliente ao __intervalo de endereços__. Você também pode usar o campo __intervalo de endereços__ para inserir manualmente o endereço IP do ambiente de desenvolvimento. Depois que o endereço IP do cliente tiver sido adicionado, ele poderá acessar a conta de armazenamento usando o SDK.

   [![O painel "Firewalls and virtual networks" no portal Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> Você pode posicionar a _conta de armazenamento padrão_ para Azure Machine Learning ou _contas de armazenamento não padrão_ em uma rede virtual.
>
> A conta de armazenamento padrão é provisionada automaticamente quando você cria um espaço de trabalho.
>
> Para contas de armazenamento não predefinidas, o parâmetro `storage_account` na [função`Workspace.create()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) permite especificar uma conta de armazenamento personalizada pelo ID de recursos Azure.

## <a name="use-azure-data-lake-storage-gen-2"></a>Usar Azure Data Lake Storage Gen 2

O Azure Data Lake Storage Gen 2 é um conjunto de recursos para Big Data Analytics, criado com base no armazenamento de BLOBs do Azure. Ele pode ser usado para armazenar dados usados para treinar modelos com Azure Machine Learning. 

Para usar Data Lake Storage Gen 2 dentro da rede virtual do seu espaço de trabalho Azure Machine Learning, use as seguintes etapas:

1. Crie uma conta Azure Data Lake Storage Gen 2. Para obter mais informações, consulte [criar uma conta de armazenamento Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

1. Use as etapas 2-4 na seção anterior, [use uma conta de armazenamento para seu espaço de trabalho](#use-a-storage-account-for-your-workspace), para colocar a conta na rede virtual.

Ao usar Azure Machine Learning com Data Lake Storage Gen 2 dentro de uma rede virtual, use as seguintes diretrizes:

* Se você usar o __SDK para criar um conjunto de um__e o sistema que executa o código __não estiver na rede virtual__, use o parâmetro `validate=False`. Esse parâmetro ignora a validação, o que falhará se o sistema não estiver na mesma rede virtual que a conta de armazenamento. Para mais informações, consulte o método [from_files().](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-)

* Ao usar Azure Machine Learning instância de computação ou cluster de computação para treinar um modelo usando o conjunto de um, ele deve estar na mesma rede virtual que a conta de armazenamento.

## <a name="use-a-key-vault-instance-with-your-workspace"></a>Usar uma instância do Key Vault com seu espaço de trabalho

A instância do cofre de chaves associada ao espaço de trabalho é usada pelo Azure Machine Learning para armazenar as seguintes credenciais:
* A cadeia de conexão da conta de armazenamento associada
* Senhas para instâncias do repositório de contêiner do Azure
* Cadeias de conexão para armazenamentos de dados

Para usar Azure Machine Learning recursos de experimentação com Azure Key Vault por trás de uma rede virtual, use as seguintes etapas:

1. Vá para o cofre de chaves associado ao espaço de trabalho.

   [![O cofre chave que está associado ao espaço de trabalho azure machine learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. Na página **Key Vault** , no painel esquerdo, selecione __firewalls e redes virtuais__.

   ![A seção "firewalls e redes virtuais" no painel de Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. Na página __firewalls e redes virtuais__ , execute as seguintes ações:
    - Em __permitir acesso de__, selecione __redes selecionadas__.
    - Em __redes virtuais__, selecione __Adicionar redes virtuais existentes__ para adicionar a rede virtual em que a computação de experimentação reside.
    - Em __permitir que os serviços confiáveis da Microsoft ignorem esse firewall__, selecione __Sim__.

   [![a secção "Firewalls e redes virtuais" no painel key vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

<a id="amlcompute"></a>

## <a name="compute-instance"></a>Usar um Computação do Machine Learning

> [!NOTE]
> As instâncias de computação (versão prévia) estão disponíveis no momento somente para espaços de trabalho com uma região de **EUA Central norte** ou **sul do Reino Unido**, com suporte para outras regiões em breve.
> Use uma dessas regiões para criar uma instância de computação que pode ser adicionada à rede virtual.

Para usar uma instância de computação ou cluster de computação Azure Machine Learning em uma rede virtual, os requisitos de rede a seguir devem ser atendidos:

> [!div class="checklist"]
> * A rede virtual deve estar na mesma assinatura e região que o espaço de trabalho Azure Machine Learning.
> * A sub-rede especificada para a instância ou cluster de computação deve ter endereços IP não atribuídos suficientes para acomodar o número de VMs que são direcionadas. Se a sub-rede não tiver endereços IP não atribuídos suficientes, um cluster de computação será parcialmente alocado.
> * Verifique se as políticas de segurança ou os bloqueios na assinatura ou no grupo de recursos da rede virtual restringem as permissões para gerenciar a rede virtual. Se você planeja proteger a rede virtual restringindo o tráfego, deixe algumas portas abertas para o serviço de computação. Para obter mais informações, consulte a seção [portas necessárias](#mlcports) .
> * Se você pretende colocar várias instâncias de computação ou clusters em uma rede virtual, talvez seja necessário solicitar um aumento de cota para um ou mais dos seus recursos.
> * Se as contas de armazenamento do Azure para o espaço de trabalho também estiverem protegidas em uma rede virtual, elas deverão estar na mesma rede virtual que a instância ou cluster de computação Azure Machine Learning. Se você estiver criando uma instância de computação na mesma rede virtual, precisará desanexar as contas de armazenamento da rede virtual, criar a instância de computação na rede virtual e, em seguida, anexar as contas de armazenamento de volta à rede virtual.

A instância ou cluster de computação Machine Learning aloca automaticamente recursos de rede adicionais no grupo de recursos que contém a rede virtual. Para cada instância ou cluster de computação, o serviço aloca os seguintes recursos:

* Um grupo de segurança de rede
* Um endereço IP público
* Um balanceador de carga

Estes recursos estão limitados pelas [quotas de recursos](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) da subscrição.


### <a id="mlcports"></a>Portas necessárias

O Computação do Machine Learning atualmente usa o serviço de lote do Azure para provisionar VMs na rede virtual especificada. A sub-rede deve permitir a comunicação de entrada do serviço de lote. Você usa essa comunicação para agendar execuções nos nós de Computação do Machine Learning e para se comunicar com o armazenamento do Azure e outros recursos. O serviço de lote adiciona NSGs (grupos de segurança de rede) no nível de adaptadores de rede (NICs) anexados às VMs. Estes NSGs configuram automaticamente regras de entrada e saída para permitir o tráfego seguinte:

- Tráfego TCP de entrada nas portas 29876 e 29877 de uma __marca de serviço__ de __BatchNodeManagement__.

    ![Uma regra de entrada que usa a marca de serviço BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- Adicional Tráfego TCP de entrada na porta 22 para permitir o acesso remoto. Use essa porta somente se você quiser se conectar usando SSH no IP público.

- Tráfego de saída em qualquer porta para a rede virtual.

- Tráfego de saída em qualquer porta para a Internet.

- Para o tráfego TCP de entrada da instância de computação na porta 44224 de uma __marca de serviço__ de __AzureMachineLearning__.

Tenha cuidado se modificar ou adicionar regras de entrada ou saída em NSGs configurados pelo Batch. Se um NSG bloquear a comunicação com os nós de computação, o serviço de computação definirá o estado dos nós de computação para inutilizável.

Você não precisa especificar NSGs no nível de sub-rede, pois o serviço de lote do Azure configura seu próprio NSGs. No entanto, se a sub-rede especificada tiver NSGs associado ou um firewall, configure as regras de segurança de entrada e saída, conforme mencionado anteriormente.

A configuração da regra NSG na portal do Azure é mostrada nas seguintes imagens:

[![As regras de nSG de entrada para a Computação de Aprendizagem Automática](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png#lightbox)

![As regras NSG de saída para Computação do Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a>Limitar a conectividade de saída da rede virtual

Se você não quiser usar as regras de saída padrão e quiser limitar o acesso de saída de sua rede virtual, use as seguintes etapas:

- Negue a conexão de Internet de saída usando as regras NSG.

- Para uma __instância de cálculo__ ou um cluster de __cálculo,__ limite o tráfego de saída aos seguintes itens:
   - Armazenamento Azure, utilizando etiqueta de __serviço__ de __armazenamento__
   - Registo de contentores azure, utilizando etiqueta de __serviço__ do __AzureContainerRegistry__
   - Azure Machine Learning, usando a __marca de serviço__ de __AzureMachineLearning__
   
- Para um __cálculo,__ adicione também os seguintes itens:
   - Gestor de Recursos Azure, utilizando etiqueta de __serviço__ do __AzureResourceManager__
   - Diretório Azure Ative, utilizando etiqueta de __serviço__ do __AzureActiveDirectory__

A configuração da regra NSG na portal do Azure é mostrada na imagem a seguir:

[![As regras de nsg de saída para machine learning compute](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Se planeia utilizar imagens predefinidas do Docker fornecidas pela Microsoft e permitir dependências geridas pelo utilizador, também deve utilizar uma etiqueta de __serviço__ do __MicrosoftContainerRegistry.Region_Name__ (por exemplo, MicrosoftContainerRegistry.EastUS).
>
> Essa configuração é necessária quando você tem um código semelhante aos trechos a seguir como parte de seus scripts de treinamento:
>
> __Treinamento de RunConfig__
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
> __Formação de estimador__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="user-defined-routes-for-forced-tunneling"></a>Rotas definidas pelo usuário para túnel forçado

Se você estiver usando o túnel forçado com o Computação do Machine Learning, adicione [rotas definidas pelo usuário (UDRs)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) à sub-rede que contém o recurso de computação.

* Estabeleça um UDR para cada endereço IP usado pelo serviço de lote do Azure na região onde os recursos existem. Esses UDRs permitem que o serviço de lote se comunique com nós de computação para o agendamento de tarefas. Para obter uma lista de endereços IP do serviço de lote, use um dos seguintes métodos:

    * Baixe os [intervalos de IP e as marcas de serviço do Azure](https://www.microsoft.com/download/details.aspx?id=56519) e pesquise o arquivo `BatchNodeManagement.<region>`, em que `<region>` é sua região do Azure.

    * Use o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para baixar as informações. O exemplo a seguir baixa as informações de endereço IP e filtra as informações para a região leste dos EUA 2:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        ```

* O tráfego de saída para o armazenamento do Azure não deve ser bloqueado pelo dispositivo de rede local. Especificamente, as URLs estão no formato `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`e `<account>.blob.core.windows.net`.

Ao adicionar o UDRs, defina a rota para cada prefixo de endereço IP do lote relacionado e defina o __tipo do próximo salto__ como __Internet__. A imagem a seguir mostra um exemplo desse UDR no portal do Azure:

![Exemplo de um UDR para um prefixo de endereço](./media/how-to-enable-virtual-network/user-defined-route.png)

Para obter mais informações, consulte [criar um pool do lote do Azure em uma rede virtual](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Criar um cluster de computação em uma rede virtual

Para criar um cluster Computação do Machine Learning, use as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com), selecione seu espaço de trabalho do Azure Machine Learning.

1. Na seção __aplicativo__ , selecione __computação__e, em seguida, selecione __Adicionar computação__.

1. Para configurar esse recurso de computação para usar uma rede virtual, execute as seguintes ações:

    a. Para __configuração de rede__, selecione __avançado__.

    b. Na lista suspensa __grupo de recursos__ , selecione o grupo de recursos que contém a rede virtual.

    c. Na lista suspensa __rede virtual__ , selecione a rede virtual que contém a sub-rede.

    d. Na lista suspensa __sub-rede__ , selecione a sub-rede a ser usada.

   ![As configurações de rede virtual para Computação do Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Você também pode criar um cluster Computação do Machine Learning usando o SDK do Azure Machine Learning. O código a seguir cria um novo cluster Computação do Machine Learning na sub-rede `default` de uma rede virtual denominada `mynetwork`:

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

Quando o processo de criação for concluído, você treinará seu modelo usando o cluster em um experimento. Para obter mais informações, consulte [selecionar e usar um destino de computação para treinamento](how-to-set-up-training-targets.md).

## <a name="use-azure-databricks"></a>Usar Azure Databricks

Para usar Azure Databricks em uma rede virtual com seu espaço de trabalho, os seguintes requisitos devem ser atendidos:

> [!div class="checklist"]
> * A rede virtual deve estar na mesma assinatura e região que o espaço de trabalho Azure Machine Learning.
> * Se as contas de armazenamento do Azure para o espaço de trabalho também estiverem protegidas em uma rede virtual, elas deverão estar na mesma rede virtual que o cluster de Azure Databricks.
> * Além do __databricks – Private__ e __databricks-sub-redes públicas__ usadas pelo Azure Databricks, a sub-rede __padrão__ criada para a rede virtual também é necessária.

Para obter informações específicas sobre como usar Azure Databricks com uma rede virtual, consulte [implantar Azure Databricks em sua rede virtual do Azure](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Usar uma máquina virtual ou um cluster HDInsight

> [!IMPORTANT]
> Azure Machine Learning dá suporte apenas a máquinas virtuais que executam o Ubuntu.

Para usar uma máquina virtual ou um cluster do Azure HDInsight em uma rede virtual com seu espaço de trabalho, use as seguintes etapas:

1. Crie uma VM ou um cluster HDInsight usando o portal do Azure ou o CLI do Azure e coloque o cluster em uma rede virtual do Azure. Para obter mais informações, veja os artigos seguintes:
    * [Criar e gerenciar redes virtuais do Azure para VMs Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Estender o HDInsight usando uma rede virtual do Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Para permitir que Azure Machine Learning se comunique com a porta SSH na VM ou cluster, configure uma entrada de origem para o grupo de segurança de rede. A porta SSH geralmente é a porta 22. Para permitir o tráfego dessa fonte, execute as seguintes ações:

    * Na lista suspensa __origem__ , selecione __marca de serviço__.

    * Na lista suspensa __marca de serviço de origem__ , selecione __AzureMachineLearning__.

    * Na lista suspensa __intervalos de portas de origem__ , selecione __*__ .

    * Na lista suspensa __destino__ , selecione __qualquer__.

    * Na lista suspensa __intervalos de portas de destino__ , selecione __22__.

    * Em __protocolo__, selecione __qualquer__.

    * Em __ação__, selecione __permitir__.

   ![Regras de entrada para fazer experimentações em uma VM ou cluster HDInsight em uma rede virtual](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Mantenha as regras de saída padrão para o grupo de segurança de rede. Para obter mais informações, consulte as regras de segurança padrão em [grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Se você não quiser usar as regras de saída padrão e quiser limitar o acesso de saída de sua rede virtual, consulte a seção [limitar a conectividade de saída da rede virtual](#limiting-outbound-from-vnet) .

1. Anexe a VM ou o cluster HDInsight ao seu espaço de trabalho Azure Machine Learning. Para obter mais informações, consulte [Configurar destinos de computação para treinamento de modelo](how-to-set-up-training-targets.md).

<a id="aksvnet"></a>

## <a name="use-azure-kubernetes-service-aks"></a>Usar o AKS (serviço kubernetes do Azure)

Para adicionar o AKS em uma rede virtual ao seu espaço de trabalho, use as seguintes etapas:

> [!IMPORTANT]
> Antes de começar o procedimento a seguir, siga os pré-requisitos no como [Configurar a rede avançada no AKs (serviço kubernetes do Azure)](https://docs.microsoft.com/azure/aks/configure-advanced-networking#prerequisites) e planejar o endereçamento IP para o cluster.
>
> A instância AKS e a rede virtual do Azure devem estar na mesma região. Se você proteger as contas de armazenamento do Azure usadas pelo espaço de trabalho em uma rede virtual, elas deverão estar na mesma rede virtual que a instância AKS.

1. No [portal do Azure](https://portal.azure.com), verifique se o NSG que controla a rede virtual tem uma regra de entrada habilitada para Azure Machine Learning usando __AzureMachineLearning__ como a **origem**.

    [![máquina de aprendizagem de máquinas de aprendizagem adicionar painel de computação](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png#lightbox)

1. Selecione seu espaço de trabalho Azure Machine Learning.

1. Na seção __aplicativo__ , selecione __computação__e, em seguida, selecione __Adicionar computação__.

1. Para configurar esse recurso de computação para usar uma rede virtual, execute as seguintes ações:

    - Para __configuração de rede__, selecione __avançado__.

    - Na lista suspensa __grupo de recursos__ , selecione o grupo de recursos que contém a rede virtual.

    - Na lista suspensa __rede virtual__ , selecione a rede virtual que contém a sub-rede.

    - Na lista suspensa __sub-rede__ , selecione a sub-rede.

    - Na caixa __intervalo de endereços de serviço do kubernetes__ , insira o intervalo de endereços do serviço kubernetes. Esse intervalo de endereços usa um intervalo de IP de notação CIDR (roteamento entre domínios sem classificação) para definir os endereços IP que estão disponíveis para o cluster. Ele não deve se sobrepor a intervalos de IP de sub-rede (por exemplo, 10.0.0.0/16).

    - Na caixa __endereço IP do serviço DNS do kubernetes__ , insira o endereço IP do serviço DNS do kubernetes. Esse endereço IP é atribuído ao serviço DNS kubernetes. Ele deve estar dentro do intervalo de endereços do serviço kubernetes (por exemplo, 10.0.0.10).

    - Na caixa __endereço do Docker Bridge__ , insira o endereço da ponte do Docker. Esse endereço IP é atribuído à ponte do Docker. Ele não deve estar em nenhum intervalo de IP de sub-rede ou no kubernetes de endereço de serviço (por exemplo, 172.17.0.1/16).

   ![Azure Machine Learning: Computação do Machine Learning configurações de rede virtual](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Verifique se o grupo NSG que controla a rede virtual tem uma regra de segurança de entrada habilitada para o ponto de extremidade de Pontuação para que ela possa ser chamada de fora da rede virtual.
   > [!IMPORTANT]
   > Mantenha as regras de saída padrão para o NSG. Para obter mais informações, consulte as regras de segurança padrão em [grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

   [![Uma regra de segurança de entrada](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

Você também pode usar o SDK do Azure Machine Learning para adicionar o serviço kubernetes do Azure em uma rede virtual. Se você já tiver um cluster AKS em uma rede virtual, anexe-o ao espaço de trabalho conforme descrito em [como implantar no AKs](how-to-deploy-and-where.md). O código a seguir cria uma nova instância AKS na sub-rede `default` de uma rede virtual chamada `mynetwork`:

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

Quando o processo de criação for concluído, você poderá executar a inferência ou a pontuação de modelo em um cluster AKS por trás de uma rede virtual. Para obter mais informações, consulte [como implantar no AKs](how-to-deploy-and-where.md).

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Usar IPs privados com o serviço kubernetes do Azure

Por padrão, um endereço IP público é atribuído a implantações do AKS. Ao usar o AKS dentro de uma rede virtual, você pode usar um endereço IP privado em vez disso. Os endereços IP privados só podem ser acessados de dentro da rede virtual ou de redes Unidas.

Um endereço IP privado é habilitado Configurando AKS para usar um _balanceador de carga interno_. 

> [!IMPORTANT]
> Você não pode habilitar o IP privado ao criar o cluster do serviço kubernetes do Azure. Ele deve ser habilitado como uma atualização para um cluster existente.

O trecho de código a seguir demonstra como **criar um novo cluster AKs**e, em seguida, atualizá-lo para usar um balanceador de carga interno/IP privado:

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

    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = "default"
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = “myaks”, provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", "default")
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__CLI do Azure__

```azurecli-interactive
az rest --method put --uri https://management.azure.com"/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>?api-version=2018-11-19 --body @body.json
```

O conteúdo do arquivo de `body.json` referenciado pelo comando é semelhante ao seguinte documento JSON:

```json
{ 
    "location": “<region>”, 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>", 
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
> No momento, não é possível configurar o balanceador de carga ao executar uma operação de __anexação__ em um cluster existente. Primeiro, você deve anexar o cluster e, em seguida, executar uma operação de atualização para alterar o balanceador de carga.

Para obter mais informações sobre como usar o balanceador de carga interno com o AKS, consulte [usar o balanceador de carga interno com o serviço kubernetes do Azure](/azure/aks/internal-lb).

## <a name="use-azure-firewall"></a>Usar o Firewall do Azure

Ao usar o Firewall do Azure, você deve configurar uma regra de rede para permitir o tráfego de e para os seguintes endereços:

- `*.batchai.core.windows.net`
- `ml.azure.com`
- `*.azureml.ms`
- `*.experiments.azureml.net`
- `*.modelmanagement.azureml.net`
- `mlworkspace.azure.ai`
- `*.aether.ms`

Ao adicionar a regra, defina o __protocolo__ como Any e as portas para `*`.

Para obter mais informações sobre como configurar uma regra de rede, consulte [implantar e configurar o Firewall do Azure](/azure/firewall/tutorial-firewall-deploy-portal#configure-a-network-rule).

## <a name="next-steps"></a>Passos seguintes

* [Configurar ambientes de treinamento](how-to-set-up-training-targets.md)
* [Onde pretende implementar modelos](how-to-deploy-and-where.md)
* [Implantar modelos com SSL com segurança](how-to-secure-web-service.md)
