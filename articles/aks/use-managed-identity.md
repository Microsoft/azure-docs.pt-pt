---
title: Use identidades geridas no Serviço Azure Kubernetes
description: Saiba como usar identidades geridas no Serviço Azure Kubernetes (AKS)
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.openlocfilehash: 6d00fd72c338fc101420bf78b5608516715d44ad
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77592973"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>Pré-visualização - Utilize identidades geridas no Serviço Azure Kubernetes

Atualmente, um cluster azure Kubernetes Service (AKS) (especificamente, o fornecedor de nuvem Kubernetes) requer um *principal de serviço* para criar recursos adicionais como equilibradores de carga e discos geridos em Azure. Ou deve fornecer um diretor de serviço ou aks cria um em seu nome. Os diretores de serviço normalmente têm uma data de validade. Os agrupamentos acabam por chegar a um estado em que o diretor de serviço deve ser renovado para manter o cluster a funcionar. Gerir os diretores de serviços adiciona complexidade.

*As identidades geridas* são essencialmente um invólucro em torno dos diretores de serviço, e tornam a sua gestão mais simples. Para saber mais, leia sobre [identidades geridas para os recursos do Azure.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

AKS cria duas identidades geridas:

- **Identidade gerida atribuída pelo sistema**: A identidade que o fornecedor de nuvem Kubernetes utiliza para criar recursos Azure em nome do utilizador. O ciclo de vida da identidade atribuída ao sistema está ligado ao do cluster. A identidade é eliminada quando o cluster é eliminado.
- **Identidade gerida atribuída ao utilizador**: A identidade que é usada para autorização no cluster. Por exemplo, a identidade atribuída ao utilizador é usada para autorizar o AKS a utilizar registos de controlo de acesso (ACRs), ou para autorizar o kubelet a obter metadados do Azure.

Neste período de pré-visualização, ainda é necessário um diretor de serviço. É usado para autorização de complementos como monitorização, nós virtuais, Política Azure e encaminhamento de aplicações HTTP. Estão em curso trabalhos para eliminar a dependência de complementos no nome principal do serviço (SPN). Eventualmente, a exigência de um SPN em AKS será completamente removida.

> [!IMPORTANT]
> As funcionalidades de pré-visualização AKS estão disponíveis numa base de autosserviço, opt-in. As pré-visualizações são fornecidas "as-is" e "conforme disponível", e estão excluídas dos Acordos de Nível de Serviço e da garantia limitada. As pré-visualizações do AKS são parcialmente cobertas pelo apoio ao cliente na base do melhor esforço. Como tal, estas características não se destinam à utilização da produção. Para mais informações, consulte os seguintes artigos de apoio:
>
> - [Políticas de apoio aks](support-policies.md)
> - [FaQ de suporte azure](faq.md)

## <a name="before-you-begin"></a>Antes de começar

Deve ter os seguintes recursos instalados:

- O Azure CLI, versão 2.0.70 ou mais tarde
- A extensão de 0.4.14 das aks

Para instalar a extensão de pré-visualização de aks 0.4.14 ou posteriormente, utilize os seguintes comandos Azure CLI:

```azurecli
az extension add --name aks-preview
az extension list
```

> [!CAUTION]
> Depois de registar uma funcionalidade numa subscrição, não pode atualmente desregistar essa funcionalidade. Quando ativa algumas funcionalidades de pré-visualização, podem ser utilizadas predefinições para todos os clusters AKS criados posteriormente na subscrição. Não ative funcionalidades de pré-visualização nas subscrições de produção. Em vez disso, utilize uma subscrição separada para testar funcionalidades de pré-visualização e recolher feedback.

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

Pode levar vários minutos para o estado mostrar como **Registado**. Pode verificar o estado de registo utilizando o comando da [lista de características az:](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

Quando o estado mostrar como registado, atualização do registo do fornecedor de recursos `Microsoft.ContainerService` utilizando o comando de registo do [fornecedor az:](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identities"></a>Criar um cluster AKS com identidades geridas

Agora pode criar um cluster AKS com identidades geridas utilizando os seguintes comandos CLI.

Primeiro, criar um grupo de recursos Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Em seguida, criar um cluster AKS:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

Finalmente, obtenha credenciais para aceder ao cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

O aglomerado será criado em poucos minutos. Em seguida, pode implementar as suas cargas de trabalho de aplicação para o novo cluster e interagir com ele tal como fez com clusters AKS baseados em serviços.

> [!IMPORTANT]
>
> - Os aglomerados AKS com identidades geridas só podem ser ativados durante a criação do cluster.
> - Os clusters AKS existentes não podem ser atualizados ou atualizados para permitir identidades geridas.
