---
title: Práticas recomendadas do operador – segurança do Cluster dos serviços de Kubernetes no Azure (AKS)
description: Conheça as práticas recomendadas de operador de cluster para saber como gerir a segurança do cluster e atualizações no Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: f02f6588946e2b63a1a092aba15603d1685e8207
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594809"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Melhores práticas para segurança do cluster e atualizações no Azure Kubernetes Service (AKS)

Como gerir clusters no Azure Kubernetes Service (AKS), a segurança dos seus dados e cargas de trabalho é uma consideração fundamental. Especialmente quando executar clusters de vários inquilinos com isolamento lógico, terá de proteger o acesso a recursos e cargas de trabalho. Para minimizar o risco de ataque, terá também de certificar-se de que aplicar o Kubernetes mais recente e atualizações de segurança do sistema operacional de nó.

Este artigo se concentra em como proteger o seu cluster do AKS. Saiba como:

> [!div class="checklist"]
> * Utilizar o Azure Active Directory e controlos de acesso baseado em funções para proteger o acesso ao servidor de API
> * Contentores seguros de acesso a recursos de nó
> * Atualizar um cluster do AKS para a versão mais recente do Kubernetes
> * Mantenha os nódosos atualizados e aplique automaticamente patches de segurança

Também pode ler as melhores práticas para [a gestão][best-practices-container-image-management] de imagem de contentores e para segurança do [casulo.][best-practices-pod-security]

Também pode utilizar a integração de [Serviços Azure Kubernetes com o Centro][security-center-aks] de Segurança para ajudar a detetar ameaças e ver recomendações para garantir os seus clusters AKS.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Proteger o acesso a nós de cluster e servidor de API

**Orientação** de boas práticas - Garantir o acesso ao Kubernetes API-Server é uma das coisas mais importantes que pode fazer para proteger o seu cluster. Integre o Kubernetes controlo de acesso baseado em funções (RBAC) com o Azure Active Directory para controlar o acesso ao servidor de API. Esses controles permitem-lhe proteger AKS da mesma forma que proteger o acesso às suas subscrições do Azure.

O servidor de API do Kubernetes fornece um ponto de ligação único para pedidos de execução de ações dentro de um cluster. Para proteger e auditar o acesso ao servidor de API, limitar o acesso e fornecem as permissões de acesso com privilégios mínimos necessárias. Essa abordagem não é exclusiva para o Kubernetes, mas é especialmente importante quando o cluster do AKS está logicamente isolado para utilização do multi-inquilino.

O Azure Active Directory (AD) fornece uma solução de gestão de identidades de prontas para empresas que se integra com os clusters do AKS. Como o Kubernetes não fornece uma solução de gestão de identidades, caso contrário, pode ser difícil fornecer uma forma granular para restringir o acesso ao servidor de API. Com o Azure clusters integrada no AD no AKS, usa suas contas de grupo e um utilizador existente para autenticar os utilizadores para o servidor de API.

![Integração do Active Directory do Azure para os clusters do AKS](media/operator-best-practices-cluster-security/aad-integration.png)

Utilizar o RBAC de Kubernetes e o Azure integração do AD para proteger o servidor de API e fornecer o menor número de permissões necessárias para um conjunto de âmbito de recursos, como um único espaço de nomes. Podem ser concedidos diferentes funções do RBAC a diferentes utilizadores ou grupos no Azure AD. Estas permissões granulares permitem-lhe restringir o acesso ao servidor de API e fornecer uma trilha de auditoria clara de ações executadas.

A melhor prática recomendada é usar grupos para fornecer acesso a ficheiros e pastas versus identidades individuais, usar a adesão do *grupo* Azure AD para vincular os utilizadores a funções RBAC em vez de *utilizadores*individuais. Como alterações de associação de grupo de um utilizador, as respetivas permissões de acesso no cluster do AKS deverá alterar em conformidade. Se o utilizador é ligar diretamente a uma função, poderá mudar a sua função de trabalho. Atualize as associações de grupo do Azure AD, mas que não seriam refletem as permissões no cluster do AKS. Neste cenário, o utilizador acaba sendo concedido mais permissões do que necessita de um utilizador.

Para obter mais informações sobre a integração da AD Azure e o RBAC, consulte [as melhores práticas de autenticação e autorização no AKS.][aks-best-practices-identity]

## <a name="secure-container-access-to-resources"></a>Contentores seguros de acesso a recursos

**Orientação das melhores práticas** - Limitar o acesso às ações que os contentores podem realizar. Fornecer o menor número de permissões e evitar o uso de raiz / privilegiado escalonamento.

Da mesma forma que só deverá conceder aos utilizadores ou grupos o menor número de privilégios necessários, contentores também devem ser limitados a apenas as ações e os processos que precisam. Para minimizar o risco de ataque, não configure aplicações e os contentores que exigem privilégios elevados ou acesso de raiz. Por exemplo, coloque `allowPrivilegeEscalation: false` no manifesto da cápsula. Estes contextos de segurança do pod são *incorporados* em Kubernetes e permitem definir permissões adicionais, como o utilizador ou grupo para executar como, ou quais as capacidades do Linux para expor. Para obter mais práticas, consulte [O acesso seguro aos recursos.][pod-security-contexts]

Para um maior controlo granular das ações dos contentores, também pode utilizar funcionalidades de segurança linux incorporadas, tais como *AppArmor* e *seccomp*. Esses recursos são definidos ao nível do nó e, em seguida, implementados através de um manifesto de pod. As funcionalidades de segurança linux incorporadas só estão disponíveis em nódos e casulos Linux.

> [!NOTE]
> Os ambientes kubernetes, em AKS ou em qualquer outro lugar, não são completamente seguros para o uso hostil de multi-inquilinos. Funcionalidades de segurança adicionais como *AppArmor,* *seccomp,* *Pod Security Policies*ou controlos de acesso baseados em funções mais finos (RBAC) para nós dificultam as explorações. No entanto, para uma verdadeira segurança quando se executam cargas de trabalho hostis de multi-inquilinos, um hipervisor é o único nível de segurança em que se deve confiar. O domínio de segurança de Kubernetes torna-se todo o cluster, não um nó individual. Para este tipo de cargas de trabalho hostis multi-inquilinos, você deve usar clusters fisicamente isolados.

### <a name="app-armor"></a>Armadura de aplicação

Para limitar as ações que os contentores podem executar, pode utilizar o módulo de segurança do kernel [AppArmor][k8s-apparmor] Linux. AppArmor está disponível como parte do sistema operacional, do nó AKS subjacente e está ativada por predefinição. Criar AppArmor perfis que restringem ações como leitura, gravação ou executarem ou funções de sistema, como a montagem de sistemas de ficheiros. Os perfis padrão da AppArmor restringem o acesso a vários locais de `/proc` e `/sys`, e fornecem um meio para isolar logicamente os contentores do nó subjacente. AppArmor funciona para qualquer aplicação que é executada no Linux, não apenas os pods do Kubernetes.

![Perfis de AppArmor em utilização num cluster do AKS para limitar as ações de contentor](media/operator-best-practices-container-security/apparmor.png)

Para ver AppArmor em ação, o exemplo seguinte cria um perfil que impede que gravar em arquivos. [SSH][aks-ssh] a um nó AKS, em seguida, criar um ficheiro chamado *deny-write.profile* e colar o seguinte conteúdo:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

Os perfis da AppArmor são adicionados utilizando o comando `apparmor_parser`. Adicionar o perfil para AppArmor e especifique o nome do perfil criado no passo anterior:

```console
sudo apparmor_parser deny-write.profile
```

Não há nenhuma saída devolvida se o perfil é analisado e aplicado a AppArmor corretamente. Forem retornados ao prompt de comando.

A partir da sua máquina local, crie agora um manifesto de pod chamado *aks-apparmor.yaml* e colá-lo o seguinte conteúdo. Este manifesto define uma anotação para `container.apparmor.security.beta.kubernetes` adicionar referências ao perfil *de deny-write* criado nos passos anteriores:

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

Implante a amostra utilizando o [comando de aplicação kubectl:][kubectl-apply]

```console
kubectl apply -f aks-apparmor.yaml
```

Com a cápsula implantada, use o comando [executivo kubectl][kubectl-exec] para escrever para um ficheiro. Não é possível executar o comando, conforme mostrado no seguinte exemplo:

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

Para obter mais informações sobre o AppArmor, consulte [os perfis da AppArmor em Kubernetes][k8s-apparmor].

### <a name="secure-computing"></a>Proteger a computação

Enquanto a AppArmor trabalha para qualquer aplicação Linux, a [seccomp *(sec*ure *up*uting)][seccomp] trabalha a nível do processo. Seccomp também é um módulo de segurança de kernel do Linux e é suportado nativamente pelo tempo de execução do Docker utilizado por nós do AKS. Com seccomp, as chamadas de processo que podem executar contentores são limitadas. Criar filtros que definem quais ações para permitir ou negar e, em seguida, utilizar anotações dentro de um manifesto YAML de pod para associar o filtro de seccomp. Essa evolução se alinha à melhor prática de apenas o contentor a conceder as permissões mínimas que são necessários para executar e nada mais.

Para ver seccomp em ação, crie um filtro que impeça a alterar permissões num arquivo. [SSH][aks-ssh] para um nó AKS, em seguida, criar um filtro de seccomp chamado */var/lib/kubelet/seccomp/prevent-chmod* e colar o seguinte conteúdo:

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

A partir da sua máquina local, crie agora um manifesto de pod chamado *aks-seccomp.yaml* e colá o seguinte conteúdo. Este manifesto define uma anotação para `seccomp.security.alpha.kubernetes.io` e refere o filtro *prevent-chmod* criado na etapa anterior:

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

Implante a amostra utilizando o [comando de aplicação kubectl:][kubectl-apply]

```console
kubectl apply -f ./aks-seccomp.yaml
```

Veja o estado das cápsulas utilizando o comando [kubectl get pods.][kubectl-get] O pod relata um erro. O comando `chmod` é impedido de ser dirigido pelo filtro seccomp, como mostra a seguinte saída de exemplo:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Para obter mais informações sobre os filtros disponíveis, consulte os perfis de [segurança da Seccomp para o Docker][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Atualizar regularmente para a versão mais recente do Kubernetes

**Orientação de boas práticas** - Para se manter atual em novas funcionalidades e correções de bugs, atualize regularmente para a versão Kubernetes no seu cluster AKS.

Novos recursos a um ritmo mais rápido que plataformas de infraestruturas mais tradicionais de lançamentos do Kubernetes. Atualizações de Kubernetes incluem novas funcionalidades e correções de bug ou segurança. As novas funcionalidades normalmente movem-se através de um *estatuto alfa* e *depois beta* antes de se tornarem *estáveis* e estão geralmente disponíveis e recomendadas para uso da produção. Este ciclo de lançamento deve permitir a atualizar o Kubernetes sem regularmente se deparar com alterações significativas ou ajustar suas implementações e modelos.

AKS oferece suporte a quatro versões secundárias do Kubernetes. Isso significa que, quando uma nova versão de patch secundária é introduzida, as mais antigas pequenas versão e patch em versões suportadas são descontinuadas. As atualizações menores de Kubernetes acontecem periodicamente. Certifique-se de que tem um processo de governação para verificar e atualizar conforme necessário para que não se enquadram sem suporte. Para mais informações, consulte as [versões Apoiadas kubernetes AKS][aks-supported-versions]

Para verificar as versões disponíveis para o seu cluster, utilize o comando de [atualizações az aks,][az-aks-get-upgrades] como mostra o seguinte exemplo:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Em seguida, pode atualizar o seu cluster AKS utilizando o comando de [upgrade az aks.][az-aks-upgrade] O processo de atualização com segurança cordons e drena o nó ao mesmo tempo, as agendas de pods em nós restantes e, em seguida, implementa um novo nó com as versões mais recentes do sistema operacional e o Kubernetes.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
```

Para obter mais informações sobre upgrades em AKS, consulte [versões De Kubernetes suportadas em AKS][aks-supported-versions] e [atualize um cluster AKS][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Processe atualizações de nó linux e reboots usando kured

**Orientação de boas práticas** - AKS descarrega e instala automaticamente correções de segurança em cada nó linux, mas não reinicia automaticamente se necessário. Utilize `kured` para vigiar as reinicializações pendentes, em seguida, penije com segurança e drene o nó para permitir que o nó reinicie, aplique as atualizações e seja o mais seguro possível no que diz respeito ao SISTEMA. Para os nós do Windows Server (atualmente em pré-visualização no AKS), execute regularmente uma operação de atualização AKS para isolar e drenar com segurança as cápsulas e implementar nós atualizados.

Todas as noites, os nós linux no AKS recebem patches de segurança disponíveis através do seu canal de atualização de distro. Este comportamento é configurado automaticamente como os nós são implementados num cluster do AKS. Para minimizar a interrupção e impacto potencial para executar cargas de trabalho, nós não são automaticamente reiniciados se um patch de segurança ou atualização de kernel o exigir.

O projeto de kured de código aberto [(KUbernetes REboot Daemon)][kured] da Weaveworks observa para reiniciar o nó pendente. Quando um nó Linux aplica atualizações que requerem um reboot, o nó é isolado e drenado com segurança para mover e agendar as cápsulas em outros nós do cluster. Assim que o nó é reiniciado, ele é adicionado novamente para o cluster e o agendamento de pods na mesma de retoma de Kubernetes. Para minimizar a perturbação, apenas um nó de cada vez é autorizado a ser reiniciado por `kured`.

![O processo de reinício de nó AKS utilizando kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Se quiser um controlo mais fino dos cereais quando ocorrerem reboots, `kured` pode integrar-se com a Prometheus para evitar reinicializações se houver outros eventos de manutenção ou problemas de cluster em curso. Esta integração minimiza complicações adicionais ao reiniciar nós enquanto estiver ativamente a resolver outros problemas.

Para obter mais informações sobre como lidar com reboots de nó, consulte Aplicar atualizações de [segurança e kernel em nós em AKS][aks-kured].

## <a name="next-steps"></a>Passos seguintes

Este artigo concentra-se sobre como proteger o seu cluster do AKS. Para implementar algumas dessas áreas, consulte os artigos seguintes:

* [Integrar o Diretório Ativo Azure com a AKS][aks-aad]
* [Atualizar um cluster AKS para a versão mais recente da Kubernetes][aks-upgrade]
* [Atualizações de segurança do processo e reinicializações de nó com kured][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
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
[aks-aad]: azure-ad-integration.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
[security-center-aks]: /azure/security-center/azure-kubernetes-service-integration
