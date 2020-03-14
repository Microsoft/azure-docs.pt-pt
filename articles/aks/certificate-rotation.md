---
title: Certificados de rotação no Serviço Azure Kubernetes (AKS)
description: Aprenda a rodar os seus certificados num cluster do Serviço Azure Kubernetes (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: f299b13baf5811b92bdc2e40b027868617d7574c
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368524"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Certificados de rotação no Serviço Azure Kubernetes (AKS)

O Serviço Azure Kubernetes (AKS) utiliza certificados para autenticação com muitos dos seus componentes. Periodicamente, poderá ter de rodar esses certificados por razões de segurança ou política. Por exemplo, pode ter uma política para rodar todos os seus certificados a cada 90 dias.

Este artigo mostra-lhe como rodar os certificados no seu cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer que esteja a executar a versão Azure CLI 2.0.77 ou mais tarde. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>Certificados AKS, Autoridades de Certificados e Contas de Serviço

A AKS gera e utiliza os seguintes certificados, Autoridades de Certificados e Contas de Serviço:

* O servidor AKS API cria uma Autoridade de Certificados (CA) chamada Cluster CA.
* O servidor API tem um Cluster CA, que assina certificados para comunicação de ida do servidor API para kubelets.
* Cada kubelet também cria um Pedido de Assinatura de Certificado (CSR), assinado pelo Cluster CA, para comunicação do kubelet para o servidor API.
* A loja de valor-chave etcd tem um certificado assinado pelo Cluster CA para comunicação de etcd para o servidor API.
* A loja de valor-chave etcd cria um CA que assina certificados para autenticar e autorizar a replicação de dados entre réplicas etcd no cluster AKS.
* O agregador API utiliza o Cluster CA para emitir certificados de comunicação com outras APIs, tais como O Corretor de Serviçoaberto para o Azure. O agregador API também pode ter o seu próprio CA para a emissão desses certificados, mas atualmente utiliza o Cluster CA.
* Cada nó usa um símbolo de Conta de Serviço (SA), assinado pelo Cluster CA.
* O cliente `kubectl` tem um certificado de comunicação com o cluster AKS.

> [!NOTE]
> Os clusters AKS criados antes de março de 2019 têm certificados que expiram após dois anos. Qualquer cluster criado após março de 2019 ou qualquer cluster que tenha os seus certificados rodados tem certificados De Acluster CA que expiram após 30 anos. Todos os outros certificados expiram após dois anos. Para verificar quando o seu cluster foi criado, use `kubectl get nodes` para ver a *idade* das piscinas do seu nó.
> 
> Além disso, pode verificar a data de validade do certificado do seu cluster. Por exemplo, o seguinte comando apresenta os dados do certificado para o cluster *myAKSCluster.*
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d > my-cert.crt
> openssl x509 -in my-cert.crt -text
> ```

## <a name="rotate-your-cluster-certificates"></a>Rode os certificados de cluster

> [!WARNING]
> Rodar os seus certificados utilizando `az aks rotate-certs` pode causar até 30 minutos de tempo de inatividade para o seu cluster AKS.

Use [az aks obter credenciais][az-aks-get-credentials] para iniciar sessão no seu cluster AKS. Este comando também descarrega e configura o `kubectl` certificado de cliente na sua máquina local.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Utilize `az aks rotate-certs` para rodar todos os certificados, CAs e SAs no seu cluster.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Pode levar até 30 minutos para `az aks rotate-certs` completar. Se o comando falhar antes de ser concluído, utilize `az aks show` para verificar se o estado do cluster é *rotativo*do certificado . Se o cluster estiver em estado de falhas, repita `az aks rotate-certs` para rodar os seus certificados novamente.

Verifique se os certificados antigos já não são válidos através do funcionamento de um comando `kubectl`. Uma vez que ainda não atualizou os certificados utilizados pelo `kubectl`, verá um erro.  Por exemplo:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Atualize o certificado utilizado por `kubectl` executando `az aks get-credentials`.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Verifique se os certificados foram atualizados com um comando `kubectl`, que agora terá sucesso. Por exemplo:

```console
kubectl get no
```

> [!NOTE]
> Se tiver algum serviço que apareça em cima da AKS, como o [Azure Dev Spaces,][dev-spaces]poderá também ter de [atualizar certificados relacionados com esses serviços.][dev-spaces-rotate]

## <a name="next-steps"></a>Passos seguintes

Este artigo mostrou-lhe como rodar automaticamente os certificados do seu cluster, CAs e SAs. Pode ver [as melhores práticas para a segurança do cluster e upgrades no Serviço Azure Kubernetes (AKS)][aks-best-practices-security-upgrades] para obter mais informações sobre as melhores práticas de segurança da AKS.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
