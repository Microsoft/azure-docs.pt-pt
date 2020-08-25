---
title: Segurança para soluções Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Compreenda as melhores práticas de segurança com a Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 973eeebfdf9164cb50cf98ae8edc845a80a7e080
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88794506"
---
# <a name="secure-azure-digital-twins"></a>Secure Azure Digital Twins

Para a segurança, a Azure Digital Twins permite um controlo preciso de acesso sobre dados, recursos e ações específicas na sua implementação. Fá-lo através de uma estratégia de gestão de funções granular e de permissão chamada **controlo de acesso baseado em funções (RBAC)**. Pode ler-se [aqui](../role-based-access-control/overview.md)sobre os princípios gerais da RBAC para o Azure.

A Azure Digital Twins também suporta a encriptação de dados em repouso.

## <a name="granting-permissions-with-rbac"></a>Concessão de permissões com o RBAC

O RBAC é fornecido à Azure Digital Twins através da integração com [o Azure Ative Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD).

Pode utilizar o RBAC para conceder permissões a um *diretor de segurança,* que pode ser um utilizador, um grupo ou um diretor de serviço de aplicação. O principal de segurança é autenticado pela Azure AD, e recebe um token OAuth 2.0 em troca. Este token pode ser usado para autorizar um pedido de acesso a uma instância Azure Digital Twins.

### <a name="authentication-and-authorization"></a>Autenticação e autorização

Com a Azure AD, o acesso é um processo em duas etapas. Quando um principal de segurança (um utilizador, grupo ou aplicação) tentar aceder a Azure Digital Twins, o pedido deve ser *autenticado* e *autorizado*. 

1. Primeiro, a identidade do diretor de segurança é *autenticada,* e um token OAuth 2.0 é devolvido.
2. Em seguida, o token é passado como parte de um pedido ao serviço Azure Digital Twins, para *autorizar* o acesso ao recurso especificado.

A etapa de autenticação requer qualquer pedido de pedido para conter um token de acesso OAuth 2.0 no tempo de execução. Se uma aplicação estiver a ser executada dentro de uma entidade Azure, como uma aplicação [Azure Functions,](../azure-functions/functions-overview.md) pode utilizar uma **identidade gerida** para aceder aos recursos. Leia mais sobre identidades geridas na secção seguinte.

O passo de autorização requer que um papel de Azure seja atribuído ao diretor de segurança. As funções atribuídas a um diretor de segurança determinam as permissões que o diretor terá. A Azure Digital Twins fornece funções Azure que englobam conjuntos de permissões para recursos da Azure Digital Twins. Estes papéis são descritos mais tarde neste artigo.

Para saber mais sobre papéis e atribuições de papéis suportados no Azure, consulte [*Compreender os diferentes papéis*](../role-based-access-control/rbac-and-directory-admin-roles.md) na documentação do Azure RBAC.

#### <a name="authentication-with-managed-identities"></a>Autenticação com identidades geridas

[As identidades geridas para os recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) são uma funcionalidade cross-Azure que lhe permite criar uma identidade segura associada à implementação onde o seu código de aplicação funciona. Em seguida, pode associar essa identidade a funções de controlo de acesso, para conceder permissões personalizadas para aceder a recursos específicos do Azure de que a sua aplicação necessita.

Com identidades geridas, a plataforma Azure gere esta identidade de tempo de execução. Não precisa de armazenar e proteger as chaves de acesso no seu código de aplicação ou configuração, quer para a própria identidade, quer para os recursos a que necessita. Uma aplicação de clientes Azure Digital Twins que funciona dentro de uma aplicação do Azure App Service não precisa de lidar com as regras e chaves da SAS, nem quaisquer outros tokens de acesso. A aplicação do cliente só precisa do endereço final do espaço de nomes Azure Digital Twins. Quando a aplicação se conecta, a Azure Digital Twins liga o contexto da entidade gerida ao cliente. Uma vez associado a uma identidade gerida, o seu cliente Azure Digital Twins pode fazer todas as operações autorizadas. A autorização será então concedida associando uma entidade gerida com um papel Azure Digital Twins Azure (descrito abaixo).

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>Autorização: Funções Azure para Azure Digital Twins

A Azure fornece as funções incorporadas abaixo do Azure para autorizar o acesso a um recurso Azure Digital Twins:
* *Azure Digital Twins Owner (Preview)* – Use esta função para dar acesso total sobre os recursos da Azure Digital Twins.
* *Azure Digital Twins Reader (Preview)* – Use este papel para dar acesso apenas à leitura dos recursos da Azure Digital Twins.

> [!TIP]
> O papel *do Azure Digital Twins Reader (Preview)* agora também suporta relações de navegação.

Para obter mais informações sobre como as funções incorporadas são [*definidas,*](../role-based-access-control/role-definitions.md) consulte definições de funções na documentação do Azure RBAC. Para obter informações sobre a criação de funções personalizadas [*Azure, consulte os papéis personalizados da Azure*](../role-based-access-control/custom-roles.md).

Pode atribuir funções de duas formas:
* através do painel de controlo de acesso (IAM) para Azure Digital Twins no portal Azure (ver [*Adicionar ou remover atribuições de funções utilizando o RBAC Azure e o portal Azure)*](../role-based-access-control/role-assignments-portal.md)
* através de comandos CLI para adicionar ou remover uma função

Para passos mais detalhados sobre como fazê-lo, experimente-o no Tutorial de Gémeos Digitais Azure: [*Conecte uma solução de ponta a ponta*](tutorial-end-to-end.md).

### <a name="permission-scopes"></a>Âmbitos de permissão

Antes de atribuir um papel de Azure a um diretor de segurança, determine o âmbito de acesso que o diretor de segurança deve ter. As melhores práticas ditam que o melhor é conceder apenas o âmbito mais estreito possível.

A lista que se segue descreve os níveis em que pode aceder aos recursos da Azure Digital Twins.
* Modelos: As ações para este recurso ditam o controlo sobre [os modelos carregados](concepts-models.md) em Azure Digital Twins.
* Consulta Digital Twins Graph: As ações deste recurso determinam a capacidade de executar [operações de consulta](concepts-query-language.md) em gémeos digitais dentro do gráfico Azure Digital Twins.
* Digital Twin: As ações deste recurso fornecem controlo sobre as operações crud em [gémeos digitais](concepts-twins-graph.md) no gráfico duplo.
* Relação Digital Twin: As ações para este recurso definem o controlo sobre as operações crud nas [relações](concepts-twins-graph.md) entre gémeos digitais no gráfico gémeo.
* Rota do evento: As ações deste recurso determinam permissões para [encaminhar eventos](concepts-route-events.md) da Azure Digital Twins para um serviço de ponto final como [Event Hub,](../event-hubs/event-hubs-about.md) [Event Grid](../event-grid/overview.md)ou [Service Bus.](../service-bus-messaging/service-bus-messaging-overview.md)

### <a name="troubleshooting-permissions"></a>Permissões de resolução de problemas

Se um utilizador tentar executar uma ação não permitida pela sua função, poderá receber um erro da leitura do pedido de serviço `403 (Forbidden)` . Para obter mais informações e etapas de resolução de problemas, consulte [*Resolução de Problemas: Pedido de Gémeos Digitais Azure falhou com o Estado: 403 (Proibido)*](troubleshoot-error-403.md).

## <a name="encryption-of-data-at-rest"></a>Encriptação de dados em repouso

A Azure Digital Twins fornece encriptação de dados em repouso e em trânsito, tal como está escrito nos nossos centros de dados, e desencripta-os para si à medida que os acede.

## <a name="next-steps"></a>Passos seguintes

* Consulte estes conceitos em ação em [*Como-a: Configurar um caso e autenticação.*](how-to-set-up-instance-scripted.md)

* Veja como interagir com estes conceitos a partir do código de aplicação do cliente em [*Como-a-: Escrever código de autenticação de aplicações*](how-to-authenticate-client.md).

* Leia mais sobre [o RBAC para Azure.](../role-based-access-control/overview.md)
