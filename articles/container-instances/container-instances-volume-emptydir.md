---
title: Monte um volume de emptyDir no Azure Container Instances
description: Saiba como montar um volume de emptyDir para partilhar dados entre os contentores num grupo de contentor no Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: danlep
ms.openlocfilehash: d91706da898e84effc6194a74dce69a66be0f4ac
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657614"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Monte um volume de emptyDir no Azure Container Instances

Saiba como montar uma *emptyDir* volume para partilhar dados entre os contentores num grupo de contentor no Azure Container Instances.

> [!NOTE]
> Montar uma *emptyDir* volume está atualmente restrita para contentores do Linux. Enquanto estamos a trabalhar para colocar todas as funcionalidades para os contentores do Windows, pode encontrar as diferenças de plataforma atual no [descrição geral](container-instances-overview.md#linux-and-windows-containers).

## <a name="emptydir-volume"></a>volumes de emptyDir

O *emptyDir* volume fornece um diretório gravável acessível para cada contentor num grupo de contentores. Contentores no grupo podem ler e escrever os mesmos ficheiros no volume e podem ser montados usando os caminhos idêntica ou diferentes em cada contentor.

Alguns exemplos utiliza para uma *emptyDir* volume:

* Espaço scratch
* Ponto de verificação durante tarefas de longa execução
* Store dados obtidos através de um contentor de sidecar e servido por um contentor de aplicações

Dados num *emptyDir* volume é persistido por meio de falhas de contentor. Contentores que são reiniciados, no entanto, não são garantidos para persistir os dados num *emptyDir* volume.

## <a name="mount-an-emptydir-volume"></a>Monte um volume de emptyDir

Para montar um volume de emptyDir numa instância de contentor, tem de implementar com um [modelo Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Em primeiro lugar, preencher a `volumes` matriz no grupo de contentores `properties` secção do modelo. Em seguida, para cada contentor no grupo de contentores em que pretende montar o *emptyDir* volume, preencher a `volumeMounts` obsahuje pole o `properties` secção da definição de contentor.

Por exemplo, o modelo do Resource Manager seguinte cria um grupo de contentores que consiste em dois contentores, cada um dos quais monta a *emptyDir* volume:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Para ver um exemplo de implementação de instância de contentor com um modelo Azure Resource Manager, consulte [implementar grupos de vários contentores no Azure Container Instances](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Passos Seguintes

Saiba como montar outros tipos de volume no Azure Container Instances:

* [Montar uma partilha de ficheiros do Azure em instâncias de contentor do Azure](container-instances-volume-azure-files.md)
* [Monte um volume de gitRepo no Azure Container Instances](container-instances-volume-gitrepo.md)
* [Montar um volume secreto em instâncias de contentor do Azure](container-instances-volume-secret.md)