---
title: O que é a gestão de direitos do Azure AD? (Visualização)-Azure Active Directory
description: Obtenha uma visão geral do gerenciamento de direitos Azure Active Directory e como você pode usá-lo para gerenciar o acesso a grupos, aplicativos e sites do SharePoint Online para usuários internos e externos.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/03/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: a467856550bf2deaab931b3fe2f54b7986f12f8a
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430310"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>O que é a gestão de direitos do Azure AD? (Pré-visualização)

> [!IMPORTANT]
> O gerenciamento de direitos do Azure Active Directory (AD do Azure) está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os funcionários em organizações precisam acessar vários grupos, aplicativos e sites para executar seu trabalho. O gerenciamento desse acesso é desafiador. Na maioria dos casos, não há nenhuma lista organizada de todos os recursos de que um usuário precisa para um projeto. O gerente de projeto tem uma boa compreensão dos recursos necessários, dos indivíduos envolvidos e de quanto tempo o projeto durará. No entanto, o gerente de projeto normalmente não tem permissões para aprovar ou conceder acesso a outras pessoas. Esse cenário fica mais complicado quando você tenta trabalhar com indivíduos ou empresas externas.

O gerenciamento de direitos do Azure Active Directory (AD do Azure) pode ajudá-lo a gerenciar o acesso a grupos, aplicativos e sites do SharePoint Online para usuários internos e também usuários fora da sua organização.

Este vídeo fornece uma visão geral do gerenciamento de direitos e seu valor comercial:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="why-use-entitlement-management"></a>Por que usar o gerenciamento de direitos?

As organizações empresariais geralmente enfrentam desafios ao gerenciar o acesso a recursos como:

- Os usuários podem não saber qual acesso eles devem ter
- Os usuários podem ter dificuldade para localizar os indivíduos certos ou os recursos corretos
- Depois que os usuários localizarem e receberem acesso a um recurso, eles poderão manter o acesso mais longo do que o necessário para fins comerciais

Esses problemas são compostos por usuários que precisam de acesso de outro diretório, como usuários externos que são de organizações de cadeia de fornecedores ou outros parceiros comerciais. Por exemplo:

- As organizações podem não conhecer todos os indivíduos específicos de outros diretórios para poderem convidá-los
- Mesmo que as organizações possam convidar esses usuários, as organizações podem não se lembrar de gerenciar todo o acesso do usuário de forma consistente

O gerenciamento de direitos do Azure AD pode ajudar a resolver esses desafios.

## <a name="what-can-i-do-with-entitlement-management"></a>O que posso fazer com o gerenciamento de direitos?

Aqui estão alguns recursos de gerenciamento de direitos:

- Criar pacotes de recursos relacionados que os usuários podem solicitar
- Definir regras para como solicitar recursos e quando o acesso expirar
- Regem o ciclo de vida do acesso para usuários internos e externos
- Delegar gerenciamento de recursos
- Designar aprovadores para aprovar solicitações
- Criar relatórios para acompanhar o histórico

Para obter uma visão geral de governança de identidade e gerenciamento de direitos, Assista ao vídeo a seguir da conferência do Ignite 2018:

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>Quais recursos posso gerenciar?

Estes são os tipos de recursos para os quais você pode gerenciar o acesso com o gerenciamento de direitos:

- Grupos de segurança do Azure AD
- Grupos do Office 365
- Aplicativos empresariais do Azure AD, incluindo aplicativo SaaS e aplicativos integrados personalizados que dão suporte à Federação ou provisionamento
- Sites e conjuntos de sites do SharePoint Online

Você também pode controlar o acesso a outros recursos que dependem de grupos de segurança do Azure AD ou grupos do Office 365.  Por exemplo:

- Você pode conceder aos usuários licenças para o Microsoft Office 365 usando um grupo de segurança do Azure AD em um pacote do Access e Configurando o [Licenciamento baseado em grupo](../users-groups-roles/licensing-groups-assign.md) para esse grupo
- Você pode conceder aos usuários acesso para gerenciar recursos do Azure usando um grupo de segurança do Azure AD em um pacote do Access e criando uma [atribuição de função do Azure](../../role-based-access-control/role-assignments-portal.md) para esse grupo

## <a name="what-are-access-packages-and-policies"></a>O que são pacotes e políticas de acesso?

O gerenciamento de direitos apresenta o conceito de um *pacote de acesso*. Um pacote do Access é um grupo de todos os recursos que um usuário precisa para trabalhar em um projeto ou executar seu trabalho. Os recursos incluem acesso a grupos, aplicativos ou sites. Os pacotes do Access são usados para controlar o acesso de seus funcionários internos e também usuários fora da sua organização. Os pacotes de acesso são definidos em contêineres chamados *catálogos*.

Pacotes de acesso também incluem uma ou mais *políticas*. Uma política define as regras ou guardrails para acessar um pacote de acesso. A habilitação de uma política impõe que somente os usuários certos tenham acesso concedido, aos recursos certos e à quantidade de tempo certa.

![Acessar o pacote e as políticas](./media/entitlement-management-overview/elm-overview-access-package.png)

Com um pacote de acesso e suas políticas, o Gerenciador de pacotes de acesso define:

- Recursos
- Funções que os usuários precisam para os recursos
- Usuários internos e organizações parceiras de usuários externos qualificados para solicitar acesso
- Processo de aprovação e os usuários que podem aprovar ou negar o acesso
- Duração do acesso do usuário

O diagrama a seguir mostra um exemplo dos diferentes elementos no gerenciamento de direitos. Ele mostra dois pacotes de acesso de exemplo.

- O **pacote do Access 1** inclui um único grupo como um recurso. O acesso é definido com uma política que permite que um conjunto de usuários no diretório solicite acesso.
- O **pacote de acesso 2** inclui um grupo, um aplicativo e um site do SharePoint Online como recursos. O acesso é definido com duas políticas diferentes. A primeira política permite que um conjunto de usuários no diretório solicite acesso. A segunda política permite que os usuários em um diretório externo solicitem acesso.

![Visão geral do gerenciamento de direitos](./media/entitlement-management-overview/elm-overview.png)

## <a name="terminology"></a>Terminologia

Para entender melhor o gerenciamento de direitos e sua documentação, você deve examinar os termos a seguir.

| Termo ou conceito | Descrição |
| --- | --- |
| gerenciamento de direitos | Um serviço que atribui, revoga e administra pacotes de acesso. |
| pacote de acesso | Um pacote de recursos que uma equipe ou projeto precisa e é regido por políticas. Um pacote do Access sempre está contido em um catálogo. |
| solicitação de acesso | Uma solicitação para acessar os recursos em um pacote do Access. Normalmente, uma solicitação passa por um fluxo de trabalho. |
| política | Um conjunto de regras que define o ciclo de vida de acesso, como como os usuários obtêm acesso, quem pode aprovar e por quanto tempo os usuários têm acesso. As políticas de exemplo incluem acesso de funcionário e acesso externo. |
| Catalog | Um contêiner de recursos relacionados e pacotes de acesso. |
| Catálogo geral | Um catálogo interno que está sempre disponível. Para adicionar recursos ao catálogo geral, o requer determinadas permissões. |
| Kit | Um ativo ou serviço (como um grupo do Office, um grupo de segurança, um aplicativo ou um site do SharePoint Online) ao qual um usuário pode receber permissões. |
| Tipo de recurso | O tipo de recurso, que inclui grupos, aplicativos e sites do SharePoint Online. |
| função de recurso | Uma coleção de permissões associadas a um recurso. |
| diretório de recursos | Um diretório que tem um ou mais recursos a serem compartilhados. |
| usuários atribuídos | Uma atribuição de um pacote de acesso a um usuário, para que o usuário tenha todas as funções de recurso desse pacote de acesso. |
| Desabilitar | O processo de disponibilizar um pacote de acesso para os usuários solicitarem. |

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Nuvens especializadas, como Azure governamental, Azure Alemanha e Azure China 21Vianet, atualmente não estão disponíveis para uso nesta versão prévia.

### <a name="which-users-must-have-licenses"></a>Quais usuários devem ter licenças?

Seu locatário deve ter pelo menos tantas licenças Azure AD Premium P2 quanto você tem usuários membros ativos. Os usuários membros ativos no gerenciamento de direitos incluem:

- Um usuário que inicia ou aprova uma solicitação para um pacote de acesso.
- Um usuário ao qual foi atribuído um pacote do Access. 
- Um usuário que gerencia pacotes de acesso.

Como parte das licenças para usuários Membros, você também pode permitir que vários usuários convidados interajam com o gerenciamento de direitos. Para obter informações sobre como calcular o número de usuários convidados que você pode incluir, consulte [diretrizes de licenciamento de colaboração B2B Azure Active Directory](../b2b/licensing-guidance.md).

Para obter informações sobre como atribuir licenças a seus usuários, consulte [atribuir ou remover licenças usando o portal de Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: criar seu primeiro pacote de acesso](entitlement-management-access-package-first.md)
- [Cenários comuns](entitlement-management-scenarios.md)
