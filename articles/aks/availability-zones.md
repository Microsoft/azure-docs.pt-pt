---
title: Utilizar zonas de disponibilidade no Azure Kubernetes Service (AKS)
description: Saiba como criar um cluster que distribui nós em zonas de disponibilidade no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: iainfou
ms.openlocfilehash: 0f99386aa9eeb75a990507e383c32412fb39eceb
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840683"
---
# <a name="preview---create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Pré-visualizar - criar um cluster do Azure Kubernetes Service (AKS) que utiliza as zonas de disponibilidade

Um cluster do Azure Kubernetes Service (AKS) distribui os recursos, tais como os nós e o armazenamento em secções lógicas do Azure subjacente da infraestrutura de computação. Este modelo de implementação torna-se de que os nós executam em vários domínios de atualização e com falha separados num único datacenter do Azure. Clusters do AKS implementadas com este comportamento predefinido fornecerem um alto nível de disponibilidade para proteger contra uma falha de hardware ou planeada de um evento de manutenção.

Para fornecer um nível mais elevado de disponibilidade às suas aplicações, os clusters do AKS podem ser distribuídos por zonas de disponibilidade. Estas zonas são datacenters separados fisicamente dentro de uma determinada região. Quando os componentes de cluster são distribuídos em várias zonas, o seu cluster do AKS é capaz de tolerar uma falha nessas zonas. As aplicações e as operações de gestão continuam a estar disponível, mesmo que um datacenter inteiro tenha um problema.

Este artigo mostra-lhe como criar um cluster do AKS e distribuir os componentes do nó em zonas de disponibilidade. Esta funcionalidade encontra-se em pré-visualização.

> [!IMPORTANT]
> Funcionalidades de pré-visualização do AKS são self-service, participar. Eles são fornecidos para recolher comentários e bugs de nossa Comunidade. Em pré-visualização, esses recursos não se destinam a utilização de produção. Funcionalidades em pré-visualização pública enquadram-se em suporte "melhor esforço". Assistência das equipas de suporte técnico do AKS está disponível durante o horário do Pacífico fuso horário (PST) apenas. Para obter mais informações, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS][aks-support-policies]
> * [FAQ de suporte do Azure][aks-faq]

## <a name="before-you-begin"></a>Antes de começar

Precisa da versão 2.0.66 da CLI do Azure ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar a CLI do Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão CLI de pré-visualização do aks

Para criar clusters do AKS que utilizam as zonas de disponibilidade, terá do *pré-visualização do aks* CLI versão da extensão 0.4.1 ou superior. Instalar o *pré-visualização do aks* extensão da CLI do Azure com o [Adicionar extensão az][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] comando::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flags-for-your-subscription"></a>Sinalizadores de recurso de Registro para a sua subscrição

Para criar um cluster do AKS que as zonas de disponibilidade, ative primeiro alguns sinalizadores de recurso na sua subscrição. Clusters utilizam um conjunto de dimensionamento para gerir a implementação e configuração de nós do Kubernetes. O *padrão* SKU do Balanceador de carga do Azure também é necessário para fornecer resiliência para os componentes de rede para encaminhar o tráfego no seu cluster. Registe-se a *AvailabilityZonePreview*, *AKSAzureStandardLoadBalancer*, e *VMSSPreview* sinalizadores de recurso com o [o registro de recurso de az][az-feature-register] comando conforme mostrado no exemplo a seguir:

> [!CAUTION]
> Quando registra um recurso numa assinatura, não pode atualmente anular o registo essa funcionalidade. Depois de ativar a algumas funcionalidades de pré-visualização, as predefinições podem ser utilizadas para todos os clusters do AKS, em seguida, criados na subscrição. Não a ativar funcionalidades de pré-visualização em subscrições de produção. Utilize uma subscrição separada para testar as funcionalidades de pré-visualização e colher comentários baseados em.

```azurecli-interactive
az feature register --name AvailabilityZonePreview --namespace Microsoft.ContainerService
az feature register --name AKSAzureStandardLoadBalancer --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

Demora alguns minutos para que o estado a mostrar *registado*. Pode verificar o estado de registo com o [lista de funcionalidades de az][az-feature-list] comando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AvailabilityZonePreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registo do *containerservice* fornecedor de recursos com o [Registre-se fornecedor de az][az-provider-register] comando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations-and-region-availability"></a>Limitações e a disponibilidade de região

Clusters do AKS atualmente podem ser criadas com as zonas de disponibilidade nas seguintes regiões:

* EUA Leste 2
* Europa do Norte
* Sudeste Asiático
* Europa Ocidental
* EUA Oeste 2

As seguintes limitações aplicam-se ao criar um cluster do AKS com zonas de disponibilidade:

* Apenas pode ativar as zonas de disponibilidade quando o cluster ser criado.
* Não não possível atualizar as definições de zona de disponibilidade depois do cluster ser criado. Também não é possível atualizar um cluster de zona existente, a não ser de disponibilidade para utilizar zonas de disponibilidade.
* Não é possível desativar as zonas de disponibilidade para um cluster do AKS, depois de este ter sido criado.
* O tamanho do nó selecionado (SKU de VM) tem de estar disponível em todas as zonas de disponibilidade.
* Clusters com disponibilidade requerem zonas ativadas utilizam de balanceadores de carga Standard do Azure para distribuição em zonas.
* Tem de utilizar o Kubernetes versão 1.13.5 ou superior para implementar os balanceadores de carga Standard.

Clusters do AKS que utilizam as zonas de disponibilidade tem de utilizar o Azure load balancer *padrão* SKU. A predefinição *básica* SKU do Balanceador de carga do Azure não suporta a distribuição por zonas de disponibilidade. Para obter mais informações e as limitações do padrão de Balanceador de carga, veja [limitações de pré-visualização SKU padrão do Azure load balancer][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Limitações de discos do Azure

Volumes que utilizam o Azure managed disks não estão atualmente recursos zonais. Pods reagendadas numa zona diferente da sua zona original não é possível voltar a ligá seus discos anteriores. É recomendado executar cargas de trabalho sem monitorização de estado que não exigem a problemas de armazenamento persistente que possam surgir em zona.

Se tiver de executar cargas de trabalho com monitorização de estado, use taints e tolerations nas suas especificações de pod ao scheduler Kubernetes para criar os pods na mesma zona de como os discos. Em alternativa, utilize o armazenamento baseado em rede, tais como ficheiros do Azure que pode anexar a pods conforme eles estão agendados entre zonas.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Descrição geral das zonas de disponibilidade para o AKS clusters

As zonas de disponibilidade é uma oferta que protege os seus aplicativos e dados de falhas de datacenter de elevada disponibilidade. As zonas são localizações físicas únicas dentro de uma região do Azure. Cada zona é constituída por um ou mais datacenters equipados com energia, refrigeração e redes. Para garantir a resiliência, existe um mínimo de três zonas separadas em todas as regiões ativadas. A separação física das zonas de disponibilidade numa região protege as aplicações e dados de falhas de datacenter. Serviços com redundância de zona replicar os seus dados e aplicações em zonas de disponibilidade para proteger contra único pontos de falha.

Para obter mais informações, consulte [quais são as zonas de disponibilidade no Azure?][az-overview].

Clusters do AKS que são implementadas através de zonas de disponibilidade podem distribuir os nós em várias zonas numa única região. Por exemplo, um cluster no *E.U.A. Leste 2* região pode criar nós em todos os três zonas de disponibilidade na *E.U.A. Leste 2*. Esta distribuição de recursos de cluster do AKS melhora a disponibilidade do cluster forem resilientes a falhas de uma zona específica.

![Distribuição de nós do AKS em zonas de disponibilidade](media/availability-zones/aks-availability-zones.png)

Numa interrupção de zona, os nós podem ser reequilibrados manualmente ou utilizar o dimensionamento automático do cluster. Se uma única zona de ficar indisponível, seus aplicativos continuam a executar.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Criar um cluster do AKS em zonas de disponibilidade

Quando cria um cluster com o [criar az aks][az-aks-create] comando, o `--node-zones` parâmetro define que nós de agente de zonas são implementadas na. Os componentes de plano de controlo do AKS para o seu cluster também são distribuídos por zonas numa configuração de disponibilidade mais elevada quando cria um cluster, especificando o `--node-zones` parâmetro.

Se não definir quaisquer zonas para o conjunto de agente predefinido quando cria um cluster do AKS, os componentes de plano de controlo do AKS para o seu cluster não irão utilizar as zonas de disponibilidade. Pode adicionar conjuntos de nós adicionais (atualmente em pré-visualização no AKS) com o [adicionar az aks nodepool][az-aks-nodepool-add] comando e especifique `--node-zones` para esses novos nós de agente, no entanto os componentes do plano de controlo permanecem sem uma zona de disponibilidade reconhecimento. Não é possível alterar o reconhecimento de zona para um conjunto de nós ou o AKS controlar os componentes de plano depois de serem implementadas.

O exemplo seguinte cria um cluster do AKS com o nome *myAKSCluster* no grupo de recursos com o nome *myResourceGroup*. Um total de *3* são criados os nós - uma o agente na zona *1*, uma na *2*e, em seguida, um em *3*. Os componentes de plano de controlo do AKS também são distribuídos por várias zonas numa configuração de disponibilidade mais elevada, uma vez que estão definidos como parte do cluster Criar processo.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version 1.13.5 \
    --generate-ssh-keys \
    --enable-vmss \
    --load-balancer-sku standard \
    --node-count 3 \
    --node-zones 1 2 3
```

Demora alguns minutos para criar o cluster do AKS.

## <a name="verify-node-distribution-across-zones"></a>Verifique se a distribuição de nó em zonas

Quando o cluster estiver pronto, liste os nós de agente no conjunto de dimensionamento para ver quais zona de disponibilidade serem implementadas no.

Primeiro, obtenha as credenciais de cluster do AKS com o [az aks get-credentials][az-aks-get-credentials] comando:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Em seguida, utilize o [kubectl descrevem][kubectl-describe] command para listar os nós do cluster. Filtrar os *failure-domain.beta.kubernetes.io/zone* valor, conforme mostrado no exemplo a seguir:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

O resultado de exemplo seguinte mostra os três nós distribuídas na região especificada e zonas de disponibilidade, como *1 eualeste2* para a primeira zona de disponibilidade e *eualeste2-2* para o segundo zona de disponibilidade:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

À medida que adiciona nós adicionais a um conjunto de agente, a plataforma Azure distribui automaticamente as VMs subjacentes entre as zonas de disponibilidade especificado.

## <a name="next-steps"></a>Passos Seguintes

Este artigo detalhada de como criar um cluster do AKS que utiliza as zonas de disponibilidade. Para obter mais considerações em clusters de elevada disponibilidade, consulte [melhores práticas para a recuperação de desastre e continuidade de negócio no AKS][best-practices-bc-dr].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
