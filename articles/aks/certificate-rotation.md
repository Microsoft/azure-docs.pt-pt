---
title: Rotate certificados no Serviço Azure Kubernetes (AKS)
description: Saiba como rodar os seus certificados num cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 11/15/2019
ms.openlocfilehash: 90526b78e65c335f07a2a9d2d152b54b47233082
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88211040"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Rotate certificados no Serviço Azure Kubernetes (AKS)

O Serviço Azure Kubernetes (AKS) utiliza certificados para autenticação com muitos dos seus componentes. Periodicamente, poderá ser necessário rodar esses certificados por razões de segurança ou política. Por exemplo, pode ter uma política para rodar todos os seus certificados a cada 90 dias.

Este artigo mostra-lhe como rodar os certificados no seu cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer que esteja a executar a versão Azure CLI 2.0.77 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>Certificados AKS, Autoridades certificados e Contas de Serviço

A AKS gera e utiliza os seguintes certificados, Autoridades certificados e Contas de Serviço:

* O servidor AKS API cria uma Autoridade de Certificados (CA) chamada Cluster CA.
* O servidor API tem um Cluster CA, que assina certificados para comunicação unidirecional do servidor API a kubelets.
* Cada kubelet também cria um Pedido de Assinatura de Certificado (CSR), que é assinado pelo Cluster CA, para comunicação do kubelet para o servidor API.
* A loja de valores-chave etcd tem um certificado assinado pelo Cluster CA para comunicação de etcd para o servidor API.
* A loja de valores-chave etcd cria um CA que assina certificados para autenticar e autorizar a replicação de dados entre réplicas etcd no cluster AKS.
* O agregador API utiliza o Cluster CA para emitir certificados de comunicação com outras APIs. O agregador API também pode ter o seu próprio CA para a emissão desses certificados, mas atualmente utiliza o Cluster CA.
* Cada nó utiliza um token de Conta de Serviço (SA), que é assinado pelo Cluster CA.
* O `kubectl` cliente tem um certificado de comunicação com o cluster AKS.

> [!NOTE]
> Os clusters AKS criados antes de março de 2019 têm certificados que expiram ao fim de dois anos. Qualquer cluster criado após março de 2019 ou qualquer cluster que tenha os seus certificados rotados tem certificados Cluster CA que expiram após 30 anos. Todos os outros certificados expiram após dois anos. Para verificar quando o seu cluster foi criado, use `kubectl get nodes` para ver a *idade* das suas piscinas de nó.
> 
> Além disso, pode verificar a data de validade do certificado do seu cluster. Por exemplo, o seguinte comando Bash apresenta os dados do certificado para o cluster *myAKSCluster.*
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d | openssl x509 -text | grep -A2 Validity
> ```

## <a name="rotate-your-cluster-certificates"></a>Rode os certificados de cluster

> [!WARNING]
> A rotação dos certificados `az aks rotate-certs` pode causar até 30 minutos de tempo de paragem para o seu cluster AKS.

Use [credenciais az aks][az-aks-get-credentials] para iniciar sação no seu cluster AKS. Este comando também descarrega e configura o certificado de `kubectl` cliente na sua máquina local.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Use `az aks rotate-certs` para rodar todos os certificados, CAs e SAs no seu cluster.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Pode levar até 30 minutos `az aks rotate-certs` para completar. Se o comando falhar antes de concluir, utilize `az aks show` para verificar se o estado do cluster é *Rotação de Certificados*. Se o cluster estiver num estado falhado, volte `az aks rotate-certs` a repetir os certificados.

Verifique se os certificados antigos já não são válidos através da execução de um `kubectl` comando. Uma vez que não atualizou os certificados utilizados `kubectl` por, verá um erro.  Por exemplo:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Atualize o certificado utilizado `kubectl` por execução `az aks get-credentials` .

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Verifique se os certificados foram atualizados executando um `kubectl` comando, que agora será bem sucedido. Por exemplo:

```console
kubectl get no
```

> [!NOTE]
> Se tiver algum serviço que esteja em cima da AKS, como [a Azure Dev Spaces,][dev-spaces]poderá também necessitar de [atualizar certificados relacionados com esses serviços.][dev-spaces-rotate]

## <a name="next-steps"></a>Passos seguintes

Este artigo mostrou-lhe como rodar automaticamente os certificados do seu cluster, CAs e SAs. Você pode ver [as melhores práticas para segurança de cluster e upgrades no Azure Kubernetes Service (AKS)][aks-best-practices-security-upgrades] para mais informações sobre as melhores práticas de segurança da AKS.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: ../dev-spaces/index.yml
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
