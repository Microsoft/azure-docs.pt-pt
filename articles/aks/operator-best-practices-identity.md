---
title: Melhores práticas para gerir a identidade
titleSuffix: Azure Kubernetes Service
description: Conheça as melhores práticas do operador do cluster para gerir a autenticação e autorização para clusters no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: jpalma
author: palma21
ms.openlocfilehash: de84e3e2a8da3e1b5195978a8a2204fdfa2108d7
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105107"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Melhores práticas para autenticação e autorização no Serviço Azure Kubernetes (AKS)

À medida que implementa e mantém clusters no Serviço Azure Kubernetes (AKS), implementa formas de gerir o acesso a recursos e serviços. Sem estes controlos:
* As contas podem ter acesso a recursos e serviços desnecessários. 
* O rastreio de que conjunto de credenciais foram utilizados para fazer alterações pode ser difícil.

Este artigo de boas práticas centra-se na forma como um operador de cluster pode gerir o acesso e a identidade para clusters AKS. Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Autenticar utilizadores do cluster AKS com diretório Azure Ative.
> * Controlar o acesso a recursos com o controlo de acesso baseado em funções da Kubernetes (Kubernetes RBAC).
> * Utilize o RBAC Azure para controlar granularmente o acesso ao recurso AKS, à API de Kubernetes em escala, e ao `kubeconfig` .
> * Utilize uma identidade gerida para autenticar as próprias cápsulas com outros serviços.

## <a name="use-azure-active-directory-azure-ad"></a>Use diretório ativo Azure (Azure AD)

> **Orientação de melhor prática** 
> 
> Implementar clusters AKS com integração AD Azure. A utilização do Azure AD centraliza a componente de gestão de identidade. Qualquer alteração na conta de utilizador ou no estado do grupo é automaticamente atualizada no acesso ao cluster AKS. Os utilizadores de âmbito ou grupos até ao mínimo de permissões equivalem a [funções, ClusterRoles ou Encadernações](#use-kubernetes-role-based-access-control-kubernetes-rbac).

Os desenvolvedores de clusters Kubernetes e os proprietários de aplicações precisam de acesso a diferentes recursos. A Kubernetes carece de uma solução de gestão de identidade para controlar os recursos com os quais os utilizadores podem interagir. Em vez disso, normalmente integra o seu cluster com uma solução de identidade existente. Enter Azure AD: uma solução de gestão de identidade pronta para a empresa que se integra com clusters AKS.

Com clusters integrados AZure em AKS, cria *Roles* ou *ClusterRoles* definindo permissões de acesso a recursos. Em seguida, *liga as* funções aos utilizadores ou grupos do Azure AD. Saiba mais sobre estes Kubernetes RBAC [na secção seguinte](#use-kubernetes-role-based-access-control-kubernetes-rbac). A integração AD do AD e como controla o acesso aos recursos pode ser visto no seguinte diagrama:

![Autenticação ao nível do cluster para integração do Azure Ative Directory com a AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. O desenvolvedor autentica com Azure AD.
1. O ponto final de emissão de ad Ad Azure emite o token de acesso.
1. O desenvolvedor executa uma ação usando o token AD Azure, como `kubectl create pod` .
1. Kubernetes valida o token com Azure AD e vai buscar os membros do grupo do desenvolvedor.
1. As políticas de kubernetes RBAC e cluster são aplicadas.
1. O pedido do desenvolvedor é bem sucedido com base na validação anterior da adesão ao grupo AZure AD e da Kubernetes RBAC e políticas.

Para criar um cluster AKS que utilize Azure AD, consulte [IntegraR o Diretório Ativo Azure com AKS][aks-aad].

## <a name="use-kubernetes-role-based-access-control-kubernetes-rbac"></a>Use o controlo de acesso baseado em funções da Kubernetes (Kubernetes RBAC)

> **Orientação de melhor prática**
> 
> Defina permissões de utilizador ou grupo para agrupar recursos com o RBAC de Kubernetes. Criar funções e encadernações que atribuam o menor número de permissões necessárias. Integre-se com a Azure AD para atualizar automaticamente qualquer estado de utilizador ou alteração de membro do grupo e manter o acesso aos recursos de cluster atual.

Em Kubernetes, você fornece controlo de acesso granular aos recursos de cluster. Você define permissões ao nível do cluster, ou para espaços de nome específicos. Você determina que recursos podem ser geridos e com que permissões. Em seguida, aplica estas funções a utilizadores ou grupos com uma ligação. Para obter mais informações sobre *Funções,* *ClusterRoles* e *Bindings,* consulte [opções de acesso e identidade para o Serviço Azure Kubernetes (AKS)][aks-concepts-identity].

Como exemplo, cria-se uma função com acesso total aos recursos no espaço de nome denominado *app financeira,* como mostra o seguinte exemplo yaml manifesto:

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

Em seguida, cria um RoleBinding e liga o desenvolvedor de utilizadores AD AZure1 *\@ contoso.com* à RoleBinding, como mostra o manifesto YAML seguinte:

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

Quando *o developer1 \@ contoso.com* é autenticado contra o cluster AKS, eles têm permissões completas para recursos no espaço *de nomes de aplicativos financeiros.* Desta forma, separa-se logicamente e controla o acesso aos recursos. Use o RBAC de Kubernetes em conjunto com a integração AD do Azure.

Para ver como usar grupos AD AZure para controlar o acesso aos recursos de Kubernetes usando o RBAC de Kubernetes, consulte [o Control access to cluster resources usando o controlo de acesso baseado em funções e identidades do Azure Ative Directory em AKS][azure-ad-rbac].

## <a name="use-azure-rbac"></a>Use Azure RBAC 
> **Orientação de melhor prática** 
> 
> Utilize o Azure RBAC para definir as permissões mínimas de utilizador e grupo exigidas aos recursos AKS em uma ou mais subscrições.

Existem dois níveis de acesso necessários para operar plenamente um cluster AKS: 
1. Aceda ao recurso AKS na sua subscrição Azure. 

    Este nível de acesso permite-lhe:
      * Controlo de dimensionamento ou atualização do seu cluster utilizando as APIs AKS
      * Puxe o `kubeconfig` seu.

    Para ver como controlar o acesso ao recurso AKS e ao , consulte limite de `kubeconfig` acesso ao ficheiro de [configuração do cluster](control-kubeconfig-access.md).

2. Acesso à API de Kubernetes. 
    
    Este nível de acesso é controlado quer por:
    * [Kubernetes RBAC](#use-kubernetes-role-based-access-control-kubernetes-rbac) (tradicionalmente) ou 
    * Integrando o Azure RBAC com a AKS para a autorização de kubernetes.

    Para ver como dar granularmente permissões à API de Kubernetes usando Azure RBAC, consulte [Use Azure RBAC para autorização kubernetes](manage-azure-rbac.md).

## <a name="use-pod-managed-identities"></a>Use identidades geridas por Pod

> **Orientação de melhor prática** 
> 
> Não utilize credenciais fixas dentro de cápsulas ou imagens de contentores, uma vez que estão em risco de exposição ou abuso. Em vez disso, utilize *identidades de pod* para solicitar automaticamente o acesso utilizando uma solução de identidade AZure AD central. 

> [!NOTE]
> As identidades do pod destinam-se apenas a ser utilizadas apenas com cápsulas Linux e imagens de contentores. O suporte de identidades geridas por pod para contentores windows está para breve.

Para aceder a outros serviços Azure, como Cosmos DB, Key Vault ou Blob Storage, o pod precisa de credenciais de acesso. Pode definir credenciais de acesso com a imagem do recipiente ou injetá-las como um segredo de Kubernetes. De qualquer forma, teria de os criar manualmente e atribuí-los. Normalmente, estas credenciais são reutilizadas através de cápsulas e não são regularmente giradas.

Com identidades geridas por cápsulas para recursos Azure, você automaticamente solicitar acesso aos serviços através do Azure AD. As identidades geridas pelo Pod estão atualmente em pré-visualização para a AKS. Consulte as identidades geridas pelo Azure Kubernetes no Serviço Azure Kubernetes (Preview)[documentação https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity) a iniciar. 

Em vez de definir manualmente credenciais para cápsulas, as identidades geridas por pods solicitam um token de acesso em tempo real, usando-o apenas para aceder aos seus serviços atribuídos. Na AKS, existem dois componentes que lidam com as operações para permitir que as cápsulas utilizem identidades geridas:

* **O servidor Node Management Identity (NMI)** é um pod que funciona como Um DaemonSet em cada nó no cluster AKS. O servidor NMI ouve pedidos de pod para os serviços Azure.
* **O Fornecedor de Recursos Azure** consulta o servidor API de Kubernetes e verifica um mapeamento de identidade Azure que corresponde a uma cápsula.

Quando as cápsulas solicitam o acesso a um serviço Azure, as regras de rede redirecionam o tráfego para o servidor NMI. 
1. O servidor NMI:
    * Identifica cápsulas que solicitam acesso aos serviços Azure com base no seu endereço remoto.
    * Consultas ao Fornecedor de Recursos Azure. 
1. O Fornecedor de Recursos Azure verifica os mapeamentos de identidade Azure no cluster AKS.
1. O servidor NMI solicita um token de acesso a partir do Azure AD com base no mapeamento de identidade da cápsula. 
1. A Azure AD fornece acesso ao servidor NMI, que é devolvido à cápsula. 
    * Este token de acesso pode ser usado pela cápsula para, em seguida, solicitar acesso aos serviços em Azure.

No exemplo seguinte, um desenvolvedor cria um pod que usa uma identidade gerida para solicitar o acesso à Base de Dados Azure SQL:

![Identidades do Pod permitem que um casulo solicite automaticamente acesso a outros serviços](media/operator-best-practices-identity/pod-identities.png)

1. O operador do cluster cria uma conta de serviço para mapear identidades quando as cápsulas solicitam o acesso aos serviços.
1. O servidor NMI é implantado para retransmitir quaisquer pedidos de pod, juntamente com o Fornecedor de Recursos Azure, para acesso a Azure AD.
1. Um desenvolvedor implementa um pod com uma identidade gerida que solicita um token de acesso através do servidor NMI.
1. O token é devolvido à cápsula e usado para aceder à Base de Dados Azure SQL

> [!NOTE]
> As identidades geridas pelo Pod estão atualmente em estado de pré-visualização.

Para utilizar identidades geridas pelo Pod, consulte [use identidades geridas pelo Azure Ative Directory no Serviço Azure Kubernetes (Preview)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity).

## <a name="next-steps"></a>Passos seguintes

Este artigo de boas práticas focado na autenticação e autorização para o seu cluster e recursos. Para implementar algumas destas boas práticas, consulte os seguintes artigos:

* [Integre o Azure Ative Directy com a AKS][aks-aad]
* [Use identidades geridas por cápsulas Azure Ative pod no Serviço Azure Kubernetes (Pré-visualização)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)

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
