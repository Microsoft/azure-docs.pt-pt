---
title: Personalize a configuração do nó para piscinas de nó Azure Kubernetes (AKS) (pré-visualização)
description: Saiba como personalizar a configuração nos nós e piscinas de nós de cluster Azure Kubernetes Service (AKS).
ms.service: container-service
ms.topic: article
ms.date: 12/03/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 589081149d08983d3cd5a4a8822873f5a6cfca0e
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99559444"
---
# <a name="customize-node-configuration-for-azure-kubernetes-service-aks-node-pools-preview"></a>Personalizar a configuração do nó para piscinas de nó Azure Kubernetes (AKS) (pré-visualização)

A personalização da configuração do nó permite-lhe configurar ou sintonizar as definições do seu sistema operativo (OS) ou os parâmetros de kubelet para corresponder às necessidades das cargas de trabalho. Quando criar um cluster AKS ou adicionar uma piscina de nó ao seu cluster, pode personalizar um subconjunto de definições de SO e kubelet comumente usadas. Para configurar as definições para além deste subconjunto, [utilize um conjunto de daemon para personalizar as configurações necessárias sem perder o suporte AKS para os seus nós](support-policies.md#shared-responsibility).

## <a name="register-the-customnodeconfigpreview-preview-feature"></a>Registar a `CustomNodeConfigPreview` funcionalidade de pré-visualização

Para criar um cluster AKS ou um conjunto de nó que possa personalizar os parâmetros de kubelet ou as definições de SO, deve ativar a bandeira de `CustomNodeConfigPreview` funcionalidade na sua subscrição.

Registe o `CustomNodeConfigPreview` pavilhão de funcionalidades utilizando o comando [de registo de recursos az,][az-feature-register] como mostra o seguinte exemplo:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "CustomNodeConfigPreview"
```

Demora alguns minutos para que o estado seja *apresentado.* Verifique o estado de registo utilizando o comando [da lista de recursos AZ:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomNodeConfigPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, reaprovi o registo do fornecedor de recursos *Microsoft.ContainerService* utilizando o comando [de registo do fornecedor az:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Para criar um cluster AKS ou um conjunto de nós que possa personalizar os parâmetros de kubelet ou as definições de SO, você precisa da mais recente extensão Azure CLI *de pré-visualização aks.* Instale a extensão Azure CLI *de pré-visualização aks* utilizando o comando [de adicionar extensão az.][az-extension-add] Ou instale quaisquer atualizações disponíveis utilizando o comando [de atualização de extensão az.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

## <a name="use-custom-node-configuration"></a>Utilize a configuração do nó personalizado

### <a name="kubelet-custom-configuration"></a>Configuração personalizada de Kubelet

Os parâmetros kubelet suportados e os valores aceites estão listados abaixo.

| Parâmetro | Valores/intervalo permitidos | Predefinição | Description |
| --------- | ----------------------- | ------- | ----------- |
| `cpuManagerPolicy` | nenhuma, estática | nenhum | A política estática permite que os contentores em [cápsulas garantidas](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/) com CPU inteiro solicitem acesso a CPUs exclusivos no nó. |
| `cpuCfsQuota` | TRUE, false | true |  Ativar/Desativar a aplicação das quotas do CPU CFS para os contentores que especificam os limites da CPU. | 
| `cpuCfsQuotaPeriod` | Intervalo em milissegundos (ms) | `100ms` | Define o valor do período de quota do CPU CFS. | 
| `imageGcHighThreshold` | 0-100 | 85 | A percentagem de utilização do disco após a qual a recolha de lixo de imagem é sempre executada. Utilização mínima do disco que **irá** desencadear a recolha de lixo. Para desativar a recolha de lixo de imagem, fixado em 100. | 
| `imageGcLowThreshold` | 0-100, não mais alto que `imageGcHighThreshold` | 80 | A percentagem de utilização do disco antes da qual a recolha de lixo de imagem nunca é executada. Uso mínimo de disco que **pode** desencadear a recolha de lixo. |
| `topologyManagerPolicy` | nenhum, o melhor esforço, restrito, single-numa-nó | nenhum | Otimize o alinhamento do nó NUMA, veja mais [aqui.](https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/) Apenas kubernetes v1.18+. |
| `allowedUnsafeSysctls` | `kernel.shm*`, `kernel.msg*`, `kernel.sem`, `fs.mqueue.*`, `net.*` | Nenhum | Lista permitida de sysctls inseguros ou padrões sysctl inseguros. | 

### <a name="linux-os-custom-configuration"></a>Configuração personalizada linux OS

As definições de SO suportadas e os valores aceites são listados abaixo.

#### <a name="file-handle-limits"></a>Limites de alça de arquivo

Quando se está a servir muito tráfego, é comum que o tráfego que está a servir venha de um grande número de ficheiros locais. Pode ajustar as definições de kernel abaixo e os limites incorporados para permitir lidar mais, à custa de alguma memória do sistema.

| Definição | Valores/intervalo permitidos | Predefinição | Description |
| ------- | ----------------------- | ------- | ----------- |
| `fs.file-max` | 8192 - 12000500 | 709620 | O número máximo de cabos de ficheiros que o kernel Linux irá atribuir, aumentando este valor pode aumentar o número máximo de ficheiros abertos permitidos. |
| `fs.inotify.max_user_watches` | 781250 - 2097152 | 1048576 | Número máximo de relógios de ficheiros permitidos pelo sistema. Cada *relógio* tem cerca de 90 bytes num núcleo de 32 bits, e cerca de 160 bytes num núcleo de 64 bits. | 
| `fs.aio-max-nr` | 65536 - 6553500 | 65536 | O aio-nr mostra o número atual de pedidos de io assíncronos. aio-max-nr permite-lhe alterar o valor máximo aio-nr pode crescer. |
| `fs.nr_open` | 8192 - 20000500 | 1048576 | O número máximo de cabos de ficheiros que um processo pode atribuir. |


#### <a name="socket-and-network-tuning"></a>Tomada e afinação da rede

Para os nós de agente, que se espera que lidem com um número muito grande de sessões simultâneas, pode utilizar o subconjunto de TCP e opções de rede abaixo que pode ajustar por piscina de nó. 

| Definição | Valores/intervalo permitidos | Predefinição | Description |
| ------- | ----------------------- | ------- | ----------- |
| `net.core.somaxconn` | 4096 - 3240000 | 16384 | Número máximo de pedidos de ligação que podem ser solicitados para qualquer tomada de audição. Um limite superior para o valor do parâmetro de atraso passou para a função [de escuta(2).](http://man7.org/linux/man-pages/man2/listen.2.html) Se o argumento de atraso é maior do que `somaxconn` o, então é silenciosamente truncado a este limite.
| `net.core.netdev_max_backlog` | 1000 - 3240000 | 1000 | Número máximo de pacotes, em fila no lado de INPUT, quando a interface recebe pacotes mais rápido do que o kernel pode processá-los. |
| `net.core.rmem_max` | 212992 - 134217728 | 212992 | O tamanho máximo do tampão de tomada de receção em bytes. |
| `net.core.wmem_max` | 212992 - 134217728 | 212992 | O tamanho máximo do tampão da tomada em bytes. | 
| `net.core.optmem_max` | 20480 - 4194304 | 20480 | Tamanho máximo do tampão auxiliar (tampão de memória de opção) permitido por tomada. A memória de opção de tomada é utilizada em alguns casos para armazenar estruturas extra relacionadas com a utilização da tomada. | 
| `net.ipv4.tcp_max_syn_backlog` | 128 - 3240000 | 16384 | O número máximo de pedidos de ligação em fila que ainda não receberam um reconhecimento do cliente de ligação. Se este número for ultrapassado, o núcleo começará a deixar cair os pedidos. |
| `net.ipv4.tcp_max_tw_buckets` | 8000 - 1440000 | 32768 | Número máximo de `timewait` tomadas mantidas pelo sistema simultaneamente. Se este número for ultrapassado, a tomada de espera de tempo é imediatamente destruída e o aviso é impresso. |
| `net.ipv4.tcp_fin_timeout` | 5 - 120 | 60 | O tempo de duração de uma ligação órfã (já não referenciada por qualquer aplicação) permanecerá no estado FIN_WAIT_2 antes de ser abortado no final local. |
| `net.ipv4.tcp_keepalive_time` | 30 - 432000 | 7200 | Com que frequência a TCP envia `keepalive` mensagens quando `keepalive` está ativada. |
| `net.ipv4.tcp_keepalive_probes` | 1 - 15 | 9 | Quantas `keepalive` sondas a TCP envia, até decidir que a ligação está avariada. |
| `net.ipv4.tcp_keepalive_intvl` | 1 - 75 | 75 | Com que frequência as sondas são enviadas. Multiplicada por `tcp_keepalive_probes` ela compensa o tempo de matar uma ligação que não responde, depois de as sondas terem começado. | 
| `net.ipv4.tcp_tw_reuse` | 0 ou 1 | 0 | Deixe reutilizar `TIME-WAIT` tomadas para novas ligações quando estiver a salvo do ponto de vista do protocolo. | 
| `net.ipv4.ip_local_port_range` | Primeiro: 1024 - 60999 e Último: 32768 - 65000] | Primeiro: 32768 e Último: 60999 | A gama portuária local que é utilizada pelo tráfego TCP e UDP para escolher o porto local. Composto por dois números: O primeiro número é o primeiro porto local permitido para o tráfego de TCP e UDP no nó do agente, o segundo é o último número de porto local. | 
| `net.ipv4.neigh.default.gc_thresh1`|  128 - 80000 | 4096 | Número mínimo de entradas que podem estar na cache ARP. A recolha de lixo não será ativada se o número de entradas estiver abaixo desta definição. | 
| `net.ipv4.neigh.default.gc_thresh2`|  512 - 90000 | 8192 | Número máximo suave de entradas que podem estar na cache ARP. Esta configuração é indiscutivelmente a mais importante, uma vez que a recolha de lixo ARP será desencadeada cerca de 5 segundos depois de atingir este máximo macio. |
| `net.ipv4.neigh.default.gc_thresh3`|  1024 - 100000 | 16384 | Número máximo de entradas na cache ARP. |
| `net.netfilter.nf_conntrack_max` | 131072 - 589824 | 131072 | `nf_conntrack` é um módulo que rastreia as entradas de ligação para NAT dentro do Linux. O `nf_conntrack` módulo utiliza uma tabela de haxixe para registar o registo de *ligação estabelecido* do protocolo TCP. `nf_conntrack_max` é o número máximo de nós na tabela de haxixe, ou seja, o número máximo de ligações suportadas pelo `nf_conntrack` módulo ou o tamanho da tabela de rastreio de ligação. | 
| `net.netfilter.nf_conntrack_buckets` | 65536 - 147456 | 65536 | `nf_conntrack` é um módulo que rastreia as entradas de ligação para NAT dentro do Linux. O `nf_conntrack` módulo utiliza uma tabela de haxixe para registar o registo de *ligação estabelecido* do protocolo TCP. `nf_conntrack_buckets` é do tamanho da mesa de haxixe. | 

#### <a name="worker-limits"></a>Limites de trabalho

Tal como os limites do descritor de ficheiros, o número de trabalhadores ou fios que um processo pode criar são limitados tanto por uma definição de núcleo como pelos limites do utilizador. O limite de utilizador em AKS é ilimitado. 

| Definição | Valores/intervalo permitidos | Predefinição | Description |
| ------- | ----------------------- | ------- | ----------- |
| `kernel.threads-max` | 20 - 513785 | 55601 | Os processos podem girar os fios dos trabalhadores. O número máximo de todos os fios que podem ser criados é definido com a definição de núcleo `kernel.threads-max` . | 

#### <a name="virtual-memory"></a>Memória virtual

As definições abaixo podem ser usadas para sintonizar o funcionamento do subsistema de memória virtual (VM) do kernel Linux e `writeout` dos dados sujos para o disco.

| Definição | Valores/intervalo permitidos | Predefinição | Description |
| ------- | ----------------------- | ------- | ----------- |
| `vm.max_map_count` |  65530 - 262144 | 65530 | Este ficheiro contém o número máximo de áreas de mapa de memória que um processo pode ter. As áreas do mapa de memória são usadas como um efeito colateral da chamada `malloc` , diretamente por `mmap` , e também ao carregar `mprotect` `madvise` bibliotecas partilhadas. | 
| `vm.vfs_cache_pressure` | 1 - 500 | 100 | Este valor percentual controla a tendência do núcleo para recuperar a memória, que é usada para caching de diretórios e objetos inode. |
| `vm.swappiness` | 0 - 100 | 60 | Este controlo é usado para definir quão agressivo o núcleo irá trocar páginas de memória. Valores mais elevados aumentarão a agressividade, os valores mais baixos diminuem a quantidade de swap. Um valor de 0 instrui o núcleo a não iniciar a troca até que a quantidade de páginas livres e apoiadas por ficheiros seja inferior à marca de água alta numa zona. | 
| `swapFileSizeMB` | 1 MB - Tamanho do [disco temporário](../virtual-machines/managed-disks-overview.md#temporary-disk) (/dev/sdb) | Nenhum | SwapFileSizeMB especifica o tamanho em MB de um ficheiro swap será criado nos nós do agente a partir deste conjunto de nós. | 
| `transparentHugePageEnabled` | `always`, `madvise`, `never` | `always` | [Transparent Hugepages](https://www.kernel.org/doc/html/latest/admin-guide/mm/transhuge.html#admin-guide-transhuge) é uma funcionalidade de kernel Linux destinada a melhorar o desempenho, utilizando mais eficientemente o hardware de mapeamento de memória do seu processador. Quando ativado, o núcleo tenta alocar `hugepages` sempre que possível e qualquer processo Linux receberá páginas de 2-MB se a `mmap` região estiver naturalmente alinhada com 2 MB. Em certos casos, quando `hugepages` são ativados em todo o sistema, as aplicações podem acabar por alocar mais recursos de memória. Uma aplicação pode `mmap` ser uma grande região, mas apenas tocar 1 byte da mesmo, nesse caso uma página de 2-MB pode ser atribuída em vez de uma página 4k sem razão aparente. Este cenário é o motivo pelo qual é possível desativar `hugepages` o sistema em todo o sistema ou apenas tê-los dentro das `MADV_HUGEPAGE madvise` regiões. | 
| `transparentHugePageDefrag` | `always`, `defer`, `defer+madvise`, `madvise`, `never` | `madvise` | Este valor controla se o núcleo deve fazer uso agressivo da compactação da memória para tornar mais `hugepages` disponível. | 

> [!IMPORTANT]
> Para facilitar a pesquisa e a legibilidade, as definições de SO são apresentadas neste documento pelo seu nome, mas devem ser adicionadas ao ficheiro json de configuração ou à API AKS utilizando a convenção de [capitalização camelCase](/dotnet/standard/design-guidelines/capitalization-conventions).

Criar um `kubeletconfig.json` ficheiro com os seguintes conteúdos:

```json
{
 "cpuManagerPolicy": "static",
 "cpuCfsQuota": true,
 "cpuCfsQuotaPeriod": "200ms",
 "imageGcHighThreshold": 90,
 "imageGcLowThreshold": 70,
 "topologyManagerPolicy": "best-effort",
 "allowedUnsafeSysctls": [
  "kernel.msg*",
  "net.*"
],
 "failSwapOn": false
}
```
Criar um `linuxosconfig.json` ficheiro com os seguintes conteúdos:

```json
{
 "transparentHugePageEnabled": "madvise",
 "transparentHugePageDefrag": "defer+madvise",
 "swapFileSizeMB": 1500,
 "sysctls": {
  "netCoreSomaxconn": 163849,
  "netIpv4TcpTwReuse": true,
  "netIpv4IpLocalPortRange": "32000 60000"
 }
}
```

Crie um novo cluster especificando as configurações de kubelet e SO utilizando os ficheiros JSON criados no passo anterior. 

> [!NOTE]
> Quando criar um cluster, pode especificar a configuração do kubelet, a configuração do SO ou ambas. Se especificar uma configuração ao criar um cluster, apenas os nós no conjunto de nós iniciais terão essa configuração aplicada. Quaisquer definições não configuradas no ficheiro JSON conservarão o valor predefinido.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json --linux-os-config ./linuxosconfig.json
```

Adicione um novo conjunto de nós especificando os parâmetros Kubelet utilizando o ficheiro JSON que criou.

> [!NOTE]
> Quando adicionar uma piscina de nó a um cluster existente, pode especificar a configuração do kubelet, a configuração do SO ou ambas. Se especificar uma configuração ao adicionar uma piscina de nós, apenas os nós na nova piscina de nós terão essa configuração aplicada. Quaisquer definições não configuradas no ficheiro JSON conservarão o valor predefinido.

```azurecli
az aks nodepool add --name mynodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json
```

## <a name="next-steps"></a>Passos seguintes

- Saiba [como configurar o seu cluster AKS](cluster-configuration.md).
- Saiba como [atualizar as imagens do nó](node-image-upgrade.md) no seu cluster.
- Consulte [o Upgrade de um cluster Azure Kubernetes Service (AKS)](upgrade-cluster.md) para aprender a atualizar o seu cluster para a versão mais recente do Kubernetes.
- Consulte a lista de [perguntas frequentes sobre aKS](faq.md) para encontrar respostas para algumas perguntas comuns da AKS.

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
[upgrade-cluster]: upgrade-cluster.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade


<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why
