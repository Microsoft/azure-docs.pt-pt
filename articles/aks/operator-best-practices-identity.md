---
title: Boas práticas para gerir a identidade
titleSuffix: Azure Kubernetes Service
description: Conheça as melhores práticas do operador de cluster supérbio para gerir a autenticação e autorização para clusters no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: e02b542f74a2dd7b7e88f1fa075ad6a736895e76
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020052"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Boas práticas para autenticação e autorização no Serviço Azure Kubernetes (AKS)

À medida que implementa e mantém clusters no Serviço Azure Kubernetes (AKS), é necessário implementar formas de gerir o acesso aos recursos e serviços. Sem estes controlos, as contas podem ter acesso a recursos e serviços de que não necessitam. Também pode ser difícil rastrear que conjunto de credenciais foram usados para fazer alterações.

Este artigo de boas práticas centra-se na forma como um operador de cluster pode gerir o acesso e identidade dos clusters AKS. Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Autenticar utilizadores de cluster AKS com Diretório Ativo Azure
> * Controlar o acesso aos recursos com controlos de acesso baseados em funções (RBAC)
> * Use uma identidade gerida para autenticar-se com outros serviços

## <a name="use-azure-active-directory"></a>Utilizar o Azure Active Directory

**Orientação de boas práticas** - Implementar clusters AKS com integração azure AD. A utilização da AD Azure centraliza a componente de gestão de identidade. Qualquer alteração na conta de utilizador ou no estado do grupo é automaticamente atualizada no acesso ao cluster AKS. Utilize Funções ou ClusterRoles e Encadernações, conforme discutido na secção seguinte, para examinar utilizadores ou grupos para pelo menos quantidade de permissões necessárias.

Os desenvolvedores e proprietários de aplicações do seu cluster Kubernetes precisam de acesso a diferentes recursos. A Kubernetes não fornece uma solução de gestão de identidade para controlar quais os utilizadores que podem interagir com os recursos. Em vez disso, normalmente integra o seu cluster com uma solução de identidade existente. O Azure Ative Directory (AD) fornece uma solução de gestão de identidade pronta para a empresa e pode integrar-se com clusters AKS.

Com clusters integrados em AD Azure em AKS, cria *Funções* ou *ClusterRoles* que definem permissões de acesso aos recursos. Em seguida, *liga* as funções a utilizadores ou grupos da Azure AD. Estes controlos de acesso baseados em funções de Kubernetes (RBAC) são discutidos na secção seguinte. A integração da AD Azure e a forma como controla o acesso aos recursos podem ser vistas no seguinte diagrama:

![Autenticação ao nível do cluster para integração de Diretórios Ativos Azure com AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Desenvolvedor autentica com Azure AD.
1. O ponto final de emissão de fichas da AD Azure emite o sinal de acesso.
1. O desenvolvedor realiza uma ação usando o token Azure AD, tais como`kubectl create pod`
1. A Kubernetes valida o símbolo com o Azure Ative Directory e vai buscar os membros do grupo de desenvolvedores.
1. Aplicam-se as políticas de controlo de acesso baseadas em papéis kubernetes (RBAC) e de cluster.
1. O pedido do desenvolvedor é bem sucedido ou não com base na validação prévia da filiação do grupo Azure AD e do Kubernetes RBAC e políticas.

Para criar um cluster AKS que utilize o Azure AD, consulte [O Diretório Ativo Integrado Azure com AKS][aks-aad].

## <a name="use-role-based-access-controls-rbac"></a>Utilizar controlos de acesso baseados em funções (RBAC)

**Orientação de boas práticas** - Utilize o Kubernetes RBAC para definir as permissões que os utilizadores ou grupos têm para recursos no cluster. Crie funções e encadernações que atribuam a menor quantidade de permissões necessárias. Integre com a AD Azure para que qualquer alteração no estatuto de utilizador ou membro do grupo seja automaticamente atualizada e o acesso aos recursos de cluster seja atual.

Em Kubernetes, você pode fornecer o controle granular do acesso aos recursos no cluster. As permissões podem ser definidas ao nível do cluster, ou a espaços de nome específicos. Pode definir quais os recursos que podem ser geridos, e com que permissões. Estas funções são então aplicadas a utilizadores ou grupos com uma ligação. Para obter mais informações sobre *Papéis*, *ClusterRoles*, e *Encadernações,* consulte opções de acesso e identidade para o [Serviço Azure Kubernetes (AKS)][aks-concepts-identity].

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

É então criado um RoleBinding que liga o desenvolvedor de utilizadores da AD *Azure 1 \@ contoso.com* à RoleBinding, como mostra o seguinte manifesto YAML:

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

Quando o *desenvolvedor1 \@ contoso.com* é autenticado contra o cluster AKS, eles têm permissões completas para recursos no espaço de nome de *app financeira.* Desta forma, separa-se logicamente e controla o acesso aos recursos. O Kubernetes RBAC deve ser utilizado em conjunto com a integração da AD Azure, tal como discutido na secção anterior.

Para ver como utilizar os grupos Azure AD para controlar o acesso aos recursos da Kubernetes utilizando o RBAC, consulte o controle de acesso aos recursos de [cluster utilizando controlos de acesso baseados em papéis e identidades do Azure Ative Directory no AKS.][azure-ad-rbac]

## <a name="use-pod-identities"></a>Use identidades de pod

**Orientação de boas práticas** - Não utilize credenciais fixas dentro de cápsulas ou imagens de contentores, uma vez que estão em risco de exposição ou abuso. Em vez disso, utilize identidades de pod para solicitar automaticamente o acesso através de uma solução de identidade AD Azure central. As identidades do pod destinam-se a ser utilizadas apenas com cápsulas Linux e imagens de contentores.

Quando as cápsulas precisam de acesso a outros serviços Azure, como cosmos DB, Key Vault ou Blob Storage, o casulo precisa de credenciais de acesso. Estas credenciais de acesso podem ser definidas com a imagem do recipiente ou injetadas como um segredo kubernetes, mas precisam de ser criadas manualmente e atribuídas. Muitas vezes, as credenciais são reutilizadas através de cápsulas, e não são regularmente giradas.

Identidades geridas para recursos Azure (atualmente implementado como um projeto de código aberto AKS associado) permitem-lhe solicitar automaticamente o acesso aos serviços através da AD Azure. Não define manualmente credenciais para cápsulas, em vez disso pedem um sinal de acesso em tempo real, e podem usá-lo para aceder apenas aos seus serviços atribuídos. No AKS, dois componentes são implantados pelo operador do cluster para permitir que as cápsulas utilizem identidades geridas:

* O servidor De **némio** de Identidade de Gestão de NMI é um pod que funciona como Um DaemonSet em cada nó do cluster AKS. O servidor NMI ouve pedidos de pod aos serviços Azure.
* **O Controlador de Identidade Gerido (MIC)** é um casulo central com permissões para consultar o servidor Kubernetes API e verifica um mapeamento de identidade Azure que corresponde a um pod.

Quando as cápsulas solicitam acesso a um serviço Azure, as regras da rede redirecionam o tráfego para o servidor De némio de Identidade de Gestão do Nó (NMI). O servidor NMI identifica cápsulas que solicitam acesso aos serviços Azure com base no seu endereço remoto, e consulta o Controlador de Identidade Gerido (MIC). O MIC verifica os mapeamentos de identidade Azure no cluster AKS, e o servidor NMI solicita então um sinal de acesso do Azure Ative Directory (AD) com base no mapeamento de identidade do pod. A Azure AD fornece acesso ao servidor NMI, que é devolvido ao casulo. Este token de acesso pode ser usado pelo pod para, em seguida, solicitar acesso a serviços em Azure.

No exemplo seguinte, um desenvolvedor cria um pod que utiliza uma identidade gerida para solicitar acesso à Base de Dados Azure SQL:

![Identidades de pod permitem que um pod solicite automaticamente acesso a outros serviços](media/operator-best-practices-identity/pod-identities.png)

1. O operador do cluster cria primeiro uma conta de serviço que pode ser usada para mapear identidades quando as cápsulas pedem acesso aos serviços.
1. O servidor NMI e mic são implantados para retransmitir quaisquer pedidos de pod para fichas de acesso a Azure AD.
1. Um desenvolvedor implementa um pod com uma identidade gerida que solicita um sinal de acesso através do servidor NMI.
1. O símbolo é devolvido ao casulo e usado para aceder à Base de Dados Azure SQL

> [!NOTE]
> Identidades de pod geridas é um projeto de código aberto, e não é suportado pelo suporte técnico azure.

Para utilizar identidades de pod, consulte identidades do [Diretório Ativo Azure para aplicações Kubernetes][aad-pod-identity].

## <a name="next-steps"></a>Próximos passos

Este artigo de boas práticas focou-se na autenticação e autorização para o seu cluster e recursos. Para implementar algumas destas melhores práticas, consulte os seguintes artigos:

* [Integrar o Diretório Ativo Azure com a AKS][aks-aad]
* [Utilize identidades geridas para recursos Azure com AKS][aad-pod-identity]

Para obter mais informações sobre operações de cluster no AKS, consulte as seguintes boas práticas:

* [Isolamento multi-inquilinos e de clusters][aks-best-practices-cluster-isolation]
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
