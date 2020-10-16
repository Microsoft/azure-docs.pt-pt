---
title: Configurar um ponto final privado
titleSuffix: Azure Machine Learning
description: Utilize o Azure Private Link para aceder de forma segura ao seu espaço de trabalho Azure Machine Learning a partir de uma rede virtual.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.openlocfilehash: 4ba7ec73ac70723e21b6acad571d62d14edd250a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91828126"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Configure Azure Private Link para um espaço de trabalho de aprendizagem de máquinas Azure

Neste documento, aprende a utilizar o Azure Private Link com o seu espaço de trabalho Azure Machine Learning. Para obter informações sobre a criação de uma rede virtual para Azure Machine Learning, consulte [o isolamento da rede virtual e a visão geral da privacidade](how-to-network-security-overview.md)

O Azure Private Link permite-lhe ligar-se ao seu espaço de trabalho utilizando um ponto final privado. O ponto final privado é um conjunto de endereços IP privados dentro da sua rede virtual. Em seguida, pode limitar o acesso ao seu espaço de trabalho para ocorrer apenas sobre os endereços IP privados. O Private Link ajuda a reduzir o risco de exfiltração de dados. Para saber mais sobre os pontos finais privados, consulte o artigo [Azure Private Link.](/azure/private-link/private-link-overview)

> [!IMPORTANT]
> O Azure Private Link não afeta o plano de controlo Azure (operações de gestão) como a eliminação do espaço de trabalho ou a gestão de recursos computacional. Por exemplo, criar, atualizar ou eliminar um alvo de computação. Estas operações são realizadas através da Internet pública normalmente. As operações de plano de dados, tais como a utilização do estúdio Azure Machine Learning, APIs (incluindo oleodutos publicados), ou o SDK usam o ponto final privado.
>
> Poderá encontrar problemas ao tentar aceder ao ponto final privado para o seu espaço de trabalho se estiver a utilizar o Mozilla Firefox. Este problema pode estar relacionado com DNS sobre HTTPS na Mozilla. Recomendamos a utilização do Microsoft Edge do Google Chrome como uma solução alternativa.

## <a name="prerequisites"></a>Pré-requisitos

Se planeia utilizar um link privado habilitado espaço de trabalho com uma chave gerida pelo cliente, deve solicitar esta funcionalidade usando um bilhete de apoio. Para obter mais informações, consulte [Gerir e aumentar as quotas.](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)

## <a name="limitations"></a>Limitações

A utilização de um espaço de trabalho de aprendizagem automática Azure com ligação privada não está disponível nas regiões do Governo Azure ou nas regiões Azure China 21Vianet.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Criar um espaço de trabalho que use um ponto final privado

Utilize um dos seguintes métodos para criar um espaço de trabalho com um ponto final privado:

> [!TIP]
> O modelo Azure Resource Manager pode criar uma nova rede virtual, se necessário. Todos os outros métodos requerem uma rede virtual existente.

# <a name="resource-manager-template"></a>[Modelo de gestor de recursos](#tab/azure-resource-manager)

O modelo Azure Resource Manager [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) proporciona uma forma fácil de criar um espaço de trabalho com um ponto final privado e rede virtual.

Para obter informações sobre a utilização deste modelo, incluindo pontos finais privados, consulte [utilize um modelo de Gestor de Recursos Azure para criar um espaço de trabalho para a aprendizagem automática Azure](how-to-create-workspace-template.md).

# <a name="python"></a>[Python](#tab/python)

A Azure Machine Learning Python SDK fornece a classe [PrivateEndpointConfig,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) que pode ser usada com [Workspace.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) para criar um espaço de trabalho com um ponto final privado. Esta classe requer uma rede virtual existente.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

A [extensão Azure CLI para machine learning](reference-azure-machine-learning-cli.md) fornece o espaço de trabalho [az ml criar](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_create) comando. Os seguintes parâmetros para este comando podem ser usados para criar um espaço de trabalho com uma rede privada, mas requer uma rede virtual existente:

* `--pe-name`: O nome do ponto final privado que é criado.
* `--pe-auto-approval`: Se as ligações privadas ao espaço de trabalho devem ser automaticamente aprovadas.
* `--pe-resource-group`: O grupo de recursos para criar o ponto final privado em. Deve ser o mesmo grupo que contém a rede virtual.
* `--pe-vnet-name`: A rede virtual existente para criar o ponto final privado em.
* `--pe-subnet-name`: O nome da sub-rede para criar o ponto final privado em. O valor predefinido é `default`.

# <a name="portal"></a>[Portal](#tab/azure-portal)

O __separador Networking__ no estúdio Azure Machine Learning permite-lhe configurar um ponto final privado. No entanto, requer uma rede virtual existente. Para obter mais informações, consulte [Criar espaços de trabalho no portal.](how-to-manage-workspace.md)

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>Usando um espaço de trabalho sobre um ponto final privado

Uma vez que a comunicação ao espaço de trabalho só é permitida a partir da rede virtual, quaisquer ambientes de desenvolvimento que utilizem o espaço de trabalho devem ser membros da rede virtual. Por exemplo, uma máquina virtual na rede virtual.

> [!IMPORTANT]
> Para evitar perturbações temporárias da conectividade, a Microsoft recomenda a descarga da cache DNS nas máquinas que se ligam ao espaço de trabalho depois de permitir o Private Link. 

Para obter informações sobre máquinas virtuais Azure, consulte a [documentação das Máquinas Virtuais.](/azure/virtual-machines/)


## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a segurança do seu espaço de trabalho Azure Machine Learning, consulte o artigo de visão geral do [isolamento e privacidade da rede Virtual.](how-to-network-security-overview.md)

* Se planeia utilizar uma solução DE DNS personalizada na sua rede virtual, consulte [como utilizar um espaço de trabalho com um servidor DNS personalizado](how-to-custom-dns.md).
