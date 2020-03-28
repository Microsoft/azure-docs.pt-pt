---
title: Tutorial - Escala um cluster OpenShift do chapéu vermelho azure
description: Aprenda a escalar um cluster OpenShift do Microsoft Azure Red Hat usando o Azure CLI
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: c6334aa20b543dfbf87fedcfe45d54bbcf7a219a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79477022"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>Tutorial: Escala um cluster OpenShift do chapéu vermelho azul

Este tutorial é a segunda parte de uma série. Você vai aprender a criar um cluster OpenShift do Microsoft Azure Red Hat usando o Azure CLI, escaloná-lo e depois apagá-lo para limpar recursos.

Na segunda parte da série, saiba como:

> [!div class="checklist"]
> * Escala um aglomerado OpenShift de chapéu vermelho

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * [Criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md)
> * Dimensionar um cluster do Azure Red Hat OpenShift
> * [Eliminar um cluster do Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Crie um cluster seguindo o tutorial de [cluster Create a Azure Red Hat OpenShift.](tutorial-create-cluster.md)

## <a name="step-1-sign-in-to-azure"></a>Passo 1: Iniciar sessão em Azure

Se estiver a executar o Azure `az login` CLI localmente, corra para iniciar sessão no Azure.

```azurecli
az login
```

Se tiver acesso a várias `az account set -s {subscription ID}` subscrições, faça a substituição `{subscription ID}` pela subscrição que pretende utilizar.

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>Passo 2: Escala rés em conjunto com nós adicionais

A partir de um terminal bash, detete a variável CLUSTER_NAME o nome do seu cluster:

```bash
CLUSTER_NAME=yourclustername
```

Agora vamos escalar o cluster para cinco nós usando o CLI Azure:

```azurecli
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

Após alguns `az openshift scale` minutos, completará com sucesso e devolverá um documento JSON contendo os detalhes do cluster em escala.

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, ficou a saber como:

> [!div class="checklist"]
> * Dimensionar um cluster do Azure Red Hat OpenShift

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Eliminar um cluster do Azure Red Hat OpenShift](tutorial-delete-cluster.md)
