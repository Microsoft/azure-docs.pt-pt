---
title: Use identidades geridas no Serviço Azure Kubernetes
description: Saiba como usar identidades geridas no Serviço Azure Kubernetes (AKS)
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 03/10/2019
ms.author: saudas
ms.openlocfilehash: 85efc6d9d203ca06c5f7566376993b4c13950788
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369959"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Use identidades geridas no Serviço Azure Kubernetes

Atualmente, um cluster azure Kubernetes Service (AKS) (especificamente, o fornecedor de nuvem Kubernetes) requer um *principal de serviço* para criar recursos adicionais como equilibradores de carga e discos geridos em Azure. Ou deve fornecer um diretor de serviço ou aks cria um em seu nome. Os diretores de serviço normalmente têm uma data de validade. Os agrupamentos acabam por chegar a um estado em que o diretor de serviço deve ser renovado para manter o cluster a funcionar. Gerir os diretores de serviços adiciona complexidade.

*As identidades geridas* são essencialmente um invólucro em torno dos diretores de serviço, e tornam a sua gestão mais simples. Para saber mais, leia sobre [identidades geridas para os recursos do Azure.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

AKS cria duas identidades geridas:

- **Identidade gerida atribuída pelo sistema**: A identidade que o fornecedor de nuvem Kubernetes utiliza para criar recursos Azure em nome do utilizador. O ciclo de vida da identidade atribuída ao sistema está ligado ao do cluster. A identidade é eliminada quando o cluster é eliminado.
- **Identidade gerida atribuída ao utilizador**: A identidade que é usada para autorização no cluster. Por exemplo, a identidade atribuída ao utilizador é usada para autorizar aks a utilizar registos de contentores Azure (ACRs), ou para autorizar o kubelet a obter metadados do Azure.

Os addons também autenticam usando uma identidade gerida. Para cada complemento, uma identidade gerida é criada pela AKS e dura a vida do addon. Para criar e utilizar o seu próprio VNet, endereço IP estático ou disco Azure anexado onde os recursos estão fora do grupo de recursos MC_*, utilize o PrincipalID do cluster para executar uma atribuição de funções. Para obter mais informações sobre a atribuição de funções, consulte o [acesso do Delegado a outros recursos Do IA.](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)

## <a name="before-you-begin"></a>Antes de começar

Deve ter o seguinte recurso instalado:

- O Azure CLI, versão 2.2.0 ou posterior

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

Uma criação de cluster bem sucedida usando identidades geridas contém esta informação de perfil principal do serviço:

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
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
