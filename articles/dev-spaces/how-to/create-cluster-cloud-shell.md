---
title: Como criar um cluster de Kubernetes ativados para espaços de desenvolvimento do Azure com o Azure Cloud Shell | Documentos da Microsoft
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 10/04/2018
ms.topic: article
description: Saiba como criar rapidamente um cluster do Kubernetes ativado para espaços de desenvolvimento do Azure diretamente a partir do seu browser, sem instalar nada.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores
ms.openlocfilehash: 81d8315224550791001610a9c32c2e2daee7e99d
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56823778"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Criar um cluster de Kubernetes com o Azure Cloud Shell

Pode usar [Azure Cloud Shell](/azure/cloud-shell) para criar um cluster de espaços de desenvolvimento do Azure utilizando o **experimentar** botão a partir desta página. Se não tem sessão iniciada, siga as instruções para iniciar sessão com uma conta do Azure, em seguida, escreva os comandos na linha de comandos do Azure Cloud Shell, quando ele é exibido.

## <a name="create-the-cluster"></a>Criar o cluster

Primeiro, crie o grupo de recursos. Utilize uma das regiões suportadas atualmente (EUA Leste, EUA Leste 2, EUA Central, EUA Oeste 2, Europa Ocidental, Sudeste Asiático, Canadá Central ou Leste do Canadá).

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Crie um cluster do Kubernetes com o seguinte comando:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --kubernetes-version 1.10.9
```

A criação do cluster demora alguns minutos.  Quando terminar, o resultado é mostrado no formato JSON. Procure `provisioningState` e certifique-se de que tem `Succeeded`.

## <a name="next-steps"></a>Passos Seguintes

Ver [do Azure Dev espaços](/azure/dev-spaces/) para obter ligações para tutoriais completas.
