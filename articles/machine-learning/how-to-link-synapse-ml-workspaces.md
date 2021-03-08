---
title: Criar um serviço ligado com espaços de trabalho de Machine Learning Synapse e Azure Machine Learning (pré-visualização)
titleSuffix: Azure Machine Learning
description: Saiba como ligar os espaços de trabalho de Azure Synapse e Azure Machine Learning para uma experiência unificada de conflito de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/08/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 8941a7f7a27f6ffe58cda3f0bf2c6833ec226783
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102456282"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-preview"></a>Link Azure Synapse Analytics e Azure Machine Learning espaços de trabalho (pré-visualização)

Neste artigo, você aprende a criar um serviço ligado que liga o seu espaço de trabalho [Azure Synapse Analytics](/synapse-analytics/overview-what-is.md) e [o espaço de trabalho Azure Machine Learning](concept-workspace.md).

Com o seu espaço de trabalho Azure Machine Learning ligado ao seu espaço de trabalho Azure Synapse, pode anexar uma piscina Apache Spark como um computamento dedicado para a luta de dados em escala e realizar treino de modelo a partir do mesmo caderno.

Você pode ligar o seu espaço de trabalho ML e o espaço de trabalho Synapse através do [Python SDK](#link-sdk) ou do [estúdio Azure Machine Learning](#link-studio).

Também pode ligar espaços de trabalho e anexar uma piscina de faíscas synapse com um único [modelo Azure Resource Manager (ARM).](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)

>[!IMPORTANT]
> A Azure Machine Learning e a integração Azure Synapse estão em pré-visualização pública. As funcionalidades apresentadas no `azureml-synapse` pacote são funcionalidades [experimentais](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental) de pré-visualização, podendo ser alteradas a qualquer momento.

## <a name="prerequisites"></a>Pré-requisitos

* Criar um espaço de trabalho para [aprendizagem automática Azure.](how-to-manage-workspace.md?tabs=python)

* [Criar um espaço de trabalho sinapse no portal Azure](/synapse-analytics/quickstart-create-workspace.md).

* [Crie a piscina Apache Spark utilizando o portal Azure, ferramentas web ou o Estúdio Synapse](/synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* Instalar o [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)

* Acesso ao [estúdio Azure Machine Learning.](https://ml.azure.com/)

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>Link espaços de trabalho com o Python SDK

> [!IMPORTANT]
> Para ligar ao espaço de trabalho da Sinapse com sucesso, deve ser-lhe concedido o papel de **Proprietário** do espaço de trabalho synapse. Verifique o seu acesso no [portal Azure](https://ms.portal.azure.com/).
>
> Se não é **Proprietário** e é apenas um **Contribuinte** para o espaço de trabalho synapse, só pode utilizar os serviços existentes ligados. Veja como [retrátil e utilize um serviço ligado existente.](how-to-data-prep-synapse-spark-pool.md#get-an-existing-linked-service)

O seguinte código emprega as [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice?preserve-view=true&view=azure-ml-py) classes e as classes [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration?preserve-view=true&view=azure-ml-py) para,

* Ligue o seu espaço de trabalho de aprendizagem automática, `ws` com o seu espaço de trabalho Azure Synapse.
* Registe o seu espaço de trabalho synapse com a Azure Machine Learning como um serviço ligado.

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```

> [!IMPORTANT] 
> Uma identidade gerida, `system_assigned_identity_principal_id` é criada para cada serviço ligado. Esta identidade gerida deve ser concedida ao papel de **Administrador de Faíscas De sinapse Apache** do espaço de trabalho synapse antes de iniciar a sua sessão de Sinapse. [Atribua o papel de Administrador de Faíscas Apache Synapse à identidade gerida no Estúdio Synapse](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md).
>
> Para encontrar o `system_assigned_identity_principal_id` serviço de ligação específica, utilize `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` .

### <a name="manage-linked-services"></a>Gerir serviços ligados

Veja todos os serviços ligados associados ao seu espaço de trabalho de aprendizagem automática.

```python
LinkedService.list(ws)
```

Para desvincular os seus espaços de trabalho, use o `unregister()` método

``` python
linked_service.unregister()
```

<a name="link-studio"></a>
## <a name="link-workspaces-via-studio"></a>Link espaços de trabalho via estúdio

Ligue o seu espaço de trabalho de aprendizagem automática e o espaço de trabalho da Synapse através do estúdio Azure Machine Learning com os seguintes passos: 

1. Inscreva-se no [estúdio Azure Machine Learning.](https://ml.azure.com/)
1. Selecione **Serviços Ligados** na secção **Gerir** o painel esquerdo.
1. Selecione **Adicionar integração**.
1. Na forma de **espaço de trabalho Link,** povoar os campos 
    Campo| Descrição    
    ---|---
    Nome| Forneça um nome para o seu serviço ligado. Este nome é o que será usado para fazer referência a este serviço particular ligado.
    Nome da subscrição | Selecione o nome da sua subscrição que está associada ao seu espaço de trabalho de aprendizagem automática. 
    Espaço de trabalho sinapse | Selecione o espaço de trabalho Synapse a que pretende ligar.
1. Selecione **Seguinte** para abrir o formulário **Select Spark pools (opcional).** Neste formulário, você seleciona qual piscina synapse Spark para anexar ao seu espaço de trabalho

1. Selecione **Seguinte** para abrir o formulário **'Rever'** e verificar as suas seleções.
1. Selecione **Criar** para completar o processo de criação de serviços ligado.

## <a name="next-steps"></a>Passos seguintes

* [Anexar piscinas de faíscas synapse para preparação de dados com Azure Synapse (pré-visualização)](how-to-data-prep-synapse-spark-pool.md).
* [Como utilizar o Apache Spark no seu pipeline de aprendizagem automática com a Azure Synapse (pré-visualização)](how-to-use-synapsesparkstep.md)
* [Treine um modelo.](how-to-set-up-training-targets.md)
