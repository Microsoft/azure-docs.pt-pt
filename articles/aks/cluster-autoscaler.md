---
title: Usar o dimensionamento de clusters de autoescalar no serviço de kubernetes do Azure (AKS)
description: Saiba como usar o dimensionamento automático do cluster para dimensionar automaticamente o cluster para atender às demandas do aplicativo em um cluster do AKS (serviço de kubernetes do Azure).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/18/2019
ms.author: mlearned
ms.openlocfilehash: 9d7a404b767d3975cefd55e1db8487fbb45042e2
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174191"
---
# <a name="preview---automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Visualização – dimensionar automaticamente um cluster para atender às demandas do aplicativo no serviço de kubernetes do Azure (AKS)

Para acompanhar as demandas de aplicativos no AKS (serviço kubernetes do Azure), talvez seja necessário ajustar o número de nós que executam suas cargas de trabalho. O componente de autoescalar do cluster pode observar os pods no cluster que não podem ser agendados devido a restrições de recursos. Quando forem detectados problemas, o número de nós em um pool de nós será aumentado para atender à demanda do aplicativo. Os nós também são verificados regularmente quanto à falta de pods em execução, com o número de nós diminuído conforme necessário. Essa capacidade de aumentar ou reduzir automaticamente o número de nós em seu cluster AKS permite que você execute um cluster eficiente e econômico.

Este artigo mostra como habilitar e gerenciar o dimensionamento do cluster em um cluster AKS. O dimensionador de cluster único deve ser testado apenas na visualização em clusters AKS.

> [!IMPORTANT]
> Os recursos de visualização do AKS são consentimento de autoatendimento. As visualizações são fornecidas "no estado em que se encontram" e "como disponíveis" e são excluídas dos contratos de nível de serviço e da garantia limitada. As visualizações do AKS são parcialmente cobertas pelo suporte ao cliente com base no melhor esforço. Dessa forma, esses recursos não são destinados ao uso em produção. Para obter outras incompatibilidades, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS][aks-support-policies]
> * [Perguntas frequentes do suporte do Azure][aks-faq]

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer que você esteja executando o CLI do Azure versão 2.0.65 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI do AKS-Preview

Para usar o dimensionamento do cluster, você precisa da extensão da CLI do *AKs* versão 0.4.12 ou superior. Instale a extensão de CLI do Azure *de AKs-Preview* usando o comando [AZ Extension Add][az-extension-add] e, em seguida, verifique se há atualizações disponíveis usando o comando [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você cria e gerencia clusters AKS que usam o dimensionamento de clusters:

* O complemento de roteamento de aplicativo HTTP não pode ser usado.

## <a name="about-the-cluster-autoscaler"></a>Sobre o dimensionador de clusters

Para ajustar a alteração das demandas de aplicativos, como entre o workday e a noite ou em um fim de semana, os clusters geralmente precisam de uma maneira de dimensionar automaticamente. Os clusters AKS podem ser dimensionados de uma das duas maneiras:

* O **autodimensionador do cluster** observa os pods que não podem ser agendados em nós devido a restrições de recursos. Em seguida, o cluster aumenta automaticamente o número de nós.
* O **pod de dimensionamento horizontal** usa o servidor de métricas em um cluster kubernetes para monitorar a demanda de recursos de pods. Se um aplicativo precisar de mais recursos, o número de pods será aumentado automaticamente para atender à demanda.

![O dimensionador de autoescalar e o Pod horizontal de cluster geralmente funcionam juntos para dar suporte às demandas de aplicativos necessárias](media/autoscaler/cluster-autoscaler.png)

Tanto o Pod horizontal como o dimensionamento automática de clusters e o autoescalar podem diminuir o número de pods e nós conforme necessário. O dimensionador automática do cluster diminui o número de nós quando houve capacidade não utilizada por um período de tempo. Os pods em um nó a ser removido pelo dimensionador de autoescala do cluster estão agendados com segurança em outro lugar no cluster. O dimensionador em autoescalar pode não ser capaz de reduzir verticalmente se o pods não puder ser movido, como nas seguintes situações:

* Um pod criado diretamente e não é apoiado por um objeto de controlador, como uma implantação ou um conjunto de réplicas.
* Um PDB (orçamento de interrupção de Pod) é muito restritivo e não permite que o número de pods fique abaixo de um determinado limite.
* Um pod usa seletores de nó ou a afinidade que não pode ser respeitada se agendada em um nó diferente.

Para obter mais informações sobre como o dimensionador de cluster pode não ser capaz de reduzir verticalmente, consulte [quais tipos de pods podem impedir que o dimensionamento de um cluster remova um nó?][autoscaler-scaledown]

O autoescalar do cluster usa parâmetros de inicialização para coisas como intervalos de tempo entre eventos de escala e limites de recursos. Esses parâmetros são definidos pela plataforma do Azure e não são atualmente expostos para você ajustar. Para obter mais informações sobre quais parâmetros são usados pelo dimensionador de cluster, consulte [quais são os parâmetros de dimensionamento de clusters?][autoscaler-parameters].

O cluster e os autodimensionadores de Pod horizontal podem trabalhar juntos e, muitas vezes, são implantados em um cluster. Quando combinado, a escala automática de Pod horizontal concentra-se na execução do número de pods necessário para atender à demanda do aplicativo. O dimensionador automática do cluster concentra-se na execução do número de nós necessários para dar suporte ao pods agendado.

> [!NOTE]
> O dimensionamento manual é desabilitado quando você usa o cluster de dimensionamento automático. Permitir que o dimensionamento automática do cluster determine o número necessário de nós. Se você quiser dimensionar manualmente o cluster, [desabilite o dimensionador de cluster](#disable-the-cluster-autoscaler)automaticamente.

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Criar um cluster AKS e habilitar o dimensionador de cluster

Se você precisar criar um cluster AKS, use o comando [AZ AKs Create][az-aks-create] . Para habilitar e configurar o cluster de dimensionamento autoescalar no pool de nós para o cluster, use o parâmetro *--Enable-cluster-AutoScaler* e especifique um nó *--min-Count* e *--Max-Count*.

> [!IMPORTANT]
> O dimensionador de cluster é um componente kubernetes. Embora o cluster AKS use um conjunto de dimensionamento de máquinas virtuais para os nós, não habilite manualmente ou edite as configurações para dimensionamento automático do conjunto de dimensionamento no portal do Azure ou usando o CLI do Azure. Permita que o dimensionador de cluster kubernetes gerencie as configurações de escala necessárias. Para obter mais informações, consulte posso [modificar os recursos do AKS no grupo de recursos do nó?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

O exemplo a seguir cria um cluster AKS com um pool de nós único apoiado por um conjunto de dimensionamento de máquinas virtuais. Ele também habilita o dimensionador do cluster em conjunto de nós para o cluster e define um mínimo de *1* e o máximo de *3* nós:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-vmss \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!NOTE]
> Se você especificar uma *--kubernetes-Version* ao executar `az aks create`, essa versão deverá atender ou exceder o número de versão mínimo necessário, conforme descrito na seção anterior [antes de começar](#before-you-begin) .

Leva alguns minutos para criar o cluster e definir as configurações de dimensionamento de clusters.

## <a name="change-the-cluster-autoscaler-settings"></a>Alterar as configurações de dimensionamento de clusters

> [!IMPORTANT]
> Se você tiver o recurso de *vários pools de agentes* habilitado em sua assinatura, pule para a [seção dimensionamento automático com vários pools de agentes](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled). Clusters com vários pools de agentes habilitados exigem o uso do comando `az aks nodepool` definido para alterar propriedades específicas do pool de nós em vez de `az aks`. As instruções abaixo pressupõem que você não habilitou vários pools de nós. Para verificar se ele está habilitado, execute `az feature  list -o table` e procure `Microsoft.ContainerService/multiagentpoolpreview`.

Na etapa anterior para criar um cluster AKS ou atualizar um pool de nós existente, a contagem de nós mínimos do cluster de dimensionamento foi definida como *1*e a contagem máxima de nós foi definida como *3*. À medida que o aplicativo exigir alteração, talvez seja necessário ajustar a contagem de nós do dimensionamento de clusters.

Para alterar a contagem de nós, use o comando [AZ AKs Update][az-aks-update] .

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

O exemplo acima atualiza o conjunto de dimensionamento de clusters em um único pool de nós em *myAKSCluster* para um mínimo de *1* e o máximo de *5* nós.

> [!NOTE]
> Durante a versão prévia, você não pode definir uma contagem de nós mínima mais alta do que a atualmente definida para o pool de nós. Por exemplo, se você tiver uma contagem mínima definida como *1*, não será possível atualizar a contagem mín para *3*.

Monitore o desempenho de seus aplicativos e serviços e ajuste as contagens de nó de dimensionamento de clusters para corresponder ao desempenho necessário.

## <a name="disable-the-cluster-autoscaler"></a>Desabilitar o dimensionador de clusters

Se você não quiser mais usar o dimensionamento automática do cluster, poderá desabilitá-lo usando o comando [AZ AKs Update][az-aks-update] , especificando o parâmetro *--Disable-cluster-AutoScaler* . Os nós não são removidos quando o dimensionador de cluster é desabilitado.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Você pode dimensionar manualmente o cluster depois de desabilitar o dimensionamento de cluster automaticamente usando o comando [AZ AKs Scale][az-aks-scale] . Se você usar a escalabilidade horizontal do pod, esse recurso continuará a ser executado com o dimensionamento em autoescala do cluster desabilitado, mas o pods poderá não ser agendado se todos os recursos do nó estiverem em uso.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Reabilitar o dimensionador de cluster desabilitado

Se desejar reabilitar o dimensionador automática do cluster em um cluster existente, você poderá habilitá-lo novamente usando o comando [AZ AKs Update][az-aks-update] , especificando o parâmetro *--Enable-cluster-AutoScaler* .

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>Usar o dimensionamento de autoescalar do cluster com vários pools de nós habilitados

O conjunto de dimensionamento de clusters pode ser usado junto com o [recurso de visualização de vários pools de nó](use-multiple-node-pools.md) habilitado. Siga esse documento para saber como habilitar vários pools de nó e adicionar pools de nós adicionais a um cluster existente. Ao usar os dois recursos juntos, você habilita o dimensionador automático do cluster em cada pool de nós individual no cluster e pode passar regras exclusivas de dimensionamento automático para cada um deles.

O comando abaixo pressupõe que você seguiu as [instruções iniciais](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) anteriormente neste documento e deseja atualizar a contagem máxima de um pool de nós existente de *3* para *5*. Use o comando [AZ AKs nodepool Update][az-aks-nodepool-update] para atualizar as configurações de um pool de nós existente.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name multipoolcluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

O dimensionador automática de cluster pode ser desabilitado com [AZ AKs nodepool Update][az-aks-nodepool-update] e passando o parâmetro `--disable-cluster-autoscaler`.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name multipoolcluster \
  --name mynodepool \
  --disable-cluster-autoscaler
```

## <a name="next-steps"></a>Passos seguintes

Este artigo mostrou como dimensionar automaticamente o número de nós AKS. Você também pode usar a escala automática de Pod horizontal para ajustar automaticamente o número de pods que executam o aplicativo. Para obter as etapas sobre como usar a escalabilidade horizontal Pod, consulte [dimensionar aplicativos em AKs][aks-scale-apps].

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
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
