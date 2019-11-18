---
title: Girar certificados no serviço kubernetes do Azure (AKS)
description: Saiba como girar seus certificados em um cluster do AKS (serviço kubernetes do Azure).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: 00d8546cb20d12c5f1a94bdcababa04a77c73133
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134414"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Girar certificados no serviço kubernetes do Azure (AKS)

O AKS (serviço kubernetes do Azure) usa certificados para autenticação com muitos de seus componentes. Periodicamente, talvez seja necessário girar esses certificados por motivos de segurança ou política. Por exemplo, você pode ter uma política para girar todos os seus certificados a cada 90 dias.

Este artigo mostra como girar os certificados em seu cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer que você esteja executando o CLI do Azure versão 2.0.76 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].


### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI do AKS-Preview

Para usar esse recurso, você precisa da extensão da CLI do *AKs* versão 0.4.21 ou superior. Instale a extensão de CLI do Azure *de AKs-Preview* usando o comando [AZ Extension Add][az-extension-add] e, em seguida, verifique se há atualizações disponíveis usando o comando [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>Certificados AKS, autoridades de certificação e contas de serviço

O AKS gera e usa os seguintes certificados, autoridades de certificação e contas de serviço:

* O servidor de API AKS cria uma autoridade de certificação (CA) chamada de AC do cluster.
* O servidor de API tem uma AC de cluster, que assina certificados para uma comunicação unidirecional do servidor de API para kubelets.
* Cada kubelet também cria uma solicitação de assinatura de certificado (CSR), que é assinada pela AC do cluster, para a comunicação do kubelet com o servidor de API.
* O repositório de valor de chave etcd tem um certificado assinado pela AC do cluster para comunicação do etcd com o servidor de API.
* O armazenamento de valor de chave etcd cria uma autoridade de certificação que assina certificados para autenticar e autorizar a replicação de dados entre réplicas do etcd no cluster AKS.
* O agregador de API usa a AC do cluster para emitir certificados para comunicação com outras APIs, como abrir Service Broker para o Azure. O agregador de API também pode ter sua própria AC para emitir esses certificados, mas atualmente usa a AC do cluster.
* Cada nó usa um token de conta de serviço (SA), que é assinado pela AC do cluster.
* O cliente `kubectl` tem um certificado para se comunicar com o cluster AKS.

> [!NOTE]
> Os clusters AKS criados antes de março de 2019 têm certificados que expiram após dois anos. Qualquer cluster criado após 2019 de março ou qualquer cluster que tenha seus certificados girados tem certificados que expiram após 30 anos.

## <a name="rotate-your-cluster-certificates"></a>Girar seus certificados de cluster

> [!WARNING]
> Girar seus certificados usando `az aks rotate-certs` pode causar até 30 minutos de inatividade para o cluster AKS.

Use [AZ AKs Get-Credentials][az-aks-get-credentials] para entrar no seu cluster do AKS. Esse comando também baixa e configura o `kubectl` certificado de cliente no computador local.

```console
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Use `az aks rotate-certs` para girar todos os certificados, CAs e SAs no cluster.

```console
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Pode levar até 30 minutos para que `az aks rotate-certs` seja concluída. Se o comando falhar antes de concluir, use `az aks show` para verificar se o status do cluster é a *rotação do certificado*. Se o cluster estiver em um estado de falha, execute novamente `az aks rotate-certs` para girar os certificados novamente.

Verifique se os certificados antigos não são mais válidos executando um comando `kubectl`. Como você não atualizou os certificados usados pelo `kubectl`, verá um erro.  Por exemplo:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Atualize o certificado usado pelo `kubectl` executando `az aks get-credentials`.

```console
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Verifique se os certificados foram atualizados executando um comando `kubectl`, que agora terá sucesso. Por exemplo:

```console
kubectl get no
```

## <a name="next-steps"></a>Passos seguintes

Este artigo mostrou como girar automaticamente os certificados, as CAs e as SAs do seu cluster. Você pode ver [as práticas recomendadas para segurança de cluster e atualizações no serviço de kubernetes do Azure (AKs)][aks-best-practices-security-upgrades] para obter mais informações sobre as práticas recomendadas de segurança do AKS.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
