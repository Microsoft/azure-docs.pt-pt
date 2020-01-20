---
title: Tutorial-excluir um cluster do Azure Red Hat OpenShift
description: Neste tutorial, saiba como excluir um cluster do Azure Red Hat OpenShift usando o CLI do Azure
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: c335236a2b0b05f03bef1ebef37f1129a5d0352b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278765"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Tutorial: excluir um cluster do Azure Red Hat OpenShift

Chegou ao fim do tutorial. Quando terminar de testar o cluster de exemplo, veja como excluí-lo e seus recursos associados para que você não seja cobrado pelo que não está usando.

Na terceira parte da série, ficará a saber como:

> [!div class="checklist"]
> * Excluir um cluster do Azure Red Hat OpenShift

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * [Criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md)
> * [Dimensionar um cluster do Azure Red Hat OpenShift](tutorial-scale-cluster.md)
> * Excluir um cluster do Azure Red Hat OpenShift

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Crie um cluster seguindo o tutorial [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) .

## <a name="step-1-sign-in-to-azure"></a>Etapa 1: entrar no Azure

Se você estiver executando o CLI do Azure localmente, execute `az login` para entrar no Azure.

```bash
az login
```

Se você tiver acesso a várias assinaturas, execute `az account set -s {subscription ID}` substituindo `{subscription ID}` pela assinatura que você deseja usar.

## <a name="step-2-delete-the-cluster"></a>Etapa 2: excluir o cluster

Abra um terminal Bash e defina a variável CLUSTER_NAME como o nome do seu cluster:

```bash
CLUSTER_NAME=yourclustername
```

Agora, exclua o cluster:

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

Você será avisado se deseja excluir o cluster. Depois de confirmar com `y`, levará vários minutos para excluir o cluster. Quando o comando for concluído, todo o grupo de recursos e todos os recursos dentro dele, incluindo o cluster, serão excluídos.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Excluindo um cluster usando o portal do Azure

Como alternativa, você pode excluir o grupo de recursos associado do cluster por meio do portal do Azure online. O nome do grupo de recursos é o mesmo que o nome do cluster.

Atualmente, o recurso de `Microsoft.ContainerService/openShiftManagedClusters` que é criado quando você cria o cluster está oculto no portal do Azure. Na exibição `Resource group`, marque `Show hidden types` para exibir o grupo de recursos.

![Captura de tela da caixa de seleção de tipo oculto](./media/aro-portal-hidden-type.png)

A exclusão do grupo de recursos excluirá todos os recursos relacionados que são criados quando você cria um cluster do Azure Red Hat OpenShift.

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, ficou a saber como:
> [!div class="checklist"]
> * Excluir um cluster do Azure Red Hat OpenShift

Saiba mais sobre como usar o OpenShift com a documentação oficial do [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html)
