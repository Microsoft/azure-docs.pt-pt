---
title: Monte o volume vazio doRir para o grupo de contentores
description: Saiba como montar um volume deDir vazio para partilhar dados entre os contentores num grupo de contentores em Instâncias de Contentores Azure
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 64a3c83008f163167528a5e5987fe2316942d5bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "77117751"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Monte um volume deDir vazio em instâncias de contentores Azure

Saiba como montar um volume *deDir vazio* para partilhar dados entre os recipientes num grupo de contentores em Instâncias de Contentores Azure. Utilize volumes *vazios doDir* como caches efémeros para as suas cargas de trabalho contentorizadas.

> [!NOTE]
> A montagem de um volume *deDir vazio* está atualmente restrita aos recipientes Linux. Enquanto estamos a trabalhar para trazer todas as funcionalidades para os contentores do Windows, pode encontrar as diferenças da plataforma atuais na [visão geral.](container-instances-overview.md#linux-and-windows-containers)

## <a name="emptydir-volume"></a>volume vazioDir

O volume *EmptyDir* fornece um diretório writable acessível a cada contentor num grupo de contentores. Os recipientes do grupo podem ler e escrever os mesmos ficheiros no volume, podendo ser montados utilizando os mesmos ou diferentes caminhos em cada recipiente.

Alguns exemplos usam para um volume *deDir vazio:*

* Espaço de risco
* Controlo durante tarefas de longa duração
* Armazenar dados recuperados por um contentor sidecar e servidos por um recipiente de aplicação

Os dados num volume *deDir vazio* são persistidos através de colisões de contentores. No entanto, os recipientes que são reiniciados não são garantidos que persistam os dados num volume *deDir vazio.* Se parar um grupo de contentores, o volume *deDir vazio* não é persistido.

O tamanho máximo de um volume Linux *vazioDir* é de 50 GB.

## <a name="mount-an-emptydir-volume"></a>Monte um volume deDir vazio

Para montar um volume deDir vazio num caso de recipiente, pode ser implantado utilizando um [modelo de Gestor de Recursos Azure](/azure/templates/microsoft.containerinstance/containergroups), um ficheiro [YAML](container-instances-reference-yaml.md)ou outros métodos programáticos para implantar um grupo de contentores.

Primeiro, povoe a `volumes` matriz na secção do grupo de contentores do `properties` ficheiro. Em seguida, para cada recipiente no grupo de contentores no qual pretende montar o volume *vazio do Dir,* povoe a `volumeMounts` matriz na secção da `properties` definição do recipiente.

Por exemplo, o seguinte modelo de Gestor de Recursos cria um grupo de contentores composto por dois recipientes, cada um dos quais monta o volume *vazioDir:*

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Para ver exemplos de implantação de grupos de contentores, consulte [implementar um grupo multi-contentores utilizando um modelo de Gestor de Recursos](container-instances-multi-container-group.md) e implementar um grupo [multi-contentores utilizando um ficheiro YAML](container-instances-multi-container-yaml.md).

## <a name="next-steps"></a>Passos seguintes

Saiba como montar outros tipos de volume em Instâncias de Contentores Azure:

* [Montar uma partilha de ficheiros do Azure no Azure Container Instances](container-instances-volume-azure-files.md)
* [Monte um volume gitRepo em Instâncias de Contentores Azure](container-instances-volume-gitrepo.md)
* [Monte um volume secreto em Instâncias de Contentores Azure](container-instances-volume-secret.md)