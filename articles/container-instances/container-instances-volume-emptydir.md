---
title: Montar o volume emptyDir para o grupo de contêineres
description: Saiba como montar um volume emptyDir para compartilhar dados entre os contêineres em um grupo de contêineres em instâncias de contêiner do Azure
ms.topic: article
ms.date: 02/08/2018
ms.openlocfilehash: 955423b685ebb3979271c7c2dc7e835a16100c2b
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552462"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Montar um volume emptyDir em instâncias de contêiner do Azure

Saiba como montar um volume *emptyDir* para compartilhar dados entre os contêineres em um grupo de contêineres em instâncias de contêiner do Azure. Use volumes *emptyDir* como caches efêmeros para suas cargas de trabalho em contêineres.

> [!NOTE]
> A montagem de um volume *emptyDir* está atualmente restrita a contêineres do Linux. Enquanto estamos trabalhando para trazer todos os recursos para contêineres do Windows, você pode encontrar as diferenças da plataforma atual na [visão geral](container-instances-overview.md#linux-and-windows-containers).

## <a name="emptydir-volume"></a>volume emptyDir

O volume *emptyDir* fornece um diretório gravável acessível a cada contêiner em um grupo de contêineres. Os contêineres no grupo podem ler e gravar os mesmos arquivos no volume e podem ser montados usando os mesmos caminhos ou diferentes em cada contêiner.

Alguns exemplos de uso para um volume *emptyDir* :

* Espaço transitório
* Ponto de verificação durante tarefas de execução longa
* Armazenar dados recuperados por um contêiner sidecar e servido por um contêiner de aplicativo

Os dados em um volume *emptyDir* são persistidos por meio de falhas de contêiner. Os contêineres reiniciados, no entanto, não têm a garantia de manter os dados em um volume *emptyDir* . Se você parar um grupo de contêineres, o volume *emptyDir* não será persistido.

## <a name="mount-an-emptydir-volume"></a>Montar um volume emptyDir

Para montar um volume emptyDir em uma instância de contêiner, você deve implantar usando um [modelo de Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Primeiro, preencha a matriz de `volumes` na seção `properties` do grupo de contêineres do modelo. Em seguida, para cada contêiner no grupo de contêineres no qual você deseja montar o volume *emptyDir* , preencha a matriz de `volumeMounts` na seção `properties` da definição do contêiner.

Por exemplo, o modelo do Resource Manager a seguir cria um grupo de contêineres que consiste em dois contêineres, cada um montando o volume *emptyDir* :

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Para ver um exemplo de implantação de instância de contêiner com um modelo de Azure Resource Manager, consulte [implantar grupos de vários contêineres em instâncias de contêiner do Azure](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Passos seguintes

Saiba como montar outros tipos de volume em instâncias de contêiner do Azure:

* [Montar uma partilha de ficheiros do Azure no Azure Container Instances](container-instances-volume-azure-files.md)
* [Montar um volume gitRepo em instâncias de contêiner do Azure](container-instances-volume-gitrepo.md)
* [Montar um volume secreto em instâncias de contentor do Azure](container-instances-volume-secret.md)