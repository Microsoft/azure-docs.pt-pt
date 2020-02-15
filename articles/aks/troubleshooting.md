---
title: Problemas de resolução de problemas problemas comuns do serviço Azure Kubernetes
description: Aprenda a resolver problemas comuns ao utilizar o Serviço Azure Kubernetes (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: saudas
ms.openlocfilehash: 663a1dc597493c7b534b54eab7ccc4bed0ff0e11
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209227"
---
# <a name="aks-troubleshooting"></a>Resolução de problemas da AKS

Quando cria ou gere os clusters do Serviço Azure Kubernetes (AKS), pode ocasionalmente encontrar problemas. Este artigo detalha alguns problemas comuns e passos de resolução de problemas.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Em geral, onde encontro informações sobre depuração dos problemas de Kubernetes?

Experimente o [guia oficial para resolver os aglomerados kubernetes.](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)
Há também um guia de resolução de [problemas,](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)publicado por um engenheiro da Microsoft para cápsulas de resolução de problemas, nós, clusters e outras funcionalidades.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Estou a ter um erro de "quota excedida" durante a criação ou atualização. O que devo fazer? 

Precisa [pedir núcleos.](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Qual é a definição máxima de pods por nó para AKS?

A definição máxima de pods por nó é de 30 por padrão se implementar um cluster AKS no portal Azure.
A definição máxima de pods por nó é de 110 por padrão se implantar um cluster AKS no Azure CLI. (Certifique-se de que está a utilizar a versão mais recente do Azure CLI). Esta definição predefinida pode ser alterada utilizando a bandeira `–-max-pods` no comando `az aks create`.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Estou a ter um erro subnetSize insuficiente enquanto implanto um cluster AKS com networking avançado. O que devo fazer?

Se o Azure CNI (rede avançada) for utilizado, o AKS atribui endereços IP baseados nos "max-pods" por nó configurado. Com base nas cápsulas max configuradas por nó, o tamanho da sub-rede deve ser maior do que o produto do número de nós e da vagem máxima por ajuste do nó. A seguinte equação descreve isto:

Tamanho da sub-rede > número de nós no cluster (tendo em conta os requisitos futuros de escala) * cápsulas max por conjunto de nó.

Para mais informações, consulte o [Plano IP endereçando para o seu cluster](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>A minha cápsula está presa no modo CrashLoopBackOff. O que devo fazer?

Pode haver várias razões para a cápsula estar presa nesse modo. Pode investigar:

* A vagem em si, utilizando `kubectl describe pod <pod-name>`.
* Os registos, utilizando `kubectl logs <pod-name>`.

Para obter mais informações sobre como resolver problemas com a cápsula, consulte [as aplicações Debug](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Estou a tentar ativar o RBAC num aglomerado existente. Como posso fazer isto?

Infelizmente, permitir o controlo de acesso baseado em papéis (RBAC) em clusters existentes não é suportado neste momento. Deve criar explicitamente novos agrupamentos. Se utilizar o CLI, o RBAC está ativado por defeito. Se utilizar o portal AKS, um botão de alternância para ativar o RBAC está disponível no fluxo de trabalho de criação.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Criei um cluster com RBAC habilitado usando o Azure CLI com padrão ou o portal Azure, e agora vejo muitos avisos no tablier Kubernetes. O tablier funcionava sem avisos. O que devo fazer?

A razão para as advertências no painel de instrumentos é que o cluster está agora ativado com RBAC e o acesso ao mesmo foi desativado por padrão. Em geral, esta abordagem é uma boa prática porque a exposição padrão do painel de instrumentos a todos os utilizadores do cluster pode levar a ameaças à segurança. Se ainda quiser ativar o dashboard, siga os passos [nesta publicação de blog](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Não consigo ligar-me ao painel. O que devo fazer?

A forma mais fácil de aceder ao seu serviço fora do cluster é executar `kubectl proxy`, que proxies solicitam enviados para o seu porto de acolhimento local 8001 para o servidor Kubernetes API. A partir daí, o servidor API pode proxy para o seu serviço: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Se não vir o painel kubernetes, verifique se a cápsula `kube-proxy` está a funcionar no espaço de `kube-system` nome. Se não estiver em estado de funcionamento, apague a cápsula e recomeçará.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Não consigo registos usando registos de kubectl ou não consigo ligar-me ao servidor DaPI. Estou a receber "Error from server: error dial dial backend: dial tcp...". O que devo fazer?

Certifique-se de que o grupo de segurança da rede padrão não é modificado e que tanto a porta 22 como 9000 estão abertas para ligação ao servidor API. Verifique se a cápsula `tunnelfront` está a funcionar no espaço de nome do *sistema kube* utilizando o comando `kubectl get pods --namespace kube-system`. Se não for, força a eliminação da cápsula e recomeçará.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Estou a tentar atualizar ou escalar e estou a receber uma "mensagem: Mudar de propriedade 'imageReference' não é permitido". Como posso resolver este problema?

Podes estar a ter este erro porque modificaste as etiquetas nos nós do agente dentro do cluster AKS. Modificar e apagar tags e outras propriedades de recursos no grupo de recursos MC_* pode levar a resultados inesperados. Modificar os recursos no âmbito do grupo MC_* no cluster AKS quebra o objetivo de nível de serviço (OIt).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Estou a receber erros de que o meu cluster está em estado falhado e que a modernização ou escalonamento não funcionará até que seja corrigido.

*Esta assistência de resolução de problemas é dirigida a partir de https://aka.ms/aks-cluster-failed*

Este erro ocorre quando os aglomerados entram num estado falhado por múltiplas razões. Siga os passos abaixo para resolver o seu estado falhado antes de voltar a tentar a operação anteriormente falhada:

1. Até que o aglomerado esteja fora `failed` estado, `upgrade` e `scale` operações não terão sucesso. As questões e resoluções fundamentais comuns incluem:
    * Escalacom **quota de cálculo insuficiente (CRP).** Para resolver, primeiro escala o seu cluster de volta para um estado de meta estável dentro da quota. Em seguida, siga estas [medidas para solicitar um aumento](../azure-portal/supportability/resource-manager-core-quotas-request.md) da quota de cálculo antes de tentar escalar novamente para além dos limites iniciais de quota.
    * Dimensionar um cluster com redes avançadas e **recursos de sub-rede insuficientes (networking).** Para resolver, primeiro escala o seu cluster de volta para um estado de meta estável dentro da quota. Em seguida, siga [estas medidas para solicitar um aumento](../azure-resource-manager/templates/error-resource-quota.md#solution) da quota de recursos antes de tentar escalar novamente para além dos limites iniciais de quota.
2. Uma vez resolvida a causa subjacente à falha de atualização, o seu cluster deve estar num estado bem sucedido. Uma vez verificado um estado bem sucedido, tente novamente a operação original.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Estou a receber erros ao tentar atualizar ou escalar esse estado, o meu cluster está a ser atualizado ou falhou na atualização.

*Esta assistência de resolução de problemas é dirigida a partir de https://aka.ms/aks-pending-upgrade*

As operações de upgrade e escala num cluster com uma única piscina de nó ou um cluster com [piscinas de nó múltiplas](use-multiple-node-pools.md) são mutuamente exclusivas. Não é possível ter um cluster ou piscina de nó simultaneamente atualizado e escala. Em vez disso, cada tipo de funcionamento deve ser preenchido no recurso-alvo antes do próximo pedido sobre esse mesmo recurso. Como resultado, as operações são limitadas quando as operações de atualização ou escala ativas estão a ocorrer ou a tentar e subsequentemente falhadas. 

Para ajudar a diagnosticar o problema, `az aks show -g myResourceGroup -n myAKSCluster -o table` para obter um estado detalhado no seu cluster. Com base no resultado:

* Se o cluster estiver a atualizar ativamente, aguarde até que a operação termine. Se tiver sucesso, volte a tentar a operação anteriormente falhada.
* Se o cluster tiver falhado na atualização, siga os passos delineados na secção anterior.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Posso mudar o meu cluster para uma subscrição diferente ou a minha assinatura com o meu cluster para um novo inquilino?

Se tiver mudado o seu cluster AKS para uma subscrição diferente ou o cluster possuir a subscrição de um novo inquilino, o cluster perderá a funcionalidade devido à perda de atribuições de papéis e direitos de diretores de serviço. A AKS não suporta a deslocação de clusters através de **subscrições ou inquilinos** devido a esta restrição.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Estou a receber erros a tentar usar funcionalidades que requerem conjuntos de escala de máquinas virtuais

*Esta assistência de resolução de problemas é dirigida a partir de aka.ms/aks-vmss-enablement*

Pode receber erros que indiquem que o seu cluster AKS não se encontra num conjunto de escala de máquina virtual, como o seguinte exemplo:

**AgentPool 'agentpool' definiu a escala automática como ativado, mas não está em conjuntos de escala de máquina virtual**

Para utilizar funcionalidades como o autoscaler do cluster ou várias piscinas de nó, devem ser criados clusters AKS que utilizem conjuntos de escala de máquinas virtuais. Os erros são devolvidos se tentar utilizar funcionalidades que dependem de conjuntos de escala de máquinas virtuais e direciona-se para um conjunto AKS de escala de máquina seletiva regular e não virtual.

Siga os passos *antes de iniciar* os passos no doc apropriado para criar corretamente um cluster AKS:

* [Utilize o autoscaler do cluster](cluster-autoscaler.md)
* [Criar e usar várias piscinas de nó](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Que restrições de nomeação são aplicadas aos recursos e parâmetros aks?

*Esta assistência de resolução de problemas é direcionada a partir de aka.ms/aks-naming-rules*

As restrições de nomeação são implementadas tanto pela plataforma Azure como pela AKS. Se um nome de recurso ou parâmetro quebrar uma destas restrições, é devolvido um erro que lhe pede fornecer uma entrada diferente. Aplicam-se as seguintes diretrizes comuns de nomeação:

* Os nomes do cluster devem ser 1-63 caracteres. Os únicos caracteres permitidos são letras, números, traços e sublinhados. O primeiro e último personagem deve ser uma carta ou um número.
* O nome do grupo de recursos *MC_* AKS combina nome de grupo de recursos e nome de recursos. A sintaxe gerada automaticamente de `MC_resourceGroupName_resourceName_AzureRegion` não deve ser superior a 80 chars. Se necessário, reduza o comprimento do nome do grupo de recursos ou o nome do cluster AKS.
* O *dnsPrefix* deve começar e terminar com valores alfanuméricos e deve estar entre 1-54 caracteres. Os caracteres válidos incluem valores alfanuméricos e hífenes (-). O *dnsPrefix* não pode incluir caracteres especiais como um período (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Estou a receber erros ao tentar criar, atualizar, escalar, eliminar ou atualizar o cluster, que a operação não é permitida, uma vez que outra operação está em curso.

*Esta assistência de resolução de problemas é direcionada a partir de aka.ms/aks-pending-operation*

As operações de cluster são limitadas quando uma operação anterior ainda está em curso. Para recuperar um estado detalhado do seu cluster, use o comando `az aks show -g myResourceGroup -n myAKSCluster -o table`. Use o seu próprio grupo de recursos e o nome do cluster AKS, conforme necessário.

Com base na saída do estado do cluster:

* Se o cluster estiver em qualquer estado de provisionamento que não *seja bem sucedido* ou *falhado,* aguarde até que a operação (*Upgrade / Upgrade / Criação / Escala/ Aparação / Atualização / Migração)* termine. Quando a operação anterior estiver concluída, reexperimente a sua última operação de cluster.

* Se o cluster tiver uma atualização falhada, siga os passos delineados, [estou a receber erros de que o meu cluster está em estado falhado e a atualização ou escalonamento não funcionará até que seja corrigido.](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed)

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Estou a receber erros que o meu diretor de serviço não foi encontrado quando tento criar um novo aglomerado sem passar por um existente.

Ao criar um cluster AKS, é necessário um diretor de serviço para criar recursos em seu nome. A AKS oferece a capacidade de ter um novo criado no tempo de criação do cluster, mas isso requer que o Azure Ative Directory propague totalmente o novo diretor de serviço num prazo razoável para que o cluster tenha sucesso na criação. Quando esta propagação demorar demasiado tempo, o cluster falhará a validação para criar, uma vez que não encontra um principal de serviço disponível para o fazer. 

Utilize as seguintes suposições para o seguinte:
1. Utilize um diretor de serviço existente que já se propagou por regiões e existe para passar para AKS no cluster criar tempo.
2. Se utilizar scripts de automação, adicione atrasos de tempo entre a criação principal de serviço e a criação de cluster AKS.
3. Se utilizar o portal Azure, volte às definições do cluster durante a criação e volte a tentar a página de validação após alguns minutos.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Estou a receber erros depois de restringir o meu tráfego de esgress.

Ao restringir o tráfego de saída de um cluster AKS, são [necessárias e opcionais](limit-egress-traffic.md) portas/regras de rede recomendadas e regras de aplicação FQDN/aplicação para AKS. Se as suas definições estiverem em conflito com qualquer uma destas regras, poderá não ser capaz de executar determinados comandos `kubectl`. Também pode ver erros ao criar um cluster AKS.

Verifique se as suas definições não estão em conflito com nenhuma das portas/regras de saída recomendadas recomendadas ou recomendadas ou opcionais e as regras de aplicação FQDN/aplicação.

## <a name="azure-storage-and-aks-troubleshooting"></a>Armazenamento Azure e resolução de problemas AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Quais são as versões estáveis recomendadas de Kubernetes para o disco Azure? 

| Versão Kubernetes | Versão recomendada |
| -- | :--: |
| 1.12 | 1.12.9 ou mais tarde |
| 1.13 | 1.13.6 ou mais tarde |
| 1.14 | 1.14.2 ou mais tarde |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>Que versões de Kubernetes têm suporte azure disk na Nuvem Soberana?

| Versão Kubernetes | Versão recomendada |
| -- | :--: |
| 1.12 | 1.12.0 ou mais tarde |
| 1.13 | 1.13.0 ou mais tarde |
| 1.14 | 1.14.0 ou mais tarde |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>WaitForAttach falhou para o Disco Azure: parsing "/dev/disk/azure/scsi1/lun1": sintaxe inválida

Na versão 1.10 da Kubernetes, o MountVolume.WaitForAttach pode falhar com uma remontagem do Disco Azure.

No Linux, pode ver um erro de formato DispositivoPath incorreto. Por exemplo:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

No Windows, pode ver um erro de número de DispositivoS Path (LUN) errado. Por exemplo:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Esta questão foi corrigida nas seguintes versões da Kubernetes:

| Versão Kubernetes | Versão fixa |
| -- | :--: |
| 1.10 | 1.10.2 ou mais tarde |
| 1.11 | 1.11.0 ou mais tarde |
| 1.12 e mais tarde | N/D |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Falha na definição de uídio e gid em mountOptions para disco azure

O Disco Azure utiliza o sistema de ficheiros ext4,xfs por padrão e mountOptions tais como uid=x,gid=x não podem ser definidos no tempo de montagem. Por exemplo, se tentasse definir mountOptions uid=999,gid=999, veria um erro como:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

Pode mitigar a questão fazendo um seguinte:

* [Configure o contexto de segurança para uma cápsula,](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) definindo uid em runAsUser e gid no fsGroup. Por exemplo, a seguinte definição definirá o pod executado como raiz, tornando-o acessível a qualquer ficheiro:

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
  > Uma vez que gid e uid são montados como raiz ou 0 por padrão. Se o gid ou o uid forem definidos como não-raiz, por exemplo 1000, kubernetes usarão `chown` para alterar todos os diretórios e ficheiros sob esse disco. Esta operação pode ser morosa e pode tornar a montagem do disco muito lenta.

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

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>Erro ao apagar Volume persistente do disco Azure em utilização por um casulo

Se tentar eliminar um Volume Persistente do Disco Azure que está a ser utilizado por uma cápsula, poderá ver um erro. Por exemplo:

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

Na versão 1.10 da Kubernetes e posteriormente, existe uma função de proteção PersistentVolumeClaim ativada por predefinição para evitar este erro. Se estiver a utilizar uma versão de Kubernetes que não tenha a correção para este problema, pode atenuar este problema apagando o casulo utilizando o PersistentVolumeClaim antes de apagar o PersistentVolumeClaim.


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>Erro "Não consigo encontrar Lun para o disco" ao fixar um disco a um nó

Ao fixar um disco a um nó, pode ver o seguinte erro:

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

Esta questão foi corrigida nas seguintes versões da Kubernetes:

| Versão Kubernetes | Versão fixa |
| -- | :--: |
| 1.10 | 1.10.10 ou mais tarde |
| 1.11 | 1.11.5 ou mais tarde |
| 1.12 | 1.12.3 ou mais tarde |
| 1.13 | 1.13.0 ou mais tarde |
| 1.14 e mais tarde | N/D |

Se estiver a utilizar uma versão da Kubernetes que não tem a correção para este problema, pode atenuar o problema esperando vários minutos e tentando novamente.

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>Falha de fixação/desmontagem do disco Azure, problemas de montagem ou erros de I/O durante operações múltiplas de fixação/desmontagem

A partir da versão 1.9.2 da Versão Kubernetes, ao executar várias operações de apego/desmontagem em paralelo, poderá ver os seguintes problemas de disco devido a uma cache VM suja:

* Falhas de fixação/desapego do disco
* Erros de I/O do disco
* Destacamento inesperado do disco da VM
* VM correndo para o estado falhado devido à fixação de disco não existente

Esta questão foi corrigida nas seguintes versões da Kubernetes:

| Versão Kubernetes | Versão fixa |
| -- | :--: |
| 1.10 | 1.10.12 ou mais tarde |
| 1.11 | 1.11.6 ou mais tarde |
| 1.12 | 1.12.4 ou mais tarde |
| 1.13 | 1.13.0 ou mais tarde |
| 1.14 e mais tarde | N/D |

Se estiver a utilizar uma versão da Kubernetes que não tem a correção para este problema, pode mitigar o problema experimentando o seguinte:

* Se houver um disco à espera de se desprender durante um longo período de tempo, tente separar o disco manualmente

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Disco Azure à espera de se separar indefinidamente

Em alguns casos, se uma operação de desapego do disco Azure falhar na primeira tentativa, não voltará a tentar a operação de desapego e permanecerá ligada ao VM do nó original. Este erro pode ocorrer ao mover um disco de um nó para outro. Por exemplo:

```console
[Warning] AttachVolume.Attach failed for volume “pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9” : Attach volume “kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance “/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0” failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code=“ConflictingUserInput” Message=“Disk ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9’ cannot be attached as the disk is already owned by VM ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1’.”
```

Esta questão foi corrigida nas seguintes versões da Kubernetes:

| Versão Kubernetes | Versão fixa |
| -- | :--: |
| 1.11 | 1.11.9 ou mais tarde |
| 1.12 | 1.12.7 ou mais tarde |
| 1.13 | 1.13.4 ou mais tarde |
| 1.14 e mais tarde | N/D |

Se estiver a utilizar uma versão de Kubernetes que não tenha a correção para este problema, pode atenuar o problema desligando manualmente o disco.

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Falha de desvinculação do Disco Azure que conduz a problema potencial de condição de corrida e lista de discos de dados inválidos

Quando um Disco Azure não se desprender, irá voltar a tentar até seis vezes para desmontar o disco utilizando o back off exponencial. Também manterá um bloqueio de nível de nó na lista de discos de dados durante cerca de 3 minutos. Se a lista de discos for atualizada manualmente durante esse período de tempo, como uma operação de fixação manual ou de desvinculação, isto fará com que a lista de discos mantida pelo bloqueio do nível do nó seja obsoleta e cause instabilidade no VM do nó.

Esta questão foi corrigida nas seguintes versões da Kubernetes:

| Versão Kubernetes | Versão fixa |
| -- | :--: |
| 1.12 | 1.12.9 ou mais tarde |
| 1.13 | 1.13.6 ou mais tarde |
| 1.14 | 1.14.2 ou mais tarde |
| 1.15 e mais tarde | N/D |

Se estiver a utilizar uma versão de Kubernetes que não tenha a correção para este problema e o seu vm de nó tem uma lista de discos obsoletos, pode atenuar o problema desmontando todos os discos não existentes do VM como uma única operação a granel. **A desvinculação individual dos discos não existentes pode falhar.**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Grande número de discos Azure causa ligação/desprendimento lento

Quando o número de Discos Azure ligados a um VM de nó for superior a 10, as operações de fixação e dedesacoplamento podem ser lentas. Esta questão é conhecida e não há, neste momento, qualquer sucude suposições.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Falha no destacamento do Disco Azure conduz a potencial VM do nó em estado falhado

Em alguns casos de borda, um destacamento de Disco Azure pode parcialmente falhar e deixar o VM do nó em estado falhado.

Esta questão foi corrigida nas seguintes versões da Kubernetes:

| Versão Kubernetes | Versão fixa |
| -- | :--: |
| 1.12 | 1.12.10 ou mais tarde |
| 1.13 | 1.13.8 ou mais tarde |
| 1.14 | 1.14.4 ou mais tarde |
| 1.15 e mais tarde | N/D |

Se estiver a utilizar uma versão da Kubernetes que não tenha a correção para este problema e o seu VM do nó estiver em estado de falhas, pode atenuar o problema atualizando manualmente o estado vm utilizando um dos seguintes:

* Para um cluster baseado em conjunto de disponibilidade:
    ```console
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* Para um cluster baseado em VMSS:
    ```console
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Ficheiros Azure e resolução de problemas aks

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Quais são as versões estáveis recomendadas de Kubernetes para ficheiros Azure?
 
| Versão Kubernetes | Versão recomendada |
| -- | :--: |
| 1.12 | 1.12.6 ou mais tarde |
| 1.13 | 1.13.4 ou mais tarde |
| 1.14 | 1.14.0 ou mais tarde |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>Que versões da Kubernetes têm suporte azure Files na Nuvem Soberana?

| Versão Kubernetes | Versão recomendada |
| -- | :--: |
| 1.12 | 1.12.0 ou mais tarde |
| 1.13 | 1.13.0 ou mais tarde |
| 1.14 | 1.14.0 ou mais tarde |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Quais são as opções de montagem predefinidas ao utilizar ficheiros Azure?

Definições recomendadas:

| Versão Kubernetes | valor fileMode e dirMode|
| -- | :--: |
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 e mais tarde | 0777 |

Se utilizar um cluster com a versão Kuberetes 1.8.5 ou maior e criar dinamicamente o volume persistente com uma classe de armazenamento, as opções de montagem podem ser especificadas no objeto da classe de armazenamento. O exemplo que se segue é o *0777:*

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

Algumas definições adicionais úteis de *mountOptions:*

* *mfsymlinks* fará com que os Ficheiros Azure montes (cifs) suporte ligações simbólicas
* *Nobrl* impedirá o envio de pedidos de bloqueio de alcance byte para o servidor. Esta definição é necessária para certas aplicações que rompem com o estilo cifs de byte obrigatório fechaduras de gama. A maioria dos servidores cifs ainda não suportam pedidos de bloqueios de alcance de byte sinte. Se não utilizar *nobrl,* as aplicações que rompam com o estilo cifs os bloqueios obrigatórios de byte podem causar mensagens de erro semelhantes a:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Erro "não podia alterar permissões" ao utilizar ficheiros Azure

Ao executar postgreSQL no plugin De Ficheiros Azure, pode ver um erro semelhante a:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Este erro é causado pelo plugin Azure Files utilizando o protocolo cifs/SMB. Ao utilizar o protocolo cifs/SMB, as permissões de ficheiros e diretórios não puderam ser alteradas após a montagem.

Para resolver este problema, utilize o *subPath* juntamente com o plugin do Disco Azure. 

> [!NOTE] 
> Para o tipo de disco ext3/4, existe um diretório perdido+encontrado após a formação do disco.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>O Azure Files tem alta latência em comparação com o Disco Azure ao manusear muitos ficheiros pequenos

Em alguns casos, como manusear muitos ficheiros pequenos, poderá sentir alta latência ao utilizar ficheiros Azure quando comparado com o Disco Azure.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Erro ao ativar a definição de "Permitir o acesso permitir o acesso a partir da rede selecionada" na conta de armazenamento

Se permitir *o acesso a partir de rede selecionada* numa conta de armazenamento que é utilizada para o provisionamento dinâmico no AKS, terá um erro quando o AKS criar uma partilha de ficheiros:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Este erro *deve-se* ao facto de o controlador de volume persistente Kubernetes não estar na rede escolhida quando a definição *permite o acesso a partir de uma rede selecionada*.

Pode mitigar o problema utilizando [o fornecimento estático com ficheiros Azure](azure-files-volume.md).

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Ficheiros Azure falham na remontagem no Windows pod

Se um pod Windows com uma montagem de Ficheiros Azure for eliminado e depois programado para ser recriado no mesmo nó, o monte falhará. Esta falha deve-se à falha do comando `New-SmbGlobalMapping`, uma vez que o suporte dos Ficheiros Azure já está montado no nó.

Por exemplo, pode ver um erro semelhante ao:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Esta questão foi corrigida nas seguintes versões da Kubernetes:

| Versão Kubernetes | Versão fixa |
| -- | :--: |
| 1.12 | 1.12.6 ou mais tarde |
| 1.13 | 1.13.4 ou mais tarde |
| 1.14 e mais tarde | N/D |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>Montagem de Ficheiros Azure falha devido à chave da conta de armazenamento alterada

Se a chave da sua conta de armazenamento tiver mudado, poderá ver falhas de montagem do Azure Files.

Pode atenuar o problema atualizando manualmente o campo *azurestorageaccountkey* manualmente em segredo de ficheiro Azure com a chave de conta de armazenamento codificada base64.

Para codificar a chave da sua conta de armazenamento na base64, pode utilizar `base64`. Por exemplo:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Para atualizar o seu ficheiro secreto Azure, utilize `kubectl edit secret`. Por exemplo:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Após alguns minutos, o nó de agente irá voltar a tentar o suporte de ficheiro sinuoso com a chave de armazenamento atualizada.

### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>O autoscaler do cluster não consegue escalar com o erro falhado na fixação dos tamanhos do grupo do nó

Se o seu autoscaler de cluster não estiver a escalonar para cima/para baixo e vir um erro como o abaixo nos registos de [autoescalador][view-master-logs]do cluster .

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Este erro deve-se a uma condição de corrida autoscaler de cluster a montante, onde o autoscaler do cluster termina com um valor diferente daquele que está realmente no cluster. Para sair deste estado, simplesmente desative e reative o [autoescalador][cluster-autoscaler]do cluster .

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
