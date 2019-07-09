---
title: Tutorial - Dimensione um cluster do Azure Red Hat OpenShift | Documentos da Microsoft
description: Saiba como dimensionar um cluster do Microsoft Azure Red Hat OpenShift com a CLI do Azure
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: deb136a70c24cb7bd1b6c60505cc6ab0376a7b02
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672440"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>Tutorial: Dimensionar um cluster do Azure Red Hat OpenShift

Este tutorial é a segunda parte de uma série. Irá aprender a criar um cluster do Microsoft Azure Red Hat OpenShift com a CLI do Azure, dimensioná-lo, em seguida, elimine-o para limpar os recursos.

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

* Criar um cluster ao seguir a [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) tutorial.

## <a name="step-1-sign-in-to-azure"></a>Passo 1: Iniciar sessão no Azure

Se estiver a executar a CLI do Azure localmente, execute `az login` para iniciar sessão no Azure.

```bash
az login
```

Se tiver acesso a várias subscrições, execute `az account set -s {subscription ID}` substituindo `{subscription ID}` com a subscrição que pretende utilizar.

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>Passo 2: Dimensionar o cluster connosco adicionais

A partir do Bash terminal, defina a variável CLUSTER_NAME para o nome do cluster:

```bash
CLUSTER_NAME=yourclustername
```

Agora vamos dimensionar o cluster de cinco nós, com a CLI do Azure:

```bash
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

Após alguns minutos, `az openshift scale` será concluído com êxito e devolver um documento JSON que contenha os detalhes do cluster expandido.

## <a name="next-steps"></a>Passos Seguintes

Nesta parte do tutorial, ficou a saber como:

> [!div class="checklist"]
> * Dimensionar um cluster do Azure Red Hat OpenShift

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Eliminar um cluster do Azure Red Hat OpenShift](tutorial-delete-cluster.md)