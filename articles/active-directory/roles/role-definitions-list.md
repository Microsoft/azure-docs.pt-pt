---
title: Lista Azure Definições de função AD - Azure AD
description: Saiba como listar os papéis incorporados e personalizados do Azure.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 03/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d37b2988d32c854e4184adee998341ebadcee053
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467885"
---
# <a name="list-azure-ad-role-definitions"></a>Lista Azure Definições de função AD

Uma definição de papel é uma coleção de permissões que podem ser realizadas, tais como ler, escrever e apagar. Normalmente chama-se papel. O Azure Ative Directory tem mais de 60 funções incorporadas ou pode criar as suas próprias funções personalizadas. Se alguma vez te perguntaste "O que é que estes papéis realmente fazem?", podes ver uma lista detalhada de permissões para cada um dos papéis.

Este artigo descreve como listar as funções azure AD incorporadas e personalizadas juntamente com as suas permissões.

## <a name="list-all-roles"></a>Listar todos os papéis

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com) e selecione **O Diretório Ativo Azure**.

1. Selecione **Funções e administradores** para ver a lista de todas as funções disponíveis.

    ![lista de papéis no portal AD AZure](./media/role-definitions-list/view-roles-in-azure-active-directory.png)

1. À direita, selecione a elipse e, em seguida, **descrição** para ver a lista completa de permissões para um papel.

    A página inclui links para documentação relevante para ajudar a guiá-lo através da gestão de funções.

    ![Screenshot que mostra a página "Administrador Global - Descrição".](./media/role-definitions-list/role-description.png)

## <a name="next-steps"></a>Passos seguintes

* Sinta-se livre para compartilhar conosco no fórum de [funções administrativas Azure AD.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
* Para obter mais informações sobre funções e atribuição de funções de Administrador, consulte [as funções de administrador de atribuição](permissions-reference.md).
* Para obter permissões de utilizador predefinidos, consulte uma [comparação entre as permissões de utilizador e de hóspedes predefinidos](../fundamentals/users-default-permissions.md).
