---
title: Cluster privado do serviço kubernetes do Azure
description: Saiba como criar um cluster do AKS (serviço de kubernetes do Azure) privado
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 12/10/2019
ms.author: mlearned
ms.openlocfilehash: 8af0f998df2a92e51078a2e23806cca07ff08ca3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480089"
---
# <a name="public-preview---private-azure-kubernetes-service-cluster"></a>Visualização pública-cluster particular do serviço kubernetes do Azure

Em um cluster privado, o servidor de plano/API de controle terá endereços IP internos definidos em [RFC1918](https://tools.ietf.org/html/rfc1918).  Usando um cluster privado, você pode garantir que o tráfego de rede entre o servidor de API e os pools de nós permaneça apenas na rede privada.

A comunicação entre o plano de controle/servidor de API, que está em uma assinatura do Azure AKS e o pool de cluster/nó de clientes, que está em uma assinatura de cliente, pode se comunicar entre si por meio do [serviço de link privado][private-link-service] na VNET do servidor de API e um ponto de extremidade privado exposto na sub-rede do cluster AKs do cliente.

> [!IMPORTANT]
> Os recursos de visualização do AKS são consentimento de autoatendimento. As visualizações são fornecidas "no estado em que se encontram" e "como disponíveis" e são excluídas dos contratos de nível de serviço e da garantia limitada. As visualizações do AKS são parcialmente cobertas pelo suporte ao cliente com base no melhor esforço. Dessa forma, esses recursos não são destinados ao uso em produção. Para obter outras incompatibilidades, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS](support-policies.md)
> * [Perguntas frequentes do suporte do Azure](faq.md)

## <a name="before-you-begin"></a>Antes de começar

* Você precisa do CLI do Azure versão 2.0.77 ou posterior e a extensão 0.4.18 AKs-Preview

## <a name="current-supported-regions"></a>Regiões com suporte atual
* Oeste dos E.U.A.
* E.U.A. Oeste 2
* Este dos E.U.A. 2
* Canadá Central
* Europa do Norte
* Europa Ocidental
* Leste da Austrália

## <a name="install-latest-aks-cli-preview-extension"></a>Instalar a última extensão de visualização da CLI do AKS

Para usar clusters privados, você precisa da extensão da CLI do *AKs* versão 0.4.18 ou superior. Instale a extensão de CLI do Azure *de AKs-Preview* usando o comando [AZ Extension Add][az-extension-add] e, em seguida, verifique se há atualizações disponíveis usando o comando [AZ Extension Update][az-extension-update] ::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> Quando você registra um recurso em uma assinatura, não é possível cancelar o registro desse recurso no momento. Depois de habilitar alguns recursos de visualização, os padrões podem ser usados para todos os clusters AKS, em seguida, criados na assinatura. Não habilite os recursos de visualização em assinaturas de produção. Use uma assinatura separada para testar recursos de visualização e coletar comentários.

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

Pode levar vários minutos para o status mostrar *registrado*. Você pode verificar o status do registro usando o comando [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

Quando o estado for registrado, atualize o registro do provedor de recursos *Microsoft. ContainerService* usando o comando [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>Criar um cluster AKS privado

#### <a name="default-basic-networking"></a>Rede básica padrão 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Onde--Enable-Private-cluster é um sinalizador obrigatório para um cluster privado 

#### <a name="advanced-networking"></a>Rede avançada  

```azurecli-interactive
az aks create \ 
    --resource-group <private-cluster-resource-group>\ 
    --name <private-cluster-name> \ 
    --load-balancer-sku standard
    --enable-private-cluster 
    --network-plugin azure \ 
    --vnet-subnet-id <subnet-id> \ 
    --docker-bridge-address 172.17.0.1/16 \ 
    --dns-service-ip 10.2.0.10 \ 
    --service-cidr 10.2.0.0/24 \ 
```
Onde--Enable-Private-cluster é um sinalizador obrigatório para um cluster privado 

## <a name="steps-to-connect-to-the-private-cluster"></a>Etapas para se conectar ao cluster privado
O ponto de extremidade do servidor de API não tem nenhum endereço IP público. Consequentemente, os usuários precisarão criar uma máquina virtual do Azure em uma rede virtual e conectar-se ao servidor de API. As etapas em

* Obter credenciais para se conectar ao cluster

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```
* Criar uma VM na mesma VNET que o cluster AKS ou criar uma VM em uma VNET diferente e emparelhar essa VNET com a VNET do cluster AKS
* Se você criar uma VM em uma VNET diferente, precisará configurar um vínculo entre essa VNET e a zona de DNS privado
    * Vá para o grupo de recursos MC_ * no portal 
    * Clique na zona de DNS privado 
    * Selecione o link de rede virtual no painel esquerdo
    * Crie um novo link para adicionar a VNET da VM à zona de DNS privado *(leva alguns minutos para que o link da zona DNS fique disponível)*
* SSH na VM
* Instalar a ferramenta Kubectl e executar comandos do Kubectl

## <a name="dependencies"></a>Dependências  
* Somente LB Standard – sem suporte para o Load Balancer básico  

## <a name="limitations"></a>Limitações 
* As mesmas [limitações do serviço de vínculo privado do Azure][private-link-service] se aplicam a clusters privados, pontos de extremidade privados do Azure e pontos de extremidade de serviço de rede virtual não têm suporte no momento na mesma VNET
* Não há suporte para nós virtuais em um cluster privado para girar instâncias ACI particulares em uma VNET privada do Azure
* Não há suporte para a integração do Azure DevOps pronta para uso com clusters privados
* Se os clientes precisarem habilitar o ACR para trabalhar com AKS particulares, a VNET do ACR precisará ser emparelhada com a VNET do cluster do agente
* Não há suporte atual para Azure Dev Spaces
* Não há suporte para converter clusters AKS existentes em clusters privados  
* Excluir ou modificar o ponto de extremidade privado na sub-rede do cliente fará com que o cluster pare de funcionar 
* Atualmente, não há suporte para Azure Monitor para os dados dinâmicos de contêineres
* Traga seu próprio DNS não tem suporte no momento


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
