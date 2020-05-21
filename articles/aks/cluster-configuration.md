---
title: Configuração do cluster nos Serviços Azure Kubernetes (AKS)
description: Saiba como configurar um cluster no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fe5ce13d9db8f2bc2231f87de7e602e63d239bfa
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725151"
---
# <a name="configure-an-aks-cluster"></a>Configurar um cluster do AKS

Como parte da criação de um cluster AKS, poderá ter de personalizar a configuração do cluster de acordo com as suas necessidades. Este artigo introduz algumas opções para personalizar o seu cluster AKS.

## <a name="os-configuration-preview"></a>Configuração do OS (Pré-visualização)

A AKS suporta agora ubuntu 18.04 como o sistema operativo do nó (OS) na pré-visualização. Durante o período de pré-visualização, estão disponíveis ubuntu 16.04 e Ubuntu 18.04.

Deve ter os seguintes recursos instalados:

- O Azure CLI, versão 2.2.0 ou posterior
- A extensão de 0.4.35 de aks

Para instalar a extensão de pré-visualização de aks 0.4.35 ou posteriormente, utilize os seguintes comandos Azure CLI:

```azurecli
az extension add --name aks-preview
az extension list
```

Registe a `UseCustomizedUbuntuPreview` funcionalidade:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Pode levar vários minutos para o estado mostrar como **Registado**. Pode verificar o estado de registo utilizando o comando da [lista de características az:](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Quando o estado mostrar como registado, atualização do registo do fornecedor de `Microsoft.ContainerService` recursos utilizando o comando de registo do fornecedor [az:](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="new-clusters"></a>Novos aglomerados

Configure o cluster para utilizar Ubuntu 18.04 quando o cluster for criado. Utilize a `--aks-custom-headers` bandeira para definir o Ubuntu 18.04 como o SISTEMA padrão.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Se quiser criar um cluster Ubuntu 16.04 regular, pode fazê-lo omitindo a `--aks-custom-headers` etiqueta personalizada.

### <a name="existing-clusters"></a>Aglomerados existentes

Configure uma nova piscina de nó para usar Ubuntu 18.04. Use a `--aks-custom-headers` bandeira para definir o Ubuntu 18.04 como o sistema operativo padrão para essa piscina de nó.

```azure-cli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Se quiser criar um Ubuntu 16.04 piscinas regulares de nós, pode fazê-lo omitindo a `--aks-custom-headers` etiqueta personalizada.


## <a name="custom-resource-group-name"></a>Nome de grupo de recursos personalizados

Quando se implanta um cluster de Serviço Azure Kubernetes em Azure, é criado um segundo grupo de recursos para os nós dos trabalhadores. Por padrão, a AKS nomeará o grupo de recursos do `MC_resourcegroupname_clustername_location` nó, mas também pode fornecer o seu próprio nome.

Para especificar o nome do seu próprio grupo de recursos, instale a versão de extensão Azure CLI de pré-visualização de aks 0.3.2 ou posterior. Utilizando o Azure CLI, utilize o `--node-resource-group` parâmetro do comando para especificar um nome personalizado para o grupo de `az aks create` recursos. Se utilizar um modelo de Gestor de Recursos Azure para implantar um cluster AKS, pode definir o nome do grupo de recursos utilizando a `nodeResourceGroup` propriedade.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

O grupo de recursos secundários é automaticamente criado pelo fornecedor de recursos Azure na sua própria subscrição. Só é possível especificar o nome do grupo de recursos personalizados quando o cluster é criado. 

Enquanto trabalha com o grupo de recursos do nó, lembre-se que não pode:

- Especifique um grupo de recursos existente para o grupo de recursos do nó.
- Especifique uma subscrição diferente para o grupo de recursos do nó.
- Mude o nome do grupo de recursos do nó após a criação do cluster.
- Especifique os nomes dos recursos geridos dentro do grupo de recursos do nó.
- Modificar ou eliminar etiquetas criadas pelo Azure de recursos geridos dentro do grupo de recursos do nó.

## <a name="next-steps"></a>Próximos passos

- Aprenda a usar para aplicar atualizações de `Kured` segurança e [kernel aos nós linux](node-updates-kured.md) no seu cluster.
- Consulte [o Upgrade de um cluster azure Kubernetes Service (AKS)](upgrade-cluster.md) para aprender a atualizar o seu cluster para a versão mais recente da Kubernetes.
- Consulte a lista de [perguntas frequentes sobre aks](faq.md) para encontrar respostas a algumas perguntas comuns da AKS.