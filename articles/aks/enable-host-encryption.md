---
title: Ativar a encriptação baseada no anfitrião no Serviço Azure Kubernetes (AKS)
description: Saiba como configurar uma encriptação baseada no anfitrião num cluster Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 01/27/2021
ms.openlocfilehash: ac28c698a766f1f3febaff582038906f658d58dd
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071855"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Encriptação baseada no anfitrião no Serviço Azure Kubernetes (AKS) (pré-visualização)

Com encriptação baseada no anfitrião, os dados armazenados no anfitrião VM dos VM dos seus subms do seu agente AKS são encriptados em repouso e fluxos encriptados para o serviço de Armazenamento. Isto significa que os discos temporários são encriptados em repouso com teclas geridas pela plataforma. A cache de SO e discos de dados é encriptada em repouso com chaves geridas pela plataforma ou teclas geridas pelo cliente, dependendo do tipo de encriptação definido nesses discos. Por padrão, quando se utiliza AKS, os oss e os discos de dados são encriptados em repouso com as teclas geridas pela plataforma, o que significa que os caches para estes discos também são encriptados por padrão em repouso com teclas geridas pela plataforma.  Pode especificar as suas próprias teclas geridas seguindo [as suas próprias chaves (BYOK) com discos Azure no Serviço Azure Kubernetes](azure-disk-customer-managed-keys.md). A cache destes discos também será encriptada utilizando a chave que especifica neste passo.


## <a name="before-you-begin"></a>Antes de começar

Esta funcionalidade só pode ser definida no tempo de criação de cluster ou de criação de piscinas de nó.

> [!NOTE]
> A encriptação baseada no anfitrião está disponível nas [regiões do Azure][supported-regions] que suportam a encriptação lateral do servidor dos discos geridos pelo Azure e apenas com [tamanhos VM suportados específicos][supported-sizes].

### <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que tem a `aks-preview` extensão CLI v0.4.73 ou versão superior.

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Para criar um cluster AKS que tenha encriptação baseada no anfitrião, precisa da mais recente extensão CLI *de pré-visualização de aks.* Instale a extensão Azure CLI *de pré-visualização aks* utilizando o comando [de adicionar extensão az][az-extension-add] ou verifique se há atualizações disponíveis utilizando o comando de atualização de [extensão az:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Limitações

- Só pode ser ativado em novas piscinas de nós ou novos aglomerados.
- Só pode ser ativado nas [regiões do Azure][supported-regions] que suportam a encriptação do lado do servidor dos discos geridos pelo Azure e apenas com [tamanhos VM suportados específicos][supported-sizes].
- Requer um cluster AKS e uma piscina de nó com base em conjuntos de balança de máquina virtual (VMSS) como *tipo de conjunto VM*.

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>Use encriptação baseada em hospedeiros em novos clusters (pré-visualização)

Configure os nós do agente de cluster para usar encriptação baseada no hospedeiro quando o cluster é criado. 

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Se pretender criar clusters sem encriptação baseada no anfitrião, pode fazê-lo omitindo o `--enable-encryption-at-host` parâmetro.

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>Utilize encriptação baseada em hospedeiros em clusters existentes (pré-visualização)

Pode ativar a encriptação baseada no anfitrião nos clusters existentes adicionando um novo conjunto de nós ao seu cluster. Configure um novo conjunto de nós para usar a encriptação baseada no hospedeiro utilizando o `--enable-encryption-at-host` parâmetro.

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Se pretender criar novos grupos de nó sem a função de encriptação baseada no anfitrião, pode fazê-lo omitindo o `--enable-encryption-at-host` parâmetro.

## <a name="next-steps"></a>Passos seguintes

Rever [as melhores práticas para a segurança do cluster AKS][best-practices-security] Leia mais sobre [encriptação baseada no anfitrião.](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
