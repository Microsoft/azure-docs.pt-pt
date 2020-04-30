---
title: Migrar para o Serviço Azure Kubernetes (AKS)
description: Migrar para o Serviço Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 9a5e2c1e36a742115ed2f5c690c81a186a86dee7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82129090"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migrar para o Serviço Azure Kubernetes (AKS)

Este artigo ajuda-o a planear e executar uma migração bem sucedida para o Serviço Azure Kubernetes (AKS). Para ajudá-lo a tomar decisões-chave, este guia fornece detalhes para a configuração recomendada atual para AKS. Este artigo não cobre todos os cenários, e se for caso disso, o artigo contém links para informações mais detalhadas para planear uma migração bem sucedida.

Este documento pode ser usado para ajudar a suportar os seguintes cenários:

* Migrar um cluster AKS apoiado por [conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) para conjuntos de [escala de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)
* Migrar um cluster AKS para usar um equilibrador de [carga SKU padrão](https://docs.microsoft.com/azure/aks/load-balancer-standard)
* Migração do Serviço de [Contentores Azure (ACS) - aposentar-se em 31](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) de janeiro de 2020 para AKS
* Migração do [motor AKS](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) para aks
* Migração de clusters kubernetes não-Azure para AKS

Ao migrar, certifique-se de que a versão Kubernetes alvo está dentro da janela suportada para AKS. Se utilizar uma versão mais antiga, pode não estar dentro da gama suportada e exigir que as versões de upgrade sejam suportadas pelo AKS. Consulte as [versões Kubernetes suportadas pela AKS](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions) para obter mais informações.

Se está a migrar para uma versão mais recente da Kubernetes, reveja a [versão kubernetes e](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)a versão envie a política de suporte.

Várias ferramentas de código aberto podem ajudar na sua migração, dependendo do seu cenário:

* [Velero](https://velero.io/) (Requer Kubernetes 1.7+)
* [Extensão CLI Azure Kube](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

Neste artigo vamos resumir detalhes da migração para:

> [!div class="checklist"]
> * AKS com balanceor de carga padrão e conjuntos de escala de máquina virtual
> * Serviços Azure existentes
> * Garantir quotas válidas
> * Alta Disponibilidade e continuidade do negócio
> * Considerações para aplicações apátridas
> * Considerações para aplicações estatais
> * Implementação da configuração do seu cluster

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS com balanceor de carga padrão e conjuntos de escala de máquina virtual

O AKS é um serviço gerido que oferece capacidades únicas com despesas de gestão mais baixas. Como resultado de um serviço gerido, deve selecionar de um conjunto de [regiões](https://docs.microsoft.com/azure/aks/quotas-skus-regions) que a AKS apoia. A transição do seu cluster existente para AKS pode exigir modificar as suas aplicações existentes para que se mantenham saudáveis no plano de controlo gerido pela AKS.

Recomendamos a utilização de clusters AKS apoiados por [conjuntos](https://docs.microsoft.com/azure/virtual-machine-scale-sets) de escala de máquinavirtual e o Equilíbrio de [Carga Padrão Azure](https://docs.microsoft.com/azure/aks/load-balancer-standard) para garantir que obtém funcionalidades como [várias piscinas](https://docs.microsoft.com/azure/aks/use-multiple-node-pools)de nós, [Zonas de Disponibilidade,](https://docs.microsoft.com/azure/availability-zones/az-overview) [gamas IP autorizadas,](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges) [Cluster Autoscaler,](https://docs.microsoft.com/azure/aks/cluster-autoscaler) [Política Azure para AKS,](https://docs.microsoft.com/azure/governance/policy/concepts/rego-for-aks)e outras novidades à medida que são lançadas.

Os clusters AKS apoiados por [Conjuntos de Disponibilidade](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) de Máquinas Virtuais carecem de suporte para muitas destas funcionalidades.

O exemplo seguinte cria um cluster AKS com uma única piscina de nó apoiada por um conjunto de escala de máquina virtual. Usa um equilibrador de carga padrão. Também permite o autoescalador de cluster na piscina do nó para o cluster e define um mínimo de *1* e nomáximo de *3* nós:

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

## <a name="existing-attached-azure-services"></a>Serviços Azure existentes

Ao migrar clusters pode ter ligado serviços azure externos. Estes não requerem recreação de recursos, mas exigirão a atualização de ligações de anteriores a novos clusters para manter a funcionalidade.

* Registo de Contentores do Azure
* Log Analytics
* Application Insights
* Gestor de Tráfego
* Conta de Armazenamento
* Bases de Dados Externas

## <a name="ensure-valid-quotas"></a>Garantir quotas válidas

Como máquinas virtuais adicionais serão implantadas na sua subscrição durante a migração, deve verificar se as suas quotas e limites são suficientes para esses recursos. Poderá ter de solicitar um aumento da [quota vCPU](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).

Pode ser necessário solicitar um aumento das quotas de [rede](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) para garantir que não esgota os IPs. Consulte as gamas de [networking e IP para](https://docs.microsoft.com/azure/aks/configure-kubenet) obter informações adicionais.

Para mais informações, consulte os limites de [subscrição e serviço do Azure.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) Para verificar as suas quotas atuais, no portal Azure, vá à lâmina de [subscrição,](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)selecione a sua subscrição e, em seguida, **selecione Usage + quotas**.

## <a name="high-availability-and-business-continuity"></a>Alta Disponibilidade e Continuidade empresarial

Se a sua aplicação não conseguir lidar com o tempo de inatividade, terá de seguir as melhores práticas para cenários de migração de alta disponibilidade.  As melhores práticas para um planeamento complexo da continuidade do negócio, recuperação de desastres e maximização do tempo de espera estão fora do âmbito deste documento.  Leia mais sobre [as melhores práticas para a continuidade do negócio e recuperação de desastres no Serviço Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) para saber mais.

Para aplicações complexas, você normalmente migrará ao longo do tempo em vez de tudo ao mesmo tempo. Isto significa que os ambientes antigos e novos podem ter de comunicar através da rede. As aplicações que `ClusterIP` utilizaram anteriormente serviços `LoadBalancer` para comunicar podem ter de ser expostas como tipo e ser devidamente protegidas.

Para completar a migração, vai querer apontar os clientes para os novos serviços que estão a decorrer na AKS. Recomendamos que redirecione o tráfego atualizando dNS para apontar para o Balancer load que se encontra em frente ao seu cluster AKS.

[O Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) pode direcionar os clientes para o cluster e instância de aplicação dekubernetes pretendidos.  Traffic Manager é um equilibrista de carga de tráfego baseado em DNS que pode distribuir tráfego de rede por regiões.  Para o melhor desempenho e redundância, direcione todo o tráfego de aplicações através do Traffic Manager antes de ir para o seu cluster AKS.  Numa implantação multicluster, os clientes devem ligar-se a um nome DNS do Gestor de Tráfego que aponte para os serviços em cada cluster AKS. Defina estes serviços utilizando pontos finais do Gestor de Tráfego. Cada ponto final é o *balanceador*de carga de serviço IP . Utilize esta configuração para direcionar o tráfego de rede do ponto final do Gestor de Tráfego numa região para o ponto final de uma região diferente.

![AKS com Gestor de Tráfego](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[O Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) é outra opção para o encaminhamento de tráfego para clusters AKS.  O Azure Front Door Service permite-lhe definir, gerir e monitorizar o encaminhamento global do tráfego da Web ao otimizar para o melhor desempenho e ativação pós-falha instantânea para uma elevada disponibilidade. 

### <a name="considerations-for-stateless-applications"></a>Considerações para aplicações apátridas

A migração de aplicações apátridas é o caso mais simples. Aplique as definições de recursos (YAML ou Helm) no novo cluster, certifique-se de que tudo funciona como esperado e redirecione o tráfego para ativar o seu novo cluster.

### <a name="considerations-for-stateful-applications"></a>Considerações para aplicações estatais

Planeje cuidadosamente a sua migração de aplicações estatais para evitar perda de dados ou tempo de inatividade inesperado.

Se utilizar ficheiros Azure, pode montar a partilha de ficheiros como um volume no novo cluster:
* [Monte Ficheiros Estáticos Azure como volume](https://docs.microsoft.com/azure/aks/azure-files-volume#mount-the-file-share-as-a-volume)

Se utilizar discos geridos pelo Azure, só pode montar o disco se não estiver ligado a qualquer VM:
* [Monte disco azul estático como volume](https://docs.microsoft.com/azure/aks/azure-disk-volume#mount-disk-as-volume)

Se nenhuma dessas abordagens funcionar, pode utilizar uma cópia de segurança e restaurar as opções:
* [Velero em Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

#### <a name="azure-files"></a>Ficheiros do Azure

Ao contrário dos discos, os Ficheiros Azure podem ser montados em vários anfitriões simultaneamente. No seu cluster AKS, o Azure e o Kubernetes não o impedem de criar uma cápsula que o seu cluster ACS ainda utiliza. Para evitar a perda de dados e comportamento inesperado, certifique-se de que os clusters não escrevem para os mesmos ficheiros ao mesmo tempo.

Se a sua aplicação puder alojar várias réplicas que apontem para a mesma quota de ficheiros, siga os passos de migração apátridas e implemente as definições de YAML para o seu novo cluster. Caso contrário, uma possível abordagem migratória envolve os seguintes passos:

* Validar que a sua aplicação está a funcionar corretamente.
* Aponte o seu tráfego ao vivo para o seu novo aglomerado AKS.
* Desligue o velho aglomerado.

Se quiser começar com uma parte vazia e fazer uma cópia [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) dos dados de origem, pode utilizar os comandos para migrar os seus dados.


#### <a name="migrating-persistent-volumes"></a>Volumes persistentes migratórios

Se estiver a migrar volumes persistentes existentes para aks, geralmente seguirá estes passos:

* Quiesce escreve para a aplicação. (Este passo é opcional e requer tempo de inatividade.)
* Tire fotos dos discos.
* Crie novos discos geridos a partir dos instantâneos.
* Criar volumes persistentes em AKS.
* Atualize as especificações do casulo para [utilizar os volumes existentes](https://docs.microsoft.com/azure/aks/azure-disk-volume) em vez de PersistentVolumeClaims (disposição estática).
* Implemente a sua aplicação para a AKS.
* Validar que a sua aplicação está a funcionar corretamente.
* Aponte o seu tráfego ao vivo para o seu novo aglomerado AKS.

> [!IMPORTANT]
> Se optar por não escrever quiesce, terá de replicar dados para a nova implementação. Caso contrário, perderá os dados que foram escritos depois de tirar as fotos do disco.

Algumas ferramentas de código aberto podem ajudá-lo a criar discos geridos e a migrar volumes entre aglomerados kubernetes:

* Cópia de extensão de [disco Azure CLI](https://github.com/noelbundick/azure-cli-disk-copy-extension) copia e converte discos em grupos de recursos e regiões azure.
* [A extensão do AZURE Kube CLI](https://github.com/yaron2/azure-kube-cli) enumera os volumes da ACS Kubernetes e migra-os para um cluster AKS.


### <a name="deployment-of-your-cluster-configuration"></a>Implementação da configuração do seu cluster

Recomendamos que utilize o seu oleoduto de Integração Contínua (CI) e Entrega Contínua (CD) para implementar uma configuração conhecida de bom para AKS. Pode utilizar os Gasodutos Azure para [construir e implementar as suas aplicações para aks](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/kubernetes/aks-template?view=azure-devops). Clone as suas tarefas de `kubeconfig` implantação existentes e certifique-se de que isso aponta para o novo cluster AKS.

Se isso não for possível, exporte definições de recursos do seu aglomerado kubernetes existente e, em seguida, aplique-as à AKS. Pode usar `kubectl` para exportar objetos.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Deslocação dos recursos existentes para outra região

Você pode querer mover o seu cluster AKS para uma [região diferente apoiada pela AKS][region-availability]. Recomendamos que crie um novo cluster na outra região e, em seguida, implemente os seus recursos e aplicações para o seu novo cluster. Além disso, se tiver algum serviço como o [Azure Dev Spaces][azure-dev-spaces] a funcionar no seu cluster AKS, também terá de instalar e configurar esses serviços no seu cluster na nova região.


Neste artigo resumimos detalhes da migração para:

> [!div class="checklist"]
> * AKS com balanceor de carga padrão e conjuntos de escala de máquina virtual
> * Serviços Azure existentes
> * Garantir quotas válidas
> * Alta Disponibilidade e continuidade do negócio
> * Considerações para aplicações apátridas
> * Considerações para aplicações estatais
> * Implementação da configuração do seu cluster


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/