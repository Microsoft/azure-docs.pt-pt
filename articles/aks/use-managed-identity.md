---
title: Utilize identidades geridas no Serviço Azure Kubernetes
description: Saiba como utilizar identidades geridas no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 06/04/2020
ms.openlocfilehash: ae66c6a6fbfef2a6052a037e010ecdeb4256bfd8
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84456441"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Utilize identidades geridas no Serviço Azure Kubernetes

Atualmente, um cluster Azure Kubernetes Service (AKS) (especificamente, o provedor de nuvem Kubernetes) requer uma identidade para criar recursos adicionais como equilibradores de carga e discos geridos em Azure, esta identidade pode ser uma *identidade gerida* ou um *principal serviço.* Se utilizar um [principal de serviço,](kubernetes-service-principal.md)deve fornecer um ou a AKS cria um em seu nome. Se utilizar a identidade gerida, esta será criada automaticamente pela AKS. Os agrupamentos que utilizam os princípios de serviço acabam por chegar a um estado em que o diretor de serviço deve ser renovado para manter o cluster a funcionar. Gerir os diretores de serviços adiciona complexidade, e é por isso que é mais fácil usar identidades geridas em vez disso. Os mesmos requisitos de permissão aplicam-se tanto aos princípios de serviço como às identidades geridas.

*As identidades geridas* são essencialmente um invólucro em torno dos diretores de serviço, e tornam a sua gestão mais simples. A rotação credencial para MSI ocorre automaticamente a cada 46 dias de acordo com o padrão do Azure Ative Directory. Para saber mais, leia sobre [identidades geridas para recursos Azure.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

AKS cria duas identidades geridas:

- **Identidade gerida atribuída pelo sistema**: A identidade que o fornecedor de nuvem Kubernetes utiliza para criar recursos Azure em nome do utilizador, como um [equilibrador](load-balancer-standard.md) de carga ou [um endereço IP público](static-ip.md). O ciclo de vida da identidade atribuída ao sistema está ligado ao do cluster e só deve ser utilizado pelo provedor da nuvem. A identidade é eliminada quando o cluster é eliminado.

- **Identidade gerida atribuída pelo utilizador**: A identidade que é usada para autorização no cluster e qualquer outra coisa que queira controlar. Por exemplo, a identidade atribuída ao utilizador é utilizada para autorizar a AKS a utilizar registos de contentores Azure (ACRs) ou a autorizar o kubelet a obter metadados da Azure.

Os addons também autenticam usando uma identidade gerida. Para cada complemento, uma identidade gerida é criada pela AKS e dura a vida do addon.

## <a name="before-you-begin"></a>Antes de começar

Deve ter o seguinte recurso instalado:

- O Azure CLI, versão 2.2.0 ou mais tarde

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

> [!NOTE]
> Para criar e utilizar o seu próprio VNet, endereço IP estático ou disco Azure anexado onde os recursos estão fora do grupo de recursos MC_*, utilize o PrincipalID do cluster System Assigned Managed Identity para executar uma atribuição de funções. Para obter mais informações sobre a atribuição de funções, consulte [o delegado de acesso a outros recursos da Azure.](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)
>
> As autorizações concedidas ao cluster Identidade Gerida utilizada pelo fornecedor Azure Cloud podem demorar 60 minutos a preencher.

Finalmente, obtenha credenciais para aceder ao cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

O cluster será criado em poucos minutos. Em seguida, pode implementar as suas cargas de trabalho de aplicação para o novo cluster e interagir com ele, tal como fez com os clusters AKS baseados em serviços.

> [!IMPORTANT]
>
> - Os agrupamentos AKS com identidades geridas só podem ser ativados durante a criação do cluster.
> - Os clusters AKS existentes não podem ser atualizados ou atualizados para permitir identidades geridas.
