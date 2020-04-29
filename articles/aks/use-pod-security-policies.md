---
title: Utilize políticas de segurança de pods no Serviço Azure Kubernetes (AKS)
description: Saiba como controlar as admissões de podutilizando podSecurityPolicy no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 04/08/2020
ms.openlocfilehash: 9e3a17e4775150247ef7924dffec68cc86a0bcac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80998364"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Pré-visualização - Proteja o seu cluster utilizando políticas de segurança do pod no Serviço Azure Kubernetes (AKS)

Para melhorar a segurança do seu cluster AKS, pode limitar o que as cápsulas podem ser programadas. Pods que solicitam recursos que não permite não podem funcionar no cluster AKS. Define este acesso usando políticas de segurança do pod. Este artigo mostra-lhe como usar políticas de segurança de pods para limitar a implantação de cápsulas em AKS.

> [!IMPORTANT]
> As funcionalidades de pré-visualização AKS são opt-in self-service. As pré-visualizações são fornecidas "as-is" e "conforme disponível" e estão excluídas dos acordos de nível de serviço e da garantia limitada. As pré-visualizações aks são parcialmente cobertas pelo apoio ao cliente na melhor base de esforço. Como tal, estas características não se destinam à utilização da produção. Para mais informações, consulte os seguintes artigos de apoio:
>
> * [Políticas de apoio aks][aks-support-policies]
> * [FaQ de suporte azure][aks-faq]

## <a name="before-you-begin"></a>Antes de começar

Este artigo assume que você tem um aglomerado AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Precisa da versão Azure CLI 2.0.61 ou posteriormente instalada e configurada. Corra `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Instale a extensão CLI de pré-visualização de aks

Para utilizar as políticas de segurança do pod, necessita da versão de extensão CLI de *pré-visualização* de aks 0.4.1 ou superior. Instale a extensão Azure CLI de *pré-visualização de aks* utilizando o comando de adição de [extensão az][az-extension-add] e, em seguida, verifique se há atualizações disponíveis utilizando o comando de atualização de [extensão az:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Registe o fornecedor de funcionalidades de política de segurança do pod

Para criar ou atualizar um cluster AKS para utilizar políticas de segurança do pod, primeiro ative uma bandeira de funcionalidade na sua subscrição. Para registar a bandeira de recurso *PodSecurityPolicyPreview,* utilize o comando de registo de [funcionalidades az,][az-feature-register] como mostra o seguinte exemplo:

> [!CAUTION]
> Ao registar uma funcionalidade numa subscrição, não pode atualmente desregistar essa funcionalidade. Depois de ativar algumas funcionalidades de pré-visualização, podem ser utilizadas predefinições para todos os clusters AKS e depois criados na subscrição. Não ative funcionalidades de pré-visualização nas subscrições de produção. Utilize uma subscrição separada para testar funcionalidades de pré-visualização e recolher feedback.

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Leva alguns minutos para o estado mostrar *registrado.* Pode verificar o estado de registo utilizando o comando da [lista de características az:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualização do registo do fornecedor de recursos *Microsoft.ContainerService* utilizando o comando de registo do [fornecedor az:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Visão geral das políticas de segurança do pod

Num cluster kubernetes, um controlador de admissão é usado para intercetar pedidos para o servidor API quando um recurso deve ser criado. O controlador de admissão pode então *validar* o pedido de recursos contra um conjunto de regras, ou *mutar* o recurso para alterar os parâmetros de implantação.

*PodSecurityPolicy* é um controlador de admissão que valida uma especificação de pod satisfaz os seus requisitos definidos. Estes requisitos podem limitar a utilização de contentores privilegiados, o acesso a determinados tipos de armazenamento, ou o utilizador ou grupo que o contentor pode executar como. Quando se tenta implementar um recurso em que as especificações do casulo não satisfaçam os requisitos descritos na política de segurança do pod, o pedido é negado. Esta capacidade de controlar o que as cápsulas podem ser programadas no cluster AKS evita algumas possíveis vulnerabilidades de segurança ou escaladas de privilégios.

Quando ativa a política de segurança do pod num cluster AKS, são aplicadas algumas políticas predefinidas. Estas políticas padrão fornecem uma experiência fora da caixa para definir quais as cápsulas que podem ser programadas. No entanto, os utilizadores de clusters podem ter problemas na implementação de cápsulas até definir em suas próprias políticas. A abordagem recomendada é:

* Criar um cluster do AKS (Create an AKS cluster)
* Defina as suas próprias políticas de segurança de pod
* Ativar a funcionalidade de política de segurança da cápsula

Para mostrar como as políticas padrão limitam as implementações de pods, neste artigo primeiro ativamos a funcionalidade de políticas de segurança do pod e, em seguida, criamos uma política personalizada.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Ativar a política de segurança do pod num cluster AKS

Pode ativar ou desativar a política de segurança do pod utilizando o comando de [atualização az aks.][az-aks-update] O exemplo seguinte permite a política de segurança do pod no nome de cluster *myAKSCluster* no grupo de recursos chamado *myResourceGroup*.

> [!NOTE]
> Para uso no mundo real, não ative a política de segurança do pod até definir as suas próprias políticas personalizadas. Neste artigo, permite a política de segurança do pod como o primeiro passo para ver como as políticas padrão limitam as implementações de pods.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Políticas de AKS padrão

Ao ativar a política de segurança do pod, aks cria uma política padrão chamada *privilegiada*. Não edite ou remova a política de incumprimento. Em vez disso, crie as suas próprias políticas que definam as definições que pretende controlar. Vamos primeiro ver qual é a forma como impactam as implementações de cápsulas.

Para ver as políticas disponíveis, utilize o [kubectl obter][kubectl-get] o comando da PSP, como mostra o seguinte exemplo

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

A política *privilegiada* de segurança do casulo é aplicada a qualquer utilizador autenticado no cluster AKS. Esta atribuição é controlada por ClusterRoles e ClusterRoleBindings. Utilize o [kubectl obter][kubectl-get] comando de rolebindings e procure o *padrão:privilegiado:* encadernação no espaço de nome do *sistema kube:*

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

Como mostra a seguinte saída condensada, o ClusterRole *restrito* da PSP é atribuído a qualquer *sistema:utilizadores autenticados.* Esta capacidade proporciona um nível básico de restrições sem que as suas próprias políticas sejam definidas.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:masters
```

É importante entender como estas políticas padrão interagem com os pedidos dos utilizadores para agendar cápsulas antes de começar a criar as suas próprias políticas de segurança. Nas próximas secções, vamos agendar algumas cápsulas para ver estas políticas padrão em ação.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Criar um utilizador de teste num cluster AKS

Por padrão, quando se utiliza o comando [az aks get-credentials,][az-aks-get-credentials] as credenciais *de administração* para o cluster AKS são adicionadas ao seu `kubectl` config. O utilizador administrativo ignora a aplicação das políticas de segurança do pod. Se utilizar a integração do Diretório Ativo Azure para os seus clusters AKS, poderá iniciar sessão com as credenciais de um utilizador não administrador para ver a aplicação das políticas em ação. Neste artigo, vamos criar uma conta de utilizador de teste no cluster AKS que pode usar.

Crie um espaço de nome de amostra chamado *PSP-aks* para testar recursos usando o [kubectl criar][kubectl-create] comando espaço de nome. Em seguida, crie uma conta de serviço chamada *nonadmin-user* usando o comando de conta de [serviço de criação kubectl:][kubectl-create]

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Em seguida, crie um RoleBinding para que o utilizador da *nonadmin* realize ações básicas no espaço de nome utilizando o [comando de criação][kubectl-create] de papel:

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Criar comandos de pseudónimos para administração e utilizador não-administrador

Para realçar a diferença entre o `kubectl` utilizador administrativo regular ao utilizar e o utilizador não administrador criado nos passos anteriores, crie dois pseudónimos de linha de comando:

* O pseudónimo **kubectl-admin** é para o utilizador administrativo regular, e é mirado para o espaço de nome da *PSP-aks.*
* O pseudónimo **kubectl-nonadminuser destina-se** ao utilizador de *nonadmin* criado no passo anterior, e é orientado para o espaço de nome saque da *PSP-AKS.*

Crie estes dois pseudónimos como mostrado nos seguintes comandos:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Teste a criação de uma cápsula privilegiada

Vamos primeiro testar o que acontece quando se marca `privileged: true`uma cápsula com o contexto de segurança de . Este contexto de segurança aumenta os privilégios da cápsula. Na secção anterior que mostrou as políticas de segurança padrão do casulo AKS, a política *restrita* deve negar este pedido.

Crie um `nginx-privileged.yaml` ficheiro nomeado e cola o seguinte manifesto YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx:1.14.2
      securityContext:
        privileged: true
```

Crie a cápsula utilizando o [kubectl aplique][kubectl-apply] o comando e especifique o nome do seu manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

A cápsula não é programada, como mostra a seguinte saída exemplo:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

A cápsula não chega à fase de agendamento, por isso não há recursos para apagar antes de seguir em frente.

## <a name="test-creation-of-an-unprivileged-pod"></a>Criação de teste de uma cápsula desfavorecida

No exemplo anterior, a especificação do pod solicitou uma escalada privilegiada. Este pedido é negado pela política de segurança *restrita* do pod, pelo que a cápsula não está programada. Vamos tentar agora executar a mesma cápsula NGINX sem o pedido de escalada de privilégios.

Crie um `nginx-unprivileged.yaml` ficheiro nomeado e cola o seguinte manifesto YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
```

Crie a cápsula utilizando o [kubectl aplique][kubectl-apply] o comando e especifique o nome do seu manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

A cápsula não é programada, como mostra a seguinte saída exemplo:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

A cápsula não chega à fase de agendamento, por isso não há recursos para apagar antes de seguir em frente.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Testar a criação de um casulo com um contexto específico do utilizador

No exemplo anterior, a imagem do recipiente tentou automaticamente utilizar a raiz para ligar o NGINX à porta 80. Este pedido foi negado pela política de segurança *restrita* do pod, pelo que a cápsula não começou. Vamos tentar agora executar o mesmo casulo NGINX `runAsUser: 2000`com um contexto específico de utilizador, como .

Crie um `nginx-unprivileged-nonroot.yaml` ficheiro nomeado e cola o seguinte manifesto YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
      securityContext:
        runAsUser: 2000
```

Crie a cápsula utilizando o [kubectl aplique][kubectl-apply] o comando e especifique o nome do seu manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

A cápsula não é programada, como mostra a seguinte saída exemplo:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

A cápsula não chega à fase de agendamento, por isso não há recursos para apagar antes de seguir em frente.

## <a name="create-a-custom-pod-security-policy"></a>Criar uma política de segurança de pod personalizado

Agora que viu o comportamento das políticas de segurança padrão do pod, vamos providenciar uma maneira para o utilizador de *nonadmin* agendar com sucesso pods.

Vamos criar uma política para rejeitar pods que pedem acesso privilegiado. Outras opções, tais como *runAsUser* ou *volumes permitidos,* não são explicitamente restritas. Este tipo de política nega um pedido de acesso privilegiado, mas de outra forma permite que o cluster execute as cápsulas solicitadas.

Crie um `psp-deny-privileged.yaml` ficheiro nomeado e cola o seguinte manifesto YAML:

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

Crie a política utilizando o [kubectl aplique][kubectl-apply] o comando e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Para ver as políticas disponíveis, utilize o [kubectl obter][kubectl-get] o comando da PSP, como mostra o seguinte exemplo. Compare a política *privilegiada da PSP* com a política *restrita* de incumprimento que foi aplicada nos exemplos anteriores para criar uma cápsula. Apenas o uso da escalada *PRIV* é negado pela sua política. Não existem restrições ao utilizador ou grupo para a política privilegiada da *PSP.*

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Permitir que a conta de utilizador utilize a política de segurança do casulo personalizado

No passo anterior, criou uma política de segurança para rejeitar cápsulas que solicitam acesso privilegiado. Para permitir a sua adoção da política, cria-se um *Papel* ou um *ClusterRole*. Em seguida, associa uma destas funções usando uma *RoleBinding* ou *ClusterRoleBinding*.

Para este exemplo, crie um ClusterRole que lhe permita *utilizar* a política privilegiada da *PSP* criada no passo anterior. Crie um `psp-deny-privileged-clusterrole.yaml` ficheiro nomeado e cola o seguinte manifesto YAML:

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

Crie o ClusterRole utilizando o comando de [aplicação kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Agora crie um ClusterRoleBinding para utilizar o ClusterRole criado no passo anterior. Crie um `psp-deny-privileged-clusterrolebinding.yaml` ficheiro nomeado e cola o seguinte manifesto YAML:

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

Crie um ClusterRoleBinding utilizando o comando de aplicação do [kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> No primeiro passo deste artigo, a funcionalidade de política de segurança da cápsula foi ativada no cluster AKS. A prática recomendada era apenas ativar a funcionalidade de política de segurança do pod depois de definir as suas próprias políticas. Esta é a fase em que você ativa a funcionalidade de política de segurança do pod. Uma ou mais políticas personalizadas foram definidas e as contas dos utilizadores têm sido associadas a essas políticas. Agora pode ativar com segurança a funcionalidade de política de segurança do pod e minimizar os problemas causados pelas políticas de predefinição.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Teste a criação de uma cápsula desfavorecida novamente

Com a sua política de segurança de pod personalizada aplicada e uma ligação para a conta de utilizador usar a apólice, vamos tentar criar uma cápsula desprivilegiada novamente. Utilize o `nginx-privileged.yaml` mesmo manifesto para criar a cápsula utilizando o [comando de aplicação kubectl:][kubectl-apply]

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

A cápsula está programada com sucesso. Quando verificar o estado da cápsula utilizando o comando do [kubectl get pods,][kubectl-get] a cápsula está *em execução:*

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Este exemplo mostra como pode criar políticas personalizadas de segurança de casulos para definir o acesso ao cluster AKS para diferentes utilizadores ou grupos. As políticas padrão do AKS fornecem controlos apertados sobre o que as cápsulas podem executar, por isso crie as suas próprias políticas personalizadas para, em seguida, definir corretamente as restrições de que necessita.

Elimine a cápsula não privilegiada NGINX utilizando o [kubectl eliminar][kubectl-delete] o comando e especificar o nome do seu manifesto YAML:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Limpar recursos

Para desativar a política de segurança do pod, use novamente o comando de [atualização az aks.][az-aks-update] O exemplo seguinte desativa a política de segurança do pod no nome de cluster *myAKSCluster* no grupo de recursos chamado *myResourceGroup:*

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Em seguida, elimine o ClusterRole e clusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Elimine a política de segurança utilizando [kubectl eliminar][kubectl-delete] comando e especificar o nome do seu manifesto YAML:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Por fim, apague o espaço de nome *saque da PSP:*

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Passos seguintes

Este artigo mostrou-lhe como criar uma política de segurança para impedir o uso de acesso privilegiado. Existem muitas funcionalidades que uma política pode impor, como o tipo de volume ou o utilizador RunAs. Para obter mais informações sobre as opções disponíveis, consulte os documentos de referência da política de segurança do [casulo Kubernetes][kubernetes-policy-reference].

Para obter mais informações sobre a limitação do tráfego da rede de [cápsulas,][network-policies]consulte o tráfego seguro entre as cápsulas utilizando políticas de rede no AKS .

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
