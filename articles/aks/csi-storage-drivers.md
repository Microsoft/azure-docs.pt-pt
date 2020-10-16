---
title: Ativar controladores da Interface de Armazenamento de Contentores (CSI) no Serviço Azure Kubernetes (AKS)
description: Saiba como ativar os controladores da Interface de Armazenamento de Contentores (CSI) para discos Azure e Ficheiros Azure num cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 2af4f9e2ea1dc0fcb8e5f40e0024297124292b49
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074826"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks-preview"></a>Ativar os controladores da Interface de Armazenamento de Contentores (CSI) para discos Azure e Ficheiros Azure no Serviço Azure Kubernetes (AKS) (pré-visualização)

A Interface de Armazenamento de Contentores (CSI) é uma norma para expor sistemas arbitrários de blocos e ficheiros a cargas de trabalho contentorizadas em Kubernetes. Ao adotar e utilizar o CSI, o Serviço Azure Kubernetes (AKS) pode escrever, implementar e iterar plug-ins para expor novos ou melhorar os sistemas de armazenamento existentes em Kubernetes sem ter de tocar no código base de Kubernetes e aguardar os seus ciclos de libertação.

O suporte do controlador de armazenamento CSI na AKS permite-lhe utilizar de forma nativa:
- [*Discos Azure*](azure-disk-csi.md), que podem ser usados para criar um recurso Kubernetes *DataDisk.* Os discos podem utilizar o Azure Premium Storage, apoiado por SSDs de alto desempenho, ou armazenamento padrão Azure, apoiado por HDDs regulares ou SSDs Standard. Para a maioria das cargas de trabalho de produção e desenvolvimento, utilize o Premium Storage. Os discos Azure são montados como *ReadWriteOnce,* por isso só estão disponíveis para uma única cápsula. Para volumes de armazenamento que podem ser acedidos simultaneamente por várias cápsulas, utilize ficheiros Azure.
- [*Ficheiros Azure*](azure-files-csi.md), que podem ser usados para montar uma partilha SMB 3.0 apoiada por uma conta de Armazenamento Azure para pods. Com os Ficheiros Azure, pode partilhar dados em vários nós e cápsulas. Os Ficheiros Azure podem utilizar o Azure Standard Storage apoiado por HDDs regulares ou armazenamento Azure Premium apoiado por SSDs de alto desempenho.

> [!IMPORTANT]
> A partir da versão 1.21 de Kubernetes, a Kubernetes utilizará apenas os controladores CSI e por padrão. Estes condutores são o futuro do suporte de armazenamento em Kubernetes.
>
> *Os condutores de árvores referem-se* aos atuais condutores de armazenamento que fazem parte do código de Kubernetes contra os novos condutores do CSI, que são plug-ins.

## <a name="limitations"></a>Limitações

- Esta funcionalidade só pode ser definida no tempo de criação do cluster.
- A versão mínima de Kubernetes menor que suporta os controladores CSI é v1.17.
- Durante a pré-visualização, a classe de armazenamento padrão continuará a ser a [mesma classe de armazenamento na árvore](concepts-storage.md#storage-classes). Após esta funcionalidade estar geralmente disponível, a classe de armazenamento padrão será a `managed-csi` classe de armazenamento e as aulas de armazenamento na árvore serão removidas.
- Durante a primeira fase de pré-visualização, apenas o Azure CLI é suportado.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-the-enableazurediskfilecsidriver-preview-feature"></a>Registar a `EnableAzureDiskFileCSIDriver` funcionalidade de pré-visualização

Para criar um cluster AKS que possa utilizar controladores CSI para discos Azure e Ficheiros Azure, tem de ativar a bandeira de `EnableAzureDiskFileCSIDriver` funcionalidades na sua subscrição.

Registe o `EnableAzureDiskFileCSIDriver` pavilhão de funcionalidades utilizando o comando [de registo de recursos az,][az-feature-register] como mostra o seguinte exemplo:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureDiskFileCSIDriver"
```

Demora alguns minutos para que o estado seja *apresentado.* Verifique o estado de registo utilizando o comando [da lista de recursos AZ:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureDiskFileCSIDriver')].{Name:name,State:properties.state}"
```

Quando estiver pronto, reaprovi o registo do fornecedor de recursos *Microsoft.ContainerService* utilizando o comando [de registo do fornecedor az:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Para criar um cluster AKS ou uma piscina de nó que possa utilizar os controladores de armazenamento CSI, você precisa da mais recente extensão Azure CLI *de pré-visualização aks.* Instale a extensão Azure CLI *de pré-visualização aks* utilizando o comando [de adicionar extensão az.][az-extension-add] Ou instale quaisquer atualizações disponíveis utilizando o comando [de atualização de extensão az.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 


## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>Criar um novo cluster que possa usar controladores de armazenamento CSI

Crie um novo cluster que possa utilizar controladores de armazenamento CSI para discos Azure e Ficheiros Azure utilizando os seguintes comandos CLI. Utilize a `--aks-custom-headers` bandeira para definir a `EnableAzureDiskFileCSIDriver` função.

Criar um grupo de recursos Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

Crie o cluster AKS com suporte para os controladores de armazenamento CSI:

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure -k 1.17.9 --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

Se pretender criar aglomerados em condutores de armazenamento de árvores em vez de controladores de armazenamento CSI, pode fazê-lo omitindo o `--aks-custom-headers` parâmetro personalizado.


Verifique quantos volumes baseados em disco Azure pode anexar a este nó executando:

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="next-steps"></a>Passos seguintes

- Para utilizar a unidade CSI para discos Azure, consulte [discos Azure use com controladores CSI](azure-disk-csi.md).
- Para utilizar a unidade CSI para ficheiros Azure, consulte [Use Ficheiros Azure com controladores CSI](azure-files-csi.md).
- Para obter mais informações sobre as melhores práticas de armazenamento, consulte [as melhores práticas de armazenamento e backups no Serviço Azure Kubernetes.][operator-best-practices-storage]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register&preserve-view=true
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true