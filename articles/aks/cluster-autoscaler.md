---
title: Utilize o autoscaler cluster no Serviço Azure Kubernetes (AKS)
description: Aprenda a utilizar o autoscaler cluster para escalar automaticamente o seu cluster para satisfazer as exigências da aplicação num cluster do Serviço Azure Kubernetes (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/18/2019
ms.author: mlearned
ms.openlocfilehash: 033cf88e29ba4a9f7ce9397fe216f7380e70be07
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713393"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Escala automaticamente um cluster para satisfazer as exigências de aplicação no Serviço Azure Kubernetes (AKS)

Para acompanhar as exigências de aplicação no Serviço Azure Kubernetes (AKS), poderá ter de ajustar o número de nós que executam as suas cargas de trabalho. O componente autoscaler cluster pode observar as cápsulas do seu cluster que não podem ser programadas devido a restrições de recursos. Quando os problemas são detetados, o número de nós numa piscina de nó é aumentado para satisfazer a procura de aplicação. Os nós também são verificados regularmente por falta de casulos de funcionamento, com o número de nós diminuído conforme necessário. Esta capacidade de escalar automaticamente para cima ou para baixo o número de nós no seu cluster AKS permite-lhe executar um cluster eficiente e rentável.

Este artigo mostra-lhe como ativar e gerir o autoscaler de cluster num cluster AKS. 

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer que esteja a executar a versão Azure CLI 2.0.76 ou mais tarde. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

## <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se quando cria e gere os clusters AKS que utilizam o autoescalador do cluster:

* O complemento de roteamento de aplicativo HTTP não pode ser usado.

## <a name="about-the-cluster-autoscaler"></a>Sobre o autoescalador cluster

Para se ajustarem às exigências de aplicação em mudança, como entre o dia de trabalho e a noite ou ao fim de semana, os clusters precisam muitas vezes de uma forma de escalar automaticamente. Os clusters AKS podem escalar de uma de duas maneiras:

* O **cluster autoscaler** observa para cápsulas que não podem ser programadas em nós devido a restrições de recursos. O cluster aumenta automaticamente o número de nós.
* O **autoscaler horizontal** da cápsula utiliza o Servidor de Métricas num cluster Kubernetes para monitorizar a procura de recursos das cápsulas. Se uma aplicação necessitar de mais recursos, o número de cápsulas é automaticamente aumentado para satisfazer a procura.

![O autoscaler de cluster e o autoscaler horizontal trabalham muitas vezes em conjunto para suportar as exigências de aplicação necessárias](media/autoscaler/cluster-autoscaler.png)

Tanto o autoscaler horizontal da cápsula como o autoscaler do cluster também podem diminuir o número de cápsulas e nós, se necessário. O autoescalador do cluster diminui o número de nós quando há capacidade não utilizada há um período de tempo. As cápsulas num nó a serem removidas pelo autoescalador do cluster são programadas com segurança noutros locais do cluster. O autoscaler do cluster pode ser incapaz de reduzir a escala se as cápsulas não se moverem, como nas seguintes situações:

* Uma cápsula é criada diretamente e não é apoiada por um objeto controlador, como um conjunto de implementação ou réplica.
* Um orçamento de rutura da cápsula (PDB) é demasiado restritivo e não permite que o número de cápsulas fique abaixo de um determinado limiar.
* Um casulo usa selecionadores de nó ou anti-afinidade que não pode ser honrado se agendado em um nó diferente.

Para obter mais informações sobre como o autoscaler do cluster pode ser incapaz de reduzir a escala, veja [que tipos de cápsulas podem impedir o autoescalador do cluster de remover um nó?][autoscaler-scaledown]

O autoscaler cluster utiliza parâmetros de arranque para coisas como intervalos de tempo entre eventos de escala e limiares de recursos. Estes parâmetros são definidos pela plataforma Azure, e não estão atualmente expostos para que se ajuste. Para obter mais informações sobre os parâmetros que o autoescalador do cluster utiliza, veja [Quais são os parâmetros autoescaladores do cluster?][autoscaler-parameters]

Os autoscalers de cluster e pod horizontal podem trabalhar em conjunto, e são frequentemente ambos implantados num cluster. Quando combinado, o autoescalador horizontal da cápsula está focado em executar o número de cápsulas necessárias para satisfazer a procura da aplicação. O autoscaler do cluster está focado em executar o número de nós necessários para suportar as cápsulas programadas.

> [!NOTE]
> A escala manual é desativada quando utiliza o autodimensionador do cluster. Deixe o autoescalador do cluster determinar o número necessário de nós. Se pretender escalar manualmente o seu cluster, [desative o autoescalador do cluster](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Criar um cluster AKS e ativar o autoscaler cluster

Se precisar de criar um cluster AKS, use as [aks az criar][az-aks-create] comando. Para ativar e configurar o autoescalador do cluster na piscina do nó para o cluster, utilize o parâmetro *--activa-cluster-cluster-autoscaler,* e especifique um nó *--contagem de min* e *--contagem máxima*.

> [!IMPORTANT]
> O autoscaler cluster é um componente Kubernetes. Embora o cluster AKS utilize um conjunto de escala de máquina virtual para os nós, não ative manualmente ou edite as definições para a escala definida automaticamente no portal Azure ou utilizando o Azure CLI. Deixe o autoescalador de cluster Kubernetes gerir as definições de escala necessárias. Para mais informações, consulte [posso modificar os recursos aks no grupo de recursos do nó?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

O exemplo seguinte cria um cluster AKS com uma única piscina de nó apoiada por um conjunto de escala de máquina virtual. Ele também habilita o dimensionador do cluster em conjunto de nós para o cluster e define um mínimo de *1* e o máximo de *3* nós:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Leva alguns minutos para criar o cluster e configurar as definições de autoescalador do cluster.

## <a name="change-the-cluster-autoscaler-settings"></a>Alterar as definições de autoescalador do cluster

> [!IMPORTANT]
> Se tiver várias piscinas de nós no seu cluster AKS, salte para a [escala automática com várias piscinas](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)de agentes . Os clusters com várias piscinas de agentes requerem a utilização do conjunto de comando `az aks nodepool` para alterar propriedades específicas do nó em vez de `az aks`.

No passo anterior para criar um cluster AKS ou atualizar um conjunto de nóexistente, a contagem mínima de nó de escalar de cluster foi fixada para *1*, e a contagem máxima do nó foi fixada para *3*. Como a sua aplicação exige alterações, poderá ter de ajustar a contagem de nósdea de escala automática do cluster.

Para alterar a contagem de nós, use o comando de [atualização az aks.][az-aks-update]

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

O exemplo acima atualiza o autoscaler do cluster na piscina de nó único no *myAKSCluster* para um mínimo de *1* e máximo de *5* nós.

> [!NOTE]
> Você não pode definir uma contagem mínima de nó mais alta do que está atualmente definida para a piscina do nó. Por exemplo, se tem atualmente a contagem de min definida para *1,* não pode atualizar a contagem de min para *3*.

Monitorize o desempenho das suas aplicações e serviços e ajuste as contagens do cluster autoscaler para corresponder ao desempenho exigido.

## <a name="disable-the-cluster-autoscaler"></a>Desativar o autoescalador do cluster

Se já não pretender utilizar o autoescalador do cluster, pode desativá-lo utilizando o comando de [atualização az aks,][az-aks-update] especificando o parâmetro *auto-escalador de cluster-desactivado.* Os nós não são removidos quando o autoscaler do cluster está desativado.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Pode escalar manualmente o seu cluster depois de desativar o autoescalador do cluster utilizando o comando de [escala az aks.][az-aks-scale] Se utilizar o autoescalador horizontal da cápsula, essa função continua a funcionar com o autoscaler do cluster desativado, mas as cápsulas podem acabar por não conseguir ser programadas se todos os recursos do nó estiverem em uso.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Reativar um autoscaler de cluster desativado

Se desejar reativar o autoescalador do cluster num cluster existente, pode reactivar-o utilizando o comando de [atualização az aks,][az-aks-update] especificando os parâmetros de *autoescalar --activa-cluster-autoscaler*, *--min-count*, e *--max-count.*

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Recuperar registos e estado de autoescalador de cluster

Para diagnosticar e depurar eventos de escalaautomática, os registos e o estado podem ser recuperados a partir do add-on de escala automática.

A AKS gere o autoscaler do cluster em seu nome e executa-o no plano de controlo gerido. Os registos de nó do mestre devem ser configurados para serem vistos como resultado.

Para configurar os registos a serem empurrados do autoescalador do cluster para o Log Analytics siga estes passos.

1. Configurar uma regra para registos de diagnóstico para empurrar os registos de escalar de cluster para log Analytics. [As instruções são detalhadas aqui](https://docs.microsoft.com/azure/aks/view-master-logs#enable-diagnostics-logs), certifique-se de verificar se a caixa tem `cluster-autoscaler` ao selecionar opções para "Registos".
1. Clique na secção "Registos" no seu cluster através do portal Azure.
1. Insera a seguinte consulta de exemplo no Log Analytics:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

Deve ver registos semelhantes aos seguintes devolvidos, desde que existam registos para recuperar.

![Log Analytics logs](media/autoscaler/autoscaler-logs.png)

O autoscaler cluster também irá escrever o estado de saúde para um configmap chamado `cluster-autoscaler-status`. Para recuperar estes registos executa o seguinte comando `kubectl`. Será reportado um estado de saúde para cada piscina de nó configurada com o autoescalador do cluster.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Para saber mais sobre o que está registado a partir do autoscaler, leia as FAQ no [projeto Kubernetes/autoscaler GitHub](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why).

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>Utilize o autoscaler cluster com várias piscinas de nós ativadas

O autoscaler de cluster pode ser utilizado juntamente com [várias piscinas](use-multiple-node-pools.md) de nós ativadas. Siga esse documento para aprender a permitir várias piscinas de nós e adicione piscinas adicionais de nós a um cluster existente. Ao utilizar ambas as funcionalidades em conjunto, ativa o autoescalador de cluster em cada piscina de nó individual do cluster e pode passar regras únicas de autoscalcificação para cada um.

O comando abaixo pressupõe que seguiu as [instruções iniciais](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) anteriormente neste documento e pretende atualizar a contagem máxima de *3* a *5*. Utilize o comando de [atualização az aks nodepool][az-aks-nodepool-update] para atualizar as definições de um nó de piscina existente.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

O autoscaler do cluster pode ser desativado com a atualização de [nodepool az aks][az-aks-nodepool-update] e passando o parâmetro `--disable-cluster-autoscaler`.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Se desejar reativar o autoescalador de cluster num cluster existente, pode reativa-lo utilizando o comando de [atualização az aks nodepool,][az-aks-nodepool-update] especificando os parâmetros de *--enable-cluster-autoscaler*, *--min-count*e *--max-count.*

## <a name="next-steps"></a>Passos seguintes

Este artigo mostrou-lhe como escalar automaticamente o número de nós AKS. Também pode utilizar o autoescalador horizontal para ajustar automaticamente o número de cápsulas que executam a sua aplicação. Para os passos na utilização do autoscaler horizontal, consulte [as aplicações Scale em AKS][aks-scale-apps].

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
