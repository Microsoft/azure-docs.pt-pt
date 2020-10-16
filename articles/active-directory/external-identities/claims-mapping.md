---
title: Utilizador de colaboração B2B reivindica mapeamento - Azure Ative Directory
description: Personalize as alegações do utilizador que são emitidas no token SAML para utilizadores B2B (Azure AD).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3508865d9f89501cf70e09087c6a609beb6662b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87909666"
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Utilizador de colaboração B2B reivindica mapeamento no Azure Ative Directory

O Azure Ative Directory (Azure AD) suporta a personalização das alegações emitidas no token SAML para utilizadores de colaboração B2B. Quando um utilizador autentica a aplicação, o Azure AD emite um token SAML na aplicação que contém informações (ou reclamações) sobre o utilizador que as identifica de forma única. Por predefinição, isto inclui o nome de utilizador do utilizador, endereço de e-mail, primeiro nome e apelido.

No [portal Azure,](https://portal.azure.com)pode visualizar ou editar as reclamações que são enviadas no token SAML para a aplicação. Para aceder às definições, selecione as aplicações **Azure Ative Directory**  >  **Enterprise** > a aplicação configurada para um único sinal de > único sinal de **inscrição**. Consulte as definições de ficha SAML na secção Atributos do **Utilizador.**

![Mostra os atributos simbólicos SAML na UI](media/claims-mapping/view-claims-in-saml-token.png)

Existem duas razões possíveis para que possa ter de editar as alegações emitidas no token SAML:

1. A aplicação requer um conjunto diferente de URIs de reclamação ou valores de reclamação.

2. A aplicação requer que a alegação do NameIdentifier seja outra coisa que não o nome principal do utilizador (UPN) que está armazenado em Azure AD.

Para obter informações sobre como adicionar e editar reclamações, consulte [Personalizar reclamações emitidas no token SAML para aplicações empresariais no Azure Ative Directory](../develop/active-directory-saml-claims-customization.md).

Para os utilizadores de colaboração B2B, o mapeamento do NameID e do inquilino cross-tenant da UPN são impedidos por razões de segurança.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre as propriedades do utilizador de colaboração B2B, consulte [propriedades de um utilizador de colaboração Azure Ative Directory B2B](user-properties.md).
- Para obter informações sobre fichas de utilizador para utilizadores de colaboração B2B, consulte [os tokens do utilizador em colaboração Azure AD B2B](user-token.md).

