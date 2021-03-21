---
title: Implementar um cluster gerido por Tecido de Serviço (pré-visualização)
description: Neste tutorial, irá implementar um cluster gerido por Tecido de Serviço para testes.
ms.topic: tutorial
ms.date: 08/27/2020
ms.custom: references_regions
ms.openlocfilehash: fd54e6f8c5272c3a8fe0e0162ca2e57968b24642
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791715"
---
# <a name="tutorial-deploy-a-service-fabric-managed-cluster-preview"></a>Tutorial: Implementar um cluster gerido por tecido de serviço (pré-visualização)

Nesta série tutorial vamos discutir:

> [!div class="checklist"]
> * Como implementar um cluster gerido por Tecido de Serviço 
> * [Como escalar um cluster gerido de tecido de serviço](tutorial-managed-cluster-scale.md)
> * [Como adicionar e remover os nódes num cluster gerido por Tecido de Serviço](tutorial-managed-cluster-add-remove-node-type.md)
> * [Como implementar uma aplicação para um cluster gerido por Tecido de Serviço](tutorial-managed-cluster-deploy-app.md)

Esta parte da série abrange como:

> [!div class="checklist"]
> * Ligar à sua conta do Azure
> * Criar um novo grupo de recursos
> * Implementar um cluster gerido por tecido de serviço
> * Adicione um tipo de nó primário ao cluster

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) se ainda não tiver uma subscrição do Azure

* Instale o [módulo SDK e PowerShell do tecido de serviço](service-fabric-get-started.md).

* Instale [a Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) (ou mais tarde).

## <a name="connect-to-your-azure-account"></a>Ligar à sua conta do Azure

`<your-subscription>`Substitua-a pela cadeia de subscrição da sua conta Azure e conecte-a:

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <your-subscription>

```

## <a name="create-a-new-resource-group"></a>Criar um novo grupo de recursos

Em seguida, crie o grupo de recursos para o cluster Managed Service Fabric, substituindo `<your-rg>` e pelo nome e `<location>` localização de grupo pretendidos.

> [!NOTE]
> As regiões apoiadas para a pré-visualização pública `centraluseuap` incluem, , , , e `eastus2euap` `eastasia` `northeurope` `westcentralus` `eastus2` .

```powershell
$resourceGroup = "myResourceGroup"
$location = "EastUS2"

New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="deploy-a-service-fabric-managed-cluster"></a>Implementar um cluster gerido por tecido de serviço

### <a name="create-a-service-fabric-managed-cluster"></a>Criar um cluster gerido do Service Fabric

Neste passo, irá criar um cluster gerido por Tecido de Serviço utilizando o comando powerShell New-AzServiceFabricManagedCluster. O exemplo a seguir cria um cluster chamado myCluster no grupo de recursos chamado myResourceGroup. Este grupo de recursos foi criado no passo anterior na região leste2.

Para este passo, forneça os seus próprios valores para os seguintes parâmetros:

* **Nome do cluster**: Introduza um nome único para o seu cluster, como *o mysfcluster*.
* **Admin Password**: Introduza uma palavra-passe para o administrador a ser utilizado para RDP nos VMs subjacentes no cluster.
* **Impressão digital do Certificado do Cliente**: Forneça a impressão digital do certificado de cliente que gostaria de utilizar para aceder ao seu cluster. Se não tiver um certificado, siga [o conjunto e recupere um certificado](../key-vault/certificates/quick-create-portal.md) para criar um certificado auto-assinado.
* **Cluster SKU**: Especifique o [tipo de conjunto de tecido de serviço gerido](overview-managed-cluster.md#service-fabric-managed-cluster-skus) para implantar. *Básico* Os clusters SKU destinam-se apenas a implantações de teste e não permitem a adição ou remoção do tipo de nó.

```powershell
$clusterName = "<unique cluster name>"
$password = "Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$clientThumbprint = "<certificate thumbprint>"
$clusterSku = "Standard"

New-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroup -Location $location -ClusterName $clusterName -ClientCertThumbprint $clientThumbprint -ClientCertIsAdmin -AdminPassword $password -Sku $clusterSKU -Verbose
```

### <a name="add-a-primary-node-type-to-the-service-fabric-managed-cluster"></a>Adicione um tipo de nó primário ao cluster gerido do Tecido de Serviço

Neste passo, irá adicionar um nó primário ao cluster que acaba de criar. Cada cluster de tecido de serviço deve ter pelo menos um tipo de nó primário.

Para este passo, forneça os seus próprios valores para os seguintes parâmetros:

* **Nome do tipo nó**: Introduza um nome único para o tipo de nó a ser adicionado ao seu cluster, como "NT1".

> [!NOTE]
> Se o tipo de nó adicionado for o primeiro ou único tipo de nó no cluster, a propriedade primária deve ser usada.

```powershell
$nodeType1Name = "NT1"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeType1Name -Primary -InstanceCount 5
```

Este comando pode demorar alguns minutos a ser concluído.

## <a name="validate-the-deployment"></a>Validar a implementação

### <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Uma vez concluída a implementação, encontre o valor do Explorador de Tecido de Serviço na página de visão geral do cluster gerido pelo Service Fabric no Portal. Quando solicitado para um certificado, utilize o certificado para o qual o polegar do cliente foi fornecido no comando PowerShell.

## <a name="next-steps"></a>Passos seguintes

Neste passo criamos e implantamos o nosso primeiro cluster gerido de Tecido de Serviço. Para saber mais sobre como escalar um cluster, consulte:

> [!div class="nextstepaction"]
> [Dimensione um cluster gerido de tecido de serviço](tutorial-managed-cluster-scale.md)
