---
title: Práticas recomendadas do operador – identidade nos serviços Kubernetess do Azure (AKS)
description: Conheça as práticas recomendadas do operador de cluster para saber como gerenciar a autenticação e a autorização para clusters no serviço de kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: mlearned
ms.openlocfilehash: 35cded14977988c80455a9c28a0e01e7913d58f4
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069511"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para autenticação e autorização no serviço kubernetes do Azure (AKS)

Ao implantar e manter clusters no AKS (serviço kubernetes do Azure), você precisa implementar maneiras de gerenciar o acesso a recursos e serviços. Sem esses controles, as contas podem ter acesso a recursos e serviços que não precisam. Também pode ser difícil controlar qual conjunto de credenciais foi usado para fazer alterações.

Este artigo de práticas recomendadas se concentra em como um operador de cluster pode gerenciar o acesso e a identidade para clusters AKS. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Autenticar usuários do cluster AKS com Azure Active Directory
> * Controlar o acesso a recursos com RBAC (controles de acesso baseado em função)
> * Usar uma identidade gerenciada para se autenticar com outros serviços

## <a name="use-azure-active-directory"></a>Utilizar o Azure Active Directory

**Orientação de boas práticas** - Implementar clusters AKS com integração azure AD. O uso do Azure AD centraliza o componente de gerenciamento de identidade. Qualquer alteração na conta de usuário ou no status do grupo é atualizada automaticamente no acesso ao cluster AKS. Use funções ou ClusterRoles e associações, conforme discutido na próxima seção, para delimitar usuários ou grupos à menor quantidade de permissões necessárias.

Os desenvolvedores e os proprietários de aplicativos do seu cluster kubernetes precisam acessar recursos diferentes. O kubernetes não fornece uma solução de gerenciamento de identidade para controlar quais usuários podem interagir com quais recursos. Em vez disso, você normalmente integra seu cluster a uma solução de identidade existente. O Azure Active Directory (AD) fornece uma solução de gerenciamento de identidade pronta para empresas e pode ser integrada a clusters AKS.

Com clusters integrados em AD Azure em AKS, cria *Funções* ou *ClusterRoles* que definem permissões de acesso aos recursos. Em seguida, *liga* as funções a utilizadores ou grupos da Azure AD. Esses controles de acesso baseado em função (RBAC) kubernetes são discutidos na próxima seção. A integração do Azure AD e como você controla o acesso aos recursos pode ser vista no diagrama a seguir:

![Autenticação em nível de cluster para integração de Azure Active Directory com o AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. O desenvolvedor é autenticado com o Azure AD.
1. O ponto de extremidade de emissão de token do Azure AD emite o token de acesso.
1. O desenvolvedor realiza uma ação utilizando o token Azure AD, como `kubectl create pod`
1. Kubernetes valida o token com Azure Active Directory e busca as associações de grupo do desenvolvedor.
1. O RBAC (controle de acesso baseado em função) kubernetes e as políticas de cluster são aplicadas.
1. A solicitação do desenvolvedor é bem-sucedida ou não com base na validação anterior da Associação de grupo do Azure AD e das políticas e RBAC do kubernetes.

Para criar um cluster AKS que utilize o Azure AD, consulte [O Diretório Ativo Integrado Azure com AKS][aks-aad].

## <a name="use-role-based-access-controls-rbac"></a>Usar controles de acesso baseado em função (RBAC)

**Orientação de boas práticas** - Utilize o Kubernetes RBAC para definir as permissões que os utilizadores ou grupos têm para recursos no cluster. Crie funções e associações que atribuem a menor quantidade de permissões necessárias. Integre com o Azure AD para que qualquer alteração no status do usuário ou na associação de grupo seja atualizada automaticamente e o acesso aos recursos de cluster seja atual.

No kubernetes, você pode fornecer um controle granular do acesso aos recursos no cluster. As permissões podem ser definidas no nível do cluster ou em namespaces específicos. Você pode definir quais recursos podem ser gerenciados e com quais permissões. Essas funções são então aplicadas a usuários ou grupos com uma associação. Para obter mais informações sobre *Papéis*, *ClusterRoles*, e *Encadernações,* consulte opções de acesso e identidade para o [Serviço Azure Kubernetes (AKS)][aks-concepts-identity].

Como exemplo, pode criar uma Função que conceda acesso total aos recursos no espaço de nome denominado *app financeira,* como mostra o seguinte manifesto YAML:

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

É então criado um RoleBinding que liga o desenvolvedor de utilizadores da AD *Azure 1\@contoso.com* à RoleBinding, como mostra o seguinte manifesto YAML:

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

Quando o *desenvolvedor1\@contoso.com* é autenticado contra o cluster AKS, eles têm permissões completas para recursos no espaço de nome de *aplicações financeiras.* Dessa forma, você separa e controla logicamente o acesso aos recursos. O RBAC kubernetes deve ser usado em conjunto com a integração do Azure AD, conforme discutido na seção anterior.

Para ver como utilizar os grupos Azure AD para controlar o acesso aos recursos da Kubernetes utilizando o RBAC, consulte o controle de acesso aos recursos de [cluster utilizando controlos de acesso baseados em papéis e identidades do Azure Ative Directory no AKS.][azure-ad-rbac]

## <a name="use-pod-identities"></a>Usar identidades de Pod

**Orientação de boas práticas** - Não utilize credenciais fixas dentro de cápsulas ou imagens de contentores, uma vez que estão em risco de exposição ou abuso. Em vez disso, use as identidades de pod para solicitar automaticamente o acesso usando uma solução de identidade central do Azure AD. As identidades de Pod se destinam ao uso apenas com as imagens de contêiner e pods do Linux.

Quando o pods precisar de acesso a outros serviços do Azure, como Cosmos DB, Key Vault ou armazenamento de BLOB, o Pod precisará de credenciais de acesso. Essas credenciais de acesso podem ser definidas com a imagem de contêiner ou injetadas como um segredo kubernetes, mas precisam ser criadas e atribuídas manualmente. Muitas vezes, as credenciais são reutilizadas em pods e não são regularmente giradas.

Identidades gerenciadas para recursos do Azure (atualmente implementados como um projeto de código-fonte aberto AKS associado) permitem que você solicite automaticamente o acesso a serviços por meio do Azure AD. Você não define manualmente as credenciais para pods, em vez disso, eles solicitam um token de acesso em tempo real e podem usá-lo para acessar apenas seus serviços atribuídos. No AKS, dois componentes são implantados pelo operador de cluster para permitir que o pods use identidades gerenciadas:

* O servidor De **némio** de Identidade de Gestão de NMI é um pod que funciona como Um DaemonSet em cada nó do cluster AKS. O servidor NMI escuta solicitações de pod para os serviços do Azure.
* **O Controlador de Identidade Gerido (MIC)** é um casulo central com permissões para consultar o servidor Kubernetes API e verifica um mapeamento de identidade Azure que corresponde a um pod.

Quando o pods solicita acesso a um serviço do Azure, as regras de rede redirecionam o tráfego para o servidor de identidade de gerenciamento de nó (NMI). O servidor NMI identifica os pods que solicitam acesso aos serviços do Azure com base em seu endereço remoto e consulta o MIC (controlador de identidade gerenciada). O MIC verifica os mapeamentos de identidade do Azure no cluster AKS, e o servidor NMI solicita um token de acesso do Azure Active Directory (AD) com base no mapeamento de identidade do pod. O Azure AD fornece acesso ao servidor NMI, que é retornado para o pod. Esse token de acesso pode ser usado pelo pod para, em seguida, solicitar acesso aos serviços no Azure.

No exemplo a seguir, um desenvolvedor cria um pod que usa uma identidade gerenciada para solicitar acesso a uma instância de SQL Server do Azure:

![As identidades de Pod permitem que um pod solicite automaticamente o acesso a outros serviços](media/operator-best-practices-identity/pod-identities.png)

1. O operador de cluster primeiro cria uma conta de serviço que pode ser usada para mapear identidades quando o pods solicita acesso aos serviços.
1. O servidor de NMI e o MIC são implantados para retransmitir quaisquer solicitações de pod para tokens de acesso ao Azure AD.
1. Um desenvolvedor implanta um pod com uma identidade gerenciada que solicita um token de acesso por meio do servidor NMI.
1. O token é retornado para o pod e usado para acessar uma instância de SQL Server do Azure.

> [!NOTE]
> As identidades de Pod gerenciadas são um projeto de software livre e não tem suporte do suporte técnico do Azure.

Para utilizar identidades de pod, consulte identidades do [Diretório Ativo Azure para aplicações Kubernetes][aad-pod-identity].

## <a name="next-steps"></a>Passos seguintes

Este artigo de práticas recomendadas se concentra na autenticação e autorização para o cluster e os recursos. Para implementar algumas dessas práticas recomendadas, consulte os seguintes artigos:

* [Integrar o Diretório Ativo Azure com a AKS][aks-aad]
* [Utilize identidades geridas para recursos Azure com AKS][aad-pod-identity]

Para obter mais informações sobre as operações de cluster no AKS, consulte as seguintes práticas recomendadas:

* [Multi-arrendamento e isolamento de clusters][aks-best-practices-cluster-isolation]
* [Funcionalidades básicas do programador Kubernetes][aks-best-practices-scheduler]
* [Funcionalidades avançadas do programador kubernetes][aks-best-practices-advanced-scheduler]

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
