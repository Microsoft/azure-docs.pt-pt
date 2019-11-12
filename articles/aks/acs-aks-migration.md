---
title: Migrar para o serviço kubernetes do Azure (AKS)
description: Migre para o serviço kubernetes do Azure (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 11/07/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 0c243d216e00adf49a6425e5b7be0d38caeef043
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929054"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migrar para o serviço kubernetes do Azure (AKS)

Este artigo ajuda você a planejar e executar uma migração bem-sucedida para o AKS (serviço kubernetes do Azure). Para ajudá-lo a tomar decisões importantes, este guia fornece detalhes para a configuração atual recomendada para o AKS. Este artigo não abrange todos os cenários e, quando apropriado, o artigo contém links para informações mais detalhadas para o planejamento de uma migração bem-sucedida.

Este documento pode ser usado para ajudar a dar suporte aos seguintes cenários:

* Migrando um cluster AKS apoiado por [conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) para conjuntos de [dimensionamento de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)
* Migrando um cluster AKS para usar um [balanceador de carga SKU padrão](https://docs.microsoft.com/azure/aks/load-balancer-standard)
* Migrando do [serviço de contêiner do Azure (ACS)-desativando 31 de janeiro de 2020](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) para AKs
* Migrando do [mecanismo do AKS](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) para o AKs
* Migrando de clusters kubernetes não baseados no Azure para AKS

Ao migrar, verifique se sua versão de kubernetes de destino está dentro da janela com suporte para AKS. Se estiver usando uma versão mais antiga, ela pode não estar dentro do intervalo com suporte e exigir que as versões de atualização sejam suportadas pelo AKS. Consulte [versões de kubernetes com suporte do AKS](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions) para obter mais informações.

Se você estiver migrando para uma versão mais recente do kubernetes, examine a [versão do kubernetes e a política de suporte à distorção de versão](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions).

Várias ferramentas de código-fonte aberto podem ajudar com sua migração, dependendo do seu cenário:

* [Velero](https://velero.io/) (requer kubernetes 1.7 +)
* [Extensão da CLI do Azure Kube](https://github.com/yaron2/azure-kube-cli)
* [Returno](https://github.com/mhausenblas/reshifter)

Neste artigo, resumiremos os detalhes de migração para:

> [!div class="checklist"]
> * AKS com Standard Load Balancer e conjuntos de dimensionamento de máquinas virtuais
> * Serviços do Azure anexados existentes
> * Garantir cotas válidas
> * Alta disponibilidade e continuidade dos negócios
> * Considerações para aplicativos sem estado
> * Considerações para aplicativos com estado
> * Implantação de sua configuração de cluster

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS com Standard Load Balancer e conjuntos de dimensionamento de máquinas virtuais

O AKS é um serviço gerenciado que oferece recursos exclusivos com menor sobrecarga de gerenciamento. Como resultado de ser um serviço gerenciado, você deve selecionar entre um conjunto de [regiões](https://docs.microsoft.com/azure/aks/quotas-skus-regions) ao qual o AKs dá suporte. A transição do cluster existente para o AKS pode exigir a modificação de seus aplicativos existentes para que eles permaneçam íntegros no plano de controle gerenciado do AKS.

Recomendamos o uso de clusters AKS apoiados por [conjuntos de dimensionamento de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machine-scale-sets) e o [Standard Load Balancer do Azure](https://docs.microsoft.com/azure/aks/load-balancer-standard) para garantir que você obtenha recursos como [vários pools de nós](https://docs.microsoft.com/azure/aks/use-multiple-node-pools), [zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview), [intervalos de IP autorizados](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges), [autodimensionador de cluster](https://docs.microsoft.com/azure/aks/cluster-autoscaler), [Azure Policy para AKs](https://docs.microsoft.com/azure/governance/policy/concepts/rego-for-aks)e outros novos recursos à medida que forem lançados.   

Os clusters AKS apoiados por [conjuntos de disponibilidade de máquina virtual](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) não têm suporte para muitos desses recursos.

O exemplo a seguir cria um cluster AKS com um pool de nós único apoiado por um conjunto de dimensionamento de máquinas virtuais. Ele usa um balanceador de carga padrão. Ele também habilita o dimensionador do cluster em conjunto de nós para o cluster e define um mínimo de *1* e o máximo de *3* nós:

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

## <a name="existing-attached-azure-services"></a>Serviços do Azure anexados existentes

Ao migrar clusters, você pode ter anexado serviços externos do Azure. Eles não exigem recriação de recursos, mas eles exigirão a atualização de conexões de clusters anteriores para novos para manter a funcionalidade.

* Registo de Contentores do Azure
* Log Analytics
* Application Insights
* Gestor de Tráfego
* Conta de Armazenamento
* Bancos de dados externos

## <a name="ensure-valid-quotas"></a>Garantir cotas válidas

Como máquinas virtuais adicionais serão implantadas em sua assinatura durante a migração, você deve verificar se suas cotas e limites são suficientes para esses recursos. Talvez seja necessário solicitar um aumento na [cota de vCPU](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

Talvez seja necessário solicitar um aumento nas [cotas de rede](https://docs.microsoft.com/azure/azure-supportability/networking-quota-requests) para garantir que você não utilize IPS. Consulte [Networking and IP Ranges for AKs](https://docs.microsoft.com/azure/aks/configure-kubenet) para obter informações adicionais.

Para obter mais informações, consulte [limites de serviço e assinatura do Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits). Para verificar suas cotas atuais, na portal do Azure, vá para a [folha assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), selecione sua assinatura e, em seguida, selecione **uso + cotas**.

## <a name="high-availability-and-business-continuity"></a>Alta disponibilidade e continuidade dos negócios

Se seu aplicativo não puder lidar com o tempo de inatividade, será necessário seguir as práticas recomendadas para cenários de migração de alta disponibilidade.  As práticas recomendadas para planejamento de continuidade de negócios complexos, recuperação de desastres e maximização do tempo de atividade estão além do escopo deste documento.  Leia mais sobre [as práticas recomendadas para continuidade dos negócios e recuperação de desastres no AKs (serviço kubernetes do Azure)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) para saber mais.

Para aplicativos complexos, normalmente você migrará ao longo do tempo em vez de todos de uma vez. Isso significa que os ambientes novos e antigos podem precisar se comunicar pela rede. Os aplicativos que usaram anteriormente `ClusterIP` serviços para comunicação podem precisar ser expostos como tipo `LoadBalancer` e ser protegidos adequadamente.

Para concluir a migração, você desejará apontar os clientes para os novos serviços em execução no AKS. Recomendamos que você redirecione o tráfego atualizando o DNS para apontar para o Load Balancer que fica na frente do cluster AKS.

O [Gerenciador de tráfego do Azure](https://docs.microsoft.com/azure/traffic-manager/) pode direcionar os clientes para o cluster kubernetes desejado e a instância do aplicativo.  O Gerenciador de tráfego é um balanceador de carga de tráfego baseado em DNS que pode distribuir o tráfego de rede entre regiões.  Para obter o melhor desempenho e redundância, direcione todo o tráfego do aplicativo pelo Gerenciador de tráfego antes que ele vá para o cluster AKS.  Em uma implantação de multicluster, os clientes devem se conectar a um nome DNS do Gerenciador de tráfego que aponta para os serviços em cada cluster do AKS. Defina esses serviços usando os pontos de extremidade do Gerenciador de tráfego. Cada ponto de extremidade é o *IP de balanceador de carga de serviço*. Use essa configuração para direcionar o tráfego de rede do ponto de extremidade do Gerenciador de tráfego em uma região para o ponto de extremidade em uma região diferente.

![AKS com o Gerenciador de tráfego](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

O [serviço de porta frontal do Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview) é outra opção para rotear o tráfego para clusters AKs.  O Azure Front Door Service permite-lhe definir, gerir e monitorizar o encaminhamento global do tráfego da Web ao otimizar para o melhor desempenho e ativação pós-falha instantânea para uma elevada disponibilidade. 

### <a name="considerations-for-stateless-applications"></a>Considerações para aplicativos sem estado 

A migração de aplicativos sem estado é o caso mais direto. Aplique suas definições de recurso (YAML ou Helm) ao novo cluster, verifique se tudo funciona conforme o esperado e redirecione o tráfego para ativar o novo cluster.

### <a name="considers-for-stateful-applications"></a>Considera os aplicativos com estado

Planeje cuidadosamente a migração de aplicativos com estado para evitar perda de dados ou tempo de inatividade inesperado.

Se você usar os arquivos do Azure, poderá montar o compartilhamento de arquivos como um volume no novo cluster:
* [Montar arquivos estáticos do Azure como um volume](https://docs.microsoft.com/azure/aks/azure-files-volume#mount-the-file-share-as-a-volume)

Se você usar o Azure Managed Disks, só poderá montar o disco se desanexado a qualquer VM:
* [Montar disco estático do Azure como um volume](https://docs.microsoft.com/azure/aks/azure-disk-volume#mount-disk-as-volume)

Se nenhuma dessas abordagens funcionar, você poderá usar as opções de backup e restauração:
* [Velero no Azure](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)

#### <a name="azure-files"></a>Ficheiros do Azure

Ao contrário dos discos, os arquivos do Azure podem ser montados em vários hosts simultaneamente. No cluster do AKS, o Azure e o kubernetes não impedem que você crie um pod que o cluster do ACS ainda usa. Para evitar a perda de dados e o comportamento inesperado, verifique se os clusters não gravam nos mesmos arquivos ao mesmo tempo.

Se seu aplicativo puder hospedar várias réplicas que apontam para o mesmo compartilhamento de arquivos, siga as etapas de migração sem monitoração de estado e implante suas definições de YAML no novo cluster. Caso contrário, uma possível abordagem de migração envolve as seguintes etapas:

* Valide se o aplicativo está funcionando corretamente.
* Aponte seu tráfego ao vivo para o novo cluster AKS.
* Desconecte o cluster antigo.

Se você quiser iniciar com um compartilhamento vazio e fazer uma cópia dos dados de origem, poderá usar os comandos [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) para migrar seus dados.


#### <a name="migrating-persistent-volumes"></a>Migrando volumes persistentes

Se estiver migrando volumes persistentes existentes para o AKS, você geralmente seguirá estas etapas:

* Desative as gravações no aplicativo. (Essa etapa é opcional e requer tempo de inatividade.)
* Tire instantâneos dos discos.
* Crie novos Managed disks a partir dos instantâneos.
* Crie volumes persistentes no AKS.
* Atualize as especificações de pod para [usar volumes existentes](https://docs.microsoft.com/azure/aks/azure-disk-volume) em vez de PersistentVolumeClaims (provisionamento estático).
* Implante seu aplicativo no AKS.
* Valide se o aplicativo está funcionando corretamente.
* Aponte seu tráfego ao vivo para o novo cluster AKS.

> [!IMPORTANT]
> Se optar por não desativar as gravações, você precisará replicar os dados para a nova implantação. Caso contrário, você perderá os dados que foram gravados depois de obter os instantâneos de disco.

Algumas ferramentas de código-fonte aberto podem ajudá-lo a criar discos gerenciados e a migrar volumes entre clusters kubernetes:

* [CLI do Azure extensão de cópia de disco](https://github.com/noelbundick/azure-cli-disk-copy-extension) copia e converte discos em grupos de recursos e regiões do Azure.
* A [extensão da CLI do Azure Kube](https://github.com/yaron2/azure-kube-cli) enumera volumes do ACS kubernetes e os migra para um cluster AKs.


### <a name="deployment-of-your-cluster-configuration"></a>Implantação de sua configuração de cluster

Recomendamos que você use seu pipeline de integração contínua (CI) e entrega contínua (CD) para implantar uma configuração válida em AKS. Você pode usar Azure Pipelines para [criar e implantar seus aplicativos para AKs](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/kubernetes/aks-template?view=azure-devops) clonar suas tarefas de implantação existentes e garantir que `kubeconfig` aponte para o novo cluster AKs.

Se isso não for possível, exporte as definições de recursos do cluster kubernetes existente e aplique-as ao AKS. Você pode usar `kubectl` para exportar objetos.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Neste artigo, resumimos os detalhes de migração para:

> [!div class="checklist"]
> * AKS com Standard Load Balancer e conjuntos de dimensionamento de máquinas virtuais
> * Serviços do Azure anexados existentes
> * Garantir cotas válidas
> * Alta disponibilidade e continuidade dos negócios
> * Considerações para aplicativos sem estado
> * Considerações para aplicativos com estado
> * Implantação de sua configuração de cluster
