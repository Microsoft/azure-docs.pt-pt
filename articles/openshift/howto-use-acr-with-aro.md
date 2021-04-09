---
title: Use o registo do contentor Azure com o chapéu vermelho Azure OpenShift
description: Aprenda a puxar e executar um recipiente do Registo de Contentores Azure no seu cluster Azure Red Hat OpenShift.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 01/10/2021
ms.openlocfilehash: 651b73db084e8090f59faeffa9991c2ac468ca08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634418"
---
# <a name="use-azure-container-registry-with-azure-red-hat-openshift-aro"></a>Use o registo do contentor Azure com o chapéu vermelho Azure OpenShift (ARO)

O Registo de Contentores Azure (ACR) é um serviço de registo de contentores gerido que pode utilizar para armazenar imagens privadas de contentores Docker com capacidades empresariais, tais como geo-replicação. Para aceder ao ACR a partir de um cluster ARO, o cluster pode autenticar com ACR armazenando credenciais de login de Docker num segredo de Kubernetes.  Da mesma forma, um cluster ARO pode usar um imagePullSecret na especificação do pod para autenticar contra o registo ao puxar a imagem. Neste artigo, você vai aprender a configurar um registo de contentores Azure com um cluster Azure Red Hat OpenShift para armazenar e puxar imagens privadas do contentor Docker.

## <a name="prerequisites"></a>Pré-requisitos

Este guia pressupõe que tem um registo de contentores Azure existente. Caso contrário, utilize as instruções do portal Azure ou [do Azure CLI](../container-registry/container-registry-get-started-azure-cli.md) para criar um registo de contentores.

Este artigo também assume que você tem um cluster Azure Red Hat OpenShift e tem o `oc` CLI instalado. Caso contrário, siga as instruções no tutorial do [cluster Create ARO](tutorial-create-cluster.md).

## <a name="get-a-pull-secret"></a>Obter um segredo de puxar

Vai precisar de um segredo da ACR para aceder ao registo do seu agrupamento ARO.

Para obter as suas credenciais secretas, pode usar o portal Azure ou o Azure CLI.

Se utilizar o portal Azure, navegue para a sua instância ACR e selecione **Teclas de acesso**.  O seu `docker-username` nome é o nome do seu registo de contentores, use a palavra-passe ou a palavra-passe2 para `docker-password` .

![Chaves de acesso](./media/acr-access-keys.png)

Em vez disso, pode usar o CLI Azure para obter estas credenciais:
```azurecli
az acr credential show -n <your registry name>
```

## <a name="create-the-kubernetes-secret"></a>Crie o segredo de Kubernetes

Agora, vamos usar estas credenciais para criar um segredo de Kubernetes. Execute o seguinte comando com as suas credenciais ACR:

```
oc create secret docker-registry \
    --docker-server=<your registry name>.azurecr.io \
    --docker-username=<your registry name> \
    --docker-password=******** \
    --docker-email=unused \
    acr-secret
```

>[!NOTE]
>Este segredo será armazenado no atual OpenShift Project (Kubernetes Namespace) e só será referenciado por cápsulas criadas nesse Projeto.  Consulte este [documento](https://docs.openshift.com/container-platform/4.4/openshift_images/managing_images/using-image-pull-secrets.html) para obter mais instruções sobre a criação de um conjunto de segredo de retirada.

## <a name="create-a-pod-using-a-private-registry-image"></a>Criar uma cápsula usando uma imagem de registo privado

Agora que ligámos o seu cluster ARO ao seu ACR, vamos tirar uma imagem do seu ACR para criar uma cápsula.

Comece com um podSpec e especifique o segredo que criou como imagemPullSecret:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-world
spec:
  containers:
  - name: hello-world
    image: <your registry name>.azurecr.io/hello-world:v1
  imagePullSecrets:
  - name: acr-secret
```

Para testar que a sua cápsula está a funcionar, execute este comando e aguarde até que o estado esteja **em funcionamento:**

```bash
$ oc get pods --watch
NAME         READY   STATUS    RESTARTS   AGE
hello-world  1/1     Running   0          30s
```

## <a name="next-steps"></a>Passos seguintes

* [Azure Container Registry](../container-registry/container-registry-concepts.md)
* [Quickstart: Criar um registo privado de contentores utilizando o CLI Azure](../container-registry/container-registry-get-started-azure-cli.md)
