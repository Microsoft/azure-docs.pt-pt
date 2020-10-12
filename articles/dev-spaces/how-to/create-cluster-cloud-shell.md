---
title: Criar um cluster Kubernetes com espaços Azure Dev habilitados - Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Aprenda a criar rapidamente um cluster Kubernetes ativado para Azure Dev Spaces diretamente do seu navegador sem instalar nada.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s
ms.openlocfilehash: f6da9055e11b5c514d71122c3650bbc326fb8de8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86232292"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Criar um cluster Kubernetes com espaços Azure Dev habilitados com Azure Cloud Shell

Pode utilizar [a Azure Cloud Shell](/azure/cloud-shell) para criar um cluster de serviço Azure Kubernetes utilizando o botão Try **It** a partir desta página. Se não estiver inscrito, siga as instruções para iniciar sação com uma conta Azure e, em seguida, digite os comandos na ordem Azure Cloud Shell quando aparecer.

## <a name="create-the-cluster"></a>Criar o cluster

Em primeiro lugar, crie o grupo de recursos numa [região que apoie os Espaços Azure Dev.][supported-regions]

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Crie um cluster do Kubernetes com o seguinte comando:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

A criação do cluster demora alguns minutos.  Quando concluída, a saída é mostrada no formato JSON. Procure `provisioningState` e verifique se `Succeeded` é.

## <a name="next-steps"></a>Passos seguintes

Consulte [os Espaços Azure Dev](../index.yml) para obter links para tutoriais completos.

> [!IMPORTANT]
> Muitos dos quickstarts e tutoriais do Azure Dev Spaces usam o Azure Dev Spaces CLI para realizar operações. Não é possível instalar o Azure Dev Spaces CLI na Concha da Nuvem Azure.


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
