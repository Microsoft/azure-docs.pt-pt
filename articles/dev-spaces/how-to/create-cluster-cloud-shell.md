---
title: Como criar um cluster kubernetes habilitado para Azure Dev Spaces usando Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Saiba como criar rapidamente um cluster kubernetes habilitado para Azure Dev Spaces diretamente do seu navegador sem instalar nada.
keywords: Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S
ms.openlocfilehash: dbdc9226e417b3142284386ae3586819cda802d9
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280100"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Criar um cluster do Kubernetes com o Azure Cloud Shell

Você pode usar [Azure cloud Shell](/azure/cloud-shell) para criar um cluster do serviço kubernetes do Azure usando o botão **experimentar** nesta página. Se você não estiver conectado, siga os prompts para entrar com uma conta do Azure e digite os comandos no prompt de Azure Cloud Shell quando ele for exibido.

## <a name="create-the-cluster"></a>Criar o cluster

Primeiro, crie o grupo de recursos em uma [região com suporte a Azure dev Spaces][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Crie um cluster do Kubernetes com o seguinte comando:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

A criação do cluster demora alguns minutos.  Ao concluir, a saída é mostrada no formato JSON. Procure `provisioningState` e verifique se ele é `Succeeded`.

## <a name="next-steps"></a>Passos seguintes

Consulte [Azure dev Spaces](/azure/dev-spaces/) para obter links para tutoriais completos.

> [!IMPORTANT]
> Muitos dos guias de início rápido e tutoriais do Azure Dev Spaces usam a CLI do Azure Dev Spaces para executar operações. Não é possível instalar a CLI do Azure Dev Spaces no Azure Cloud Shell.


[supported-regions]: ../about.md#supported-regions-and-configurations