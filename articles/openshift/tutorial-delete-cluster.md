---
title: Tutorial - Eliminar um cluster OpenShift do chapéu vermelho azure
description: Neste tutorial, aprenda a apagar um cluster OpenShift do Chapéu Vermelho Azure usando o Azure CLI
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 2de60b90eb6fb75ef013a2fd8785f1b8b616fba6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232143"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-4-cluster"></a>Tutorial: Eliminar um cluster OpenShift 4 do chapéu vermelho azure

Neste tutorial, parte três de três, um cluster OpenShift de chapéu vermelho azure executando OpenShift 4 é eliminado. Saiba como:

> [!div class="checklist"]
> * Eliminar um cluster do Azure Red Hat OpenShift


## <a name="before-you-begin"></a>Antes de começar

Em tutoriais anteriores, foi criado um cluster OpenShift do Chapéu Vermelho Azure e ligado à utilização da consola web OpenShift. Se não fez estes passos, e gostaria de seguir em frente, comece com [tutorial 1 - Crie um Cluster Openshift 4 do Chapéu Vermelho Azure.](tutorial-create-cluster.md)

Se optar por instalar e utilizar o CLI localmente, este tutorial requer que esteja a executar a versão Azure CLI 2.0.75 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Se estiver a executar o Azure `az login` CLI localmente, corra para iniciar sessão no Azure.

```bash
az login
```

Se tiver acesso a várias `az account set -s {subscription ID}` subscrições, faça a substituição `{subscription ID}` pela subscrição que pretende utilizar.

## <a name="delete-the-cluster"></a>Eliminar o cluster

Em tutoriais anteriores, foram definidas as seguintes variáveis. 

```bash
CLUSTER=yourclustername
RESOURCE_GROUP=yourresourcegroup
```

Utilizando estes valores, elimine o seu cluster:

```bash
az aro delete --resource-group $RESOURCEGROUP --name $CLUSTER
```

Em seguida, ser-lhe-á solicitado que confirme se pretende eliminar o cluster. Depois de `y`confirmar com, levará vários minutos para apagar o cluster. Quando o comando terminar, todo o grupo de recursos e todos os recursos que estão no seu interior, incluindo o cluster, serão eliminados.

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, ficou a saber como:
> [!div class="checklist"]
> * Eliminar um cluster OpenShift 4 do chapéu vermelho azure

Saiba mais sobre o uso do OpenShift com a documentação oficial do [Red Hat OpenShift](https://www.openshift.com/try)
