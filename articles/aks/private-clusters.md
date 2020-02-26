---
title: Criar um cluster privado de serviço Azure Kubernetes
description: Saiba como criar um cluster privado do Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: e59dccbcc7514f12e148bfb2f771593a53e85dc5
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594571"
---
# <a name="create-a-private-azure-kubernetes-service-cluster-preview"></a>Criar um cluster privado de serviço Azure Kubernetes (pré-visualização)

Num cluster privado, o plano de controlo ou servidor API tem endereços IP internos definidos no documento [RFC1918 - Atribuição de Endereços para Internets Privadas.](https://tools.ietf.org/html/rfc1918) Ao utilizar um cluster privado, pode garantir que o tráfego de rede entre o seu servidor API e as piscinas do nó permanece apenas na rede privada.

O plano de controlo ou servidor API está numa subscrição Azure Kubernetes Service (AKS) gerida pelo Azure. O cluster ou piscina de nó de um cliente está na subscrição do cliente. O servidor e o cluster ou piscina de nó podem comunicar entre si através do [serviço Azure Private Link][private-link-service] na rede virtual do servidor API e de um ponto final privado que está exposto na subnet do cluster AKS do cliente.

> [!IMPORTANT]
> As funcionalidades de pré-visualização AKS são self-service e são oferecidas numa base de opt-in. As pré-visualizações são fornecidas *como estão* *disponíveis* e estão excluídas do acordo de nível de serviço (SLA) e da garantia limitada. As pré-visualizações do AKS são parcialmente cobertas pelo apoio ao cliente numa base de *melhor esforço.* Portanto, as características não são destinadas ao uso da produção. Para mais informações, consulte os seguintes artigos de apoio:
>
> * [Políticas de apoio aks](support-policies.md)
> * [FaQ de suporte azure](faq.md)

## <a name="prerequisites"></a>Pré-requisitos

* A versão Azure CLI 2.0.77 ou posterior, e a versão de extensão de pré-visualização Azure CLI AKS 0.4.18

## <a name="currently-supported-regions"></a>Regiões atualmente apoiadas

* Leste da Austrália
* Austrália Sudeste
* Sul do Brasil
* Canadá Central
* Leste do Canadá
* Cenral EUA
* Ásia Leste
* E.U.A. Leste
* E.U.A. Leste 2
* LESTE DOS EUA 2 EUAP
* França Central
* Alemanha Norte
* Leste do Japão
* Oeste do Japão
* Coreia do Sul Central
* Sul da Coreia do Sul
* E.U.A. Centro-Norte
* Europa do Norte
* Europa do Norte
* E.U.A. Centro-Sul
* Sul do Reino Unido
* Europa Ocidental
* E.U.A. Oeste
* E.U.A.Oeste 2
* E.U.A. Leste 2


## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>Instale a mais recente extensão de pré-visualização Azure CLI AKS

Para utilizar clusters privados, necessita da versão de extensão de pré-visualização Azure CLI AKS 0.4.18 ou posterior. Instale a extensão de pré-visualização Azure CLI AKS utilizando o comando de adição de [extensão az][az-extension-add] e, em seguida, verifique se há quaisquer atualizações disponíveis utilizando o seguinte comando de atualização de [extensão az:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> Ao registar uma funcionalidade numa subscrição, não pode atualmente desregistar essa funcionalidade. Depois de ativar algumas funcionalidades de pré-visualização, pode utilizar as definições padrão para todos os clusters AKS que foram criados na subscrição. Não ative funcionalidades de pré-visualização nas subscrições de produção. Utilize uma subscrição separada para testar funcionalidades de pré-visualização e recolher feedback.

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

Pode levar vários minutos para que o estado de registo apresente como *Registado*. Pode verificar o estado utilizando o seguinte comando da [lista de características az:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

Quando o estado estiver registado, anote o registo do fornecedor de recursos *Microsoft.ContainerService* utilizando o seguinte comando de registo do [fornecedor az:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>Criar um cluster PRIVADO AKS

### <a name="default-basic-networking"></a>Rede básica padrão 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Onde *-enable-private-cluster* é uma bandeira obrigatória para um aglomerado privado. 

### <a name="advanced-networking"></a>Networking avançado  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Onde *-enable-private-cluster* é uma bandeira obrigatória para um aglomerado privado. 

> [!NOTE]
> Se a ponte Docker abordar o CIDR (172.17.0.1/16) entrar em conflito com a sub-rede CIDR, mude o endereço da ponte Docker adequadamente.

## <a name="connect-to-the-private-cluster"></a>Ligue-se ao cluster privado
O ponto final do servidor API não tem endereço IP público. Consequentemente, deve criar uma máquina virtual Azure (VM) numa rede virtual e ligar-se ao servidor API. Para isso, faça o seguinte:

1. Obtenha credenciais para se ligar ao cluster.

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```

1. Efetue um dos seguintes procedimentos:
   * Crie um VM na mesma rede virtual que o cluster AKS.  
   * Crie um VM numa rede virtual diferente e perselhe esta rede virtual com a rede virtual do cluster AKS.

     Se criar um VM numa rede virtual diferente, crie uma ligação entre esta rede virtual e a zona privada de DNS. Para tal:
    
     a. Vá ao grupo de recursos MC_* no portal Azure.  
     b. Selecione a zona privada de DNS.   
     c. No painel esquerdo, selecione o link de **rede Virtual.**  
     d. Crie um novo link para adicionar a rede virtual do VM à zona privada de DNS. Leva alguns minutos para que a ligação de zona DNS fique disponível.  
     e. Volte para o grupo de recursos MC_* no portal Azure.  
     f. No painel certo, selecione a rede virtual. O nome da rede virtual está na forma *aks-vnet-\** .  
     g. No painel esquerdo, selecione **Peerings**.  
     h. Selecione **Adicionar,** adicione a rede virtual do VM e, em seguida, crie o peering.  
     i. Vá à rede virtual onde tem o VM, selecione **Peerings,** selecione a rede virtual AKS e, em seguida, crie o peering. Se o endereço variar na rede virtual AKS e no choque de rede virtual do VM, o olhar falha. Para mais informações, consulte o peering da [rede Virtual.][virtual-network-peering]

1. Aceda ao VM via Secure Shell (SSH).
1. Instale a ferramenta Kubectl e execute os comandos Kubectl.


## <a name="dependencies"></a>Dependências  
* O serviço Private Link é suportado apenas no Standard Azure Load Balancer. O Equilíbrio básico de carga azure não é suportado.  
* Para utilizar um servidor DNS personalizado, implemente um servidor AD com DNS para encaminhar para este IP 168.63.129.16

## <a name="limitations"></a>Limitações 
* As Zonas de Disponibilidade são atualmente apenas suportadas para regiões leste dos EUA 2 e 2 do Oeste dos EUA
* As limitações do [serviço Azure Private Link][private-link-service] aplicam-se a clusters privados, pontos finais privados do Azure e pontos finais de serviço de rede virtual, que não são atualmente suportados na mesma rede virtual.
* Nenhum suporte para nós virtuais em um cluster privado para girar instâncias privadas de contentores Azure (ACI) em uma rede virtual azure privada
* Nenhum apoio à integração de Azure DevOps fora da caixa com clusters privados
* Para os clientes que precisam de permitir que o Registo de Contentores do Azure trabalhe com AKS privados, a rede virtual de registo de contentores deve ser espreitada com a rede virtual do cluster de agentes.
* Nenhum suporte atual para espaços Azure Dev
* Nenhum apoio para converter os aglomerados AKS existentes em clusters privados
* A apagamento ou modificação do ponto final privado na sub-rede do cliente fará com que o cluster deixe de funcionar. 
* O Monitor Azure para recipientes Live Data não é suportado atualmente.
* *Traga o seu próprio DNS* não é suportado atualmente.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md

