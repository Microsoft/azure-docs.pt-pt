---
title: Crie um cluster Kubernetes com espaços Azure Dev habilitados - Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Aprenda a criar rapidamente um cluster Kubernetes habilitado para o Azure Dev Spaces diretamente do seu navegador sem instalar nada.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s
ms.openlocfilehash: 5e2e5cfd22eeedd3554737458caeca0b891b62fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77605298"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Crie um cluster Kubernetes com espaços Azure Dev habilitados com casca de nuvem azure

Pode utilizar a [Azure Cloud Shell](/azure/cloud-shell) para criar um cluster de serviço Azure Kubernetes utilizando o botão **Try It** a partir desta página. Se não estiver inscrito, siga as instruções para iniciar sessão com uma conta Azure e, em seguida, digite os comandos no aviso Azure Cloud Shell quando aparecer.

## <a name="create-the-cluster"></a>Criar o cluster

Em primeiro lugar, crie o grupo de recursos numa região que apoie o [Azure Dev Spaces.][supported-regions]

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Crie um cluster do Kubernetes com o seguinte comando:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

A criação do cluster demora alguns minutos.  Quando concluída, a saída é mostrada no formato JSON. Procure `provisioningState` e verifique se `Succeeded`é.

## <a name="next-steps"></a>Passos seguintes

Consulte [o Azure Dev Spaces](/azure/dev-spaces/) para obter links para tutoriais completos.

> [!IMPORTANT]
> Muitos dos quickstarts e tutoriais do Azure Dev Spaces utilizam o Azure Dev Spaces CLI para realizar operações. Não é possível instalar o Azure Dev Spaces CLI na Casca de Nuvem Azure.


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service