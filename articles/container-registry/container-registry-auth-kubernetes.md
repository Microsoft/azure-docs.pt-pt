---
title: Autenticação do cluster Kubernetes
description: Aprenda a fornecer um cluster Kubernetes com acesso a imagens no seu registo de contentores Azure, criando um segredo de puxar usando um diretor de serviço
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 02/10/2020
ms.openlocfilehash: 0608ca0e0e53acf2f19910a7f1107dacf67d4e61
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154897"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>Puxe imagens de um registo de contentores Azure para um cluster Kubernetes

Você pode usar um registo de contentores Azure como fonte de imagens de contentores com qualquer cluster Kubernetes, incluindo aglomerados kubernetes "locais" como [minikube](https://minikube.sigs.k8s.io/) e [kind](https://kind.sigs.k8s.io/). Este artigo mostra como criar um Kubernetes puxar o segredo com base num diretor de serviço de Diretório Ativo Azure. Em seguida, use o segredo para retirar imagens de um registo de contentores Azure numa implantação de Kubernetes.

> [!TIP]
> Se estiver a utilizar o [Serviço Azure Kubernetes](../aks/intro-kubernetes.md)gerido, também pode [integrar o seu cluster](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) com um registo de contentores Azure alvo para puxões de imagem. 

Este artigo assume que já criou um registo privado de contentores Azure. Também precisa de ter um cluster Kubernetes a funcionar e acessível através da ferramenta de linha de comando `kubectl`.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

Se não guardar ou se lembrar da senha principal do serviço, pode redefini-la com o comando de [reset credencial az ad sp:][az-ad-sp-credential-reset]

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

Este comando devolve uma nova senha válida para o seu diretor de serviço.

## <a name="create-an-image-pull-secret"></a>Criar um segredo de imagem

Kubernetes usa um *segredo de imagem* para armazenar informações necessárias para autenticar o seu registo. Para criar o segredo de retirada para um registo de contentores Azure, fornece o ID principal do serviço, a palavra-passe e o URL do registo. 

Crie um segredo de imagem com o seguinte comando `kubectl`:

```console
kubectl create secret docker-registry <secret-name> \
  --namespace <namespace> \
  --docker-server=https://<container-registry-name>.azurecr.io \
  --docker-username=<service-principal-ID> \
  --docker-password=<service-principal-password>
```
Quando:

| Valor | Descrição |
| :--- | :--- |
| `secret-name` | Nome da imagem puxa segredo, por exemplo, *acr-secret* |
| `namespace` | Kubernetes espaço de nome para colocar o segredo em <br/> Só é necessário se quiser colocar o segredo num espaço de nome que não seja o espaço de nome padrão |
| `container-registry-name` | Nome do seu registo de contentores Azure |
| `service-principal-ID` | ID do diretor de serviço que será usado pela Kubernetes para aceder ao seu registo |
| `service-principal-password` | Senha principal de serviço |

## <a name="use-the-image-pull-secret"></a>Use a imagem puxar segredo

Uma vez criada a imagem, pode usá-la para criar cápsulas e implementações kubernetes. Forneça o nome do segredo sob `imagePullSecrets` no ficheiro de implantação. Por exemplo:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: your-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: your-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

No exemplo anterior, `your-awesome-app:v1` é o nome da imagem a retirar do registo de contentores Azure, e `acr-secret` é o nome do segredo de puxar que criou para aceder ao registo. Quando implanta a cápsula, os Kubernetes retiram automaticamente a imagem do seu registo, caso ainda não esteja presente no cluster.


## <a name="next-steps"></a>Passos seguintes

* Para mais informações sobre o trabalho com os principais de serviço e o Registo de Contentores Azure, consulte a autenticação do Registo de [Contentores azure com os principais diretores](container-registry-auth-service-principal.md) de serviço
* Saiba mais sobre a imagem puxar segredos na [documentação kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset