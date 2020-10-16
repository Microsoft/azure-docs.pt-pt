---
title: Utilize o autoescalador de cluster no Serviço Azure Kubernetes (AKS)
description: Aprenda a utilizar o autoescalador de cluster para escalar automaticamente o seu cluster para satisfazer as exigências da aplicação num cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: 7368745d3b6bf9731f987d6f4fc36b81d354fed8
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92103871"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Dimensionar automaticamente um cluster para satisfazer as exigências das aplicações no Azure Kubernetes Service (AKS)

Para acompanhar as exigências da aplicação no Serviço Azure Kubernetes (AKS), poderá ter de ajustar o número de nós que executam as suas cargas de trabalho. O componente de autoescala de cluster pode observar por cápsulas no seu cluster que não podem ser programadas devido a restrições de recursos. Quando as questões são detetadas, o número de nós num conjunto de nós é aumentado para satisfazer a procura de aplicação. Os nós também são verificados regularmente por falta de cápsulas de corrida, com o número de nós a diminuir conforme necessário. Esta capacidade de aumentar ou diminuir automaticamente o número de nós no seu cluster AKS permite-lhe executar um cluster eficiente e rentável.

Este artigo mostra-lhe como ativar e gerir o cluster autoscaler num cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer que esteja a executar a versão Azure CLI 2.0.76 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="about-the-cluster-autoscaler"></a>Sobre o cluster autoscaler

Para se ajustarem às exigências de aplicação em mudança, como entre o dia de trabalho e a noite ou ao fim de semana, os agrupamentos precisam frequentemente de uma forma de escalar automaticamente. Os clusters AKS podem escalar de uma de duas maneiras:

* O **cluster autoscaler** observa para cápsulas que não podem ser programadas em nós devido a restrições de recursos. Em seguida, o cluster aumenta automaticamente o número de nós.
* O **autoescalador horizontal** usa o Metrics Server num cluster Kubernetes para monitorizar a procura de recursos de cápsulas. Se uma aplicação necessitar de mais recursos, o número de cápsulas é automaticamente aumentado para satisfazer a procura.

![O autoescalador de cluster e o autoescalador horizontal trabalham frequentemente em conjunto para suportar as exigências de aplicação exigidas](media/autoscaler/cluster-autoscaler.png)

Tanto o autoescalador horizontal como o autoescalador de agrupamento também podem diminuir o número de cápsulas e nós conforme necessário. O autoescalador do cluster diminui o número de nós quando há capacidade não utilizada durante um período de tempo. As cápsulas num nó a remover pelo autoescalador do cluster estão agendadas com segurança noutros locais do cluster. O autoescalador do cluster pode não conseguir reduzir a escala se as cápsulas não se moverem, tais como nas seguintes situações:

* Uma cápsula é criada diretamente e não é apoiada por um objeto controlador, como um conjunto de implementação ou réplica.
* Um orçamento de rutura de vagem (PDB) é demasiado restritivo e não permite que o número de cápsulas fique abaixo de um determinado limiar.
* Um pod usa seletores de nó ou anti-afinidade que não pode ser honrado se for programado em um nó diferente.

Para obter mais informações sobre como o autoescalador do cluster pode não conseguir reduzir a escala, veja [que tipos de cápsulas podem impedir o cluster autoscaler de remover um nó?][autoscaler-scaledown]

O autoescalador do cluster utiliza parâmetros de arranque para coisas como intervalos de tempo entre eventos de escala e limiares de recursos. Para obter mais informações sobre os parâmetros que o autoescalador do cluster utiliza, consulte [utilizando o perfil de autoescalador](#using-the-autoscaler-profile).

O cluster e os autoescaladores horizontais podem trabalhar em conjunto, e são muitas vezes ambos implantados num cluster. Quando combinado, o autoescalador horizontal está focado em executar o número de cápsulas necessárias para satisfazer a procura de aplicações. O autoescalador do cluster está focado em executar o número de nós necessários para suportar as cápsulas programadas.

> [!NOTE]
> A escala manual é desativada quando utiliza o autoescalador do cluster. Deixe o autoescalador de cluster determinar o número de nós necessário. Se pretender escalar manualmente o seu cluster, [desative o autoescalador do cluster](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Crie um cluster AKS e ative o cluster autoscaler

Se precisar de criar um cluster AKS, use os [az aks criar][az-aks-create] comando. Para ativar e configurar o autoescalador do cluster na piscina de nós para o cluster, utilize o parâmetro *autoescalo-cluster-activar* e especifique um nó *-- contagem de min-contagem* e *--contagem máxima*.

> [!IMPORTANT]
> O autoescalador de cluster é um componente Kubernetes. Embora o cluster AKS utilize um conjunto de balança de máquina virtual para os nós, não ative manualmente ou edite definições para escala definida automaticamente no portal Azure ou utilizando o CLI Azure. Deixe que o cluster Kubernetes gere as definições de escala necessárias. Para mais informações, consulte [posso modificar os recursos da AKS no grupo de recursos de nó?][aks-faq-node-resource-group]

O exemplo a seguir cria um cluster AKS com um único conjunto de nós apoiado por um conjunto de balança de máquina virtual. Também permite o autoescalador de cluster na piscina de nó para o cluster e define um mínimo de *1* e máximo de *3* nóns:

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

Demora alguns minutos a criar o cluster e a configurar as definições de autoescala do cluster.

## <a name="update-an-existing-aks-cluster-to-enable-the-cluster-autoscaler"></a>Atualize um cluster AKS existente para permitir o cluster autoscaler

Utilize o comando [de atualização az aks][az-aks-update] para ativar e configurar o autoescalador do cluster no conjunto de nós para o cluster existente. Utilize o parâmetro *autoescalador de agrupamento-activador* e especifique um nó *-- contagem de min* e *--contagem máxima*.

> [!IMPORTANT]
> O autoescalador de cluster é um componente Kubernetes. Embora o cluster AKS utilize um conjunto de balança de máquina virtual para os nós, não ative manualmente ou edite definições para escala definida automaticamente no portal Azure ou utilizando o CLI Azure. Deixe que o cluster Kubernetes gere as definições de escala necessárias. Para mais informações, consulte [posso modificar os recursos da AKS no grupo de recursos de nó?][aks-faq-node-resource-group]

O exemplo a seguir atualiza um cluster AKS existente para permitir o autoescalador de cluster na piscina de nó para o cluster e define um mínimo de *1* e máximo de *3* nóns:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Demora alguns minutos a atualizar o cluster e a configurar as definições de autoescala do cluster.

## <a name="change-the-cluster-autoscaler-settings"></a>Alterar as definições de autoescalador de cluster

> [!IMPORTANT]
> Se tiver várias piscinas de nós no seu cluster AKS, salte para a [autoestamos com a secção de piscinas de vários agentes](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled). Os agrupamentos com piscinas de vários agentes requerem a utilização do conjunto de `az aks nodepool` comandos para alterar propriedades específicas do conjunto de nós em vez de `az aks` .

No passo anterior para criar um cluster AKS ou atualizar um conjunto de nós existente, a contagem mínima de nó de autoescalador foi definida para *1*, e a contagem máxima de nós foi definida para *3*. À medida que a sua aplicação exige alterações, poderá ter de ajustar a contagem de nós de autoescalador do cluster.

Para alterar a contagem de nós, utilize o comando [de atualização az aks.][az-aks-update]

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

O exemplo acima atualiza o autoescalador de cluster na piscina de nó único no *myAKSCluster* para um mínimo de *1* e máximo de *5* nós.

> [!NOTE]
> O autoescalador de clusters toma decisões de escala com base nas contagens mínimas e máximas definidas em cada piscina de nós, mas não as aplica após a atualização das contagens min ou max. Por exemplo, definir uma contagem de min de 5 quando a contagem atual do nó é 3 não escala imediatamente a piscina até 5. Se a contagem mínima no conjunto de nós tiver um valor superior ao número atual de nós, as novas definições min ou max serão respeitadas quando existirem cápsulas não-dobráveis suficientes que requerem 2 novos nós adicionais e desencadeiem um evento de autoescala. Após o evento de escala, os novos limites de contagem são respeitados.

Monitorize o desempenho das suas aplicações e serviços e ajuste as contagens de nó de autoescalador do cluster para corresponder ao desempenho necessário.

## <a name="using-the-autoscaler-profile"></a>Usando o perfil de autoescalador

Também pode configurar mais detalhes granulares do autoescalador do cluster alterando os valores predefinidos no perfil de autoescala em todo o cluster. Por exemplo, um evento de escala para baixo ocorre depois de os nós serem subutilizados após 10 minutos. Se tiver cargas de trabalho que se desciam a cada 15 minutos, talvez quisesse alterar o perfil de autoescalador para reduzir sob nós utilizados após 15 ou 20 minutos. Quando ativa o autoescalador do cluster, é utilizado um perfil predefinido a menos que especifique diferentes definições. O perfil de autoescalador do cluster tem as seguintes definições que pode atualizar:

| Definições                          | Descrição                                                                              | Valor predefinido |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| intervalo de digitalização                    | Quantas vezes o cluster é reavaliado para escala para cima ou para baixo                                    | 10 segundos    |
| escala-down-delay-after-add       | Quanto tempo depois de escalar a escala para baixo a avaliação retoma                               | 10 minutos    |
| escala-down-delay-after-delete    | Quanto tempo depois da eliminação do nó que a avaliação de escala para baixo retoma                          | intervalo de digitalização |
| escala-down-delay-after-falha   | Quanto tempo depois de reduzir a falha que escala para baixo a avaliação retoma                     | 3 minutos     |
| escala-down-un-desempreed-tempo         | Quanto tempo um nó deve ser despromugonado antes de ser elegível para escala para baixo                  | 10 minutos    |
| escala-down-unready-tempo          | Quanto tempo um nó não redondo deve ser desconso antes de ser elegível para escala para baixo         | 20 minutos    |
| limiar de redução da escala | Nível de utilização do nó, definido como soma de recursos solicitados divididos por capacidade, abaixo do qual um nó pode ser considerado para escala para baixo | 0,5 |
| max-graciosa-rescisão-se     | O número máximo de segundos que o autoescalador do cluster aguarda a terminação da cápsula ao tentar reduzir um nó. | 600 segundos   |
| balanço-similar-grupos de nó | Detete piscinas de nó semelhantes e equilibre o número de nós entre eles | false |

> [!IMPORTANT]
> O perfil de autoescalador do cluster afeta todas as piscinas de nós que utilizam o autoescalador do cluster. Não é possível definir um perfil de autoescalador por piscina de nó.
>
> O perfil de autoescalador do cluster requer a versão *2.11.1* ou maior do Azure CLI. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>Desacruque o perfil de autoescalador de cluster num cluster AKS existente

Utilize o comando [de atualização az aks][az-aks-update-preview] com o parâmetro *de perfil de cluster-autoscaler* para definir o perfil de autoescalador do cluster no seu cluster. O exemplo a seguir configura a definição do intervalo de digitalização como 30s no perfil.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

Quando ativar o autoescalador do cluster em piscinas de nó no cluster, esses clusters também utilizarão o perfil de autoescalador do cluster. Por exemplo:

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!IMPORTANT]
> Quando definir o perfil de autoescalador do cluster, quaisquer piscinas de nós existentes com o autoescalador ativado começarão a utilizar o perfil imediatamente.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>Desacruque o perfil de autoescalador do cluster ao criar um cluster AKS

Também pode utilizar o parâmetro *de perfil de escala de cluster-autoscaler* quando criar o seu cluster. Por exemplo:

```azurecli-interactive
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3 \
  --cluster-autoscaler-profile scan-interval=30s
```

O comando acima cria um cluster AKS e define o intervalo de digitalização como 30 segundos para o perfil de autoescala de cluster. O comando também permite o autoescalador do cluster na piscina inicial do nó, define a contagem mínima de nós para 1 e a contagem máxima do nó para 3.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>Redefinir o perfil de autoescalador do cluster para valores predefinidos

Utilize o comando [de atualização az aks][az-aks-update-preview] para redefinir o perfil de autoescalador do cluster no seu cluster.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>Desative o autoescalador de cluster

Se já não pretender utilizar o autoescalador do cluster, pode desativá-lo utilizando o comando [de atualização az aks,][az-aks-update-preview] especificando o parâmetro *autoescala de cluster-cluster.desativado.* Os nós não são removidos quando o autoescalador do cluster é desativado.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Pode escalar manualmente o seu cluster depois de desativar o autoescalador do cluster utilizando o comando [de escala az aks.][az-aks-scale] Se utilizar o autoescalador horizontal, essa função continua a funcionar com o cluster autoscaler desativado, mas as cápsulas podem acabar por não poder ser programadas se todos os recursos do nó estiverem a ser utilizados.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Reativar um autoescalador de cluster desativado

Se desejar voltar a ativar o autoescalador de cluster num cluster existente, pode voltar a capacitá-lo utilizando o comando [de atualização az aks,][az-aks-update-preview] especificando os parâmetros *de autoescala de cluster-capacitação*, *--contagem de min-* e *--contagem máxima.*

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Recuperar registos e estado de autoescala de cluster

Para diagnosticar e depurar eventos de autoescala, os registos e o estado podem ser recuperados a partir do addon autoscaler.

A AKS gere o autoescala de cluster em seu nome e executa-o no plano de controlo gerido. Os registos de nómadas principais devem ser configurados para serem vistos como resultado.

Para configurar os registos a serem empurrados do autoescalador do cluster para o Log Analytics, siga estes passos.

1. Crie uma regra para os registos de recursos para empurrar os registos de escala de cluster-autoscaler para Log Analytics. [As instruções são detalhadas aqui,][aks-view-master-logs]certifique-se de que verifica a caixa para `cluster-autoscaler` quando selecionar opções para "Logs".
1. Clique na secção "Logs" no seu cluster através do portal Azure.
1. Insira a seguinte consulta de exemplo no Log Analytics:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

Deve ver registos semelhantes ao exemplo a seguir, desde que existam registos para recuperar.

![Registar registos de análise](media/autoscaler/autoscaler-logs.png)

O autoescalador do cluster também irá escrever o estado de saúde para um configmap chamado `cluster-autoscaler-status` . Para recuperar estes registos, execute o seguinte `kubectl` comando. Será reportado um estado de saúde para cada piscina de nó configurado com o autoescalador do cluster.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Para saber mais sobre o que é registado a partir do autoescalador, leia as FAQ no [projeto Kubernetes/autoscaler GitHub][kubernetes-faq].

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>Utilize o autoescalador de cluster com várias piscinas de nó ativados

O autoescalador de cluster pode ser utilizado juntamente com [várias piscinas de nós][aks-multiple-node-pools] ativadas. Siga este documento para aprender como ativar várias piscinas de nós e adicionar piscinas de nó adicionais a um cluster existente. Ao utilizar ambas as funcionalidades em conjunto, ative o autoescalador de cluster em cada piscina de nó individual no cluster e pode passar regras de autoscalagem únicas para cada um.

O comando abaixo pressupõe que seguiu as [instruções iniciais](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) no início deste documento e pretende atualizar a contagem máxima de um número máximo de piscina existente de *3* a *5*. Utilize o comando [de atualização de nodepool az aks][az-aks-nodepool-update] para atualizar as definições de um grupo de nós existentes.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

O autoescalador do cluster pode ser desativado com [a atualização do nodepool az aks][az-aks-nodepool-update] e passando o `--disable-cluster-autoscaler` parâmetro.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Se desejar voltar a ativar o autoescalador de cluster num cluster existente, pode voltar a capacitá-lo utilizando o comando [de atualização de nodepool az aks,][az-aks-nodepool-update] especificando os parâmetros *de autoescalo de cluster-activar*, *--contagem de min-* e *--contagem máxima.*

## <a name="next-steps"></a>Passos seguintes

Este artigo mostrou-lhe como escalar automaticamente o número de nós AKS. Também pode utilizar o autoescalador horizontal para ajustar automaticamente o número de cápsulas que executam a sua aplicação. Para passos na utilização do autoescalador horizontal, consulte [as aplicações scale em AKS][aks-scale-apps].

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-master-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why
