---
title: Resolução de problemas problemas comuns do Serviço Azure Kubernetes
description: Saiba como resolver problemas e resolver problemas comuns ao utilizar o Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: troubleshooting
ms.date: 06/20/2020
ms.openlocfilehash: dcbfed4fc83b980b3e54a808406b8d27e1e6c919
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074418"
---
# <a name="aks-troubleshooting"></a>Resolução de problemas do AKS

Quando cria ou gere os clusters do Serviço Azure Kubernetes (AKS), poderá ocasionalmente encontrar problemas. Este artigo detalha alguns problemas comuns e etapas de resolução de problemas.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Em geral, onde encontro informações sobre problemas de depurar Kubernetes?

Experimente o [guia oficial para resolver os aglomerados de Kubernetes.](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)
Há também um guia de [resolução de problemas](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md), publicado por um engenheiro da Microsoft para resolução de problemas de pods, nós, clusters e outras funcionalidades.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Estou a receber um erro de "quota excedido" durante a criação ou atualização. O que devo fazer? 

 [Solicite mais núcleos.](../azure-portal/supportability/resource-manager-core-quotas-request.md)

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Qual é a definição máxima de pods por nó para AKS?

A definição máxima de pods por nó é de 30 por defeito se implementar um cluster AKS no portal Azure.
A definição máxima de pods por nó é de 110 por defeito se implementar um cluster AKS no CLI Azure. (Certifique-se de que está a utilizar a versão mais recente do Azure CLI). Esta definição pode ser alterada utilizando a `–-max-pods` bandeira no `az aks create` comando.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Estou a receber um erro insuficiente de SubnetSize enquanto implanto um cluster AKS com rede avançada. O que devo fazer?

Este erro indica que uma sub-rede em uso para um cluster já não tem IPs disponíveis no seu CIDR para uma atribuição bem sucedida de recursos. Para os clusters Kubenet, o requisito é espaço IP suficiente para cada nó no cluster. Para os agrupamentos CNI Azure, a exigência é espaço IP suficiente para cada nó e vagem no cluster.
Leia mais sobre o [design da Azure CNI para atribuir IPs a cápsulas](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

Estes erros também são surgidos em [AKS Diagnostics](./concepts-diagnostics.md) que proativamente surgem problemas como um tamanho de sub-rede insuficiente.

Os seguintes três (3) casos causam um erro de tamanho da sub-rede insuficiente:

1. Escala AKS ou escala de nodepool AKS
   1. Se utilizar o Kubenet, isto ocorre quando `number of free IPs in the subnet` o é **inferior** ao `number of new nodes requested` .
   1. Se utilizar o Azure CNI, isto ocorre quando o `number of free IPs in the subnet` é **inferior** ao `number of nodes requested times (*) the node pool's --max-pod value` .

1. Atualização AKS ou atualização de nodepool AKS
   1. Se utilizar o Kubenet, isto ocorre quando `number of free IPs in the subnet` o é **inferior** ao `number of buffer nodes needed to upgrade` .
   1. Se utilizar o Azure CNI, isto ocorre quando o `number of free IPs in the subnet` é **inferior** ao `number of buffer nodes needed to upgrade times (*) the node pool's --max-pod value` .
   
   Por padrão, os clusters AKS definem um valor de aumento máximo (tampão de upgrade) de um (1), mas este comportamento de upgrade pode ser personalizado definindo o [valor máximo de um conjunto de nós](upgrade-cluster.md#customize-node-surge-upgrade-preview) que irá aumentar o número de IPs disponíveis necessários para completar uma atualização.

1. AKS criar ou adicionar AKS Nodepool
   1. Se utilizar o Kubenet, isto ocorre quando `number of free IPs in the subnet` o é **inferior** ao `number of nodes requested for the node pool` .
   1. Se utilizar o Azure CNI, isto ocorre quando o `number of free IPs in the subnet` é **inferior** ao `number of nodes requested times (*) the node pool's --max-pod value` .

A seguinte mitigação pode ser tomada através da criação de novas sub-redes. A permissão para criar uma nova sub-rede é necessária para mitigação devido à incapacidade de atualizar a gama CIDR de uma sub-rede existente.

1. Reconstruir uma nova sub-rede com uma gama CIDR maior suficiente para os objetivos de operação:
   1. Crie uma nova sub-rede com uma nova gama não sobreposta desejada.
   1. Crie uma nova rede na nova sub-rede.
   1. As cápsulas de drenagem da antiga nodepool que reside na antiga sub-rede a serem substituídas.
   1. Apague a antiga sub-rede e a velha nodepool.

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>A minha cápsula está presa no modo CrashLoopBackOff. O que devo fazer?

Pode haver várias razões para a cápsula estar presa nesse modo. Pode investigar:

* A própria cápsula, `kubectl describe pod <pod-name>` utilizando.
* Os troncos, `kubectl logs <pod-name>` utilizando.

Para obter mais informações sobre como resolver problemas com a cápsula, consulte [as aplicações da Debug](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-receiving-tcp-timeouts-when-using-kubectl-or-other-third-party-tools-connecting-to-the-api-server"></a>Estou a receber `TCP timeouts` quando uso ou `kubectl` outras ferramentas de terceiros que se conectam ao servidor API
A AKS dispõe de aviões de controlo HA que escalam verticalmente de acordo com o número de núcleos para garantir os seus Objetivos de Nível de Serviço (SLOs) e Acordos de Nível de Serviço (SLAs). Se estiver a experimentar o tempo de cronometragem das ligações, consulte o seguinte:

- **Todos os seus comandos da API estão a cronometrar consistentemente ou apenas alguns?** Se forem apenas alguns, a sua `tunnelfront` cápsula ou `aks-link` vagem, responsável pela comunicação do avião de controlo de nós ->, pode não estar em funcionamento. Certifique-se de que os nós que hospedam esta cápsula não são sobreutilizá-lo ou sob stress. Considere movê-los para a sua própria [ `system` piscina de nós.](use-system-pools.md)
- **Abriu todas as portas necessárias, FQDNs e IPs notados na [AKS restringir os docs de tráfego de saídas?](limit-egress-traffic.md)** Caso contrário, várias chamadas de comandos podem falhar.
- **O seu IP atual é coberto por [Gamas Autorizadas API IP?](api-server-authorized-ip-ranges.md)** Se estiver a utilizar esta funcionalidade e o seu IP não estiver incluído nos intervalos, as suas chamadas serão bloqueadas. 
- **Tem um cliente ou aplicação a vazar chamadas para o servidor API?** Certifique-se de que utiliza relógios em vez de receber chamadas frequentes e que as suas aplicações de terceiros não estão a vazar tais chamadas. Por exemplo, um bug na batedeira Istio faz com que uma nova ligação do relógio API Server seja criada sempre que um segredo é lido internamente. Como este comportamento acontece num intervalo regular, as ligações do relógio acumulam-se rapidamente e, eventualmente, fazem com que o Servidor API fique sobrecarregado independentemente do padrão de escala. https://github.com/istio/istio/issues/19481
- **Tem muitos lançamentos nos seus lançamentos de leme?** Este cenário pode fazer com que tanto o leme utilizem demasiada memória nos nós, como uma grande quantidade de `configmaps` , o que pode causar picos desnecessários no servidor API. Considere configurar `--history-max` `helm init` e alavancar o novo Helm 3. Mais detalhes sobre as seguintes questões: 
    - https://github.com/helm/helm/issues/4821
    - https://github.com/helm/helm/issues/3500
    - https://github.com/helm/helm/issues/4543
- **[O tráfego interno entre nós está a ser bloqueado?](#im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout)**

## <a name="im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout"></a>Estou a `TCP timeouts` receber, tal como. `dial tcp <Node_IP>:10250: i/o timeout`

Estes intervalos podem estar relacionados com o tráfego interno entre nós bloqueados. Verifique se este tráfego não está a ser bloqueado, como por exemplo por [grupos de segurança](concepts-security.md#azure-network-security-groups) de rede na sub-rede para os nós do seu cluster.

## <a name="im-trying-to-enable-role-based-access-control-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Estou a tentar ativar Role-Based Controlo de Acesso (RBAC) num cluster existente. Como posso fazer isto?

Permitir o controlo de acesso baseado em funções (RBAC) nos clusters existentes não é suportado neste momento, deve ser definido ao criar novos clusters. O RBAC é ativado por padrão ao utilizar a versão CLI, Portal ou API mais tarde do que `2020-03-01` .

## <a name="i-created-a-cluster-with-rbac-enabled-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Criei um aglomerado com o RBAC ativado e agora vejo muitos avisos no painel de instrumentos de Kubernetes. O painel funcionava sem avisos. O que devo fazer?

A razão para os avisos é que o cluster tem RBAC habilitado e o acesso ao painel é agora restrito por padrão. Em geral, esta abordagem é uma boa prática porque a exposição padrão do dashboard a todos os utilizadores do cluster pode levar a ameaças à segurança. Se ainda quiser ativar o painel de instrumentos, siga os passos [desta publicação de blog](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Não consigo obter registos usando registos de kubectl ou não posso ligar-me ao servidor API. Estou a receber "Erro do servidor: recuo de marcação de erro: marcação tcp...". O que devo fazer?

Certifique-se de que as portas 22, 9000 e 1194 estão abertas para ligar ao servidor API. Verifique se o `tunnelfront` `aks-link` ou o casulo está a funcionar no espaço de nomes *do sistema kube* utilizando o `kubectl get pods --namespace kube-system` comando. Se não for, a eliminação forçada da cápsula e recomeçará.

## <a name="im-getting-tls-client-offered-only-unsupported-versions-from-my-client-when-connecting-to-aks-api-what-should-i-do"></a>Estou a receber `"tls: client offered only unsupported versions"` do meu cliente quando estou a ligar-me à AKS API. O que devo fazer?

A versão TLS suportada mínima em AKS é TLS 1.2.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Estou a tentar melhorar ou escalar e estou a ter um `"Changing property 'imageReference' is not allowed"` erro. Como é que resolvo este problema?

Podes estar a cometer este erro porque modificaste as etiquetas nos nós dos agentes dentro do cluster AKS. Modificar ou eliminar tags e outras propriedades de recursos no grupo de recursos MC_* pode levar a resultados inesperados. A alteração dos recursos no âmbito do grupo MC_* no cluster AKS quebra o objetivo de nível de serviço (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Estou a receber erros de que o meu cluster está em estado de falha e que a atualização ou escala não funcionará até que seja corrigido.

*Esta assistência de resolução de problemas é direcionada a partir de https://aka.ms/aks-cluster-failed*

Este erro ocorre quando os clusters entram num estado falhado por múltiplas razões. Siga os passos abaixo para resolver o seu estado falhado do seu cluster antes de voltar a tentar a operação anteriormente falhada:

1. Até que o aglomerado esteja fora do `failed` estado, `upgrade` e as `scale` operações não vão ter sucesso. As questões e resoluções comuns incluem:
    * Escalonamento com **quota de computação insuficiente (CRP).** Para resolver, em primeiro lugar, o seu cluster volta a um estado estável dentro da quota. Em seguida, siga estas [medidas para solicitar um aumento da quota de cálculo](../azure-portal/supportability/resource-manager-core-quotas-request.md) antes de tentar escalar novamente para além dos limites iniciais de quota.
    * Dimensionamento de um cluster com redes avançadas e **recursos insuficientes de sub-rede (networking).** Para resolver, em primeiro lugar, o seu cluster volta a um estado estável dentro da quota. Em seguida, siga [estas medidas para solicitar um aumento da quota de recursos](../azure-resource-manager/templates/error-resource-quota.md#solution) antes de tentar escalar novamente para além dos limites iniciais de quota.
2. Uma vez resolvida a causa subjacente à falha de atualização, o seu cluster deve estar num estado de sucesso. Uma vez verificado um estado bem sucedido, recandiduça a operação original.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade"></a>Estou a receber erros ao tentar atualizar ou escalar o meu cluster está a ser atualizado ou falhou na atualização.

*Esta assistência de resolução de problemas é direcionada a partir de https://aka.ms/aks-pending-upgrade*

 Não pode ter um cluster ou piscina de nó simultaneamente upgrade e escala. Em vez disso, cada tipo de operação deve ser preenchido no recurso-alvo antes do próximo pedido sobre esse mesmo recurso. Como resultado, as operações são limitadas quando ocorrem ou se tentam operações de atualização ou escala ativas. 

Para ajudar a diagnosticar o problema, corra para recuperar o `az aks show -g myResourceGroup -n myAKSCluster -o table` estado detalhado no seu cluster. Com base no resultado:

* Se o cluster estiver a atualizar ativamente, aguarde até que a operação termine. Se tiver sido bem sucedido, volte a tentar a operação anteriormente falhada.
* Se o cluster tiver falhado na atualização, siga os passos descritos na secção anterior.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Posso mudar o meu cluster para uma subscrição diferente ou a minha assinatura com o meu cluster para um novo inquilino?

Se mudou o seu cluster AKS para uma subscrição diferente ou a subscrição do cluster para um novo inquilino, o cluster não funcionará devido à falta de permissões de identidade do cluster. **A AKS não suporta a movimentação** de clusters através de subscrições ou inquilinos devido a este constrangimento.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Estou a receber erros ao tentar usar funcionalidades que requerem conjuntos de escala de máquina virtual

*Esta assistência para a resolução de problemas é dirigida a partir de aka.ms/aks-vmss-enablement*

Pode receber erros que indiquem que o seu cluster AKS não está num conjunto de escala de máquina virtual, tal como o seguinte exemplo:

**AgentPool `<agentpoolname>` definiu a escala automática como ativado, mas não está em conjuntos de escala de máquina virtual**

Características como o autoescalador de cluster ou várias piscinas de nós requerem conjuntos de escala de máquina virtual como `vm-set-type` .

Siga os passos *antes de iniciar* os passos no doc apropriado para criar corretamente um cluster AKS:

* [Utilize o autoescalador de cluster](cluster-autoscaler.md)
* [Criar e utilizar várias piscinas de nó](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Que restrições de nomeação são aplicadas para recursos e parâmetros AKS?

*Esta assistência para a resolução de problemas é dirigida a partir de aka.ms/aks-naming-rules*

As restrições de nomeação são implementadas tanto pela plataforma Azure como pela AKS. Se um nome de recurso ou parâmetro quebrar uma destas restrições, é devolvido um erro que lhe pede que forneça uma entrada diferente. Aplicam-se as seguintes diretrizes comuns de nomeação:

* Os nomes dos agrupamentos devem ser de 1-63 caracteres. Os únicos caracteres permitidos são letras, números, traços e sublinhados. O primeiro e último personagem deve ser uma letra ou um número.
* O nome do grupo*MC_* de recursos AKS Node/MC_ combina nome de grupo de recursos e nome de recurso. A sintaxe autogerada `MC_resourceGroupName_resourceName_AzureRegion` não deve ser superior a 80 chars. Se necessário, reduza o comprimento do nome do grupo de recursos ou o nome do cluster AKS. Também pode [personalizar o nome do grupo de recursos de nó](cluster-configuration.md#custom-resource-group-name)
* O *dnsPrefix* deve começar e terminar com valores alfanuméricos e deve estar entre 1-54 caracteres. Os caracteres válidos incluem valores alfanuméricos e hífens (-). O *dnsPrefix* não pode incluir caracteres especiais como um período (.).
* Os nomes do AKS Node Pool devem ser todos minúsculos e ser caracteres de 1-11 para piscinas de nóleiros linux e 1-6 caracteres para piscinas de nó de janelas. O nome deve começar com uma letra e os únicos caracteres permitidos são letras e números.
* O *nome de administração-utilizador*, que define o nome de utilizador do administrador para os nós Linux, deve começar com uma letra, pode conter apenas letras, números, hífenes e sublinhados, e tem um comprimento máximo de 64 caracteres.

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Estou a receber erros ao tentar criar, atualizar, escalar, eliminar ou atualizar o cluster, essa operação não é permitida à medida que outra operação está em andamento.

*Esta assistência para a resolução de problemas é dirigida a partir de aka.ms/aks-pending-operation*

As operações de fragmentação são limitadas quando uma operação anterior ainda está em curso. Para recuperar um estado detalhado do seu aglomerado, use o `az aks show -g myResourceGroup -n myAKSCluster -o table` comando. Use o seu próprio grupo de recursos e o nome de cluster AKS, conforme necessário.

Com base na saída do estado do cluster:

* Se o cluster estiver em qualquer estado de provisionamento que não *seja Bem sucedido* ou *Falhado,* aguarde até que a operação *(Upgrade / Upgrade / Criação / Produção / Eliminação / Eliminação / Migração)* termine. Quando a operação anterior tiver terminado, relemisse a sua última operação de cluster.

* Se o cluster tiver uma atualização falhada, siga os passos delineados Estou a receber erros de que o [meu cluster está em estado de falha e que a atualização ou dimensionamento não funcionará até que seja corrigido.](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed)

## <a name="received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster"></a>Recebi um erro a dizer que o meu diretor de serviço não foi encontrado ou é inválido quando tento criar um novo cluster.

Ao criar um cluster AKS, requer um principal de serviço ou identidade gerida para criar recursos em seu nome. A AKS pode automaticamente criar um novo diretor de serviço no tempo de criação do cluster ou receber um existente. Ao utilizar um automaticamente criado, o Azure Ative Directory precisa de o propagar a todas as regiões para que a criação tenha sucesso. Quando a propagação demorar muito tempo, o cluster falhará na validação para criar, uma vez que não consegue encontrar um diretor de serviço disponível para o fazer. 

Utilize as seguintes soluções alternativas para esta questão:
* Utilize um principal de serviço existente, que já se propagou através de regiões e existe para passar para a AKS no cluster criar tempo.
* Se utilizar scripts de automatização, adicione atrasos de tempo entre a criação principal do serviço e a criação de clusterS AKS.
* Se utilizar o portal Azure, volte às definições do cluster durante a criação e volte a tentar a página de validação após alguns minutos.

## <a name="im-getting-aadsts7000215-invalid-client-secret-is-provided-when-using-aks-api-what-should-i-do"></a>Estou a receber quando estou a `"AADSTS7000215: Invalid client secret is provided."` usar a API da AKS. O que devo fazer?

Isto deve-se geralmente à caducidade das principais credenciais de serviço. [Atualize as credenciais para um cluster AKS.](update-credentials.md)

## <a name="i-cant-access-my-cluster-api-from-my-automationdev-machinetooling-when-using-api-server-authorized-ip-ranges-how-do-i-fix-this-problem"></a>Não posso aceder ao meu cluster API a partir da minha máquina/ferramenta de automação/dev ao usar gamas IP autorizadas pelo servidor API. Como é que resolvo este problema?

Isto requer `--api-server-authorized-ip-ranges` incluir os sistemas ip(s) ou IP de sistemas de automação/dev/ferramentas que estão a ser utilizados. Consulte a secção "Como encontrar o meu IP" no [acesso seguro ao servidor API utilizando intervalos de endereço IP autorizados](api-server-authorized-ip-ranges.md).

## <a name="im-unable-to-view-resources-in-kubernetes-resource-viewer-in-azure-portal-for-my-cluster-configured-with-api-server-authorized-ip-ranges-how-do-i-fix-this-problem"></a>Não consigo ver recursos no visualizador de recursos de Kubernetes no portal Azure para o meu cluster configurado com gamas IP autorizadas pelo servidor API. Como é que resolvo este problema?

O [visualizador de recursos Kubernetes](kubernetes-portal.md) requer `--api-server-authorized-ip-ranges` que inclua acesso para o computador cliente local ou gama de endereços IP (a partir do qual o portal está a ser navegado). Consulte a secção "Como encontrar o meu IP" no [acesso seguro ao servidor API utilizando intervalos de endereço IP autorizados](api-server-authorized-ip-ranges.md).

## <a name="im-receiving-errors-after-restricting-egress-traffic"></a>Estou a receber erros depois de restringir o tráfego de saídas.

Ao restringir o tráfego de saída de um cluster AKS, são [necessárias e opcionais regras de](limit-egress-traffic.md) saída recomendadas/regras de rede e regras de aplicação FQDN/aplicação para AKS. Se as suas definições estiverem em conflito com qualquer uma `kubectl` destas regras, certos comandos não funcionarão corretamente. Também pode ver erros ao criar um cluster AKS.

Verifique se as suas definições não estão em conflito com nenhuma das portas de saída recomendadas ou opcionais recomendadas ou regras de FQDN/aplicação.

## <a name="im-receiving-429---too-many-requests-errors"></a>Estou a receber erros de "429 - Muitos Pedidos" 

Quando um cluster kubernetes em Azure (AKS ou não) faz uma escala frequente para cima/para baixo ou utiliza o autoescalador de cluster (CA), essas operações podem resultar num grande número de chamadas HTTP que, por sua vez, excedem a quota de subscrição atribuída levando à falha. Os erros vão parecer

```
Service returned an error. Status=429 Code=\"OperationNotAllowed\" Message=\"The server rejected the request because too many requests have been received for this subscription.\" Details=[{\"code\":\"TooManyRequests\",\"message\":\"{\\\"operationGroup\\\":\\\"HighCostGetVMScaleSet30Min\\\",\\\"startTime\\\":\\\"2020-09-20T07:13:55.2177346+00:00\\\",\\\"endTime\\\":\\\"2020-09-20T07:28:55.2177346+00:00\\\",\\\"allowedRequestCount\\\":1800,\\\"measuredRequestCount\\\":2208}\",\"target\":\"HighCostGetVMScaleSet30Min\"}] InnerError={\"internalErrorCode\":\"TooManyRequestsReceived\"}"}
```

Estes erros de estrangulamento são descritos em detalhe [aqui](../azure-resource-manager/management/request-limits-and-throttling.md) e [aqui](../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md)

A recomunicação da AKS Engineering Team é garantir que está a executar a versão pelo menos 1.18.x, que contém muitas melhorias. Mais detalhes podem ser encontrados nestas melhorias [aqui](https://github.com/Azure/AKS/issues/1413) e [aqui.](https://github.com/kubernetes-sigs/cloud-provider-azure/issues/247)

Dado que estes erros de estrangulamento são medidos ao nível da subscrição, podem ainda acontecer se:
- Existem aplicações de terceiros a fazer pedidos GET (por exemplo. aplicações de monitorização, etc...). A recomendação é reduzir a frequência destas chamadas.
- Há um monte de clusters AKS /nodepools no VMSS. A recomendação habitual é ter menos de 20-30 clusters numa determinada subscrição.


## <a name="azure-storage-and-aks-troubleshooting"></a>Armazenamento Azure e resolução de problemas AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Quais são as versões estáveis recomendadas de Kubernetes para o disco Azure? 

| Versão Kubernetes | Versão recomendada |
|--|:--:|
| 1.12 | 1.12.9 ou mais tarde |
| 1.13 | 1.13.6 ou mais tarde |
| 1.14 | 1.14.2 ou mais tarde |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>WaitForAttach falhou para O Disco Azure: análise "/dev/disk/azure/scsi1/lun1": sintaxe inválida

Na versão 1.10 de Kubernetes, mountVolume.WaitForAttach pode falhar com uma montagem em disco Azure.

No Linux, poderá ver um erro de formato incorreto do DevicePath. Por exemplo:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

No Windows, poderá ver um erro de número do DevicePath (LUN) errado. Por exemplo:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Esta questão foi corrigida nas seguintes versões de Kubernetes:

| Versão Kubernetes | Versão fixa |
|--|:--:|
| 1.10 | 1.10.2 ou mais tarde |
| 1.11 | 1.11.0 ou mais tarde |
| 1.12 e mais tarde | N/D |


### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Falha ao definir uid e gid em mountOptions para disco Azure

O Disco Azure utiliza o sistema de ficheiros ext4,xfs por predefinição e montagem Opções como uid=x,gid=x não podem ser definidas na hora de montagem. Por exemplo, se tentasse definir mountOptions uid=999,gid=999, veria um erro como:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

Pode atenuar o problema fazendo uma das opções:

* [Configure o contexto de segurança para uma cápsula,](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) definindo uid em runAsUser e gid no fsGroup. Por exemplo, a seguinte definição definirá o pod run como raiz, tornando-o acessível a qualquer ficheiro:

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

  >[!NOTE]
  > Uma vez que o gid e o uid são montados como raiz ou 0 por defeito. Se o gid ou o uid forem definidos como não-raiz, por exemplo 1000, kubernetes usará `chown` para alterar todos os diretórios e ficheiros sob esse disco. Esta operação pode ser morosa e pode tornar a montagem do disco muito lenta.

* Utilize `chown` initContainers para definir gid e uid. Por exemplo:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Falha no disquete do disco Azure que conduz a potencial problema de condição de raça e lista de discos de dados inválidos

Quando um Disco Azure não se desprende, volta a tentar até seis vezes para desprender o disco utilizando recuo exponencial. Também irá conter um bloqueio de nível de nó na lista de discos de dados durante cerca de 3 minutos. Se a lista de discos for atualizada manualmente durante esse tempo, fará com que a lista de discos mantida pelo bloqueio de nível de nó seja obsoleta e cause instabilidade no nó.

Esta questão foi corrigida nas seguintes versões de Kubernetes:

| Versão Kubernetes | Versão fixa |
|--|:--:|
| 1.12 | 1.12.9 ou mais tarde |
| 1.13 | 1.13.6 ou mais tarde |
| 1.14 | 1.14.2 ou mais tarde |
| 1.15 e mais tarde | N/D |

Se estiver a utilizar uma versão de Kubernetes que não tem a correção para este problema e o seu nó tiver uma lista de discos obsoletos, pode atenuar ao separar todos os discos não existentes do VM como uma operação a granel. **Separar individualmente os discos não existentes pode falhar.**

### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Grande número de Discos Azure causa fixação/desprendimento lento

Quando os números de operações de fixação/desprendimento do disco Azure que visam um único nó VM sejam superiores a 10, ou superiores a 3 quando direcionam uma única piscina de escala virtual, podem ser mais lentos do que o esperado, uma vez que são feitos sequencialmente. Esta questão é uma limitação conhecida e não há soluções alternativas neste momento. [Produto de voz do utilizador para suportar o encaixe paralelo/desprendimento para além do número.](https://feedback.azure.com/forums/216843-virtual-machines/suggestions/40444528-vmss-support-for-parallel-disk-attach-detach-for)

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Falha no disquete do disco Azure que conduz a um potencial VM de nó em estado falhado

Em alguns casos de borda, um disquete do disco Azure pode falhar parcialmente e deixar o VM do nó num estado falhado.

Esta questão foi corrigida nas seguintes versões de Kubernetes:

| Versão Kubernetes | Versão fixa |
|--|:--:|
| 1.12 | 1.12.10 ou mais tarde |
| 1.13 | 1.13.8 ou mais tarde |
| 1.14 | 1.14.4 ou mais tarde |
| 1.15 e mais tarde | N/D |

Se estiver a utilizar uma versão de Kubernetes que não tenha a correção para este problema e o seu nó estiver num estado falhado, pode atenuar atualizando manualmente o estado de VM utilizando um dos seguintes:

* Para um cluster baseado em conjunto de disponibilidade:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* Para um cluster baseado em VMSS:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Ficheiros Azure e resolução de problemas da AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Quais são as versões estáveis recomendadas de Kubernetes para ficheiros Azure?
 
| Versão Kubernetes | Versão recomendada |
|--|:--:|
| 1.12 | 1.12.6 ou mais tarde |
| 1.13 | 1.13.4 ou mais tarde |
| 1.14 | 1.14.0 ou mais tarde |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Quais são as opções de montagem predefinidas ao utilizar ficheiros Azure?

Definições recomendadas:

| Versão Kubernetes | valor fileMode e dirMode|
|--|:--:|
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 e mais tarde | 0777 |

As opções de montagem podem ser especificadas no objeto da classe de armazenamento. O exemplo a seguir define *0777:*

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Algumas definições de *montagem* úteis adicionais:

* *mfsymlinks* farão Azure Files montar (cifs) suporte ligações simbólicas
* *O nobrl* evitará o envio de pedidos de bloqueio de alcance byte para o servidor. Esta definição é necessária para certas aplicações que rompem com as fechaduras de alcance obrigatório do estilo CIFS. A maioria dos servidores cifs ainda não suportam pedidos de bloqueios de alcance de byte de aconselhamento. Se não utilizar *nobrl,* as aplicações que rompem com as fechaduras de alcance obrigatórias do estilo CIFs podem causar mensagens de erro semelhantes a:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Erro "não podia alterar permissões" ao utilizar ficheiros Azure

Ao executar o PostgreSQL no plugin Azure Files, poderá ver um erro semelhante a:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Este erro é causado pelo plugin Azure Files utilizando o protocolo cifs/SMB. Ao utilizar o protocolo cifs/SMB, as permissões de ficheiros e diretórios não puderam ser alteradas após a montagem.

Para resolver este problema, utilize *o subPata* juntamente com o plugin do Disco Azure. 

> [!NOTE] 
> Para o tipo de disco ext3/4, existe um diretório perdido+encontrado após o disco ser formatado.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>A azure Files tem alta latência em comparação com O Disco Azure ao manusear muitos ficheiros pequenos

Em alguns casos, tal como o manuseamento de muitos ficheiros pequenos, pode sentir uma elevada latência quando utilizar ficheiros Azure quando comparado com o Disco Azure.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Erro ao permitir o acesso permitir o acesso a partir da rede selecionada na conta de armazenamento

Se *ativar o acesso a partir de* uma rede selecionada numa conta de armazenamento que seja utilizada para o provisionamento dinâmico em AKS, terá um erro quando a AKS criar uma partilha de ficheiros:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Este erro deve-se ao facto de o *controlador devolume persistente* da Kubernetes não estar na rede escolhida ao definir permitir o *acesso a partir de uma rede selecionada*.

Pode atenuar o problema utilizando [o fornecimento estático com ficheiros Azure](azure-files-volume.md).

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Ficheiros Azure falham em remontar no pod do Windows

Se um pod do Windows com um suporte de Ficheiros Azure for eliminado e, em seguida, programado para ser recriado no mesmo nó, o suporte falhará. Esta falha deve-se à falha do `New-SmbGlobalMapping` comando, uma vez que o suporte dos Ficheiros Azure já está montado no nó.

Por exemplo, pode ver um erro semelhante a:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Esta questão foi corrigida nas seguintes versões de Kubernetes:

| Versão Kubernetes | Versão fixa |
|--|:--:|
| 1.12 | 1.12.6 ou mais tarde |
| 1.13 | 1.13.4 ou mais tarde |
| 1.14 e mais tarde | N/D |

### <a name="azure-files-mount-fails-because-of-storage-account-key-changed"></a>Montagem de Ficheiros Azure falha por causa da chave da conta de armazenamento alterada

Se a chave da sua conta de armazenamento tiver sido alterada, poderá ver falhas de montagem de ficheiros Azure.

Pode atenuar atualizando manualmente o `azurestorageaccountkey` campo num segredo de ficheiro Azure com a chave de conta de armazenamento codificada na base64.

Para codificar a chave da sua conta de armazenamento na base64, pode utilizar `base64` . Por exemplo:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Para atualizar o seu ficheiro secreto Azure, utilize `kubectl edit secret` . Por exemplo:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Após alguns minutos, o nó do agente voltará a tentar o suporte do Ficheiro Azure com a chave de armazenamento atualizada.


### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>O autoescalador de cluster não escala com erro falhado na fixação dos tamanhos do grupo de nó

Se o seu autoescalador de cluster não estiver a aumentar/descer e vir um erro como o seguinte nos [registos de autoescala][view-master-logs]do cluster .

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Este erro deve-se a uma condição de corrida de autoescalador de cluster a montante. Neste caso, o cluster autoscaler termina com um valor diferente daquele que está realmente no cluster. Para sair deste estado, desative e reative o [autoescalador][cluster-autoscaler]de cluster .

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>Acessório de disco lento, GetAzureDiskLun demora 10 a 15 minutos e recebe um erro

Nas versões Kubernetes **com mais de 1.15.0,** pode receber um erro como **Error WaitForAttach Não pode encontrar Lun para o disco**.  A solução para esta questão é esperar aproximadamente 15 minutos e voltar a tentar.

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md

### <a name="why-do-upgrades-to-kubernetes-116-fail-when-using-node-labels-with-a-kubernetesio-prefix"></a>Por que as atualizações para Kubernetes 1.16 falham ao usar etiquetas de nó com um prefixo kubernetes.io

A partir de Kubernetes [1.16](https://v1-16.docs.kubernetes.io/docs/setup/release/notes/) [apenas um subconjunto definido de etiquetas com o prefixo kubernetes.io](https://github.com/kubernetes/enhancements/blob/master/keps/sig-auth/0000-20170814-bounding-self-labeling-kubelets.md#proposal) pode ser aplicado pelo kubelet aos nós. A AKS não pode remover etiquetas ativas em seu nome sem consentimento, pois pode causar tempo de inatividade para cargas de trabalho impactadas.

Como resultado, para atenuar isto pode:

1. Atualize o seu plano de controlo de cluster para 1,16 ou mais
2. Adicione um novo nodepoool em 1.16 ou superior sem as etiquetas de kubernetes.io não suportadas
3. Eliminar o nodepool mais antigo

A AKS está a investigar a capacidade de mutar rótulos ativos num nodepool para melhorar esta mitigação.