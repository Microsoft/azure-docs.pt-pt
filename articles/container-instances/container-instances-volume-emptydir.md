---
title: Monte volume dir vazio para grupo de contentores
description: Saiba como montar um volume vazio do Dir para partilhar dados entre os contentores de um grupo de contentores em Instâncias de Contentores De Azure
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 64a3c83008f163167528a5e5987fe2316942d5bc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77117751"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Monte um volume dir vazio em instâncias de contentores azure

Aprenda a montar um volume *vazio de Dir* para partilhar dados entre os contentores de um grupo de contentores em Instâncias de Contentores Azure. Utilize volumes *dir vazios* como caches efémeros para as suas cargas de trabalho contentorizadas.

> [!NOTE]
> A montagem de um volume *Dir vazio* está atualmente restrita aos recipientes Linux. Enquanto estamos a trabalhar para trazer todas as funcionalidades para os recipientes windows, pode encontrar as diferenças de plataforma atuais na [visão geral](container-instances-overview.md#linux-and-windows-containers).

## <a name="emptydir-volume"></a>volume vazio Dir

O volume *vazio do Dir* fornece um diretório writável acessível a cada recipiente num grupo de contentores. Os contentores do grupo podem ler e escrever os mesmos ficheiros no volume, e podem ser montados utilizando os mesmos ou diferentes caminhos em cada recipiente.

Alguns exemplos usam para um volume *dir vazio:*

* Espaço de risco
* Checkpoint durante tarefas de longa duração
* Armazenar dados recuperados por um contentor sidecar e servidos por um recipiente de aplicação

Os dados num volume *deDir vazio* são persistidos através de acidentes de contentores. No entanto, não é garantido que os recipientes reiniciados persistam os dados num volume *deDir vazio.* Se parar um grupo de contentores, o volume *vazio do Dir* não é persistente.

O tamanho máximo de um volume *DeDir vazio* linux é de 50 GB.

## <a name="mount-an-emptydir-volume"></a>Monte um volume dir vazio

Para montar um volume Dir vazio numa instância de contentores, pode ser implantado utilizando um modelo de Gestor de [Recursos Azure,](/azure/templates/microsoft.containerinstance/containergroups)um [ficheiro YAML](container-instances-reference-yaml.md)ou outros métodos programáticos para implantar um grupo de contentores.

Primeiro, povoe a `volumes` matriz na secção do grupo `properties` de contentores do ficheiro. Em seguida, para cada recipiente do grupo de contentores em que `volumeMounts` deseja `properties` montar o volume vazio *do Dir,* povoe a matriz na secção da definição do recipiente.

Por exemplo, o seguinte modelo de Gestor de Recursos cria um grupo de contentores composto por dois contentores, cada um dos quais monta o volume *vazio do Dir:*

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Para ver exemplos de implantação de grupos de contentores, consulte [A implantação de um grupo multi-contentores utilizando um modelo](container-instances-multi-container-group.md) de Gestor de Recursos e implante um grupo [multi-contentorusando de um ficheiro YAML](container-instances-multi-container-yaml.md).

## <a name="next-steps"></a>Passos seguintes

Saiba montar outros tipos de volume em instâncias de contentores azure:

* [Montar uma partilha de ficheiros do Azure no Azure Container Instances](container-instances-volume-azure-files.md)
* [Monte um volume gitRepo em instâncias de contentores azure](container-instances-volume-gitrepo.md)
* [Monte um volume secreto em instâncias de contentores azure](container-instances-volume-secret.md)