---
title: Conceitos-acesso e identidade nos serviços Kubernetess do Azure (AKS)
description: Saiba mais sobre o acesso e a identidade no AKS (serviço de kubernetes do Azure), incluindo integração Azure Active Directory, controle de acesso baseado em função (RBAC) e funções e associações.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: mlearned
ms.openlocfilehash: a1ed1eccd7a10d78cd503559469654e5562cde0c
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "67615867"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Opções de acesso e identidade para o serviço kubernetes do Azure (AKS)

Há diferentes maneiras de se autenticar com o e proteger os clusters kubernetes. Usando o RBAC (controles de acesso baseado em função), você pode conceder a usuários ou grupos acesso apenas aos recursos de que precisam. Com o AKS (serviço kubernetes do Azure), você pode aprimorar ainda mais a estrutura de segurança e permissões usando Azure Active Directory. Essas abordagens ajudam a proteger as cargas de trabalho do aplicativo e os dados do cliente.

Este artigo apresenta os principais conceitos que ajudam você a autenticar e atribuir permissões no AKS:

- [Contas de serviço kubernetes](#kubernetes-service-accounts)
- [Integração do Azure Active Directory](#azure-active-directory-integration)
- [RBAC (controles de acesso baseado em função)](#role-based-access-controls-rbac)
- [Funções e ClusterRoles](#roles-and-clusterroles)
- [RoleBindings e ClusterRoleBindings](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Contas de serviço kubernetes

Um dos tipos de usuário primários no kubernetes é uma *conta de serviço*. Uma conta de serviço existe no e é gerenciada pelo, a API do kubernetes. As credenciais para contas de serviço são armazenadas como segredos kubernetes, o que permite que elas sejam usadas por pods autorizados para se comunicar com o servidor de API. A maioria das solicitações de API fornece um token de autenticação para uma conta de serviço ou uma conta de usuário normal.

As contas de usuário normais permitem acesso mais tradicional a administradores ou desenvolvedores humanos, não apenas a serviços e processos. O kubernetes em si não fornece uma solução de gerenciamento de identidades onde contas de usuário e senhas regulares são armazenadas. Em vez disso, as soluções de identidade externa podem ser integradas ao kubernetes. Para clusters AKS, essa solução de identidade integrada é Azure Active Directory.

Para obter mais informações sobre as opções de identidade no kubernetes, consulte [autenticação do kubernetes][kubernetes-authentication].

## <a name="azure-active-directory-integration"></a>Integração do Azure Active Directory

A segurança dos clusters AKS pode ser aprimorada com a integração do Azure Active Directory (AD). Criado com base em décadas de gerenciamento de identidades corporativas, o Azure AD é um serviço de gerenciamento de identidade e diretório baseado em nuvem multilocatário que combina os principais serviços de diretório, gerenciamento de acesso a aplicativos e proteção de identidade. Com o Azure AD, você pode integrar identidades locais em clusters AKS para fornecer uma única fonte de gerenciamento de conta e segurança.

![Integração de Azure Active Directory com clusters AKS](media/concepts-identity/aad-integration.png)

Com os clusters AKS integrados do Azure AD, você pode conceder aos usuários ou grupos acesso aos recursos do kubernetes em um namespace ou através do cluster. Para obter um `kubectl` contexto de configuração, um usuário pode executar o comando [AZ AKs Get-Credentials][az-aks-get-credentials] . Quando um usuário interage com o cluster AKs com `kubectl`, ele é solicitado a entrar com suas credenciais do Azure AD. Essa abordagem fornece uma única fonte para as credenciais de senha e gerenciamento de conta de usuário. O usuário só pode acessar os recursos conforme definido pelo administrador do cluster.

A autenticação do Azure AD em clusters AKS usa o OpenID Connect, uma camada de identidade criada sobre o protocolo OAuth 2,0. O OAuth 2,0 define mecanismos para obter e usar tokens de acesso para acessar recursos protegidos, e o OpenID Connect implementa a autenticação como uma extensão para o processo de autorização do OAuth 2,0. Para obter mais informações sobre o OpenID Connect, consulte a [documentação do Open ID Connect][openid-connect]. Para verificar os tokens de autenticação obtidos do Azure AD por meio do OpenID Connect, os clusters AKS usam a autenticação de token do webhook kubernetes. Para obter mais informações, consulte a [documentação de autenticação de token][webhook-token-docs]do webhook.

## <a name="role-based-access-controls-rbac"></a>RBAC (controles de acesso baseado em função)

Para fornecer filtragem granular das ações que os usuários podem executar, o kubernetes usa controles de acesso baseado em função (RBAC). Esse mecanismo de controle permite atribuir usuários ou grupos de usuários, permissão para tarefas como criar ou modificar recursos ou exibir logs da execução de cargas de trabalho de aplicativo. Essas permissões podem ser delimitadas para um único namespace ou concedidas em todo o cluster AKS. Com o RBAC kubernetes, você cria *funções* para definir permissões e, em seguida, atribui essas funções a usuários com *associações de função*.

Para obter mais informações, consulte [usando a autorização do RBAC][kubernetes-rbac].

### <a name="azure-role-based-access-controls-rbac"></a>RBAC (controles de acesso baseado em função) do Azure
Um mecanismo adicional para controlar o acesso a recursos é o RBAC (controle de acesso baseado em função) do Azure. O RBAC kubernetes foi projetado para trabalhar em recursos no cluster do AKS e o RBAC do Azure foi projetado para trabalhar em recursos em sua assinatura do Azure. Com o RBAC do Azure, você cria uma *definição de função* que descreve as permissões a serem aplicadas. Um usuário ou grupo recebe essa definição de função para um *escopo*específico, que pode ser um recurso individual, um grupo de recursos ou pela assinatura.

Para obter mais informações, consulte [o que é o RBAC do Azure?][azure-rbac]

## <a name="roles-and-clusterroles"></a>Funções e ClusterRoles

Antes de atribuir permissões a usuários com o RBAC kubernetes, você primeiro define essas permissões como uma *função*. As funções kubernetes concedem permissões. Não há nenhum conceito de permissão *Deny* .

As funções são usadas para conceder permissões em um namespace. Se precisar conceder permissões em todo o cluster ou em recursos de cluster fora de um namespace específico, você poderá usar *ClusterRoles*.

Um ClusterRole funciona da mesma maneira para conceder permissões a recursos, mas pode ser aplicado a recursos em todo o cluster, não em um namespace específico.

## <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings e ClusterRoleBindings

Depois que as funções são definidas para conceder permissões a recursos, você atribui essas permissões de RBACkubernetes com uma rolebinding. Se o cluster AKS se integrar com o Azure Active Directory, as associações serão como esses usuários do Azure AD recebem permissões para executar ações no cluster.

Associações de função são usadas para atribuir funções para um namespace específico. Essa abordagem permite separar logicamente um único cluster AKS, com os usuários capazes de acessar somente os recursos do aplicativo em seu namespace atribuído. Se você precisar associar funções em todo o cluster ou para recursos de cluster fora de um namespace específico, você pode usar *ClusterRoleBindings*.

Um ClusterRoleBinding funciona da mesma maneira para associar funções a usuários, mas pode ser aplicado a recursos em todo o cluster, não em um namespace específico. Essa abordagem permite conceder aos administradores ou engenheiros de suporte acesso a todos os recursos no cluster AKS.

## <a name="next-steps"></a>Passos seguintes

Para começar a usar o Azure AD e o kubernetes RBAC, consulte [integrar Azure Active Directory com AKs][aks-aad].

Para obter as práticas recomendadas associadas, consulte [práticas recomendadas para autenticação e autorização no AKs][operator-best-practices-identity].

Para obter informações adicionais sobre os principais conceitos de kubernetes e AKS, consulte os seguintes artigos:

- [Clusters e cargas de trabalho do kubernetes/AKS][aks-concepts-clusters-workloads]
- [Segurança do kubernetes/AKS][aks-concepts-security]
- [Redes virtuais kubernetes/AKS][aks-concepts-network]
- [Armazenamento kubernetes/AKS][aks-concepts-storage]
- [Escala de kubernetes/AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v1-protocols-openid-connect-code.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: azure-ad-integration-cli.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
