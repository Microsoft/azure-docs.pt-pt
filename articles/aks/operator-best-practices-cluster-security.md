---
title: Melhores práticas para a segurança do cluster
titleSuffix: Azure Kubernetes Service
description: Aprenda as melhores práticas do operador do cluster para gerir a segurança do cluster e atualizações no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 9cb51cb0f5b902553bda0b881c8392d74905c4bc
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92073636"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Melhores práticas para segurança de clusters e upgrades no Serviço Azure Kubernetes (AKS)

À medida que gere clusters no Serviço Azure Kubernetes (AKS), a segurança das suas cargas de trabalho e dados é uma consideração fundamental. Especialmente quando você gere clusters multi-inquilinos usando isolamento lógico, você precisa garantir o acesso a recursos e cargas de trabalho. Para minimizar o risco de ataque, também precisa de se certificar de que aplica as mais recentes atualizações de segurança kubernetes e nós.

Este artigo centra-se em como proteger o seu cluster AKS. Saiba como:

> [!div class="checklist"]
> * Use o Azure Ative Directory e o controlo de acesso baseado em funções (RBAC) para garantir o acesso ao servidor API
> * Acesso seguro ao contentor aos recursos dos nódissais
> * Atualize um cluster AKS para a versão mais recente de Kubernetes
> * Mantenha os acenos atualizados e aplique automaticamente patches de segurança

Também pode ler as melhores práticas para [a gestão da imagem do contentor][best-practices-container-image-management] e para a segurança do [casulo.][best-practices-pod-security]

Também pode utilizar [a integração dos Serviços Azure Kubernetes com][security-center-aks] o Security Center para ajudar a detetar ameaças e ver recomendações para garantir os seus clusters AKS.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Acesso seguro ao servidor API e nosmes de cluster

**Orientação para as melhores práticas** - Garantir o acesso aos Kubernetes API-Server é uma das coisas mais importantes que pode fazer para garantir o seu cluster. Integre o controlo de acesso baseado em funções da Kubernetes (RBAC) com o Azure Ative Directory para controlar o acesso ao servidor API. Estes controlos permitem-lhe proteger a AKS da mesma forma que assegura o acesso às suas subscrições Azure.

O servidor API de Kubernetes fornece um único ponto de ligação para pedidos para executar ações dentro de um cluster. Para garantir e auditar o acesso ao servidor API, limite o acesso e forneça as permissões de acesso menos privilegiadas necessárias. Esta abordagem não é exclusiva de Kubernetes, mas é especialmente importante quando o cluster AKS é logicamente isolado para uso multi-inquilino.

O Azure Ative Directory (AD) fornece uma solução de gestão de identidade pronta para a empresa que se integra com clusters AKS. Como a Kubernetes não fornece uma solução de gestão de identidade, pode ser difícil fornecer uma forma granular de restringir o acesso ao servidor API. Com clusters integrados em AZure em AKS, utiliza as contas de utilizador e grupo existentes para autenticar os utilizadores no servidor API.

![Integração do Diretório Ativo Azure para clusters AKS](media/operator-best-practices-cluster-security/aad-integration.png)

Utilize a integração de Kubernetes RBAC e Azure AD para proteger o servidor API e fornecer o menor número de permissões necessárias a um conjunto de recursos, como um único espaço de nome. Diferentes utilizadores ou grupos em Azure AD podem ser concedidos diferentes papéis de RBAC. Estas permissões granulares permitem restringir o acesso ao servidor API e fornecer um claro rasto de auditoria das ações realizadas.

A melhor prática recomendada é usar grupos para fornecer acesso a ficheiros e pastas versus identidades individuais, usar a filiação *do grupo* AD AZure para vincular os utilizadores a papéis de RBAC em vez de *utilizadores*individuais. À medida que a adesão de um utilizador muda, as suas permissões de acesso no cluster AKS mudariam em conformidade. Se ligar o utilizador diretamente a uma função, a sua função de trabalho pode mudar. Os membros do grupo AZure AD atualizariam, mas as permissões no cluster AKS não refletiriam isso. Neste cenário, o utilizador acaba por receber mais permissões do que um utilizador necessita.

Para obter mais informações sobre a integração azure AD e o RBAC, consulte [as melhores práticas de autenticação e autorização em AKS][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Acesso seguro aos recursos dos contentores

**Orientação de boas práticas** - Limite o acesso a ações que os contentores podem realizar. Forneça o menor número de permissões e evite o uso de uma escalada raiz/privilegiada.

Da mesma forma que deve conceder aos utilizadores ou grupos o menor número de privilégios necessários, os contentores devem também limitar-se apenas às ações e processos de que necessitam. Para minimizar o risco de ataque, não configuure aplicações e recipientes que exijam privilégios e acessos de raiz. Por exemplo, coloque `allowPrivilegeEscalation: false` no manifesto da cápsula. Estes *contextos de segurança do pod* são incorporados em Kubernetes e permitem definir permissões adicionais, como o utilizador ou grupo para executar como, ou quais as capacidades do Linux para expor. Para obter mais boas práticas, consulte [o acesso seguro dos recursos ao pod.][pod-security-contexts]

Para um maior controlo granular das ações de contentores, também pode utilizar funcionalidades de segurança lídulas incorporadas, como *AppArmor* e *seccomp.* Estas características são definidas ao nível do nó e implementadas através de um manifesto de vagem. As funcionalidades de segurança lux incorporadas só estão disponíveis em nós e cápsulas Linux.

> [!NOTE]
> Os ambientes kubernetes, em AKS ou em qualquer outro lugar, não são completamente seguros para uso hostil de vários inquilinos. Funcionalidades de segurança adicionais como *AppArmor*, *seccomp,* *Pod Security Policies*ou mais controlo de acesso baseado em funções (RBAC) para nós tornam as explorações mais difíceis. No entanto, para uma verdadeira segurança ao executar cargas de trabalho hostis de vários inquilinos, um hipervisor é o único nível de segurança em que deve confiar. O domínio de segurança de Kubernetes torna-se todo o cluster, não um nó individual. Para este tipo de cargas de trabalho hostis multi-inquilinos, você deve usar aglomerados fisicamente isolados.

### <a name="app-armor"></a>Armadura de aplicativo

Para limitar as ações que os recipientes podem executar, pode utilizar o módulo de segurança do núcleo [AppArmor][k8s-apparmor] Linux. O AppArmor está disponível como parte do nó AKS subjacente e está ativado por padrão. Cria perfis AppArmor que restringem ações como ler, escrever ou executar ou funções do sistema, como a montagem de sistemas de ficheiros. Os perfis padrão do AppArmor restringem o acesso a `/proc` `/sys` vários locais e locais e fornecem um meio para isolar logicamente os contentores do nó subjacente. O AppArmor trabalha para qualquer aplicação que funcione no Linux, e não apenas para as cápsulas Kubernetes.

![Perfis AppArmor em uso num cluster AKS para limitar as ações de contentores](media/operator-best-practices-container-security/apparmor.png)

Para ver o AppArmor em ação, o exemplo a seguir cria um perfil que impede a escrita para ficheiros. [SSH][aks-ssh] para um nó AKS, em seguida, criar um ficheiro chamado *deny-write.profile* e colar o seguinte conteúdo:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

Os perfis appArmor são adicionados usando o `apparmor_parser` comando. Adicione o perfil ao AppArmor e especifique o nome do perfil criado no passo anterior:

```console
sudo apparmor_parser deny-write.profile
```

Não há saída devolvida se o perfil estiver corretamente analisado e aplicado ao AppArmor. Voltou ao comando.

A partir da sua máquina local, crie agora um manifesto de pod chamado *aks-apparmor.yaml* e cole o seguinte conteúdo. Este manifesto define uma anotação para `container.apparmor.security.beta.kubernetes` adicionar referências ao perfil *de negação-escrita* criado nos passos anteriores:

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

Implementar a cápsula de amostra utilizando o [comando de aplicação de kubectl:][kubectl-apply]

```console
kubectl apply -f aks-apparmor.yaml
```

Com a cápsula implantada, utilize o comando [executivo de kubectl][kubectl-exec] para escrever num ficheiro. O comando não pode ser executado, como mostra a seguinte saída de exemplo:

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

Para mais informações sobre o AppArmor, consulte [os perfis appArmor em Kubernetes.][k8s-apparmor]

### <a name="secure-computing"></a>Computação segura

Enquanto o AppArmor trabalha para qualquer aplicação Linux, [o seccomp *(sec*ure *comp*uting)][seccomp] funciona ao nível do processo. O Seccomp também é um módulo de segurança do kernel Linux, e é suportado de forma nativa pelo tempo de funcionação do Docker usado pelos nós AKS. Com o seccomp, as chamadas de processo que os contentores podem realizar são limitadas. Cria filtros que definem que ações permitir ou negar e, em seguida, utiliza anotações dentro de um manifesto YAML de vagem para associar ao filtro seccomp. Isto alinha-se com a melhor prática de apenas conceder ao contentor as permissões mínimas necessárias para funcionar, e não mais.

Para ver o seccomp em ação, crie um filtro que impeça a alteração de permissões num ficheiro. [SSH][aks-ssh] para um nó AKS, em seguida, criar um filtro seccomp chamado */var/lib/kubelet/seccomp/prevent-chmod* e colar o seguinte conteúdo:

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

A partir da sua máquina local, crie agora um manifesto de pod chamado *aks-seccomp.yaml* e cole o seguinte conteúdo. Este manifesto define uma anotação para `seccomp.security.alpha.kubernetes.io` e faz referência ao filtro *preventivo-chmod* criado no passo anterior:

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

Implementar a cápsula de amostra utilizando o [comando de aplicação de kubectl:][kubectl-apply]

```console
kubectl apply -f ./aks-seccomp.yaml
```

Veja o estado das cápsulas utilizando o comando [kubectl get pods.][kubectl-get] A cápsula relata um erro. O `chmod` comando é impedido de funcionar pelo filtro de seccomp, como mostra a saída do exemplo seguinte:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Para obter mais informações sobre filtros disponíveis, consulte [os perfis de segurança da Seccomp para o Docker.][seccomp]

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Atualizar regularmente para a versão mais recente de Kubernetes

**Orientação para as melhores práticas** - Para se manter atualizado sobre novas funcionalidades e correções de bugs, atualize regularmente a versão Kubernetes no seu cluster AKS.

A Kubernetes lança novas funcionalidades a um ritmo mais rápido do que as plataformas de infraestruturas mais tradicionais. As atualizações de Kubernetes incluem novas funcionalidades e correções de bug ou segurança. As novas funcionalidades normalmente movem-se através de um estatuto *alfa* e, em seguida, *beta* antes de ficarem *estáveis* e geralmente estão disponíveis e recomendadas para uso de produção. Este ciclo de lançamento deve permitir-lhe atualizar Kubernetes sem encontrar regularmente alterações de rutura ou ajustar as suas implementações e modelos.

AKS suporta três versões menores de Kubernetes. Isto significa que quando uma nova versão de patch menor é introduzida, a versão menor mais antiga e os lançamentos de patch suportados são retirados. Pequenas atualizações a Kubernetes acontecem periodicamente. Certifique-se de que tem um processo de governação para verificar e atualizar conforme necessário para que não deixe de suportar. Para obter mais informações, consulte [as versões AKS suportadas de Kubernetes.][aks-supported-versions]

Para verificar as versões disponíveis para o seu cluster, utilize o comando [az aks get-upgrades][az-aks-get-upgrades] como mostrado no seguinte exemplo:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Em seguida, pode atualizar o seu cluster AKS utilizando o comando [de atualização az aks.][az-aks-upgrade] O processo de atualização seguramente isola e drena um nó de cada vez, programa cápsulas nos nós restantes e, em seguida, implementa um novo nó executando as versões mais recentes de SO e Kubernetes.

É altamente recomendado testar novas versões menores num ambiente de teste dev para que possa validar a sua carga de trabalho continua o funcionamento saudável com a nova versão Kubernetes. Kubernetes pode depreciar APIs, como na versão 1.16, que poderia ser invocada pelas suas cargas de trabalho. Ao introduzir novas versões em produção, considere usar [várias piscinas de nós em versões separadas](use-multiple-node-pools.md) e atualizar piscinas individuais uma de cada vez para lançar progressivamente a atualização através de um cluster. Se executar vários clusters, atualize um cluster de cada vez para monitorizar progressivamente o impacto ou alterações.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
```

Para obter mais informações sobre upgrades em AKS, consulte [as versões Kubernetes suportadas em AKS][aks-supported-versions] e [atualize um cluster AKS][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Processa atualizações e reboots de nó Linux

**Orientação para as melhores práticas** - AKS descarrega e instala automaticamente correções de segurança em cada nó Linux, mas não reinicia automaticamente se necessário. Utilize `kured` para observar as reinicializações pendentes, depois isole com segurança e escorra o nó para permitir que o nó reinicie, aplique as atualizações e seja o mais seguro possível no que diz respeito ao SO. Para os nós do Windows Server, efetue regularmente uma operação de atualização AKS para isolar e drenar com segurança as cápsulas e implementar nós atualizados.

Todas as noites, os nós Linux em AKS obtêm patches de segurança disponíveis através do seu canal de atualização distro. Este comportamento é configurado automaticamente à medida que os nós são implantados num cluster AKS. Para minimizar a perturbação e o impacto potencial nas cargas de trabalho de funcionamento, os nós não são automaticamente reiniciados se um patch de segurança ou uma atualização de kernel o exigir.

O projeto [open-source kured (KUbernetes REboot Daemon)][kured] da Weaveworks observa para reboots pendentes de nó. Quando um nó Linux aplica atualizações que requerem um reboot, o nó é seguramente isolado e drenado para mover e agendar as cápsulas em outros nós no cluster. Uma vez reiniciado o nó, é adicionado de volta ao cluster e Kubernetes retoma as cápsulas de agendamento nele. Para minimizar a perturbação, apenas um nó de cada vez é autorizado a ser reiniciado por `kured` .

![O processo de reinicialização do nó AKS usando kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Se quiser um controlo de grãos mais fino quando ocorrerem reboots, `kured` pode integrar-se com a Prometheus para evitar reinicializações se houver outros eventos de manutenção ou problemas de cluster em curso. Esta integração minimiza complicações adicionais reiniciando os nós enquanto está a resolver ativamente outros problemas.

Para obter mais informações sobre como lidar com as reinicializações de nó, consulte [Aplicar atualizações de segurança e kernel aos nós em AKS][aks-kured].

## <a name="next-steps"></a>Passos seguintes

Este artigo focou-se em como proteger o seu cluster AKS. Para implementar algumas destas áreas, consulte os seguintes artigos:

* [Integre o Azure Ative Directy com a AKS][aks-aad]
* [Atualize um cluster AKS para a versão mais recente de Kubernetes][aks-upgrade]
* [Atualizações de segurança do processo e reinicializes de nó com kured][aks-kured]

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
[aks-aad]: ./azure-ad-integration-cli.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
[security-center-aks]: ../security-center/defender-for-kubernetes-introduction.md