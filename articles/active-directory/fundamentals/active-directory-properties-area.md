---
title: Adicione a informação de privacidade da sua organização - Azure Ative Directory Microsoft Docs
description: Instruções sobre como adicionar as informações de privacidade da sua organização à área Azure Ative Directory Properties.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfc60eacd70576d9cfa79dbc03368c8e8c4a8636
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87797398"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Adicione as informações de privacidade da sua organização usando o Azure Ative Directory
Este artigo explica como um administrador inquilino pode adicionar informações relacionadas com a privacidade ao inquilino Azure Ative Directory (Azure AD) de uma organização, através do portal Azure.

Recomendamos vivamente que adicione tanto o seu contacto de privacidade global como a declaração de privacidade da sua organização, para que os seus funcionários internos e hóspedes externos possam rever as suas políticas. Como as declarações de privacidade são criadas e adaptadas exclusivamente para cada negócio, recomendamos vivamente que contacte um advogado para obter assistência.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Adicione a sua informação de privacidade no Azure AD
Você adiciona as informações de privacidade da sua organização na área **de Propriedades** do Azure AD.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Para aceder à área propriedades e adicionar as suas informações de privacidade

1. Inscreva-se no portal Azure como administrador de inquilinos.

2. Na navbar esquerda, selecione **Azure Ative Directory**e, em seguida, selecione **Propriedades**.

    A área **de Propriedades** aparece.

    ![Área Azure AD Properties destacando a área de informação de privacidade](media/active-directory-properties-area/properties-area.png)

3. Adicione a sua informação de privacidade aos seus colaboradores:

    - **Contacto técnico.** Digite o endereço de e-mail para que a pessoa contacte para apoio técnico dentro da sua organização.
    
    - **Contacto global de privacidade.** Digite o endereço de e-mail para a pessoa entrar em contato para informações sobre privacidade de dados pessoais. Esta pessoa também é quem a Microsoft contacta se houver uma violação de dados. Se não houver nenhuma pessoa listada aqui, a Microsoft contacta os seus administradores globais.

    - **URL de declaração de privacidade.** Digite o link para o documento da sua organização que descreve como a sua organização lida com a privacidade dos dados internos e externos dos hóspedes.

        >[!Important]
        >Se não incluir nem a sua própria declaração de privacidade nem o seu contacto de privacidade, os seus hóspedes externos verão texto na caixa **de Permissões de Revisão** que diz que o ** < _seu nome org_> não forneceu links para os seus termos para que possa rever.** Por exemplo, um utilizador convidado verá esta mensagem quando receber um convite para aceder a uma organização através da colaboração B2B.

        ![Caixa de permissões de revisão de colaboração B2B com mensagem](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4. Selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes
- [Azure Ative Directory B2B convite de colaboração resgate](../b2b/redemption-experience.md)
- [Adicionar ou alterar informações de perfil para um utilizador no Azure Ative Directory](active-directory-users-profile-azure-portal.md)
