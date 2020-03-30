---
title: Conceitos - Acesso e identidade nos Serviços Azure Kubernetes (AKS)
description: Conheça o acesso e identidade no Serviço Azure Kubernetes (AKS), incluindo integração de Diretórios Ativos Azure, controlo de acesso baseado em papéis kubernetes (RBAC), e papéis e encadernações.
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: e4945535417f7d8d33308121267ba97e1f835e13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259607"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Access and identity options for Azure Kubernetes Service (AKS) (Opções de acesso e de identidade do Azure Kubernetes Service (AKS))

Existem diferentes formas de autenticar e proteger os aglomerados kubernetes. Utilizando controlos de acesso baseados em funções (RBAC), pode conceder aos utilizadores ou grupos o acesso apenas aos recursos de que necessitam. Com o Serviço Azure Kubernetes (AKS), pode aumentar ainda mais a estrutura de segurança e permissões utilizando o Diretório Ativo Azure. Estas abordagens ajudam-no a garantir as cargas de trabalho da sua aplicação e os dados do cliente.

Este artigo introduz os conceitos fundamentais que o ajudam a autenticar e atribuir permissões no AKS:

- [Contas de serviço kubernetes](#kubernetes-service-accounts)
- [Integração do Azure Active Directory](#azure-active-directory-integration)
- [Controlos de acesso baseados em funções (RBAC)](#role-based-access-controls-rbac)
- [Papéis e ClusterRoles](#roles-and-clusterroles)
- [RoleBindings e ClusterRoleBindings](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Contas de serviço kubernetes

Um dos principais tipos de utilizadores em Kubernetes é uma *conta de serviço.* Existe uma conta de serviço na API kubernetes. As credenciais para contas de serviço são armazenadas como segredos kubernetes, o que permite que sejam usadas por pods autorizados para comunicar com o Servidor API. A maioria dos pedidos da API fornecem um símbolo de autenticação para uma conta de serviço ou uma conta de utilizador normal.

As contas normais de utilizador permitem um acesso mais tradicional a administradores ou desenvolvedores humanos, e não apenas a serviços e processos. A própria Kubernetes não fornece uma solução de gestão de identidade onde as contas regulares de utilizadores e as palavras-passe são armazenadas. Em vez disso, as soluções de identidade externa podem ser integradas em Kubernetes. Para os clusters AKS, esta solução de identidade integrada é o Azure Ative Directory.

Para obter mais informações sobre as opções de identidade em Kubernetes, consulte a [autenticação kubernetes][kubernetes-authentication].

## <a name="azure-active-directory-integration"></a>Integração do Azure Active Directory

A segurança dos clusters AKS pode ser reforçada com a integração do Azure Ative Directory (AD). Baseado em décadas de gestão de identidade empresarial, a Azure AD é um serviço multi-inquilino, baseado na nuvem e serviço de gestão de identidade que combina serviços de diretório principal, gestão de acesso a aplicações e proteção de identidade. Com a Azure AD, pode integrar identidades no local em clusters AKS para fornecer uma única fonte de gestão e segurança de contas.

![Integração do Diretório Ativo Azure com clusters AKS](media/concepts-identity/aad-integration.png)

Com clusters AKS integrados em Azure AD, pode conceder aos utilizadores ou grupos acesso aos recursos kubernetes dentro de um espaço de nome ou em todo o cluster. Para obter `kubectl` um contexto de configuração, um utilizador pode executar o comando [az aks get-credentials.][az-aks-get-credentials] Quando um utilizador interage com o `kubectl`cluster AKS com, é solicitado a assinar com as suas credenciais De AD Azure. Esta abordagem fornece uma única fonte para a gestão da conta de utilizador e credenciais de senha. O utilizador só pode aceder aos recursos definidos pelo administrador do cluster.

A autenticação Azure AD em clusters AKS utiliza o OpenID Connect, uma camada de identidade construída em cima do protocolo OAuth 2.0. O OAuth 2.0 define mecanismos para obter e usar fichas de acesso para aceder a recursos protegidos, e o OpenID Connect implementa a autenticação como uma extensão ao processo de autorização OAuth 2.0. Para mais informações sobre o OpenID Connect, consulte a [documentação Open ID Connect][openid-connect]. Para verificar as fichas de autenticação obtidas a partir de Azure AD através do OpenID Connect, os clusters AKS utilizam a autenticação de token de Webhook Kubernetes. Para mais informações, consulte a documentação de [autenticação token webhook][webhook-token-docs].

## <a name="role-based-access-controls-rbac"></a>Controlos de acesso baseados em funções (RBAC)

Para fornecer filtragem granular das ações que os utilizadores podem executar, a Kubernetes utiliza controlos de acesso baseados em funções (RBAC). Este mecanismo de controlo permite-lhe atribuir aos utilizadores, ou grupos de utilizadores, permissão para fazer coisas como criar ou modificar recursos, ou visualizar registos de trabalhos de aplicação em execução. Estas permissões podem ser consultadas para um único espaço de nome, ou concedidas em todo o cluster AKS. Com o Kubernetes RBAC, cria *funções* para definir permissões e, em seguida, atribui essas funções aos utilizadores com *encadernações*de funções .

Para mais informações, consulte [a utilização da autorização RBAC][kubernetes-rbac].

### <a name="azure-role-based-access-controls-rbac"></a>Controlos de acesso baseados em funções azure (RBAC)
Um mecanismo adicional para controlar o acesso aos recursos são os controlos de acesso baseados em funções do Azure (RBAC). O Kubernetes RBAC foi projetado para trabalhar em recursos dentro do seu cluster AKS, e o Azure RBAC foi projetado para trabalhar em recursos dentro da sua subscrição Azure. Com o Azure RBAC, cria-se uma *definição* de papel que descreve as permissões a aplicar. Um utilizador ou grupo é então atribuído a esta definição de função para um *âmbito*específico , que pode ser um recurso individual, um grupo de recursos ou em toda a subscrição.

Para mais informações, consulte [o que é Azure RBAC?][azure-rbac]

## <a name="roles-and-clusterroles"></a>Papéis e ClusterRoles

Antes de atribuir permissões aos utilizadores com Kubernetes RBAC, primeiro define essas permissões como *Uma Função*. As funções de Kubernetes *concedem* permissões. Não há noção de *permissão de negação.*

Os papéis são usados para conceder permissões dentro de um espaço de nome. Se precisar de conceder permissões em todo o cluster, ou para agrupar recursos fora de um determinado espaço de nome, pode utilizar *clusterRoles*.

Um ClusterRole funciona da mesma forma para conceder permissões aos recursos, mas pode ser aplicado a recursos em todo o cluster, e não um espaço de nome específico.

## <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings e ClusterRoleBindings

Uma vez definidas as funções para conceder permissões aos recursos, você atribui essas permissões Kubernetes RBAC com um *RoleBinding*. Se o seu cluster AKS se integrar com o Azure Ative Directory, as encadernações são como esses utilizadores de AD Azure são autorizados a realizar ações dentro do cluster.

As encadernações de funções são usadas para atribuir funções para um determinado espaço de nome. Esta abordagem permite segregar logicamente um único cluster AKS, com os utilizadores a penas que só conseguem aceder aos recursos de aplicação no seu espaço de nome atribuído. Se precisar de ligar funções em todo o cluster, ou de agrupar recursos fora de um determinado espaço de nome, pode utilizar *clusterRoleBindings*.

Um ClusterRoleBinding funciona da mesma forma para ligar as funções aos utilizadores, mas pode ser aplicado a recursos em todo o cluster, e não um espaço de nome específico. Esta abordagem permite-lhe conceder aos administradores ou apoiar engenheiros o acesso a todos os recursos do cluster AKS.

## <a name="next-steps"></a>Passos seguintes

Para começar com a Azure AD e a Kubernetes RBAC, consulte [O Diretório Ativo Integrado Azure com AKS][aks-aad].

Para obter as melhores práticas associadas, consulte [as melhores práticas de autenticação e autorização no AKS.][operator-best-practices-identity]

Para obter informações adicionais sobre os conceitos core Kubernetes e AKS, consulte os seguintes artigos:

- [Aglomerados kubernetes / AKS e cargas de trabalho][aks-concepts-clusters-workloads]
- [Kubernetes / Segurança AKS][aks-concepts-security]
- [Redes virtuais Kubernetes / AKS][aks-concepts-network]
- [Armazenamento Kubernetes / AKS][aks-concepts-storage]
- [Escala kubernetes / AKS][aks-concepts-scale]

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
