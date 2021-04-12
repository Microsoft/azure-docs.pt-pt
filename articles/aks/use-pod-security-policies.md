---
title: Utilize políticas de segurança do pod no Serviço Azure Kubernetes (AKS)
description: Saiba como controlar as admissões de pod usando PodSecurityPolicy no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.openlocfilehash: 4e72bd28910f471656feb27d10c123930305494e
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031227"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Pré-visualização - Proteja o seu cluster utilizando as políticas de segurança do pod no Serviço Azure Kubernetes (AKS)

> [!WARNING]
> **A funcionalidade descrita neste documento, a política de segurança do pod (pré-visualização), começará [a depreciação](https://kubernetes.io/blog/2021/04/06/podsecuritypolicy-deprecation-past-present-and-future/) com a versão 1.21 de Kubernetes, com a sua remoção na versão 1.25.** À medida que Kubernetes Upstream se aproxima desse marco, a comunidade de Kubernetes trabalhará para documentar alternativas viáveis. O anúncio de depreciação anterior foi feito na altura, uma vez que não existia uma opção viável para os clientes. Agora que a comunidade de Kubernetes está a trabalhar numa alternativa, já não há uma necessidade premente de deprecação à frente de Kubernetes. 
>
> Após a deprecação da política de segurança do pod (pré-visualização), tem de desativar a funcionalidade em quaisquer clusters existentes utilizando a funcionalidade prevadida para realizar futuras atualizações de clusters e permanecer dentro do suporte do Azure.

Para melhorar a segurança do seu cluster AKS, pode limitar quais as cápsulas que podem ser programadas. Os pods que solicitam recursos que não permite não podem funcionar no cluster AKS. Você define este acesso usando políticas de segurança de pod. Este artigo mostra-lhe como usar as políticas de segurança do pod para limitar a implementação de cápsulas em AKS.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tem um cluster AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Precisa da versão 2.0.61 do Azure CLI ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Para utilizar as políticas de segurança do pod, precisa da versão de extensão CLI *de pré-visualização aks* 0.4.1 ou superior. Instale a extensão Azure CLI *de pré-visualização aks* utilizando o comando [de adicionar extensão az][az-extension-add] e, em seguida, verifique se há atualizações disponíveis utilizando o comando de atualização de [extensão az:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Registar fornecedor de recursos de política de segurança do pod

**Este documento e recurso estão definidos para depreciação no dia 15 de outubro de 2020.**

Para criar ou atualizar um cluster AKS para utilizar as políticas de segurança do pod, primeiro ative uma bandeira de funcionalidade na sua subscrição. Para registar a bandeira de funcionalidade *PodSecurityPolicyPreview,* utilize o comando [de registo de funcionalidades az,][az-feature-register] como mostra o seguinte exemplo:

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Demora alguns minutos para que o estado seja *apresentado.* Pode verificar o estado de registo utilizando o comando [da lista de recursos az:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, reaprovi o registo do fornecedor de recursos *Microsoft.ContainerService* utilizando o comando [de registo do fornecedor az:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Visão geral das políticas de segurança da pod

Num cluster Kubernetes, um controlador de admissão é usado para intercetar pedidos para o servidor API quando um recurso deve ser criado. O controlador de admissão pode então *validar* o pedido de recurso contra um conjunto de regras, ou *alterar* o recurso para alterar parâmetros de implantação.

*PodSecurityPolicy* é um controlador de admissão que valida uma especificação de pod satisfaz os requisitos definidos. Estes requisitos podem limitar a utilização de recipientes privilegiados, o acesso a certos tipos de armazenamento, ou o utilizador ou grupo que o contentor pode executar como. Quando se tenta implementar um recurso em que as especificações do pod não satisfaçam os requisitos descritos na política de segurança da cápsula, o pedido é negado. Esta capacidade de controlar quais as cápsulas que podem ser programadas no cluster AKS impede algumas possíveis vulnerabilidades de segurança ou escaladas de privilégio.

Quando ativa a política de segurança do pod num cluster AKS, algumas políticas predefinidos são aplicadas. Estas políticas predefinidas proporcionam uma experiência fora da caixa para definir quais as cápsulas que podem ser programadas. No entanto, os utilizadores de cluster podem ter problemas em implementar cápsulas até definir as suas próprias políticas. A abordagem recomendada é:

* Criar um cluster do AKS (Create an AKS cluster)
* Defina as suas próprias políticas de segurança de vagem
* Ativar a função de política de segurança do pod

Para mostrar como as políticas predefinidos limitam as implementações do pod, neste artigo primeiro permitimos que as políticas de segurança do pod apresentem e, em seguida, criemos uma política personalizada.

### <a name="behavior-changes-between-pod-security-policy-and-azure-policy"></a>Mudanças de comportamento entre a política de segurança da pod e a política do Azure

Abaixo está um resumo das mudanças de comportamento entre a política de segurança da pod e a Política Azure.

|Scenario| Política de segurança da Pod | Azure Policy |
|---|---|---|
|Instalação|Ativar a funcionalidade de política de segurança do pod |Ativar o Add-on da Política Azure
|Implementar políticas| Implementar recurso de política de segurança de pod| Atribua as políticas Azure ao âmbito do grupo de subscrição ou recursos. O Add-on de política Azure é necessário para aplicações de recursos Kubernetes.
| Políticas predefinidos | Quando a política de segurança do pod é ativada em AKS, são aplicadas políticas privilegiadas e sem restrições. | Não são aplicadas políticas predefinidas, permitindo o Add-on de Política Azure. Deve permitir explicitamente políticas na Política Azure.
| Quem pode criar e atribuir políticas | O administrador do cluster cria um recurso de política de segurança do pod | Os utilizadores devem ter um papel mínimo de permissões de "proprietário" ou "Contribuinte de Política de Recursos" no grupo de recursos do cluster AKS. - Através da API, os utilizadores podem atribuir políticas no âmbito do cluster AKS. O utilizador deve ter o mínimo de permissões de "proprietário" ou "Contribuinte de Política de Recursos" no recurso de cluster AKS. - No portal Azure, as políticas podem ser atribuídas ao nível do grupo Management/subscrição/grupo de recursos.
| Políticas de autorização| Os utilizadores e contas de serviço requerem permissões explícitas para utilizar as políticas de segurança do pod. | Não é necessária nenhuma atribuição adicional para autorizar políticas. Uma vez que as políticas são atribuídas no Azure, todos os utilizadores do cluster podem usar estas políticas.
| Aplicabilidade da política | O utilizador administrador ignora a aplicação das políticas de segurança da pod. | Todos os utilizadores (administrador & não administrador) vêem as mesmas políticas. Não existe invólucro especial baseado nos utilizadores. A aplicação da política pode ser excluída ao nível do espaço de nome.
| Âmbito de política | As políticas de segurança da Pod não são nomeadas | Os modelos de restrição utilizados pela Política Azure não são com o nome de um limite.
| Ação de negação/auditoria/mutação | As políticas de segurança da Pod só negam ações. A mutação pode ser feita com valores predefinidos na criação de pedidos. A validação pode ser feita durante os pedidos de atualização.| A Azure Policy apoia tanto a auditoria & negam ações. A mutação ainda não é suportada, mas planeada.
| Conformidade da política de segurança da Pod | Não existe visibilidade sobre o cumprimento das cápsulas que existiam antes de permitir a política de segurança da cápsula. As cápsulas não conformes criadas após a ativação das políticas de segurança da cápsula são negadas. | Cápsulas não conformes que existiam antes de aplicar as políticas Azure apareceriam em violações políticas. As cápsulas não conformes criadas após permitir que as políticas de Azure sejam negadas se as políticas forem definidas com um efeito de negação.
| Como ver políticas no cluster | `kubectl get psp` | `kubectl get constrainttemplate` - Todas as apólices são devolvidas.
| Padrão de política de segurança pod - Privilegiado | Um recurso de política de segurança de pod privilegiado é criado por padrão ao ativar a funcionalidade. | O modo privilegiado não implica qualquer restrição, como resultado, é equivalente a não ter qualquer atribuição da Política Azure.
| [Norma de política de segurança do pod - Linha de Base/Padrão](https://kubernetes.io/docs/concepts/security/pod-security-standards/#baseline-default) | O utilizador instala um recurso de base de política de segurança do pod. | A Azure Policy fornece uma [iniciativa de base incorporada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) que mapeia a política de segurança da cápsula de base.
| [Padrão de política de segurança pod - Restrito](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) | O utilizador instala um recurso restrito de política de segurança do pod. | A Azure Policy oferece uma [iniciativa restrita incorporada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) que mapeia a política restrita de segurança das cápsulas.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Ativar a política de segurança do pod num cluster AKS

Pode ativar ou desativar a política de segurança do pod utilizando o comando [de atualização az aks.][az-aks-update] O exemplo seguinte permite a política de segurança do pod no nome de cluster *myAKSCluster* no grupo de recursos chamado *myResourceGroup*.

> [!NOTE]
> Para uso no mundo real, não ative a política de segurança do pod até que tenha definido as suas próprias políticas personalizadas. Neste artigo, você ativa a política de segurança do pod como o primeiro passo para ver como as políticas padrão limitam as implementações do pod.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Políticas AKS predefinidos

Ao ativar a política de segurança do pod, a AKS cria uma política padrão chamada *privilegiada*. Não edite nem remova a política predefinida. Em vez disso, crie as suas próprias políticas que definam as definições que pretende controlar. Vamos primeiro ver quais são estas políticas padrão como impactam as implementações das cápsulas.

Para ver as políticas disponíveis, use o [kubectl obter][kubectl-get] comando da PSP, como mostra o exemplo seguinte

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

A política *privilegiada* de segurança do pod é aplicada a qualquer utilizador autenticado no cluster AKS. Esta atribuição é controlada por ClusterRoles e ClusterRoleBindings. Utilize o comando [de rolebindings kubectl get rolebinings][kubectl-get] e procure o *padrão:privilegiado:* encadernação no espaço *de nomes do sistema kube:*

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

Como mostrado na seguinte saída condensada, o ClusterRole *privilegiado da PSP* é atribuído a qualquer *sistema:utilizadores autenticados.* Esta capacidade proporciona um nível básico de privilégio sem que as suas políticas sejam definidas.

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

É importante entender como estas políticas padrão interagem com os pedidos do utilizador para agendar pods antes de começar a criar as suas próprias políticas de segurança. Nas próximas secções, vamos agendar algumas cápsulas para ver estas políticas em ação.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Criar um utilizador de teste num cluster AKS

Por padrão, quando utilizar o comando [az aks get-credentials,][az-aks-get-credentials] as credenciais *de administração* para o cluster AKS são adicionadas ao seu `kubectl` config. O utilizador administrador ignora a aplicação das políticas de segurança da pod. Se utilizar a integração do Azure Ative Directory para os seus clusters AKS, pode iniciar súmis com as credenciais de um utilizador não administrado para ver a aplicação das políticas em ação. Neste artigo, vamos criar uma conta de utilizador de teste no cluster AKS que pode utilizar.

Crie um espaço de nome de amostra chamado *PSP-aks* para os recursos de teste usando o comando de espaço de [nome kubectl.][kubectl-create] Em seguida, crie uma conta de serviço denominada *não-utilizador-administrador* usando o comando [de contagem de serviços de criação de kubectl:][kubectl-create]

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Em seguida, crie um RoleBinding para o *utilizador não-mineiro* realizar ações básicas no espaço de nomes usando o [comando de role-encadernação de criação de kubectl:][kubectl-create]

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Criar comandos de pseudónimos para o utilizador administrador e não administrador

Para realçar a diferença entre o utilizador administrativo regular ao utilizar `kubectl` e o utilizador não administrador criado nos passos anteriores, crie dois pseudónimos de linha de comando:

* O **pseudónimo de administração kubectl-admin** é para o utilizador administrativo regular, e é traçado para o espaço *de nomes PSP-aks.*
* O **pseudónimo kubectl-nonadminuser** é para o *não-utilizador* criado no escalão anterior, e é traçado para o espaço *de nomes PSP-aks.*

Crie estes dois pseudónimos como mostrado nos seguintes comandos:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Teste a criação de uma cápsula privilegiada

Vamos primeiro testar o que acontece quando se marca uma cápsula com o contexto de segurança de `privileged: true` . Este contexto de segurança aumenta os privilégios da cápsula. Na secção anterior que mostrou as políticas de segurança da cápsula AKS padrão, a política de *privilégios* deve negar este pedido.

Crie um ficheiro com o nome `nginx-privileged.yaml` e cole o seguinte manifesto YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        privileged: true
```

Crie a cápsula utilizando o comando [de aplicação de kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

A cápsula não é programada, como mostra a saída do exemplo seguinte:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

A cápsula não chega à fase de agendamento, por isso não há recursos para apagar antes de seguir em frente.

## <a name="test-creation-of-an-unprivileged-pod"></a>Criação de teste de uma cápsula desprivilegiada

No exemplo anterior, a especificação da vagem solicitou uma escalada privilegiada. Este pedido é negado pela política de segurança do pod do *privilégio* padrão, pelo que a cápsula não é agendada. Vamos tentar agora executar o mesmo casulo NGINX sem o pedido de escalada de privilégio.

Crie um ficheiro com o nome `nginx-unprivileged.yaml` e cole o seguinte manifesto YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
```

Crie a cápsula utilizando o comando [de aplicação de kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

A cápsula não é programada, como mostra a saída do exemplo seguinte:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

A cápsula não chega à fase de agendamento, por isso não há recursos para apagar antes de seguir em frente.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Teste de criação de um casulo com um contexto específico do utilizador

No exemplo anterior, a imagem do recipiente tentou automaticamente utilizar a raiz para ligar o NGINX à porta 80. Este pedido foi negado pela política de segurança do pod do *privilégio* padrão, pelo que a cápsula não começa. Vamos tentar agora executar o mesmo pod NGINX com um contexto específico do utilizador, como `runAsUser: 2000` .

Crie um ficheiro com o nome `nginx-unprivileged-nonroot.yaml` e cole o seguinte manifesto YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        runAsUser: 2000
```

Crie a cápsula utilizando o comando [de aplicação de kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

A cápsula não é programada, como mostra a saída do exemplo seguinte:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

A cápsula não chega à fase de agendamento, por isso não há recursos para apagar antes de seguir em frente.

## <a name="create-a-custom-pod-security-policy"></a>Crie uma política de segurança de pod personalizado

Agora que viu o comportamento das políticas de segurança do pod padrão, vamos providenciar uma maneira de o *utilizador não-administrador* agendar com sucesso as cápsulas.

Vamos criar uma política para rejeitar cápsulas que pedem acesso privilegiado. Outras opções, como *runAsUser* ou *volumes permitidos,* não são explicitamente restritas. Este tipo de política nega um pedido de acesso privilegiado, mas de outra forma permite que o cluster execute as cápsulas solicitadas.

Crie um ficheiro com o nome `psp-deny-privileged.yaml` e cole o seguinte manifesto YAML:

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

Crie a política utilizando o comando [de aplicação de kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Para visualizar as políticas disponíveis, utilize o [kubectl obter][kubectl-get] o comando da PSP, como mostra o exemplo seguinte. Compare a política *privilegiada da PSP* com a política de *privilégios* de incumprimento que foi aplicada nos exemplos anteriores para criar uma vagem. Apenas o uso da escalada *PRIVADA* é negado pela sua política. Não existem restrições ao utilizador ou grupo para a política *de negação da PSP.*

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Permitir que a conta do utilizador utilize a política de segurança do pod personalizado

No passo anterior, criou uma política de segurança para rejeitar cápsulas que solicitam acesso privilegiado. Para permitir a utilização da política, cria-se uma *Função* ou um *ClusterRole.* Em seguida, associa-se a um destes papéis usando uma *RoleBinding* ou *ClusterRoleBinding*.

Para tal, crie um ClusterRole que lhe permita *utilizar* a política *privilegiada da PSP* criada no passo anterior. Crie um ficheiro com o nome `psp-deny-privileged-clusterrole.yaml` e cole o seguinte manifesto YAML:

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

Crie o ClusterRole utilizando o comando [de aplicação de kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Agora crie um ClusterRoleBinding para utilizar o ClusterRole criado no passo anterior. Crie um ficheiro com o nome `psp-deny-privileged-clusterrolebinding.yaml` e cole o seguinte manifesto YAML:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
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

Crie um ClusterRoleBinding utilizando o comando [de aplicação de kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> No primeiro passo deste artigo, a funcionalidade de política de segurança do pod foi ativada no cluster AKS. A prática recomendada era apenas ativar a funcionalidade de política de segurança do pod depois de definir as suas próprias políticas. Esta é a fase em que você ativaria a funcionalidade de política de segurança do pod. Uma ou mais políticas personalizadas foram definidas e as contas dos utilizadores têm sido associadas a essas políticas. Agora pode ativar com segurança a funcionalidade de política de segurança do pod e minimizar os problemas causados pelas políticas predefinidas.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Teste a criação de uma cápsula desprivilegiada novamente

Com a sua política de segurança personalizada do pod e uma ligação para a conta do utilizador usar a apólice, vamos tentar criar novamente um pod desprivilegiado. Utilize o mesmo `nginx-privileged.yaml` manifesto para criar a cápsula utilizando o comando de [aplicação de kubectl:][kubectl-apply]

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

A cápsula está programada com sucesso. Quando verificar o estado da cápsula utilizando o comando [kubectl get pods,][kubectl-get] a cápsula está *a funcionar:*

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Este exemplo mostra como pode criar políticas de segurança personalizadas para definir o acesso ao cluster AKS para diferentes utilizadores ou grupos. As políticas AKS predefinidos fornecem controlos apertados sobre quais as cápsulas que podem ser executadas, por isso crie as suas próprias políticas personalizadas para, em seguida, definir corretamente as restrições de que necessita.

Elimine o pod desprivilegiado NGINX utilizando o comando [de eliminação de kubectl][kubectl-delete] e especifique o nome do seu manifesto YAML:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para desativar a política de segurança da cápsula, utilize novamente o comando [de atualização az aks.][az-aks-update] O exemplo a seguir desativa a política de segurança do casulo no nome de cluster *myAKSCluster* no grupo de recursos denominado *myResourceGroup*:

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

Elimine a política de segurança utilizando o comando [de eliminação de kubectl][kubectl-delete] e especifique o nome do seu manifesto YAML:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Por fim, elimine o espaço *de nomes psp-aks:*

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Passos seguintes

Este artigo mostrou-lhe como criar uma política de segurança de pod para impedir o uso de acesso privilegiado. Existem muitas funcionalidades que uma política pode impor, como o tipo de volume ou o utilizador RunAs. Para obter mais informações sobre as opções disponíveis, consulte os documentos de referência da [política de segurança da cápsula Kubernetes.][kubernetes-policy-reference]

Para obter mais informações sobre a limitação do tráfego da rede de [cápsulas, consulte o tráfego seguro entre as cápsulas utilizando as políticas de rede em AKS][network-policies].

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
[policy-samples]: ./policy-reference.md#microsoftcontainerservice
[azure-policy-add-on]: ../governance/policy/concepts/policy-for-kubernetes.md
