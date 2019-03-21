---
title: Práticas recomendadas do operador – identidade, serviços de Kubernetes no Azure (AKS)
description: Aprenda as práticas recomendadas de operador de cluster para saber como gerir a autenticação e autorização para os clusters no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: 201fef6b3e773daa18ae252d1d5734d8d87419b5
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287133"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Melhores práticas para autenticação e autorização no Azure Kubernetes Service (AKS)

Como implementar e manter clusters no Azure Kubernetes Service (AKS), precisa implementar formas de gerir o acesso a recursos e serviços. Sem esses controles, contas podem ter acesso a recursos e serviços não precisam. Também pode ser difícil de controlar qual conjunto de credenciais que foram utilizadas para fazer alterações.

Este artigo de melhores práticas se concentra em como um operador de cluster pode gerir o acesso e identidade para clusters do AKS. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Autenticar os utilizadores de cluster do AKS com o Azure Active Directory
> * Controlar o acesso a recursos com controlos de acesso baseado em funções (RBAC)
> * Utilizar uma identidade gerida para se autenticar com outros serviços

## <a name="use-azure-active-directory"></a>Utilizar o Azure Active Directory

**Melhores diretrizes de práticas** -AKS implementar clusters com a integração do Azure AD. Utilizar o Azure AD centraliza o componente de gestão de identidade. Qualquer alteração do Estado de conta ou grupo do utilizador será atualizada automaticamente no acesso ao cluster do AKS. Utilize funções ou ClusterRoles e enlaces, tal como explicado na próxima seção, definir âmbito de utilizadores ou grupos a menor quantidade de permissões necessárias.

Os desenvolvedores e proprietários da aplicação do cluster do Kubernetes tem acesso a recursos diferentes. Kubernetes não fornece uma solução de gerenciamento de identidades para controlar que os usuários podem interagir com os recursos. Em vez disso, normalmente integrar o seu cluster com uma solução de identidade existente. O Azure Active Directory (AD) fornece uma solução de gestão de identidades de prontas para empresas e pode ser integrado com clusters do AKS.

Com o Azure clusters integrada no AD no AKS, crie *funções* ou *ClusterRoles* que definem as permissões de acesso aos recursos. Em seguida, *vincular* as funções a utilizadores ou grupos do Azure AD. Esses controles de acesso baseado em funções (RBAC) de Kubernetes são abordados na secção seguinte. A integração do Azure AD e como controlar o acesso aos recursos podem ser vistos no diagrama seguinte:

![Autenticação de nível de cluster para a integração do Azure Active Directory com o AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Desenvolvedor autentica com o Azure AD.
1. O ponto de final de emissão de token do Azure AD emite o token de acesso.
1. O desenvolvedor realiza uma ação com o token do Azure AD, como `kubectl create pod`
1. Kubernetes valida o token com o Azure Active Directory e obtém as associações de grupo do desenvolvedor.
1. Kubernetes com base em função de controlo de acesso (RBAC) e cluster políticas são aplicadas.
1. Pedido do desenvolvedor é efetuada com êxito ou não com base no anterior validação de associação a grupos do Azure AD e Kubernetes RBAC e as políticas.

Para criar um cluster do AKS que utiliza o Azure AD, veja [integrar o Azure Active Directory com o AKS][aks-aad].

## <a name="use-role-based-access-controls-rbac"></a>Utilizar controlos de acesso baseado em funções (RBAC)

**Melhores diretrizes de práticas** -utilização Kubernetes RBAC para definir as permissões que os utilizadores ou grupos têm a recursos no cluster. Crie funções e associações que atribuir o mínimo de permissões necessárias. Integrar com o Azure AD, pelo que qualquer alteração do Estado de utilizador ou associação de grupo é atualizada automaticamente e o acesso aos recursos do cluster é atual.

No Kubernetes, pode fornecer controle granular de acesso aos recursos do cluster. As permissões podem ser definidas ao nível do cluster ou para espaços de nomes específicos. Pode definir quais recursos podem ser geridos e com as permissões. Estas funções são aplicado a utilizadores ou grupos com uma associação. Para obter mais informações sobre *funções*, *ClusterRoles*, e *enlaces*, consulte [as opções de acesso e identidade para o Azure Kubernetes Service (AKS)] [aks-concepts-identity].

Por exemplo, pode criar uma função que concede acesso total aos recursos no espaço de nomes com o nome *aplicação de finanças*, conforme mostrado no manifesto do YAML de exemplo seguinte:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

Que liga o utilizador do Azure AD, em seguida, é criado um RoleBinding *developer1\@contoso.com* para RoleBinding, conforme mostrado no manifesto YAML seguinte:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

Quando *developer1\@contoso.com* é autenticado no cluster do AKS, eles têm permissões completas para recursos no *Finanças aplicação* espaço de nomes. Desta forma, logicamente separados e controlo de acesso a recursos. RBAC do Kubernetes que deve ser utilizado em conjunto com o Azure AD-integração, como discutido na seção anterior.

## <a name="use-pod-identities"></a>Utilizar identidades de pod

**Melhores diretrizes de práticas** -não utilizar credenciais dentro de pods ou imagens de contentor, como estão em risco de exposição ou abusos. Em alternativa, utilize as identidades de pod em solicitar automaticamente o acesso com uma central solução de identidade do Azure AD.

Quando pods precisam de acesso a outros serviços do Azure, por exemplo, o Cosmos DB, o Key Vault ou o armazenamento de BLOBs, o pod necessita de credenciais de acesso. Estas credenciais de acesso poderiam ser definidos com a imagem de contentor ou injetado como segredo do Kubernetes, mas tem de ser criados e atribuídos manualmente. Muitas vezes, as credenciais são reutilizadas em pods e não são regularmente giradas.

Identidades geridas para recursos do Azure (atualmente implementados como um projeto de código-fonte aberto do AKS associado) permitem-lhe automaticamente solicitar acesso a serviços através do Azure AD. Não define manualmente as credenciais para pods, em vez disso, eles solicitar um token de acesso em tempo real e pode utilizá-la para acessar apenas os seus serviços atribuídos. No AKS, dois componentes são implementados pela operadora de rede de cluster para permitir que os pods utilizar identidades geridas:

* **O servidor de gestão de nó identidade (NMI)** é um pod que é executado como um DaemonSet em cada nó do cluster do AKS. O servidor de NMI escuta de pedidos de pod aos serviços do Azure.
* **O controlador de identidade gerida (MIC)** é um pod central com permissões para consultar o servidor de API do Kubernetes e verifica a existência de uma identidade do Azure de mapeamento que corresponde a um pod.

Quando pods solicitar acesso a um serviço do Azure, as regras de rede redirecionarem o tráfego para o servidor de gestão de nó identidade (NMI). O servidor de NMI identifica pods que pedem acesso aos serviços do Azure com base no respetivo endereço remoto e consulta o controlador de identidade gerida (MIC). O MIC verifica a existência de mapeamentos de identidade do Azure no cluster do AKS e o servidor de NMI, em seguida, pede que um token de acesso do Azure Active Directory (AD) com base no mapeamento de identidades o pod. O Azure AD fornece acesso ao servidor NMI, que é retornado para o pod. Esse token de acesso pode ser utilizado pelo pod para, em seguida, solicitar acesso a serviços no Azure.

No exemplo a seguir, um desenvolvedor cria um pod que utiliza uma identidade gerida para pedir acesso a uma instância de servidor SQL do Azure:

![Identidades de pod permitem um pod em solicitar automaticamente acesso a outros serviços](media/operator-best-practices-identity/pod-identities.png)

1. Operador do cluster primeiro cria uma conta de serviço que pode ser utilizada para mapear as identidades quando pods pedem acesso aos serviços.
1. O servidor de NMI e o MIC são implementadas para retransmitir quaisquer pedidos de pod para tokens de acesso para o Azure AD.
1. Um desenvolvedor implementa um pod com uma identidade gerida que solicita um token de acesso através do servidor de NMI.
1. O token é retornado para o pod e utilizado para aceder a uma instância de servidor SQL do Azure.

Identidades de pod gerido é um projeto de código-fonte aberto do AKS e não é suportado pelo suporte técnico do Azure. Ele é fornecido para recolher comentários e bugs de nossa Comunidade. O projeto não é recomendado para utilização em produção.

Para utilizar identidades de pod, consulte [identidades do Azure Active Directory para aplicações do Kubernetes][aad-pod-identity].

## <a name="next-steps"></a>Passos Seguintes

Este artigo de melhores práticas se concentrou em autenticação e autorização para o cluster e recursos. Para implementar algumas dessas melhores práticas, consulte os artigos seguintes:

* [Integrar o Azure Active Directory com o AKS][aks-aad]
* [Utilizar identidades geridas para recursos do Azure com o AKS][aad-pod-identity]

Para obter mais informações sobre as operações de cluster no AKS, consulte as seguintes práticas recomendadas:

* [Isolamento de vários inquilinos e de cluster][aks-best-practices-scheduler]
* [Funcionalidades de agendador básicas do Kubernetes][aks-best-practices-scheduler]
* [Funcionalidades avançadas de scheduler de Kubernetes][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: aad-integration.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
