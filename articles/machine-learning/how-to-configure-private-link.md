---
title: Configurar um ponto final privado (pré-visualização)
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
ms.date: 07/28/2020
ms.openlocfilehash: bdb7ba30d9fa2d0bd1eff9368d6e30e516b53895
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192734"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Configure Azure Private Link para um espaço de trabalho de aprendizagem de máquinas Azure (pré-visualização)

Neste documento, aprende a utilizar o Azure Private Link com o seu espaço de trabalho Azure Machine Learning. 

> [!IMPORTANT]
> A utilização do Azure Private Link com o espaço de trabalho Azure Machine Learning está atualmente em pré-visualização pública. Esta funcionalidade está disponível apenas nas regiões **leste** e **norte-americanas do Oeste 2.** Esta pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Azure Private Link permite-lhe ligar-se ao seu espaço de trabalho utilizando um ponto final privado. O ponto final privado é um conjunto de endereços IP privados dentro da sua rede virtual. Em seguida, pode limitar o acesso ao seu espaço de trabalho para ocorrer apenas sobre os endereços IP privados. O Private Link ajuda a reduzir o risco de exfiltração de dados. Para saber mais sobre os pontos finais privados, consulte o artigo [Azure Private Link.](/azure/private-link/private-link-overview)

> [!IMPORTANT]
> O Azure Private Link não afeta o plano de controlo Azure (operações de gestão) como a eliminação do espaço de trabalho ou a gestão de recursos computacional. Por exemplo, criar, atualizar ou eliminar um alvo de computação. Estas operações são realizadas através da Internet pública normalmente.
>
> A pré-visualização de instâncias computacional de aprendizagem automática Azure não é suportada num espaço de trabalho onde o Private Link está ativado.
>
> Poderá encontrar problemas ao tentar aceder ao ponto final privado para o seu espaço de trabalho se estiver a utilizar o Mozilla Firefox. Este problema pode estar relacionado com DNS sobre HTTPS na Mozilla. Recomendamos a utilização do Microsoft Edge do Google Chrome como uma solução alternativa.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Criar um espaço de trabalho que use um ponto final privado

> [!IMPORTANT]
> Atualmente, só apoiamos a viabilização de um ponto final privado na criação de um novo espaço de trabalho para aprendizagem automática Azure.

O modelo [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) pode ser usado para criar um espaço de trabalho com um ponto final privado.

Para obter informações sobre a utilização deste modelo, incluindo pontos finais privados, consulte [utilize um modelo de Gestor de Recursos Azure para criar um espaço de trabalho para a aprendizagem automática Azure](how-to-create-workspace-template.md).

## <a name="using-a-workspace-over-a-private-endpoint"></a>Usando um espaço de trabalho sobre um ponto final privado

Uma vez que a comunicação ao espaço de trabalho só é permitida a partir da rede virtual, quaisquer ambientes de desenvolvimento que utilizem o espaço de trabalho devem ser membros da rede virtual. Por exemplo, uma máquina virtual na rede virtual.

> [!IMPORTANT]
> Para evitar perturbações temporárias da conectividade, a Microsoft recomenda a descarga da cache DNS nas máquinas que se ligam ao espaço de trabalho depois de permitir o Private Link. 

Para obter informações sobre máquinas virtuais Azure, consulte a [documentação das Máquinas Virtuais.](/azure/virtual-machines/)


## <a name="using-azure-storage"></a>Utilizar o Armazenamento do Azure

Para proteger a conta de Armazenamento Azure utilizada pelo seu espaço de trabalho, coloque-a dentro da rede virtual.

Para obter informações sobre a colocação da conta de armazenamento na rede virtual, consulte [utilize uma conta de armazenamento para o seu espaço de trabalho.](how-to-enable-virtual-network.md#use-a-storage-account-for-your-workspace)

> [!WARNING]
> O Azure Machine Learning não suporta a utilização de uma conta de Armazenamento Azure que tenha uma ligação privada ativada.

## <a name="using-azure-key-vault"></a>Usando o cofre da chave Azure

Para proteger o Cofre de Chaves Azure utilizado pelo seu espaço de trabalho, pode colocá-lo dentro da rede virtual ou ativar o Link Privado para o mesmo.

Para obter informações sobre a colocação do cofre chave na rede virtual, consulte [use uma instância de cofre chave com o seu espaço de trabalho](how-to-enable-virtual-network.md#key-vault-instance).

Para obter informações sobre a ativação do Link Privado para o cofre da chave, consulte [o Cofre de Chaves Integrado com o Azure Private Link](/azure/key-vault/private-link-service).

## <a name="using-azure-kubernetes-services"></a>Usando serviços Azure Kubernetes

Para proteger os serviços Azure Kubernetes utilizados pelo seu espaço de trabalho, coloque-o dentro de uma rede virtual. Para mais informações, consulte [os Serviços Azure Kubernetes com o seu espaço de trabalho.](how-to-enable-virtual-network.md#aksvnet)

O Azure Machine Learning suporta agora a utilização de um Serviço Azure Kubernetes que tem ligação privada ativada.
Para criar um cluster AKS privado siga os docs [aqui](https://docs.microsoft.com/azure/aks/private-clusters)

## <a name="azure-container-registry"></a>Registo de Contentores do Azure

Para obter informações sobre a segurança do Registo do Contentor Azure dentro da rede virtual, consulte o [Registo do Contentor Azure](how-to-enable-virtual-network.md#azure-container-registry).

> [!IMPORTANT]
> Se estiver a utilizar o Link Privado para o seu espaço de trabalho Azure Machine Learning e colocar o Registo de Contentores Azure para o seu espaço de trabalho numa rede virtual, também deve aplicar o seguinte modelo de Gestor de Recursos Azure. Este modelo permite que o seu espaço de trabalho comunique com a ACR sobre o Link Privado.

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

Para obter mais informações sobre a segurança do seu espaço de trabalho Azure Machine Learning, consulte o artigo de segurança da [Enterprise.](concept-enterprise-security.md)