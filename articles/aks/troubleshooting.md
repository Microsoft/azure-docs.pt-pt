---
title: Problemas de resolução de problemas problemas comuns do serviço Azure Kubernetes
description: Aprenda a resolver problemas comuns ao utilizar o Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: troubleshooting
ms.date: 05/16/2020
ms.openlocfilehash: f9831077d1f2850d39e4ef5e5ba35245f16cd683
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83724999"
---
# <a name="aks-troubleshooting"></a>Resolução de problemas do AKS

Quando cria ou gere os clusters do Serviço Azure Kubernetes (AKS), pode ocasionalmente encontrar problemas. Este artigo detalha alguns problemas comuns e passos de resolução de problemas.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Em geral, onde encontro informações sobre depuração dos problemas de Kubernetes?

Experimente o [guia oficial para resolver os aglomerados kubernetes.](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)
Há também um guia de resolução de [problemas,](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)publicado por um engenheiro da Microsoft para cápsulas de resolução de problemas, nós, clusters e outras funcionalidades.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Estou a ter um erro de "quota excedida" durante a criação ou atualização. O que devo fazer? 

 [Solicite mais núcleos.](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Qual é a definição máxima de pods por nó para AKS?

A definição máxima de pods por nó é de 30 por padrão se implementar um cluster AKS no portal Azure.
A definição máxima de pods por nó é de 110 por padrão se implantar um cluster AKS no Azure CLI. (Certifique-se de que está a utilizar a versão mais recente do Azure CLI). Esta definição pode ser alterada utilizando a `–-max-pods` bandeira no `az aks create` comando.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Estou a ter um erro subnetSize insuficiente enquanto implanto um cluster AKS com networking avançado. O que devo fazer?

Ao utilizar o plugin de rede Azure CNI, o AKS atribui endereços IP baseados nos "--vvagens máximas" por parâmetro de nó. O tamanho da sub-rede deve ser superior ao número de nós vezes as cápsulas máximas por regulação do nó. A seguinte equação descreve-a:

Tamanho da sub-rede > número de nós no cluster (tendo em conta os futuros requisitos de escala) * cápsulas max por conjunto de nó.

Para mais informações, consulte o [Plano IP endereçando para o seu cluster](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>A minha cápsula está presa no modo CrashLoopBackOff. O que devo fazer?

Pode haver várias razões para a cápsula estar presa nesse modo. Pode investigar:

* A vagem em si, utilizando `kubectl describe pod <pod-name>` .
* Os registos, utilizando `kubectl logs <pod-name>` .

Para obter mais informações sobre como resolver problemas com a cápsula, consulte [as aplicações Debug](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-role-based-access-control-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Estou a tentar ativar o Controlo de Acesso baseado em Papéis (RBAC) num cluster existente. Como posso fazer isto?

Permitir o controlo de acesso baseado em papéis (RBAC) em clusters existentes não é suportado neste momento, deve ser definido na criação de novos clusters. O RBAC é ativado por predefinição ao utilizar CLI, Portal ou uma versão API mais tarde do que `2020-03-01` .

## <a name="i-created-a-cluster-with-rbac-enabled-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Criei um cluster com RBAC ativado e agora vejo muitos avisos no painel kubernetes. O tablier funcionava sem avisos. O que devo fazer?

A razão para as advertências é que o cluster tem RBAC ativado e o acesso ao tablier está agora restringido por padrão. Em geral, esta abordagem é uma boa prática porque a exposição padrão do painel de instrumentos a todos os utilizadores do cluster pode levar a ameaças à segurança. Se ainda quiser ativar o dashboard, siga os passos [nesta publicação de blog](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Não é possível ligar ao dashboard. O que devo fazer?

A forma mais fácil de aceder ao seu serviço fora do cluster é `kubectl proxy` executar, quais os pedidos enviados para o seu porto de acolhimento local 8001 para o servidor Kubernetes API. A partir daí, o servidor API pode proxy para o seu serviço: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/` .

Se não vir o painel kubernetes, verifique se a `kube-proxy` cápsula está a funcionar no espaço de `kube-system` nome. Se não estiver em estado de funcionamento, apague a cápsula e recomeçará.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Não consigo registos usando registos de kubectl ou não consigo ligar-me ao servidor DaPI. Estou a receber "Error from server: error dial dial backend: dial tcp...". O que devo fazer?

Certifique-se de que as portas 22, 9000 e 1194 estão abertas para ligar ao servidor API. Verifique se a ou a cápsula está a funcionar no espaço de nome do `tunnelfront` `aks-link` sistema *kube* utilizando o `kubectl get pods --namespace kube-system` comando. Se não for, força a eliminação da cápsula e recomeçará.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Estou a tentar atualizar ou escalar e estou a ter um `"Changing property 'imageReference' is not allowed"` erro. Como posso resolver este problema?

Podes estar a ter este erro porque modificaste as etiquetas nos nós do agente dentro do cluster AKS. Modificar ou eliminar tags e outras propriedades de recursos no grupo de recursos MC_* pode levar a resultados inesperados. Alterar os recursos no âmbito do grupo MC_* no cluster AKS quebra o objetivo de nível de serviço (OIt).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Estou a receber erros de que o meu cluster está em estado falhado e que a modernização ou escalonamento não funcionará até que seja corrigido.

*Esta assistência de resolução de problemas é direcionada a partir dehttps://aka.ms/aks-cluster-failed*

Este erro ocorre quando os aglomerados entram num estado falhado por múltiplas razões. Siga os passos abaixo para resolver o seu estado falhado antes de voltar a tentar a operação anteriormente falhada:

1. Até que o aglomerado esteja fora do `failed` estado, `upgrade` e as `scale` operações não vão ter sucesso. As questões e resoluções fundamentais comuns incluem:
    * Escalacom **quota de cálculo insuficiente (CRP).** Para resolver, primeiro escala o seu cluster de volta para um estado de meta estável dentro da quota. Em seguida, siga estas [medidas para solicitar um aumento](../azure-portal/supportability/resource-manager-core-quotas-request.md) da quota de cálculo antes de tentar escalar novamente para além dos limites iniciais de quota.
    * Dimensionar um cluster com redes avançadas e **recursos de sub-rede insuficientes (networking).** Para resolver, primeiro escala o seu cluster de volta para um estado de meta estável dentro da quota. Em seguida, siga [estas medidas para solicitar um aumento](../azure-resource-manager/templates/error-resource-quota.md#solution) da quota de recursos antes de tentar escalar novamente para além dos limites iniciais de quota.
2. Uma vez resolvida a causa subjacente à falha de atualização, o seu cluster deve estar num estado bem sucedido. Uma vez verificado um estado bem sucedido, tente novamente a operação original.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade"></a>Estou a receber erros ao tentar atualizar ou escalar esse estado, o meu cluster está a ser atualizado ou falhou na atualização.

*Esta assistência de resolução de problemas é direcionada a partir dehttps://aka.ms/aks-pending-upgrade*

 Você não pode ter um cluster ou piscina de nó simultaneamente atualizar e escalar. Em vez disso, cada tipo de funcionamento deve ser preenchido no recurso-alvo antes do próximo pedido sobre o mesmo recurso. Como resultado, as operações são limitadas quando operações ativas de atualização ou escala estão a ocorrer ou a tentar. 

Para ajudar a diagnosticar o problema `az aks show -g myResourceGroup -n myAKSCluster -o table` corre para recuperar o estado detalhado no seu cluster. Com base no resultado:

* Se o cluster estiver a atualizar ativamente, aguarde até que a operação termine. Se tiver sucesso, volte a tentar a operação anteriormente falhada.
* Se o cluster tiver falhado na atualização, siga os passos delineados na secção anterior.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Posso mudar o meu cluster para uma subscrição diferente ou a minha assinatura com o meu cluster para um novo inquilino?

Se mudou o seu cluster AKS para uma subscrição diferente ou a subscrição do cluster a um novo inquilino, o cluster não funcionará devido à falta de permissões de identidade do cluster. A **AKS não suporta a deslocação** de clusters através de subscrições ou inquilinos devido a esta restrição.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Estou a receber erros a tentar usar funcionalidades que requerem conjuntos de escala de máquinas virtuais

*Esta assistência de resolução de problemas é dirigida a partir de aka.ms/aks-vmss-enablement*

Pode receber erros que indiquem que o seu cluster AKS não está num conjunto de escala de máquina virtual, como o seguinte exemplo:

**AgentPool `<agentpoolname>` definiu a escala automática como ativado, mas não está em conjuntos de escala de máquina virtual**

Características como o autoscaler do cluster ou várias piscinas de nó requerem conjuntos de escala de máquina virtual como `vm-set-type` o .

Siga os passos *antes de iniciar* os passos no doc apropriado para criar corretamente um cluster AKS:

* [Utilize o autoscaler do cluster](cluster-autoscaler.md)
* [Criar e usar várias piscinas de nó](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Que restrições de nomeação são aplicadas aos recursos e parâmetros aks?

*Esta assistência de resolução de problemas é direcionada a partir de aka.ms/aks-naming-rules*

As restrições de nomeação são implementadas tanto pela plataforma Azure como pela AKS. Se um nome de recurso ou parâmetro quebrar uma destas restrições, é devolvido um erro que lhe pede fornecer uma entrada diferente. Aplicam-se as seguintes diretrizes comuns de nomeação:

* Os nomes do cluster devem ser 1-63 caracteres. Os únicos caracteres permitidos são letras, números, traços e sublinhados. O primeiro e último personagem deve ser uma carta ou um número.
* O nome do grupo de recursos AKS/*MC_* combina nome de grupo de recursos e nome de recursos. A sintaxe autogerada não deve ser superior a `MC_resourceGroupName_resourceName_AzureRegion` 80 chars. Se necessário, reduza o comprimento do nome do grupo de recursos ou o nome do cluster AKS. Também pode [personalizar o nome do seu grupo](cluster-configuration.md#custom-resource-group-name) de recursos de nó
* O *dnsPrefix* deve começar e terminar com valores alfanuméricos e deve estar entre 1-54 caracteres. Os caracteres válidos incluem valores alfanuméricos e hífenes (-). O *dnsPrefix* não pode incluir caracteres especiais como um período (.).
* Os nomes da PISCINA do Nó AKS devem ser todos minúsculos e ser 1-11 caracteres para piscinas de nó linux e 1-6 caracteres para piscinas de nós de janelas. O nome deve começar com uma letra e os únicos caracteres permitidos são letras e números.

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Estou a receber erros ao tentar criar, atualizar, escalar, eliminar ou atualizar o cluster, que a operação não é permitida, uma vez que outra operação está em curso.

*Esta assistência de resolução de problemas é direcionada a partir de aka.ms/aks-pending-operation*

As operações de cluster são limitadas quando uma operação anterior ainda está em curso. Para recuperar um estado detalhado do seu cluster, use o `az aks show -g myResourceGroup -n myAKSCluster -o table` comando. Use o seu próprio grupo de recursos e o nome do cluster AKS, conforme necessário.

Com base na saída do estado do cluster:

* Se o cluster estiver em qualquer estado de provisionamento que não seja *bem sucedido* ou *falhado,* aguarde até que a operação (*Upgrade / Upgrade / Criação / Criação / Aparação / Aparação / Migração).* Quando a operação anterior estiver concluída, tente novamente a sua última operação de cluster.

* Se o cluster tiver uma atualização falhada, siga os passos delineados, [estou a receber erros de que o meu cluster está em estado falhado e a atualização ou escalonamento não funcionará até que seja corrigido.](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed)

## <a name="received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster"></a>Recebi um erro dizendo que o meu diretor de serviço não foi encontrado ou é inválido quando tento criar um novo cluster.

Ao criar um cluster AKS, requer um diretor de serviço ou uma identidade gerida para criar recursos em seu nome. O AKS pode criar automaticamente um novo diretor de serviço no momento da criação do cluster ou receber um existente. Ao utilizar um criado automaticamente, o Azure Ative Directory precisa de o propagar a todas as regiões para que a criação tenha sucesso. Quando a propagação demorar muito tempo, o cluster falhará a validação para criar, uma vez que não encontra um principal de serviço disponível para o fazer. 

Utilize as seguintes suposições para esta questão:
* Utilize um diretor de serviço existente, que já se propagou por regiões e existe para passar para AKS no cluster criar tempo.
* Se utilizar scripts de automação, adicione atrasos de tempo entre a criação principal de serviço e a criação de cluster AKS.
* Se utilizar o portal Azure, volte às definições do cluster durante a criação e volte a tentar a página de validação após alguns minutos.





## <a name="im-receiving-errors-after-restricting-egress-traffic"></a>Estou a receber erros depois de restringir o tráfego de esgress.

Ao restringir o tráfego de saída de um cluster AKS, são [necessárias e opcionais](limit-egress-traffic.md) portas/regras de rede recomendadas e regras de aplicação FQDN/aplicação para AKS. Se as suas configurações estiverem em conflito com alguma destas regras, certos `kubectl` comandos não funcionarão corretamente. Também pode ver erros ao criar um cluster AKS.

Verifique se as suas definições não estão em conflito com nenhuma das portas/regras de saída recomendadas recomendadas ou recomendadas opcionalmente e regras de aplicação FQDN/aplicação.

## <a name="azure-storage-and-aks-troubleshooting"></a>Armazenamento Azure e resolução de problemas AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Quais são as versões estáveis recomendadas de Kubernetes para o disco Azure? 

| Versão Kubernetes | Versão recomendada |
|--|:--:|
| 1.12 | 1.12.9 ou mais tarde |
| 1.13 | 1.13.6 ou mais tarde |
| 1.14 | 1.14.2 ou mais tarde |


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
|--|:--:|
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

Pode mitigar a questão fazendo uma das opções:

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

* Utilize `chown` em recipientes initas para definir gid e uid. Por exemplo:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Falha de desvinculação do Disco Azure que conduz a problema potencial de condição de corrida e lista de discos de dados inválidos

Quando um Disco Azure não se desprender, irá voltar a tentar até seis vezes para desmontar o disco utilizando o back off exponencial. Também manterá um bloqueio de nível de nó na lista de discos de dados durante cerca de 3 minutos. Se a lista de discos for atualizada manualmente durante esse período, fará com que a lista de discos mantida pelo bloqueio do nível do nó seja obsoleta e cause instabilidade no nó.

Esta questão foi corrigida nas seguintes versões da Kubernetes:

| Versão Kubernetes | Versão fixa |
|--|:--:|
| 1.12 | 1.12.9 ou mais tarde |
| 1.13 | 1.13.6 ou mais tarde |
| 1.14 | 1.14.2 ou mais tarde |
| 1.15 e mais tarde | N/D |

Se estiver a usar uma versão de Kubernetes que não tenha a correção para este problema e o seu nó tiver uma lista de discos obsoletos, pode atenuar todos os discos não existentes do VM como uma operação a granel. **A desvinculação individual dos discos não existentes pode falhar.**

### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Grande número de discos Azure causa ligação/desprendimento lento

Quando os números de operações de fixação/desapego do Disco Azure que visam um único VM de nó é superior a 10, ou superior a 3 quando se destina ruma uma única balança de conjuntos de máquinas virtuais, podem ser mais lentos do que o esperado, uma vez que são feitos sequencialmente. Esta questão é uma limitação conhecida e não há sobras neste momento. [Item de voz do utilizador para suportar anexação/desapego paralelo para além](https://feedback.azure.com/forums/216843-virtual-machines/suggestions/40444528-vmss-support-for-parallel-disk-attach-detach-for)do número. . .

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Falha no destacamento do Disco Azure conduz a potencial VM do nó em estado falhado

Em alguns casos de borda, um destacamento de Disco Azure pode parcialmente falhar e deixar o VM do nó em estado falhado.

Esta questão foi corrigida nas seguintes versões da Kubernetes:

| Versão Kubernetes | Versão fixa |
|--|:--:|
| 1.12 | 1.12.10 ou mais tarde |
| 1.13 | 1.13.8 ou mais tarde |
| 1.14 | 1.14.4 ou mais tarde |
| 1.15 e mais tarde | N/D |

Se estiver a usar uma versão de Kubernetes que não tenha a correção para este problema e o seu nó estiver em estado de falhas, pode atenuar atualizando manualmente o estado de VM usando um dos seguintes:

* Para um cluster baseado em conjunto de disponibilidade:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* Para um cluster baseado em VMSS:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Ficheiros Azure e resolução de problemas aks

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

As opções de montagem podem ser especificadas no objeto da classe de armazenamento. O exemplo que se segue é o *0777:*

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
* *Nobrl* impedirá o envio de pedidos de bloqueio de alcance byte para o servidor. Esta definição é necessária para certas aplicações que rompem com o estilo cifs de byte obrigatório fechaduras de gama. A maioria dos servidores do CIFS ainda não suportam pedidos de bloqueios de alcance de byte sinte. Se não utilizar *nobrl,* as aplicações que rompam com o estilo cifs os bloqueios obrigatórios de byte podem causar mensagens de erro semelhantes a:
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

Se permitir *o acesso a partir de rede selecionada* numa conta de armazenamento que é usada para o provisionamento dinâmico no AKS, terá um erro quando o AKS criar uma partilha de ficheiros:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Este erro *deve-se* ao facto de o controlador de volume persistente Kubernetes não estar na rede escolhida quando a definição *permite o acesso a partir de uma rede selecionada*.

Pode mitigar o problema utilizando [o fornecimento estático com ficheiros Azure](azure-files-volume.md).

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Ficheiros Azure falham na remontagem no Windows pod

Se um pod Windows com uma montagem de Ficheiros Azure for eliminado e depois programado para ser recriado no mesmo nó, o monte falhará. Esta falha deve-se à falha do comando, uma vez que `New-SmbGlobalMapping` o suporte dos Ficheiros Azure já está montado no nó.

Por exemplo, pode ver um erro semelhante ao:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Esta questão foi corrigida nas seguintes versões da Kubernetes:

| Versão Kubernetes | Versão fixa |
|--|:--:|
| 1.12 | 1.12.6 ou mais tarde |
| 1.13 | 1.13.4 ou mais tarde |
| 1.14 e mais tarde | N/D |

### <a name="azure-files-mount-fails-because-of-storage-account-key-changed"></a>Montagem de Ficheiros Azure falha por causa da chave da conta de armazenamento alterada

Se a chave da sua conta de armazenamento tiver mudado, poderá ver falhas de montagem do Azure Files.

Pode atenuar atualizando manualmente o campo manualmente num segredo de `azurestorageaccountkey` ficheiro Sinuoso com a chave de armazenamento codificada base64.

Para codificar a chave da sua conta de armazenamento no base64, pode utilizar `base64` . Por exemplo:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Para atualizar o seu ficheiro secreto Azure, utilize `kubectl edit secret` . Por exemplo:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Após alguns minutos, o nó de agente irá voltar a tentar o suporte do Ficheiro Azure com a chave de armazenamento atualizada.


### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>O autoscaler do cluster não consegue escalar com o erro falhado na fixação dos tamanhos do grupo do nó

Se o seu autoscaler de cluster não estiver a escalonar para cima/para baixo e vir um erro como o abaixo nos registos de [autoescalador][view-master-logs]do cluster .

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Este erro deve-se a uma condição de corrida autoescalar de cluster a montante. Neste caso, o autoscaler cluster termina com um valor diferente daquele que está realmente no cluster. Para sair deste estado, desative e reative o [autoescalador][cluster-autoscaler]do cluster .

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>Acessório de disco lento, GetAzureDiskLun demora 10 a 15 minutos e recebe um erro

Nas versões Kubernetes com mais de **1.15.0,** pode receber um erro como **o Error WaitForAttach Não pode encontrar Lun para o disco**.  A suposição para esta questão é esperar aproximadamente 15 minutos e voltar a tentar.

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
