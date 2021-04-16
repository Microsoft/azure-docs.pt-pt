---
title: Ativar os espaços Azure Dev em AKS & instalar as ferramentas do lado do cliente
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Saiba como ativar os Espaços Azure Dev num cluster AKS e instale as ferramentas do lado do cliente.
ms.custom: devx-track-azurecli
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s
ms.openlocfilehash: 079a9e1b28b315457ac20d3aa9e7d29ce28fa077
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505365"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Ativar os espaços Azure Dev num cluster AKS e instalar as ferramentas do lado do cliente

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Este artigo mostra-lhe várias formas de ativar a Azure Dev Spaces num cluster AKS, bem como instalar as ferramentas do lado do cliente.

## <a name="enable-azure-dev-spaces-using-the-azure-cli"></a>Ativar espaços Azure Dev utilizando o Azure CLI

Antes de poder ativar os Espaços Dev utilizando o CLI, precisa de:
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode criar uma [conta gratuita.][az-portal-create-account]
* [O Azure CLI instalado][install-cli].
* [Um aglomerado AKS][create-aks-cli] numa [região apoiada.][supported-regions]

Utilize o `use-dev-spaces` comando para ativar os Espaços Dev no seu cluster AKS e siga as indicações.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

O comando acima permite espaços Dev no cluster *myAKSCluster* no grupo *myResourceGroup* e cria um espaço dev *padrão.*

```console
'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

O `use-dev-spaces` comando também instala o Azure Dev Spaces CLI.

## <a name="install-the-client-side-tools"></a>Instale as ferramentas do lado do cliente

Você pode usar as ferramentas do lado do cliente Azure Dev Spaces para interagir com espaços dev em um cluster AKS da sua máquina local. Existem várias formas de instalar as ferramentas do lado do cliente:

* No [Código do Estúdio Visual,][vscode]instale a [extensão Azure Dev Spaces][vscode-extension].
* No [Visual Studio 2019,][visual-studio]instale a carga de trabalho do Azure Development.
* Faça o download e instale o [Windows][cli-win], [Mac][cli-mac]ou [Linux][cli-linux] CLI.

## <a name="remove-azure-dev-spaces-using-the-azure-cli"></a>Remova espaços Azure Dev usando o Azure CLI

Para remover os espaços Azure Dev do seu cluster AKS, utilize o `azds remove` comando.

```azurecli
azds remove -g MyResourceGroup -n MyAKS
```

A saída abaixo mostra a remoção dos espaços Azure Dev do cluster *MyAKS.*

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

Quaisquer espaços de nome que tenha criado com a Azure Dev Spaces permanecerão juntamente com as suas cargas de trabalho, mas novas cargas de trabalho nesses espaços de nome não serão instrumentadas com Azure Dev Spaces. Além disso, se reiniciar quaisquer cápsulas existentes instrumentadas com espaços Azure Dev, poderá ver erros. Estas cápsulas devem ser redistribuídas sem a ferramenta Azure Dev Spaces. Para remover totalmente os Espaços Azure Dev do seu cluster, elimine todas as cápsulas em todos os espaços de nome onde o Azure Dev Spaces estava ativado.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o funcionamento da Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Como funciona o Azure Dev Spaces](../how-dev-spaces-works.md)

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[install-cli]: /cli/azure/install-azure-cli
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
