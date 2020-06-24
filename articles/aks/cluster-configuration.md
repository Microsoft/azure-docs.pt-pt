---
title: Configuração do cluster nos Serviços Azure Kubernetes (AKS)
description: Saiba como configurar um cluster no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 06/20/2020
ms.openlocfilehash: 43aadd52f17367b488fcec086404caaba9158f33
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85205780"
---
# <a name="configure-an-aks-cluster"></a>Configurar um cluster do AKS

Como parte da criação de um cluster AKS, poderá necessitar de personalizar a sua configuração de cluster de acordo com as suas necessidades. Este artigo introduz algumas opções para personalizar o seu cluster AKS.

## <a name="os-configuration-preview"></a>Configuração do SO (Pré-visualização)

AKS suporta agora ubuntu 18.04 como o sistema operativo do nó (OS) na pré-visualização. Durante o período de pré-visualização, tanto Ubuntu 16.04 como Ubuntu 18.04 estão disponíveis.

Deve ter os seguintes recursos instalados:

- O Azure CLI, versão 2.2.0 ou mais tarde
- A extensão aks-preview 0.4.35

Para instalar a extensão aks-preview 0.4.35 ou posterior, utilize os seguintes comandos Azure CLI:

```azurecli
az extension add --name aks-preview
az extension list
```

Registar a `UseCustomizedUbuntuPreview` função:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Pode levar vários minutos para que o estado seja apresentado como **Registado**. Pode verificar o estado de registo utilizando o comando [da lista de funcionalidades AZ:](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Quando o estado aparecer como registado, reaprovida o registo do fornecedor de `Microsoft.ContainerService` recursos utilizando o comando de registo do fornecedor [az:](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="new-clusters"></a>Novos aglomerados

Configure o cluster para usar Ubuntu 18.04 quando o cluster é criado. Utilize a `--aks-custom-headers` bandeira para definir o Ubuntu 18.04 como o SISTEMA padrão.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Se quiser criar um cluster Ubuntu 16.04 regular, pode fazê-lo omitindo a `--aks-custom-headers` etiqueta personalizada.

### <a name="existing-clusters"></a>Aglomerados existentes

Configure uma nova piscina de nó para usar Ubuntu 18.04. Utilize a `--aks-custom-headers` bandeira para definir o Ubuntu 18.04 como o SISTEMA padrão para a piscina de nó.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Se quiser criar um nó de nó ubuntu 16.04 regular, pode fazê-lo omitindo a `--aks-custom-headers` etiqueta personalizada.

## <a name="generation-2-virtual-machines-preview"></a>Máquinas virtuais de geração 2 (Pré-visualização)
A Azure suporta [máquinas virtuais geração 2 (Gen2) (VMs)](../virtual-machines/windows/generation-2.md). A geração 2 VMs suporta características-chave que não são suportadas na geração 1 VMs (Gen1). Estas funcionalidades incluem memória aumentada, extensões de guarda de software Intel (Intel SGX) e memória persistente virtualizada (vPMEM).

A geração 2 VMs usa a nova arquitetura de arranque baseada na UEFI em vez da arquitetura baseada em BIOS usada pela geração 1 VMs.
Apenas SKUs e tamanhos específicos suportam Os VMs da Gen2. Verifique a [lista de tamanhos suportados,](../virtual-machines/windows/generation-2.md#generation-2-vm-sizes)para ver se o seu SKU suporta ou requer Gen2.

Além disso, nem todas as imagens VM suportam a Gen2, em VMs AKS Gen2 utilizarão a nova [imagem AKS Ubuntu 18.04](#os-configuration-preview). Esta imagem suporta todos os SKUs e tamanhos da Gen2.

Para utilizar os VMs da Gen2 durante a pré-visualização, você vai precisar:
- A `aks-preview` extensão CLI instalada.
- A `Gen2VMPreview` bandeira de características registada.

Registar a `Gen2VMPreview` função:

```azurecli
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```

Pode levar vários minutos para que o estado seja apresentado como **Registado**. Pode verificar o estado de registo utilizando o comando [da lista de funcionalidades AZ:](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```

Quando o estado aparecer como registado, reaprovida o registo do fornecedor de `Microsoft.ContainerService` recursos utilizando o comando de registo do fornecedor [az:](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Para instalar a extensão CLI de pré-visualização aks, utilize os seguintes comandos Azure CLI:

```azurecli
az extension add --name aks-preview
```

Para atualizar a extensão CLI de pré-visualização aks, utilize os seguintes comandos Azure CLI:

```azurecli
az extension update --name aks-preview
```

### <a name="new-clusters"></a>Novos aglomerados
Configure o cluster para usar Os VMs da Gen2 para o SKU selecionado quando o cluster é criado. Use a `--aks-custom-headers` bandeira para definir a Gen2 como a geração VM num novo aglomerado.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Se quiser criar um cluster regular utilizando VMs da Geração 1 (Gen1), pode fazê-lo omitindo a `--aks-custom-headers` etiqueta personalizada. Também pode optar por adicionar mais Gen1 ou Gen2 VMs conforme abaixo.

### <a name="existing-clusters"></a>Aglomerados existentes
Configure uma nova piscina de nós para usar Os VMs da Gen2. Use a `--aks-custom-headers` bandeira para definir a Gen2 como a geração VM para a piscina de nós.

```azure-cli
az aks nodepool add --name gen2 --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Se quiser criar piscinas regulares de nó gen1, pode fazê-lo omitindo a `--aks-custom-headers` etiqueta personalizada.

## <a name="custom-resource-group-name"></a>Nome do grupo de recursos personalizados

Quando você implanta um cluster de serviço Azure Kubernetes em Azure, um segundo grupo de recursos é criado para os nós operários. Por predefinição, a AKS nomeará o grupo de recursos de `MC_resourcegroupname_clustername_location` nó, mas também pode fornecer o seu próprio nome.

Para especificar o nome do seu próprio grupo de recursos, instale a versão de extensão Azure CLI de pré-visualização aks-preview 0.3.2 ou posterior. Utilizando o CLI Azure, utilize o `--node-resource-group` parâmetro do comando para `az aks create` especificar um nome personalizado para o grupo de recursos. Se utilizar um modelo de Gestor de Recursos Azure para implantar um cluster AKS, pode definir o nome do grupo de recursos utilizando a `nodeResourceGroup` propriedade.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

O grupo de recursos secundários é automaticamente criado pelo fornecedor de recursos Azure na sua própria subscrição. Só é possível especificar o nome do grupo de recursos personalizados quando o cluster for criado. 

Enquanto trabalha com o grupo de recursos de nó, lembre-se que não pode:

- Especifique um grupo de recursos existente para o grupo de recursos de nó.
- Especifique uma subscrição diferente para o grupo de recursos de nó.
- Mude o nome do grupo de recursos do nó após a criação do cluster.
- Especificar os nomes dos recursos geridos dentro do grupo de recursos de nó.
- Modifique ou elimine as tags criadas pelo Azure de recursos geridos dentro do grupo de recursos do nó.

## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar para aplicar atualizações de `Kured` segurança e [kernel aos nós Linux](node-updates-kured.md) no seu cluster.
- Consulte [o Upgrade de um cluster Azure Kubernetes Service (AKS)](upgrade-cluster.md) para aprender a atualizar o seu cluster para a versão mais recente do Kubernetes.
- Consulte a lista de [perguntas frequentes sobre aKS](faq.md) para encontrar respostas para algumas perguntas comuns da AKS.
