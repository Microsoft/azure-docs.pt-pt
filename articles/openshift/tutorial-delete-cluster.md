---
title: Tutorial - eliminar um cluster do Azure Red Hat OpenShift | Documentos da Microsoft
description: Neste tutorial, saiba como eliminar um cluster do Azure Red Hat OpenShift com a CLI do Azure
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/06/2019
ms.openlocfilehash: ce4142bdcdfb7a9ab687bb60dca91d6aab00c7bd
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080717"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Tutorial: Eliminar um cluster do Azure Red Hat OpenShift

Chegou ao fim do tutorial. Quando tiver terminado de testar o cluster de exemplo, eis como eliminar ele e seus recursos associados, de modo que não é cobrada o que não estiver a utilizar.

Na terceira parte da série, ficará a saber como:

> [!div class="checklist"]
> * Eliminar um cluster do Azure Red Hat OpenShift

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * [Criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md)
> * [Dimensionar um cluster do Azure Red Hat OpenShift](tutorial-scale-cluster.md)
> * Eliminar um cluster do Azure Red Hat OpenShift

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Criar um cluster ao seguir a [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) tutorial.

## <a name="step-1-sign-in-to-azure"></a>Passo 1: Iniciar sessão no Azure

Se estiver a executar a CLI do Azure localmente, execute `az login` para iniciar sessão no Azure.

```bash
az login
```

Se tiver acesso a várias subscrições, execute `az account set -s {subscription ID}` substituindo `{subscription ID}` com a subscrição que pretende utilizar.

## <a name="step-2-delete-the-cluster"></a>Passo 2: Eliminar o cluster

Abra um terminal de Bash e defina a variável CLUSTER_NAME para o nome do cluster:

```bash
CLUSTER_NAME=yourclustername
```

Agora, elimina o cluster:

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

Será solicitado que se pretende eliminar o cluster. Depois de confirmar com `y`, irá demorar alguns minutos para eliminar o cluster. Quando o comando for concluído, o grupo de recursos completo e todos os recursos no interior do mesmo, incluindo o cluster, serão eliminados.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>A eliminar um cluster com o portal do Azure

Em alternativa, pode eliminar o grupo de recursos associados do seu cluster através do portal do Azure online. O nome do grupo de recursos é o mesmo que o nome do cluster.

Atualmente, o `Microsoft.ContainerService/openShiftManagedClusters` recurso que é criado quando criar o cluster está oculta no portal do Azure. Na `Resource group` modo de exibição, verificação `Show hidden types` para ver o grupo de recursos.

![Captura de ecrã da caixa de seleção de tipo ocultos](./media/aro-portal-hidden-type.png)

A eliminar o grupo de recursos irá eliminar todos os recursos relacionados que são criados quando cria um cluster do Azure Red Hat OpenShift.

## <a name="next-steps"></a>Passos Seguintes

Nesta parte do tutorial, ficou a saber como:
> [!div class="checklist"]
> * Eliminar um cluster do Azure Red Hat OpenShift

Saiba mais sobre como utilizar o OpenShift com oficial [documentação do Red Hat OpenShift](https://access.redhat.com/documentation/openshift_dedicated/3/)