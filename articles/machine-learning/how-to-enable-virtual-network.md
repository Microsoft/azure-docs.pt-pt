---
title: Experiências seguras e inferência na rede virtual
titleSuffix: Azure Machine Learning
description: aprender a garantir empregos de experimentação/formação e empregos de inferência/pontuação em Azure Machine Learning dentro de uma Rede Virtual Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/13/2020
ms.openlocfilehash: f70c24c91e048270696b244bb9775cb24f0ef30d
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383481"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>Empregos de experimentação e inferência secure Azure ML dentro de uma Rede Virtual Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a garantir trabalhos de experimentação/formação e empregos de inferência/pontuação em Azure Machine Learning dentro de uma Rede Virtual Azure (vnet).

Uma **rede virtual** funciona como um limite de segurança, isolando os seus recursos Azure da internet pública. Também pode aderir a uma rede virtual Azure à sua rede no local. Ao juntar redes, pode treinar de forma segura os seus modelos e aceder aos seus modelos implantados para inferência.

O Azure Machine Learning conta com outros serviços Azure para recursos de computação. Os recursos computacionais, ou metas de [computação,](concept-compute-target.md)são usados para treinar e implementar modelos. Os alvos podem ser criados dentro de uma rede virtual. Por exemplo, pode utilizar a Microsoft Data Science Virtual Machine para treinar um modelo e depois implementar o modelo para o Serviço Azure Kubernetes (AKS). Para obter mais informações sobre redes virtuais, consulte a [visão geral da Rede Virtual Azure.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)

Este artigo também fornece informações detalhadas sobre *configurações avançadas*de segurança, informações que não são necessárias para casos básicos ou experimentais de uso. Algumas secções deste artigo fornecem informações de configuração para uma variedade de cenários. Não precisa completar as instruções em ordem ou na totalidade.

> [!TIP]
> A menos que seja especificamente convocado, a utilização de recursos como contas de armazenamento ou alvos de cálculo dentro de uma rede virtual funcionará tanto com os gasodutos de aprendizagem automática, como com fluxos de trabalho não oleodutos, como as execuções de scripts.

> [!WARNING]
> A Microsoft não suporta utilizar as funcionalidades do Estúdio de Aprendizagem automática do Azure Machine Learning, tais como ML automatizado, Datasets, Datalabeling, Designer e Notebooks se o armazenamento subjacente tiver uma rede virtual ativada.

## <a name="prerequisites"></a>Pré-requisitos

+ Um [espaço de trabalho](how-to-manage-workspace.md)azure machine learning.

+ Conhecimento geral de trabalho tanto do serviço de [Rede Virtual Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) como da [rede IP.](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)

+ Uma rede virtual pré-existente e uma sub-rede para usar com os seus recursos computacionais.

## <a name="use-a-storage-account-for-your-workspace"></a>Use uma conta de armazenamento para o seu espaço de trabalho

> [!WARNING]
> Se tiver cientistas de dados que usam o designer de Machine Learning Azure, receberão um erro ao visualizar dados de uma conta de armazenamento dentro de uma rede virtual. O seguinte texto é o erro que recebem:
>
> __Erro: Incapaz de perfilar este conjunto de dados. Isto pode acontecer porque os seus dados estão armazenados atrás de uma rede virtual ou os seus dados não suportam o perfil.__

Para utilizar uma conta de armazenamento Azure para o espaço de trabalho numa rede virtual, utilize os seguintes passos:

1. Criar um recurso computacional (por exemplo, uma instância ou cluster de machine learning) por trás de uma rede virtual, ou anexar um recurso computacional ao espaço de trabalho (por exemplo, um cluster HDInsight, máquina virtual ou cluster de serviço Azure Kubernetes). O recurso computacional pode ser para experimentação ou implementação de modelos.

   Para mais informações, consulte a [computação Use a Machine Learning](#amlcompute), utilize uma máquina virtual ou um cluster [HDInsight](#vmorhdi)e utilize as secções de [serviço Azure Kubernetes](#aksvnet) neste artigo.

1. No portal Azure, vá ao armazenamento que está ligado ao seu espaço de trabalho.

   [![O armazenamento que está ligado ao espaço de trabalho azure machine learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Na página **de Armazenamento Azure,** selecione __Firewalls e redes virtuais__.

   ![A área "Firewalls e redes virtuais" na página de Armazenamento Azure no portal Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Na página __Firewalls e redes virtuais,__ faça as seguintes ações:
    - Selecione __Redes selecionadas__.
    - Em __redes Virtuais,__ selecione a ligação __de rede virtual Existente.__ Esta ação adiciona a rede virtual onde reside o seu cálculo (ver passo 1).

        > [!IMPORTANT]
        > A conta de armazenamento deve estar na mesma rede virtual e sub-rede que os casos de cálculo ou clusters utilizados para a formação ou inferência.

    - Selecione os __serviços de confiança da Microsoft para aceder a esta__ caixa de verificação de conta de armazenamento.

    > [!IMPORTANT]
    > Ao trabalhar com o Azure Machine Learning SDK, o seu ambiente de desenvolvimento deve ser capaz de se ligar à Conta de Armazenamento Azure. Quando a conta de armazenamento estiver dentro de uma rede virtual, a firewall deve permitir o acesso a partir do endereço IP do ambiente de desenvolvimento.
    >
    > Para permitir o acesso à conta de armazenamento, visite as __Firewalls e redes virtuais__ para a conta de armazenamento *a partir de um navegador web no cliente de desenvolvimento.* Em seguida, utilize a caixa de verificação de __endereços IP do seu cliente__ para adicionar o endereço IP do cliente ao INTERVALO DE __ENDEREÇOs__. Também pode utilizar o campo __ADDRESS RANGE__ para introduzir manualmente o endereço IP do ambiente de desenvolvimento. Uma vez adicionado o endereço IP do cliente, pode aceder à conta de armazenamento utilizando o SDK.

   [![O painel "Firewalls and virtual networks" no portal Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> Pode colocar a _conta_ de armazenamento padrão para o Azure Machine Learning ou _contas de armazenamento não predefinidas_ numa rede virtual.
>
> A conta de armazenamento por defeito é automaticamente disponibilizada quando cria um espaço de trabalho.
>
> Para contas de armazenamento não `storage_account` predefinidas, o parâmetro da [ `Workspace.create()` função](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) permite especificar uma conta de armazenamento personalizada pelo ID de recurso Azure.

## <a name="use-azure-data-lake-storage-gen-2"></a>Use azure Data Lake Storage Gen 2

Azure Data Lake Storage Gen 2 é um conjunto de capacidades para a análise de big data, construída sumponsuticos. Pode ser usado para armazenar dados usados para treinar modelos com Azure Machine Learning. 

Para utilizar data Lake Storage Gen 2 dentro da rede virtual do seu espaço de trabalho Azure Machine Learning, use os seguintes passos:

1. Crie uma conta azure Data Lake Storage gen 2. Para mais informações, consulte Criar uma conta de armazenamento de 12 milhões de dados [azure.](../storage/blobs/data-lake-storage-quickstart-create-account.md)

1. Utilize os passos 2-4 na secção anterior, utilize uma conta de armazenamento para o seu espaço de [trabalho,](#use-a-storage-account-for-your-workspace)para colocar a conta na rede virtual.

Ao utilizar o Azure Machine Learning com data Lake Storage Gen 2 dentro de uma rede virtual, utilize as seguintes orientações:

* Se utilizar o __SDK para criar um conjunto__de dados , e o sistema que executa o código não estiver na rede __virtual,__ utilize o `validate=False` parâmetro. Este parâmetro ignora a validação, o que falha se o sistema não estiver na mesma rede virtual que a conta de armazenamento. Para mais informações, consulte o método [from_files().](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-)

* Ao utilizar o Azure Machine Learning Compute Instance ou o cluster compute para treinar um modelo utilizando o conjunto de dados, deve estar na mesma rede virtual que a conta de armazenamento.

## <a name="use-a-key-vault-instance-with-your-workspace"></a>Use uma instância chave do cofre com o seu espaço de trabalho

A principal instância do cofre que está associada ao espaço de trabalho é usada pela Azure Machine Learning para armazenar as seguintes credenciais:
* A cadeia de ligação à conta de armazenamento associada
* Palavras-passe para casos de repositório de contentores azure
* Cordas de ligação às lojas de dados

Para utilizar as capacidades de experimentação de machine learning azure com o Azure Key Vault por trás de uma rede virtual, utilize os seguintes passos:

1. Vá ao cofre que está associado ao espaço de trabalho.

   [![O cofre chave que está associado ao espaço de trabalho azure machine learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. Na página **Key Vault,** no painel esquerdo, selecione __Firewalls e redes virtuais__.

   ![A secção "Firewalls e redes virtuais" no painel Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. Na página __Firewalls e redes virtuais,__ faça as seguintes ações:
    - Em __permitir o acesso a partir de__, selecione redes __selecionadas__.
    - Em __redes Virtuais,__ selecione __Adicionar redes virtuais existentes__ para adicionar a rede virtual onde reside a sua computação de experimentação.
    - Sob __os serviços fidedignos__da Microsoft para contornar esta firewall, selecione __Sim__.

   [![A secção "Firewalls e redes virtuais" no painel Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

<a id="amlcompute"></a>

## <a name="use-a-machine-learning-compute"></a><a name="compute-instance"></a>Utilize uma computação de aprendizagem automática

Para utilizar um conjunto de cálculos de machine learning Azure ou um cluster de cálculo numa rede virtual, devem ser cumpridos os seguintes requisitos de rede:

> [!div class="checklist"]
> * A rede virtual deve estar na mesma subscrição e região que o espaço de trabalho Azure Machine Learning.
> * A sub-rede especificada para a instância de cálculo ou cluster deve ter endereços IP não atribuídos suficientes para acomodar o número de VMs que são direcionados. Se a sub-rede não tiver endereços IP suficientes não atribuídos, um cluster de cálculo será parcialmente atribuído.
> * Verifique se as suas políticas de segurança ou bloqueios na subscrição ou grupo de recursos da rede virtual restringem as permissões para gerir a rede virtual. Se planeia proteger a rede virtual restringindo o tráfego, deixe algumas portas abertas para o serviço de computação. Para mais informações, consulte a secção de [portas requerida.](#mlcports)
> * Se vai colocar vários casos de computação ou clusters numa rede virtual, poderá precisar de solicitar um aumento de quota para um ou mais dos seus recursos.
> * Se as Contas de Armazenamento Azure para o espaço de trabalho também estiverem protegidas numa rede virtual, devem estar na mesma rede virtual que a instância ou cluster de computação de Machine Learning Azure. 

> [!TIP]
> A instância ou cluster de computação machine learning atribui automaticamente recursos adicionais de rede no grupo de recursos que contém a rede virtual. Para cada instância ou cluster de cálculo, o serviço atribui os seguintes recursos:
> 
> * Um grupo de segurança de rede
> * Um endereço IP público
> * Um equilibrador de carga
> 
> Estes recursos estão limitados pelas [quotas de recursos](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) da subscrição.


### <a name="required-ports"></a><a id="mlcports"></a>Portas necessárias

A Machine Learning Compute utiliza atualmente o serviço Azure Batch para fornecer VMs na rede virtual especificada. A sub-rede deve permitir a comunicação de entrada do serviço Batch. Você usa esta comunicação para agendar corridas nos nós da Computação de Aprendizagem automática e para comunicar com o Armazenamento Azure e outros recursos. O serviço Batch adiciona grupos de segurança de rede (NSGs) ao nível das interfaces de rede (NICs) que estão ligados aos VMs. Estes NSGs configuram automaticamente regras de entrada e saída para permitir o tráfego seguinte:

- Tráfego de TCP de entrada nos portos 29876 e 29877 a partir de uma etiqueta de __serviço__ de __BatchNodeManagement__.

    ![Uma regra de entrada que usa a etiqueta de serviço BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (Opcional) Tráfego de TCP de entrada na porta 22 para permitir acesso remoto. Utilize esta porta apenas se pretender ligar utilizando o SSH no IP público.

- Tráfego de saída em qualquer porta para a rede virtual.

- Tráfego de saída em qualquer porta para a Internet.

- Para calcular, por exemplo, tráfego de TCP de entrada na porta 44224 a partir de uma etiqueta de __serviço__ de __AzureMachineLearning__.

Tenha cuidado se modificar ou adicionar regras de entrada ou saída em NSGs configurados pelo Batch. Se um NSG bloqueia a comunicação aos nós de computação, o serviço de computação define o estado dos nós computacionais como inutilizáveis.

Não precisa especificar NSGs ao nível da subnet, porque o serviço Azure Batch configura os seus próprios NSGs. No entanto, se a sub-rede especificada tiver nsgs ou uma firewall, configure as regras de segurança de entrada e saída, como mencionado anteriormente.

A configuração da regra NSG no portal Azure é mostrada nas seguintes imagens:

[![As regras de nSG de entrada para machine learning compute](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png#lightbox)

![As regras de nsg de saída para machine learning compute](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a>Limite a conectividade de saída da rede virtual

Se não quiser utilizar as regras de saída por defeito e pretender limitar o acesso de saída da sua rede virtual, utilize os seguintes passos:

- Negue a ligação à internet de saída utilizando as regras da NSG.

- Para uma __instância de cálculo__ ou um cluster de __cálculo,__ limite o tráfego de saída aos seguintes itens:
   - Armazenamento Azure, utilizando etiqueta de __serviço__ de __armazenamento.RegionName__. Onde `{RegionName}` está o nome de uma região de Azure.
   - Registo de contentores Azure, utilizando etiqueta de __serviço__ de __AzureContainerRegistry.RegionName__. Onde `{RegionName}` está o nome de uma região de Azure.
   - Azure Machine Learning, utilizando __a etiqueta__ de serviço do __AzureMachineLearning__
   - Gestor de Recursos Azure, utilizando etiqueta de __serviço__ do __AzureResourceManager__
   - Diretório Azure Ative, utilizando etiqueta de __serviço__ do __AzureActiveDirectory__

A configuração da regra NSG no portal Azure é mostrada na seguinte imagem:

[![As regras de nsg de saída para machine learning compute](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Se planeia utilizar imagens predefinidas do Docker fornecidas pela Microsoft e permitir dependências geridas pelo utilizador, também deve utilizar uma etiqueta de __serviço__ do __MicrosoftContainerRegistry.Region_Name__ (por exemplo, MicrosoftContainerRegistry.EastUS).
>
> Esta configuração é necessária quando tem um código semelhante aos seguintes snippets como parte dos seus scripts de treino:
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
> __Formação de estimador__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="user-defined-routes-for-forced-tunneling"></a>Rotas definidas pelo utilizador para túneis forçados

Se estiver a utilizar túneis forçados com a Computação de Aprendizagem automática, adicione [as rotas definidas pelo utilizador (UDRs)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) à sub-rede que contém o recurso computacional.

* Estabeleça um UDR para cada endereço IP que seja utilizado pelo serviço Azure Batch na região onde os seus recursos existem. Estes UDRs permitem que o serviço Batch comunique com os nossos nódeos computacionais para agendamento de tarefas. Para obter uma lista de endereços IP do serviço Batch, utilize um dos seguintes métodos:

    * Descarregue as [gamas e etiquetas](https://www.microsoft.com/download/details.aspx?id=56519) de `BatchNodeManagement.<region>`serviço `<region>` Azure IP e procure no ficheiro, onde fica a sua região Azure.

    * Utilize o [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para descarregar as informações. O exemplo seguinte descarrega as informações de endereço IP e filtra as informações para a região leste dos EUA 2:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        ```

* O tráfego de saída para o Armazenamento Azure não deve ser bloqueado pelo seu aparelho de rede no local. Especificamente, os URLs `<account>.table.core.windows.net`estão na forma, `<account>.queue.core.windows.net`e `<account>.blob.core.windows.net`.

Quando adicionar os UDRs, defina a rota para cada prefixo de endereço IP do Lote relacionado e coloque o __tipo de lúpulo seguinte__ na __Internet__. A imagem que se segue mostra um exemplo deste UDR no portal Azure:

![Exemplo de um UDR para um prefixo de endereço](./media/how-to-enable-virtual-network/user-defined-route.png)

Para mais informações, consulte [Criar um conjunto de Lotes Azure numa rede virtual.](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Criar um cluster de computação numa rede virtual

Para criar um cluster de Computação de Aprendizagem automática, utilize os seguintes passos:

1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com/)e, em seguida, selecione a sua subscrição e espaço de trabalho.

1. Selecione __Compute__ à esquerda.

1. Selecione __clusters__ de treino __+__ a partir do centro e, em seguida, selecione .

1. No diálogo new __Training Cluster,__ expanda a secção de __definições Avançadas.__

1. Para configurar este recurso computacional para utilizar uma rede virtual, execute as seguintes ações na secção de __rede virtual Configure:__

    1. Na lista de drop-down do __grupo Resource,__ selecione o grupo de recursos que contém a rede virtual.
    1. Na lista de drop-down da __rede Virtual,__ selecione a rede virtual que contém a sub-rede.
    1. Na lista de abandono da __Subnet,__ selecione a sub-rede para utilizar.

   ![As definições de rede virtual para Machine Learning Compute](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Também pode criar um cluster de Machine Learning Compute utilizando o Azure Machine Learning SDK. O seguinte código cria um novo cluster `default` machine learning compute `mynetwork`na subnet de uma rede virtual chamada:

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

Quando o processo de criação terminar, treina o seu modelo utilizando o cluster numa experiência. Para mais informações, consulte [Select e utilize um alvo de cálculo para treino](how-to-set-up-training-targets.md).

## <a name="use-azure-databricks"></a>Utilizar tijolos de dados Azure

Para utilizar os Azure Databricks numa rede virtual com o seu espaço de trabalho, devem ser cumpridos os seguintes requisitos:

> [!div class="checklist"]
> * A rede virtual deve estar na mesma subscrição e região que o espaço de trabalho Azure Machine Learning.
> * Se as Contas de Armazenamento Azure para o espaço de trabalho também estiverem protegidas numa rede virtual, devem estar na mesma rede virtual que o cluster Azure Databricks.
> * Para além das subredes __públicas de databricks e__ __databricks utilizadas__ pela Azure Databricks, é também necessária a subnet __predefinida__ criada para a rede virtual.

Para obter informações específicas sobre a utilização de Tijolos de Dados Azure com uma rede virtual, consulte a implantação de tijolos de [dados Azure na sua Rede Virtual Azure](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Utilize uma máquina virtual ou um cluster HDInsight

> [!IMPORTANT]
> O Azure Machine Learning suporta apenas máquinas virtuais que estão a executar ubuntu.

Para utilizar uma máquina virtual ou um cluster Azure HDInsight numa rede virtual com o seu espaço de trabalho, utilize os seguintes passos:

1. Crie um cluster VM ou HDInsight utilizando o portal Azure ou o Azure CLI, e coloque o cluster numa rede virtual Azure. Para obter mais informações, veja os artigos seguintes:
    * [Criar e gerir redes virtuais Azure para VMs Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Alargar o HDInsight utilizando uma rede virtual Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Para permitir que o Azure Machine Learning comunique com a porta SSH no VM ou cluster, configure uma entrada de origem para o grupo de segurança da rede. A porta SSH é normalmente a porta 22. Para permitir o tráfego a partir desta fonte, faça as seguintes ações:

    * Na lista de drop-down __Source,__ selecione __Etiqueta de Serviço__.

    * Na lista de drop-down da etiqueta de __serviço Source,__ selecione __AzureMachineLearning__.

    * Na lista de intervalos de drop-down da __porta Source,__ selecione __*__.

    * Na lista de desistência seletiva do __Destino,__ selecione __Qualquer__.

    * Na lista de intervalos de drop-down da __porta Destination,__ selecione __22__.

    * Em __Protocolo,__ selecione __Qualquer__.

    * Em __ação,__ __selecione Permitir__.

   ![Regras de entrada para fazer experimentação num cluster VM ou HDInsight dentro de uma rede virtual](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Mantenha as regras de saída padrão para o grupo de segurança da rede. Para mais informações, consulte as regras de segurança padrão nos [grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Se não quiser utilizar as regras de saída por defeito e pretender limitar o acesso de saída da sua rede virtual, consulte a conectividade limite da secção [de rede virtual.](#limiting-outbound-from-vnet)

1. Fixe o cluster VM ou HDInsight ao seu espaço de trabalho de Aprendizagem automática Azure. Para obter mais informações, consulte [Configurar os alvos do cálculo para a formação](how-to-set-up-training-targets.md)de modelos .

<a id="aksvnet"></a>

## <a name="use-azure-kubernetes-service-aks"></a>Utilizar o Serviço Azure Kubernetes (AKS)

Para adicionar AKS numa rede virtual ao seu espaço de trabalho, use os seguintes passos:

> [!IMPORTANT]
> Antes de iniciar o seguinte procedimento, siga os pré-requisitos no [sistema de rede avançado Configure no Serviço Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/configure-advanced-networking#prerequisites) como e planeie o endereço IP para o seu cluster.
>
> A instância AKS e a rede virtual Azure devem estar na mesma região. Se proteger a conta de armazenamento Azure(s) utilizada pelo espaço de trabalho numa rede virtual, devem estar na mesma rede virtual que a instância AKS.

> [!WARNING]
> O Azure Machine Learning não suporta a utilização de um Serviço Azure Kubernetes que tenha ligação privada ativada.

1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com/)e, em seguida, selecione a sua subscrição e espaço de trabalho.

1. Selecione __Compute__ à esquerda.

1. Selecione clusters de __inferência__ a __+__ partir do centro e, em seguida, selecione .

1. No diálogo __New Inference Cluster,__ selecione __Advanced__ sob __a configuração da Rede__.

1. Para configurar este recurso computacional para utilizar uma rede virtual, execute as seguintes ações:

    1. Na lista de drop-down do __grupo Resource,__ selecione o grupo de recursos que contém a rede virtual.
    1. Na lista de drop-down da __rede Virtual,__ selecione a rede virtual que contém a sub-rede.
    1. Na lista de entrega da __Subnet,__ selecione a sub-rede.
    1. Na caixa de __endereços do Serviço Kubernetes,__ introduza a gama de endereços de serviço Kubernetes. Esta gama de endereços utiliza uma gama IP de notação inter-domínio sem classe (CIDR) para definir os endereços IP disponíveis para o cluster. Não deve sobrepor-se a quaisquer gamas IP da sub-rede (por exemplo, 10.0.0.0/16).
    1. Na caixa de endereços IP do __serviço DNS kubernetes,__ introduza o endereço IP do serviço DNS da Kubernetes. Este endereço IP é atribuído ao serviço DNS kubernetes. Deve estar dentro da gama de endereços de serviço Kubernetes (por exemplo, 10.0.0.10).
    1. Na caixa de endereços da __ponte Docker,__ entre na ponte docker. Este endereço IP é atribuído à Ponte Docker. Não deve estar em nenhuma gama IP sub-rede, ou na gama de endereços de serviço Kubernetes (por exemplo, 172.17.0.1/16).

   ![Azure Machine Learning: Machine Learning Compute configurações de rede virtual](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Certifique-se de que o grupo NSG que controla a rede virtual tem uma regra de segurança de entrada ativada para o ponto final de pontuação para que possa ser chamada de fora da rede virtual.
   > [!IMPORTANT]
   > Mantenha as regras de saída padrão para o NSG. Para mais informações, consulte as regras de segurança padrão nos [grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

   [![Uma regra de segurança de entrada](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

Também pode utilizar o Azure Machine Learning SDK para adicionar o Serviço Azure Kubernetes numa rede virtual. Se já tem um cluster AKS numa rede virtual, fixe-o ao espaço de trabalho descrito em [Como implantar no AKS](how-to-deploy-and-where.md). O seguinte código cria uma nova `default` instância AKS na `mynetwork`subnet de uma rede virtual chamada:

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

Quando o processo de criação estiver concluído, pode executar inferência, ou pontuação de modelo, num cluster AKS atrás de uma rede virtual. Para mais informações, consulte [Como se deslocar para AKS](how-to-deploy-and-where.md).

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Use IPs privados com serviço Azure Kubernetes

Por predefinição, um endereço IP público é atribuído às implementações aks. Ao utilizar AKS dentro de uma rede virtual, pode utilizar um endereço IP privado. Os endereços IP privados só são acessíveis a partir de dentro da rede virtual ou redes adesão.

Um endereço IP privado é ativado configurando AKS para utilizar um _equilíbrio interno_de carga . 

> [!IMPORTANT]
> Não é possível ativar ip privado na criação do cluster de serviço Azure Kubernetes. Deve ser ativado como uma atualização de um cluster existente.

O seguinte código de snippet demonstra como **criar um novo cluster AKS,** e depois atualizá-lo para usar um equilíbrio de carga IP/interno privado:

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
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
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

O conteúdo `body.json` do ficheiro referenciado pelo comando é semelhante ao seguinte documento JSON:

```json
{ 
    "location": "<region>", 
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
> Atualmente, não é possível configurar o equilibrista de carga ao executar uma operação __de fixação__ num cluster existente. Primeiro, tem de fixar o cluster e, em seguida, executar uma operação de atualização para alterar o equilíbrio de carga.

Para obter mais informações sobre a utilização do equilibrador de carga interna com AKS, consulte [Utilize o equilibrador de carga interno com o Serviço Azure Kubernetes](/azure/aks/internal-lb).

## <a name="use-azure-container-instances-aci"></a>Utilizar instâncias de contentores Azure (ACI)

As instâncias de contentores Azure são criadas dinamicamente ao implementar um modelo. Para permitir que o Azure Machine Learning crie ACI dentro da rede virtual, deve permitir a delegação de __sub-rede__ para a sub-rede utilizada pela implantação.

Para utilizar o ACI numa rede virtual para o seu espaço de trabalho, utilize os seguintes passos:

1. Para permitir a delegação de sub-redes na sua rede virtual, utilize as informações no Add ou remova um artigo de delegação de [sub-rede.](../virtual-network/manage-subnet-delegation.md) Pode permitir a delegação na criação de uma rede virtual ou adicioná-la a uma rede existente.

    > [!IMPORTANT]
    > Ao permitir a `Microsoft.ContainerInstance/containerGroups` delegação, utilize como __sub-rede delegado o__ valor do serviço.

2. Implementar o modelo utilizando [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-)() use os `vnet_name` e `subnet_name` os parâmetros. Defina estes parâmetros para o nome e subrede de rede virtual onde permitiu a delegação.



## <a name="use-azure-firewall"></a>Utilizar firewall Azure

Ao utilizar o Firewall Azure, deve configurar uma regra de rede para permitir o tráfego de e para os seguintes endereços:

- `*.batchai.core.windows.net`
- `ml.azure.com`
- `*.azureml.ms`
- `*.experiments.azureml.net`
- `*.modelmanagement.azureml.net`
- `mlworkspace.azure.ai`
- `*.aether.ms`

Ao adicionar a regra, dereo o `*` __Protocolo__ a qualquer, e as portas a .

Para obter mais informações sobre a configuração de uma regra de rede, consulte [Implementar e configurar firewall Azure](/azure/firewall/tutorial-firewall-deploy-portal#configure-a-network-rule).

## <a name="use-azure-container-registry"></a>Utilizar o Azure Container Registry

> [!IMPORTANT]
> O Registo de Contentores Azure (ACR) pode ser colocado dentro de uma rede virtual, no entanto deve cumprir os seguintes pré-requisitos:
>
> * O seu espaço de trabalho azure machine learning deve ser a edição da Enterprise. Para obter informações sobre upgrade, consulte [Upgrade para a Edição Enterprise](how-to-manage-workspace.md#upgrade).
> * O registo do seu contentor Azure deve ser versão Premium. Para obter mais informações sobre a atualização, consulte [Changing SKUs](/azure/container-registry/container-registry-skus#changing-skus).
> * O registo do seu contentor Azure deve estar na mesma rede virtual e sub-rede que a conta de armazenamento e os alvos de cálculo utilizados para treino ou inferência.
> * O seu espaço de trabalho azure machine learning deve conter um aglomerado de [computação azure machine learning](how-to-set-up-training-targets.md#amlcompute).
>
>     Quando o ACR está por detrás de uma rede virtual, o Azure Machine Learning não pode usá-lo para construir diretamente imagens do Docker. Em vez disso, o cluster de cálculo é usado para construir as imagens.

1. Para encontrar o nome do Registo de Contentores Azure para o seu espaço de trabalho, utilize um dos seguintes métodos:

    __Portal do Azure__

    A partir da secção geral do seu espaço de trabalho, o valor __do registo__ liga-se ao Registo de Contentores Azure.

    ![Registo de contentores azure para o espaço de trabalho](./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png)

    __CLI do Azure__

    Se [tiver instalado a extensão de Machine Learning para o Azure CLI,](reference-azure-machine-learning-cli.md)pode utilizar o `az ml workspace show` comando para mostrar as informações do espaço de trabalho.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Este comando devolve um `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`valor semelhante a . A última parte da corda é o nome do Registo de Contentores Azure para o espaço de trabalho.

1. Para limitar o acesso à sua rede virtual, utilize os passos no acesso à [rede Configure para registo](../container-registry/container-registry-vnet.md#configure-network-access-for-registry). Ao adicionar a rede virtual, selecione a rede virtual e a subnet para os seus recursos de Aprendizagem automática Azure.

1. Utilize o Azure Machine Learning Python SDK para configurar um cluster de cálculo para construir imagens de estivadores. O seguinte código de corte demonstra como fazê-lo:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > A sua conta de armazenamento, o cluster de cálculo e o Registo de Contentores Azure devem estar todos na mesma sub-rede da rede virtual.
    
    Para mais informações, consulte a referência do método [de atualização()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-)

1. Se estiver a utilizar o Private Link para o seu espaço de trabalho de Aprendizagem automática Azure e colocar o Registo de Contentores Azure para o seu espaço de trabalho numa rede virtual, também deve aplicar o seguinte modelo de Gestor de Recursos Azure. Este modelo permite que o seu espaço de trabalho se comunique com a ACR através do Link Privado.

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

## <a name="next-steps"></a>Passos seguintes

* [Configurar ambientes de preparação](how-to-set-up-training-targets.md)
* [Onde implementar os modelos](how-to-deploy-and-where.md)
* [Use tLS para garantir um serviço web através de Azure Machine Learning](how-to-secure-web-service.md)
