---
title: Melhores práticas para a segurança do cluster
titleSuffix: Azure Kubernetes Service
description: Aprenda as melhores práticas do operador do cluster para gerir a segurança do cluster e atualizações no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: ea63db2d8868a1333ae264c9cfdf31d0b7397a83
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105158"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Melhores práticas para segurança de clusters e upgrades no Serviço Azure Kubernetes (AKS)

À medida que gere clusters no Serviço Azure Kubernetes (AKS), a carga de trabalho e a segurança dos dados são uma consideração fundamental. Quando você gere clusters multi-inquilinos usando isolamento lógico, você precisa especialmente para garantir o acesso de recursos e carga de trabalho. Minimizar o risco de ataque aplicando as mais recentes atualizações de segurança kubernetes e nós.

Este artigo centra-se em como proteger o seu cluster AKS. Saiba como:

> [!div class="checklist"]
> * Utilize o Azure Ative Directory e o controlo de acesso baseado em funções (Kubernetes RBAC) para garantir o acesso ao servidor API.
> * Acesso seguro ao contentor aos recursos do nó.
> * Atualize um cluster AKS para a versão mais recente de Kubernetes.
> * Mantenha os nós atualizados e aplique automaticamente patches de segurança.

Também pode ler as melhores práticas para [a gestão da imagem do contentor][best-practices-container-image-management] e para a segurança do [casulo.][best-practices-pod-security]

Também pode utilizar [a integração dos Serviços Azure Kubernetes com][security-center-aks] o Security Center para ajudar a detetar ameaças e ver recomendações para garantir os seus clusters AKS.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Acesso seguro ao servidor API e nosmes de cluster

> **Orientação de melhor prática** 
>
> Uma das formas mais importantes de proteger o seu cluster é garantir o acesso ao servidor API da Kubernetes. Para controlar o acesso ao servidor API, integre o RBAC de Kubernetes com o Azure Ative Directory (Azure AD). Com estes controlos, assegura a AKS da mesma forma que assegura o acesso às suas subscrições Azure.

O servidor API de Kubernetes fornece um único ponto de ligação para pedidos para executar ações dentro de um cluster. Para garantir e auditar o acesso ao servidor API, limite o acesso e forneça os níveis de permissão mais baixos possíveis. embora esta abordagem não seja exclusiva de Kubernetes, é especialmente importante quando você logicamente isolou o seu cluster AKS para uso multi-inquilino.

A Azure AD fornece uma solução de gestão de identidade pronta para a empresa que se integra com clusters AKS. Uma vez que a Kubernetes não fornece uma solução de gestão de identidade, pode ser pressionado a restringir granularmente o acesso ao servidor API. Com clusters integrados em AZure em AKS, utiliza as contas de utilizador e grupo existentes para autenticar os utilizadores no servidor API.

![Integração do Diretório Ativo Azure para clusters AKS](media/operator-best-practices-cluster-security/aad-integration.png)

Utilizando a integração de Kubernetes RBAC e Azure AD, pode proteger o servidor API e fornecer as permissões mínimas necessárias para um conjunto de recursos de âmbito, como um único espaço de nome. Você pode conceder diferentes utilizadores AD Azure ou grupos diferentes funções Kubernetes. Com permissões granulares, pode restringir o acesso ao servidor API e fornecer um claro rasto de auditoria das ações realizadas.

A melhor prática recomendada é usar *grupos* para fornecer acesso a ficheiros e pastas em vez de identidades individuais. Por exemplo, utilize uma associação *do grupo* AZURE AD para ligar os utilizadores aos papéis de Kubernetes em vez de *utilizadores* individuais . À medida que a adesão de um utilizador muda, as suas permissões de acesso no cluster AKS mudam em conformidade. 

Entretanto, digamos que ligas o utilizador individualmente a um papel e a sua função de trabalho muda. Embora os membros do grupo AZure AD atualizam, as suas permissões no cluster AKS não o fariam. Neste cenário, o utilizador acaba por ter mais permissões do que necessita.

Para obter mais informações sobre a integração da AD Azure, Kubernetes RBAC e Azure RBAC, consulte [as melhores práticas para autenticação e autorização em AKS][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Acesso seguro aos recursos dos contentores

> **Orientação de melhor prática** 
> 
> Limitar o acesso a ações que os contentores podem realizar. Forneça o menor número de permissões e evite o uso de acesso à raiz ou escalada privilegiada.

Da mesma forma que deve conceder aos utilizadores ou grupos os privilégios mínimos necessários, deve também limitar os contentores a apenas ações e processos necessários. Para minimizar o risco de ataque, evite configurar aplicações e recipientes que exijam privilégios e acessos de raiz. 

Por exemplo, coloque `allowPrivilegeEscalation: false` no manifesto da cápsula. Estes contextos de segurança do *casulo* de Kubernetes incorporados permitem definir permissões adicionais, como o utilizador ou grupo para executar como, ou as capacidades do Linux para expor. Para obter mais boas práticas, consulte [o acesso seguro dos recursos ao pod.][pod-security-contexts]

Para um controlo ainda mais granular das ações de contentores, também pode utilizar funcionalidades de segurança lídulas incorporadas, como *AppArmor* e *seccomp.* 
1. Defina as funcionalidades de segurança Linux ao nível do nó.
1. Implementar funcionalidades através de um manifesto de vagem. 

As funcionalidades de segurança lux incorporadas só estão disponíveis em nós e cápsulas Linux.

> [!NOTE]
> Atualmente, os ambientes de Kubernetes não são completamente seguros para o uso hostil de vários inquilinos. Funcionalidades de segurança adicionais, como *AppArmor*, *seccomp,**Pod Security Policies* ou Kubernetes RBAC para nós, bloqueiam eficientemente as explorações. 
>
>Para uma verdadeira segurança quando executar cargas de trabalho hostis de vários inquilinos, só confie num hipervisor. O domínio de segurança de Kubernetes torna-se todo o cluster, não um nó individual. 
>
> Para este tipo de cargas de trabalho hostis multi-inquilinos, você deve usar aglomerados fisicamente isolados.

### <a name="app-armor"></a>Armadura de aplicativo

Para limitar as ações do contentor, pode utilizar o módulo de segurança do kernel [AppArmor][k8s-apparmor] Linux. O AppArmor está disponível como parte do nó AKS subjacente e está ativado por padrão. Cria perfis AppArmor que restringem a leitura, escrita ou execução de ações, ou funções do sistema como a montagem de sistemas de ficheiros. Os perfis padrão do AppArmor restringem o acesso a `/proc` `/sys` vários locais e locais e fornecem um meio para isolar logicamente os contentores do nó subjacente. O AppArmor trabalha para qualquer aplicação que funcione no Linux, e não apenas para as cápsulas Kubernetes.

![Perfis AppArmor em uso num cluster AKS para limitar as ações de contentores](media/operator-best-practices-container-security/apparmor.png)

Para ver o AppArmor em ação, o exemplo a seguir cria um perfil que impede a escrita para ficheiros. 
1. [SSH][aks-ssh] para um nó AKS.
1. Crie um ficheiro chamado *deny-write.profile*.
1. Cole o seguinte conteúdo:

    ```
    #include <tunables/global>
    profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
      #include <abstractions/base>
  
      file,
      # Deny all file writes.
      deny /** w,
    }
    ```

Os perfis appArmor são adicionados usando o `apparmor_parser` comando. 
1. Adicione o perfil ao AppArmor.
1. Especificar o nome do perfil criado no passo anterior:

    ```console
    sudo apparmor_parser deny-write.profile
    ```

    Se o perfil estiver corretamente analisado e aplicado ao AppArmor, não verá nenhuma saída e será devolvido à solicitação de comando.

1. A partir da sua máquina local, crie um manifesto de pod chamado *aks-apparmor.yaml*. Este manifesto:
    * Define uma anotação para `container.apparmor.security.beta.kubernetes` .
    * Refere o perfil *de negação criado* nos passos anteriores.

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
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
    ```

1. Com a cápsula implantada, utilize verifique se o pod da *cápsula hello-apparmor* mostra como *bloqueado:*

    ```
    $ kubectl get pods

    NAME             READY   STATUS    RESTARTS   AGE
    aks-ssh          1/1     Running   0          4m2s
    hello-apparmor   0/1     Blocked   0          50s
    ```

Para mais informações sobre o AppArmor, consulte [os perfis appArmor em Kubernetes.][k8s-apparmor]

### <a name="secure-computing"></a>Computação segura

Enquanto o AppArmor trabalha para qualquer aplicação Linux, [o seccomp *(sec* ure *comp* uting)][seccomp] funciona ao nível do processo. O Seccomp também é um módulo de segurança do kernel Linux, e é suportado de forma nativa pelo tempo de funcionação do Docker usado pelos nós AKS. Com o seccomp, pode limitar as chamadas de processo de contentores. Alinhe-se com as melhores práticas de conceder ao contentor a mínima permissão apenas para ser executado:
* Definir com filtros que ações permitir ou negar.
* Anotando dentro de um manifesto YAML de vagem para associar-se ao filtro seccomp. 

Para ver o seccomp em ação, crie um filtro que impeça a alteração de permissões num ficheiro. 
1. [SSH][aks-ssh] para um nó AKS.
1. Criar um filtro seccomp chamado */var/lib/kubelet/seccomp/prevent-chmod*.
1. Cole o seguinte conteúdo:

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "name": "chmod",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "fchmodat",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "chmodat",
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

    Na versão 1.19 e posterior, é necessário configurar o seguinte:

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "names": ["chmod","fchmodat","chmodat"],
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

1. A partir da sua máquina local, crie um manifesto de pod chamado *aks-seccomp.yaml* e cole o seguinte conteúdo. Este manifesto:
    * Define uma anotação para `seccomp.security.alpha.kubernetes.io` .
    * Refere o filtro *prevent-chmod* criado no passo anterior.

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
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

    Na versão 1.19 e posterior, é necessário configurar o seguinte:

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: chmod-prevented
    spec:
      securityContext:
        seccompProfile:
          type: Localhost
          localhostProfile: prevent-chmod
      containers:
      - name: chmod
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

1. Implementar a cápsula de amostra utilizando o [comando de aplicação de kubectl:][kubectl-apply]

    ```console
    kubectl apply -f ./aks-seccomp.yaml
    ```

1. Ver o estado do pod utilizando o comando [kubectl get pods.][kubectl-get] 
    * A cápsula relata um erro. 
    * O `chmod` comando é impedido de funcionar pelo filtro de seccomp, como mostra a saída do exemplo seguinte:    

    ```
    $ kubectl get pods

    NAME                      READY     STATUS    RESTARTS   AGE
    chmod-prevented           0/1       Error     0          7s
    ```

Para obter mais informações sobre filtros disponíveis, consulte [os perfis de segurança da Seccomp para o Docker.][seccomp]

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Atualizar regularmente para a versão mais recente de Kubernetes

> **Orientação de melhor prática** 
> 
> Para se manter atualizado sobre novas funcionalidades e correções de bugs, atualize regularmente a versão Kubernetes no seu cluster AKS.

A Kubernetes lança novas funcionalidades a um ritmo mais rápido do que as plataformas de infraestruturas mais tradicionais. As atualizações de Kubernetes incluem:
* Novas funcionalidades
* Correções de bugs ou segurança 

As novas funcionalidades normalmente movem-se através do estatuto *alfa* e *beta* antes de se tornarem *estáveis.* Uma vez estável, estão geralmente disponíveis e recomendados para uso de produção. O novo ciclo de lançamento de funcionalidades da Kubernetes permite-lhe atualizar kubernetes sem encontrar regularmente alterações de rutura ou ajustar as suas implementações e modelos.

AKS suporta três versões menores de Kubernetes. Uma vez introduzida uma nova versão de patch menor, a versão menor mais antiga e os lançamentos de patch suportados são retirados. As atualizações de Kubernetes menores acontecem periodicamente. Para se manter dentro do apoio, certifique-se de que tem um processo de governação para verificar as atualizações necessárias. Para obter mais informações, consulte [as versões AKS suportadas de Kubernetes.][aks-supported-versions]

Para verificar as versões disponíveis para o seu cluster, utilize o comando [az aks get-upgrades][az-aks-get-upgrades] como mostrado no seguinte exemplo:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Em seguida, pode atualizar o seu cluster AKS utilizando o comando [de atualização az aks.][az-aks-upgrade] O processo de atualização com segurança:
* Cordons e drena um nó de cada vez.
* Agenda cápsulas em nóns restantes.
* Implementa um novo nó que executa as versões mais recentes de OS e Kubernetes.

>[!IMPORTANT]
> Teste novas versões menores num ambiente de teste dev e valide que a sua carga de trabalho permanece saudável com a nova versão Kubernetes. 
>
> Kubernetes pode deprecar APIs (como na versão 1.16) em que as suas cargas de trabalho dependem. Ao introduzir novas versões em produção, considere usar [várias piscinas de nós em versões separadas](use-multiple-node-pools.md) e atualizar piscinas individuais uma de cada vez para lançar progressivamente a atualização através de um cluster. Se executar vários clusters, atualize um cluster de cada vez para monitorizar progressivamente o impacto ou alterações.
>
>```azurecli-interactive
>az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
>```

Para obter mais informações sobre upgrades em AKS, consulte [as versões Kubernetes suportadas em AKS][aks-supported-versions] e [atualize um cluster AKS][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Processa atualizações e reboots de nó Linux

> **Orientação de melhor prática** 
> 
> Enquanto a AKS descarrega e instala automaticamente correções de segurança em cada nó Linux, não reinicia automaticamente. 
> 1. Use `kured` para observar as reinicializações pendentes.
> 1. Isolar com segurança e drenar o nó para permitir que o nó reinicie.
> 1. Aplique as atualizações.
> 1. Esteja o mais seguro possível em relação ao SISTEMA. 

Para os nós do Windows Server, efetue regularmente uma operação de atualização AKS para isolar e drenar com segurança as cápsulas e implementar nós atualizados.

Todas as noites, os nós Linux em AKS recebem patches de segurança através do seu canal de atualização de distro. Este comportamento é configurado automaticamente à medida que os nós são implantados num cluster AKS. Para minimizar a perturbação e o impacto potencial nas cargas de trabalho de funcionamento, os nós não são automaticamente reiniciados se um patch de segurança ou uma atualização de kernel o exigir.

O projeto [open-source kured (KUbernetes REboot Daemon)][kured] da Weaveworks observa para reboots pendentes de nó. Quando um nó Linux aplica atualizações que requerem um reboot, o nó é seguramente isolado e drenado para mover e agendar as cápsulas em outros nós no cluster. Uma vez reiniciado o nó, é adicionado de volta ao cluster e Kubernetes retoma o agendamento do pod. Para minimizar a perturbação, apenas um nó de cada vez é autorizado a ser reiniciado por `kured` .

![O processo de reinicialização do nó AKS usando kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Se quiser um controlo ainda mais próximo sobre as reinicializações, `kured` pode integrar-se com a Prometheus para evitar reinicializações se houver outros eventos de manutenção ou problemas de cluster em andamento. Esta integração reduz a complicação reiniciando os nós enquanto está a resolver ativamente outros problemas.

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
