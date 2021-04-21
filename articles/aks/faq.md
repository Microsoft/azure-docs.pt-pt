---
title: Perguntas frequentes para o Serviço Azure Kubernetes (AKS)
description: Encontre respostas para algumas das perguntas comuns sobre o Serviço Azure Kubernetes (AKS).
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: references_regions
ms.openlocfilehash: f13d7a33ce1dc04700932072fe0af80a901c681f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783202"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Perguntas mais frequentes acerca do Azure Kubernetes Service (AKS)

Este artigo aborda questões frequentes sobre o Serviço Azure Kubernetes (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Quais as regiões de Azure que atualmente fornecem AKS?

Para obter uma lista completa das regiões disponíveis, consulte [as regiões AKS e a disponibilidade.][aks-regions]

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>Posso espalhar um aglomerado AKS por regiões?

N.º Os aglomerados AKS são recursos regionais e não podem abranger regiões. Consulte [as melhores práticas para a continuidade do negócio e recuperação de desastres][bcdr-bestpractices] para obter orientações sobre como criar uma arquitetura que inclua várias regiões.

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>Posso espalhar um cluster AKS através de zonas de disponibilidade?

Sim. Você pode implementar um cluster AKS em uma ou mais [zonas de disponibilidade][availability-zones] em [regiões que as suportam.][az-regions]

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Posso limitar quem tem acesso ao servidor API da Kubernetes?

Sim. Existem duas opções para limitar o acesso ao servidor API:

- Utilize [gamas IP autorizadas do servidor API][api-server-authorized-ip-ranges] se pretender manter um ponto final público para o servidor API, mas restringir o acesso a um conjunto de gamas IP fidedignas.
- Utilize [um cluster privado][private-clusters] se pretender limitar o servidor API para estar acessível *apenas* a partir da sua rede virtual.

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Posso ter diferentes tamanhos de VM num único aglomerado?

Sim, você pode usar diferentes tamanhos de máquina virtual no seu cluster AKS criando [várias piscinas de nós.][multi-node-pools]

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>As atualizações de segurança são aplicadas aos nós de agente AKS?

O Azure aplica automaticamente patches de segurança aos nós Linux no seu cluster num horário noturno. No entanto, é responsável por garantir que os nós Linux sejam reiniciados conforme necessário. Tem várias opções para reiniciar os nóns:

- Manualmente, através do portal Azure ou do Azure CLI.
- Atualizando o seu cluster AKS. O cluster atualiza os [nós de cordon e drenagem][cordon-drain] automaticamente e, em seguida, traz um novo nó on-line com a mais recente imagem Ubuntu e uma nova versão patch ou uma versão menor de Kubernetes. Para obter mais informações, consulte [atualizar um cluster AKS][aks-upgrade].
- Utilizando [a atualização da imagem do nó](node-image-upgrade.md).

### <a name="windows-server-nodes"></a>Os acenos do Servidor do Windows

Para os nós do Windows Server, o Windows Update não é executado automaticamente e aplica as atualizações mais recentes. Numa programação regular em torno do ciclo de lançamento do Windows Update e do seu próprio processo de validação, deverá realizar uma atualização no cluster e no conjunto de nós do Windows Server no seu cluster AKS. Este processo de atualização cria nós que executam a mais recente imagem e patches do Windows Server e, em seguida, remove os nós mais antigos. Para obter mais informações sobre este processo, consulte [a atualização de um conjunto de nós em AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Por que são criados dois grupos de recursos com AKS?

A AKS baseia-se numa série de recursos de infraestrutura Azure, incluindo conjuntos de escala de máquinas virtuais, redes virtuais e discos geridos. Isto permite-lhe alavancar muitas das capacidades centrais da plataforma Azure dentro do ambiente gerido de Kubernetes fornecido pela AKS. Por exemplo, a maioria dos tipos de máquinas virtuais Azure podem ser usados diretamente com reservas AKS e Azure podem ser usados para receber descontos nesses recursos automaticamente.

Para permitir esta arquitetura, cada implantação AKS abrange dois grupos de recursos:

1. Cria-se o primeiro grupo de recursos. Este grupo contém apenas o recurso de serviço Kubernetes. O fornecedor de recursos AKS cria automaticamente o segundo grupo de recursos durante a implementação. Um exemplo do segundo grupo de recursos é *MC_myResourceGroup_myAKSCluster_eastus*. Para obter informações sobre como especificar o nome deste segundo grupo de recursos, consulte a secção seguinte.
1. O segundo grupo de recursos, conhecido como grupo de *recursos de nó,* contém todos os recursos de infraestrutura associados ao cluster. Estes recursos incluem os VMs de nó de Kubernetes, networking virtual e armazenamento. Por predefinição, o grupo de recursos de nó tem um nome como *MC_myResourceGroup_myAKSCluster_eastus*. A AKS elimina automaticamente o recurso de nó sempre que o cluster é eliminado, pelo que deve ser utilizado apenas para recursos que partilhem o ciclo de vida do cluster.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Posso dar o meu próprio nome para o grupo de recursos de nó AKS?

Sim. Por padrão, a AKS nomeará o grupo de recursos de nó *MC_resourcegroupname_clustername_location*, mas também pode fornecer o seu próprio nome.

Para especificar o nome do seu próprio grupo de recursos, instale a versão de extensão Azure CLI [de pré-visualização aks-preview][aks-preview-cli] *0.3.2* ou posterior. Quando criar um cluster AKS utilizando os [az aks criar][az-aks-create] comando, use o `--node-resource-group` parâmetro e especifique um nome para o grupo de recursos. Se [utilizar um modelo de Gestor de Recursos Azure][aks-rm-template] para implantar um cluster AKS, pode definir o nome do grupo de recursos utilizando a propriedade *nodeResourceGroup.*

* O grupo de recursos secundários é automaticamente criado pelo fornecedor de recursos Azure na sua própria subscrição.
* Só pode especificar um nome de grupo de recursos personalizados quando estiver a criar o cluster.

Enquanto trabalha com o grupo de recursos de nó, lembre-se que não pode:

* Especifique um grupo de recursos existente para o grupo de recursos de nó.
* Especifique uma subscrição diferente para o grupo de recursos de nó.
* Mude o nome do grupo de recursos do nó após a criação do cluster.
* Especificar os nomes dos recursos geridos dentro do grupo de recursos de nó.
* Modifique ou elimine as tags criadas pelo Azure de recursos geridos dentro do grupo de recursos do nó. (Ver informações adicionais na secção seguinte.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Posso modificar etiquetas e outras propriedades dos recursos AKS no grupo de recursos de nó?

Se modificar ou eliminar tags criadas pelo Azure e outras propriedades de recursos no grupo de recursos de nó, poderá obter resultados inesperados, tais como erros de escala e de atualização. A AKS permite-lhe criar e modificar tags personalizadas criadas pelos utilizadores finais, e pode adicionar essas tags ao [criar uma piscina de nós.](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool) É possível que queira criar ou modificar tags personalizadas, por exemplo, para atribuir uma unidade de negócio ou um centro de custos. Isto também pode ser conseguido através da criação de Políticas Azure com âmbito no grupo de recursos geridos.

No entanto, modificar quaisquer **tags criadas pelo Azure** em recursos no âmbito do grupo de recursos de nó no cluster AKS é uma ação não apoiada, que quebra o objetivo de nível de serviço (SLO). Para mais informações, consulte [a AKS oferecer um acordo de nível de serviço?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Que controladores de admissão kubernetes suportam a AKS? Os controladores de admissão podem ser adicionados ou removidos?

A AKS suporta os seguintes [controladores de admissão:][admission-controllers]

- *Espaço nomeLifecycle*
- *LimiteRanger*
- *ServiceAccount*
- *Classe defaultstorage*
- *Padrão DeleraçãoSegundos*
- *MutatingAdmissionWebhook*
- *Validação DoWebhook daAdmission*
- *ResourceQuota*
- *PodNodeSelector*
- *PodTolerationRestriction*
- *Ampliação da Localização*

Atualmente, não é possível modificar a lista de controladores de admissão em AKS.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>Posso usar webhooks de controlador de admissão na AKS?

Sim, pode usar webhooks do controlador de admissão na AKS. Recomenda-se que exclua espaços internos de nomes AKS, que estão marcados com a **etiqueta do plano de controlo.** Por exemplo, adicionando o abaixo à configuração webhook:

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

AKS firewalls o servidor API egress para que os seus webhooks do controlador de admissão de admissão devem estar acessíveis a partir do cluster.

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>Os webhooks do controlador de admissão podem impactar o sistema kube e os espaços internos de nomeS AKS?

Para proteger a estabilidade do sistema e evitar que os controladores de admissão personalizados impactem nos serviços internos no sistema kube, o Namespace AKS dispõe de um Executor de **Admissões**, que exclui automaticamente o sistema kube e os espaços de nome internos AKS. Este serviço garante que os controladores de admissão personalizados não afetam os serviços em execução no sistema kube.

Se tiver um caso crítico de uso para ter algo implantado no sistema kube (não recomendado) que você precisa ser coberto pelo seu webhook de admissão personalizado, você pode adicionar o rótulo ou anotação abaixo para que o Executor de Admissões o ignore.

Etiqueta: ```"admissions.enforcer/disabled": "true"``` ou Anotação: ```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>O Azure Key Vault está integrado com a AKS?

AKS não está integrada de forma nativa com o Azure Key Vault. No entanto, o [fornecedor Azure Key Vault para a CSI Secrets Store][csi-driver] permite a integração direta de cápsulas Kubernetes para segredos key vault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Posso executar os contentores do Windows Server na AKS?

Sim, os contentores do Windows Server estão disponíveis em AKS. Para executar os contentores do Windows Server em AKS, cria uma piscina de nó que executa o Windows Server como o SISTEMA convidado. Os contentores do Windows Server só podem utilizar o Windows Server 2019. Para começar, consulte [Criar um cluster AKS com um conjunto de nós do Windows Server][aks-windows-cli].

O suporte do Windows Server para o conjunto de nós inclui algumas limitações que fazem parte do projeto Do Windows Server a montante em Kubernetes. Para obter mais informações sobre estas limitações, consulte [os recipientes do Windows Server em limitações AKS][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>A AKS oferece um acordo de nível de serviço?

A AKS fornece garantias SLA como um recurso de complemento opcional com [Uptime SLA][uptime-sla]. 

O SKU gratuito oferecido por padrão não tem um *Acordo* de Nível de Serviço associado, mas tem um *Objetivo* de Nível de Serviço de 99,5%. Pode acontecer que os problemas transitórios de conectividade sejam observados em caso de upgrades, nóns de subalentes pouco saudáveis, manutenção de plataformas, aplicação que sobrecarrega o Servidor API com pedidos, etc. Se a sua carga de trabalho não tolerar o reinício do Servidor API, então sugerimos que utilize o Uptime SLA.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Posso aplicar descontos de reserva Azure aos meus nós de agente AKS?

Os nós de agente AKS são faturados como máquinas virtuais Azure padrão, por isso, se você comprou [reservas Azure][reservation-discounts] para o tamanho VM que você está usando em AKS, esses descontos são automaticamente aplicados.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Posso mover/migrar o meu aglomerado entre inquilinos do Azure?

Mover o seu cluster AKS entre inquilinos não é apoiado.

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Posso mover/migrar o meu cluster entre assinaturas?

Atualmente, o movimento de clusters entre subscrições não é suportado.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Posso mover os meus clusters AKS da atual assinatura do Azure para outro?

A movimentação do seu cluster AKS e os seus recursos associados entre subscrições Azure não são suportados.

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>Posso mover o meu cluster AKS ou recursos de infraestrutura AKS para outros grupos de recursos ou renomeá-los?

Mover ou renomear o seu cluster AKS e os seus recursos associados não é suportado.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Porque é que o meu grupo está a demorar tanto tempo?

A maioria dos clusters são eliminados a pedido do utilizador; em alguns casos, especialmente quando os clientes estão trazendo o seu próprio Grupo de Recursos, ou fazendo a supressão de tarefas cross-RG pode levar tempo adicional ou falhar. Se tiver algum problema com as eliminações, verifique duas vezes se não tem fechaduras no RG, que quaisquer recursos fora do RG estão dissociados do RG, e assim por diante.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Se eu tiver pod/implantações no estado 'NodeLost' ou 'Unknown' ainda posso atualizar o meu cluster?

Podes, mas a AKS não recomenda isto. As atualizações devem ser realizadas quando o estado do cluster é conhecido e saudável.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Se eu tiver um aglomerado com um ou mais nós em estado insalubre ou desligado, posso fazer uma atualização?

Não, elimine/remova quaisquer nós num estado falhado ou de outra forma removido do cluster antes da atualização.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Corri um aglomerado apagar, mas ver o erro `[Errno 11001] getaddrinfo failed`

Mais comummente, isto é causado por utilizadores que têm um ou mais Grupos de Segurança de Rede (NSGs) ainda em uso e associados ao cluster.  Retire-os e tente apagar novamente.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Fiz um upgrade, mas agora as minhas cápsulas estão em circuitos de colisão, e as sondas de prontidão falham?

Confirme que o seu diretor de serviço não expirou.  Ver: Credenciais de atualização [de serviço AKS](./kubernetes-service-principal.md) e [AKS](./update-credentials.md).

## <a name="my-cluster-was-working-but-suddenly-cant-provision-loadbalancers-mount-pvcs-etc"></a>O meu grupo estava a funcionar, mas de repente não consegue abastecer LoadBalancers, montar PVCs, etc.?

Confirme que o seu diretor de serviço não expirou.  Ver: Credenciais de atualização [de serviço AKS](./kubernetes-service-principal.md)  e [AKS](./update-credentials.md).

## <a name="can-i-scale-my-aks-cluster-to-zero"></a>Posso escalar o meu cluster AKS para zero?
Pode parar completamente [um cluster AKS em execução,](start-stop-cluster.md)economizando nos respetivos custos de computação. Além disso, também pode optar por [escalar ou autoescalar todas as piscinas de nó ou conjuntos de `User` nós específicos](scale-cluster.md#scale-user-node-pools-to-0) para 0, mantendo apenas a configuração de cluster necessária.
Não se pode escalar diretamente [as piscinas de nós do sistema](use-system-pools.md) a zero.

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Posso utilizar as APIs de escala de máquina virtual para escalar manualmente?

Não, as operações de escala utilizando as APIs de escala de máquina virtual não são suportadas. Utilize as APIs AKS `az aks scale` ().

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-zero-nodes"></a>Posso usar conjuntos de balança de máquinas virtuais para escalar manualmente até zero nós?

Não, as operações de escala utilizando as APIs de escala de máquina virtual não são suportadas. Pode utilizar a API AKS para escalar para zero piscinas de nó de não sistema ou [parar o seu cluster.](start-stop-cluster.md)

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Posso parar ou desatribuer todos os meus VMs?

Embora a AKS tenha mecanismos de resiliência para resistir a tal config e recuperar dele, esta não é uma configuração suportada. Em vez [disso, pare o seu agrupamento.](start-stop-cluster.md)

## <a name="can-i-use-custom-vm-extensions"></a>Posso usar extensões VM personalizadas?

O agente Log Analytics é suportado porque é uma extensão gerida pela Microsoft. Caso contrário, não, a AKS é um serviço gerido, e a manipulação dos recursos da IAAS não é suportada. Para instalar componentes personalizados, utilize as APIs e mecanismos de Kubernetes. Por exemplo, utilize Os DaemonSets para instalar os componentes necessários.

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>A AKS armazena algum dado de cliente fora da região do cluster?

A funcionalidade para permitir o armazenamento de dados de clientes numa única região está atualmente disponível apenas na Região do Sudeste Asiático (Singapura) da Região De Geo pacífico asiático e Brasil Sul (Estado de São Paulo) do Brasil Geo. Para todas as outras regiões, os dados dos clientes são armazenados na Geo.

## <a name="are-aks-images-required-to-run-as-root"></a>As imagens AKS são necessárias para funcionar como raiz?

Com exceção das duas imagens seguintes, as imagens AKS não são necessárias para funcionar como raiz:

- *mcr.microsoft.com/oss/kubernetes/coredns*
- *mcr.microsoft.com/azuremonitor/containerinsights/ciprod*

## <a name="what-is-azure-cni-transparent-mode-vs-bridge-mode"></a>O que é Azure CNI Modo Transparente vs. Modo de Ponte?

A partir de v1.2.0 Azure CNI terá o modo Transparente como padrão para as implementações de RC CNI de arrendamento único. O modo transparente está a substituir o modo de ponte. Nesta secção, discutiremos mais sobre as diferenças em ambos os modos e quais são os benefícios/limitações para a utilização do modo Transparente no Azure CNI.

### <a name="bridge-mode"></a>Modo ponte

Como o nome sugere, o modo ponte Azure CNI, de uma forma "just in time", vai criar uma ponte L2 chamada "azure0". Todas as interfaces de par de `veth` pods laterais do anfitrião serão ligadas a esta ponte. Assim, Pod-Pod comunicação intra VM e o tráfego restante passa por esta ponte. A ponte em questão é um dispositivo virtual de camada 2 que por si só não pode receber ou transmitir nada a menos que você ligue um ou mais dispositivos reais a ele. Por esta razão, a eth0 do LM Linux tem de ser convertida numa ponte subordinada a "azure0". Isto cria uma topologia complexa da rede dentro do Linux VM e como um sintoma CNI teve que cuidar de outras funções de networking como a atualização do servidor DNS e assim por diante.

:::image type="content" source="media/faq/bridge-mode.png" alt-text="Topologia do modo de ponte":::

Abaixo está um exemplo de como a configuração da rota ip se parece no modo Bridge. Independentemente de quantas cápsulas o nó tem, haverá apenas duas rotas. O primeiro a dizer, todo o tráfego excluindo o local em azure0 irá para a porta de entrada padrão da sub-rede através da interface com ip "src 10.240.0.4" (que é o NODE primário IP) e o segundo dizendo "10.20.x" Pod espaço para kernel para kernel decidir.

```bash
default via 10.240.0.1 dev azure0 proto dhcp src 10.240.0.4 metric 100
10.240.0.0/12 dev azure0 proto kernel scope link src 10.240.0.4
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
root@k8s-agentpool1-20465682-1:/#
```

### <a name="transparent-mode"></a>Modo transparente
O modo transparente tem uma abordagem direta para a criação da rede Linux. Neste modo, o Azure CNI não altera quaisquer propriedades da interface eth0 no Linux VM. Esta abordagem mínima de alteração das propriedades de rede Linux ajuda a reduzir problemas complexos de casos de canto que os clusters podem enfrentar com o modo Bridge. No Modo Transparente, o Azure CNI criará e adicionará interfaces de par de pods do lado do anfitrião `veth` que serão adicionadas à rede de anfitriões. A comunicação Intra VM Pod-to-Pod é através de rotas ip que o CNI irá adicionar. Essencialmente, a comunicação Pod-to-Pod é sobre a camada 3 e o tráfego de pod é encaminhado pelas regras de encaminhamento L3.

:::image type="content" source="media/faq/transparent-mode.png" alt-text="Topologia de modo transparente":::

Abaixo está um exemplo de configuração da rota ip de modo transparente, cada interface do Pod receberá uma rota estática anexada para que o tráfego com dest IP como o Pod será enviado diretamente para a interface de par lateral do vôm. `veth`

```bash
10.240.0.216 dev azv79d05038592 proto static
10.240.0.218 dev azv8184320e2bf proto static
10.240.0.219 dev azvc0339d223b9 proto static
10.240.0.222 dev azv722a6b28449 proto static
10.240.0.223 dev azve7f326f1507 proto static
10.240.0.224 dev azvb3bfccdd75a proto static
168.63.129.16 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
169.254.169.254 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
```

### <a name="benefits-of-transparent-mode"></a>Benefícios do modo transparente

- Fornece mitigação para `conntrack` o estado de corrida paralelo DNS e evita problemas de latência DNS de 5 segundos sem a necessidade de configurar DNS locais (você ainda pode usar o nó DNS local por razões de desempenho).
- Elimina o modo de ponte CNI de latência de 5 seg iniciais, introduz hoje devido à configuração da ponte "just in time".
- Um dos casos de canto no modo ponte é que o Azure CNI não pode continuar a atualizar as listas personalizadas de servidores DNS que os utilizadores adicionam ao VNET ou NIC. Isto resulta na recolha do CNI apenas a primeira instância da lista de servidores DNS. Resolvido em modo Transparente como CNI não altera nenhuma propriedade eth0. Veja mais [aqui.](https://github.com/Azure/azure-container-networking/issues/713)
- Proporciona uma melhor manipulação do tráfego da UDP e mitigação para a tempestade de inundação da UDP quando o ARP estiver esgotado. No modo ponte, quando a ponte não conhece um endereço MAC do casulo de destino na comunicação intra-VM Pod-to-Pod, por design, isto resulta em tempestade do pacote para todas as portas. Resolvido em modo Transparente uma vez que não existem dispositivos L2 no caminho. Veja mais [aqui.](https://github.com/Azure/azure-container-networking/issues/704)
- O modo transparente funciona melhor na comunicação Intra VM Pod-to-Pod em termos de produção e latência em comparação com o modo de ponte.

## <a name="how-to-avoid-permission-ownership-setting-slow-issues-when-the-volume-has-a-lot-of-files"></a>Como evitar a propriedade de permissão definindo problemas lentos quando o volume tem um monte de ficheiros?

Tradicionalmente, se o seu casulo estiver a funcionar como um utilizador sem raízes (o que deve fazer), deve especificar um `fsGroup` contexto de segurança dentro da cápsula para que o volume possa ser legível e legível pelo Pod. Este requisito é mais detalhado [aqui.](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)

Mas um efeito colateral da definição `fsGroup` é que, cada vez que um volume é montado, Kubernetes deve recursivamente `chown()` e todos os `chmod()` ficheiros e diretórios dentro do volume - com algumas exceções notadas abaixo. Isto acontece mesmo que a propriedade em grupo do volume já corresponda ao pedido `fsGroup` – e pode ser bastante caro para volumes maiores com muitos ficheiros pequenos, o que faz com que o pod startup desemoque muito tempo. Este cenário tem sido um problema conhecido antes do v1.20 e a solução está a definir o Pod run como raiz:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

O problema foi resolvido por Kubernetes v1.20, refere [Kubernetes 1.20: Controlo Granular de Alterações de Permissão de Volume](https://kubernetes.io/blog/2020/12/14/kubernetes-release-1.20-fsgroupchangepolicy-fsgrouppolicy/) para mais detalhes.


<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az_aks_create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: ./windows-faq.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md
[private-clusters]: ./private-clusters.md
[bcdr-bestpractices]: ./operator-best-practices-multi-region.md#plan-for-multiregion-deployment
[availability-zones]: ./availability-zones.md
[az-regions]: ../availability-zones/az-region.md
[uptime-sla]: ./uptime-sla.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/
