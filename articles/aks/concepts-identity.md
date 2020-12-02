---
title: Conceitos - Acesso e identidade nos Serviços Azure Kubernetes (AKS)
description: Saiba mais sobre o acesso e identidade no Serviço Azure Kubernetes (AKS), incluindo a integração do Azure Ative Directory, o controlo de acesso baseado em funções da Kubernetes (Kubernetes RBAC), e papéis e encadernações.
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: 983b1a5e024a44733fab418a67375f232e66cfe4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96457175"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Access and identity options for Azure Kubernetes Service (AKS) (Opções de acesso e de identidade do Azure Kubernetes Service (AKS))

Existem diferentes formas de autenticar, controlar o acesso/autorizar e proteger os clusters Kubernetes. Utilizando o controlo de acesso baseado em funções da Kubernetes (Kubernetes RBAC), pode conceder aos utilizadores, grupos e contas de serviço acesso apenas aos recursos de que necessitam. Com o Serviço Azure Kubernetes (AKS), pode melhorar ainda mais a estrutura de segurança e permissões utilizando o Azure Ative Directory e o Azure RBAC. Estas abordagens ajudam-no a garantir o acesso ao cluster e a fornecer apenas as permissões mínimas necessárias aos desenvolvedores e operadores.

Este artigo introduz os conceitos fundamentais que o ajudam a autenticar e atribuir permissões em AKS:

- [Kubernetes controlo de acesso baseado em funções (Kubernetes RBAC)](#kubernetes-role-based-access-control-kubernetes-rbac)
  - [Funções e ClusterRoles](#roles-and-clusterroles)
  - [RoleBindings e ClusterRoleBindings](#rolebindings-and-clusterrolebindings) 
  - [Contas de serviço kubernetes](#kubernetes-service-accounts)
- [Integração do Azure Active Directory](#azure-active-directory-integration)
- [RBAC do Azure](#azure-role-based-access-control-azure-rbac)
  - [Azure RBAC para autorizar o acesso ao recurso AKS](#azure-rbac-to-authorize-access-to-the-aks-resource)
  - [Azure RBAC para autorização kubernetes (pré-visualização)](#azure-rbac-for-kubernetes-authorization-preview)


## <a name="kubernetes-role-based-access-control-kubernetes-rbac"></a>Kubernetes controlo de acesso baseado em funções (Kubernetes RBAC)

Para fornecer filtragem granular das ações que os utilizadores podem fazer, Kubernetes usa o controlo de acesso baseado em funções kubernetes (Kubernetes RBAC). Este mecanismo de controlo permite atribuir aos utilizadores, ou grupos de utilizadores, permissão para fazer coisas como criar ou modificar recursos, ou ver registos de cargas de trabalho de aplicações. Estas permissões podem ser miradas para um único espaço de nome, ou concedidas em todo o cluster AKS. Com o KUBernetes RBAC, *cria-se funções* para definir permissões e, em seguida, atribui-las a utilizadores com *encadernações de papéis*.

Para obter mais informações, consulte [a autorização do RBAC da Kubernetes.][kubernetes-rbac]


### <a name="roles-and-clusterroles"></a>Funções e ClusterRoles

Antes de atribuir permissões a utilizadores com O RBAC de Kubernetes, primeiro define essas permissões como *um Papel*. Os papéis de Kubernetes *concedem* permissões. Não há nenhum conceito de permissão *de negação.*

As funções são usadas para conceder permissões dentro de um espaço de nome. Se precisar de conceder permissões em todo o cluster, ou para agrupar recursos fora de um determinado espaço de nome, pode, em vez disso, utilizar *clusterRoles.*

Um ClusterRole trabalha da mesma forma para conceder permissões aos recursos, mas pode ser aplicado a recursos em todo o cluster, e não um espaço de nome específico.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings e ClusterRoleBindings

Uma vez definidas as funções para conceder permissões aos recursos, atribua essas permissões de NCC de Kubernetes com um *RoleBinding*. Se o seu cluster AKS [se integrar com o Azure Ative Directory (Azure AD)](#azure-active-directory-integration), as ligações são como esses utilizadores Azure AD recebem permissões para executar ações dentro do cluster, ver como no Control o acesso aos recursos do cluster [usando o controlo de acesso baseado em funções da Kubernetes e identidades do Azure Ative Directory.](azure-ad-rbac.md)

As ligações de funções são usadas para atribuir papéis para um determinado espaço de nome. Esta abordagem permite-lhe, logicamente, segregar um único cluster AKS, com os utilizadores apenas capazes de aceder aos recursos da aplicação no seu espaço de nome atribuído. Se precisar de ligar funções em todo o cluster, ou de agrupar recursos fora de um determinado espaço de nome, pode, em vez disso, utilizar *ClusterRoleBindings*.

Um ClusterRoleBinding funciona da mesma forma para ligar funções aos utilizadores, mas pode ser aplicado a recursos em todo o cluster, e não num espaço de nome específico. Esta abordagem permite conceder aos administradores ou engenheiros de apoio acesso a todos os recursos do cluster AKS.


> [!NOTE]
> Quaisquer ações de cluster tomadas pela Microsoft/AKS são feitas com o consentimento do utilizador sob uma função incorporada de Kubernetes `aks-service` e encadernação de funções `aks-service-rolebinding` incorporadas . Esta função permite que a AKS resolva problemas e diagnostice problemas de cluster, mas não pode modificar permissões, criar papéis ou encadernações de papéis, ou outras ações de alto privilégio. O acesso à função só é ativado em bilhetes de apoio ativos com acesso just-in-time (JIT). Leia mais sobre [as políticas de apoio à AKS.](support-policies.md)


### <a name="kubernetes-service-accounts"></a>Contas de serviço kubernetes

Um dos principais tipos de utilizadores em Kubernetes é uma *conta de serviço.* Existe uma conta de serviço na API de Kubernetes. As credenciais para contas de serviço são armazenadas como segredos de Kubernetes, o que permite que sejam usadas por cápsulas autorizadas para comunicar com o Servidor API. A maioria dos pedidos da API fornecem um símbolo de autenticação para uma conta de serviço ou uma conta de utilizador normal.

As contas normais de utilizador permitem um acesso mais tradicional a administradores ou desenvolvedores humanos, não apenas serviços e processos. A Kubernetes em si não fornece uma solução de gestão de identidade onde contas de utilizador regulares e palavras-passe são armazenadas. Em vez disso, as soluções de identidade externa podem ser integradas em Kubernetes. Para os clusters AKS, esta solução de identidade integrada é o Azure Ative Directory.

Para obter mais informações sobre as opções de identidade em Kubernetes, consulte [a autenticação de Kubernetes.][kubernetes-authentication]

## <a name="azure-active-directory-integration"></a>Integração do Azure Active Directory

A segurança dos clusters AKS pode ser reforçada com a integração do Azure Ative Directory (AD). Construído com décadas de gestão de identidade empresarial, a Azure AD é um serviço multi-inquilino, com base na nuvem e serviço de gestão de identidade que combina serviços de diretório principal, gestão de acesso a aplicações e proteção de identidade. Com o Azure AD, pode integrar identidades no local em clusters AKS para fornecer uma única fonte de gestão de conta e segurança.

![Integração do Azure Ative Directory com clusters AKS](media/concepts-identity/aad-integration.png)

Com clusters AKS integrados em Azure, pode conceder aos utilizadores ou grupos acesso aos recursos de Kubernetes dentro de um espaço de nome ou em todo o cluster. Para obter um `kubectl` contexto de configuração, um utilizador pode executar o comando [az aks get-credentials.][az-aks-get-credentials] Quando um utilizador interage com o cluster AKS, `kubectl` é-lhes solicitado que assinem com as suas credenciais AD AZure. Esta abordagem fornece uma única fonte para a gestão da conta de utilizador e credenciais de senha. O utilizador só pode aceder aos recursos definidos pelo administrador do cluster.

A autenticação AZure AD é fornecida aos clusters AKS com OpenID Connect. OpenID Connect é uma camada de identidade construída em cima do protocolo OAuth 2.0. Para obter mais informações sobre o OpenID Connect, consulte a [documentação de ligação Open ID][openid-connect]. A partir do interior do cluster Kubernetes, [a Autenticação Token Webhook][webhook-token-docs] é usada para verificar fichas de autenticação. A autenticação de ficha webhook é configurada e gerida como parte do cluster AKS.

### <a name="webhook-and-api-server"></a>Servidor Webhook e API

![Fluxo de autenticação de servidores Webhook e API](media/concepts-identity/auth-flow.png)

Como mostrado no gráfico acima, o servidor API chama o servidor webhook AKS e executa os seguintes passos:

1. A aplicação do cliente Azure AD é utilizada pela kubectl para assinar em utilizadores com o fluxo de autorização do [dispositivo OAuth 2.0](../active-directory/develop/v2-oauth2-device-code.md).
2. A AZure AD fornece um access_token, id_token e um refresh_token.
3. O utilizador faz um pedido para kubectl com um access_token da kubeconfig.
4. A Kubectl envia o access_token para a APIServer.
5. O Servidor API está configurado com o Auth WebHook Server para realizar validação.
6. O servidor webhook de autenticação confirma que a assinatura JSON Web Token é válida verificando a chave de assinatura pública Azure AD.
7. A aplicação do servidor utiliza credenciais fornecidas pelo utilizador para consultar membros do grupo do utilizador iniciado a partir da API do Gráfico MS.
8. Uma resposta é enviada para o APIServer com informações do utilizador, tais como a reivindicação principal do utilizador (UPN) do token de acesso e a adesão do grupo ao utilizador com base no ID do objeto.
9. A API executa uma decisão de autorização com base no Papel/RoleBinding da Kubernetes.
10. Uma vez autorizado, o servidor API retorna uma resposta ao kubectl.
11. A Kubectl fornece feedback ao utilizador.
 
**Saiba como integrar a AKS com a AAD [aqui.](managed-aad.md)**

## <a name="azure-role-based-access-control-azure-rbac"></a>Controlo de acesso baseado em funções do Azure (RBAC do Azure)

O Azure RBAC é um sistema de autorização construído na [Azure Resource Manager](../azure-resource-manager/management/overview.md) que fornece uma gestão de acesso fino dos recursos Azure.

 O Azure RBAC foi projetado para trabalhar em recursos dentro da sua subscrição Azure, enquanto kubernetes RBAC é projetado para trabalhar em recursos Kubernetes dentro do seu cluster AKS. 

Com o Azure RBAC, cria-se uma *definição de papel* que descreve as permissões a aplicar. Um utilizador ou grupo é então atribuído esta definição de função através de uma *atribuição de papel* para um determinado *âmbito*, que poderia ser um recurso individual, um grupo de recursos, ou em toda a subscrição.

Para obter mais informações, consulte [o que é o controlo de acesso baseado em funções do Azure (Azure RBAC)?][azure-rbac]

Existem dois níveis de acesso necessários para operar plenamente um cluster AKS: 
1. [Aceda ao recurso AKS na sua subscrição Azure.](#azure-rbac-to-authorize-access-to-the-aks-resource) Este processo permite-lhe controlar as coisas que escalam ou melhoram o seu cluster utilizando as APIs AKS, bem como puxar o seu kubeconfig.
2. Acesso à API de Kubernetes. Este acesso é controlado quer pela [Kubernetes RBAC](#kubernetes-role-based-access-control-kubernetes-rbac) (tradicionalmente) quer pela [integração do Azure RBAC com a AKS para a autorização kubernetes](#azure-rbac-for-kubernetes-authorization-preview)

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Azure RBAC para autorizar o acesso ao recurso AKS

Com o Azure RBAC, pode fornecer aos seus utilizadores (ou identidades) acesso granular aos recursos AKS através de uma ou mais subscrições. Por exemplo, pode ter a [função Azure Kubernetes Service Contributor](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) que lhe permite fazer ações como escala e atualizar o seu cluster. Enquanto outro utilizador poderia ter o [papel de Azure Kubernetes Service Cluster Admin](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) que apenas dá permissão para puxar o Admin kubeconfig.

Em alternativa, poderia dar ao seu utilizador o papel [de Contribuinte](../role-based-access-control/built-in-roles.md#contributor) Geral, que abrangeria as permissões acima referidas e todas as ações possíveis no recurso AKS, com exceção da gestão das próprias permissões.

Veja mais como usar o Azure RBAC para garantir o acesso ao ficheiro kubeconfig que dá acesso à API de Kubernetes [aqui.](control-kubeconfig-access.md)

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC para autorização kubernetes (pré-visualização)

Com a integração do Azure RBAC, a AKS utilizará um servidor webhook de autorização kubernetes para lhe permitir gerir permissões e atribuições de recursos de cluster K8s integrados em Azure, utilizando definição de função Azure e atribuições de funções.

![Azure RBAC para o fluxo de autorização de Kubernetes](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Como mostrado no diagrama acima, ao utilizar a integração Azure RBAC todos os pedidos à API de Kubernetes seguirão o mesmo fluxo de autenticação como explicado na [secção de integração Azure Ative](#azure-active-directory-integration). 

Mas depois disso, em vez de depender exclusivamente da Kubernetes RBAC para autorização, o pedido será realmente autorizado pela Azure, desde que a identidade que fez o pedido exista na AAD. Se a identidade não existir no AAD, por exemplo uma conta de serviço Kubernetes, então o Azure RBAC não vai fazer efeito, e será o RBAC normal de Kubernetes.

Neste cenário, você poderia dar aos utilizadores uma das quatro funções incorporadas, ou criar papéis personalizados como faria com os papéis de Kubernetes, mas neste caso usando os mecanismos e APIs Azure RBAC. 

Esta funcionalidade permitirá, por exemplo, não só dar aos utilizadores permissões ao recurso AKS através de subscrições, como configurar e dar-lhes o papel e permissões que terão dentro de cada um desses clusters que controlam o acesso à API de Kubernetes. Por exemplo, pode conceder o `Azure Kubernetes Service RBAC Viewer` papel no âmbito de subscrição e o seu destinatário será capaz de listar e obter todos os objetos Kubernetes de todos os clusters, mas não modificá-los.


#### <a name="built-in-roles"></a>Funções incorporadas

A AKS fornece os seguintes quatro papéis incorporados. São semelhantes aos [papéis incorporados de Kubernetes,](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) mas com algumas diferenças como apoiar CRDs. Para a lista completa de ações permitidas por cada um deles construído em funções, consulte [aqui.](../role-based-access-control/built-in-roles.md)

| Função                                | Descrição  |
|-------------------------------------|--------------|
| Azure Kubernetes Service RBAC Viewer  | Permite o acesso apenas à leitura para ver a maioria dos objetos num espaço de nome. Não permite visualizar papéis ou encadernações de papéis. Esta função não permite a `Secrets` visualização, uma vez que a leitura do conteúdo dos Segredos permite o acesso a `ServiceAccount` credenciais no espaço de nomes, o que permitiria o acesso da API como qualquer outro `ServiceAccount` no espaço de nome (uma forma de escalada de privilégio)  |
| Azure Kubernetes Service RBAC Writer | Permite ler/escrever o acesso à maioria dos objetos num espaço de nome. Esta função não permite visualizar ou modificar papéis ou encadernações de papéis. No entanto, esta função permite aceder `Secrets` e executar Pods como qualquer ServiceAccount no espaço de nomes, para que possa ser usado para obter os níveis de acesso API de qualquer ServiceAccount no espaço de nomes. |
| Azure Kubernetes Serviço RBAC Administrador  | Permite o acesso a administrador, destinado a ser concedido dentro de um espaço de nome. Permite o acesso de leitura/escrita à maioria dos recursos num espaço de nome (ou âmbito de cluster), incluindo a capacidade de criar papéis e encadernações de papéis dentro do espaço de nomes. Esta função não permite escrever acesso à quota de recursos ou ao próprio espaço de nome. |
| Azure Kubernetes Serviço RBAC Cluster Admin  | Permite o acesso do super-utilizador para realizar qualquer ação em qualquer recurso. Dá total controlo sobre todos os recursos do cluster e em todos os espaços de nome. |

**Para aprender como ativar a autorização do Azure RBAC para a Kubernetes, [leia aqui.](manage-azure-rbac.md)**

## <a name="summary"></a>Resumo

Esta tabela resume a forma como os utilizadores podem autenticar em Kubernetes quando a integração AZure AD está ativada.  Em todos os casos, a sequência de comandos do utilizador é:
1. Corra `az login` para autenticar até Azure.
1. Corra `az aks get-credentials` para descarregar credenciais para o cluster em `.kube/config` .
1. Executar `kubectl` comandos (o primeiro dos quais pode desencadear a autenticação baseada no navegador para autenticar o cluster, conforme descrito na tabela seguinte).

A subvenção de papel referida na segunda coluna é a subvenção de função Azure RBAC mostrada no **separador Controlo de Acesso** no portal Azure. O Cluster Admin Azure AD Group é apresentado no **separador Configuração** no portal (ou com nome de parâmetro `--aad-admin-group-object-ids` no CLI Azure).

| Descrição        | Subvenção de papel exigida| Grupo de administrador azure Ad cluster | Quando utilizar |
| -------------------|------------|----------------------------|-------------|
| Login de administração legado usando certificado de cliente| **Azure Kubernetes Admin Role**. Esta função permite `az aks get-credentials` ser utilizada com a `--admin` bandeira, que descarrega um [certificado de administração de cluster legacy (não-Azure AD)](control-kubeconfig-access.md) no do utilizador `.kube/config` . Este é o único propósito de "Azure Kubernetes Admin Role".|n/a|Se estiver permanentemente bloqueado por não ter acesso a um grupo AZure AD válido com acesso ao seu cluster.| 
| Azure AD com rolebindings manuais (Cluster)| **Papel de utilizador de Azure Kubernetes**. A função "Utilizador" permite `az aks get-credentials` ser utilizada sem a `--admin` bandeira. (Este é o único propósito de "Azure Kubernetes User Role".) O resultado, num cluster AD ativado pelo Azure, é o download de [uma entrada vazia](control-kubeconfig-access.md) em , que despoleta a `.kube/config` autenticação baseada no navegador quando é usada pela primeira vez por `kubectl` .| O utilizador não está em nenhum destes grupos. Como o utilizador não está em nenhum grupo de Administração cluster, os seus direitos serão controlados inteiramente por quaisquer RoleBindings ou ClusterRoleBindings que tenham sido criados por administradores de cluster. Os (Cluster)RoleBindings [nomeiam os utilizadores AD do Azure ou os grupos AD Azure](azure-ad-rbac.md) como seus `subjects` . Se não tiverem sido criadas tais ligações, o utilizador não poderá excortar quaisquer `kubectl` comandos.|Se quer um controlo de acesso fino e não está a usar o Azure RBAC para a Autorização Kubernetes. Note que o utilizador que configura as ligações deve iniciar sessão por um dos outros métodos listados nesta tabela.|
| Azure AD por membro do grupo de administração| Mesmo que acima|O utilizador é membro de um dos grupos listados aqui. AKS gera automaticamente um ClusterRoleBinding que liga todos os grupos listados ao `cluster-admin` papel de Kubernetes. Para que os utilizadores destes grupos possam executar todos os `kubectl` comandos como `cluster-admin` .|Se quiser conceder convenientemente aos utilizadores plenos direitos de administração, e _não_ estiver a utilizar o RBAC Azure para a autorização da Kubernetes.|
| Azure AD com Azure RBAC para autorização Kubernetes|Duas funções: Primeiro, **Azure Kubernetes User Role** (como acima). Segundo, um dos "Azure Kubernetes Service **RBAC..."** papéis listados acima, ou a sua própria alternativa personalizada.|O campo de funções de administração no separador Configuração é irrelevante quando o RBAC de Azure para a Autorização de Kubernetes está ativado.|Está a usar o Azure RBAC para autorização kubernetes. Esta abordagem dá-lhe um controlo fino, sem a necessidade de configurar RoleBindings ou ClusterRoleBindings.|

## <a name="next-steps"></a>Passos seguintes

- Para começar com Azure AD e Kubernetes RBAC, consulte [Integrate Azure Ative Directory com AKS][aks-aad].
- Para as melhores práticas associadas, consulte [as melhores práticas de autenticação e autorização em AKS][operator-best-practices-identity].
- Para começar com o Azure RBAC para autorização kubernetes, consulte [Use Azure RBAC para autorizar o acesso dentro do Cluster Azure Kubernetes Service (AKS).](manage-azure-rbac.md)
- Para começar a proteger o seu ficheiro kubeconfig, consulte [limite de acesso ao ficheiro de configuração do cluster](control-kubeconfig-access.md)

Para obter mais informações sobre os conceitos core Kubernetes e AKS, consulte os seguintes artigos:

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
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
