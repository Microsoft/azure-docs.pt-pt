---
title: Garantir um espaço de trabalho de aprendizagem automática Azure com redes virtuais
titleSuffix: Azure Machine Learning
description: Utilize uma Rede Virtual Azure isolada para garantir o seu espaço de trabalho de aprendizagem de máquinas Azure e recursos associados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 03/17/2021
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: a923f65e5c6183d045f4b7455e0a01edda75d499
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584353"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>Garantir um espaço de trabalho de aprendizagem automática Azure com redes virtuais

Neste artigo, aprende-se a garantir um espaço de trabalho Azure Machine Learning e os seus recursos associados numa rede virtual.

Este artigo é parte dois de uma série de cinco partes que o acompanha através da garantia de um fluxo de trabalho de Aprendizagem automática Azure. Recomendamos vivamente que leia a [primeira parte: visão geral do VNet](how-to-network-security-overview.md) para entender primeiro a arquitetura geral. 

Veja os outros artigos desta série:

[1. Visão geral da VNet](how-to-network-security-overview.md)  >  **2. Proteja o espaço de trabalho**  >  [3. Proteja o ambiente de treino](how-to-secure-training-vnet.md)  >  [4. Fixe o ambiente de inferencção](how-to-secure-inferencing-vnet.md)  >  [5. Ativar a funcionalidade do estúdio](how-to-enable-studio-virtual-network.md)

Neste artigo aprende-se a permitir os seguintes recursos de espaços de trabalho numa rede virtual:
> [!div class="checklist"]
> - Área de trabalho do Azure Machine Learning
> - Contas de Armazenamento do Azure
> - Lojas de dados e conjuntos de dados de aprendizagem automática Azure
> - Azure Key Vault
> - Registo de Contentores do Azure

## <a name="prerequisites"></a>Pré-requisitos

+ Leia o artigo [de visão geral de segurança da Rede](how-to-network-security-overview.md) para entender cenários de rede virtual comuns e arquitetura de rede virtual geral.

+ Uma rede virtual existente e uma sub-rede para usar com os seus recursos de computação.

+ Para implantar recursos numa rede virtual ou numa sub-rede, a sua conta de utilizador deve ter permissões para as seguintes ações no controlo de acesso baseado em funções Azure (Azure RBAC):

    - "Microsoft.Network/virtualNetworks/join/action" no recurso de rede virtual.
    - "Microsoft.Network/virtualNetworks/subnet/join/action" no recurso sub-rede.

    Para obter mais informações sobre o Azure RBAC com networking, consulte as [funções embutidas em rede](../role-based-access-control/built-in-roles.md#networking)


## <a name="secure-the-workspace-with-private-endpoint"></a>Proteja o espaço de trabalho com ponto final privado

O Azure Private Link permite-lhe ligar-se ao seu espaço de trabalho utilizando um ponto final privado. O ponto final privado é um conjunto de endereços IP privados dentro da sua rede virtual. Em seguida, pode limitar o acesso ao seu espaço de trabalho para ocorrer apenas sobre os endereços IP privados. O Private Link ajuda a reduzir o risco de exfiltração de dados.

Para obter mais informações sobre a criação de um espaço de trabalho private link, consulte [Como configurar o Private Link](how-to-configure-private-link.md).

> [!Warning]
> Garantir um espaço de trabalho com pontos finais privados não garante a segurança de ponta a ponta por si só. Deve seguir os passos do resto deste artigo, e da série VNet, para garantir componentes individuais da sua solução.

## <a name="secure-azure-storage-accounts-with-service-endpoints"></a>Contas de armazenamento Secure Azure com pontos finais de serviço

A Azure Machine Learning suporta contas de armazenamento configuradas para utilizar pontos finais de serviço ou pontos finais privados. Nesta secção, você aprende a proteger uma conta de armazenamento Azure usando pontos finais de serviço. Para pontos finais privados, consulte a secção seguinte.

Para utilizar uma conta de armazenamento Azure para o espaço de trabalho numa rede virtual, utilize os seguintes passos:

1. No portal Azure, vá ao serviço de armazenamento que pretende utilizar no seu espaço de trabalho.

   [![O armazenamento que está ligado ao espaço de trabalho Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Na página da conta de serviço de armazenamento, selecione __Networking__.

   ![A área de networking na página de Armazenamento Azure no portal Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. No separador __Firewalls e redes virtuais,__ faça as seguintes ações:
    1. Selecione __Redes selecionadas__.
    1. Nas __redes Virtuais,__ selecione a ligação __de rede virtual Add existente.__ Esta ação adiciona a rede virtual onde reside o seu cálculo (ver passo 1).

        > [!IMPORTANT]
        > A conta de armazenamento deve estar na mesma rede virtual e sub-rede que as instâncias de computação ou clusters utilizados para treino ou inferência.

    1. Selecione os __serviços da Microsoft fidedignos para aceder a esta__ caixa de verificação de conta de armazenamento. Esta alteração não dá a todos os serviços Azure acesso à sua conta de armazenamento.
    
        * Os recursos de alguns serviços, **registados na sua subscrição,** podem aceder à conta de armazenamento **na mesma subscrição** para operações selecionadas. Por exemplo, escrever registos ou criar cópias de segurança.
        * Os recursos de alguns serviços podem ser concedidos acesso explícito à sua conta de __armazenamento, atribuindo uma função Azure__ à sua identidade gerida atribuída pelo sistema.

        Para obter mais informações, veja [Configurar firewalls e redes virtuais do Armazenamento do Microsoft Azure](../storage/common/storage-network-security.md#trusted-microsoft-services).

    > [!IMPORTANT]
    > Ao trabalhar com o Azure Machine Learning SDK, o seu ambiente de desenvolvimento deve ser capaz de se conectar à Conta de Armazenamento Azure. Quando a conta de armazenamento está dentro de uma rede virtual, a firewall deve permitir o acesso a partir do endereço IP do ambiente de desenvolvimento.
    >
    > Para permitir o acesso à conta de armazenamento, visite as __Firewalls e redes virtuais__ para a conta de armazenamento *a partir de um navegador web no cliente de desenvolvimento.* Em seguida, utilize a caixa de verificação __ip do seu cliente__ para adicionar o endereço IP do cliente à GAMA DE __ENDEREÇOS__. Também pode utilizar o campo __ADDRESS RANGE__ para introduzir manualmente o endereço IP do ambiente de desenvolvimento. Uma vez adicionado o endereço IP do cliente, pode aceder à conta de armazenamento utilizando o SDK.

   [![O painel "Firewalls e redes virtuais" no portal Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="secure-azure-storage-accounts-with-private-endpoints"></a>Contas de armazenamento Secure Azure com pontos finais privados

A Azure Machine Learning suporta contas de armazenamento configuradas para utilizar pontos finais de serviço ou pontos finais privados. Se a conta de armazenamento utilizar pontos finais privados, deve configurar dois pontos finais privados para a sua conta de armazenamento predefinido:
1. Um ponto final privado com um subresource de alvo de **bolha.**
1. Um ponto final privado com subresource de alvo **de ficheiro** (fileshare).

![Screenshot mostrando página de configuração de ponto final privado com opções de blob e arquivo](./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png)

Para configurar um ponto final privado para uma conta de armazenamento que **não** seja o armazenamento predefinido, selecione o tipo **de sub-fonte Target** que corresponde à conta de armazenamento que pretende adicionar.

Para obter mais informações, consulte [utilizar pontos finais privados para armazenamento Azure](../storage/common/storage-private-endpoints.md)

## <a name="secure-datastores-and-datasets"></a>Lojas de dados e conjuntos de dados seguros

Nesta secção, aprende-se a utilizar datastore e conjuntos de dados na experiência SDK com uma rede virtual. Para obter mais informações sobre a experiência do estúdio, consulte o [estúdio Use Azure Machine Learning numa rede virtual.](how-to-enable-studio-virtual-network.md)

Para aceder aos dados utilizando o SDK, deve utilizar o método de autenticação exigido pelo serviço individual em que os dados são armazenados. Por exemplo, se registar uma loja de dados para aceder à Azure Data Lake Store Gen2, ainda deve utilizar um principal de serviço como documentado nos [serviços de armazenamento Connect to Azure](how-to-access-data.md#azure-data-lake-storage-generation-2).

### <a name="disable-data-validation"></a>Desativar validação de dados

Por predefinição, a Azure Machine Learning realiza verificações de validade e credenciais de dados quando tenta aceder a dados utilizando o SDK. Se os dados estiverem por detrás de uma rede virtual, o Azure Machine Learning não pode completar estes controlos. Para contornar esta verificação, tem de criar datas-lojas e conjuntos de dados que saltem a validação.

### <a name="use-datastores"></a>Utilizar datastores

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

### <a name="use-datasets"></a>Utilizar conjuntos de dados

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

## <a name="secure-azure-key-vault"></a>Cofre de chave Azure seguro

A Azure Machine Learning utiliza uma instância associada do Cofre de Chaves para armazenar as seguintes credenciais:
* A cadeia de ligação de conta de armazenamento associada
* Palavras-passe para instâncias de repositório de contentores Azure
* Cadeias de ligação a lojas de dados

Para utilizar as capacidades de experimentação de aprendizagem automática Azure com cofre de chaves Azure por trás de uma rede virtual, utilize os seguintes passos:

1. Vá ao Cofre de Chaves que está associado ao espaço de trabalho.

1. Na página __Key Vault,__ no painel esquerdo, selecione __Networking__.

1. No separador __Firewalls e redes virtuais,__ faça as seguintes ações:
    1. Em __Permitir o acesso a partir de,__ selecione Private __endpoint e redes selecionadas__.
    1. Nas __redes Virtuais,__ selecione __Adicionar as redes virtuais existentes__ para adicionar a rede virtual onde reside o seu compute de experimentação.
    1. Em __'Permitir que os serviços fidedignos da Microsoft' contornem esta firewall__, selecione __Sim__.

   [![A secção "Firewalls e redes virtuais" no painel do Cofre de Chaves](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>Ativar o Registo do Contentor de Azure (ACR)

Para utilizar o Registo de Contentores Azure dentro de uma rede virtual, deve cumprir os seguintes requisitos:

* O seu registo de contentores Azure deve ser versão Premium. Para obter mais informações sobre a atualização, consulte [a Alteração de SKUs](../container-registry/container-registry-skus.md#changing-tiers).

* O registo do seu contentor Azure deve estar na mesma rede virtual e sub-rede que a conta de armazenamento e os alvos de cálculo utilizados para treino ou inferência.

* O seu espaço de trabalho de aprendizagem automática Azure deve conter um [cluster de cálculo de aprendizagem automática Azure.](how-to-create-attach-compute-cluster.md)

    Quando o ACR está por detrás de uma rede virtual, o Azure Machine Learning não pode usá-lo para construir diretamente imagens do Docker. Em vez disso, o cluster computacional é usado para construir as imagens.

    > [!IMPORTANT]
    > O cluster de cálculo usado para construir imagens Docker precisa ser capaz de aceder aos repositórios de pacotes que são usados para treinar e implantar os seus modelos. Poderá ter de adicionar regras de segurança de rede que permitam o acesso a repos públicos, [utilize pacotes Python privados,](how-to-use-private-python-packages.md)ou utilize [imagens personalizadas](how-to-train-with-custom-image.md) do Docker que já incluam os pacotes.

Uma vez cumpridos os requisitos, utilize os seguintes passos para permitir o registo do contentor Azure.

> [!TIP]
> Se não utilizar um registo de contentores Azure existente ao criar o espaço de trabalho, pode não existir. Por predefinição, o espaço de trabalho não criará uma instância ACR até que precise de uma. Para forçar a criação de um, treine ou implemente um modelo utilizando o seu espaço de trabalho antes de utilizar os passos nesta secção.

1. Encontre o nome do Registo do Contentor Azure para o seu espaço de trabalho, utilizando um dos seguintes métodos:

    __Portal do Azure__

    A partir da secção de visão geral do seu espaço de trabalho, o valor do __registo__ liga-se ao Registo do Contentor de Azure.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Registo do contentor de Azure para o espaço de trabalho" border="true":::

    __CLI do Azure__

    Se [tiver instalado a extensão machine learning para Azure CLI,](reference-azure-machine-learning-cli.md)pode utilizar o comando para mostrar as `az ml workspace show` informações do espaço de trabalho.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Este comando devolve um valor semelhante a `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` . A última parte da cadeia é o nome do Registo do Contentor Azure para o espaço de trabalho.

1. Limite o acesso à sua rede virtual utilizando os passos no acesso à [rede Configure para registo.](../container-registry/container-registry-vnet.md#configure-network-access-for-registry) Ao adicionar a rede virtual, selecione a rede virtual e a sub-rede para os seus recursos de Aprendizagem de Máquinas Azure.

1. Configure o ACR para o espaço de trabalho para permitir o [acesso por serviços fidedignos.](../container-registry/allow-access-trusted-services.md)

1. Use o Azure Machine Learning Python SDK para configurar um cluster de computação para construir imagens de estivadores. O seguinte código snippet demonstra como fazê-lo:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    # To switch back to using ACR to build (if ACR is not in the VNet):
    # ws.update(image_build_compute = None)
    ```

    > [!IMPORTANT]
    > A sua conta de armazenamento, cluster compute e registo de contentores Azure devem estar todos na mesma sub-rede da rede virtual.
    
    Para obter mais informações, consulte a referência do método [de atualização.).](/python/api/azureml-core/azureml.core.workspace.workspace#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-)

## <a name="next-steps"></a>Passos seguintes

Este artigo é parte dois de uma série de rede virtual de cinco partes. Veja o resto dos artigos para aprender a proteger uma rede virtual:

* [Parte 1: Visão geral da rede virtual](how-to-network-security-overview.md)
* [Parte 3: Assegurar o ambiente de treino](how-to-secure-training-vnet.md)
* [Parte 4: Assegurar o ambiente de inferencção](how-to-secure-inferencing-vnet.md)
* [Parte 5: Ativar a funcionalidade do estúdio](how-to-enable-studio-virtual-network.md)

Consulte também o artigo sobre a utilização [de DNS personalizados](how-to-custom-dns.md) para resolução de nomes.