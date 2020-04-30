---
title: Funções de administrador personalizado no Diretório Ativo azure [ Microsoft Docs
description: Preview custom Azure AD funções para delegar gestão de identidade. Gerencie as funções Azure no portal Azure, PowerShell ou Graph API.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/22/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae244d93d679199aaa0bd08891cd34d4ca3a2ddc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085115"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Funções de administrador personalizado no Diretório Ativo azure (pré-visualização)

Este artigo descreve como compreender as funções personalizadas da Azure AD no Azure Ative Directory (Azure AD) com controlo de acesso baseado em funções e âmbitos de recursos. Os papéis personalizados de Anúncio Azure surgem nas permissões subjacentes às [funções incorporadas,](directory-assign-admin-roles.md)para que possa criar e organizar as suas próprias funções personalizadas. Esta abordagem permite-lhe conceder acesso de uma forma mais granular do que papéis incorporados, sempre que são necessários. Este primeiro lançamento de funções personalizadas da Azure AD inclui a capacidade de criar um papel para atribuir permissões para a gestão de registos de aplicações. Com o tempo, serão adicionadas permissões adicionais para recursos da organização, como aplicações empresariais, utilizadores e dispositivos.  

Além disso, as funções personalizadas da Azure AD apoiam atribuições por recurso, além das atribuições mais tradicionais em toda a organização. Esta abordagem dá-lhe a capacidade de conceder acesso à gestão de alguns recursos (por exemplo, uma inscrição de uma aplicação) sem dar acesso a todos os recursos (todos os registos de aplicações).

O controlo de acesso baseado em funções da Azure AD é uma funcionalidade de pré-visualização pública da Azure AD e está disponível com qualquer plano de licença Azure AD pago. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Compreender o controlo de acesso baseado em funções da Azure AD

Conceder permissão usando funções personalizadas de AD Azure é um processo em duas etapas que envolve criar uma definição de função personalizada e, em seguida, atribuí-la usando uma atribuição de funções. Uma definição de papel personalizada é uma coleção de permissões que adiciona a partir de uma lista predefinida. Estas permissões são as mesmas permissões usadas nos papéis incorporados.  

Uma vez criado a sua definição de papel, pode atribuí-la a um utilizador criando uma atribuição de funções. Uma atribuição de funções concede ao utilizador as permissões numa definição de função num âmbito especificado. Este processo em duas etapas permite-lhe criar uma definição de função única e atribuí-la muitas vezes em diferentes âmbitos. Um âmbito define o conjunto de recursos da AD Azure a que o membro do papel tem acesso. O âmbito mais comum é o âmbito de âmbito (org-wide) em toda a organização. Um papel personalizado pode ser atribuído no âmbito org-wide, o que significa que o membro do papel tem o papel permissões sobre todos os recursos da organização. Uma função personalizada também pode ser atribuída num âmbito de objeto. Um exemplo de um âmbito de objeto seria uma única aplicação. A mesma função pode ser atribuída a um utilizador sobre todas as aplicações da organização e, em seguida, a outro utilizador com um âmbito apenas da aplicação Contoso Expense Reports.  

As funções azure AD incorporadas e personalizadas operam em conceitos semelhantes ao [controlo de acesso baseado em funções Azure.](../../role-based-access-control/overview.md) A diferença entre estes dois sistemas de controlo de [acesso baseados em funções](../../role-based-access-control/rbac-and-directory-admin-roles.md) é que o Azure RBAC controla o acesso a recursos azure, como máquinas virtuais ou armazenamento utilizando a Azure Resource Management, e as funções personalizadas azure controlam o acesso aos recursos da AD Azure utilizando a API graph. Ambos os sistemas alavancam o conceito de definições de papéis e atribuições de papéis.

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Como a AD Azure determina se um utilizador tem acesso a um recurso

Seguem-se os passos de alto nível que a Azure AD utiliza para determinar se tem acesso a um recurso de gestão. Utilize esta informação para resolver problemas de acesso.

1. Um utilizador (ou diretor de serviço) adquire um símbolo no ponto final do Microsoft Graph ou Azure AD Graph.

1. O utilizador faz uma chamada aPi para o Azure Ative Directory (Azure AD) através do Microsoft Graph ou do Azure AD Graph utilizando o token emitido.

1. Dependendo da circunstância, a Azure AD toma uma das seguintes ações:

    - Avalia as adesões de função do utilizador com base na reclamação de [wids](https://docs.microsoft.com/azure/active-directory/develop/access-tokens) no token de acesso do utilizador.
    - Recupera todas as atribuições de funções que se aplicam ao utilizador, quer diretamente quer através da adesão ao grupo, ao recurso em que a ação está a ser tomada.

1. A Azure AD determina se a ação na chamada API está incluída nas funções que o utilizador tem para este recurso.
1. Se o utilizador não tiver um papel com a ação no âmbito solicitado, o acesso não é concedido. Caso contrário, o acesso é concedido.

### <a name="role-assignments"></a>Atribuições de funções

Uma atribuição de funções é o objeto que anexa uma definição de função a um utilizador num âmbito específico para conceder acesso ao recurso Azure AD. O acesso é concedido ao criar uma atribuição de função e o acesso é revogado ao remover uma atribuição de função. No seu cerne, uma atribuição de funções é constituída por três elementos:

- Utilizador (um indivíduo que tem um perfil de utilizador no Diretório Ativo do Azure)
- Definição de função
- Âmbito de recurso

Pode [criar atribuições](roles-create-custom.md) de funções utilizando o portal Azure, Azure AD PowerShell ou Graph API. Também pode [ver as atribuições para um papel personalizado.](roles-view-assignments.md#view-the-assignments-of-a-role)

O diagrama seguinte mostra um exemplo de uma atribuição de função. Neste exemplo, Chris Green foi designado o papel personalizado do administrador de registo da App no âmbito do registo de aplicações Contoso Widget Builder. A atribuição concede a Chris as permissões do papel de administrador de registo da App apenas para este registo específico da aplicação.

![Atribuição de funções é como as permissões são aplicadas e tem três partes](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Principal de segurança

Um diretor de segurança representa o utilizador a que será atribuído acesso aos recursos da AD Azure. Um *utilizador* é um indivíduo que tem um perfil de utilizador no Diretório Ativo Azure.

### <a name="role"></a>Função

Uma definição de papel, ou papel, é uma coleção de permissões. Uma definição de papel lista as operações que podem ser realizadas nos recursos da AD Azure, tais como criar, ler, atualizar e eliminar. Existem dois tipos de funções em Azure AD:

- Papéis incorporados criados pela Microsoft que não podem ser alterados.
- Funções personalizadas criadas e geridas pela sua organização.

### <a name="scope"></a>Âmbito

Um âmbito é a restrição das ações permitidas a um recurso ad ida e revestido específico como parte de uma atribuição de funções. Ao atribuir uma função, pode especificar um âmbito que limita o acesso do administrador a um recurso específico. Por exemplo, se quiser conceder a um programador uma função personalizada, mas apenas para gerir um registo específico de candidatura, pode incluir o registo específico da candidatura como âmbito na atribuição de funções.

  > [!Note]
  > As funções personalizadas podem ser atribuídas no âmbito do diretório e no âmbito dos recursos. Ainda não podem ser atribuídos no âmbito da Unidade Administrativa.
  > As funções incorporadas podem ser atribuídas no âmbito do diretório e, em alguns casos, no âmbito da Unidade Administrativa. Ainda não podem ser atribuídos no âmbito de recursos da AD Azure.

## <a name="required-license-plan"></a>Plano de licença obrigatório

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Passos seguintes

- Crie atribuições de funções personalizadas utilizando [o portal Azure, Azure AD PowerShell e Graph API](roles-create-custom.md)
- [Ver as atribuições para um papel personalizado](roles-view-assignments.md#view-assignments-of-single-application-scope)
