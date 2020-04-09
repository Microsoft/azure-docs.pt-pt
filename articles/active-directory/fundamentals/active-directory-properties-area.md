---
title: Adicione as informações de privacidade da sua organização - Azure Ative Diretórios / Microsoft Docs
description: Instruções sobre como adicionar as informações de privacidade da sua organização à área de Propriedades de Diretório Ativo Azure.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 343671c1d9ee82950a9822648f9831588da7e9f8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876189"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Adicione as informações de privacidade da sua organização usando o Diretório Ativo Azure
Este artigo explica como um administrador inquilino pode adicionar informações relacionadas com a privacidade ao inquilino Azure Ative Directory (Azure AD) de uma organização, através do portal Azure.

Recomendamos vivamente que adicione tanto o seu contacto de privacidade global como a declaração de privacidade da sua organização, para que os seus colaboradores internos e hóspedes externos possam rever as suas políticas. Como as declarações de privacidade são criadas e adaptadas exclusivamente para cada negócio, recomendamos vivamente que contacte um advogado para obter assistência.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Adicione as suas informações de privacidade no Azure AD
Você adiciona as informações de privacidade da sua organização na área de **Propriedades** do Azure AD.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Para aceder à área de Propriedades e adicionar as suas informações de privacidade

1.    Inscreva-se no portal Azure como administrador de inquilinos.

2.    Na barra de navegação à esquerda, selecione **Azure Ative Directory**, e depois selecione **Propriedades**.

    A área **de Propriedades** aparece.

    ![Área da Azure AD Properties destacando a área de informação de privacidade](media/active-directory-properties-area/properties-area.png)

3.    Adicione as suas informações de privacidade aos seus colaboradores:

    - **Contacto técnico.** Digite o endereço de e-mail para que a pessoa contacte para obter apoio técnico dentro da sua organização.
    
    - **Contacto global de privacidade.** Digite o endereço de e-mail para a pessoa contactar para obter informações sobre privacidade de dados pessoais. Esta pessoa também é quem a Microsoft contacta se houver uma violação de dados. Se não houver ninguém listado aqui, a Microsoft contacta os seus administradores globais.

    - **URL de declaração de privacidade.** Digite o link para o documento da sua organização que descreve como a sua organização lida com a privacidade de dados dos hóspedes internos e externos.

        >[!Important]
        >Se não incluir a sua declaração de privacidade ou o seu contacto de privacidade, os seus hóspedes externos verão texto na caixa **de Permissões de Revisão** que diz, ** <o seu nome _org_> não forneceu links para os seus termos para que possa rever**. Por exemplo, um utilizador convidado verá esta mensagem quando receber um convite para aceder a uma organização através da colaboração B2B.

        ![Caixa de permissões de revisão de colaboração B2B com mensagem](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.    Selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes
- [Redenção do convite de colaboração Azure Ative Directory B2B](../b2b/redemption-experience.md)
- [Adicionar ou alterar informações de perfil para um utilizador no Diretório Ativo do Azure](active-directory-users-profile-azure-portal.md)
