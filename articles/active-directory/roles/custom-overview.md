---
title: Funções de administrador personalizado no Azure Ative Directory Microsoft Docs
description: Saiba como entender as funções personalizadas da Azure AD no Azure Ative Directory (Azure AD) com controlo de acesso baseado em funções e âmbitos de recursos.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b230361ddc771693849d01de156339d1b595826d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949212"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Funções de administrador personalizado no Azure Ative Directory (pré-visualização)

Este artigo descreve como compreender as funções personalizadas da Azure AD no Azure Ative Directory (Azure AD) com controlo de acesso baseado em funções e âmbitos de recursos. As funções AD personalizadas surfacem as permissões subjacentes às [funções incorporadas,](permissions-reference.md)para que possa criar e organizar as suas próprias funções personalizadas. Esta abordagem permite-lhe conceder acesso de uma forma mais granular do que papéis incorporados, sempre que necessário. Esta primeira versão das funções personalizadas Azure AD inclui a capacidade de criar um papel para atribuir permissões para gerir registos de aplicações. Com o tempo, serão adicionadas permissões adicionais para recursos da organização, como aplicações empresariais, utilizadores e dispositivos.  

Além disso, as funções personalizadas AZURE suportam atribuições por recurso, além das atribuições mais tradicionais em toda a organização. Esta abordagem dá-lhe a possibilidade de conceder acesso à gestão de alguns recursos (por exemplo, um registo de aplicações) sem dar acesso a todos os recursos (todos os registos de aplicações).

O controlo de acesso baseado em funções Azure AD é uma funcionalidade de pré-visualização pública do Azure AD e está disponível com qualquer plano de licença AZure AD pago. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Compreenda o controlo de acesso baseado em funções da Azure AD

Conceder permissão usando funções Azure AD personalizadas é um processo em duas etapas que envolve criar uma definição de papel personalizada e, em seguida, atribuí-la usando uma atribuição de papel. Uma definição de função personalizada é uma coleção de permissões que você adiciona a partir de uma lista pré-configurada. Estas permissões são as mesmas permissões usadas nas funções incorporadas.  

Uma vez criada a definição de papel, pode atribuí-la a um utilizador criando uma atribuição de funções. Uma atribuição de funções concede ao utilizador as permissões numa definição de função num âmbito especificado. Este processo em duas etapas permite-lhe criar uma definição de papel única e atribuí-la muitas vezes em diferentes âmbitos. Um âmbito define o conjunto de recursos Azure AD a que o membro tem acesso. O âmbito mais comum é o âmbito de organização (org-wide). Um papel personalizado pode ser atribuído no âmbito org-wide, o que significa que o membro do papel tem o papel permissões sobre todos os recursos na organização. Uma função personalizada também pode ser atribuída no âmbito do objeto. Um exemplo de um âmbito de objeto seria uma única aplicação. A mesma função pode ser atribuída a um utilizador em todas as aplicações da organização e depois a outro utilizador com âmbito apenas da app Contoso Expense Reports.  

As funções azure AD incorporadas e personalizadas operam em conceitos semelhantes ao controlo de acesso baseado em [funções Azure (Azure RBAC)](../../active-directory-b2c/overview.md). A [diferença entre estes dois sistemas de controlo de acesso baseados em funções](../../role-based-access-control/rbac-and-directory-admin-roles.md) é que o Azure RBAC controla o acesso aos recursos Azure, tais como máquinas virtuais ou armazenamento usando a Azure Resource Management, e as funções personalizadas AZURE controlam o acesso aos recursos Azure AD usando a API do gráfico. Ambos os sistemas alavancam o conceito de definições de funções e atribuições de funções. As permissões Azure AD RBAC não podem ser incluídas em funções de Azure e vice-versa.

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Como a Azure AD determina se um utilizador tem acesso a um recurso

Seguem-se os passos de alto nível que o Azure AD utiliza para determinar se tem acesso a um recurso de gestão. Utilize estas informações para resolver problemas de acesso.

1. Um utilizador (ou principal de serviço) adquire um símbolo para o microsoft Graph ou Azure AD Graph.

1. O utilizador faz uma chamada da API para o Azure Ative Directory (Azure AD) através do Microsoft Graph ou Azure AD Graph utilizando o token emitido.

1. Dependendo das circunstâncias, a Azure AD toma uma das seguintes ações:

    - Avalia as funções do utilizador com base na [alegação de wids](../../active-directory-b2c/access-tokens.md) no token de acesso do utilizador.
    - Recupera todas as atribuições de funções que se candidatam ao utilizador, direta ou através da adesão ao grupo, ao recurso em que as medidas estão a ser tomadas.

1. A Azure AD determina se a ação na chamada API está incluída nas funções que o utilizador tem para este recurso.
1. Se o utilizador não tiver um papel na ação no âmbito solicitado, o acesso não é concedido. Caso contrário, o acesso é concedido.

### <a name="role-assignments"></a>Atribuições de funções

Uma atribuição de funções é o objeto que anexa uma definição de papel a um utilizador num âmbito específico para conceder acesso a recursos Azure AD. O acesso é concedido ao criar uma atribuição de função e o acesso é revogado ao remover uma atribuição de função. No fundo, uma função é composta por três elementos:

- Utilizador (um indivíduo que tem um perfil de utilizador no Azure Ative Directory)
- Definição de função
- Âmbito do recurso

Pode [criar atribuições de funções](custom-create.md) utilizando o portal Azure, Azure AD PowerShell ou API de gráficos. Também pode [ver as atribuições para um papel personalizado.](custom-view-assignments.md#view-the-assignments-of-a-role)

O diagrama seguinte mostra um exemplo de uma atribuição de função. Neste exemplo, Chris Green foi atribuído ao administrador de registo da App papel personalizado no âmbito do registo da aplicação Contoso Widget Builder. A atribuição concede ao Chris as permissões da função de administrador de registo da App apenas para este registo específico da aplicação.

![Atribuição de funções é como as permissões são aplicadas e tem três partes](./media/custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Principal de segurança

Um principal de segurança representa o utilizador que deve ser atribuído ao acesso aos recursos Azure AD. Um *utilizador* é um indivíduo que tem um perfil de utilizador no Azure Ative Directory.

### <a name="role"></a>Função

Uma definição de papel, ou papel, é uma coleção de permissões. Uma definição de papel lista as operações que podem ser realizadas em recursos AD Azure, tais como criar, ler, atualizar e apagar. Existem dois tipos de papéis em Azure AD:

- Papéis incorporados criados pela Microsoft que não podem ser alterados.
- Papéis personalizados criados e geridos pela sua organização.

### <a name="scope"></a>Âmbito

Um âmbito é a restrição de ações permitidas a um determinado recurso AD Azure como parte de uma atribuição de funções. Quando atribui uma função, pode especificar um âmbito que limita o acesso do administrador a um recurso específico. Por exemplo, se pretender conceder a um desenvolvedor um papel personalizado, mas apenas para gerir um registo específico de candidatura, pode incluir o registo específico de candidatura como âmbito na atribuição de funções.

  > [!Note]
  > As funções personalizadas podem ser atribuídas no âmbito do diretório e no âmbito dos recursos. Ainda não podem ser atribuídos no âmbito da Unidade Administrativa.
  > As funções incorporadas podem ser atribuídas no âmbito do diretório e, em alguns casos, no âmbito da Unidade Administrativa. Ainda não podem ser atribuídos no âmbito de recursos Azure AD.

## <a name="required-license-plan"></a>Plano de licença exigido

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Passos seguintes

- Crie atribuições de funções personalizadas utilizando [o portal Azure AD PowerShell e API de gráficos](custom-create.md)
- [Ver as atribuições para um papel personalizado](custom-view-assignments.md)
