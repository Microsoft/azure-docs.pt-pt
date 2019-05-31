---
title: Como criar um cluster de Kubernetes ativados para espaços de desenvolvimento do Azure com o Azure Cloud Shell
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 10/04/2018
ms.topic: conceptual
description: Saiba como criar rapidamente um cluster do Kubernetes ativado para espaços de desenvolvimento do Azure diretamente a partir do seu browser, sem instalar nada.
keywords: Docker, o Kubernetes, o Azure, o AKS, o serviço Kubernetes do Azure, contentores, Helm, a malha de serviço, roteamento de malha do serviço, kubectl, k8s
ms.openlocfilehash: 532a094f96817f3c525384694b6fa60a292aa948
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391663"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Criar um cluster do Kubernetes com o Azure Cloud Shell

Pode usar [Azure Cloud Shell](/azure/cloud-shell) para criar um cluster de espaços de desenvolvimento do Azure utilizando o **experimentar** botão a partir desta página. Se não tem sessão iniciada, siga as instruções para iniciar sessão com uma conta do Azure, em seguida, escreva os comandos na linha de comandos do Azure Cloud Shell, quando ele é exibido.

## <a name="create-the-cluster"></a>Criar o cluster

Primeiro, crie o grupo de recursos num [região que suporte do Azure Dev espaços][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Crie um cluster do Kubernetes com o seguinte comando:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

A criação do cluster demora alguns minutos.  Quando terminar, o resultado é mostrado no formato JSON. Procure `provisioningState` e certifique-se de que tem `Succeeded`.

## <a name="next-steps"></a>Passos Seguintes

Ver [do Azure Dev espaços](/azure/dev-spaces/) para obter ligações para tutoriais completas.


[supported-regions]: ../about.md#supported-regions-and-configurations