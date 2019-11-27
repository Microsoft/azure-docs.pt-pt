---
title: Práticas de segurança recomendadas
description: Ao usar o gerenciamento de recursos delegado do Azure, é importante considerar a segurança e o controle de acesso.
ms.date: 07/11/2019
ms.topic: conceptual
ms.openlocfilehash: 18decc337722c1dc64fac94679d783dd55915ee6
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463896"
---
# <a name="recommended-security-practices"></a>Práticas de segurança recomendadas

Ao usar o gerenciamento de recursos delegado do Azure, é importante considerar a segurança e o controle de acesso. Os usuários em seu locatário terão acesso direto a grupos de recursos e assinaturas de cliente, portanto, você desejará tomar medidas para manter a segurança do seu locatário. Você também desejará certificar-se de permitir apenas o acesso necessário para gerenciar os recursos de seus clientes com eficiência. Este tópico fornece recomendações para ajudá-lo a fazer isso.

## <a name="require-azure-multi-factor-authentication"></a>Exigir autenticação multifator do Azure

A [autenticação multifator do Azure](../../active-directory/authentication/concept-mfa-howitworks.md) (também conhecida como verificação em duas etapas) ajuda a impedir que invasores obtenham acesso a uma conta exigindo várias etapas de autenticação. Você deve exigir a autenticação multifator para todos os usuários em seu locatário do provedor de serviços, incluindo qualquer usuário que terá acesso aos recursos do cliente.

Sugerimos que você solicite que seus clientes implementem a autenticação multifator do Azure em seus locatários também.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Atribuir permissões a grupos, usando o princípio de privilégios mínimos

Para facilitar o gerenciamento, é recomendável usar grupos de usuários do Azure AD para cada função necessária para gerenciar os recursos dos seus clientes. Isso permite que você adicione ou remova usuários individuais do grupo, conforme necessário, em vez de atribuir permissões diretamente a esse usuário.

Ao criar sua estrutura de permissão, certifique-se de seguir o princípio de privilégios mínimos para que os usuários tenham apenas as permissões necessárias para concluir seu trabalho, ajudando a reduzir a chance de erros inadvertidos.

Por exemplo, talvez você queira usar uma estrutura como esta:

|Nome do grupo  |Tipo  |principalId  |Definição de função  |ID de definição de função  |
|---------|---------|---------|---------|---------|
|Arquitetos     |Grupo de utilizadores         |\<PrincipalId\>         |Contribuinte         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Avaliação     |Grupo de utilizadores         |\<PrincipalId\>         |Leitor         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|Especialistas de VM     |Grupo de utilizadores         |\<PrincipalId\>         |Colaborador de VM         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automatização     |SPN (nome da entidade de serviço)         |\<PrincipalId\>         |Contribuinte         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

Depois de criar esses grupos, você pode atribuir usuários conforme necessário. Somente adicione os usuários que realmente precisam ter acesso. Certifique-se de revisar a associação de grupo regularmente e remover os usuários que não são mais apropriados ou necessários para incluir.

Tenha em mente que, quando você integrar [clientes por meio de uma oferta de serviço gerenciado público](../how-to/publish-managed-services-offers.md), qualquer grupo (ou entidade de serviço ou usuário) que você incluir terá as mesmas permissões para cada cliente que comprar o plano. Para atribuir grupos diferentes para trabalhar com cada cliente, você precisará publicar um plano privado separado que seja exclusivo para cada cliente ou integrar clientes individualmente usando modelos de Azure Resource Manager. Por exemplo, você pode publicar um plano público com acesso muito limitado e, em seguida, trabalhar com o cliente diretamente para integrar seus recursos para acesso adicional usando um modelo de recurso personalizado do Azure concedendo acesso adicional, conforme necessário.


## <a name="next-steps"></a>Passos Seguintes

- [Implante a autenticação multifator do Azure](../../active-directory/authentication/howto-mfa-getstarted.md).
- Saiba mais sobre as [experiências de gerenciamento entre locatários](cross-tenant-management-experience.md).
