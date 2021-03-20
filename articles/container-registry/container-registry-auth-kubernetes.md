---
title: Autenticação do cluster Kubernetes
description: Saiba como fornecer um cluster Kubernetes com acesso a imagens no seu registo de contentores Azure, criando um segredo de puxar usando um diretor de serviço
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 05/28/2020
ms.openlocfilehash: fbf5dfd68b823b600b11cad3643e5d4004b85ff5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84309820"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>Retire imagens de um registo de contentores Azure para um cluster Kubernetes

Você pode usar um registo de contentores Azure como uma fonte de imagens de recipientes com qualquer cluster Kubernetes, incluindo clusters "locais" kubernetes como [minikube](https://minikube.sigs.k8s.io/) e [tipo](https://kind.sigs.k8s.io/). Este artigo mostra como criar um segredo de retirada de Kubernetes baseado num diretor de serviço Azure Ative. Em seguida, use o segredo para extrair imagens de um registo de contentores Azure numa implantação de Kubernetes.

> [!TIP]
> Se estiver a utilizar o [serviço gerido Azure Kubernetes,](../aks/intro-kubernetes.md)também pode [integrar o seu cluster](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) com um registo de contentores Azure alvo para puxar imagens. 

Este artigo pressupõe que já criou um registo privado de contentores Azure. Também precisa de ter um cluster Kubernetes a funcionar e acessível através da `kubectl` ferramenta da linha de comando.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

Se não guardar ou se lembrar da senha principal do serviço, pode repor-a com o comando [de reset credencial ad sp:][az-ad-sp-credential-reset]

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

Este comando devolve uma nova senha válida para o seu principal de serviço.

## <a name="create-an-image-pull-secret"></a>Criar um segredo de puxar a imagem

Kubernetes usa um *segredo de puxar imagem* para armazenar informações necessárias para autenticar o seu registo. Para criar o segredo de puxar para um registo de contentores Azure, você fornece o iD principal do serviço, senha e o URL de registo. 

Criar um segredo de imagem com o seguinte `kubectl` comando:

```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```
em que:

| Valor | Descrição |
| :--- | :--- |
| `secret-name` | Nome da imagem puxa segredo, por exemplo, *acr-secret* |
| `namespace` | Kubernetes namespace para colocar o segredo em <br/> Só é necessário se quiser colocar o segredo num espaço de nome que não seja o espaço de nome padrão |
| `container-registry-name` | Nome do seu registo de contentores Azure, por exemplo, *miotratria*<br/><br/>É `--docker-server` o nome totalmente qualificado do servidor de login do registo  |
| `service-principal-ID` | ID do principal de serviço que será usado pela Kubernetes para aceder ao seu registo |
| `service-principal-password` | Senha principal do serviço |

## <a name="use-the-image-pull-secret"></a>Use o segredo de puxar a imagem

Uma vez criado o segredo de retirada de imagem, pode usá-lo para criar cápsulas e implementações de Kubernetes. Forneça o nome do segredo `imagePullSecrets` no ficheiro de implantação. Por exemplo:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: myregistry.azurecr.io/my-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

No exemplo anterior, `my-awesome-app:v1` é o nome da imagem a retirar do registo do contentor Azure, e  `acr-secret` é o nome do segredo de puxar que criou para aceder ao registo. Quando implementa a cápsula, a Kubernetes retira automaticamente a imagem do seu registo, se ainda não estiver presente no cluster.


## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o trabalho com os principais serviços e o Registo de Contentores Azure, consulte [a autenticação do Registo de Contentores Azure com os principais serviços](container-registry-auth-service-principal.md)
* Saiba mais sobre a imagem a puxar segredos na documentação de [Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset