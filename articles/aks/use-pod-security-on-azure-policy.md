---
title: Cápsulas seguras com política Azure no Serviço Azure Kubernetes (AKS)
description: Saiba como proteger os casulos com a política Azure no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 09/22/2020
ms.openlocfilehash: 34f2bfe346d7163a254e2ccecd1d7ef63ddb4194
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092625"
---
# <a name="secure-pods-with-azure-policy"></a>Proteger pods com o Azure Policy

Para melhorar a segurança do seu cluster AKS, pode controlar as funções que as cápsulas são concedidas e se alguma coisa está a correr contra a política da empresa. Este acesso é definido através de políticas incorporadas fornecidas pelo [Azure Policy Add-on for AKS][kubernetes-policy-reference]. Ao fornecer um controlo adicional sobre os aspetos de segurança da especificação da sua cápsula, como privilégios de raiz, permite uma maior adesão e visibilidade à segurança do que é implantado no seu cluster. Se uma cápsula não cumprir as condições especificadas na apólice, a Azure Policy pode não permitir que a cápsula inicie ou sinalize uma violação. Este artigo mostra-lhe como usar a Política Azure para limitar a implantação de cápsulas em AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tem um cluster AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

### <a name="install-the-azure-policy-add-on-for-aks"></a>Instale o Add-on de Política Azure para AKS

Para proteger as cápsulas AKS através da Azure Policy, é necessário instalar o Add-on Azure Policy para AKS num cluster AKS. Siga estes [passos para instalar o Add-on de Política Azure](../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks).

Este documento pressupõe que tem o seguinte, que são implantados no walk-through ligado acima.

* Registrou os `Microsoft.ContainerService` fornecedores e `Microsoft.PolicyInsights` recursos usando `az provider register`
* Azure CLI 2.12 ou maior
* Um cluster AKS numa versão de 1.15 ou superior instalada com o Azure Policy Add-on

## <a name="overview-of-securing-pods-with-azure-policy-for-aks"></a>Visão geral da fixação de cápsulas com política Azure para a AKS

>[!NOTE]
> Este documento detalha como utilizar a Azure Policy para proteger as cápsulas, que é o sucessor da [funcionalidade de política de segurança do casulo Kubernetes na pré-visualização](use-pod-security-policies.md).
> **Tanto a política de segurança do pod (pré-visualização) como o Add-on de Política Azure para AKS não podem ser ativados simultaneamente.**
> 
> Se instalar o Add-on de Política Azure num cluster com a política de segurança do pod ativada, [siga estes passos para desativar a política de segurança do casulo](use-pod-security-policies.md#enable-pod-security-policy-on-an-aks-cluster).

Num cluster AKS, um controlador de admissão é utilizado para intercetar pedidos para o servidor API quando um recurso deve ser criado e atualizado. O controlador de admissão pode então *validar* o pedido de recursos contra um conjunto de regras sobre a sua criação.

Anteriormente, a política de segurança do pod de recurso [(pré-visualização)](use-pod-security-policies.md) foi ativada através do projeto Kubernetes para limitar quais as cápsulas que podem ser implementadas.

Ao utilizar o Azure Policy Add-on, um cluster AKS pode usar políticas Azure incorporadas, que protegem cápsulas e outros recursos Kubernetes semelhantes à política de segurança do pod anteriormente. O Azure Policy Add-on for AKS instala uma instância gerida de [Gatekeeper](https://github.com/open-policy-agent/gatekeeper), um controlador de admissão validador. A política de Azure para Kubernetes baseia-se no agente de política aberta Open Policy, que se baseia na [linguagem política do Rego.](../governance/policy/concepts/policy-for-kubernetes.md#policy-language)

Este documento detalha como utilizar a Política Azure para proteger as cápsulas num cluster AKS e instruir como migrar das políticas de segurança do pod (pré-visualização).

## <a name="limitations"></a>Limitações

As seguintes limitações gerais aplicam-se ao Add-on de Política Azure para os clusters Kubernetes:

- O Azure Policy Add-on for Kubernetes é suportado na versão **1.14** ou superior de Kubernetes.
- Azure Policy Add-on para Kubernetes só pode ser implantado em piscinas de nól de linux
- Apenas as definições políticas incorporadas são apoiadas
- Número máximo de registos não conformes por política por agrupamento: **500**
- Número máximo de registos não conformes por subscrição: **1 milhão**
- As instalações do Gatekeeper fora do Azure Policy Add-on não são suportadas. Desinstale quaisquer componentes instalados por uma instalação anterior do Gatekeeper antes de ativar o Add-on de Política Azure.
- [As razões para o incumprimento](../governance/policy/how-to/determine-non-compliance.md#compliance-reasons) não estão disponíveis para este [modo fornecedor de recursos](../governance/policy/concepts/definition-structure.md#resource-provider-modes)

As seguintes limitações aplicam-se apenas ao Add-on da Política Azure para a AKS:

- [A política de segurança AKS Pod (pré-visualização)](use-pod-security-policies.md) e o Add-on de Política Azure para a AKS não podem ser ambos ativados. 
- Espaços de nome automaticamente excluídos pelo Azure Policy Add-on para avaliação: _kube-system,_ _gatekeeper-system_ e _aks-periscope_. Se utilizar a política de rede Calico com a versão 1.20 ou superior a Kubernetes, são automaticamente excluídos mais 2 espaços de nome, que são _sistema calico_ e _operador de tigera._

### <a name="recommendations"></a>Recomendações

Seguem-se recomendações gerais relativas à utilização do Complemento político Azure:

- O Add-on de Política Azure requer 3 componentes gatekeeper para executar: 1 cápsula de auditoria e 2 réplicas de pod webhook. Estes componentes consomem mais recursos à medida que a contagem de recursos da Kubernetes e atribuições de políticas aumenta no cluster, o que requer operações de auditoria e execução.

  - Por menos de 500 cápsulas num único cluster com um máximo de 20 restrições: 2 vCPUs e 350 MB de memória por componente.
  - Para mais de 500 cápsulas num único cluster com um máximo de 40 restrições: 3 vCPUs e 600 MB de memória por componente.

A seguinte recomendação aplica-se apenas à AKS e ao Addon da Política Azure:

- Utilize a piscina de nó do sistema com `CriticalAddonsOnly` mancha para agendar cápsulas gatekeeper. Para obter mais informações, consulte [Usando as piscinas de nó do sistema.](use-system-pools.md#system-and-user-node-pools)
- Proteja o tráfego de saída dos seus clusters AKS. Para obter mais informações, consulte [o controle do tráfego para os nós de cluster](limit-egress-traffic.md).
- Se o cluster `aad-pod-identity` tiver ativado, as cápsulas de identidade gerida do nó (NMI) modificam os iptables dos nós para intercetar chamadas para o ponto final dos metadados de instância Azure. Esta configuração significa que qualquer pedido feito ao ponto final dos metadados é intercetado pelo NMI mesmo que a cápsula não utilize `aad-pod-identity` . AzurePodIdentityException CRD pode ser configurado para informar `aad-pod-identity` que quaisquer pedidos para o ponto final de metadados originários de um pod que corresponda às etiquetas definidas em CRD devem ser proxiited sem qualquer processamento em NMI. As cápsulas do sistema com `kubernetes.azure.com/managedby: aks` etiqueta no espaço de _nomes do sistema kube_ devem ser excluídas `aad-pod-identity` configurando o CRD AzurePodIdentityException. Para obter mais informações, consulte [Desativar a identidade do aad-pod para uma cápsula ou aplicação específica.](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)
  Para configurar uma exceção, instale a [yaML de exceção ao microfone.](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml)

O complemento da Política Azure requer que os recursos de CPU e memória funcionem. Estes requisitos aumentam à medida que o tamanho de um cluster aumenta. Consulte [as recomendações da Política Azure][policy-recommendations] para orientação geral para a utilização do complemento da Política Azure.

## <a name="azure-policies-to-secure-kubernetes-pods"></a>Políticas azure para garantir cápsulas Kubernetes

Após a instalação do Add-on de Política Azure, nenhuma política é aplicada por defeito.

Existem 11 políticas individuais de Azure incorporadas e duas iniciativas incorporadas que especificamente protegem os casulos num cluster AKS.
Cada política pode ser personalizada com um efeito. Uma lista completa das [políticas da AKS e os seus efeitos apoiados está listada aqui.][policy-samples] Leia mais sobre [os efeitos da Política Azure](../governance/policy/concepts/effects.md).

As políticas de Azure podem ser aplicadas ao nível do grupo de gestão, subscrição ou grupo de recursos. Ao atribuir uma política ao nível do grupo de recursos, certifique-se de que o grupo de recursos target AKS é selecionado no âmbito da política. Todos os clusters no âmbito atribuído com o Add-on de Política Azure instalado estão em alcance para a política.

Se utilizar [a política de segurança do pod (pré-visualização) ](use-pod-security-policies.md), aprenda a [migrar para a Política Azure e sobre outras diferenças comportamentais](#migrate-from-kubernetes-pod-security-policy-to-azure-policy).

### <a name="built-in-policy-initiatives"></a>Iniciativas políticas integradas

Uma iniciativa na Política Azure é uma coleção de definições políticas adaptadas para alcançar um objetivo singular abrangente. A utilização de iniciativas pode simplificar a gestão e atribuição de políticas em todos os clusters AKS. Uma iniciativa existe como um único objeto, saiba mais sobre [as iniciativas da Política Azure.](../governance/policy/overview.md#initiative-definition)

A Azure Policy for Kubernetes oferece duas iniciativas incorporadas, que garantem cápsulas, [linha de base](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) e [restritas.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00)

Ambas as iniciativas incorporadas são construídas a partir de definições utilizadas na [política de segurança de pods da Kubernetes.](https://github.com/kubernetes/website/blob/master/content/en/examples/policy/baseline-psp.yaml)

|[Controlo da política de segurança da Pod](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)| Ligação de definição de política Azure| [Iniciativa de base](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) | [Iniciativa restrita](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) |
|---|---|---|---|
|Não permitir a execução de contentores privilegiados|[Nuvem Pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95edb821-ddaf-4404-9732-666045e056b4)| Yes | Yes
|Não permitir o uso partilhado de espaços de nomes de hospedeiros|[Nuvem Pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a1ee2f-2a2a-4576-bf2a-e0e36709c2b8)| Yes | Yes
|Restringir todo o uso da rede de anfitriões e portas|[Nuvem Pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe)| Yes | Yes
|Restringir qualquer utilização do sistema de ficheiros hospedeiro|[Nuvem Pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F098fc59e-46c7-4d99-9b16-64990e543d75)| Yes | Yes
|Restringir as capacidades do Linux ao [conjunto predefinido](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)|[Nuvem Pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc26596ff-4d70-4e6a-9a30-c2506bd2f80c) | Yes | Yes
|Restringir a utilização de tipos de volume definidos|[Nuvem Pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16697877-1118-4fb1-9b65-9898ec2509ec)| - | Sim - os tipos de volume permitidos `configMap` `emptyDir` são, `projected` , `downwardAPI` , `persistentVolumeClaim`|
|Escalada de privilégio para raiz|[Nuvem Pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c6e92c9-99f0-4e55-9cf2-0c234dc48f99) | - | Yes |
|Restringir os IDs do utilizador e do grupo do recipiente|[Nuvem Pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | Yes|
|Restringir a atribuição de um FSGroup que detém os volumes da cápsula|[Nuvem Pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | Sim - regras permitidas `runAsUser: mustRunAsNonRoot` `supplementalGroup: mustRunAs 1:65536` são, `fsGroup: mustRunAs 1:65535` , . . `runAsGroup: mustRunAs 1:65535` .  |
|Requer perfil de seccomp|[Nuvem Pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F975ce327-682c-4f2e-aa46-b9598289b86c) | - | Sim, os Proffiles permitidos são * `docker/default` ou `runtime/default` |

\* docker/default é depreciado em Kubernetes desde v1.11

### <a name="additional-optional-policies"></a>Políticas opcionais adicionais

Existem políticas adicionais de Azure, que podem ser aplicadas sem rodeios fora da aplicação de uma iniciativa. Considere a possibilidade de anexar estas políticas para além de iniciativas se os seus requisitos não forem cumpridos pelas iniciativas incorporadas.

|[Controlo da política de segurança da Pod](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)| Ligação de definição de política Azure| Aplicar para além da iniciativa Baseline | Candidatar-se para além da iniciativa restrita |
|---|---|---|---|
|Defina o perfil AppArmor utilizado por contentores|[Nuvem Pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F511f5417-5d12-434d-ab2e-816901e72a5e) | Opcional | Opcional |
|Permitir suportes que não são lidos apenas|[Nuvem Pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf49d893-a74c-421d-bc95-c663042e5b80) | Opcional | Opcional |
|Restringir a controladores flexvolume específicos|[Nuvem Pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4a8fce0-2dd5-4c21-9a36-8f0ec809d663) | Opcional - use se quiser apenas restringir os controladores FlexVolume, mas não outros definidos por "Restringir a utilização de tipos de volume definidos" | Não aplicável - A iniciativa restrita inclui "Restringir a utilização de tipos de volume definido", que não permite a todos os condutores da FlexVolume |

### <a name="unsupported-built-in-policies-for-managed-aks-clusters"></a>Políticas não apoiadas para clusters AKS geridos

> [!NOTE]
> As 3 políticas seguintes não são **apoiadas na AKS** devido à personalização de aspetos geridos e garantidos pela AKS como um serviço gerido. Estas políticas são construídas especificamente para aglomerados ligados a Azure Arc com aviões de controlo não geridos.

|[Controlo da política de segurança da Pod](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)|
|---|
|Defina o contexto personalizado SELinux de um recipiente|
|Restringir o perfil sisctl utilizado pelos contentores|
|Os tipos de montagem Proc predefinidos são definidos para reduzir a superfície de ataque|

<!---
# Removing until custom initiatives are supported the week after preview

#### Custom initiative

If the built-in initiatives to address pod security do not match your requirements, you can choose your own policies to exist in a custom initiative. Read more about [building custom initatives in Azure Policy](../governance/policy/tutorials/create-and-manage#create-and-assign-an-initiative-definition).

--->

### <a name="namespace-exclusion"></a>Exclusão do espaço de nome

> [!WARNING]
> Os pods em espaços de nomes de administração, como o sistema kube, devem correr para que um cluster permaneça saudável, removendo um espaço de nome necessário da lista de espaços de nome excluídos por defeito pode desencadear violações de políticas devido a uma cápsula de sistema necessária.

A AKS requer que as cápsulas do sistema corram num cluster para fornecer serviços críticos, como a resolução de DNS. As políticas que limitam a funcionalidade do casulo podem ter impacto na estabilidade do sistema de capacidade. Como resultado, os seguintes espaços de nome são **excluídos da avaliação de políticas durante os pedidos de admissão durante a criação, atualização e auditoria de políticas**. Isto força novos destacamentos para estes espaços de nome a serem excluídos das políticas de Azure.

1. kube-system
1. gatekeeper-sistema
1. azure-arc
1. aks-periscópio

Espaços de nome personalizados adicionais podem ser excluídos da avaliação durante a criação, atualização e auditoria. Estas exclusões devem ser utilizadas se tiver cápsulas especializadas que funcionam num espaço de nome sancionado e que queiram evitar desencadear violações de auditoria.

## <a name="apply-the-baseline-initiative"></a>Aplicar a iniciativa de base

> [!TIP]
> Todas as políticas não têm efeito de auditoria. Os efeitos podem ser atualizados para serem ressarçantes a qualquer momento através da Política Azure.

Para aplicar a iniciativa de base, podemos atribuir através do portal Azure.
<!--
1. Navigate to the Policy service in Azure portal
1. In the left pane of the Azure Policy page, select **Definitions**
1. Search for "Baseline Profile" on the search pane to the right of the page
1. Select `Kubernetes Pod Security Standards Baseline Profile for Linux-based workloads` from the `Kubernetes` category
-->
1. Siga [este link para o portal Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) para rever a iniciativa de base de segurança da pod
1. Definir o **Âmbito** para o nível de subscrição ou apenas o grupo de recursos que detém clusters AKS com o Add-on de política Azure ativado
1. Selecione a página **parâmetros** e atualize o **Efeito** de `audit` bloquear novas `deny` implementações violando a iniciativa de base
1. Adicione espaços de nome adicionais para excluir da avaliação durante a criação, atualização e auditoria, [alguns espaços de nome são excluídos à força da avaliação de políticas.](#namespace-exclusion) 
 ![ efeito de atualização](media/use-pod-security-on-azure-policy/update-effect.png)
1. Selecione **Review + criar** para submeter as políticas

Confirme que as políticas são aplicadas ao seu cluster executando `kubectl get constrainttemplates` .

> [!NOTE]
> As políticas podem demorar [até 20 minutos a sincronizar](../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition) cada cluster.

A saída deve ser semelhante a:

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              30m
k8sazureblockhostnamespace               30m
k8sazurecontainernoprivilege             30m
k8sazurehostfilesystem                   30m
k8sazurehostnetworkingports              30m
```

## <a name="validate-rejection-of-a-privileged-pod"></a>Validar a rejeição de uma cápsula privilegiada

Vamos primeiro testar o que acontece quando se marca uma cápsula com o contexto de segurança de `privileged: true` . Este contexto de segurança aumenta os privilégios da cápsula. A iniciativa de base não permite cápsulas privilegiadas, pelo que o pedido será negado, resultando na rejeição da implantação.

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

## <a name="test-creation-of-an-unprivileged-pod"></a>Criação de teste de uma cápsula desprivilegiada

No exemplo anterior, a imagem do recipiente tentou automaticamente utilizar a raiz para ligar o NGINX à porta 80. Este pedido foi negado pela iniciativa de base política, pelo que a cápsula não começa. Vamos tentar agora executar o mesmo pod NGINX sem acesso privilegiado.

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

## <a name="disable-policies-and-the-azure-policy-add-on"></a>Desativar as políticas e o Add-on da Política Azure

Para remover a iniciativa de base:

1. Navegue para o painel de política no portal Azure
1. Selecione **Atribuições** do painel esquerdo
1. Clique no "..." botão ao lado do Perfil de Base
1. Selecione "Eliminar atribuição"

![Excluir atribuição](media/use-pod-security-on-azure-policy/delete-assignment.png)

Para desativar o Add-on de Política Azure, utilize o comando [az aks desativar addons.][az-aks-disable-addons]

```azurecli
az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
```

Saiba como remover o [Azure Policy Add-on do portal Azure](../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks).

## <a name="migrate-from-kubernetes-pod-security-policy-to-azure-policy"></a>Migrar da política de segurança da cápsula Kubernetes para a Política de Azure

Para migrar da política de segurança da cápsula, você precisa tomar as seguintes ações em um cluster.

1. [Desativar a política de segurança](use-pod-security-policies.md#clean-up-resources) do casulo no cluster
1. Ativar o [Add-on da Política Azure][kubernetes-policy-reference]
1. Permitir as políticas Azure desejadas a partir de [políticas incorporadas disponíveis][policy-samples]

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

## <a name="next-steps"></a>Passos seguintes

Este artigo mostrou-lhe como aplicar uma política Azure que restringe a implementação de cápsulas privilegiadas para impedir o uso de acesso privilegiado. Há muitas políticas que podem ser aplicadas, como políticas que restringem a utilização de volumes. Para obter mais informações sobre as opções disponíveis, consulte a [Política Azure para os docs de referência de Kubernetes][kubernetes-policy-reference].

Para obter mais informações sobre a limitação do tráfego da rede de [cápsulas, consulte o tráfego seguro entre as cápsulas utilizando as políticas de rede em AKS][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity
[aad-pod-identity-exception]: https://azure.github.io/aad-pod-identity/docs/configure/application_exception

<!-- LINKS - internal -->
[policy-recommendations]: ../governance/policy/concepts/policy-for-kubernetes.md
[policy-limitations]: ../governance/policy/concepts/policy-for-kubernetes.md?#limitations
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
[policy-samples]: ./policy-reference.md#microsoftcontainerservice
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
[az-aks-disable-addons]: /cli/azure/aks#az-aks-disable-addons
[az-policy-assignment-delete]: /cli/azure/policy/assignment#az-policy-assignment-delete