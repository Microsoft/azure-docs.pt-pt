---
title: Isolamento de rede & privacidade
titleSuffix: Azure Machine Learning
description: Utilize uma rede virtual Azure isolada com Azure Machine Learning para garantir a experimentação/formação, bem como trabalhos de inferência/pontuação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: 58f0a25ebeb7fb715cfba27d7482a031d1fe8c32
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121209"
---
# <a name="network-isolation-during-training-and-inference-with-private-endpoints-and-virtual-networks"></a>Isolamento da rede durante o treino e inferência com pontos finais privados e redes virtuais
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a proteger os seus ciclos de vida de aprendizagem automática isolando a Azure Machine Learning e inferências de trabalhos dentro de uma Rede Virtual Azure (vnet). Uma __rede virtual__ funciona como uma fronteira de segurança, isolando os seus recursos Azure da internet pública. Também pode aderir a uma rede virtual Azure à sua rede no local. Ao juntar redes, pode treinar os seus modelos de forma segura e aceder aos seus modelos implantados para inferência.

O espaço de trabalho Azure Machine Learning pode ser acedido a partir de uma rede virtual utilizando um __ponto final privado.__ O ponto final privado é um conjunto de endereços IP privados dentro da sua rede virtual, e o acesso ao seu espaço de trabalho está limitado à rede virtual. O ponto final privado ajuda a reduzir o risco de exfiltração de dados. Para saber mais sobre os pontos finais privados, consulte o artigo [Azure Private Link.](/azure/private-link/private-link-overview)

> [!NOTE]
> Pode encontrar problemas em aceder a um espaço de trabalho através de um ponto final privado ao utilizar o Mozilla Firefox. O problema pode estar relacionado com a definição de DNS sobre HTTPS no navegador. Recomendamos a utilização do Microsoft Edge ou do Google Chrome para contornar este problema.

O Azure Machine Learning conta com outros serviços Azure para armazenamento de dados e recursos computacionais (usados para treinar e implantar modelos). Estes recursos também podem ser criados dentro de uma rede virtual. Por exemplo, pode utilizar o computamento Azure Machine Learning para treinar um modelo e, em seguida, implementar o modelo para o Serviço Azure Kubernetes (AKS). 

## <a name="prerequisites"></a>Pré-requisitos

+ Um espaço de [trabalho](how-to-manage-workspace.md)de aprendizagem automática Azure.

+ Conhecimento geral de trabalho tanto do [serviço de Rede Virtual Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) como da rede [IP.](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)

+ Uma rede virtual pré-existente e uma sub-rede para utilizar com os seus recursos de computação.

+ Para implantar recursos numa rede virtual ou numa sub-rede, a sua conta de utilizador deve ter permissões para as seguintes ações nos controlos de acesso baseados em funções Azure (RBAC):

    - "Microsoft.Network/virtualNetworks/join/action" no recurso de rede virtual.
    - "Microsoft.Network/virtualNetworks/subnet/join/action" no recurso sub-rede.

    Para obter mais informações sobre o RBAC com as funções em rede, consulte as [funções embutimento em rede](/azure/role-based-access-control/built-in-roles#networking)

## <a name="secure-your-workspace"></a>Proteja o seu espaço de trabalho

O seu espaço de trabalho Azure Machine Learning pode ter um __ponto final público__ ou um ponto final __privado.__ Um ponto final público é um conjunto de endereços IP que são acessíveis na internet pública, enquanto um ponto final privado é um conjunto de endereços IP privados dentro de uma rede virtual. 

Para limitar o acesso ao seu espaço de trabalho para ocorrer apenas sobre os endereços IP privados, utilize um ponto final privado.

Uma vez que a comunicação ao espaço de trabalho só é permitida a partir da rede virtual, quaisquer ambientes de desenvolvimento que utilizem o espaço de trabalho devem ser membros da rede virtual. Por exemplo, uma máquina virtual na rede virtual.

> [!IMPORTANT]
> O ponto final privado não afeta o plano de controlo Azure (operações de gestão) como a eliminação do espaço de trabalho ou a gestão dos recursos computacional. Por exemplo, criar, atualizar ou eliminar um alvo de computação. Estas operações são realizadas através da Internet pública normalmente.
>
> O ponto final privado impede o acesso ao espaço de trabalho fora da rede virtual.

Algumas combinações de recursos com um ponto final privado requerem um espaço de trabalho de edição da Enterprise. Use a seguinte tabela para entender que cenários requerem edição enterprise:

| Cenário | Grandes Empresas</br>edição | Básico</br>edição |
| ----- |:-----:|:-----:| 
| Sem rede virtual ou ponto final privado | ✔ | ✔ |
| Espaço de trabalho sem ponto final privado. Outros recursos (exceto registo de contentores Azure) numa rede virtual | ✔ | ✔ |
| Espaço de trabalho sem ponto final privado. Outros recursos com ponto final privado | ✔ | |
| Espaço de trabalho com ponto final privado. Outros recursos (exceto registo de contentores Azure) numa rede virtual | ✔ | ✔ |
| Espaço de trabalho e qualquer outro recurso com ponto final privado | ✔ | |
| Espaço de trabalho com ponto final privado. Outros recursos sem ponto final privado ou rede virtual | ✔ | ✔ |
| Registo de contentores Azure em uma rede virtual | ✔ | |
| Chaves geridas pelo cliente para espaço de trabalho | ✔ | |

> [!WARNING]
> 
> As instâncias compute de aprendizagem automática Azure não são suportadas num espaço de trabalho onde o ponto final privado está ativado.
>
> O Azure Machine Learning não suporta a utilização de um Serviço Azure Kubernetes que tenha um ponto final privado ativado. Em vez disso, pode utilizar o Serviço Azure Kubernetes numa rede virtual. Para obter mais informações, consulte [secure Azure ML experimentação e inferências de trabalhos dentro de uma Rede Virtual Azure](how-to-enable-virtual-network.md).

Para saber mais sobre os pontos finais privados em Azure, consulte o artigo [Azure Private Link.](/azure/private-link/private-link-overview)

### <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Criar um espaço de trabalho que use um ponto final privado

Você pode criar um novo espaço de trabalho com um ponto final privado usando o Azure Machine Learning SDK, CLI, um modelo de Gestor de Recursos Azure ou o portal Azure.

__Requisitos__

* A rede virtual que utiliza com o ponto final privado deve ter políticas de rede desativadas. Para obter mais informações, consulte [as políticas de rede de desativação para um ponto final privado.](/azure/private-link/disable-private-endpoint-network-policy)

__Limitações__

* Não é possível ligar-se ao espaço de trabalho através da internet pública, apenas a partir de dentro da rede virtual.

* Se vários espaços de trabalho forem criados usando pontos finais privados, e usarem a mesma zona privada de DNS, apenas o primeiro espaço de trabalho é adicionado às __ligações de rede virtual__ da zona privada de DNS.

    Para contornar esta limitação, adicione manualmente a ligação de rede virtual para os espaços de trabalho adicionais. Para obter mais informações, consulte [o que é uma ligação de rede virtual.](/azure/dns/private-dns-virtual-network-links)

__Configuração__

Para obter informações sobre como criar um espaço de trabalho utilizando uma rede virtual e um ponto final privado, juntamente com outras opções de configuração, consulte os seguintes artigos:

* [Utilize um modelo de Gestor de Recursos Azure para criar um espaço de trabalho para a aprendizagem de máquinas Azure.](how-to-create-workspace-template.md)
* [Criar espaços de trabalho no portal.](how-to-manage-workspace.md)
* [Criar espaços de trabalho com Azure CLI](how-to-manage-workspace-cli.md).
* Para utilizar o Python SDK, consulte a documentação de referência [PrivateEndPointConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.private_endpoint.privateendpointconfig?view=azure-ml-py) e [Workspace.create().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-)

<a id="amlcompute"></a>

## <a name="machine-learning-studio"></a>Estúdio de Machine Learning

__Requisitos__

* Para aceder aos dados numa conta de armazenamento, a conta de armazenamento deve estar na mesma rede virtual que o espaço de trabalho.

* Se os seus dados forem armazenados numa rede virtual, deve utilizar uma [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) por um espaço de trabalho para permitir ao estúdio o acesso aos seus dados.

    > [!IMPORTANT]
    > Enquanto a maioria do estúdio trabalha com dados armazenados numa rede virtual, os cadernos integrados __não.__ Os cadernos integrados não suportam o armazenamento que se encontra numa rede virtual. Em vez disso, pode usar os Cadernos Jupyter de uma instância computacional. Para mais informações, consulte os [clusters compute &](#compute-instance) secções de instâncias.

    Se não conceder acesso ao estúdio, receberá este erro, `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` as seguintes operações não estarão disponíveis:

    * Pré-visualizar dados no estúdio.
    * Visualizar dados no designer.
    * Submeta uma experiência AutoML.
    * Inicie um projeto de rotulagem.

__Limitações__

* O estúdio Azure Machine Learning suporta dados de leitura dos seguintes tipos de datastore numa rede virtual:

    * Blob do Azure
    * Armazenamento do Azure Data Lake Ger1
    * Armazenamento do Azure Data Lake Ger2
    * Base de Dados SQL do Azure

__Configuração__

* __Configure as lojas de dados para utilizar uma identidade gerida__ para aceder aos seus dados. Estes passos adicionam a identidade gerida pelo espaço de trabalho como __Leitor__ ao serviço de armazenamento utilizando o controlo de acesso baseado em recursos Azure (RBAC). __O__ acesso ao leitor permite que o espaço de trabalho recupere as definições de firewall e certifique-se de que os dados não saem da rede virtual.

    1. No estúdio, selecione __Datastores.__

    1. Para criar uma nova loja de dados, selecione __+ Nova datastore__. Para atualizar uma existente, selecione a datastore e selecione __credenciais de Atualização__.

    1. Nas definições da datastore, selecione __Sim__ para __permitir o serviço de aprendizagem automática Azure para aceder ao armazenamento utilizando identidade gerida pelo espaço de trabalho__.

    > [!NOTE]
    > Estas alterações podem demorar até 10 minutos a produzir efeitos.

* Para __o armazenamento de Azure Blob,__ a identidade gerida pelo espaço de trabalho também deve ser adicionada como leitor de [dados blob](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) para que possa ler dados a partir do armazenamento de bolhas.

* O designer utiliza a conta de armazenamento anexada ao seu espaço de trabalho para armazenar a saída por padrão. No entanto, pode especi-lo para armazenar a saída em qualquer data-loja a que tenha acesso. Se o seu ambiente utilizar redes virtuais, pode utilizar estes controlos para garantir que os seus dados permanecem seguros e acessíveis. Para definir um novo armazenamento predefinido para um oleoduto:

    1. Num rascunho de pipeline, selecione o **ícone de engrenagem Definições** perto do título do seu oleoduto.
    1. Selecione a entrada **de loja de dados predefinitiva Select.**
    1. Especifique uma nova loja de dados.

    Também pode sobrepor a datastore predefinido numa base por módulo. Isto dá-lhe controlo sobre o local de armazenamento de cada módulo individual.

    1. Selecione o módulo cuja saída pretende especificar.
    1. Expandir a secção **de definições de saída.**
    1. Selecione **as definições de saída predefinido do Override**.
    1. Selecione **definições de saída de conjunto**.
    1. Especifique uma nova loja de dados.

* Se utilizar __a Azure Data Lake Storage Gen2,__ pode utilizar as listas de controlo de acesso ao estilo RBAC e POSIX (ACLs) para controlar o acesso de dados dentro de uma rede virtual.

    Para utilizar o RBAC, adicione a identidade gerida pelo espaço de trabalho à função [Blob Data Reader.](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) Para obter mais informações, consulte [o controlo de acesso baseado em funções.](../storage/blobs/data-lake-storage-access-control.md#role-based-access-control)

    Para utilizar ACLs, a identidade gerida pelo espaço de trabalho pode ser atribuída ao acesso como qualquer outro princípio de segurança. Para obter mais informações, consulte [as listas de controlo do Access em ficheiros e diretórios.](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

* __Azure Data Lake Storage Gen1__ suporta apenas listas de controlo de acesso ao estilo POSIX. Pode atribuir o espaço de trabalho gerido acesso de identidade a recursos como qualquer outro princípio de segurança. Para obter mais informações, consulte [o controlo de acesso em Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

* Para aceder aos dados armazenados numa __Base de Dados Azure SQL__ utilizando identidade gerida, tem de criar um utilizador de sql contido que mapeia para a identidade gerida. Para obter mais informações sobre a criação de um utilizador a partir de um fornecedor externo, consulte [criar utilizadores contidos mapeados para identidades AD Azure](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

    Depois de criar um utilizador de sql contido, conceda-lhe permissões utilizando o [comando GRANT T-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql).

* Se estiver __a aceder ao estúdio a partir de um recurso dentro de uma rede virtual__ (por exemplo, uma instância de computação ou uma máquina virtual), deve permitir o tráfego de saída da rede virtual para o estúdio. 

    Por exemplo, se estiver a utilizar grupos de segurança de rede (NSG) para restringir o tráfego de saída, adicione uma regra a um destino de __marcação__ de serviço de __AzureFrontDoor.Frontend__.

## <a name="use-datastores-and-datasets"></a>Utilizar datastores e conjuntos de dados

> [!NOTE]
> Esta secção abrange a datastore e a utilização do conjunto de dados para a experiência SDK. Para mais informações sobre a experiência do estúdio, consulte a secção [Machine Learning studio.](#machine-learning-studio)

__Limitações__

Por predefinição, a Azure Machine Learning realiza verificações de validade e credenciais de dados quando tenta aceder a dados utilizando o SDK. Se os seus dados estiverem por detrás de uma rede virtual, o Azure Machine Learning não pode aceder aos dados e falhar as suas verificações. Para contornar este problema, ignore a validação ao criar datastores e conjuntos de dados.

* Ao utilizar uma __loja de dados:__

    Azure Data Lake Store Gen1 e Azure Data Lake Store Gen2 saltam a validação por padrão, pelo que não é necessária mais nenhuma ação. No entanto, para os seguintes serviços pode utilizar sintaxe semelhante para ignorar a validação da datastore:

    - Armazenamento de Blobs do Azure
    - Azure fileshare
    - PostgreSQL
    - Base de Dados SQL do Azure

    A amostra de código que se segue cria uma nova loja de dados Azure Blob e define `skip_validation=True` .

    ```python
    blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  
                                                            datastore_name=blob_datastore_name,  
                                                            container_name=container_name,  
                                                            account_name=account_name, 
                                                            account_key=account_key, 
                                                            skip_validation=True ) // Set skip_validation to true
    ```

* Ao utilizar um __conjunto de dados:__

    A sintaxe para saltar a validação do conjunto de dados é semelhante para os seguintes tipos de conjunto de dados:
    - Arquivo delimitado
    - JSON 
    - Parquet
    - SQL
    - Ficheiro

    O código a seguir cria um novo conjunto de dados JSON e conjuntos `validate=False` .

    ```python
    json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 
                                                   validate=False) 
    ```

## <a name="azure-storage-account"></a>Conta de armazenamento do Azure

> [!IMPORTANT]
> Pode colocar a _conta de armazenamento predefinida_ para Azure Machine Learning e _contas de armazenamento não padrão_ numa rede virtual.

__Requisitos__

* A conta de armazenamento deve estar na mesma rede virtual e sub-rede que as instâncias de computação ou clusters utilizados para treino ou inferência.

__Configuração__

Para proteger a conta de Armazenamento Azure utilizada pelo seu espaço de trabalho, ou ative um __ponto final privado__ ou um ponto final de __serviço__ para a conta de armazenamento na sua rede virtual.

* Para configurar a conta de armazenamento para utilizar um __ponto final privado,__ consulte o artigo [Pontos finais privados Use.](/azure/storage/common/storage-private-endpoints.md)

* Para configurar a conta de armazenamento para utilizar um __ponto final de serviço,__ utilize os seguintes passos:

    1. Para adicionar a conta de armazenamento à rede virtual utilizada pelo seu espaço de trabalho, utilize as informações no acesso do __Grant a partir de uma__ secção de rede virtual das [firewalls de Armazenamento Configure Azure e](/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network) artigo de redes virtuais.
    1. Para permitir o acesso a partir de serviços da Microsoft na rede virtual (como Azure Machine Learning), utilize as informações na secção __Exceções__ das [firewalls de Armazenamento Configure Azure e](/azure/storage/common/storage-network-security#exceptions) artigo de redes virtuais.
    1. Ao trabalhar com o Azure Machine Learning SDK, o seu ambiente de desenvolvimento deve ser capaz de se conectar à Conta de Armazenamento Azure. Quando a conta de armazenamento está dentro de uma rede virtual, a firewall deve permitir o acesso a partir do endereço IP do ambiente de desenvolvimento. Para adicionar o endereço IP do ambiente de desenvolvimento, utilize as informações no acesso do __Grant a partir de uma__ secção de gama IP da Internet das [firewalls de Armazenamento Configure Azure e](/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range) artigo de redes virtuais.

## <a name="azure-container-registry"></a>Registo de Contentores do Azure

__Requisitos__

* O seu espaço de trabalho Azure Machine Learning deve ser a edição da Enterprise. Para obter informações sobre a atualização, consulte [a edição "Upgrade to Enterprise".](how-to-manage-workspace.md#upgrade)
* A sua região de espaço de trabalho Azure Machine Learning deve ser [uma região ativada por ligação privada.](https://docs.microsoft.com/azure/private-link/private-link-overview#availability) 
* O seu registo de contentores Azure deve ser versão Premium. Para obter mais informações sobre a atualização, consulte [a Alteração de SKUs](/azure/container-registry/container-registry-skus#changing-skus).
* O registo do seu contentor Azure deve estar na mesma rede virtual e sub-rede que a conta de armazenamento e os alvos de cálculo utilizados para treino ou inferência.
* O seu espaço de trabalho de aprendizagem automática Azure deve conter um [cluster de cálculo de aprendizagem automática Azure.](how-to-set-up-training-targets.md#amlcompute)

__Limitações__

* Quando o ACR está por detrás de uma rede virtual, o Azure Machine Learning não pode usá-lo para construir diretamente imagens do Docker. Em vez disso, o cluster computacional é usado para construir as imagens.

__Configuração__

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

1. Para permitir que o seu espaço de trabalho comunique com a instância ACR, aplique o seguinte modelo de Gestor de Recursos Azure:

    > [!WARNING]
    > Este modelo permite um ponto final privado para o seu espaço de trabalho e altera-o para um espaço de trabalho empresarial. Não pode desfazer estas mudanças.

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

## <a name="key-vault-instance"></a>Caso do cofre chave 

__Requisitos__

__Limitações__

__Configuração__ 

Para utilizar as capacidades de experimentação de aprendizagem automática Azure com cofre de chaves Azure por trás de uma rede virtual, utilize as [firewalls Configure Key Vault e](/azure/key-vault/general/network-security) o artigo de redes virtuais.

> [!IMPORTANT]
> Ao seguir os passos do artigo, utilize a mesma rede virtual utilizada pelos seus recursos de cálculo de experimentação. Também deve __permitir que serviços de confiança da Microsoft contornem esta firewall.__

## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Clusters de computação & instâncias 

__Requisitos__

* A rede virtual deve estar na mesma subscrição e região que o espaço de trabalho Azure Machine Learning.
* A sub-rede especificada para a instância de computação ou cluster deve ter endereços IP não atribuídos suficientes para acomodar o número de VMs que são alvo. Se a sub-rede não tiver endereços IP não atribuídos suficientes, um cluster de cálculo será parcialmente atribuído.
* Se planeia proteger a rede virtual restringindo o tráfego, algumas portas devem ser deixadas abertas para o serviço de computação.
* Se vai colocar várias instâncias de computação ou clusters numa rede virtual, poderá ter de solicitar um aumento de quota para um ou mais dos seus recursos.
* Se a conta de armazenamento Azure para o espaço de trabalho também estiver protegida numa rede virtual, devem estar na mesma rede virtual que o azure machine learning ou cluster. 
* Para que a funcionalidade do Jupyter funcione, certifique-se de que a comunicação da tomada web não está desativada.

__Limitações__

* A instância ou cluster do cálculo machine learning aloca automaticamente recursos adicionais de rede __no grupo de recursos que contém a rede virtual.__ Para cada instância de cálculo ou cluster, o serviço aloca os seguintes recursos:

    * Um grupo de segurança de rede
    * Um endereço IP público
    * Um equilibrador de carga
    
    Para __os clusters de cálculo,__ estes recursos são eliminados (e recriados) sempre que o cluster escala para 0 nós.
    
    Para um __caso de cálculo,__ os recursos são mantidos até que a instância seja eliminada (parar não remove os recursos).

    Estes recursos estão limitados pelas [quotas de recursos](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) da subscrição.

__Configuração__

* Para criar um cluster machine learning compute, utilize os seguintes passos:

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

* Se estiver a utilizar cadernos numa instância do Azure Compute, tem de se certificar de que o seu caderno está a funcionar num recurso computacional por trás da mesma rede virtual e sub-redes que os seus dados. 

    Configure a sua Instância de Computação para estar na mesma rede virtual durante a criação em **Configurações Avançadas**  >  **Configurar rede virtual**. Não é possível adicionar uma Instância de Computação existente a uma rede virtual.

* Se planeia proteger a rede virtual restringindo o tráfego de rede de/para a internet pública, deve permitir comunicações de entrada a partir do serviço Azure Batch.

    O serviço Batch adiciona grupos de segurança de rede (NSGs) ao nível das interfaces de rede (NICs) que estão ligadas aos VMs. Estes NSGs configuram automaticamente regras de entrada e saída para permitir o tráfego seguinte:

    - Tráfego TCP de entrada nos portos 29876 e 29877 a partir de uma etiqueta de __serviço__ de __BatchNodeManagement__.

    - (Opcional) Tráfego TCP de entrada na porta 22 para permitir o acesso remoto. Utilize esta porta apenas se pretender ligar utilizando sSH no IP público.

    - Tráfego de saída em qualquer porta para a rede virtual.

    - Tráfego de saída em qualquer porta para a Internet.

    - Para o porputação, o tráfego TCP no porto 44224 a partir de uma etiqueta de __serviço__ da __AzureMachineLearning__.

    > [!IMPORTANT]
    > Tenha cuidado se modificar ou adicionar regras de entrada ou saída em NSGs configurados pelo Batch. Se um NSG bloqueia a comunicação aos nós de computação, o serviço de computação define o estado dos nós de computação para inutilizável.
    >
    > Não precisa de especificar NSGs ao nível da sub-rede, porque o serviço Azure Batch configura os seus próprios NSGs. No entanto, se a sub-rede que contém o cálculo Azure Machine Learning tiver associado NSGs ou uma firewall, também deve permitir o tráfego listado anteriormente.

* Se não quiser utilizar as regras de saída predefinidos e pretender limitar o acesso de saída da sua rede virtual, utilize os seguintes passos:

    1. Negar a ligação à Internet de saída utilizando as regras NSG.
    1. Para uma __instância de computação__ ou um __cluster de cálculo,__ limite o tráfego de saída para os seguintes itens:
        - Armazenamento Azure, utilizando __a Etiqueta__ de Serviço de __Armazenamento.RegionName__. Onde `{RegionName}` está o nome de uma região de Azure.
        - Registo do Contentor Azure, utilizando __a etiqueta__ de serviço da __AzureContainerRegistry.RegionName__. Onde `{RegionName}` está o nome de uma região de Azure.
        - Azure Machine Learning, utilizando __a etiqueta__ de serviço da __AzureMachineLearning__
        - Gestor de Recursos Azure, utilizando __a Tag__ de Serviço da __AzureResourceManager__
        - Diretório Ativo Azure, utilizando __a Tag__ de Serviço da __AzureActiveDirectory__

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

* Se estiver a utilizar [um túnel forçado](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm) com o cálculo Azure Machine Learning, deve permitir a comunicação com a internet pública a partir da sub-rede que contém o recurso computacional. Esta comunicação é utilizada para agendamento de tarefas e acesso ao Armazenamento Azure.

    Há duas maneiras de conseguir isto:

    * Utilize um [NAT de rede virtual.](../virtual-network/nat-overview.md) Um gateway NAT fornece conectividade de saída da Internet para uma ou mais sub-redes na sua rede virtual. Para obter informações, consulte [conceber redes virtuais com recursos de gateway NAT.](../virtual-network/nat-gateway-resource.md)

    * Adicione [as rotas definidas pelo utilizador (UDRs)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) à sub-rede que contém o recurso compute. Estabeleça uma UDR para cada endereço IP que seja utilizado pelo serviço Azure Batch na região onde os seus recursos existem. Estes UDRs permitem ao serviço Batch comunicar com nós computacional para agendamento de tarefas. Adicione também o endereço IP para o serviço de Aprendizagem automática Azure onde existem os recursos, uma vez que este é necessário para o acesso a Instâncias computacional. Para obter uma lista de endereços IP do serviço Batch e do serviço Azure Machine Learning, utilize um dos seguintes métodos:

        * Faça o download das [Gamas IP E Tags de Serviço Azure](https://www.microsoft.com/download/details.aspx?id=56519) e procure no ficheiro `BatchNodeManagement.<region>` `AzureMachineLearning.<region>` e, onde `<region>` fica a sua região Azure.

        * Utilize o [CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para descarregar as informações. O exemplo a seguir descarrega as informações do endereço IP e filtra as informações para a região leste dos EUA 2:

            ```azurecli-interactive
            az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
            az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
            ```
        
        Quando adicionar os UDRs, defina a rota para cada prefixo de endereço IP do lote relacionado e defina __o tipo de próximo lúpulo__ para a __Internet__. 

        Além de quaisquer UDRs que defina, o tráfego de saída para o Azure Storage deve ser permitido através do seu aparelho de rede no local. Especificamente, os URLs para este tráfego estão nas seguintes formas: `<account>.table.core.windows.net` `<account>.queue.core.windows.net` , e `<account>.blob.core.windows.net` . 

        Para obter mais informações, consulte [criar uma piscina Azure Batch numa rede virtual.](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

__Requisitos__

* A instância do Serviço Azure Kubernetes (AKS) e a rede virtual Azure devem estar na mesma região. Se proteger a(s) conta de Armazenamento Azure utilizada pelo espaço de trabalho numa rede virtual, devem estar na mesma rede virtual que a aks.

* Para __planear a endereçamento ip__ para o seu cluster, siga os pré-requisitos no artigo de rede avançada [Configure em Azure Kubernetes Service (AKS).](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites)

* Para restringir a comunicação de entrada e saída ao cluster AKS, siga as orientações no tráfego de saída de controlo para os nós de cluster no artigo [de serviço Azure Kubernetes](/azure/aks/limit-egress-traffic) para garantir que a comunicação de _saída_ da AKS está configurada corretamente. Os requisitos de comunicação _de entrada,_ se houver, são chamados na secção de configuração abaixo.

__Limitações__

* Se pretender utilizar um Serviço Azure Kubernetes que tenha ligação privada ativada, deve anexá-lo ao seu espaço de trabalho. Não é possível criar um cluster de serviço Azure Kubernetes com Ligação Privada a partir de Azure Machine Learning (SDK, portal, CLI, etc.).

__Configuração__

> [!IMPORTANT]
> Esta secção contém múltiplas configurações. Selecione o que melhor se adequa às suas necessidades.

* Para __utilizar a AKS por trás da rede virtual, com um equilibrador de carga pública:__

    1. Criar ou fixar o cluster AKS. Se estiver __a criar__ um novo cluster, tem de especificar a rede virtual a utilizar para o cluster.
    
        O exemplo a seguir demonstra como criar um novo cluster AKS utilizando o Python SDK:

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

        Se tiver um cluster AKS existente que já esteja por trás da rede virtual, utilize as informações no artigo [implementar para Azure Kubernetes.](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster)

    1. Certifique-se de que o grupo de segurança da rede que controla a rede virtual tem uma regra de segurança __de entrada__ ativada para o ponto final de pontuação para que possa ser chamada de fora da rede virtual.

* Para __utilizar a AKS por trás da rede virtual, com um equilibrador de carga privado:__

    1. Criar ou fixar o cluster AKS. Se estiver __a criar__ um novo cluster, tem de especificar a rede virtual a utilizar para o cluster.
    
        O exemplo a seguir demonstra como criar um novo cluster AKS utilizando o Python SDK:

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

        Se tiver um cluster AKS existente que já esteja por trás da rede virtual, utilize as informações no artigo [implementar para Azure Kubernetes.](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster)

    1. Para encontrar o principal de serviço ou iD de identidade gerido para AKS, utilize os seguintes comandos Azure CLI. `<aks-cluster-name>`Substitua-o pelo nome do cluster. `<resource-group-name>`Substitua-o pelo nome do grupo de recursos que _contém o cluster AKS:_

        ```azurecli-interactive
        az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
        ``` 

        Se este comando devolver um valor `msi` de, use o seguinte comando para identificar o ID principal para a identidade gerida:

        ```azurecli-interactive
        az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
        ```
    1. Para encontrar o ID do grupo de recursos que contém a sua rede virtual, utilize o seguinte comando. `<resource-group-name>`Substitua-o pelo nome do grupo de recursos que _contém a rede virtual:_

        ```azurecli-interactive
        az group show -n <resource-group-name> --query id
        ```

    1. Para adicionar o principal do serviço ou identidade gerida como contribuinte de rede, utilize o seguinte comando. `<SP-or-managed-identity>`Substitua-o pelo ID devolvido pelo principal de serviço ou identidade gerida. Substitua-o pelo `<resource-group-id>` ID devolvido pelo grupo de recursos que contém a rede virtual:

        ```azurecli-interactive
        az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
        ```

    1. Para atualizar o cluster AKS para utilizar um __balançador de carga privado,__ utilize o Python SDK. O seguinte corte de código demonstra como atualizar um cluster AKS existente que foi adicionado ou ligado ao espaço de trabalho:
    
        ```python
        import azureml.core
        from azureml.core.compute.aks import AksUpdateConfiguration
        from azureml.core.compute import AksCompute

        # ws = workspace object. Creation not shown in this snippet
        aks_target = AksCompute(ws,"myaks")

        # Change to the name of the subnet that contains AKS
        subnet_name = "default"
        # Update AKS configuration to use an internal load balancer
        update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
        aks_target.update(update_config)
        # Wait for the operation to complete
        aks_target.wait_for_completion(show_output = True)
        ```

* Para __anexar a AKS utilizando um ponto final privado:__

    1. Utilize o seguinte comando Azure CLI para obter o ID da __sub-rede__ que o cluster AKS utilizará. Por exemplo, a sub-rede predefinida para a sua rede virtual:
    
        ```azurecli
        az network vnet show -g myresourcegroup -n myvnet --query subnets[].id
        ```
        
        Este comando devolve uma série de IDs para as sub-redes da rede virtual. O seguinte JSON é um exemplo de uma rede virtual que tem apenas uma sub-rede:

        ```json
        [
            "/subscriptions/GUID/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/default"
        ]
        ```

        Se forem devolvidos vários IDs, selecione o que pretende utilizar.

    1. Para criar um cluster AKS um ponto final privado, utilize as informações na secção avançada de __networking__ do Criar um artigo [de cluster privado do Serviço Azure Kubernetes.](/azure/aks/private-clusters#advanced-networking) Ao criar o cluster, utilize o ID da sub-rede do comando anterior com o `--vnet-subnet-id` parâmetro.

    1. Para anexar o cluster, utilize as informações no artigo [implementar para Azure Kubernetes.](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster)

    > [!TIP]
    > Ao utilizar esta configuração e restringir a comunicação de saída do cluster AKS, não é necessário permitir a comunicação nas portas __1194__ ou __9000__. Para outras portas que devem ser permitidas, consulte as orientações no tráfego de saída de controlo para nós de cluster no artigo [de Serviço Azure Kubernetes.](/azure/aks/limit-egress-traffic)

## <a name="use-azure-container-instances-aci"></a>Utilizar instâncias de contentores Azure (ACI)

__Requisitos__

* As instâncias do recipiente Azure são criadas dinamicamente ao implementar um modelo. Para permitir que o Azure Machine Learning crie ACI dentro da rede virtual, deve ativar a delegação de __sub-redes__ para a sub-rede utilizada pela implantação.

__Limitações__

* A rede virtual deve estar no mesmo grupo de recursos que o seu espaço de trabalho Azure Machine Learning.

* O Registo de Contentores Azure (ACR) para o seu espaço de trabalho também não pode estar na rede virtual.

__Configuração__

Para utilizar o ACI numa rede virtual para o seu espaço de trabalho, utilize os seguintes passos:

1. Para ativar a delegação de sub-redes na sua rede virtual, utilize as informações no Add ou remova um artigo [de delegação de sub-rede.](../virtual-network/manage-subnet-delegation.md) Pode ativar a delegação ao criar uma rede virtual ou adicioná-la a uma rede existente.

    > [!IMPORTANT]
    > Ao ativar a delegação, utilize `Microsoft.ContainerInstance/containerGroups` como __sub-rede delegado o__ valor de serviço.

2. Implementar o modelo utilizando [a AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-), utilize os `vnet_name` parâmetros e `subnet_name` parâmetros. Deslo sente estes parâmetros no nome e na sub-rede de rede virtuais onde permitiu a delegação.

## <a name="azure-databricks"></a>Azure Databricks

__Requisitos__

* A rede virtual deve estar na mesma subscrição e região que o espaço de trabalho Azure Machine Learning.
* Se a conta de armazenamento Azure para o espaço de trabalho também estiver protegida numa rede virtual, devem estar na mesma rede virtual que o cluster Azure Databricks.
* Além das __sub-redes databricks-private__ e __databricks-public__ utilizadas pela Azure Databricks, a sub-rede __padrão__ criada para a rede virtual também é necessária.

__Limitações__

__Configuração__

Para obter informações específicas sobre a utilização de databricks Azure com uma rede virtual, consulte [Implementar databricks no seu Azure Virtual Network](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Máquina virtual ou cluster HDInsight

__Requisitos__

* A Azure Machine Learning suporta apenas máquinas virtuais que estão a executar Ubuntu.
* A porta SSH deve ser ativada na máquina virtual ou no cluster HDInsight.

__Limitações__

__Configuração__

1. Crie um cluster VM ou HDInsight utilizando o portal Azure ou o CLI Azure, e coloque o cluster numa rede virtual Azure. Para obter mais informações, veja os seguintes artigos:

    * [Criar e gerir redes virtuais Azure para Os VMs Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Estender o HDInsight usando uma rede virtual Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Um NSG é criado automaticamente para máquinas virtuais Azure baseadas em Linux. Este NSG permite o acesso à porta 22 a partir de qualquer fonte. Se quiser limitar o acesso à porta SSH, tem de permitir o acesso a partir do Azure Machine Learning. Para preservar o acesso ao Azure ML, deve permitir o acesso a partir de um serviço de __origem__ com uma etiqueta de serviço de __origem__ da __AzureMachineLearning__. Por exemplo, os seguintes comandos Azure CLI modificam a regra SSH para permitir apenas o acesso a partir de Azure Machine Learning.

    ```azurecli
    # Get default SSH rule
    nsgrule=$(az network nsg rule list --resource-group myResourceGroup --nsg-name myNetworkSecurityGroup --query [0].name -o tsv)
    # Update network security group rule to limit SSH to source service.
    az network nsg rule update --resource-group myResourceGroup --nsg-name myNetworkSecurityGroupBackEnd \
    --name $nsgrule --protocol tcp --direction inbound --priority 100 \
    --source-address-prefix AzureMachineLearning --source-port-range '*' --destination-address-prefix '*' \
    --destination-port-range 22 --access allow
    ```

    Para obter mais informações, consulte a secção [Criar grupos](/azure/virtual-machines/linux/tutorial-virtual-network#create-network-security-groups) de segurança de rede do artigo redes virtuais Azure para máquinas virtuais Linux.
    
    Mantenha as regras de saída padrão para o grupo de segurança da rede. Para obter mais informações, consulte as regras de segurança em [grupos de segurança.](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)

1. Fixe o cluster VM ou HDInsight ao seu espaço de trabalho de aprendizagem de máquinas Azure. Para obter mais informações, consulte [Configurar metas de computação para a formação de modelos.](how-to-set-up-training-targets.md)

## <a name="next-steps"></a>Passos seguintes

* [Utilize o espaço de trabalho Azure Machine Learning atrás do Azure Firewall](how-to-access-azureml-behind-firewall.md).
* [Configurar ambientes de preparação](how-to-set-up-training-targets.md)
* [Configurar pontos finais privados](how-to-configure-private-link.md)
* [Onde implementar os modelos](how-to-deploy-and-where.md)
* [Utilize o TLS para garantir um serviço web através do Azure Machine Learning](how-to-secure-web-service.md)
