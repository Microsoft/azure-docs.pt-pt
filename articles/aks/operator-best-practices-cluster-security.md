---
title: Práticas recomendadas do operador – segurança do Cluster dos serviços de Kubernetes no Azure (AKS)
description: Conheça as práticas recomendadas de operador de cluster para saber como gerir a segurança do cluster e atualizações no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: iainfou
ms.openlocfilehash: b0d6afbe2db4c95460aef96a9d918219bd4240e2
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769885"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Melhores práticas para segurança do cluster e atualizações no Azure Kubernetes Service (AKS)

Como gerir clusters no Azure Kubernetes Service (AKS), a segurança dos seus dados e cargas de trabalho é uma consideração fundamental. Especialmente quando executar clusters de vários inquilinos com isolamento lógico, terá de proteger o acesso a recursos e cargas de trabalho. Para minimizar o risco de ataque, terá também de certificar-se de que aplicar o Kubernetes mais recente e atualizações de segurança do sistema operacional de nó.

Este artigo se concentra em como proteger o seu cluster do AKS. Saiba como:

> [!div class="checklist"]
> * Utilizar o Azure Active Directory e controlos de acesso baseado em funções para proteger o acesso ao servidor de API
> * Contentores seguros de acesso a recursos de nó
> * Atualizar um cluster do AKS para a versão mais recente do Kubernetes
> * Manter a atualização de nós até à data e automaticamente aplicar patches de segurança

Também pode ler as melhores práticas para [gerenciamento de imagens de contentor] [ best-practices-container-image-management] e para [pod segurança][best-practices-pod-security].

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Proteger o acesso a nós de cluster e servidor de API

**Melhores diretrizes de práticas** -proteger o acesso ao servidor de API Kubernetes é uma das coisas mais importantes que pode fazer para proteger o seu cluster. Integre o Kubernetes controlo de acesso baseado em funções (RBAC) com o Azure Active Directory para controlar o acesso ao servidor de API. Esses controles permitem-lhe proteger AKS da mesma forma que proteger o acesso às suas subscrições do Azure.

O servidor de API do Kubernetes fornece um ponto de ligação único para pedidos de execução de ações dentro de um cluster. Para proteger e auditar o acesso ao servidor de API, limitar o acesso e fornecem as permissões de acesso com privilégios mínimos necessárias. Essa abordagem não é exclusiva para o Kubernetes, mas é especialmente importante quando o cluster do AKS está logicamente isolado para utilização do multi-inquilino.

O Azure Active Directory (AD) fornece uma solução de gestão de identidades de prontas para empresas que se integra com os clusters do AKS. Como o Kubernetes não fornece uma solução de gestão de identidades, caso contrário, pode ser difícil fornecer uma forma granular para restringir o acesso ao servidor de API. Com o Azure clusters integrada no AD no AKS, usa suas contas de grupo e um utilizador existente para autenticar os utilizadores para o servidor de API.

![Integração do Active Directory do Azure para os clusters do AKS](media/operator-best-practices-cluster-security/aad-integration.png)

Utilizar o RBAC de Kubernetes e o Azure integração do AD para proteger o servidor de API e fornecer o menor número de permissões necessárias para um conjunto de âmbito de recursos, como um único espaço de nomes. Podem ser concedidos diferentes funções do RBAC a diferentes utilizadores ou grupos no Azure AD. Estas permissões granulares permitem-lhe restringir o acesso ao servidor de API e fornecer uma trilha de auditoria clara de ações executadas.

A prática recomendada é usar grupos para fornecer acesso a ficheiros e pastas em comparação com identidades individuais, utilize o Azure AD *grupo* associação para vincular os utilizadores a funções RBAC em vez de pessoa *utilizadores*. Como alterações de associação de grupo de um utilizador, as respetivas permissões de acesso no cluster do AKS deverá alterar em conformidade. Se o utilizador é ligar diretamente a uma função, poderá mudar a sua função de trabalho. Atualize as associações de grupo do Azure AD, mas que não seriam refletem as permissões no cluster do AKS. Neste cenário, o utilizador acaba sendo concedido mais permissões do que necessita de um utilizador.

Para obter mais informações sobre a integração do Azure AD e o RBAC, veja [melhores práticas para autenticação e autorização no AKS][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Contentores seguros de acesso a recursos

**Melhores diretrizes de práticas** -limitar o acesso a ações que podem executar contentores. Fornecer o menor número de permissões e evitar o uso de raiz / privilegiado escalonamento.

Da mesma forma que só deverá conceder aos utilizadores ou grupos o menor número de privilégios necessários, contentores também devem ser limitados a apenas as ações e os processos que precisam. Para minimizar o risco de ataque, não configure aplicações e os contentores que exigem privilégios elevados ou acesso de raiz. Por exemplo, definir `allowPrivilegeEscalation: false` no manifesto do pod. Estes *pod contextos de segurança* incorporados no Kubernetes e permitem que define permissões adicionais, como o utilizador ou grupo para executar como, ou que capacidades de Linux para expor. Para obter mais melhores práticas, consulte [pod segura de acesso a recursos][pod-security-contexts].

Para um controle mais granular de ações de contentor, também pode utilizar as funcionalidades de segurança incorporadas do Linux, como *AppArmor* e *seccomp*. Esses recursos são definidos ao nível do nó e, em seguida, implementados através de um manifesto de pod.

> [!NOTE]
> Ambientes do Kubernetes no AKS ou noutro local, não são totalmente seguros para utilização multi-inquilinos hostil. Funcionalidades de segurança adicional, como *AppArmor*, *seccomp*, *políticas de segurança de Pod*, ou mais controlos de acesso detalhado baseada em funções (RBAC) para nós fazem explorações mais difícil. No entanto, para segurança verdadeira quando executar cargas de trabalho de multi-inquilinos hostis, um hipervisor é o único nível de segurança que deve confiar. O domínio de segurança para Kubernetes torna-se a todo o cluster, não é um nó individual. Para estes tipos de cargas de trabalho de multi-inquilinos hostis, deve usar clusters fisicamente isolados.

### <a name="app-armor"></a>Armadura de aplicação

Para limitar as ações que podem executar contentores, pode utilizar o [AppArmor] [ k8s-apparmor] módulo de segurança de kernel do Linux. AppArmor está disponível como parte do sistema operacional, do nó AKS subjacente e está ativada por predefinição. Criar AppArmor perfis que restringem ações como leitura, gravação ou executarem ou funções de sistema, como a montagem de sistemas de ficheiros. Perfis de AppArmor predefinidos restringem o acesso a vários `/proc` e `/sys` locais e fornecer um meio para isolar logicamente contentores a partir do nó subjacente. AppArmor funciona para qualquer aplicação que é executada no Linux, não apenas os pods do Kubernetes.

![Perfis de AppArmor em utilização num cluster do AKS para limitar as ações de contentor](media/operator-best-practices-container-security/apparmor.png)

Para ver AppArmor em ação, o exemplo seguinte cria um perfil que impede que gravar em arquivos. [SSH] [ aks-ssh] para um nó do AKS, em seguida, crie um ficheiro denominado *write.profile negar* e cole o seguinte conteúdo:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

Perfis de AppArmor são adicionados usando o `apparmor_parser` comando. Adicionar o perfil para AppArmor e especifique o nome do perfil criado no passo anterior:

```console
sudo apparmor_parser deny-write.profile
```

Não há nenhuma saída devolvida se o perfil é analisado e aplicado a AppArmor corretamente. Forem retornados ao prompt de comando.

A partir de seu computador local, agora criar um manifesto de pod com o nome *aks apparmor.yaml* e cole o seguinte conteúdo. Esse manifesto define uma anotação para `container.apparmor.security.beta.kubernetes` adicione referências a *recusa a gravação* perfil criado nos passos anteriores:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-apparmor
  annotations:
    container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
spec:
  containers:
  - name: hello
    image: busybox
    command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
```

Implementar o pod de exemplo com o [aplicam-se de kubectl] [ kubectl-apply] comando:

```console
kubectl apply -f aks-apparmor.yaml
```

Com o pod implementado, utilize o [kubectl exec] [ kubectl-exec] comando para escrever num ficheiro. Não é possível executar o comando, conforme mostrado no seguinte exemplo:

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

Para obter mais informações sobre AppArmor, consulte [AppArmor perfis no Kubernetes][k8s-apparmor].

### <a name="secure-computing"></a>Proteger a computação

Embora AppArmor funcione em qualquer aplicação do Linux [seccomp (*seg*urar *comp*uting)] [ seccomp] funciona ao nível do processo. Seccomp também é um módulo de segurança de kernel do Linux e é suportado nativamente pelo tempo de execução do Docker utilizado por nós do AKS. Com seccomp, as chamadas de processo que podem executar contentores são limitadas. Criar filtros que definem quais ações para permitir ou negar e, em seguida, utilizar anotações dentro de um manifesto YAML de pod para associar o filtro de seccomp. Essa evolução se alinha à melhor prática de apenas o contentor a conceder as permissões mínimas que são necessários para executar e nada mais.

Para ver seccomp em ação, crie um filtro que impeça a alterar permissões num arquivo. [SSH] [ aks-ssh] para um nó do AKS, em seguida, crie um filtro de seccomp com o nome */var/lib/kubelet/seccomp/prevent-chmod* e cole o seguinte conteúdo:

```
{
  "defaultAction": "SCMP_ACT_ALLOW",
  "syscalls": [
    {
      "name": "chmod",
      "action": "SCMP_ACT_ERRNO"
    }
  ]
}
```

A partir de seu computador local, agora criar um manifesto de pod com o nome *aks seccomp.yaml* e cole o seguinte conteúdo. Esse manifesto define uma anotação para `seccomp.security.alpha.kubernetes.io` e faz referência a *chmod impedir* filtro criado no passo anterior:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: chmod-prevented
  annotations:
    seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
spec:
  containers:
  - name: chmod
    image: busybox
    command:
      - "chmod"
    args:
     - "777"
     - /etc/hostname
  restartPolicy: Never
```

Implementar o pod de exemplo com o [aplicam-se de kubectl] [ kubectl-apply] comando:

```console
kubectl apply -f ./aks-seccomp.yaml
```

Ver o estado de pods com o [kubectl obter pods] [ kubectl-get] comando. O pod relata um erro. O `chmod` comando é impedido de execução pelo filtro seccomp, conforme mostrado no seguinte exemplo:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Para obter mais informações sobre filtros disponíveis, consulte [Seccomp perfis de segurança para o Docker][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Atualizar regularmente para a versão mais recente do Kubernetes

**Melhores diretrizes de práticas** - para manter-se atualizado sobre novos recursos e correções de erros, regularmente atualizar para a versão do Kubernetes no seu cluster do AKS.

Novos recursos a um ritmo mais rápido que plataformas de infraestruturas mais tradicionais de lançamentos do Kubernetes. Atualizações de Kubernetes incluem novas funcionalidades e correções de bug ou segurança. Novos recursos, normalmente, mover através de um *alpha* e, em seguida *beta* estado antes que se tornem *estável* e estão geralmente disponíveis e recomendados para utilização em produção. Este ciclo de lançamento deve permitir a atualizar o Kubernetes sem regularmente se deparar com alterações significativas ou ajustar suas implementações e modelos.

AKS oferece suporte a quatro versões secundárias do Kubernetes. Isso significa que, quando uma nova versão de patch secundária é introduzida, as mais antigas pequenas versão e patch em versões suportadas são descontinuadas. As atualizações menores de Kubernetes acontecem periodicamente. Certifique-se de que tem um processo de governação para verificar e atualizar conforme necessário para que não se enquadram sem suporte. Para obter mais informações, consulte [versões de suportado Kubernetes AKS][aks-supported-versions]

Para verificar as versões que estão disponíveis para o seu cluster, utilize o [az aks get-atualizações] [ az-aks-get-upgrades] comando conforme mostrado no exemplo a seguir:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Em seguida, pode atualizar o cluster do AKS com o [az aks upgrade] [ az-aks-upgrade] comando. O processo de atualização com segurança cordons e drena o nó ao mesmo tempo, as agendas de pods em nós restantes e, em seguida, implementa um novo nó com as versões mais recentes do sistema operacional e o Kubernetes.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.11.8
```

Para obter mais informações sobre as atualizações no AKS, consulte [versões suportadas Kubernetes no AKS] [ aks-supported-versions] e [atualizar um cluster do AKS][aks-upgrade].

## <a name="process-node-updates-and-reboots-using-kured"></a>Nó de processo de atualizações e reinicializações usando kured

**Melhores diretrizes de práticas** - AKS automaticamente downloads e instalações de segurança corrige em cada um de nós de trabalho, mas não reiniciar automaticamente o computador se for necessário. Utilize `kured` para observar reinicializações pendentes, em seguida, com segurança cordão e drenagem do nó para permitir que o nó reiniciar o computador, aplicar as atualizações e ser o mais segura possível em relação ao sistema operacional.

Todas as noites, os nós do AKS obtém patches de segurança disponíveis por meio de seus distro atualizar canal. Este comportamento é configurado automaticamente como os nós são implementados num cluster do AKS. Para minimizar a interrupção e impacto potencial para executar cargas de trabalho, nós não são automaticamente reiniciados se um patch de segurança ou atualização de kernel o exigir.

O código-fonte aberto [kured (KUbernetes reiniciar Daemon)] [ kured] projeto por Weaveworks monitoriza nó reinicializações pendentes. Quando um nó aplica atualizações que requerem um reinício, o nó com segurança é isolado e drenado para mover e agendar os pods nos outros nós do cluster. Assim que o nó é reiniciado, ele é adicionado novamente para o cluster e o agendamento de pods na mesma de retoma de Kubernetes. Para minimizar a interrupção, apenas um nó por vez tem permissão para ser reiniciado pelo `kured`.

![O processo de reinício de nó AKS utilizando kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Se pretender que o controle de intervalo de agregação rigoroso em reinícios acontecem quando, `kured` pode ser integrado com Prometheus para impedir reinícios, se existirem outros eventos de manutenção ou problemas de cluster está em curso. Esta integração minimiza complicações adicionais ao reiniciar nós enquanto estiver ativamente a resolver outros problemas.

Para obter mais informações sobre como lidar com os reinícios de nó, veja [aplicar atualizações de segurança e de kernel para nós no AKS][aks-kured].

## <a name="next-steps"></a>Passos Seguintes

Este artigo concentra-se sobre como proteger o seu cluster do AKS. Para implementar algumas dessas áreas, consulte os artigos seguintes:

* [Integrar o Azure Active Directory com o AKS][aks-aad]
* [Atualizar um cluster do AKS para a versão mais recente do Kubernetes][aks-upgrade]
* [Atualizações de segurança do processo e o nó é reiniciado com kured][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://docs.docker.com/engine/security/seccomp/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: aad-integration.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
