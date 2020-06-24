---
title: Conceitos - Acesso e identidade nos Serviços Azure Kubernetes (AKS)
description: Saiba mais sobre o acesso e identidade no Serviço Azure Kubernetes (AKS), incluindo a integração do Azure Ative Directory, o controlo de acesso baseado em funções da Kubernetes (RBAC), e papéis e encadernações.
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: e4945535417f7d8d33308121267ba97e1f835e13
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84690426"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Access and identity options for Azure Kubernetes Service (AKS) (Opções de acesso e de identidade do Azure Kubernetes Service (AKS))

Existem diferentes formas de autenticar e proteger os clusters kubernetes. Utilizando controlos de acesso baseados em funções (RBAC), pode conceder aos utilizadores ou grupos acesso apenas aos recursos de que necessitam. Com o Serviço Azure Kubernetes (AKS), pode melhorar ainda mais a estrutura de segurança e permissões utilizando o Azure Ative Directory. Estas abordagens ajudam-no a proteger as cargas de trabalho da sua aplicação e os dados do cliente.

Este artigo introduz os conceitos fundamentais que o ajudam a autenticar e atribuir permissões em AKS:

- [Contas de serviço kubernetes](#kubernetes-service-accounts)
- [Integração do Azure Active Directory](#azure-active-directory-integration)
- [Controlos de acesso baseados em funções (RBAC)](#role-based-access-controls-rbac)
- [Funções e ClusterRoles](#roles-and-clusterroles)
- [RoleBindings e ClusterRoleBindings](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Contas de serviço kubernetes

Um dos principais tipos de utilizadores em Kubernetes é uma *conta de serviço.* Existe uma conta de serviço na API de Kubernetes. As credenciais para contas de serviço são armazenadas como segredos de Kubernetes, o que permite que sejam usadas por cápsulas autorizadas para comunicar com o Servidor API. A maioria dos pedidos da API fornecem um símbolo de autenticação para uma conta de serviço ou uma conta de utilizador normal.

As contas normais de utilizador permitem um acesso mais tradicional a administradores ou desenvolvedores humanos, e não apenas serviços e processos. A Kubernetes em si não fornece uma solução de gestão de identidade onde contas de utilizador regulares e palavras-passe são armazenadas. Em vez disso, as soluções de identidade externa podem ser integradas em Kubernetes. Para os clusters AKS, esta solução de identidade integrada é o Azure Ative Directory.

Para obter mais informações sobre as opções de identidade em Kubernetes, consulte [a autenticação de Kubernetes.][kubernetes-authentication]

## <a name="azure-active-directory-integration"></a>Integração do Azure Active Directory

A segurança dos clusters AKS pode ser reforçada com a integração do Azure Ative Directory (AD). Construído com décadas de gestão de identidade empresarial, a Azure AD é um serviço multi-inquilino, com base na nuvem e serviço de gestão de identidade que combina serviços de diretório principal, gestão de acesso a aplicações e proteção de identidade. Com o Azure AD, pode integrar identidades no local em clusters AKS para fornecer uma única fonte de gestão de conta e segurança.

![Integração do Azure Ative Directory com clusters AKS](media/concepts-identity/aad-integration.png)

Com clusters AKS integrados em Azure, pode conceder aos utilizadores ou grupos acesso aos recursos de Kubernetes dentro de um espaço de nome ou em todo o cluster. Para obter um `kubectl` contexto de configuração, um utilizador pode executar o comando [az aks get-credentials.][az-aks-get-credentials] Quando um utilizador interage com o cluster AKS, `kubectl` é-lhes solicitado que assinem com as suas credenciais AD AZure. Esta abordagem fornece uma única fonte para a gestão da conta de utilizador e credenciais de senha. O utilizador só pode aceder aos recursos definidos pelo administrador do cluster.

A autenticação AD AD em clusters AKS utiliza OpenID Connect, uma camada de identidade construída em cima do protocolo OAuth 2.0. O OAuth 2.0 define mecanismos para obter e utilizar fichas de acesso para aceder a recursos protegidos, e o OpenID Connect implementa a autenticação como extensão ao processo de autorização OAuth 2.0. Para obter mais informações sobre o OpenID Connect, consulte a [documentação Open ID Connect][openid-connect]. Para verificar os tokens de autenticação obtidos a partir de Azure AD através do OpenID Connect, os clusters AKS utilizam a autenticação token de Kubernetes Webhook. Para obter mais informações, consulte a [documentação de autenticação token webhook][webhook-token-docs].

## <a name="role-based-access-controls-rbac"></a>Controlos de acesso baseados em funções (RBAC)

Para fornecer filtragem granular das ações que os utilizadores podem executar, a Kubernetes utiliza controlos de acesso baseados em funções (RBAC). Este mecanismo de controlo permite atribuir aos utilizadores, ou grupos de utilizadores, permissão para fazer coisas como criar ou modificar recursos, ou ver registos de cargas de trabalho de aplicações. Estas permissões podem ser miradas para um único espaço de nome, ou concedidas em todo o cluster AKS. Com o KUBernetes RBAC, *cria-se funções* para definir permissões e, em seguida, atribui-las a utilizadores com *encadernações de papéis*.

Para obter mais informações, consulte [a autorização do RBAC.][kubernetes-rbac]

### <a name="azure-role-based-access-controls-rbac"></a>Controlos de acesso baseados em funções Azure (RBAC)
Um mecanismo adicional para controlar o acesso aos recursos é os controlos de acesso baseados em funções Azure (RBAC). Kubernetes RBAC é projetado para trabalhar em recursos dentro do seu cluster AKS, e Azure RBAC é projetado para trabalhar em recursos dentro da sua subscrição Azure. Com o Azure RBAC, cria-se uma *definição de papel* que descreve as permissões a aplicar. Um utilizador ou grupo é então atribuído esta definição de função para um *determinado âmbito*, que poderia ser um recurso individual, um grupo de recursos, ou em toda a subscrição.

Para mais informações, veja [o que é Azure RBAC?][azure-rbac]

## <a name="roles-and-clusterroles"></a>Funções e ClusterRoles

Antes de atribuir permissões a utilizadores com O RBAC de Kubernetes, primeiro define essas permissões como *um Papel*. Os papéis de Kubernetes *concedem* permissões. Não há nenhum conceito de permissão *de negação.*

As funções são usadas para conceder permissões dentro de um espaço de nome. Se precisar de conceder permissões em todo o cluster, ou para agrupar recursos fora de um determinado espaço de nome, pode, em vez disso, utilizar *clusterRoles.*

Um ClusterRole trabalha da mesma forma para conceder permissões aos recursos, mas pode ser aplicado a recursos em todo o cluster, e não um espaço de nome específico.

## <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings e ClusterRoleBindings

Uma vez definidas as funções para conceder permissões aos recursos, atribua essas permissões de NCC de Kubernetes com um *RoleBinding*. Se o seu cluster AKS se integrar com o Azure Ative Directory, as ligações são como esses utilizadores AZure AD recebem permissões para executar ações dentro do cluster.

As ligações de funções são usadas para atribuir papéis para um determinado espaço de nome. Esta abordagem permite-lhe, logicamente, segregar um único cluster AKS, com os utilizadores apenas capazes de aceder aos recursos da aplicação no seu espaço de nome atribuído. Se precisar de ligar funções em todo o cluster, ou de agrupar recursos fora de um determinado espaço de nome, pode, em vez disso, utilizar *ClusterRoleBindings*.

Um ClusterRoleBinding funciona da mesma forma para ligar funções aos utilizadores, mas pode ser aplicado a recursos em todo o cluster, e não num espaço de nome específico. Esta abordagem permite conceder aos administradores ou engenheiros de apoio acesso a todos os recursos do cluster AKS.

## <a name="next-steps"></a>Passos seguintes

Para começar com Azure AD e Kubernetes RBAC, consulte [Integrate Azure Ative Directory com AKS][aks-aad].

Para as melhores práticas associadas, consulte [as melhores práticas de autenticação e autorização em AKS][operator-best-practices-identity].

Para obter informações adicionais sobre os conceitos core Kubernetes e AKS, consulte os seguintes artigos:

- [Aglomerados de Kubernetes / AKS e cargas de trabalho][aks-concepts-clusters-workloads]
- [Segurança Kubernetes / AKS][aks-concepts-security]
- [Redes virtuais Kubernetes / AKS][aks-concepts-network]
- [Armazenamento Kubernetes / AKS][aks-concepts-storage]
- [Escala de Kubernetes / AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: azure-ad-integration-cli.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
