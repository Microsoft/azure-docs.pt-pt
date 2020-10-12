---
title: Melhores práticas para gerir a identidade
titleSuffix: Azure Kubernetes Service
description: Conheça as melhores práticas do operador do cluster para gerir a autenticação e autorização para clusters no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 0e11f345bfed287be3170df38a909ed24149b754
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88010264"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Melhores práticas para autenticação e autorização no Serviço Azure Kubernetes (AKS)

À medida que implementa e mantém clusters no Serviço Azure Kubernetes (AKS), precisa de implementar formas de gerir o acesso a recursos e serviços. Sem estes controlos, as contas podem ter acesso a recursos e serviços de que não necessitam. Também pode ser difícil de controlar que conjunto de credenciais foram usados para fazer alterações.

Este artigo de boas práticas centra-se na forma como um operador de cluster pode gerir o acesso e a identidade para clusters AKS. Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Autenticar utilizadores do cluster AKS com diretório ativo Azure
> * Controlar o acesso aos recursos com o controlo de acesso baseado em funções da Kubernetes (RBAC)
> * Utilize o RBAC Azure para controlar granulivelmente o acesso ao recurso AKS e à API de Kubernetes em escala, bem como ao kubeconfig.
> * Utilize uma identidade gerida para autenticar as próprias cápsulas com outros serviços

## <a name="use-azure-active-directory"></a>Utilizar o Azure Active Directory

**Orientação de boas práticas** - Implementar clusters AKS com integração AD AZure. A utilização do Azure AD centraliza a componente de gestão de identidade. Qualquer alteração na conta de utilizador ou no estado do grupo é automaticamente atualizada no acesso ao cluster AKS. Utilizar Funções ou ClusterRoles e Encadernações, conforme discutido na secção seguinte, para acionar utilizadores ou grupos para o mínimo de permissões necessárias.

Os desenvolvedores e proprietários de aplicações do seu cluster Kubernetes precisam de acesso a diferentes recursos. A Kubernetes não fornece uma solução de gestão de identidade para controlar quais os utilizadores que podem interagir com os recursos. Em vez disso, normalmente integra o seu cluster com uma solução de identidade existente. O Azure Ative Directory (AD) fornece uma solução de gestão de identidade pronta para a empresa e pode integrar-se com clusters AKS.

Com clusters integrados AZURE em AKS, cria *Roles* ou *ClusterRoles* que definem permissões de acesso a recursos. Em seguida, *liga as* funções aos utilizadores ou grupos do Azure AD. Estes kubernetes controle de acesso baseado em funções (RBAC) são discutidos na secção seguinte. A integração do Azure AD e a forma como controla o acesso aos recursos podem ser vistas no seguinte diagrama:

![Autenticação ao nível do cluster para integração do Azure Ative Directory com a AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. O desenvolvedor autentica com Azure AD.
1. O ponto final de emissão de ad Ad Azure emite o token de acesso.
1. O desenvolvedor faz uma ação usando o token AD Azure, como `kubectl create pod`
1. A Kubernetes valida o token com o Azure Ative Directory e adquire os membros do grupo do desenvolvedor.
1. Kubernetes controle de acesso baseado em funções (RBAC) e políticas de cluster são aplicadas.
1. O pedido do desenvolvedor é bem sucedido ou não com base na validação prévia da filiação do grupo AD Azure e das políticas e políticas da Kubernetes RBAC.

Para criar um cluster AKS que utilize Azure AD, consulte [IntegraR o Diretório Ativo Azure com AKS][aks-aad].

## <a name="use-kubernetes-role-based-access-control-rbac"></a>Use o controlo de acesso baseado em funções da Kubernetes (RBAC)

**Orientação de boas práticas** - Use o RBAC de Kubernetes para definir as permissões que os utilizadores ou grupos têm para recursos no cluster. Criar funções e encadernações que atribuam o menor número de permissões necessárias. Integre-se com a Azure AD para que qualquer alteração no estado do utilizador ou da adesão ao grupo seja atualizada automaticamente e o acesso aos recursos do cluster é atual.

Em Kubernetes, você pode fornecer controlo granular de acesso a recursos no cluster. As permissões são definidas ao nível do cluster, ou a espaços de nome específicos. Pode definir que recursos podem ser geridos, e com que permissões. Estas funções são então aplicadas a utilizadores ou grupos com uma ligação. Para obter mais informações sobre *Funções,* *ClusterRoles*e *Bindings,* consulte [opções de acesso e identidade para o Serviço Azure Kubernetes (AKS)][aks-concepts-identity].

Como exemplo, pode criar uma Função que concede acesso total aos recursos no espaço de nome denominado *app financeira,* como mostra o seguinte exemplo YAML manifesto:

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

É então criado um RoleBinding que liga o desenvolvedor de utilizadores AD AZure1 * \@ contoso.com* ao RoleBinding, como mostra o manifesto YAML seguinte:

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

Quando *o developer1 \@ contoso.com* é autenticado contra o cluster AKS, eles têm permissões completas para recursos no espaço *de nomes de aplicativos financeiros.* Desta forma, separa-se logicamente e controla o acesso aos recursos. Kubernetes RBAC deve ser usado em conjunto com a integração AD Azure, como discutido na secção anterior.

Para ver como utilizar grupos AD Azure para controlar o acesso aos recursos de Kubernetes utilizando o RBAC, consulte [o controlo do acesso aos recursos de cluster utilizando o controlo de acesso baseado em funções e identidades do Azure Ative Directory em AKS][azure-ad-rbac].

## <a name="use-azure-rbac"></a>Use Azure RBAC 
**Orientação para as melhores práticas** - Use o Azure RBAC para definir as permissões mínimas exigidas que os utilizadores ou grupos têm para os recursos AKS em uma ou mais subscrições.

Existem dois níveis de acesso necessários para operar plenamente um cluster AKS: 
1. Aceda ao recurso AKS na sua subscrição Azure. Este nível de acesso permite-lhe controlar as coisas que escalam ou melhoram o seu cluster utilizando as APIs AKS, bem como puxar o seu kubeconfig.
Para ver como controlar o acesso ao recurso AKS e ao kubeconfig, consulte [o limite de acesso ao ficheiro de configuração do cluster](control-kubeconfig-access.md).

2. Acesso à API de Kubernetes. Este nível de acesso é controlado quer pela [Kubernetes RBAC](#use-kubernetes-role-based-access-control-rbac) (tradicionalmente) quer pela integração do Azure RBAC com a AKS para autorização de kubernetes.
Para ver como dar granularmente permissões à API de Kubernetes usando Azure RBAC ver [Use Azure RBAC para autorização Kubernetes](manage-azure-rbac.md).

## <a name="use-pod-identities"></a>Use identidades de vagem

**Orientação para as melhores práticas** - Não utilize credenciais fixas dentro de cápsulas ou imagens de contentores, uma vez que estão em risco de exposição ou abuso. Em vez disso, utilize identidades de pod para solicitar automaticamente o acesso utilizando uma solução de identidade AZure AD central. As identidades do pod destinam-se apenas a ser utilizadas apenas com cápsulas Linux e imagens de contentores.

Quando os pods precisam de acesso a outros serviços Azure, tais como Cosmos DB, Key Vault ou Blob Storage, o pod precisa de credenciais de acesso. Estas credenciais de acesso podem ser definidas com a imagem do recipiente ou injetadas como um segredo de Kubernetes, mas precisam de ser criadas e atribuídas manualmente. Muitas vezes, as credenciais são reutilizadas através de cápsulas, e não são regularmente giradas.

Identidades geridas para recursos Azure (atualmente implementadas como um projeto de código aberto AKS associado) permitem solicitar automaticamente o acesso aos serviços através do Azure AD. Não define manualmente credenciais para cápsulas, em vez disso, solicitam um token de acesso em tempo real, e podem usá-lo apenas para aceder apenas aos seus serviços atribuídos. Em AKS, dois componentes são implantados pelo operador do cluster para permitir que as cápsulas utilizem identidades geridas:

* **O servidor Node Management Identity (NMI)** é um pod que funciona como Um DaemonSet em cada nó no cluster AKS. O servidor NMI ouve pedidos de pod para os serviços Azure.
* **O Controlador de Identidade Gerido (MIC)** é um pod central com permissões para consultar o servidor API de Kubernetes e verifica um mapeamento de identidade Azure que corresponde a uma cápsula.

Quando os pods solicitam o acesso a um serviço Azure, as regras de rede redirecionam o tráfego para o servidor Node Management Identity (NMI). O servidor NMI identifica cápsulas que solicitam o acesso aos serviços Azure com base no seu endereço remoto, e consulta o Controlador de Identidade Gerido (MIC). O MIC verifica os mapeamentos de identidade Azure no cluster AKS, e o servidor NMI solicita então um token de acesso a partir do Azure Ative Directory (AD) com base no mapeamento de identidade da cápsula. A Azure AD fornece acesso ao servidor NMI, que é devolvido à cápsula. Este token de acesso pode ser usado pela cápsula para, em seguida, solicitar acesso aos serviços em Azure.

No exemplo seguinte, um desenvolvedor cria um pod que usa uma identidade gerida para solicitar o acesso à Base de Dados Azure SQL:

![Identidades do Pod permitem que um casulo solicite automaticamente acesso a outros serviços](media/operator-best-practices-identity/pod-identities.png)

1. O operador de cluster cria primeiro uma conta de serviço que pode ser usada para mapear identidades quando as cápsulas pedem acesso aos serviços.
1. O servidor NMI e o MIC são implantados para transmitir quaisquer pedidos de pod para fichas de acesso ao Azure AD.
1. Um desenvolvedor implementa um pod com uma identidade gerida que solicita um token de acesso através do servidor NMI.
1. O token é devolvido à cápsula e usado para aceder à Base de Dados Azure SQL

> [!NOTE]
> As identidades geridas do pod são um projeto de código aberto, e não é suportado pelo suporte técnico da Azure.

Para utilizar identidades de vagem, consulte [as identidades do Azure Ative Directory para aplicações kubernetes][aad-pod-identity].

## <a name="next-steps"></a>Passos seguintes

Este artigo de boas práticas focado na autenticação e autorização para o seu cluster e recursos. Para implementar algumas destas boas práticas, consulte os seguintes artigos:

* [Integre o Azure Ative Directy com a AKS][aks-aad]
* [Utilize identidades geridas para recursos Azure com AKS][aad-pod-identity]

Para obter mais informações sobre as operações de cluster em AKS, consulte as seguintes boas práticas:

* [Isolamento multi-inquilinos e de clusters][aks-best-practices-cluster-isolation]
* [Funcionalidades básicas do programador Kubernetes][aks-best-practices-scheduler]
* [Recursos avançados do programador Kubernetes][aks-best-practices-advanced-scheduler]

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
