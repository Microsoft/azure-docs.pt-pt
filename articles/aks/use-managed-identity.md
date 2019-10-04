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
ms.openlocfilehash: a5717d8ee44e4d2e086a6e7bc1b7c3d0deb614c8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827551"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>Visualização – usar identidades gerenciadas no serviço kubernetes do Azure

Atualmente, os usuários devem fornecer uma entidade de serviço ou AKS cria uma em seu nome para que o cluster AKS (especificamente o provedor de nuvem kubernetes) crie recursos adicionais como balanceadores de carga e Managed disks no Azure. As entidades de serviço normalmente são criadas com uma data de expiração. Os clusters eventualmente atingirão um estado em que a entidade de serviço precisará ser renovada caso contrário, o cluster não funcionará. O gerenciamento de entidades de serviço adiciona complexidade. Identidades gerenciadas são essencialmente um wrapper em relação às entidades de serviço e tornam seu gerenciamento mais simples. Leia mais sobre [identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) artigo.

O AKS cria duas identidades gerenciadas uma identidade gerenciada atribuída pelo sistema e a outra identidade atribuída pelo usuário. Uma identidade gerenciada atribuída pelo sistema é usada pelo provedor de nuvem do kubernetes para criar recursos do Azure em nome do usuário. O ciclo de vida deste sistema atribuiu a identidade gerenciada está vinculado ao do cluster e é excluído quando o cluster é excluído. O AKS também cria uma identidade gerenciada atribuída pelo usuário que é usada no cluster para autorizar o AKS a acessar o ACRs, kubelet para obter metadados do Azure, etc.

Nesse período de visualização, uma entidade de serviço ainda é necessária. Ele será usado para autorização de Complementos, como monitoramento, nó virtual, política do Azure e roteamento de aplicativos http. Há trabalho contínuo para remover a dependência dos complementos no SPN e, eventualmente, o requisito de SPN no AKS será removido completamente.

> [!IMPORTANT]
> Os recursos de visualização do AKS são consentimento de autoatendimento. As visualizações são fornecidas "no estado em que se encontram" e "como disponíveis" e são excluídas dos contratos de nível de serviço e da garantia limitada. As visualizações do AKS são parcialmente cobertas pelo suporte ao cliente com base no melhor esforço. Dessa forma, esses recursos não são destinados ao uso em produção. Para obter outras incompatibilidades, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS](support-policies.md)
> * [Perguntas frequentes do suporte do Azure](faq.md)

## <a name="before-you-begin"></a>Antes de começar

Você deve ter o seguinte:

* Você também precisa do CLI do Azure versão 2.0.70 ou posterior e a extensão 0.4.14 AKs-Preview

## <a name="install-latest-aks-cli-preview-extension"></a>Instalar a última extensão de visualização da CLI do AKS

Você precisa da extensão **0.4.14 AKs-Preview** ou posterior.

```azurecli
az extension update --name aks-preview 
az extension list
```

> [!CAUTION]
> Quando você registra um recurso em uma assinatura, não é possível cancelar o registro desse recurso no momento. Depois de habilitar alguns recursos de visualização, os padrões podem ser usados para todos os clusters AKS, em seguida, criados na assinatura. Não habilite os recursos de visualização em assinaturas de produção. Use uma assinatura separada para testar recursos de visualização e coletar comentários.

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

Pode levar vários minutos para o status mostrar *registrado*. Você pode verificar o status do registro usando o comando [AZ Feature List] [AZ-Feature-List]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

Quando o estado for registrado, atualize o registro do provedor de recursos *Microsoft. ContainerService* usando o comando [AZ Provider registrar] [AZ-Provider-Register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identity"></a>Criar um cluster AKS com identidade gerenciada

Agora você pode criar um cluster AKS com identidades gerenciadas usando o comando da CLI a seguir
```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)
```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

## <a name="get-credentials-to-access-the-cluster"></a>Obter credenciais para acessar o cluster
```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Depois que o cluster for criado em alguns minutos, você poderá implantar suas cargas de trabalho de aplicativo e interagir com ela como você esteve com os clusters AKS baseados na entidade de serviço. 

> [!IMPORTANT]
> * Os clusters AKS com identidades gerenciadas só podem ser habilitados durante a criação do cluster
> * Os clusters AKS existentes não podem ser atualizados/atualizados para habilitar identidades gerenciadas