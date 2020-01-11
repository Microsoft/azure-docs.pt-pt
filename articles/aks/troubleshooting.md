---
title: Solucionar problemas comuns do serviço kubernetes do Azure
description: Saiba como solucionar problemas comuns ao usar o serviço de kubernetes do Azure (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: saudas
ms.openlocfilehash: de3a3d9e5523341c2f549ff2a90c9c40a4e3cb50
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75889456"
---
# <a name="aks-troubleshooting"></a>Solução de problemas do AKS

Ao criar ou gerenciar clusters do AKS (serviço kubernetes do Azure), ocasionalmente, você pode encontrar problemas. Este artigo fornece detalhes sobre alguns problemas comuns e etapas de solução de problemas.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Em geral, onde encontro informações sobre a depuração de problemas do kubernetes?

Experimente o [guia oficial para solucionar problemas de clusters do kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Há também um [Guia de solução de problemas](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md), publicado por um engenheiro da Microsoft para solucionar problemas de pods, nós, clusters e outros recursos.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Estou recebendo um erro de "cota excedido" durante a criação ou a atualização. O que devo fazer? 

Você precisa [solicitar núcleos](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Qual é a configuração máxima de pods por nó para AKS?

A configuração de pods máxima por nó será de 30 por padrão se você implantar um cluster AKS no portal do Azure.
A configuração de pods máxima por nó será 110 por padrão se você implantar um cluster AKS no CLI do Azure. (Verifique se você está usando a versão mais recente do CLI do Azure). Essa configuração padrão pode ser alterada usando o sinalizador `–-max-pods` no comando `az aks create`.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Estou recebendo um erro de insufficientSubnetSize ao implantar um cluster AKS com rede avançada. O que devo fazer?

Se o Azure CNI (rede avançada) for usado, o AKS alocará endereços IP com base no "Max-pods" por nó configurado. Com base no máximo de pods configurado por nó, o tamanho da sub-rede deve ser maior que o produto do número de nós e a configuração do pod máximo por nó. A equação a seguir descreve isso:

O tamanho da sub-rede > número de nós no cluster (levando em consideração os requisitos de dimensionamento futuros) * máximo de pods por conjunto de nós.

Para obter mais informações, consulte [planejar o endereçamento de IP para o cluster](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Meu pod está preso no modo CrashLoopBackOff. O que devo fazer?

Pode haver vários motivos para o Pod estar preso nesse modo. Você pode examinar:

* O próprio Pod, usando `kubectl describe pod <pod-name>`.
* Os logs, usando `kubectl log <pod-name>`.

Para obter mais informações sobre como solucionar problemas de Pod, consulte [depurar aplicativos](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Estou tentando habilitar o RBAC em um cluster existente. Como posso fazer isso?

Infelizmente, não há suporte para habilitar o controle de acesso baseado em função (RBAC) em clusters existentes no momento. Você deve criar explicitamente novos clusters. Se você usar a CLI, o RBAC será habilitado por padrão. Se você usar o portal do AKS, um botão de alternância para habilitar o RBAC estará disponível no fluxo de trabalho de criação.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Criei um cluster com o RBAC habilitado usando o CLI do Azure com os padrões ou o portal do Azure, e agora vejo muitos avisos no painel do kubernetes. O painel usado para funcionar sem avisos. O que devo fazer?

O motivo para os avisos no painel é que o cluster agora está habilitado com RBAC e o acesso a ele foi desabilitado por padrão. Em geral, essa abordagem é uma boa prática porque a exposição padrão do painel a todos os usuários do cluster pode levar a ameaças de segurança. Se você ainda quiser habilitar o painel, siga as etapas nesta [postagem no blog](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Não consigo me conectar ao painel. O que devo fazer?

A maneira mais fácil de acessar o serviço fora do cluster é executar `kubectl proxy`, quais proxies as solicitações enviaram para a porta localhost 8001 para o servidor de API kubernetes. A partir daí, o servidor de API pode fazer proxy para seu serviço: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Se você não vir o painel do kubernetes, verifique se o Pod `kube-proxy` está em execução no namespace `kube-system`. Se não estiver em um estado de execução, exclua o pod e ele será reiniciado.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Não consigo obter logs usando logs do kubectl ou não consigo me conectar ao servidor de API. Estou recebendo "erro do servidor: erro ao discar back-end: discar TCP...". O que devo fazer?

Verifique se o grupo de segurança de rede padrão não foi modificado e se a porta 22 e 9000 estão abertas para conexão com o servidor de API. Verifique se o pod de `tunnelfront` está em execução no namespace *Kube-System* usando o comando `kubectl get pods --namespace kube-system`. Se não estiver, force a exclusão do pod e ela será reiniciada.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Estou tentando atualizar ou dimensionar e estou recebendo um erro "mensagem: a alteração da propriedade ' imageReference ' não é permitida". Como fazer corrigir esse problema?

Você pode estar recebendo esse erro porque modificou as marcas nos nós de agente dentro do cluster AKS. Modificar e excluir marcas e outras propriedades de recursos no grupo de recursos MC_ * pode levar a resultados inesperados. Modificar os recursos no grupo MC_ * no cluster AKS interrompe o SLO (objetivo de nível de serviço).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Estou recebendo erros de que meu cluster está em estado de falha e a atualização ou o dimensionamento não funcionará até que seja corrigido

*Esta assistência para solução de problemas é direcionada de https://aka.ms/aks-cluster-failed*

Esse erro ocorre quando os clusters entram em um estado de falha por vários motivos. Siga as etapas abaixo para resolver o estado de falha do cluster antes de repetir a operação que falhou anteriormente:

1. Até que o cluster esteja fora do estado de `failed`, as operações de `upgrade` e `scale` não terão sucesso. As resoluções e problemas de raiz comuns incluem:
    * Dimensionamento com **cota de computação insuficiente (CRP)** . Para resolver, primeiro dimensione o cluster de volta para um estado de meta estável dentro da cota. Em seguida, siga estas [etapas para solicitar um aumento de cota de computação](../azure-portal/supportability/resource-manager-core-quotas-request.md) antes de tentar escalar verticalmente novamente além dos limites de cota iniciais.
    * Dimensionamento de um cluster com rede avançada e **recursos de sub-rede (rede) insuficientes**. Para resolver, primeiro dimensione o cluster de volta para um estado de meta estável dentro da cota. Em seguida, siga [estas etapas para solicitar um aumento de cota de recursos](../azure-resource-manager/templates/error-resource-quota.md#solution) antes de tentar escalar verticalmente novamente além dos limites de cota iniciais.
2. Depois que a causa subjacente da falha de atualização for resolvida, o cluster deverá estar em um estado com êxito. Quando um estado bem-sucedido for verificado, repita a operação original.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Estou recebendo erros ao tentar atualizar ou dimensionar o estado em que meu cluster está sendo atualizado no momento ou com falha na atualização

*Esta assistência para solução de problemas é direcionada de https://aka.ms/aks-pending-upgrade*

As operações de atualização e dimensionamento em um cluster com um único pool de nós ou um cluster com [vários pools de nós](use-multiple-node-pools.md) são mutuamente exclusivas. Você não pode ter um cluster ou pool de nós simultaneamente para atualizar e dimensionar. Em vez disso, cada tipo de operação deve ser concluído no recurso de destino antes da próxima solicitação no mesmo recurso. Como resultado, as operações são limitadas quando as operações de atualização ativa ou de escala estão ocorrendo ou tentadas e subsequentemente falharam. 

Para ajudar a diagnosticar o problema, execute `az aks show -g myResourceGroup -n myAKSCluster -o table` para recuperar o status detalhado no cluster. Com base no resultado:

* Se o cluster estiver sendo atualizado ativamente, aguarde até que a operação seja encerrada. Se tiver êxito, repita a operação anterior com falha novamente.
* Se o cluster tiver falhado na atualização, siga as etapas descritas na seção anterior.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Posso mover meu cluster para uma assinatura diferente ou minha assinatura com meu cluster para um novo locatário?

Se você moveu o cluster AKS para uma assinatura diferente ou o cluster que possui a assinatura para um novo locatário, o cluster perderá a funcionalidade devido à perda de atribuições de função e aos direitos de entidades de serviço. **AKs não dá suporte à movimentação de clusters entre assinaturas ou locatários** devido a essa restrição.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Estou recebendo erros ao tentar usar recursos que exigem conjuntos de dimensionamento de máquinas virtuais

*Esta assistência para solução de problemas é direcionada do aka.ms/aks-vmss-enablement*

Você pode receber erros que indicam que o cluster AKS não está em um conjunto de dimensionamento de máquinas virtuais, como o exemplo a seguir:

**O AgentPool ' AgentPool ' definiu o dimensionamento automático como habilitado, mas não está em conjuntos de dimensionamento de máquinas virtuais**

Para usar recursos como os pools de dimensionamento de vários nós ou do cluster, os clusters AKS devem ser criados para usar conjuntos de dimensionamento de máquinas virtuais. Os erros são retornados se você tentar usar recursos que dependem de conjuntos de dimensionamento de máquinas virtuais e você tiver como alvo um cluster AKS de conjunto de dimensionamento de máquinas não virtuais regular.

Siga as etapas *antes de começar* no documento apropriado para criar corretamente um cluster AKs:

* [Usar o dimensionamento de cluster](cluster-autoscaler.md)
* [Criar e usar vários pools de nós](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Quais restrições de nomenclatura são impostas para recursos e parâmetros AKS?

*Esta assistência para solução de problemas é direcionada do aka.ms/aks-naming-rules*

As restrições de nomenclatura são implementadas pela plataforma do Azure e AKS. Se um nome de recurso ou parâmetro quebrar uma dessas restrições, será retornado um erro solicitando que você forneça uma entrada diferente. As seguintes diretrizes de nomenclatura comuns se aplicam:

* Os nomes de cluster devem ter de 1-63 caracteres. Os únicos caracteres permitidos são letras, números, traços e sublinhados. O primeiro e o último caractere devem ser uma letra ou um número.
* O nome do grupo de recursos do AKS *MC_* combina o nome do grupo de recursos e o nome do recurso. A sintaxe gerada automaticamente de `MC_resourceGroupName_resourceName_AzureRegion` não deve ser maior que 80 caracteres. Se necessário, reduza o tamanho do nome do grupo de recursos ou do nome do cluster AKS.
* O *dnsPrefix* deve começar e terminar com valores alfanuméricos. Os caracteres válidos incluem valores alfanuméricos e hifens (-). O *dnsPrefix* não pode incluir caracteres especiais, como um ponto (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Estou recebendo erros ao tentar criar, atualizar, dimensionar, excluir ou atualizar o cluster. essa operação não é permitida porque outra operação está em andamento.

*Esta assistência para solução de problemas é direcionada do aka.ms/aks-pending-operation*

As operações de cluster são limitadas quando uma operação anterior ainda está em andamento. Para recuperar um status detalhado do cluster, use o comando `az aks show -g myResourceGroup -n myAKSCluster -o table`. Use seu próprio grupo de recursos e o nome do cluster AKS, conforme necessário.

Com base na saída do status do cluster:

* Se o cluster estiver em qualquer estado de provisionamento diferente de *êxito* ou *falha*, aguarde até que a operação (*atualização/atualização/criação/dimensionamento/exclusão/migração*) seja encerrada. Quando a operação anterior for concluída, tente novamente a operação de cluster mais recente.

* Se o cluster tiver uma falha de atualização, siga as etapas descritas estou [recebendo erros de que meu cluster está em estado de falha e a atualização ou o dimensionamento não funcionará até que seja corrigido](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Estou recebendo erros de que minha entidade de serviço não foi encontrada quando tento criar um novo cluster sem passar um existente.

Ao criar um cluster AKS, ele requer uma entidade de serviço para criar recursos em seu nome. O AKS oferece a capacidade de ter um novo criado no momento da criação do cluster, mas isso requer que Azure Active Directory propague totalmente a nova entidade de serviço em um tempo razoável para que o cluster tenha sucesso na criação. Quando essa propagação demorar muito, o cluster falhará na validação para criar, pois ele não pode encontrar uma entidade de serviço disponível para fazer isso. 

Use as seguintes soluções alternativas para isso:
1. Use uma entidade de serviço existente que já propagada entre regiões e exista para passar para AKS no momento da criação do cluster.
2. Se você estiver usando scripts de automação, adicione atrasos entre a criação da entidade de serviço e a criação do cluster AKS.
3. Se estiver usando portal do Azure, retorne para as configurações de cluster durante a criação e repita a página de validação após alguns minutos.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Estou recebendo erros depois de restringir o tráfego de egresso

Ao restringir o tráfego de saída de um cluster AKS, há regras de saída/portas de rede [recomendadas e opcionais necessárias](limit-egress-traffic.md) e regras de FQDN/aplicativo para AKs. Se suas configurações estiverem em conflito com qualquer uma dessas regras, talvez você não possa executar determinados comandos de `kubectl`. Você também pode ver erros ao criar um cluster AKS.

Verifique se as configurações não estão em conflito com nenhuma das portas de rede/regras de saída necessárias ou opcionais recomendadas ou regras de FQDN/aplicativo.

## <a name="azure-storage-and-aks-troubleshooting"></a>Solução de problemas de armazenamento e AKS do Azure

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Quais são as versões estáveis recomendadas do kubernetes para o disco do Azure? 

| Versão do kubernetes | Versão recomendada |
| -- | :--: |
| 1.12 | 1.12.9 ou posterior |
| 1.13 | 1.13.6 ou posterior |
| 1,14 | 1.14.2 ou posterior |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>Quais versões do kubernetes têm suporte de disco do Azure na nuvem do soberanas?

| Versão do kubernetes | Versão recomendada |
| -- | :--: |
| 1.12 | 1.12.0 ou posterior |
| 1.13 | 1.13.0 ou posterior |
| 1,14 | 1.14.0 ou posterior |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>Falha de WaitForAttach para o disco do Azure: analisando "/dev/disk/Azure/scsi1/lun1": sintaxe inválida

No kubernetes versão 1,10, MountVolume. WaitForAttach pode falhar com a remontagem de disco do Azure.

No Linux, você pode ver um erro de formato DevicePath incorreto. Por exemplo:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

No Windows, você pode ver um erro de número de DevicePath (LUN) errado. Por exemplo:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Esse problema foi corrigido nas seguintes versões do kubernetes:

| Versão do kubernetes | Versão corrigida |
| -- | :--: |
| 1,10 | 1.10.2 ou posterior |
| 1,11 | 1.11.0 ou posterior |
| 1,12 e posterior | N/A |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Falha ao definir UID e GID em mountoptions para disco do Azure

O disco do Azure usa o ext4, o sistema de arquivos XFS por padrão e mountoptions como uid = x, GID = x não pode ser definido no momento da montagem. Por exemplo, se você tentou definir mountoptions UID = 999, GID = 999, o veria um erro como:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

Você pode mitigar o problema seguindo um destes procedimentos:

* [Configure o contexto de segurança para um pod](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) definindo UID em runAsUser e GID em fsGroup. Por exemplo, a configuração a seguir definirá o Pod executar como raiz, torná-lo acessível para qualquer arquivo:

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
  > Como GID e uid são montados como root ou 0 por padrão. Se GID ou uid forem definidos como não raiz, por exemplo, 1000, o kubernetes usará `chown` para alterar todos os diretórios e arquivos nesse disco. Essa operação pode ser demorada e pode tornar a montagem do disco muito lenta.

* Use `chown` em initContainers para definir o GID e UID. Por exemplo:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>Erro ao excluir o Azure Disk PersistentVolumeClaim em uso por um pod

Se você tentar excluir um disco PersistentVolumeClaim do Azure que está sendo usado por um pod, você poderá ver um erro. Por exemplo:

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

No kubernetes versão 1,10 e posterior, há um recurso de proteção do PersistentVolumeClaim habilitado por padrão para evitar esse erro. Se você estiver usando uma versão do kubernetes que não tenha a correção para esse problema, você pode mitigar esse problema excluindo o Pod usando o PersistentVolumeClaim antes de excluir o PersistentVolumeClaim.


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>Erro "não é possível localizar o LUN para o disco" ao anexar um disco a um nó

Ao anexar um disco a um nó, você poderá ver o seguinte erro:

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

Esse problema foi corrigido nas seguintes versões do kubernetes:

| Versão do kubernetes | Versão corrigida |
| -- | :--: |
| 1,10 | 1.10.10 ou posterior |
| 1,11 | 1.11.5 ou posterior |
| 1.12 | 1.12.3 ou posterior |
| 1.13 | 1.13.0 ou posterior |
| 1,14 e posterior | N/A |

Se você estiver usando uma versão do kubernetes que não tenha a correção para esse problema, você pode mitigar o problema aguardando vários minutos e tentando novamente.

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>Falha de anexação/desanexação de disco do Azure, problemas de montagem ou erros de e/s durante várias operações de conexão/desanexação

A partir do kubernetes versão 1.9.2, ao executar várias operações de anexar/desanexar em paralelo, você poderá ver os seguintes problemas de disco devido a um cache de VM sujo:

* Falhas de anexação/desanexação de disco
* Erros de e/s de disco
* Desanexação de disco inesperada da VM
* VM em execução em estado de falha devido à anexação de disco não existente

Esse problema foi corrigido nas seguintes versões do kubernetes:

| Versão do kubernetes | Versão corrigida |
| -- | :--: |
| 1,10 | 1.10.12 ou posterior |
| 1,11 | 1.11.6 ou posterior |
| 1.12 | 1.12.4 ou posterior |
| 1.13 | 1.13.0 ou posterior |
| 1,14 e posterior | N/A |

Se você estiver usando uma versão do kubernetes que não tenha a correção para esse problema, você pode mitigar o problema experimentando o seguinte:

* Se houver um disco aguardando para desanexar por um longo período de tempo, tente desanexar o disco manualmente

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Disco do Azure aguardando desanexar indefinidamente

Em alguns casos, se uma operação de desanexação de disco do Azure falhar na primeira tentativa, ela não repetirá a operação de desanexação e permanecerá anexada à VM do nó original. Esse erro pode ocorrer ao mover um disco de um nó para outro. Por exemplo:

```console
[Warning] AttachVolume.Attach failed for volume “pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9” : Attach volume “kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance “/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0” failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code=“ConflictingUserInput” Message=“Disk ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9’ cannot be attached as the disk is already owned by VM ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1’.”
```

Esse problema foi corrigido nas seguintes versões do kubernetes:

| Versão do kubernetes | Versão corrigida |
| -- | :--: |
| 1,11 | 1.11.9 ou posterior |
| 1.12 | 1.12.7 ou posterior |
| 1.13 | 1.13.4 ou posterior |
| 1,14 e posterior | N/A |

Se você estiver usando uma versão do kubernetes que não tenha a correção para esse problema, você pode mitigar o problema desanexando manualmente o disco.

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Falha de desanexação de disco do Azure que leva a problemas potenciais de condição de corrida e lista de discos de dados

Quando um disco do Azure não é desconectado, ele tentará novamente até seis vezes para desanexar o disco usando a retirada exponencial. Ele também manterá um bloqueio em nível de nó na lista de discos de dados por cerca de 3 minutos. Se a lista de discos for atualizada manualmente durante esse período de tempo, como uma operação de anexação ou desanexação manual, isso fará com que a lista de discos mantida pelo bloqueio em nível de nó fique obsoleta e cause instabilidade na VM do nó.

Esse problema foi corrigido nas seguintes versões do kubernetes:

| Versão do kubernetes | Versão corrigida |
| -- | :--: |
| 1.12 | 1.12.9 ou posterior |
| 1.13 | 1.13.6 ou posterior |
| 1,14 | 1.14.2 ou posterior |
| 1,15 e posterior | N/A |

Se você estiver usando uma versão do kubernetes que não tenha a correção para esse problema e a VM do nó tiver uma lista de discos obsoletos, você poderá mitigar o problema desanexando todos os discos não existentes da VM como uma única operação em massa. **O desanexação individual de discos não existentes pode falhar.**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Um grande número de discos do Azure causa conexão/desanexação lenta

Quando o número de discos do Azure anexados a uma VM de nó é maior que 10, as operações de anexar e desanexar podem ser lentas. Esse problema é um problema conhecido e não há soluções alternativas no momento.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Falha de desanexação de disco do Azure que leva à VM de nó potencial em estado de falha

Em alguns casos de borda, uma desanexação de disco do Azure pode falhar parcialmente e deixar a VM do nó em um estado de falha.

Esse problema foi corrigido nas seguintes versões do kubernetes:

| Versão do kubernetes | Versão corrigida |
| -- | :--: |
| 1.12 | 1.12.10 ou posterior |
| 1.13 | 1.13.8 ou posterior |
| 1,14 | 1.14.4 ou posterior |
| 1,15 e posterior | N/A |

Se você estiver usando uma versão do kubernetes que não tenha a correção para esse problema e a VM do nó estiver em um estado de falha, você poderá mitigar o problema atualizando manualmente o status da VM usando um dos seguintes:

* Para um cluster baseado em conjunto de disponibilidade:
    ```console
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* Para um cluster baseado em VMSS:
    ```console
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Solução de problemas de arquivos e AKS do Azure

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Quais são as versões estáveis recomendadas do kubernetes para arquivos do Azure?
 
| Versão do kubernetes | Versão recomendada |
| -- | :--: |
| 1.12 | 1.12.6 ou posterior |
| 1.13 | 1.13.4 ou posterior |
| 1,14 | 1.14.0 ou posterior |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>Quais versões do kubernetes têm suporte aos arquivos do Azure na nuvem do soberanas?

| Versão do kubernetes | Versão recomendada |
| -- | :--: |
| 1.12 | 1.12.0 ou posterior |
| 1.13 | 1.13.0 ou posterior |
| 1,14 | 1.14.0 ou posterior |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Quais são as montagens padrão ao usar os arquivos do Azure?

Configurações recomendadas:

| Versão do kubernetes | valor fileMode e dirMode|
| -- | :--: |
| 1.12.0-1.12.1 | 0755 |
| 1.12.2 e posterior | 0777 |

Se estiver usando um cluster com Kuberetes versão 1.8.5 ou superior e criando dinamicamente o volume persistente com uma classe de armazenamento, as opções de montagem poderão ser especificadas no objeto de classe de armazenamento. O exemplo a seguir define *0777*:

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

Algumas configurações adicionais de *mountoptions* úteis:

* *mfsymlinks* fará com que os links simbólicos do suporte a montagem de arquivos do Azure (CIFS)
* o *nobrl* impedirá o envio de solicitações de bloqueio de intervalo de bytes para o servidor. Essa configuração é necessária para determinados aplicativos que interrompem com bloqueios de intervalo de bytes obrigatórios de estilo CIFS. A maioria dos servidores CIFS ainda não dá suporte à solicitação de bloqueios de intervalo de bytes de consultoria. Se não estiver usando *nobrl*, os aplicativos que interromperem com o estilo CIFS os bloqueios de intervalo de bytes obrigatórios podem causar mensagens de erro semelhantes a:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Erro "não foi possível alterar as permissões" ao usar arquivos do Azure

Ao executar o PostgreSQL no plug-in de arquivos do Azure, você poderá ver um erro semelhante a:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Esse erro é causado pelo plug-in de arquivos do Azure usando o protocolo CIFS/SMB. Ao usar o protocolo CIFS/SMB, as permissões de arquivo e diretório não puderam ser alteradas após a montagem.

Para resolver esse problema, use *subcaminho* junto com o plug-in de disco do Azure. 

> [!NOTE] 
> Para o tipo de disco ext3/4, há um diretório perdido + encontrado depois que o disco é formatado.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Os arquivos do Azure têm alta latência em comparação com o disco do Azure ao lidar com muitos arquivos pequenos

Em alguns casos, como a manipulação de muitos arquivos pequenos, você pode enfrentar alta latência ao usar os arquivos do Azure em comparação com o disco do Azure.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Erro ao habilitar a configuração "permitir acesso de permissão de acesso da rede selecionada" na conta de armazenamento

Se você habilitar *permitir o acesso da rede selecionada* em uma conta de armazenamento que é usada para provisionamento dinâmico no AKs, receberá um erro quando o AKs criar um compartilhamento de arquivos:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Esse erro ocorre devido ao kubernetes *persistentvolume-Controller* não estar na rede escolhida quando a configuração *permitir acesso da rede selecionada*.

Você pode mitigar o problema usando o [provisionamento estático com os arquivos do Azure](azure-files-volume.md).

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Falha na remontagem de arquivos do Azure no pod do Windows

Se um pod do Windows com uma montagem de arquivos do Azure for excluído e agendado para ser recriado no mesmo nó, a montagem falhará. Essa falha ocorre devido à falha do comando `New-SmbGlobalMapping`, pois a montagem dos arquivos do Azure já está montada no nó.

Por exemplo, você pode ver um erro semelhante a:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Esse problema foi corrigido nas seguintes versões do kubernetes:

| Versão do kubernetes | Versão corrigida |
| -- | :--: |
| 1.12 | 1.12.6 ou posterior |
| 1.13 | 1.13.4 ou posterior |
| 1,14 e posterior | N/A |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>Falha na montagem de arquivos do Azure devido à chave de conta de armazenamento alterada

Se sua chave de conta de armazenamento tiver sido alterada, você poderá ver falhas de montagem de arquivos do Azure.

Você pode mitigar o problema atualizando manualmente o campo *azurestorageaccountkey* manualmente no segredo de arquivo do Azure com sua chave de conta de armazenamento codificada em base64.

Para codificar sua chave de conta de armazenamento em base64, você pode usar `base64`. Por exemplo:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Para atualizar o arquivo secreto do Azure, use `kubectl edit secret`. Por exemplo:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Após alguns minutos, o nó do agente tentará novamente a montagem de arquivo do Azure com a chave de armazenamento atualizada.

### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>Falha no dimensionamento do autodimensionator do cluster com o erro falha ao corrigir os tamanhos do grupo de nós

Se o dimensionador automático do cluster não estiver sendo expandido/reduzido e você vir um erro como abaixo nos [logs de dimensionamento][view-master-logs]automático do cluster.

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Esse erro ocorre devido a uma condição de corrida de autoescalar de cluster upstream em que o dimensionamento de cluster é encerrado com um valor diferente daquele que realmente está no cluster. Para sair desse Estado, basta desabilitar e reabilitar o [dimensionador do cluster][cluster-autoscaler].

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md