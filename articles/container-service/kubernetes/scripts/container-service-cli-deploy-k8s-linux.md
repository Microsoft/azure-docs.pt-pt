---
title: Exemplo do Script da CLI do Azure - Criar Cluster do ACS Linux Kubernetes
description: Exemplo do Script da CLI do Azure - Criar Cluster do ACS Linux Kubernetes
author: iainfoulds
tags: acs, azure-container-service
keywords: Docker, Contentores, Microsserviços, Kubernetes, DC/OS, Azure
ms.assetid: ''
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.date: 05/30/2017
ms.author: iainfou
ms.openlocfilehash: 7b5c6d5931b5d36069850b1bba90413731a48022
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76270699"
---
# <a name="deprecated-create-an-azure-container-service-kubernetes-linux-cluster"></a>PRETERIDO Criar um cluster do kubernetes do serviço de contêiner do Azure

[!INCLUDE [ACS deprecation](../../../../includes/container-service-kubernetes-deprecation.md)]

Este exemplo cria um cluster do Azure Container Service em execução no Kubernetes para contentores baseados no Linux.

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

```azurecli
az group create --name myResourceGroup --location eastus

az acs create \
  --orchestrator-type kubernetes \
  --resource-group myResourceGroup \
  --name myK8SCluster \
  --generate-ssh-keys
```

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar a implementação. Cada item na tabela liga a documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az acs create](https://docs.microsoft.com/cli/azure/acs#az-acs-create) | Cria e cluster ACS. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Pode ver exemplos da CLI do Azure Container Service adicionais na [documentação do Azure Container Service](../cli-samples.md).

