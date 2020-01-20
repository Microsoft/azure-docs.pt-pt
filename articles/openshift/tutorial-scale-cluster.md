---
title: Tutorial-dimensionar um cluster do Azure Red Hat OpenShift
description: Saiba como dimensionar um cluster Microsoft Azure Red Hat OpenShift usando o CLI do Azure
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: bf9172f0c84834c951446520ff0bfcc3ef756c9c
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278315"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>Tutorial: dimensionar um cluster do Azure Red Hat OpenShift

Este tutorial é a segunda parte de uma série. Você aprenderá como criar um cluster Microsoft Azure Red Hat OpenShift usando o CLI do Azure, dimensioná-lo e, em seguida, excluí-lo para limpar os recursos.

Na segunda parte da série, saiba como:

> [!div class="checklist"]
> * Dimensionar um cluster do Red Hat OpenShift

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * [Criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md)
> * Dimensionar um cluster do Azure Red Hat OpenShift
> * [Eliminar um cluster do Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Crie um cluster seguindo o tutorial [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) .

## <a name="step-1-sign-in-to-azure"></a>Etapa 1: entrar no Azure

Se você estiver executando o CLI do Azure localmente, execute `az login` para entrar no Azure.

```bash
az login
```

Se você tiver acesso a várias assinaturas, execute `az account set -s {subscription ID}` substituindo `{subscription ID}` pela assinatura que você deseja usar.

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>Etapa 2: dimensionar o cluster com nós adicionais

De um terminal Bash, defina a variável CLUSTER_NAME como o nome do seu cluster:

```bash
CLUSTER_NAME=yourclustername
```

Agora, vamos dimensionar o cluster para cinco nós usando o CLI do Azure:

```bash
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

Após alguns minutos, `az openshift scale` será concluída com êxito e retornará um documento JSON contendo os detalhes do cluster dimensionado.

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, ficou a saber como:

> [!div class="checklist"]
> * Dimensionar um cluster do Azure Red Hat OpenShift

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Eliminar um cluster do Azure Red Hat OpenShift](tutorial-delete-cluster.md)
