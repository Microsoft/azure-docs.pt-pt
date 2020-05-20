---
title: Utilize o autoscaler cluster no Serviço Azure Kubernetes (AKS)
description: Aprenda a utilizar o autoscaler cluster para escalar automaticamente o seu cluster para satisfazer as exigências da aplicação num cluster do Serviço Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: f40d13b6b9a37f4c5efcc73e52b631bd2eec659a
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683552"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Escala automaticamente um cluster para satisfazer as exigências de aplicação no Serviço Azure Kubernetes (AKS)

Para acompanhar as exigências de aplicação no Serviço Azure Kubernetes (AKS), poderá ter de ajustar o número de nós que executam as suas cargas de trabalho. O componente autoscaler cluster pode observar as cápsulas do seu cluster que não podem ser programadas devido a restrições de recursos. Quando os problemas são detetados, o número de nós numa piscina de nó é aumentado para satisfazer a procura de aplicação. Os nós também são verificados regularmente por falta de casulos de funcionamento, com o número de nós diminuído conforme necessário. Esta capacidade de escalar automaticamente para cima ou para baixo o número de nós no seu cluster AKS permite-lhe executar um cluster eficiente e rentável.

Este artigo mostra-lhe como ativar e gerir o autoscaler de cluster num cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer que esteja a executar a versão Azure CLI 2.0.76 ou mais tarde. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se quando cria e gere os clusters AKS que utilizam o autoescalador do cluster:

* O add-on de encaminhamento de aplicações HTTP não pode ser utilizado.

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

O autoscaler cluster utiliza parâmetros de arranque para coisas como intervalos de tempo entre eventos de escala e limiares de recursos. Para obter mais informações sobre os parâmetros que o autoescalador do cluster utiliza, veja [Quais são os parâmetros autoescaladores do cluster?][autoscaler-parameters]

Os autoscalers de cluster e pod horizontal podem trabalhar em conjunto, e são frequentemente ambos implantados num cluster. Quando combinado, o autoescalador horizontal da cápsula está focado em executar o número de cápsulas necessárias para satisfazer a procura da aplicação. O autoscaler do cluster está focado em executar o número de nós necessários para suportar as cápsulas programadas.

> [!NOTE]
> A escala manual é desativada quando utiliza o autodimensionador do cluster. Deixe o autoescalador do cluster determinar o número necessário de nós. Se pretender escalar manualmente o seu cluster, [desative o autoescalador do cluster](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Criar um cluster AKS e ativar o autoscaler cluster

Se precisar de criar um cluster AKS, use as [aks az criar][az-aks-create] comando. Para ativar e configurar o autoescalador do cluster na piscina do nó para o cluster, utilize o parâmetro *--activa-cluster-cluster-autoscaler,* e especifique um nó *--contagem de min* e *--contagem máxima*.

> [!IMPORTANT]
> O autoscaler cluster é um componente Kubernetes. Embora o cluster AKS utilize um conjunto de escala de máquina virtual para os nós, não ative manualmente ou edite as definições para a escala definida automaticamente no portal Azure ou utilizando o Azure CLI. Deixe o autoescalador de cluster Kubernetes gerir as definições de escala necessárias. Para mais informações, consulte [posso modificar os recursos aks no grupo de recursos do nó?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

O exemplo seguinte cria um cluster AKS com uma única piscina de nó apoiada por um conjunto de escala de máquina virtual. Também permite o autoescalador de cluster na piscina do nó para o cluster e define um mínimo de *1* e nomáximo de *3* nós:

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
> Se tiver várias piscinas de nós no seu cluster AKS, salte para a [escala automática com várias piscinas](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)de agentes . Os clusters com várias piscinas de agentes requerem a utilização do conjunto de `az aks nodepool` comandos para alterar propriedades específicas da piscina de nós em vez de `az aks` .

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
O autoscaler cluster tomará as suas decisões de escala com base nas contagens mínimas e máximas definidas em cada piscina de nó, mas não as aplica. Por exemplo, a definição de uma contagem de 5 quando a contagem atual do nó é 3 não escalará imediatamente a piscina até 5. Se alterar a contagem mínima no conjunto do nó para um valor superior ao número atual de nós, este novo limite será respeitado quando houver pods desprogramados suficientes que exigiriam 2 novos nós adicionais e desencadeariam um evento de escalar automática. Após a ocorrência, o novo limite mínimo de contagem será respeitado para o autoescalador do cluster.

Monitorize o desempenho das suas aplicações e serviços e ajuste as contagens do cluster autoscaler para corresponder ao desempenho exigido.

## <a name="using-the-autoscaler-profile"></a>Utilizando o perfil autoscaler

Também pode configurar mais detalhes granulares do autoescalador do cluster alterando os valores predefinidos no perfil autoescalador em todo o cluster. Por exemplo, um evento de escala para baixo acontece depois de os nós serem subutilizados após 10 minutos. Se tivesse cargas de trabalho que funcionavam a cada 15 minutos, talvez quisesse alterar o perfil de autoescalar para reduzir em nós utilizados após 15 ou 20 minutos. Quando ativa o auto-escalador do cluster, é utilizado um perfil predefinido a menos que especifique diferentes definições. O perfil autoescalador do cluster tem as seguintes definições que pode atualizar:

| Definição                          | Descrição                                                                              | Valor predefinido |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| intervalo de digitalização                    | Quantas vezes o cluster é reavaliado para escala para cima ou para baixo                                    | 10 segundos    |
| escala-down-atraso-após-adicionar       | Quanto tempo depois da escala para cima que a avaliação de escala para baixo retoma                               | 10 minutos    |
| escala-down-atraso-após-apagar    | Quanto tempo depois da eliminação do nó que escala a avaliação retoma                          | intervalo de digitalização |
| escala-down-atraso-após-falha   | Quanto tempo depois da redução da escala que escala para baixo a avaliação retoma                     | 3 minutos     |
| escala-down-tempo desnecessário         | Quanto tempo um nó deve ser desnecessário antes de ser elegível para a escala para baixo                  | 10 minutos    |
| escala-down-unready-time          | Quanto tempo um nó não pronto deve ser desnecessário antes de ser elegível para a escala para baixo         | 20 minutos    |
| escala-down-intervalo de utilização | Nível de utilização do nó, definido como soma dos recursos solicitados divididos por capacidade, abaixo do qual um nó pode ser considerado para a escala para baixo | 0,5 |
| max-graceful-termination-sec     | Número máximo de segundos o autoscaler do cluster aguarda a terminação da cápsula ao tentar escalar um nó. | 600 segundos   |
| equilíbrio-grupos de nó semelhantes | Detete piscinas de nós semelhantes e equilibre o número de nós entre eles | false |

> [!IMPORTANT]
> O perfil autoscaler do cluster afeta todas as piscinas de nós que utilizam o autoscaler do cluster. Não se pode definir um perfil de escala automática por piscina de nó.

### <a name="install-aks-preview-cli-extension"></a>Instale a extensão CLI de pré-visualização de aks

Para definir o perfil de definições de autoescalar do cluster, necessita da versão de extensão CLI de *pré-visualização* de aks 0.4.30 ou superior. Instale a extensão Azure CLI de *pré-visualização de aks* utilizando o comando de adição de [extensão az][az-extension-add] e, em seguida, verifique se há atualizações disponíveis utilizando o comando de atualização de [extensão az:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>Defina o perfil autoscaler cluster em um cluster AKS existente

Utilize o comando de [atualização az aks][az-aks-update] com o parâmetro de perfil de *cluster-autoscaler* para definir o perfil autoescalador do cluster no seu cluster. O exemplo seguinte configura a definição de intervalo de digitalização como 30s no perfil.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

Quando activao autoescalador de cluster em piscinas de nós no cluster, esses clusters também utilizarão o perfil autoescalador do cluster. Por exemplo:

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
> Quando definir o perfil autoscaler do cluster, quaisquer piscinas de nó existentes com o autoescalador de cluster ativado começarão a utilizar o perfil imediatamente.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>Defina o perfil autoscaler cluster ao criar um cluster AKS

Também pode utilizar o parâmetro *de perfil de cluster-autoscaler* quando criar o seu cluster. Por exemplo:

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

O comando acima cria um cluster AKS e define o intervalo de digitalização como 30 segundos para o perfil autoescalador em todo o cluster. O comando também permite o autoescalador de cluster na piscina inicial do nó, define a contagem mínima do nó para 1 e a contagem máxima do nó para 3.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>Redefinir o perfil de autoescalador do cluster para valores padrão

Utilize o comando de [atualização az aks][az-aks-update] para redefinir o perfil autoscaler do cluster no seu cluster.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

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

Para configurar os registos a serem empurrados do autoscaler do cluster para o Log Analytics, siga estes passos.

1. Estabeleça uma regra para os registos de recursos para empurrar os registos de escalar de cluster para log Analytics. [As instruções são detalhadas aqui](https://docs.microsoft.com/azure/aks/view-master-logs#enable-resource-logs), certifique-se de verificar se a caixa está a `cluster-autoscaler` selecionar opções para "Registos".
1. Clique na secção "Registos" no seu cluster através do portal Azure.
1. Insera a seguinte consulta de exemplo no Log Analytics:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

Deve ver registos semelhantes ao seguinte exemplo, desde que existam registos para recuperar.

![Log Analytics logs](media/autoscaler/autoscaler-logs.png)

O autoscaler cluster também irá escrever o estado de saúde para um configmap chamado `cluster-autoscaler-status` . Para recuperar estes troncos, execute o seguinte `kubectl` comando. Será reportado um estado de saúde para cada piscina de nó configurada com o autoescalador do cluster.

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

O autoscaler do cluster pode ser desativado com a atualização de [nodepool az aks][az-aks-nodepool-update] e passando o `--disable-cluster-autoscaler` parâmetro.

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
[aks-faq]: faq.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
