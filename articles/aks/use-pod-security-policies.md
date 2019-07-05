---
title: Utilizar políticas de segurança de pod no Azure Kubernetes Service (AKS)
description: Saiba como controlar o pod admissão com PodSecurityPolicy no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/17/2019
ms.author: iainfou
ms.openlocfilehash: 9da722006651cfc9e9f2a175d5c330ba5df08123
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447073"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Pré-visualizar - proteger o seu cluster através de políticas de segurança de pod no Azure Kubernetes Service (AKS)

Para melhorar a segurança do cluster do AKS, pode limitar o que podem ser pods agendada. Pods que solicitam recursos não permitir que o não é possível executar no cluster do AKS. Definir este acesso utilizando as políticas de segurança de pod. Este artigo mostra-lhe como utilizar políticas de segurança de pod para limitar a implementação de pods no AKS.

> [!IMPORTANT]
> Funcionalidades de pré-visualização do AKS são self-service, participar. Eles são fornecidos para recolher comentários e bugs de nossa Comunidade. Em pré-visualização, esses recursos não se destinam a utilização de produção. Funcionalidades em pré-visualização pública enquadram-se em suporte "melhor esforço". Assistência das equipas de suporte técnico do AKS está disponível durante o horário do Pacífico fuso horário (PST) apenas. Para obter mais informações, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS][aks-support-policies]
> * [FAQ de suporte do Azure][aks-faq]

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem um cluster do AKS existente. Se precisar de um cluster do AKS, consulte o guia de introdução do AKS [com a CLI do Azure][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

Precisa da versão 2.0.61 da CLI do Azure ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar a CLI do Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão CLI de pré-visualização do aks

Para utilizar políticas de segurança de pod, terá do *pré-visualização do aks* CLI versão da extensão 0.4.1 ou superior. Instalar o *pré-visualização do aks* extensão da CLI do Azure com o [Adicionar extensão az][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] comando::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Registar o fornecedor de recursos de política de segurança de pod

Para criar ou atualizar um cluster do AKS para utilizar políticas de segurança de pod, ative primeiro um sinalizador de funcionalidade na sua subscrição. Para registar o *PodSecurityPolicyPreview* sinalizador de funcionalidade, utilize o [Registre-se de funcionalidade de az][az-feature-register] comando conforme mostrado no exemplo a seguir:

> [!CAUTION]
> Quando registra um recurso numa assinatura, não pode atualmente anular o registo essa funcionalidade. Depois de ativar a algumas funcionalidades de pré-visualização, as predefinições podem ser utilizadas para todos os clusters do AKS, em seguida, criados na subscrição. Não a ativar funcionalidades de pré-visualização em subscrições de produção. Utilize uma subscrição separada para testar as funcionalidades de pré-visualização e colher comentários baseados em.

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Demora alguns minutos para que o estado a mostrar *registado*. Pode verificar o estado de registo com o [lista de funcionalidades de az][az-feature-list] comando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registo do *containerservice* fornecedor de recursos com o [Registre-se fornecedor de az][az-provider-register] comando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Descrição geral das políticas de segurança de pod

Num cluster do Kubernetes, é utilizado um controlador de admissão para interceptar solicitações para o servidor de API quando um recurso é criado. O controlador de admissão pode então *validar* a solicitação de recurso com um conjunto de regras, ou *sofrem mutações* o recurso para alterar os parâmetros de implementação.

*PodSecurityPolicy* é um controlador de admissão valida uma especificação de pod cumpre os requisitos definidos. Estes requisitos podem limitar a utilização de contentores com privilégios, acesso a determinados tipos de armazenamento, ou o utilizador ou grupo que pode ser executado como o contentor. Ao tentar implementar um recurso em que as especificações de pod não cumprem os requisitos descritos na política de segurança de pod, o pedido é negado. Esta capacidade de controlar o que podem ser pods agendada no AKS cluster impede algumas vulnerabilidades de segurança possível ou Escalamentos de privilégios.

Quando ativa a política de segurança de pod num cluster do AKS, algumas políticas predefinidas são aplicadas. Estas políticas padrão fornecem uma experiência de out-of-the-box para definir os pods o que pode ser agendada. No entanto, os utilizadores de cluster podem ter problemas ao implementar os pods até que definir suas próprias políticas. A abordagem recomendada é:

* Criar um cluster do AKS (Create an AKS cluster)
* Definir as suas políticas de segurança de pod
* Ativar a funcionalidade de política de segurança de pod

Para mostrar como políticas predefinidas limitam as implementações de pod, neste artigo vamos primeiro ativar a funcionalidade de políticas de segurança de pod, em seguida, criar uma política personalizada.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Ativar política de segurança de pod num cluster do AKS

Pode ativar ou desativar a política de segurança do pod utilizando o [atualizar az aks][az-aks-update] comando. Política de segurança de pod de ativa o seguinte exemplo no nome do cluster *myAKSCluster* no grupo de recursos com o nome *myResourceGroup*.

> [!NOTE]
> Para a utilização do mundo real, não a ativar a política de segurança de pod até que tenha definido a suas próprias políticas personalizadas. Neste artigo, vai ativar a política de segurança de pod como o primeiro passo para ver como as políticas padrão limitam pod implementações.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Políticas padrão do AKS

Quando ativa a política de segurança de pod, AKS cria duas políticas padrão com o nome *com privilégios* e *restrito*. Não editar ou remover estas políticas padrão. Em vez disso, crie as suas políticas que definem as definições que pretende controlar. Vamos primeiro contato com que estas políticas padrão são como afetam as implementações de pod.

Para ver as políticas disponíveis, utilize o [kubectl obter psp][kubectl-get] de comando, conforme mostrado no exemplo a seguir. Como parte do padrão *restrito* política, o utilizador é negado *PRIV* Utilize para Escalamento de pod com privilégios e o utilizador *MustRunAsNonRoot*.

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted   false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

O *restrito* pod política de segurança é aplicada a qualquer utilizador autenticado no cluster do AKS. Esta atribuição é controlada pelo ClusterRoles e ClusterRoleBindings. Utilize o [kubectl obter clusterrolebindings][kubectl-get] de comandos e procure o *predefinido: restritos:* enlace:

```console
kubectl get clusterrolebindings default:restricted -o yaml
```

Como mostra o resultado condensado seguinte, o *psp: restrito* ClusterRole é atribuído a qualquer *system: autenticado* utilizadores. Esta capacidade fornece um nível básico de restrições sem as suas políticas que está a ser definidas.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  [...]
  name: default:restricted
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:restricted
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:authenticated
```

É importante compreender como estas políticas predefinidas de interagirem com os pedidos de utilizador para agendar pods antes de começar a criar seu próprio pod políticas de segurança. Nas próximas secções, vamos agendar algumas pods para ver estas políticas padrão em ação.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Criar um utilizador de teste num cluster do AKS

Por predefinição, quando utiliza a [az aks get-credentials][az-aks-get-credentials] comando, o *administração* credenciais para o cluster do AKS e adicionado à sua `kubectl` config. O utilizador administrador ignora a imposição de políticas de segurança de pod. Se utilizar a integração do Active Directory do Azure para os seus clusters do AKS, pode iniciar sessão com as credenciais de um utilizador não administrador para ver a imposição de políticas em ação. Neste artigo, vamos criar uma conta de utilizador de teste do cluster do AKS que pode utilizar.

Criar um espaço de nomes de exemplo com o nome *psp aks* nos recursos de teste que utilizem o [kubectl criar espaço de nomes][kubectl-create] comando. Em seguida, crie uma conta de serviço com o nome *utilizador nonadmin* com o [kubectl criar serviceaccount][kubectl-create] comando:

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Em seguida, crie um RoleBinding para o *utilizador nonadmin* para executar ações básicas no espaço de nomes com o [kubectl criar rolebinding][kubectl-create] comando:

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Criar comandos de alias para o utilizador de administrador e não-administrador

Destacar a diferença entre o utilizador de administrador regulares ao utilizar `kubectl` e o utilizador não administrador, criado nos passos anteriores, crie dois aliases de linha de comandos:

* O **kubectl-admin** alias destina-se o utilizador administrador regulares e tem como escopo o *psp aks* espaço de nomes.
* O **kubectl nonadminuser** alias destina-se a *nonadmin utilizador* criado no passo anterior e tem como escopo o *psp aks* espaço de nomes.

Crie estes duas aliases, conforme mostrado nos seguintes comandos:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Testar a criação de um pod com privilégios

Vamos testar primeiro o que acontece quando agendar um pod com o contexto de segurança `privileged: true`. Este contexto de segurança escala privilégios o pod. Na secção anterior, que mostrava o pod do AKS padrão políticas de segurança, o *restrito* política deve negar este pedido.

Crie um ficheiro denominado `nginx-privileged.yaml` e cole o manifesto YAML seguinte:

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

Criar o pod com o [kubectl aplicar][kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

O pod falha agendadas, conforme mostrado no seguinte exemplo:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

O pod não atinge a fase de planejamento, portanto, não há nenhum recurso de eliminar antes de prosseguir com.

## <a name="test-creation-of-an-unprivileged-pod"></a>Testar a criação de um pod sem privilégios

No exemplo anterior, a especificação de pod pediu o Escalamento de privilégios. Este pedido é negado por predefinição *restrito* pod política de segurança, para que o pod não consegue ser agendada. Vamos experimentar agora em execução nesse mesmo pod do NGINX sem que a solicitação de escalonamento de privilégio.

Crie um ficheiro denominado `nginx-unprivileged.yaml` e cole o manifesto YAML seguinte:

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

Criar o pod com o [kubectl aplicar][kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

O agendador de Kubernetes aceita a solicitação de pod. No entanto, se examinar o estado da utilização de pod `kubectl get pods`, existe um erro:

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

Utilize o [kubectl descrevem pod][kubectl-describe] comando para ver os eventos para o pod. O seguinte exemplo condensado mostra que o contentor e a imagem requerem permissões de raiz, apesar de não solicitamos-los:

```console
$ kubectl-nonadminuser describe pod nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                     From                               Message
  ----     ------     ----                    ----                               -------
  Normal   Scheduled  7m14s                   default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged to aks-agentpool-34777077-0
  Warning  Failed     5m2s (x12 over 7m13s)   kubelet, aks-agentpool-34777077-0  Error: container has runAsNonRoot and image will run as root
  Normal   Pulled     2m10s (x25 over 7m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
```

Apesar de não solicitamos qualquer acesso privilegiado, a imagem de contentor de NGINX tem de criar uma ligação para a porta *80*. Para vincular as portas *1024* e abaixo, o *raiz* utilizador é necessário. Quando o pod tenta iniciar, o *restrito* política de segurança de pod nega este pedido.

Este exemplo mostra que as políticas de segurança de pod padrão criadas pelo AKS estão em vigor e restringem as ações de que um utilizador pode executar. É importante compreender o comportamento dessas diretivas de padrão, como não pode esperar um pod NGINX básico para ser negado.

Antes de passar para o passo seguinte, elimine este pod de teste com o [kubectl eliminar o pod][kubectl-delete] comando:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Testar a criação de um pod com um contexto de utilizador específico

No exemplo anterior, a imagem de contentor automaticamente a tentar utilizar raiz para vincular o NGINX para a porta 80. Este pedido foi negado pela predefinição *restrito* pod política de segurança, pelo que não for possível iniciar o pod. Vamos experimentar agora em execução nesse mesmo pod do NGINX com um contexto de utilizador específico, tal como `runAsUser: 2000`.

Crie um ficheiro denominado `nginx-unprivileged-nonroot.yaml` e cole o manifesto YAML seguinte:

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

Criar o pod com o [kubectl aplicar][kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

O agendador de Kubernetes aceita a solicitação de pod. No entanto, se examinar o estado da utilização de pod `kubectl get pods`, existe um erro diferente que o exemplo anterior:

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

Utilize o [kubectl descrevem pod][kubectl-describe] comando para ver os eventos para o pod. O exemplo condensado seguinte mostra os eventos de pod:

```console
$ kubectl-nonadminuser describe pods nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                   From                               Message
  ----     ------     ----                  ----                               -------
  Normal   Scheduled  2m14s                 default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged-nonroot to aks-agentpool-34777077-0
  Normal   Pulled     118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
  Normal   Created    118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Created container
  Normal   Started    118s (x3 over 2m12s)  kubelet, aks-agentpool-34777077-0  Started container
  Warning  BackOff    105s (x5 over 2m11s)  kubelet, aks-agentpool-34777077-0  Back-off restarting failed container
```

Os eventos indicam que o contentor foi criado e iniciado. Não há nada imediatamente óbvias sobre por que motivo o pod está num Estado com falhas. Vamos examinar os registos de pod com o [kubectl registos][kubectl-logs] comando:

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

A saída de registo de exemplo seguinte fornece uma indicação de que na configuração do NGINX em si, existe um erro de permissões quando tenta iniciar o serviço. Novamente, este erro é causado pela necessidade de vincular a porta 80. Embora a especificação de pod definido uma conta de utilizador normal, esta conta de utilizador não é suficiente no nível de sistema operacional para o serviço NGINX para iniciar e vincular à porta restrita.

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

Novamente, é importante compreender o comportamento das políticas de segurança de pod predefinidas. Este erro foi um pouco mais difícil rastrear e, novamente, não pode esperar um pod NGINX básico para ser negado.

Antes de passar para o passo seguinte, elimine este pod de teste com o [kubectl eliminar o pod][kubectl-delete] comando:

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>Criar uma política de segurança de pod personalizado

Agora que já viu o comportamento das políticas de segurança de pod predefinidas, vamos fornecer uma maneira para o *nonadmin utilizador* para agendar com êxito os pods.

Vamos criar uma política para rejeitar pods que pedem acesso privilegiado. Outras opções, tais como *runAsUser* ou permitidas *volumes*, não estão explicitamente restrito. Este tipo de política nega um pedido para acesso privilegiado, mas caso contrário, permite que o cluster, execute os pods pedidos.

Crie um ficheiro denominado `psp-deny-privileged.yaml` e cole o manifesto YAML seguinte:

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

Crie a política com o [kubectl aplicar][kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Para ver as políticas disponíveis, utilize o [kubectl obter psp][kubectl-get] de comando, conforme mostrado no exemplo a seguir. Compare os *psp negar-com privilégios* política com o padrão *restrito* política tivesse sido imposta nos exemplos anteriores para criar um pod. Apenas a utilização de *PRIV* escalonamento é negado pela sua política. Não há restrições sobre o utilizador ou grupo para o *privilégios psp negar* política.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted            false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Permitir que a conta de utilizador para utilizar a política de segurança de pod personalizado

No passo anterior, criou uma política de segurança de pod rejeitar pods que o acesso privilegiado de pedido. Para permitir que a política a utilizar, crie uma *função* ou uma *ClusterRole*. Em seguida, associar uma destas funções usando um *RoleBinding* ou *ClusterRoleBinding*.

Para este exemplo, crie um ClusterRole que lhe permite *utilizar* a *privilégios psp negar* política criada no passo anterior. Crie um ficheiro denominado `psp-deny-privileged-clusterrole.yaml` e cole o manifesto YAML seguinte:

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

Crie a ClusterRole com o [kubectl aplicar][kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Agora, crie um ClusterRoleBinding utilizar ClusterRole criado no passo anterior. Crie um ficheiro denominado `psp-deny-privileged-clusterrolebinding.yaml` e cole o manifesto YAML seguinte:

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

Criar uma ClusterRoleBinding com o [kubectl aplicar][kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> No primeiro passo deste artigo, a funcionalidade de política de segurança de pod foi ativada no cluster do AKS. Foi a prática recomendada permitir que o recurso de política de segurança de pod apenas depois de definir as suas políticas. Esta é a fase em que permitiria a funcionalidade de política de segurança de pod. Um ou mais políticas personalizadas foram definidas e contas de utilizador foram associadas essas políticas. Agora pode com segurança a política de segurança de pod de recursos e minimizar os problemas causados pelas políticas de predefinição.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>A criação de um pod sem privilégios de teste

Com a política de segurança de pod personalizadas aplicadas e uma ligação para a conta de utilizador para utilizar a política, vamos tentar criar um pod sem privilégios novamente. Utilizar o mesmo `nginx-privileged.yaml` manifesto para criar o pod com o [kubectl aplicar][kubectl-apply] comando:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

O pod está agendado com êxito. Quando verifica o estado do pod com o [kubectl obter pods][kubectl-get] comando, é o pod *em execução*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Este exemplo mostra como pode criar políticas de segurança de pod personalizado para definir o acesso ao cluster do AKS para diferentes utilizadores ou grupos. A predefinição, as políticas AKS fornecem controlos apertados em quais pods podem executar, pelo que pode criar suas próprias políticas personalizadas para, em seguida, definir corretamente as restrições que precisa.

Eliminar o pod do NGINX sem privilégios com o [kubectl eliminar][kubectl-delete] de comando e especifique o nome do seu manifesto YAML:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Limpar recursos

Para desativar a política de segurança de pod, utilize o [atualizar az aks][az-aks-update] novamente o comando. Política de segurança de pod de desativa o seguinte exemplo no nome do cluster *myAKSCluster* no grupo de recursos com o nome *myResourceGroup*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Em seguida, elimine o ClusterRole e ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Eliminar a política de rede com [kubectl eliminar][kubectl-delete] de comando e especifique o nome do seu manifesto YAML:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Por fim, eliminar a *psp aks* espaço de nomes:

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Passos Seguintes

Este artigo mostrou como criar uma política de segurança de pod para impedir a utilização de acesso privilegiado. Existem muitos recursos que pode impor uma política, como o tipo de volume ou o utilizador de RunAs. Para obter mais informações sobre as opções disponíveis, consulte a [pod de Kubernetes documentos de referência de política de segurança][kubernetes-policy-reference].

Para obter mais informações sobre como limitar o tráfego de rede de pod, consulte [proteger o tráfego entre pods através de políticas de rede no AKS][network-policies].

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
