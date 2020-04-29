---
title: Práticas de segurança recomendadas
description: Ao utilizar a gestão de recursos delegados do Azure, é importante considerar a segurança e o controlo de acesso.
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: d9b806aaf988fedfde6ce468f3eff948aa8ce344
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80246913"
---
# <a name="recommended-security-practices"></a>Práticas de segurança recomendadas

Ao utilizar a [gestão de recursos delegados do Azure,](azure-delegated-resource-management.md)é importante considerar a segurança e o controlo de acesso. Os utilizadores do seu inquilino terão acesso direto a subscrições de clientes e grupos de recursos, pelo que irá querer tomar medidas para manter a segurança do seu inquilino. Também vai querer ter a certeza de que só permite o acesso necessário para gerir eficazmente os recursos dos seus clientes. Este tópico fornece recomendações para ajudá-lo a fazê-lo.

## <a name="require-azure-multi-factor-authentication"></a>Exigir a Multi-Factor Authentication do Azure

[A autenticação de vários fatores azure](../../active-directory/authentication/concept-mfa-howitworks.md) (também conhecida como verificação em duas etapas) ajuda a impedir que os atacantes tenham acesso a uma conta, exigindo múltiplas etapas de autenticação. Deverá necessitar de Autenticação Multi-Factor para todos os utilizadores do seu fornecedor de serviços, incluindo quaisquer utilizadores que tenham acesso aos recursos do cliente.

Sugerimos que peça aos seus clientes que implementem a Autenticação Multi-Factor Azure nos seus inquilinos.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Atribuir permissões a grupos, usando o princípio do menor privilégio

Para facilitar a gestão, recomendamos a utilização de grupos de utilizadores Da Azure AD para cada função necessária para gerir os recursos dos seus clientes. Isto permite adicionar ou remover utilizadores individuais ao grupo conforme necessário, em vez de atribuir permissões diretamente a esse utilizador.

> [!IMPORTANT]
> Para adicionar permissões a um grupo Azure AD, o **tipo de grupo** deve ser **segurança** e não o **Office 365**. Esta opção é selecionada quando o grupo é criado. Para mais informações, consulte [Criar um grupo básico e adicionar membros usando o Diretório Ativo Azure](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Ao criar a sua estrutura de permissão, certifique-se de seguir o princípio do menor privilégio para que os utilizadores tenham apenas as permissões necessárias para completar o seu trabalho, ajudando a reduzir a probabilidade de erros inadvertidos.

Por exemplo, pode querer usar uma estrutura como esta:

|Nome do grupo  |Tipo  |principais  |Definição de função  |ID de definição de papel  |
|---------|---------|---------|---------|---------|
|Arquitetos     |Grupo de utilizadores         |\<principais\>         |Contribuinte         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Avaliação     |Grupo de utilizadores         |\<principais\>         |Leitor         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|Especialistas em VM     |Grupo de utilizadores         |\<principais\>         |Colaborador vM         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automatização     |Nome principal do serviço (SPN)         |\<principais\>         |Contribuinte         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

Uma vez criados estes grupos, pode atribuir aos utilizadores as necessidades. Adicione apenas os utilizadores que realmente precisam de ter acesso. Certifique-se de que revê regularmente a adesão ao grupo e remova todos os utilizadores que já não sejam adequados ou necessários para incluir.

Tenha em mente que quando embarcar nos clientes através de uma oferta de [serviço gerido pelo público,](../how-to/publish-managed-services-offers.md)qualquer grupo (ou utilizador ou diretor de serviço) que inclua terá as mesmas permissões para cada cliente que comprar o plano. Para atribuir diferentes grupos para trabalhar com cada cliente, terá de publicar um plano privado separado que seja exclusivo de cada cliente, ou clientes a bordo individualmente utilizando modelos do Gestor de Recursos Azure. Por exemplo, você poderia publicar um plano público que tem acesso muito limitado, em seguida, trabalhar com o cliente diretamente para embarcar seus recursos para acesso adicional usando um Modelo de Recurso Azure personalizado que concede acesso adicional conforme necessário.


## <a name="next-steps"></a>Passos seguintes

- [Implementar a autenticação de vários fatores azure.](../../active-directory/authentication/howto-mfa-getstarted.md)
- Conheça [as experiências de gestão de inquilinos cruzados.](cross-tenant-management-experience.md)
