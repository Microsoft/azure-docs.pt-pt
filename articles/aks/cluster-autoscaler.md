---
title: Utilizar o dimensionamento automático de cluster no Azure Kubernetes Service (AKS)
description: Saiba como utilizar o dimensionamento automático de cluster para dimensionar automaticamente o seu cluster para atender às necessidades de aplicações num cluster do Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/29/2019
ms.author: iainfou
ms.openlocfilehash: 1ab31662b55f89b23d6aba8ebe4394d1e78092dc
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437531"
---
# <a name="preview---automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Pré-visualizar - Dimensionar automaticamente um cluster para atender às necessidades de aplicações no Azure Kubernetes Service (AKS)

Para se manter atualizado com pedidos de aplicações no Azure Kubernetes Service (AKS), terá de ajustar o número de nós que executam cargas de trabalho. O componente de dimensionamento automático do cluster pode ver para pods no seu cluster que não é possível agendar devido a restrições de recursos. Quando forem detetados problemas, aumenta o número de nós para satisfazer a procura da aplicação. Nós também regularmente são verificados relativamente à falta de execução pods, com o número de nós, em seguida, diminuída conforme necessário. Essa capacidade de dimensionar automaticamente ou reduzir verticalmente o número de nós no cluster do AKS permite-lhe executar um cluster de eficiente e econômico.

Este artigo mostra como ativar e gerir o dimensionamento automático de cluster num cluster do AKS.

> [!IMPORTANT]
> Funcionalidades de pré-visualização do AKS são self-service e participar. Pré-visualizações são fornecidas para recolher comentários e bugs de nossa Comunidade. No entanto, não são suportados pelo suporte técnico do Azure. Se cria um cluster ou adicionar esses recursos em clusters existentes, esse cluster não é suportado até que a funcionalidade não se encontra em pré-visualização e é formado para disponibilidade geral (GA).
>
> Se tiver problemas com funcionalidades de pré-visualização [abra um problema no repositório GitHub do AKS] [ aks-github] com o nome da funcionalidade de pré-visualização no título do bug.

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer a execução da versão 2.0.55 da CLI do Azure ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão CLI de pré-visualização do aks

Clusters do AKS que suportam o dimensionamento automático do cluster tem de utilizar os conjuntos de dimensionamento de máquinas virtuais e executar a versão do Kubernetes *1.12.4* ou posterior. Este suporte de conjunto de dimensionamento está em pré-visualização. Para optar ativamente por participar no e criar clusters que utilizam conjuntos de dimensionamento, instale primeiro o *pré-visualização do aks* extensão da CLI do Azure com o [Adicionar extensão az] [ az-extension-add] de comando, conforme mostrado na seguinte exemplo:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Se tiver instalado anteriormente a *pré-visualização do aks* extensão, instale qualquer disponível atualizações com o `az extension update --name aks-preview` comando.

### <a name="register-scale-set-feature-provider"></a>Registar o fornecedor de recursos de conjunto de dimensionamento

Para criar um AKS que utiliza o dimensionamento conjuntos, também tem de ativar um sinalizador de funcionalidade na sua subscrição. Para registar o *VMSSPreview* sinalizador de funcionalidade, utilize o [Registre-se de funcionalidade de az] [ az-feature-register] comando conforme mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

Demora alguns minutos para que o estado a mostrar *registado*. Pode verificar o estado de registo com o [lista de funcionalidades de az] [ az-feature-list] comando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registo do *containerservice* fornecedor de recursos com o [Registre-se fornecedor de az] [ az-provider-register] comando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="about-the-cluster-autoscaler"></a>Sobre o dimensionamento automático de cluster

Para ajustar-se às mudanças na procura de aplicativo, como o entre o dia de trabalho e a noite ou durante um fim de semana, clusters, muitas vezes, precisam de uma forma para dimensionar automaticamente. Clusters do AKS podem ser dimensionados em uma das seguintes formas:

* O **dimensionamento automático de clusters** supervisiona pods que não podem ser agendadas em nós devido a restrições de recursos. Os aumentos de automaticamente, em seguida, de cluster o número de nós.
* O **dimensionamento automático de horizontal de pods** utiliza o servidor de métricas num cluster do Kubernetes para monitorizar a procura de recursos de pods. Se precisar de mais recursos de um serviço, o número de pods automaticamente é aumentado para satisfazer a procura.

![O dimensionamento automático de cluster e o dimensionamento automático horizontal de pods, muitas vezes, funcionam em conjunto para suportar as necessidades de aplicação necessária](media/autoscaler/cluster-autoscaler.png)

O dimensionamento automático de horizontal de pods e o dimensionamento automático de cluster também, em seguida, podem diminuir o número de pods e nós conforme necessário. O dimensionamento automático de cluster diminui o número de nós quando foi a capacidade não utilizada por um período de tempo. Pods num nó será removido o dimensionamento automático de cluster com segurança estão agendadas em outro lugar no cluster. O dimensionamento automático de cluster pode ser não é possível reduzir verticalmente se pods não é possível mover, tal como nas seguintes situações:

* Um pod diretamente criado e não é apoiado por um conjunto de objeto, esse uma implementação ou a réplica do controlador.
* Um orçamento de interrupção de pod (PDB) é restritivo demais e não permite que o número de pods para cair abaixo de um determinado limiar.
* Um pod usa seletores de nó ou antiafinidade não for possível cumprir se agendado num nó diferente.

Para obter mais informações sobre como o dimensionamento automático do cluster pode não ser possível reduzir verticalmente, consulte [que tipos de pods pode impedir que o dimensionamento automático de cluster de remover um nó?][autoscaler-scaledown]

O dimensionamento automático de cluster utiliza os parâmetros de arranque para coisas como intervalos de tempo entre eventos de dimensionamento e limiares de recursos. Esses parâmetros são definidos pela plataforma do Azure e, atualmente não são expostos para que possa ajustar. Para obter mais informações sobre quais os parâmetros utiliza o dimensionamento automático de cluster, consulte [quais são os parâmetros de dimensionamento automático do cluster?] [autoscaler-parameters].

Os dois autoscalers podem trabalhar em conjunto e são, muitas vezes, ambos implementadas num cluster. Quando combinadas, o dimensionamento automático horizontal de pods concentra-se sobre como executar o número de pods necessários para atender a exigência da aplicação. O dimensionamento automático de cluster se concentra em que executa o número de nós necessários para suportar os pods agendados.

> [!NOTE]
> Dimensionamento manual é desativado quando utiliza o dimensionamento automático do cluster. Permitir que o dimensionamento automático de cluster determinar o número necessário de nós. Se quiser Dimensionar manualmente o seu cluster, [desativar o dimensionamento automático de cluster](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Criar um cluster do AKS e ativar o dimensionamento automático de cluster

Se precisar de criar um cluster do AKS, utilize o [criar az aks] [ az-aks-create] comando. Especifique um *kubernetes--version* que atenda ou exceda o número de versão mínima necessário, conforme descrito na anterior [antes de começar](#before-you-begin) secção. Para ativar e configurar o dimensionamento automático de cluster, utilize o *– enable-cluster-dimensionamento automático* parâmetro e especificar um nó *– min-count* e *– contagem máxima*.

> [!IMPORTANT]
> O dimensionamento automático do cluster é um componente de Kubernetes. Embora o cluster do AKS utiliza um conjunto para os nós de dimensionamento de máquina virtual, manualmente não ativar ou editar as definições do dimensionamento automático do conjunto de dimensionamento no portal do Azure ou ao utilizar a CLI do Azure. Permitir que o cluster de Kubernetes dimensionamento automático gerir as definições de dimensionamento necessário. Para obter mais informações, consulte [posso modificar os recursos do AKS no grupo de recursos MC_?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc_-resource-group)

O exemplo seguinte cria um cluster do AKS com o conjunto de dimensionamento de máquina virtual e o dimensionamento automático de cluster ativada e utiliza um mínimo de *1* e máximo dos *3* nós:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location canadaeast

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --kubernetes-version 1.12.6 \
  --node-count 1 \
  --enable-vmss \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Demora alguns minutos para criar o cluster e configurar as definições de dimensionamento automático do cluster.

### <a name="enable-the-cluster-autoscaler-on-an-existing-aks-cluster"></a>Ativar o dimensionamento automático de cluster num cluster do AKS existente

Pode ativar o dimensionamento automático de cluster num cluster do AKS existente que cumpra os requisitos, conforme descrito na precedente [antes de começar](#before-you-begin) secção. Utilize o [atualizar az aks] [ az-aks-update] de comandos e optar por *– enable-cluster-dimensionamento automático*, em seguida, especifique um nó *– min-count* e *– contagem máxima*. O exemplo seguinte ativa o dimensionamento automático de cluster num cluster existente que utiliza um mínimo de *1* e máximo dos *3* nós:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Se a contagem mínima de nós é superior ao número existente de nós do cluster, demora alguns minutos para criar os nós adicionais.

## <a name="change-the-cluster-autoscaler-settings"></a>Alterar as definições de dimensionamento automático do cluster

No passo anterior para criar ou atualizar um cluster do AKS existente, a contagem de nós mínimo de dimensionamento automático do cluster foi definida como *1*, e a contagem máxima de nós foi definida como *3*. Como seu aplicativo, as exigências mudam, poderá ter de ajustar a contagem de nós de dimensionamento automático do cluster.

Para alterar a contagem de nós, utilize o [atualizar az aks] [ az-aks-update] de comando e especificar um valor mínimo e máximo. O exemplo seguinte define a *count, min* ao *1* e o *– contagem máxima* para *5*:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

> [!NOTE]
> Durante a pré-visualização, não é possível definir uma contagem mínima de nós mais elevada que está definido para o cluster. Por exemplo, se tiver atualmente definida como de contagem de min *1*, não é possível atualizar a contagem mínima para *3*.

Monitorizar o desempenho das suas aplicações e serviços e ajustar as contagens de nó do cluster dimensionamento automático para comparar o desempenho necessário.

## <a name="disable-the-cluster-autoscaler"></a>Desativar o dimensionamento automático de cluster

Se já não pretender utilizar o dimensionamento automático de cluster, pode desativá-lo com o [atualizar az aks] [ az-aks-update] comando. Nós não são removidos quando o dimensionamento automático de cluster está desabilitado.

Para remover o dimensionamento automático de cluster, especifique a *– disable-cluster-dimensionamento automático* parâmetro, conforme mostrado no exemplo a seguir:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Pode dimensionar manualmente o seu cluster com o [dimensionamento do az aks] [ az-aks-scale] comando. Se utilizar o dimensionamento automático horizontal de pods, essa funcionalidade continua a ser executado com o dimensionamento automático de cluster desativado, mas pods poderão acabar por não é possível ser agendada se os recursos de nó estão todos em utilização.

## <a name="next-steps"></a>Passos Seguintes

Este artigo mostrou como Dimensionar automaticamente o número de nós do AKS. Também pode utilizar o dimensionamento automático horizontal de pods para ajustar automaticamente o número de pods que execute a sua aplicação. Para obter passos sobre como utilizar o dimensionamento automático horizontal de pods, veja [dimensionar aplicações no AKS][aks-scale-apps].

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-github]: https://github.com/azure/aks/issues]

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
