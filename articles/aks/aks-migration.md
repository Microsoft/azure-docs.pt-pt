---
title: Migrar para o Serviço Azure Kubernetes (AKS)
description: Migrar para o Serviço Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 9a5e2c1e36a742115ed2f5c690c81a186a86dee7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82129090"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migrar para o Serviço Azure Kubernetes (AKS)

Este artigo ajuda-o a planear e executar uma migração bem sucedida para o Serviço Azure Kubernetes (AKS). Para ajudá-lo a tomar decisões chave, este guia fornece detalhes para a configuração recomendada atual para AKS. Este artigo não cobre todos os cenários, e, se for caso disso, o artigo contém ligações a informações mais detalhadas para planear uma migração bem sucedida.

Este documento pode ser usado para ajudar a suportar os seguintes cenários:

* Migração de um cluster AKS apoiado por [conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) para [conjuntos de escala de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)
* Migrar um cluster AKS para usar um [balanceador de carga SKU standard](https://docs.microsoft.com/azure/aks/load-balancer-standard)
* Migração do Serviço de [Contentores Azure (ACS) - reforma 31 de janeiro de 2020](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) para AKS
* Migrando do [motor AKS](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) para aKS
* Migrando de clusters kubernetes não-Azure para AKS

Ao migrar, certifique-se de que a versão target Kubernetes está dentro da janela suportada para AKS. Se utilizar uma versão mais antiga, pode não estar dentro da gama suportada e exigir que as versões de atualização sejam suportadas pela AKS. Consulte [as versões Kubernetes suportadas pela AKS](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions) para obter mais informações.

Se está a migrar para uma versão mais recente de Kubernetes, reveja a [versão de Kubernetes e a versão distorça a política de suporte](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions).

Várias ferramentas de código aberto podem ajudar na sua migração, dependendo do seu cenário:

* [Velero](https://velero.io/) (Requer Kubernetes 1.7+)
* [Extensão CLI de Azure Kube](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

Neste artigo vamos resumir os detalhes da migração para:

> [!div class="checklist"]
> * AKS com balanceador de carga padrão e conjuntos de balança de máquinas virtuais
> * Serviços Azure existentes anexados
> * Garantir quotas válidas
> * Alta Disponibilidade e continuidade de negócios
> * Considerações para aplicações apátridas
> * Considerações para aplicações estatais
> * Implementação da sua configuração de cluster

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS com balanceador de carga padrão e conjuntos de balança de máquinas virtuais

AKS é um serviço gerido que oferece capacidades únicas com menor carga de gestão. Como resultado de ser um serviço gerido, você deve selecionar de um conjunto de [regiões](https://docs.microsoft.com/azure/aks/quotas-skus-regions) que a AKS apoia. A transição do seu cluster existente para a AKS pode exigir modificar as suas aplicações existentes para que se mantenham saudáveis no plano de controlo gerido pela AKS.

Recomendamos a utilização de clusters AKS apoiados por [conjuntos de balanças de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machine-scale-sets) e o [Balanceador de Carga Padrão Azure](https://docs.microsoft.com/azure/aks/load-balancer-standard) para garantir que obtém funcionalidades como [múltiplas piscinas de nós,](https://docs.microsoft.com/azure/aks/use-multiple-node-pools) [Zonas de Disponibilidade,](https://docs.microsoft.com/azure/availability-zones/az-overview) [Intervalos IP autorizados,](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges) [Cluster Autoscaler,](https://docs.microsoft.com/azure/aks/cluster-autoscaler) [Política de Azure para AKS,](https://docs.microsoft.com/azure/governance/policy/concepts/rego-for-aks)e outras novidades à medida que são lançadas.

Os clusters AKS apoiados por [Conjuntos de Disponibilidade de Máquinas Virtuais](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) carecem de suporte para muitas destas funcionalidades.

O exemplo a seguir cria um cluster AKS com um único conjunto de nós apoiado por um conjunto de balança de máquina virtual. Usa um equilibrador de carga padrão. Também permite o autoescalador de cluster na piscina de nó para o cluster e define um mínimo de *1* e máximo de *3* nóns:

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

## <a name="existing-attached-azure-services"></a>Serviços Azure existentes anexados

Ao migrar clusters, poderá ter ligado serviços externos da Azure. Estes não requerem recreação de recursos, mas vão exigir a atualização de ligações de anteriores a novos clusters para manter a funcionalidade.

* Registo de Contentores do Azure
* Log Analytics
* Application Insights
* Gestor de Tráfego
* Conta de Armazenamento
* Bases de Dados Externas

## <a name="ensure-valid-quotas"></a>Garantir quotas válidas

Como máquinas virtuais adicionais serão implantadas na sua subscrição durante a migração, deve verificar se as suas quotas e limites são suficientes para estes recursos. Poderá ter de solicitar um aumento da [quota vCPU](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).

Poderá ter de solicitar um aumento das [quotas da Rede](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) para garantir que não esgote os IPs. Consulte [as gamas de networking e IP para AKS](https://docs.microsoft.com/azure/aks/configure-kubenet) para obter informações adicionais.

Para mais informações, consulte [os limites de subscrição e serviço da Azure.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) Para verificar as suas quotas atuais, no portal Azure, vá à lâmina de [subscrições,](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)selecione a sua subscrição e, em seguida, selecione **Usage + quotas**.

## <a name="high-availability-and-business-continuity"></a>Alta Disponibilidade e Continuidade empresarial

Se a sua aplicação não conseguir lidar com o tempo de inatividade, terá de seguir as melhores práticas para cenários de migração de alta disponibilidade.  As melhores práticas para o planeamento complexo da continuidade das empresas, a recuperação de desastres e a maximização do tempo de vida estão fora do âmbito deste documento.  Leia mais sobre [as melhores práticas para a continuidade do negócio e recuperação de desastres no Serviço Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) para saber mais.

Para aplicações complexas, você normalmente migrará ao longo do tempo em vez de todos ao mesmo tempo. Isto significa que os ambientes antigos e novos podem precisar de comunicar através da rede. As aplicações que anteriormente utilizavam `ClusterIP` serviços para comunicar podem ter de ser expostas como tipo `LoadBalancer` e ser protegidas adequadamente.

Para completar a migração, você vai querer apontar os clientes para os novos serviços que estão em execução na AKS. Recomendamos que redirecione o tráfego atualizando o DNS para apontar para o Balanceador de Carga que se encontra em frente ao seu cluster AKS.

[O Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) pode direcionar os clientes para o cluster e instância de aplicação de Kubernetes desejados.  Traffic Manager é um equilibrador de carga de tráfego baseado em DNS que pode distribuir tráfego de rede em regiões.  Para obter o melhor desempenho e redundância, direcione todo o tráfego de aplicações através do Traffic Manager antes de ir para o seu cluster AKS.  Numa implementação multicluster, os clientes devem ligar-se a um nome DNS do Gestor de Tráfego que aponta para os serviços em cada cluster AKS. Defina estes serviços utilizando pontos finais do Traffic Manager. Cada ponto final é o *equilibrador de carga de serviço IP*. Utilize esta configuração para direcionar o tráfego de rede do ponto final do Gestor de Tráfego numa região até ao ponto final numa região diferente.

![AKS com gestor de tráfego](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[O Serviço de Porta Frontal Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview) é outra opção para encaminhar o tráfego para clusters AKS.  O Azure Front Door Service permite-lhe definir, gerir e monitorizar o encaminhamento global do tráfego da Web ao otimizar para o melhor desempenho e ativação pós-falha instantânea para uma elevada disponibilidade. 

### <a name="considerations-for-stateless-applications"></a>Considerações para aplicações apátridas

A migração de aplicações apátridas é o caso mais simples. Aplique as definições de recursos (YAML ou Helm) no novo cluster, certifique-se de que tudo funciona como esperado e redirecione o tráfego para ativar o seu novo cluster.

### <a name="considerations-for-stateful-applications"></a>Considerações para aplicações estatais

Planeie cuidadosamente a sua migração de aplicações estatais para evitar a perda de dados ou tempo de inatividade inesperado.

Se utilizar ficheiros Azure, pode montar a partilha de ficheiros como um volume no novo cluster:
* [Monte arquivos estáticos de azul como um volume](https://docs.microsoft.com/azure/aks/azure-files-volume#mount-the-file-share-as-a-volume)

Se utilizar discos geridos Azure, só pode montar o disco se não estiver ligado a qualquer VM:
* [Monte Disco Azure Estático como volume](https://docs.microsoft.com/azure/aks/azure-disk-volume#mount-disk-as-volume)

Se nenhuma dessas abordagens funcionar, pode utilizar uma cópia de segurança e restaurar opções:
* [Velero em Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

#### <a name="azure-files"></a>Ficheiros do Azure

Ao contrário dos discos, os Ficheiros Azure podem ser montados em vários anfitriões simultaneamente. No seu cluster AKS, a Azure e a Kubernetes não o impedem de criar uma cápsula que o seu cluster ACS ainda utiliza. Para evitar a perda de dados e comportamentos inesperados, certifique-se de que os clusters não escrevem para os mesmos ficheiros ao mesmo tempo.

Se a sua aplicação pode hospedar várias réplicas que apontam para a mesma partilha de ficheiros, siga os passos de migração apátridas e implemente as definições YAML para o seu novo cluster. Caso contrário, uma possível abordagem de migração envolve as seguintes etapas:

* Validar a sua aplicação está a funcionar corretamente.
* Aponte o seu tráfego ao vivo para o seu novo cluster AKS.
* Desligue o velho aglomerado.

Se quiser começar com uma partilha vazia e fazer uma cópia dos dados de origem, pode utilizar os [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) comandos para migrar os seus dados.


#### <a name="migrating-persistent-volumes"></a>Volumes persistentes migratórios

Se estiver a migrar volumes persistentes existentes para a AKS, geralmente seguirá estes passos:

* Quiesce escreve para o pedido. (Este passo é opcional e requer tempo de inatividade.)
* Tira fotos dos discos.
* Crie novos discos geridos a partir dos instantâneos.
* Crie volumes persistentes em AKS.
* Atualizar as especificações do casulo para [utilizar volumes existentes](https://docs.microsoft.com/azure/aks/azure-disk-volume) em vez de PersistenteVolumeClaims (provisão estática).
* Implemente a sua aplicação para AKS.
* Validar a sua aplicação está a funcionar corretamente.
* Aponte o seu tráfego ao vivo para o seu novo cluster AKS.

> [!IMPORTANT]
> Se optar por não escrever quiesce, terá de replicar dados para a nova implementação. Caso contrário, perderá os dados que foram escritos depois de tirar as fotos do disco.

Algumas ferramentas de código aberto podem ajudá-lo a criar discos geridos e a migrar volumes entre clusters Kubernetes:

* [Azure CLI Disk Copy](https://github.com/noelbundick/azure-cli-disk-copy-extension) copies e converte discos em grupos de recursos e regiões de Azure.
* [A extensão CLI de Azure Kube](https://github.com/yaron2/azure-kube-cli) enumera volumes ACS Kubernetes e migra-os para um cluster AKS.


### <a name="deployment-of-your-cluster-configuration"></a>Implementação da sua configuração de cluster

Recomendamos que utilize o seu pipeline de Integração Contínua (CI) e Entrega Contínua (CD) para implantar uma configuração de bem-estar conhecida para AKS. Pode utilizar a Azure Pipelines para [construir e implementar as suas aplicações na AKS](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/kubernetes/aks-template?view=azure-devops). Clone as suas tarefas de implantação existentes e certifique-se de que `kubeconfig` aponta para o novo cluster AKS.

Se isso não for possível, exporte definições de recursos do seu cluster Kubernetes existente e, em seguida, aplique-as em AKS. Pode usar `kubectl` para exportar objetos.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Deslocação dos recursos existentes para outra região

Você pode querer mover o seu cluster AKS para uma [região diferente apoiada pela AKS][region-availability]. Recomendamos que crie um novo cluster na outra região e depois implemente os seus recursos e aplicações para o seu novo cluster. Além disso, se tiver algum serviço como [o Azure Dev Spaces][azure-dev-spaces] em execução no seu cluster AKS, também terá de instalar e configurar esses serviços no seu cluster na nova região.


Neste artigo resumimos detalhes da migração para:

> [!div class="checklist"]
> * AKS com balanceador de carga padrão e conjuntos de balança de máquinas virtuais
> * Serviços Azure existentes anexados
> * Garantir quotas válidas
> * Alta Disponibilidade e continuidade de negócios
> * Considerações para aplicações apátridas
> * Considerações para aplicações estatais
> * Implementação da sua configuração de cluster


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/