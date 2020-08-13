---
title: Práticas de segurança recomendadas
description: Ao utilizar o Farol Azure, é importante considerar o controlo de segurança e acesso.
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: ddc4d2c69e7aacc49c5309bc407aecd9bc8e572f
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163276"
---
# <a name="recommended-security-practices"></a>Práticas de segurança recomendadas

Ao utilizar [o Farol Azure,](../overview.md)é importante considerar o controlo de segurança e acesso. Os utilizadores do seu inquilino terão acesso direto a subscrições de clientes e grupos de recursos, pelo que deverá tomar medidas para manter a segurança do seu inquilino. Também vai querer ter a certeza de que apenas permite o acesso necessário para gerir eficazmente os recursos dos seus clientes. Este tópico fornece recomendações para ajudá-lo a fazê-lo.

> [!TIP]
> Estas recomendações aplicam-se também às [empresas que gerem vários inquilinos](enterprise.md) com o Farol de Azure.

## <a name="require-azure-multi-factor-authentication"></a>Exigir a Multi-Factor Authentication do Azure

[A autenticação multi-factor Azure](../../active-directory/authentication/concept-mfa-howitworks.md) (também conhecida como verificação em duas etapas) ajuda a evitar que os atacantes tenham acesso a uma conta, exigindo várias etapas de autenticação. Deverá necessitar de autenticação multi-factor para todos os utilizadores do seu inquilino gerente, incluindo utilizadores que tenham acesso aos recursos delegados do cliente.

Sugerimos que peça aos seus clientes que implementem a Autenticação Multi-Factor Azure também nos seus inquilinos.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Atribuir permissões a grupos, utilizando o princípio do menor privilégio

Para facilitar a gestão, utilize grupos Azure Ative Directory (Azure AD) para cada função necessária para gerir os recursos dos seus clientes. Isto permite adicionar ou remover utilizadores individuais ao grupo conforme necessário, em vez de atribuir permissões diretamente a cada utilizador.

> [!IMPORTANT]
> Para adicionar permissões para um grupo AD Azure, o **tipo de grupo** deve ser a **Segurança** e não o **Office 365**. Esta opção é selecionada quando o grupo é criado. Para obter mais informações, consulte [Criar um grupo básico e adicionar membros utilizando o Azure Ative Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Ao criar a sua estrutura de permissão, certifique-se de seguir o princípio do menor privilégio para que os utilizadores tenham apenas as permissões necessárias para completar o seu trabalho, ajudando a reduzir a chance de erros inadvertidos.

Por exemplo, pode querer usar uma estrutura como esta:

|Nome do grupo  |Tipo  |principalId  |Definição de função  |ID de definição de papel  |
|---------|---------|---------|---------|---------|
|Arquitetos     |Grupo de utilizadores         |\<principalId\>         |Contribuidor         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Avaliação     |Grupo de utilizadores         |\<principalId\>         |Leitor         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|Especialistas em VM     |Grupo de utilizadores         |\<principalId\>         |Colaborador VM         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automatização     |Nome principal do serviço (SPN)         |\<principalId\>         |Contribuidor         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

Uma vez criados estes grupos, pode atribuir os utilizadores conforme necessário. Adicione apenas os utilizadores que realmente precisam de ter acesso. Certifique-se de rever regularmente a adesão ao grupo e remover quaisquer utilizadores que já não sejam apropriados ou necessários para incluir.

Tenha em mente que quando você [estiver a bordo de clientes através de uma oferta de serviço gerido pelo público,](../how-to/publish-managed-services-offers.md)qualquer grupo (ou utilizador ou diretor de serviço) que você inclua terá as mesmas permissões para cada cliente que comprar o plano. Para atribuir diferentes grupos para trabalhar com cada cliente, terá de publicar um plano privado separado que seja exclusivo de cada cliente, ou clientes a bordo individualmente, utilizando modelos de Gestor de Recursos Azure. Por exemplo, pode publicar um plano público que tenha acesso muito limitado, e depois trabalhar com o cliente diretamente para bordo dos seus recursos para acesso adicional usando um Modelo de Recursos Azure personalizado que concede acesso adicional conforme necessário.

## <a name="next-steps"></a>Passos seguintes

- [Implementar a autenticação multi-factor Azure](../../active-directory/authentication/howto-mfa-getstarted.md).
- Conheça as [experiências de gestão de inquilinos cruzados.](cross-tenant-management-experience.md)
