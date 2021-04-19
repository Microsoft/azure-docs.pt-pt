---
title: Pré-requisitos | Modo de ligação direta
description: Pré-requisitos para implantar o controlador de dados no modo de ligação direta.
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/31/2021
ms.topic: overview
ms.openlocfilehash: 940c08dc56e8b0f2217e556da1af31b44ceeda0a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716315"
---
# <a name="deploy-data-controller---direct-connect-mode-prerequisites"></a>Implementar o controlador de dados - modo de ligação direta (pré-requisitos)

Este artigo descreve como se preparar para implementar um controlador de dados para o Azure Arc ativado serviços de dados no modo de ligação direta.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Num resumo de alto nível, os pré-requisitos incluem:

1. Instalar ferramentas
1. Adicionar extensões
1. Crie o principal de serviço e configuure funções para métricas
1. Conecte o cluster Kubernetes ao Azure usando o Arco Azure habilitado a Kubernetes

Depois de ter concluído estes pré-requisitos, pode [implementar o controlador de dados Azure Arc | Modo de ligação direta](deploy-data-controller-direct-mode.md).

As restantes secções deste artigo identificam os pré-requisitos.

## <a name="install-tools"></a>Instalar ferramentas

- Versão de leme 3.3+[(instalação)](https://helm.sh/docs/intro/install/)
- Azure CLI[(instalação)](/sql/azdata/install/deploy-install-azdata)

## <a name="add-extensions-for-azure-cli"></a>Adicionar extensões para Azure CLI

Além disso, são também necessárias as seguintes extensões az:
- Extensão Azure CLI `k8s-extension` (0.2.0)
- Azure CLI `customlocation` (0.1.0)

A amostra `az` e as suas extensões de IPC seriam:

```console
$ az version
{
  "azure-cli": "2.19.1",
  "azure-cli-core": "2.19.1",
  "azure-cli-telemetry": "1.0.6",
  "extensions": {
    "connectedk8s": "1.1.0",
    "customlocation": "0.1.0",
    "k8s-configuration": "1.0.0",
    "k8s-extension": "0.2.0"
  }
}
```

## <a name="create-service-principal-and-configure-roles-for-metrics"></a>Criar funções principais de serviço e configurar para métricas

Siga os passos detalhados no artigo de [métricas enviar](upload-metrics-and-logs-to-azure-monitor.md) e crie um Diretor de Serviço e conceda as funções conforme descrito o artigo. 

As informações SPN ClientID, TenantID e Client Secret serão necessárias quando [implementar o controlador de dados Azure Arc.](deploy-data-controller-direct-mode.md) 

## <a name="connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>Conecte o cluster Kubernetes ao Azure usando o Arco Azure habilitado a Kubernetes

Para completar esta tarefa, siga os passos em [Ligar um cluster Kubernetes existente ao arco de Azure.](../kubernetes/quickstart-connect-cluster.md)

## <a name="next-steps"></a>Passos seguintes

Depois de ter concluído estes pré-requisitos, pode [implementar o controlador de dados Azure Arc | Modo de ligação direta](deploy-data-controller-direct-mode.md).
