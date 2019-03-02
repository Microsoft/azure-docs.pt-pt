---
title: Conceitos - acesso e identidade dos serviços de Kubernetes no Azure (AKS)
description: Saiba mais sobre o acesso e identidade no Azure Kubernetes Service (AKS), incluindo a integração do Active Directory do Azure, Kubernetes controlo de acesso baseado em funções (RBAC) e funções e associações.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: 64150438560d48575b4b65db4cdaf61aa0ada240
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243836"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Opções de acesso e identidade para o Azure Kubernetes Service (AKS)

Existem diferentes formas de autenticar com e proteger clusters de Kubernetes. Utilizar controlos de acesso baseado em funções (RBAC), pode conceder a utilizadores ou o acesso de grupos para apenas os recursos que necessitam. Com o Azure Kubernetes Service (AKS), pode melhorar ainda mais a estrutura de segurança e permissões ao utilizar o Azure Active Directory. Essas abordagens ajudam a proteger as cargas de trabalho de aplicação e os dados dos clientes.

Este artigo apresenta as principais conceitos que o ajudam a autenticar e atribuir permissões no AKS:

- [Contas de serviço do Kubernetes](#kubernetes-service-accounts)
- [Integração do Active Directory do Azure](#azure-active-directory-integration)
- [Controlos de acesso baseado em funções (RBAC)](#role-based-access-controls-rbac)
- [Funções e ClusterRoles](#roles-and-clusterroles)
- [RoleBindings e ClusterRoleBindings](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Contas de serviço do Kubernetes

Um dos tipos de utilizador primário no Kubernetes é uma *conta de serviço*. Uma conta de serviço existe no e é gerenciada pelo, a API do Kubernetes. As credenciais para contas de serviço são armazenadas como segredos do Kubernetes, que permite que eles sejam utilizados pelo pods autorizados para comunicar com o servidor de API. A maioria dos pedidos de API fornecem um token de autenticação para uma conta de serviço ou uma conta de utilizador normal.

Contas de usuários normais permitem o acesso mais tradicional para administradores humanos ou programadores, não apenas serviços e processos. Kubernetes em si não fornece uma solução de gerenciamento de identidades em que as contas de utilizador normal e as palavras-passe são armazenadas. Em vez disso, as soluções de identidade externo podem ser integradas no Kubernetes. Para os clusters do AKS, essa solução de identidades integrada é o Azure Active Directory.

Para obter mais informações sobre as opções de identidade no Kubernetes, consulte [autenticação de Kubernetes][kubernetes-authentication].

## <a name="azure-active-directory-integration"></a>Integração do Azure Active Directory

Pode ser aprimorada a segurança dos clusters do AKS com a integração do Azure Active Directory (AD). Baseia-se em décadas de gestão de identidades da empresa, o Azure AD é um diretório de multi-inquilino, com base na cloud e o serviço de gestão de identidades que combina serviços de diretório centrais, gestão de acesso de aplicações e proteção de identidade. Com o Azure AD, pode integrar identidades no local em clusters do AKS para fornecer uma única origem para gestão de contas e segurança.

![Integração do Active Directory do Azure com clusters do AKS](media/concepts-identity/aad-integration.png)

Com os clusters do AKS integrada no AD do Azure, pode conceder a utilizadores ou o acesso de grupos de recursos dentro de um espaço de nomes ou entre o cluster do Kubernetes. Para obter um `kubectl` contexto de configuração, um usuário pode executar o [az aks get-credentials] [ az-aks-get-credentials] comando. Quando um utilizador, em seguida, interage com o cluster do AKS com `kubectl`, são-lhes pedido que inicie sessão com as credenciais do Azure AD. Esta abordagem fornece uma única origem para gestão de contas de utilizador e as credenciais de palavra-passe. O utilizador só pode aceder os recursos, conforme definido pelo administrador do cluster.

Autenticação do Azure AD em clusters do AKS usa o OpenID Connect, uma camada de identidade criada com base no protocolo de OAuth 2.0. OAuth 2.0 define os mecanismos de obter e utilizar tokens de acesso para aceder a recursos protegidos e OpenID Connect implementa a autenticação como uma extensão para o processo de autorização de OAuth 2.0. Para obter mais informações sobre o OpenID Connect, consulte a [documentação abrir ID Connect][openid-connect]. Para verificar os tokens de autenticação obtido a partir do Azure AD através do OpenID Connect, os clusters do AKS utilizam autenticação de Token do Webhook de Kubernetes. Para obter mais informações, consulte a [documentação de autenticação de Token do Webhook][webhook-token-docs].

## <a name="role-based-access-controls-rbac"></a>Controlos de acesso baseado em funções (RBAC)

Para fornecer a filtragem granular das ações que os utilizadores podem executar, o Kubernetes utiliza controlos de acesso baseado em funções (RBAC). Esse mecanismo de controlo permite-lhe atribuir utilizadores ou grupos de utilizadores, permissão para fazer coisas como criar ou modificar os recursos ou ver registos de executar cargas de trabalho de aplicação. Estas permissões podem ser confinadas a um único espaço de nomes ou concedidas em todo o cluster do AKS. Com o RBAC do Kubernetes, crie *funções* para definir as permissões e, em seguida, atribuir essas funções a utilizadores com *enlaces de função*.

Para obter mais informações, consulte [autorização RBAC usando][kubernetes-rbac].

### <a name="azure-role-based-access-controls-rbac"></a>Controlos de acesso baseado em função do Azure (RBAC)
Um mecanismo adicional para controlar o acesso aos recursos é controles de acesso baseado em função do Azure (RBAC). RBAC do Kubernetes foi concebida para funcionar em recursos no seu cluster do AKS e RBAC do Azure foi concebida para funcionar em recursos na sua subscrição do Azure. Com o Azure RBAC, pode criar uma *definição de função* que descreve as permissões sejam aplicadas. Um utilizador ou grupo, em seguida, é atribuído esta definição de função para um determinado *âmbito*, que pode ser um recurso individual, um recurso, grupo, ou entre a subscrição.

Para obter mais informações, consulte [o que é o RBAC do Azure?][azure-rbac]

## <a name="roles-and-clusterroles"></a>Funções e ClusterRoles

Antes de atribuir permissões a utilizadores com RBAC do Kubernetes, primeiro defina essas permissões como uma *função*. Funções de Kubernetes *conceder* permissões. Não há nenhum conceito de um *negar* permissão.

Funções são utilizadas para conceder permissões num namespace. Se tem de conceder permissões em todo o cluster, ou a recursos de cluster fora do espaço de nomes especificado, em vez disso, pode utilizar *ClusterRoles*.

Um ClusterRole funciona da mesma forma para conceder permissões para recursos, mas pode ser aplicada a recursos no cluster inteiro, não um espaço de nomes específico.

## <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings e ClusterRoleBindings

Depois das funções são definidas para conceder permissões para recursos, atribuir essas permissões RBAC do Kubernetes com um *RoleBinding*. Se o seu cluster do AKS integra-se com o Azure Active Directory, enlaces são como os utilizadores do Azure AD são concedidos permissões para efetuar ações dentro do cluster.

Os enlaces de função são utilizados para atribuir funções para um espaço de nomes especificado. Esta abordagem permite-lhe separar logicamente um único cluster do AKS, com os utilizadores só possam acessar os recursos de aplicação no seu espaço de nomes atribuído. Se precisa vincular funções no cluster inteiro ou aos recursos do cluster fora do espaço de nomes especificado, em vez disso, pode utilizar *ClusterRoleBindings*.

Um ClusterRoleBinding funciona da mesma forma para vincular funções a utilizadores, mas pode ser aplicada a recursos no cluster inteiro, não um espaço de nomes específico. Esta abordagem permite-lhe conceder aos administradores ou suportar o acesso a engenheiros a todos os recursos do cluster do AKS.

## <a name="next-steps"></a>Passos Seguintes

Para começar a utilizar com o Azure AD e RBAC do Kubernetes, veja [integrar o Azure Active Directory com o AKS][aks-aad].

Para as práticas recomendadas associadas, consulte [melhores práticas para autenticação e autorização no AKS][operator-best-practices-identity].

Para obter mais informações sobre principais Kubernetes e conceitos do AKS, consulte os artigos seguintes:

- [Kubernetes / clusters do AKS e cargas de trabalho][aks-concepts-clusters-workloads]
- [Kubernetes / segurança AKS][aks-concepts-security]
- [Kubernetes / redes virtuais do AKS][aks-concepts-network]
- [Kubernetes / armazenamento AKS][aks-concepts-storage]
- [Kubernetes / aumentar do AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v1-protocols-openid-connect-code.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: aad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
