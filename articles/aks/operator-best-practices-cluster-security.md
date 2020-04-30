---
title: Boas práticas para a segurança do cluster
titleSuffix: Azure Kubernetes Service
description: Conheça as melhores práticas do operador de cluster para gerir a segurança do cluster e upgrades no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 305d4c15aaf72a47549497902e3027064fbfd608
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82208096"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Boas práticas para segurança de clusters e upgrades no Serviço Azure Kubernetes (AKS)

À medida que gere clusters no Serviço Azure Kubernetes (AKS), a segurança das suas cargas de trabalho e dados é uma consideração fundamental. Especialmente quando você executa aglomerados multi-inquilinos usando isolamento lógico, você precisa garantir acesso a recursos e cargas de trabalho. Para minimizar o risco de ataque, também precisa de se certificar de que aplica as mais recentes atualizações de segurança kubernetes e node OS.

Este artigo centra-se em como proteger o seu cluster AKS. Saiba como:

> [!div class="checklist"]
> * Utilize o Diretório Ativo Azure e os controlos de acesso baseados em funções para garantir o acesso ao servidor API
> * Acesso seguro de contentores aos recursos do nó
> * Atualize um cluster AKS para a versão mais recente da Kubernetes
> * Mantenha os nódosos atualizados e aplique automaticamente patches de segurança

Também pode ler as melhores práticas para [a gestão][best-practices-container-image-management] de imagem de contentores e para segurança do [casulo.][best-practices-pod-security]

Também pode utilizar a integração de [Serviços Azure Kubernetes com o Centro][security-center-aks] de Segurança para ajudar a detetar ameaças e ver recomendações para garantir os seus clusters AKS.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Acesso seguro ao servidor API e nós de cluster

**Orientação** de boas práticas - Garantir o acesso ao Kubernetes API-Server é uma das coisas mais importantes que pode fazer para proteger o seu cluster. Integre o controlo de acesso baseado em funções da Kubernetes (RBAC) com o Diretório Ativo Azure para controlar o acesso ao servidor API. Estes controlos permitem-lhe assegurar aks da mesma forma que garante o acesso às suas subscrições Do Azure.

O servidor Kubernetes API fornece um único ponto de ligação para pedidos para realizar ações dentro de um cluster. Para garantir e auditar o acesso ao servidor API, limite o acesso e forneça as permissões de acesso menos privilegiadas necessárias. Esta abordagem não é exclusiva de Kubernetes, mas é especialmente importante quando o cluster AKS é logicamente isolado para uso multi-inquilino.

O Azure Ative Directory (AD) fornece uma solução de gestão de identidade pronta para a empresa que se integra com clusters AKS. Como a Kubernetes não fornece uma solução de gestão de identidade, de outra forma pode ser difícil fornecer uma forma granular de restringir o acesso ao servidor API. Com clusters integrados em AD Azure no AKS, utiliza as suas contas de utilizador e grupo existentes para autenticar utilizadores no servidor API.

![Integração de Diretório Sonárte Azure para clusters AKS](media/operator-best-practices-cluster-security/aad-integration.png)

Utilize a integração de Kubernetes RBAC e Azure AD para proteger o servidor API e fornecer o menor número de permissões necessárias a um conjunto de recursos, como um único espaço de nome. Diferentes utilizadores ou grupos em Azure AD podem ser concedidos diferentes funções RBAC. Estas permissões granulares permitem-lhe restringir o acesso ao servidor API e fornecer um claro rasto de auditoria das ações realizadas.

A melhor prática recomendada é usar grupos para fornecer acesso a ficheiros e pastas versus identidades individuais, usar a adesão do *grupo* Azure AD para vincular os utilizadores a funções RBAC em vez de *utilizadores*individuais. À medida que a adesão de um utilizador muda, as suas permissões de acesso no cluster AKS mudariam em conformidade. Se ligar o utilizador diretamente a uma função, a sua função de trabalho pode mudar. Os membros do grupo Azure AD atualizariam, mas as permissões no cluster AKS não refletiriam isso. Neste cenário, o utilizador acaba por receber mais permissões do que um utilizador exige.

Para obter mais informações sobre a integração da AD Azure e o RBAC, consulte [as melhores práticas de autenticação e autorização no AKS.][aks-best-practices-identity]

## <a name="secure-container-access-to-resources"></a>Acesso seguro de contentores aos recursos

**Orientação das melhores práticas** - Limitar o acesso às ações que os contentores podem realizar. Forneça o menor número de permissões e evite o uso de raiz/escalada privilegiada.

Da mesma forma que deve conceder aos utilizadores ou grupos o menor número de privilégios necessários, os contentores devem também limitar-se apenas às ações e processos de que necessitam. Para minimizar o risco de ataque, não configure aplicações e recipientes que requerem privilégios escalados ou acesso à raiz. Por exemplo, `allowPrivilegeEscalation: false` colocado no manifesto da cápsula. Estes contextos de segurança do pod são *incorporados* em Kubernetes e permitem definir permissões adicionais, como o utilizador ou grupo para executar como, ou quais as capacidades do Linux para expor. Para obter mais práticas, consulte [O acesso seguro aos recursos.][pod-security-contexts]

Para um maior controlo granular das ações dos contentores, também pode utilizar funcionalidades de segurança linux incorporadas, tais como *AppArmor* e *seccomp*. Estas características são definidas ao nível do nó, e depois implementadas através de um manifesto de pod. As funcionalidades de segurança linux incorporadas só estão disponíveis em nódos e casulos Linux.

> [!NOTE]
> Os ambientes kubernetes, em AKS ou em qualquer outro lugar, não são completamente seguros para o uso hostil de multi-inquilinos. Funcionalidades de segurança adicionais como *AppArmor,* *seccomp,* *Pod Security Policies*ou controlos de acesso baseados em funções mais finos (RBAC) para nós dificultam as explorações. No entanto, para uma verdadeira segurança quando se executam cargas de trabalho hostis de multi-inquilinos, um hipervisor é o único nível de segurança em que se deve confiar. O domínio de segurança de Kubernetes torna-se todo o cluster, não um nó individual. Para este tipo de cargas de trabalho hostis multi-inquilinos, você deve usar clusters fisicamente isolados.

### <a name="app-armor"></a>Armadura de aplicações

Para limitar as ações que os contentores podem executar, pode utilizar o módulo de segurança do kernel [AppArmor][k8s-apparmor] Linux. O AppArmor está disponível como parte do nó AKS os subjacente, e está ativado por padrão. Cria perfis AppArmor que restringem ações como leitura, escrita ou execução ou funções do sistema, tais como a montagem de sistemas de ficheiros. Os perfis padrão da AppArmor restringem o acesso a vários `/proc` e `/sys` locais, e fornecem um meio para isolar logicamente os contentores do nó subjacente. A AppArmor trabalha para qualquer aplicação que funcione no Linux, e não apenas nas cápsulas Kubernetes.

![Perfis AppArmor em uso num cluster AKS para limitar ações de contentores](media/operator-best-practices-container-security/apparmor.png)

Para ver o AppArmor em ação, o exemplo seguinte cria um perfil que impede a escrita em ficheiros. [SSH][aks-ssh] a um nó AKS, em seguida, criar um ficheiro chamado *deny-write.profile* e colar o seguinte conteúdo:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

Os perfis da AppArmor `apparmor_parser` são adicionados usando o comando. Adicione o perfil ao AppArmor e especifique o nome do perfil criado na etapa anterior:

```console
sudo apparmor_parser deny-write.profile
```

Não há saída devolvida se o perfil for corretamente analisado e aplicado à AppArmor. Voltou ao comando.

A partir da sua máquina local, crie agora um manifesto de pod chamado *aks-apparmor.yaml* e colá-lo o seguinte conteúdo. Este manifesto define uma anotação para `container.apparmor.security.beta.kubernetes` adicionar referências ao perfil de *deny-write* criado nos passos anteriores:

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

Com a cápsula implantada, use o comando [executivo kubectl][kubectl-exec] para escrever para um ficheiro. O comando não pode ser executado, como mostra a seguinte saída exemplo:

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

Para obter mais informações sobre o AppArmor, consulte [os perfis da AppArmor em Kubernetes][k8s-apparmor].

### <a name="secure-computing"></a>Computação segura

Enquanto a AppArmor trabalha para qualquer aplicação Linux, a [seccomp *(sec*ure *up*uting)][seccomp] trabalha a nível do processo. Seccomp é também um módulo de segurança linux kernel, e é apoiado de forma nativa pelo tempo de funcionação do Docker usado pelos nós AKS. Com seccomp, as chamadas de processo que os contentores podem executar são limitadas. Cria filtros que definem quais as ações que permitem ou negam e, em seguida, utiliza anotações dentro de um manifesto YAML para se associar ao filtro de seccomp. Isto alinha-se com as melhores práticas de apenas conceder ao recipiente as permissões mínimas necessárias para serem executadas, e não mais.

Para ver seccomp em ação, crie um filtro que impeça a alteração das permissões num ficheiro. [SSH][aks-ssh] para um nó AKS, em seguida, criar um filtro de seccomp chamado */var/lib/kubelet/seccomp/prevent-chmod* e colar o seguinte conteúdo:

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

A partir da sua máquina local, crie agora um manifesto de pod chamado *aks-seccomp.yaml* e colá o seguinte conteúdo. Este manifesto define uma anotação e `seccomp.security.alpha.kubernetes.io` refere o filtro *prevent-chmod* criado na etapa anterior:

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

Veja o estado das cápsulas utilizando o comando [kubectl get pods.][kubectl-get] A cápsula relata um erro. O `chmod` comando é impedido de ser dirigido pelo filtro seccomp, como mostra a seguinte saída de exemplo:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Para obter mais informações sobre os filtros disponíveis, consulte os perfis de [segurança da Seccomp para o Docker][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Atualizar regularmente para a versão mais recente da Kubernetes

**Orientação de boas práticas** - Para se manter atual em novas funcionalidades e correções de bugs, atualize regularmente para a versão Kubernetes no seu cluster AKS.

A Kubernetes lança novas funcionalidades a um ritmo mais rápido do que as plataformas de infraestruturas mais tradicionais. As atualizações da Kubernetes incluem novas funcionalidades e correções de bugs ou segurança. As novas funcionalidades normalmente movem-se através de um *estatuto alfa* e *depois beta* antes de se tornarem *estáveis* e estão geralmente disponíveis e recomendadas para uso da produção. Este ciclo de libertação deve permitir-lhe atualizar kubernetes sem encontrar regularmente alterações de rutura ou ajustar as suas implementações e modelos.

A AKS suporta quatro versões menores de Kubernetes. Isto significa que quando uma nova versão de patch menor é introduzida, a versão menor mais antiga e os lançamentos de patch suportados são retirados. Pequenas atualizações a Kubernetes acontecem periodtimelmente. Certifique-se de que tem um processo de governação para verificar e atualizar conforme necessário para não perder o apoio. Para mais informações, consulte as [versões Apoiadas kubernetes AKS][aks-supported-versions]

Para verificar as versões disponíveis para o seu cluster, utilize o comando de [atualizações az aks,][az-aks-get-upgrades] como mostra o seguinte exemplo:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Em seguida, pode atualizar o seu cluster AKS utilizando o comando de [upgrade az aks.][az-aks-upgrade] O processo de atualização isola e drena com segurança um nó de cada vez, programa cápsulas em nós restantes, e depois implanta um novo nó executando as versões mais recentes de OS e Kubernetes.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
```

Para obter mais informações sobre upgrades em AKS, consulte [versões De Kubernetes suportadas em AKS][aks-supported-versions] e [atualize um cluster AKS][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Processe atualizações de nó linux e reboots usando kured

**Orientação de boas práticas** - AKS descarrega e instala automaticamente correções de segurança em cada nó linux, mas não reinicia automaticamente se necessário. Utilize `kured` para vigiar as reinicializações pendentes, em seguida, penije com segurança e drene o nó para permitir que o nó reinicie, aplique as atualizações e seja o mais seguro possível no que diz respeito ao SISTEMA. Para os nós do Windows Server, execute regularmente uma operação de atualização AKS para isolar e drenar com segurança as cápsulas e implementar nós atualizados.

Todas as noites, os nós linux no AKS recebem patches de segurança disponíveis através do seu canal de atualização de distro. Este comportamento é configurado automaticamente à medida que os nós são implantados num cluster AKS. Para minimizar a perturbação e o impacto potencial nas cargas de trabalho de funcionamento, os nós não são automaticamente reiniciados se uma correção de segurança ou atualização do núcleo o exigir.

O projeto de kured de código aberto [(KUbernetes REboot Daemon)][kured] da Weaveworks observa para reiniciar o nó pendente. Quando um nó Linux aplica atualizações que requerem um reboot, o nó é isolado e drenado com segurança para mover e agendar as cápsulas em outros nós do cluster. Uma vez reiniciado o nó, é adicionado de volta ao cluster e Kubernetes retoma a programação de cápsulas nele. Para minimizar a perturbação, apenas um nó de `kured`cada vez é permitido ser reiniciado por .

![O processo de reinicialização do nó AKS usando kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Se quiser um controlo mais fino dos `kured` cereais quando ocorrerem reboots, pode integrar-se com a Prometheus para evitar reinicializações se houver outros eventos de manutenção ou problemas de cluster em curso. Esta integração minimiza complicações adicionais reiniciando nós enquanto está a resolver ativamente outros problemas.

Para obter mais informações sobre como lidar com reboots de nó, consulte Aplicar atualizações de [segurança e kernel em nós em AKS][aks-kured].

## <a name="next-steps"></a>Passos seguintes

Este artigo focou-se em como proteger o seu cluster AKS. Para implementar algumas destas áreas, consulte os seguintes artigos:

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
