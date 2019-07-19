---
title: Montar um volume emptyDir em instâncias de contêiner do Azure
description: Saiba como montar um volume emptyDir para compartilhar dados entre os contêineres em um grupo de contêineres em instâncias de contêiner do Azure
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: danlep
ms.openlocfilehash: 0dbe26ff1e00e1912cfd63e8383695ca794dd037
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325453"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Montar um volume emptyDir em instâncias de contêiner do Azure

Saiba como montar um volume *emptyDir* para compartilhar dados entre os contêineres em um grupo de contêineres em instâncias de contêiner do Azure.

> [!NOTE]
> A montagem de um volume *emptyDir* está atualmente restrita a contêineres do Linux. Enquanto estamos trabalhando para trazer todos os recursos para contêineres do Windows, você pode encontrar as diferenças da plataforma atual na [visão geral](container-instances-overview.md#linux-and-windows-containers).

## <a name="emptydir-volume"></a>volume emptyDir

O volume *emptyDir* fornece um diretório gravável acessível a cada contêiner em um grupo de contêineres. Os contêineres no grupo podem ler e gravar os mesmos arquivos no volume e podem ser montados usando os mesmos caminhos ou diferentes em cada contêiner.

Alguns exemplos de uso para um volume *emptyDir* :

* Espaço transitório
* Ponto de verificação durante tarefas de execução longa
* Armazenar dados recuperados por um contêiner sidecar e servido por um contêiner de aplicativo

Os dados em um volume *emptyDir* são persistidos por meio de falhas de contêiner. Os contêineres reiniciados, no entanto, não têm a garantia de manter os dados em um volume *emptyDir* .

## <a name="mount-an-emptydir-volume"></a>Montar um volume emptyDir

Para montar um volume emptyDir em uma instância de contêiner, você deve implantar usando um [modelo de Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Primeiro, preencha a `volumes` matriz na seção grupo `properties` de contêineres do modelo. Em seguida, para cada contêiner no grupo de contêineres no qual você deseja montar o volume *emptyDir* , preencha a `volumeMounts` matriz na `properties` seção da definição do contêiner.

Por exemplo, o modelo do Resource Manager a seguir cria um grupo de contêineres que consiste em dois contêineres, cada um montando o volume *emptyDir* :

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Para ver um exemplo de implantação de instância de contêiner com um modelo de Azure Resource Manager, consulte [implantar grupos de vários contêineres em instâncias de contêiner do Azure](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Passos Seguintes

Saiba como montar outros tipos de volume em instâncias de contêiner do Azure:

* [Montar uma partilha de ficheiros do Azure em instâncias de contentor do Azure](container-instances-volume-azure-files.md)
* [Montar um volume gitRepo em instâncias de contêiner do Azure](container-instances-volume-gitrepo.md)
* [Montar um volume secreto em instâncias de contentor do Azure](container-instances-volume-secret.md)