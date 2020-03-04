---
title: Instale espaços Azure Dev na AKS & the client-side tooling
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Aprenda a instalar o Azure Dev Spaces num cluster AKS e instale a ferramenta do lado do cliente.
keywords: Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S
ms.openlocfilehash: de4f91e3e0e0653519bb48db5e3e8a116e24cc78
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252010"
---
# <a name="install-azure-dev-spaces-on-aks-and-the-client-side-tooling"></a>Instale espaços Azure Dev na AKS e na ferramenta do lado do cliente

Este artigo mostra-lhe várias formas de instalar o Azure Dev Spaces num cluster AKS, bem como instalar a ferramenta do lado do cliente.

## <a name="install-azure-dev-spaces-using-the-cli"></a>Instale espaços Azure Dev utilizando o CLI

Antes de poder instalar espaços Dev utilizando o CLI, precisa de:
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita][az-portal-create-account].
* [O Azure CLI instalado][install-cli].
* [Um aglomerado aks][create-aks-cli] numa [região apoiada.][supported-regions]

Utilize o comando `use-dev-spaces` para ativar os Espaços Dev no seu cluster AKS e seguir as instruções.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

O comando acima permite a Dev Spaces no cluster *myAKSCluster* no grupo *myResourceGroup* e cria um espaço de dev *padrão.*

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

O comando `use-dev-spaces` também instala o Azure Dev Spaces CLI.

## <a name="install-azure-dev-spaces-using-the-azure-portal"></a>Instale espaços Azure Dev utilizando o portal Azure

Antes de poder instalar espaços Dev utilizando o portal Azure, precisa de:
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita][az-portal-create-account].
* [Um aglomerado aks][create-aks-portal] numa [região apoiada.][supported-regions]

Para instalar os Espaços Azure Dev utilizando o portal Azure:
1. Inicie sessão no [portal do Azure][az-portal].
1. Navegue para o seu aglomerado AKS.
1. Clique em *Espaços Dev*.
1. Alterar *ativar espaços de dev* para *sim* e clicar em *Guardar*.

![Ativar espaços de dev no portal Azure](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

Instalar espaços Azure Dev utilizando o portal Azure **não** instala nenhuma ferramenta do lado do cliente para os Espaços Azure Dev.

## <a name="install-the-client-side-tooling"></a>Instale a ferramenta do lado do cliente

Você pode usar a ferramenta do lado do cliente Azure Dev Spaces para interagir com espaços de v num cluster AKS da sua máquina local. Existem várias formas de instalar a ferramenta do lado do cliente:

* No Código do [Estúdio Visual,][vscode]instale a [extensão Dos Espaços Azure Dev.][vscode-extension]
* No [Visual Studio 2019,][visual-studio]instale a carga de trabalho do Desenvolvimento Azure.
* No Visual Studio 2017, instale a carga de trabalho de Desenvolvimento Web e [ferramentas de estúdio visual para kubernetes.][visual-studio-k8s-tools]
* Descarregue e instale o [Windows,][cli-win] [Mac][cli-mac]ou [Linux][cli-linux] CLI.

## <a name="next-steps"></a>Passos seguintes

Saiba como o Azure Dev Spaces o ajuda a desenvolver aplicações mais complexas em vários recipientes e como pode simplificar o desenvolvimento colaborativo trabalhando com diferentes versões ou ramos do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Desenvolvimento de equipa em Espaços Azure Dev][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-development-qs]: ../quickstart-team-development.md

[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
