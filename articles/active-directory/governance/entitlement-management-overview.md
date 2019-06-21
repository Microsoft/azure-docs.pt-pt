---
title: O que é a gestão de direitos do Azure AD? (Pré-visualização) - o Azure Active Directory
description: Obtenha uma descrição geral da gestão de direitos do Azure Active Directory e como pode usá-lo para gerir o acesso a grupos, aplicações e sites do SharePoint Online para utilizadores internos e externos.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/05/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbb4001e9496d31d9c2879721f8cf8e26b74ddf3
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204540"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>O que é a gestão de direitos do Azure AD? (Pré-visualização)

> [!IMPORTANT]
> Gestão de direitos do Active Directory (Azure AD) do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os funcionários em organizações precisam de acesso a vários grupos, aplicações e sites para realizar seu trabalho. Gerir este acesso é um desafio. Na maioria dos casos, não há nenhuma lista organizada de todos os recursos de que precisa de um utilizador para um projeto. O Gestor de projeto tem uma boa compreensão dos recursos necessários, os indivíduos envolvidos e durante quanto tempo que durará o projeto. No entanto, normalmente, o Gestor de projeto não tem permissões para aprovar ou conceder acesso a outras pessoas. Este cenário fica mais complicado ao tentar trabalhar com pessoas externas ou as empresas.

Gestão de direitos do Active Directory (Azure AD) do Azure pode ajudá-lo a gerir o acesso a grupos, aplicações e sites do SharePoint Online para utilizadores internos e, também, com utilizadores fora da sua organização.

## <a name="why-use-entitlement-management"></a>Por que usar o gerenciamento de direitos?

As empresas frequentemente enfrentam desafios ao gerir o acesso a recursos como:

- Os usuários talvez não saiba o que eles devem ter de acesso
- Os utilizadores podem ter dificuldade em localizar certos indivíduos ou recursos corretos
- Assim que os usuários localizarem e recebem acesso a um recurso, eles podem sem soltar para aceder a mais do que é necessário para fins empresariais

Esses problemas são compostos para utilizadores que necessitam de acesso a partir de outro diretório, tais como utilizadores externos que são de organizações de cadeia de fornecimento ou outros parceiros de negócios. Por exemplo:

- As organizações podem não saber todos os indivíduos específicos em outros diretórios para conseguir convidá-los
- Mesmo se as organizações podem convidar estes utilizadores, as organizações podem não se lembrar gerir todos os de acesso do usuário de forma consistente

Gestão de direitos do Azure AD pode ajudar a vencer esses desafios.

## <a name="what-can-i-do-with-entitlement-management"></a>O que posso fazer com a gestão de direitos?

Aqui estão algumas das capacidades de gestão de direitos de:

- Criar pacotes de recursos relacionados que os utilizadores podem pedir
- Definir regras para saber como a recursos de pedido e que expira o acesso
- Governar o ciclo de vida de acesso para utilizadores internos e externos
- Delegar a gestão de recursos
- Designar os aprovadores para aprovar pedidos
- Criar relatórios para controlar o histórico

Para obter uma descrição geral de governação de identidade e gestão de elegibilidade, veja o vídeo seguinte da conferência Ignite 2018:

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>Que recursos posso gerir?

Aqui estão os tipos de recursos, que pode gerir o acesso à gestão de direitos:

- Grupos de segurança do Azure AD
- Grupos do Office 365
- Aplicações do Azure AD empresarial, incluindo a aplicação SaaS e aplicações integradas personalizado que oferecem suporte à Federação ou aprovisionamento
- Conjuntos de sites SharePoint Online e sites

Também pode controlar o acesso a outros recursos que contam com grupos de segurança do Azure AD ou grupos do Office 365.  Por exemplo:

- Pode dar licenças de utilizadores para o Microsoft Office 365, utilizando um grupo de segurança do Azure AD num pacote de acesso e configurando [licenciamento baseado em grupo](../users-groups-roles/licensing-groups-assign.md) para esse grupo
- Pode dar aos utilizadores acesso para gerir recursos do Azure ao utilizar um grupo de segurança do Azure AD num pacote de acesso e criar uma [atribuição de função do Azure](../../role-based-access-control/role-assignments-portal.md) para esse grupo

## <a name="what-are-access-packages-and-policies"></a>Quais são os pacotes de acesso e políticas?

Gestão de direitos introduz o conceito de um *pacote de acesso*. Um pacote de acesso é um pacote de todos os recursos de que um utilizador tem de trabalhar num projeto ou executar seu trabalho. Os recursos incluem acesso a grupos, aplicações ou sites. Pacotes de acesso são utilizadas para governar o acesso para os funcionários internos bem como os utilizadores fora da sua organização. Pacotes de acesso são definidas nos contentores chamados *catálogos*.

Pacotes de acesso também incluem um ou mais *políticas*. Uma política define as regras ou guardrails para aceder a um pacote de acesso. Ativar uma política impõe que apenas os utilizadores certos são ganham acesso aos recursos certos e para a quantidade certa de tempo.

![Pacote de acesso e políticas](./media/entitlement-management-overview/elm-overview-access-package.png)

Com um pacote de acesso e as políticas, define o Gestor de pacotes de acesso:

- Recursos
- Funções os utilizadores precisam para os recursos
- Os usuários internos e utilizadores externos que são elegíveis para pedir acesso
- Processo de aprovação e os utilizadores que podem aprovar ou negar o acesso
- Duração do acesso de utilizador

O diagrama seguinte mostra um exemplo dos diferentes elementos na gestão de direitos. Mostra dois pacotes de acesso de exemplo.

- **Pacote de acesso 1** inclui um único grupo como um recurso. Acesso é definido com uma política que permite a um conjunto de utilizadores no diretório para pedir acesso.
- **Pacote de acesso 2** inclui um grupo, um aplicativo e um site do SharePoint Online, como recursos. Acesso é definido com duas políticas diferentes. A primeira diretiva permite que um conjunto de utilizadores no diretório para pedir acesso. A segunda política permite aos utilizadores num diretório externo pedir acesso.

![Descrição geral da gestão de direitos](./media/entitlement-management-overview/elm-overview.png)

## <a name="external-users"></a>Utilizadores externos

Ao utilizar o [do Azure AD empresa-empresa (B2B)](../b2b/what-is-b2b.md) convidar experiência, já deve saber que os endereços de e-mail dos utilizadores convidados externos que pretende trazer para o seu diretório de recursos e trabalhar com. Este funciona muito bem de quando estiver trabalhando num projeto mais pequeno ou curto prazo e que já conhece todos os participantes, mas isso é mais difícil de gerenciar se tiver muitos utilizadores que pretende trabalhar com ou se os participantes mudam ao longo do tempo.  Por exemplo, poderá trabalhar com outra organização e ter um ponto de contacto com essa organização, mas ao longo do tempo os utilizadores adicionais dessa organização também precisará de acesso.

Com a gestão de direitos, pode definir uma política que permite aos utilizadores de organizações que especificar, que também estão a utilizar o Azure AD, para poder pedir um pacote de acesso. Pode especificar se é necessária aprovação e uma data de expiração para o acesso. Se a aprovação for necessária, também pode designar como um aprovador um ou mais utilizadores da organização externa que anteriormente convidou - uma vez que é provável que sabe que utilizadores externos da sua organização precisam de acesso. Assim que tiver configurado o pacote de acesso, pode enviar uma ligação para o pacote de acesso à sua pessoa de contato numa organização externa. Esse contacto pode partilhar com outros utilizadores na organização externa e podem utilizar esta ligação para o pacote de acesso do pedido.  Os utilizadores dessa organização que já foi convidados para o seu diretório também podem utilizar essa ligação.

Quando um pedido for aprovado, gestão de direitos irá aprovisionar o utilizador com o acesso necessário, o que pode incluir a convidar o usuário se não ainda estiverem no seu diretório. Do Azure AD irá criar automaticamente uma conta de B2B para eles.  Tenha em atenção que um administrador pode ter anteriormente limitado que as organizações têm permissão para a colaboração, definindo um [B2B permitir ou negar lista](../b2b/allow-deny-list.md) permitir ou bloquear convites para outras organizações.  Se o utilizador não é permitido pela lista de permissões ou de bloqueios, em seguida, não serão convidados.

Uma vez que não quiser acesso do utilizador externo ao último para sempre, especifique uma data de expiração a política, como 180 dias. Após 180 dias, se o acesso não for renovado, gestão de direitos removerá todos os acessos associados a esse pacote de acesso.  Se o usuário que foi convidado através da gestão de direitos não existem outras atribuições de pacote de acesso, em seguida, quando eles perderem a sua última atribuição, a conta de B2B será bloqueada no início de sessão para 30 dias e subsequentemente removida.  Isto impede que a proliferação de contas desnecessárias.  

## <a name="terminology"></a>Terminologia

Para compreender melhor a gestão de direitos e a respetiva documentação, deve rever os termos seguintes.

| Termo ou conceito | Descrição |
| --- | --- |
| gestão de direitos | Um serviço que atribui, revoga e administra os pacotes de acesso. |
| pacote de acesso | Uma coleção de permissões e políticas de recursos que os utilizadores podem pedir. Um pacote de acesso sempre está contido num catálogo. |
| pedido de acesso | Um pedido para aceder a um pacote de acesso. Um pedido normalmente passa por um fluxo de trabalho. |
| política | Um conjunto de regras que define o ciclo de vida de acesso, por exemplo, como os utilizadores obtêm acesso, que pode aprovar e o tempo que os utilizadores têm acesso. Algumas políticas incluem o acesso dos funcionários e de acesso externo. |
| catalog | Um contentor de recursos relacionados e pacotes de acesso. |
| Catálogo geral | Um catálogo interno que está sempre disponível. Para adicionar recursos para o catálogo de geral, requer determinadas permissões. |
| resource | Um recurso ou serviço (por exemplo, um grupo, aplicação ou site) que um usuário pode ser concedido permissões para. |
| Tipo de recurso | O tipo de recurso, que inclui os grupos, aplicações e sites do SharePoint Online. |
| função de recursos | Uma coleção de permissões associadas um recurso. |
| diretório de recursos | Um diretório que tem um ou mais recursos para partilhar. |
| utilizadores atribuídos | Uma atribuição de um pacote de acesso a um utilizador ou grupo. |
| Ativar | O processo de disponibilizar um pacote de acesso dos utilizadores de solicitarem. |

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Nuvens especializadas, como o Azure Government, Azure Alemanha e Azure China 21Vianet, não estão atualmente disponíveis para utilização nesta pré-visualização.

## <a name="next-steps"></a>Passos Seguintes

- [Tutorial: Crie seu primeiro pacote de acesso](entitlement-management-access-package-first.md)
- [Cenários comuns](entitlement-management-scenarios.md)
