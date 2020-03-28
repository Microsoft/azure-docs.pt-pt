---
title: Tutorial - Eliminar um cluster OpenShift do chapéu vermelho azure
description: Neste tutorial, aprenda a apagar um cluster OpenShift do Chapéu Vermelho Azure usando o Azure CLI
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: c335236a2b0b05f03bef1ebef37f1129a5d0352b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76278765"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Tutorial: Eliminar um cluster OpenShift do chapéu vermelho azure

Chegou ao fim do tutorial. Quando terminar de testar o cluster de amostras, eis como apagá-lo e os seus recursos associados para que não seja cobrado pelo que não está a usar.

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

* Crie um cluster seguindo o tutorial de [cluster Create a Azure Red Hat OpenShift.](tutorial-create-cluster.md)

## <a name="step-1-sign-in-to-azure"></a>Passo 1: Iniciar sessão em Azure

Se estiver a executar o Azure `az login` CLI localmente, corra para iniciar sessão no Azure.

```bash
az login
```

Se tiver acesso a várias `az account set -s {subscription ID}` subscrições, faça a substituição `{subscription ID}` pela subscrição que pretende utilizar.

## <a name="step-2-delete-the-cluster"></a>Passo 2: Eliminar o cluster

Abra um terminal Bash e detete a variável CLUSTER_NAME para o nome do seu cluster:

```bash
CLUSTER_NAME=yourclustername
```

Agora apague o seu cluster:

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

Será-lhe solicitado se pretende eliminar o cluster. Depois de `y`confirmar com, levará vários minutos para apagar o cluster. Quando o comando terminar, todo o Grupo de Recursos e todos os recursos no seu interior, incluindo o cluster, serão eliminados.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Apagar um cluster usando o portal Azure

Alternadamente, pode eliminar o grupo de recursos associados do seu cluster através do portal Azure online. O nome do grupo de recursos é o mesmo que o seu nome de cluster.

Atualmente, `Microsoft.ContainerService/openShiftManagedClusters` o recurso que é criado quando se cria o cluster está escondido no portal Azure. Na `Resource group` vista, `Show hidden types` verifique para ver o grupo de recursos.

![Screenshot da caixa de verificação do tipo oculto](./media/aro-portal-hidden-type.png)

A eliminação do grupo de recursos irá eliminar todos os recursos relacionados que são criados quando se constrói um cluster OpenShift do Chapéu Vermelho Azure.

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, ficou a saber como:
> [!div class="checklist"]
> * Eliminar um cluster do Azure Red Hat OpenShift

Saiba mais sobre o uso do OpenShift com a documentação oficial do [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html)
