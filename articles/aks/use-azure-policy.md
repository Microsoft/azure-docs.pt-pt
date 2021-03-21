---
title: Use a Política Azure para proteger o seu cluster
description: Utilize a Política Azure para garantir um cluster Azure Kubernetes Service (AKS).
ms.service: container-service
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 46e92e6842204cd323992a2561e71302bb9cc722
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102193504"
---
# <a name="secure-your-cluster-with-azure-policy"></a>Proteja o seu cluster com a Política Azure

Para melhorar a segurança do seu cluster Azure Kubernetes Service (AKS), pode aplicar e aplicar políticas de segurança incorporadas no seu cluster utilizando a Azure Policy. [A Azure Policy][azure-policy] ajuda a impor normas organizacionais e a avaliar o cumprimento em escala. Depois de instalar o [Azure Policy Add-on para a AKS,][kubernetes-policy-reference]pode aplicar definições de políticas individuais ou grupos de definições políticas chamadas iniciativas (por vezes chamadas de policysets) ao seu cluster. Consulte [as definições de Azure Policy incorporadas para a AKS][aks-policies] para obter uma lista completa das definições de política e iniciativa da AKS.

Este artigo mostra-lhe como aplicar definições de política ao seu cluster e verificar se essas atribuições estão a ser aplicadas.

## <a name="prerequisites"></a>Pré-requisitos

- Um aglomerado AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].
- O Add-on de Política Azure para AKS instalado num cluster AKS. Siga estes [passos para instalar o Add-on de Política Azure][azure-policy-addon].

## <a name="assign-a-built-in-policy-definition-or-initiative"></a>Atribuir uma definição ou iniciativa de política incorporada

Para aplicar uma definição ou iniciativa política, utilize o portal Azure.

1. Navegue para o serviço Azure Policy no portal Azure.
1. No painel esquerdo da página Política Azure, selecione **Definições**.
1. Em **Categorias** `Kubernetes` selecione .
1. Escolha a definição de política ou iniciativa que pretende aplicar. Para este exemplo, selecione a `Kubernetes cluster pod security baseline standards for Linux-based workloads` iniciativa.
1. Selecione **Atribuir**.
1. Definir o **Âmbito** para o grupo de recursos do cluster AKS com o Add-on de política Azure ativado.
1. Selecione a página **parâmetros** e atualize o **Efeito** de `audit` para bloquear novas `deny` implementações violando a iniciativa de base. Também pode adicionar espaços de nome adicionais para excluir da avaliação. Para este exemplo, mantenha os valores predefinidos.
1. Selecione **Rever + criar** e criar para submeter a atribuição de política. 

## <a name="validate-a-azure-policy-is-running"></a>Validar uma Política de Azure está em execução

Confirme que as atribuições de política são aplicadas ao seu cluster executando o seguinte:

```azurecli-interactive
kubectl get constrainttemplates
```

> [!NOTE]
> As atribuições de política podem demorar [até 20 minutos a sincronizar-se][azure-policy-assign-policy] em cada cluster.

A saída deve ser semelhante a:

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              23m
k8sazureallowedusersgroups               23m
k8sazureblockhostnamespace               23m
k8sazurecontainerallowedimages           23m
k8sazurecontainerallowedports            23m
k8sazurecontainerlimits                  23m
k8sazurecontainernoprivilege             23m
k8sazurecontainernoprivilegeescalation   23m
k8sazureenforceapparmor                  23m
k8sazurehostfilesystem                   23m
k8sazurehostnetworkingports              23m
k8sazurereadonlyrootfilesystem           23m
k8sazureserviceallowedports              23m
```

### <a name="validate-rejection-of-a-privileged-pod"></a>Validar a rejeição de uma cápsula privilegiada

Vamos primeiro testar o que acontece quando se marca uma cápsula com o contexto de segurança de `privileged: true` . Este contexto de segurança aumenta os privilégios da cápsula. A iniciativa não permite cápsulas privilegiadas, pelo que o pedido será negado, resultando na rejeição da implantação.

Crie um ficheiro com o nome `nginx-privileged.yaml` e cole o seguinte manifesto YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        privileged: true
```

Crie o pod com [comando de aplicação de kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f nginx-privileged.yaml
```

Como esperado, a cápsula não é programada, como mostra a saída do exemplo seguinte:

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

A cápsula não chega à fase de agendamento, por isso não há recursos para apagar antes de seguir em frente.

### <a name="test-creation-of-an-unprivileged-pod"></a>Criação de teste de uma cápsula desprivilegiada

No exemplo anterior, a imagem do recipiente tentou automaticamente utilizar a raiz para ligar o NGINX à porta 80. Este pedido foi negado pela iniciativa política, pelo que a cápsula não começa. Vamos tentar agora executar o mesmo pod NGINX sem acesso privilegiado.

Crie um ficheiro com o nome `nginx-unprivileged.yaml` e cole o seguinte manifesto YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Crie a cápsula utilizando o comando [de aplicação de kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f nginx-unprivileged.yaml
```

A cápsula está programada com sucesso. Quando verificar o estado da cápsula utilizando o comando [kubectl get pods,][kubectl-get] a cápsula está *a funcionar:*

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

Este exemplo mostra a iniciativa de base que afeta apenas as implementações que violam as políticas da recolha. As implementações permitidas continuam a funcionar.

Elimine o pod desprivilegiado NGINX utilizando o comando [de eliminação de kubectl][kubectl-delete] e especifique o nome do seu manifesto YAML:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-a-policy-or-initiative"></a>Desativar uma política ou iniciativa

Para remover a iniciativa de base:

1. Navegue para o painel de política no portal Azure.
1. Selecione **Atribuições** a partir do painel esquerdo.
1. Clique no **botão ...** ao lado da `Kubernetes cluster pod security baseline standards for Linux-based workloads` iniciativa.
1. Selecione **Eliminar a atribuição**.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre como funciona a Política Azure:

- [Descrição Geral do Azure Policy][azure-policy]
- [Iniciativas e polícias da Azure Policy para a AKS][aks-policies]
- Remova o [Add-on de Política Azure][azure-policy-addon-remove].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs

<!-- LINKS - internal -->
[aks-policies]: policy-reference.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-policy]: ../governance/policy/overview.md
[azure-policy-addon]: ../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks
[azure-policy-addon-remove]: ../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks
[azure-policy-assign-policy]: ../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
