---
title: Tutorial - Apagar um aglomerado Azure Red Hat OpenShift
description: Neste tutorial, aprenda a apagar um cluster Azure Red Hat OpenShift usando o Azure CLI
author: sakthi-vetrivel
ms.custom: fasttrack-edit
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 372f531154528698da05c7a20c5d842b593259d1
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072256"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-4-cluster"></a>Tutorial: Apagar um aglomerado de chapéu vermelho Azure OpenShift 4

Neste tutorial, parte três de três, é eliminado um cluster Azure Red Hat OpenShift com openshift 4. Saiba como:

> [!div class="checklist"]
> * Eliminar um cluster do Azure Red Hat OpenShift


## <a name="before-you-begin"></a>Antes de começar

Em tutoriais anteriores, um cluster Azure Red Hat OpenShift foi criado e ligado à utilização da consola web OpenShift. Se não tiver feito estes passos, e gostaria de seguir em frente, comece com [Tutorial 1 - Crie um Azure Red Hat Openshift 4 Cluster.](tutorial-create-cluster.md)

Se optar por instalar e utilizar o CLI localmente, este tutorial requer que esteja a executar a versão Azure CLI 2.6.0 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Se estiver a comandar o Azure CLI localmente, corra `az login` para se inscrever em Azure.

```bash
az login
```

Se tiver acesso a várias subscrições, faça `az account set -s {subscription ID}` a substituição `{subscription ID}` pela subscrição que pretende utilizar.

## <a name="delete-the-cluster"></a>Eliminar o cluster

Em tutoriais anteriores, foram definidas as seguintes variáveis.

```bash
CLUSTER=yourclustername
RESOURCEGROUP=yourresourcegroup
```

Utilizando estes valores, elimine o seu cluster:

```bash
az aro delete --resource-group $RESOURCEGROUP --name $CLUSTER
```

Em seguida, será solicitado que confirme se deseja eliminar o cluster. Depois de `y` confirmar, levará vários minutos para apagar o cluster. Quando o comando terminar, todo o grupo de recursos e todos os recursos dentro dele - incluindo o cluster - serão eliminados.

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, ficou a saber como:
> [!div class="checklist"]
> * Eliminar um cluster do Azure Red Hat OpenShift 4

Saiba mais sobre a utilização do OpenShift com a documentação oficial [do Red Hat OpenShift](https://docs.openshift.com/container-platform/4.6/welcome/index.html)
