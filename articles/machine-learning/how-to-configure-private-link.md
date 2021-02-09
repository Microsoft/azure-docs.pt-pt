---
title: Configurar um ponto final privado
titleSuffix: Azure Machine Learning
description: Utilize o Azure Private Link para aceder de forma segura ao seu espaço de trabalho Azure Machine Learning a partir de uma rede virtual.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.openlocfilehash: 5ba1b9d53255406a73b1b74dbc59fe39e3f9a0d7
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979186"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Configure Azure Private Link para um espaço de trabalho de aprendizagem de máquinas Azure

Neste documento, aprende a utilizar o Azure Private Link com o seu espaço de trabalho Azure Machine Learning. Para obter informações sobre a criação de uma rede virtual para Azure Machine Learning, consulte [o isolamento da rede virtual e a visão geral da privacidade](how-to-network-security-overview.md)

O Azure Private Link permite-lhe ligar-se ao seu espaço de trabalho utilizando um ponto final privado. O ponto final privado é um conjunto de endereços IP privados dentro da sua rede virtual. Em seguida, pode limitar o acesso ao seu espaço de trabalho para ocorrer apenas sobre os endereços IP privados. O Private Link ajuda a reduzir o risco de exfiltração de dados. Para saber mais sobre os pontos finais privados, consulte o artigo [Azure Private Link.](../private-link/private-link-overview.md)

> [!IMPORTANT]
> O Azure Private Link não afeta o plano de controlo Azure (operações de gestão) como a eliminação do espaço de trabalho ou a gestão de recursos computacional. Por exemplo, criar, atualizar ou eliminar um alvo de computação. Estas operações são realizadas através da Internet pública normalmente. As operações de plano de dados, tais como a utilização do estúdio Azure Machine Learning, APIs (incluindo oleodutos publicados), ou o SDK usam o ponto final privado.
>
> Poderá encontrar problemas ao tentar aceder ao ponto final privado para o seu espaço de trabalho se estiver a utilizar o Mozilla Firefox. Este problema pode estar relacionado com DNS sobre HTTPS na Mozilla. Recomendamos a utilização do Microsoft Edge do Google Chrome como uma solução alternativa.

## <a name="prerequisites"></a>Pré-requisitos

Se planeia utilizar um link privado habilitado espaço de trabalho com uma chave gerida pelo cliente, deve solicitar esta funcionalidade usando um bilhete de apoio. Para obter mais informações, consulte [Gerir e aumentar as quotas.](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)

## <a name="limitations"></a>Limitações

* A utilização de um espaço de trabalho de aprendizagem automática Azure com ligação privada não está disponível nas regiões do Governo Azure ou nas regiões Azure China 21Vianet.
* Se permitir o acesso público a um espaço de trabalho protegido com link privado e utilizar o estúdio Azure Machine Learning através da internet pública, algumas funcionalidades, como o designer, podem não conseguir aceder aos seus dados. Este problema acontece quando os dados são armazenados num serviço que é protegido por trás do VNet. Por exemplo, uma Conta de Armazenamento Azure.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Criar um espaço de trabalho que use um ponto final privado

Utilize um dos seguintes métodos para criar um espaço de trabalho com um ponto final privado. Cada um __destes métodos requer uma rede virtual existente:__

> [!TIP]
> Se quiser criar um espaço de trabalho, um ponto final privado e uma rede virtual ao mesmo tempo, consulte [use um modelo de Gestor de Recursos Azure para criar um espaço de trabalho para a Azure Machine Learning](how-to-create-workspace-template.md).

# <a name="python"></a>[Python](#tab/python)

A Azure Machine Learning Python SDK fornece a classe [PrivateEndpointConfig,](/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) que pode ser usada com [Workspace.create()](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) para criar um espaço de trabalho com um ponto final privado. Esta classe requer uma rede virtual existente.

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.create(name='myworkspace',
    subscription_id='<my-subscription-id>',
    resource_group='myresourcegroup',
    location='eastus2',
    private_endpoint_config=pe,
    private_endpoint_auto_approval=True,
    show_output=True)
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

A [extensão Azure CLI para machine learning](reference-azure-machine-learning-cli.md) fornece o espaço de trabalho [az ml criar](/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_create) comando. Os seguintes parâmetros para este comando podem ser usados para criar um espaço de trabalho com uma rede privada, mas requer uma rede virtual existente:

* `--pe-name`: O nome do ponto final privado que é criado.
* `--pe-auto-approval`: Se as ligações privadas ao espaço de trabalho devem ser automaticamente aprovadas.
* `--pe-resource-group`: O grupo de recursos para criar o ponto final privado em. Deve ser o mesmo grupo que contém a rede virtual.
* `--pe-vnet-name`: A rede virtual existente para criar o ponto final privado em.
* `--pe-subnet-name`: O nome da sub-rede para criar o ponto final privado em. O valor predefinido é `default`.

# <a name="portal"></a>[Portal](#tab/azure-portal)

O __separador Networking__ no estúdio Azure Machine Learning permite-lhe configurar um ponto final privado. No entanto, requer uma rede virtual existente. Para obter mais informações, consulte [Criar espaços de trabalho no portal.](how-to-manage-workspace.md)

---

## <a name="add-a-private-endpoint-to-a-workspace"></a>Adicione um ponto final privado a um espaço de trabalho

Utilize um dos seguintes métodos para adicionar um ponto final privado a um espaço de trabalho existente:

> [!IMPORTANT]
>
> Deve ter uma rede virtual existente para criar o ponto final privado. Também deve [desativar as políticas de rede para pontos finais privados](../private-link/disable-private-endpoint-network-policy.md) antes de adicionar o ponto final privado.

> [!WARNING]
>
> Se tiver quaisquer metas de computação existentes associadas a este espaço de trabalho, e não estiverem por trás da mesma rede virtual em que o ponto final privado é criado, eles não funcionarão.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.from_config()
ws.add_private_endpoint(private_endpoint_config=pe, private_endpoint_auto_approval=True, show_output=True)
```

Para obter mais informações sobre as classes e métodos utilizados neste exemplo, consulte [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) e [Workspace.add_private_endpoint](/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#add-private-endpoint-private-endpoint-config--private-endpoint-auto-approval-true--location-none--show-output-true--tags-none-).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

A [extensão Azure CLI para machine learning](reference-azure-machine-learning-cli.md) fornece o comando de [az ml workspace private-endpoint.](/cli/azure/ext/azure-cli-ml/ml/workspace/private-endpoint?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_private_endpoint_add)

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval true --pe-vnet-name myvnet
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

A partir do espaço de trabalho Azure Machine Learning no portal, selecione __ligações de ponto final privado e,__ em seguida, selecione __+ ponto final privado__. Use os campos para criar um novo ponto final privado.

* Ao selecionar a __Região,__ selecione a mesma região que a sua rede virtual. 
* Ao selecionar __o tipo de recurso,__ utilize __o Microsoft.MachineLearningServices/workspaces__. 
* Desaprote o __recurso__ para o nome do seu espaço de trabalho.

Por fim, __selecione Criar__ para criar o ponto final privado.

---

## <a name="remove-a-private-endpoint"></a>Remover um ponto final privado

Utilize um dos seguintes métodos para remover um ponto final privado de um espaço de trabalho:

# <a name="python"></a>[Python](#tab/python)

Utilize [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#delete-private-endpoint-connection-private-endpoint-connection-name-) para remover um ponto final privado.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
# get the connection name
_, _, connection_name = ws.get_details()['privateEndpointConnections'][0]['id'].rpartition('/')
ws.delete_private_endpoint_connection(private_endpoint_connection_name=connection_name)
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

A [extensão Azure CLI para machine learning](reference-azure-machine-learning-cli.md) fornece o comando de [exclusão do espaço de trabalho az ml.](/cli/azure/ext/azure-cli-ml/ml/workspace/private-endpoint?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_private_endpoint_delete)

# <a name="portal"></a>[Portal](#tab/azure-portal)

A partir do espaço de trabalho Azure Machine Learning no portal, selecione __as ligações de ponto final privados__ e, em seguida, selecione o ponto final que pretende remover. Por fim, selecione __Remover__.

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>Usando um espaço de trabalho sobre um ponto final privado

Uma vez que a comunicação ao espaço de trabalho só é permitida a partir da rede virtual, quaisquer ambientes de desenvolvimento que utilizem o espaço de trabalho devem ser membros da rede virtual. Por exemplo, uma máquina virtual na rede virtual.

> [!IMPORTANT]
> Para evitar perturbações temporárias da conectividade, a Microsoft recomenda a descarga da cache DNS nas máquinas que se ligam ao espaço de trabalho depois de permitir o Private Link. 

Para obter informações sobre máquinas virtuais Azure, consulte a [documentação das Máquinas Virtuais.](../virtual-machines/index.yml)

## <a name="enable-public-access"></a>Permitir o acesso do público

Depois de configurar um espaço de trabalho com um ponto final privado, pode opcionalmente permitir o acesso do público ao espaço de trabalho. Ao fazê-lo, não retira o ponto final privado. Permite o acesso do público para além do acesso privado. Para permitir o acesso do público a um espaço de trabalho privado, utilize os seguintes passos:

# <a name="python"></a>[Python](#tab/python)

Utilize [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#delete-private-endpoint-connection-private-endpoint-connection-name-) para remover um ponto final privado.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
ws.update(allow_public_access_when_behind_vnet=True)
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

A [extensão Azure CLI para machine learning](reference-azure-machine-learning-cli.md) fornece o comando de [atualização do espaço de trabalho az ml.](/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_update) Para permitir o acesso do público ao espaço de trabalho, adicione o parâmetro `--allow-public-access true` .

# <a name="portal"></a>[Portal](#tab/azure-portal)

Atualmente não existe forma de ativar esta funcionalidade utilizando o portal.

---


## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a segurança do seu espaço de trabalho Azure Machine Learning, consulte o artigo de visão geral do [isolamento e privacidade da rede Virtual.](how-to-network-security-overview.md)

* Se planeia utilizar uma solução DE DNS personalizada na sua rede virtual, consulte [como utilizar um espaço de trabalho com um servidor DNS personalizado](how-to-custom-dns.md).
