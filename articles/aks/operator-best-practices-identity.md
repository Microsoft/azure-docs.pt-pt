---
title: Práticas recomendadas do operador – identidade nos serviços Kubernetess do Azure (AKS)
description: Conheça as práticas recomendadas do operador de cluster para saber como gerenciar a autenticação e a autorização para clusters no serviço de kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: mlearned
ms.openlocfilehash: 06d15d66df0b2ec0049d4b2fffae6a9909b05dca
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549143"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para autenticação e autorização no serviço kubernetes do Azure (AKS)

Ao implantar e manter clusters no AKS (serviço kubernetes do Azure), você precisa implementar maneiras de gerenciar o acesso a recursos e serviços. Sem esses controles, as contas podem ter acesso a recursos e serviços que não precisam. Também pode ser difícil controlar qual conjunto de credenciais foi usado para fazer alterações.

Este artigo de práticas recomendadas se concentra em como um operador de cluster pode gerenciar o acesso e a identidade para clusters AKS. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Autenticar usuários do cluster AKS com Azure Active Directory
> * Controlar o acesso a recursos com RBAC (controles de acesso baseado em função)
> * Usar uma identidade gerenciada para se autenticar com outros serviços

## <a name="use-azure-active-directory"></a>Utilizar o Azure Active Directory

**Diretrizes de práticas recomendadas** -implantar clusters AKs com a integração do Azure AD. O uso do Azure AD centraliza o componente de gerenciamento de identidade. Qualquer alteração na conta de usuário ou no status do grupo é atualizada automaticamente no acesso ao cluster AKS. Use funções ou ClusterRoles e associações, conforme discutido na próxima seção, para delimitar usuários ou grupos à menor quantidade de permissões necessárias.

Os desenvolvedores e os proprietários de aplicativos do seu cluster kubernetes precisam acessar recursos diferentes. O kubernetes não fornece uma solução de gerenciamento de identidade para controlar quais usuários podem interagir com quais recursos. Em vez disso, você normalmente integra seu cluster a uma solução de identidade existente. O Azure Active Directory (AD) fornece uma solução de gerenciamento de identidade pronta para empresas e pode ser integrada a clusters AKS.

Com os clusters integrados do Azure AD no AKS, você cria *funções* ou *ClusterRoles* que definem permissões de acesso aos recursos. Em seguida, você *associa* as funções a usuários ou grupos do Azure AD. Esses controles de acesso baseado em função (RBAC) kubernetes são discutidos na próxima seção. A integração do Azure AD e como você controla o acesso aos recursos pode ser vista no diagrama a seguir:

![Autenticação em nível de cluster para integração de Azure Active Directory com o AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. O desenvolvedor é autenticado com o Azure AD.
1. O ponto de extremidade de emissão de token do Azure AD emite o token de acesso.
1. O desenvolvedor executa uma ação usando o token do Azure AD, como `kubectl create pod`
1. Kubernetes valida o token com Azure Active Directory e busca as associações de grupo do desenvolvedor.
1. O RBAC (controle de acesso baseado em função) kubernetes e as políticas de cluster são aplicadas.
1. A solicitação do desenvolvedor é bem-sucedida ou não com base na validação anterior da Associação de grupo do Azure AD e das políticas e RBAC do kubernetes.

Para criar um cluster AKS que usa o Azure AD, consulte [integrar Azure Active Directory com AKs][aks-aad].

## <a name="use-role-based-access-controls-rbac"></a>Usar controles de acesso baseado em função (RBAC)

**Diretrizes de práticas recomendadas** -use o RBAC kubernetes para definir as permissões que os usuários ou grupos têm para os recursos no cluster. Crie funções e associações que atribuem a menor quantidade de permissões necessárias. Integre com o Azure AD para que qualquer alteração no status do usuário ou na associação de grupo seja atualizada automaticamente e o acesso aos recursos de cluster seja atual.

No kubernetes, você pode fornecer um controle granular do acesso aos recursos no cluster. As permissões podem ser definidas no nível do cluster ou em namespaces específicos. Você pode definir quais recursos podem ser gerenciados e com quais permissões. Essas funções são então aplicadas a usuários ou grupos com uma associação. Para obter mais informações sobre *funções*, *ClusterRoles*e *associações*, consulte [Opções de acesso e identidade para o serviço kubernetes do Azure (AKs)][aks-concepts-identity].

Por exemplo, você pode criar uma função que conceda acesso completo a recursos no namespace chamado *Finance-app*, conforme mostrado no seguinte exemplo de manifesto YAML:

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

Em seguida, é criada uma função que associa o usuário do Azure AD *developer1\@contoso.com* ao rolebinding, conforme mostrado no seguinte manifesto YAML:

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

Quando *developer1\@contoso.com* é autenticado no cluster AKs, ele tem permissões completas para recursos no namespace *Finance-app* . Dessa forma, você separa e controla logicamente o acesso aos recursos. O RBAC kubernetes deve ser usado em conjunto com a integração do Azure AD, conforme discutido na seção anterior.

Para saber como usar os grupos do Azure AD para controlar o acesso aos recursos do kubernetes usando o RBAC, consulte [controlar o acesso a recursos de cluster usando controles de acesso baseado em função e identidades de Azure Active Directory no AKs][azure-ad-rbac].

## <a name="use-pod-identities"></a>Usar identidades de Pod

**Diretrizes de práticas recomendadas** -não use credenciais fixas em imagens de contêiner ou pods, pois elas correm o risco de exposição ou abuso. Em vez disso, use as identidades de pod para solicitar automaticamente o acesso usando uma solução de identidade central do Azure AD. As identidades de Pod se destinam ao uso apenas com as imagens de contêiner e pods do Linux.

Quando o pods precisar de acesso a outros serviços do Azure, como Cosmos DB, Key Vault ou armazenamento de BLOB, o Pod precisará de credenciais de acesso. Essas credenciais de acesso podem ser definidas com a imagem de contêiner ou injetadas como um segredo kubernetes, mas precisam ser criadas e atribuídas manualmente. Muitas vezes, as credenciais são reutilizadas em pods e não são regularmente giradas.

Identidades gerenciadas para recursos do Azure (atualmente implementados como um projeto de código-fonte aberto AKS associado) permitem que você solicite automaticamente o acesso a serviços por meio do Azure AD. Você não define manualmente as credenciais para pods, em vez disso, eles solicitam um token de acesso em tempo real e podem usá-lo para acessar apenas seus serviços atribuídos. No AKS, dois componentes são implantados pelo operador de cluster para permitir que o pods use identidades gerenciadas:

* **O servidor de identidade de gerenciamento de nó (NMI)** é um pod que é executado como um daemonset em cada nó no cluster AKs. O servidor NMI escuta solicitações de pod para os serviços do Azure.
* **O controlador de identidade gerenciada (MIC)** é um Pod central com permissões para consultar o servidor de API kubernetes e verifica um mapeamento de identidade do Azure que corresponde a um pod.

Quando o pods solicita acesso a um serviço do Azure, as regras de rede redirecionam o tráfego para o servidor de identidade de gerenciamento de nó (NMI). O servidor NMI identifica os pods que solicitam acesso aos serviços do Azure com base em seu endereço remoto e consulta o MIC (controlador de identidade gerenciada). O MIC verifica os mapeamentos de identidade do Azure no cluster AKS, e o servidor NMI solicita um token de acesso do Azure Active Directory (AD) com base no mapeamento de identidade do pod. O Azure AD fornece acesso ao servidor NMI, que é retornado para o pod. Esse token de acesso pode ser usado pelo pod para, em seguida, solicitar acesso aos serviços no Azure.

No exemplo a seguir, um desenvolvedor cria um pod que usa uma identidade gerenciada para solicitar acesso a uma instância de SQL Server do Azure:

![As identidades de Pod permitem que um pod solicite automaticamente o acesso a outros serviços](media/operator-best-practices-identity/pod-identities.png)

1. O operador de cluster primeiro cria uma conta de serviço que pode ser usada para mapear identidades quando o pods solicita acesso aos serviços.
1. O servidor de NMI e o MIC são implantados para retransmitir quaisquer solicitações de pod para tokens de acesso ao Azure AD.
1. Um desenvolvedor implanta um pod com uma identidade gerenciada que solicita um token de acesso por meio do servidor NMI.
1. O token é retornado para o pod e usado para acessar uma instância de SQL Server do Azure.

> [!NOTE]
> As identidades de Pod gerenciadas são um projeto de software livre e não tem suporte do suporte técnico do Azure.

Para usar identidades de Pod, consulte [identidades de Azure Active Directory para aplicativos kubernetes][aad-pod-identity].

## <a name="next-steps"></a>Passos seguintes

Este artigo de práticas recomendadas se concentra na autenticação e autorização para o cluster e os recursos. Para implementar algumas dessas práticas recomendadas, consulte os seguintes artigos:

* [Integrar o Azure Active Directory com o AKS][aks-aad]
* [Usar identidades gerenciadas para recursos do Azure com AKS][aad-pod-identity]

Para obter mais informações sobre as operações de cluster no AKS, consulte as seguintes práticas recomendadas:

* [Multilocação e isolamento de cluster][aks-best-practices-scheduler]
* [Recursos básicos do Agendador do kubernetes][aks-best-practices-scheduler]
* [Recursos do Agendador do kubernetes avançado][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
