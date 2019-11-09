---
title: Usar identidades gerenciadas no serviço kubernetes do Azure
description: Saiba como usar identidades gerenciadas no serviço kubernetes do Azure (AKS)
services: container-service
author: saudas
manager: saudas
ms.service: container-service
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.openlocfilehash: 16a1f3b8f9ef036bcdc9af122243d313186f99f1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885326"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>Visualização – usar identidades gerenciadas no serviço kubernetes do Azure

Atualmente, um cluster AKS (serviço de kubernetes do Azure) (especificamente, o provedor de nuvem kubernetes) requer uma *entidade de serviço* para criar recursos adicionais, como balanceadores de carga e discos gerenciados no Azure. Você deve fornecer uma entidade de serviço ou AKS cria uma em seu nome. As entidades de serviço normalmente têm uma data de expiração. Os clusters eventualmente atingem um estado no qual a entidade de serviço deve ser renovada para manter o cluster funcionando. O gerenciamento de entidades de serviço adiciona complexidade.

*Identidades gerenciadas* são essencialmente um wrapper em relação às entidades de serviço e tornam seu gerenciamento mais simples. Para saber mais, leia sobre [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

O AKS cria duas identidades gerenciadas:

- **Identidade gerenciada atribuída pelo sistema**: a identidade que o provedor de nuvem kubernetes usa para criar recursos do Azure em nome do usuário. O ciclo de vida da identidade atribuída pelo sistema está vinculado ao do cluster. A identidade é excluída quando o cluster é excluído.
- **Identidade gerenciada atribuída pelo usuário**: a identidade que é usada para autorização no cluster. Por exemplo, a identidade atribuída pelo usuário é usada para autorizar o AKS a usar os ACRs (registros de controle de acesso) ou autorizar o kubelet a obter metadados do Azure.

Nesse período de visualização, uma entidade de serviço ainda é necessária. Ele é usado para autorização de Complementos, como monitoramento, nós virtuais, Azure Policy e roteamento de aplicativos HTTP. O trabalho está em andamento para remover a dependência de Complementos no SPN (nome da entidade de serviço). Eventualmente, o requisito de um SPN no AKS será removido completamente.

> [!IMPORTANT]
> Os recursos de visualização do AKS estão disponíveis em uma base de autoatendimento e aceitação. As visualizações são fornecidas "no estado em que se encontram" e "como disponíveis" e são excluídas dos contratos de nível de serviço e da garantia limitada. As visualizações do AKS são parcialmente cobertas pelo suporte ao cliente na base do melhor esforço. Dessa forma, esses recursos não são destinados ao uso em produção. Para obter mais informações, consulte os seguintes artigos de suporte:
>
> - [Políticas de suporte do AKS](support-policies.md)
> - [Perguntas frequentes do suporte do Azure](faq.md)

## <a name="before-you-begin"></a>Antes de começar

Você deve ter os seguintes recursos instalados:

- O CLI do Azure, versão 2.0.70 ou posterior
- A extensão 0.4.14 AKs-Preview

Para instalar a extensão AKs-preview 0.4.14 ou posterior, use os seguintes comandos de CLI do Azure:

```azurecli
az extension update --name aks-preview
az extension list
```

> [!CAUTION]
> Depois de registrar um recurso em uma assinatura, não é possível cancelar o registro desse recurso no momento. Quando você habilita alguns recursos de visualização, os padrões podem ser usados para todos os clusters AKS criados posteriormente na assinatura. Não habilite os recursos de visualização em assinaturas de produção. Em vez disso, use uma assinatura separada para testar recursos de visualização e coletar comentários.

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

Pode levar vários minutos para que o status seja exibido como **registrado**. Você pode verificar o status do registro usando o comando [AZ Feature List](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

Quando o status for exibido como registrado, atualize o registro do provedor de recursos de `Microsoft.ContainerService` usando o comando [AZ Provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identities"></a>Criar um cluster AKS com identidades gerenciadas

Agora você pode criar um cluster AKS com identidades gerenciadas usando os comandos da CLI a seguir.

Primeiro, crie um grupo de recursos do Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Em seguida, crie um cluster AKS:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

Por fim, obtenha as credenciais para acessar o cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

O cluster será criado em alguns minutos. Em seguida, você pode implantar suas cargas de trabalho de aplicativo no novo cluster e interagir com ela da mesma forma como você fez com clusters AKS com base na entidade de serviço.

> [!IMPORTANT]
>
> - Os clusters AKS com identidades gerenciadas podem ser habilitados somente durante a criação do cluster.
> - Os clusters AKS existentes não podem ser atualizados ou atualizados para habilitar identidades gerenciadas.
