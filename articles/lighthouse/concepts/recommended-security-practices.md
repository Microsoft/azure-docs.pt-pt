---
title: Práticas de segurança recomendado para Lighthouse do Azure
description: Ao utilizar o Azure, gestão de recursos é delegada, é importante considerar a segurança e controlo de acesso.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 843b965e6ea74a7c11dc11459ff5d30ddbe5c987
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809870"
---
# <a name="recommended-security-practices"></a>Práticas recomendadas de segurança

Ao utilizar o Azure, gestão de recursos é delegada, é importante considerar a segurança e controlo de acesso. Os utilizadores no seu inquilino têm acesso direto para subscrições de clientes e grupos de recursos, pelo que deverá tomar medidas para manter a segurança do seu inquilino. Também deve certificar-se de que apenas permite o acesso necessário para gerenciar com eficiência os recursos dos seus clientes. Este tópico fornece recomendações para o ajudar a fazê-lo.

## <a name="require-azure-multi-factor-authentication"></a>Exigir autenticação Multifator do Azure

[O Azure multi-factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (também conhecida como verificação de dois passos) ajuda a evitar que atacantes obtenham acesso a uma conta ao exigir que várias etapas de autenticação. Deve exigir multi-factor Authentication para todos os utilizadores no seu inquilino do fornecedor de serviço, incluindo quaisquer utilizadores que terão acesso aos recursos de cliente.

Sugerimos que peça a seus clientes para implementar o Azure multi-factor Authentication em seus inquilinos também.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Atribuir permissões a grupos, com o princípio de privilégio mínimo

Para facilitar a gestão, recomendamos que utilize grupos de utilizadores do Azure AD para cada função necessária para gerir os recursos dos seus clientes. Isto permite-lhe adicionar ou remover utilizadores individuais para o grupo, conforme necessário, em vez de atribuir permissões diretamente a esse utilizador.

Ao criar a estrutura da sua permissão, certifique-se de que acompanhar o princípio de privilégio mínimo, para que os utilizadores só têm as permissões necessárias para concluir seu trabalho, ajudando a reduzir a probabilidade de erros acidentais.

Por exemplo, pode querer utilizar uma estrutura como esta:

|Nome do grupo  |Type  |principalId  |Definição de função  |ID de definição de função  |
|---------|---------|---------|---------|---------|
|Arquitetos     |Grupo de utilizadores         |\<principalId\>         |Contribuinte         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Avaliação     |Grupo de utilizadores         |\<principalId\>         |Leitor         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|Especialistas de VM     |Grupo de utilizadores         |\<principalId\>         |Contribuidor de VM         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automatização     |Nome principal de serviço (SPN)         |\<principalId\>         |Contribuinte         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

Depois de criar estes grupos, pode atribuir utilizadores conforme necessário. Adicione apenas os utilizadores que realmente precisam de ter acesso. Não se esqueça de rever a associação ao grupo regularmente e remover todos os utilizadores que já não são necessárias para incluir ou apropriados.

Tenha em atenção que quando [integrar clientes através de uma oferta de serviço gerido pública](../how-to/publish-managed-services-offers.md), qualquer grupo (ou utilizador ou principal de serviço) que incluem terão as mesmas permissões para cada cliente que comprar o plano. Para atribuir grupos diferentes para trabalhar com cada cliente, terá de publicar um plano de privada separado que é exclusivo para cada cliente, ou integrar clientes individualmente utilizando modelos Azure Resource Manager. Por exemplo, pode publicar um plano de público que tenha muito acesso limitado, em seguida, trabalhar com o cliente diretamente para carregar os recursos para o acesso adicional através de um modelo de recursos do Azure personalizadas conceder o acesso adicional conforme necessário.


## <a name="next-steps"></a>Passos Seguintes

- [Implementar o Azure multi-factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md).
- Saiba mais sobre [experiências de gestão entre inquilinos](cross-tenant-management-experience.md).
