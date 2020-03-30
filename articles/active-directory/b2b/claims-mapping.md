---
title: Utilizador de colaboração B2B reclama mapeamento - Diretório Ativo Azure
description: Personalize as alegações do utilizador que são emitidas no token SAML para utilizadores B2B do Diretório Ativo Azure (Azure AD).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74273189"
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Utilizador de colaboração B2B reclama mapeamento no Diretório Ativo azure

O Azure Ative Directory (Azure AD) suporta a personalização das alegações emitidas no token SAML para utilizadores de colaboração B2B. Quando um utilizador autentica a aplicação, a Azure AD emite um token SAML na aplicação que contém informações (ou reclamações) sobre o utilizador que os identifica de forma única. Por predefinição, isto inclui o nome de utilizador, endereço de e-mail, primeiro nome e apelido.

No [portal Azure,](https://portal.azure.com)pode ver ou editar as reclamações que são enviadas no token SAML para a aplicação. Para aceder às definições, selecione**aplicações** **Azure Ative Directory** > Enterprise > aplicação configurada para um único sign-on > **single sign-on**. Consulte as definições de ficha SAML na secção **Atributos** do Utilizador.

![Mostra os atributos de ficha SAML na UI](media/claims-mapping/view-claims-in-saml-token.png)

Existem duas razões possíveis para que seja necessário editar as alegações emitidas no token SAML:

1. A aplicação requer um conjunto diferente de URIs de reclamação ou valores de reclamação.

2. A aplicação requer que o NameIdentifier pretenda ser algo diferente do nome principal do utilizador (UPN) que está armazenado em Azure AD.

Para obter informações sobre como adicionar e editar reclamações, consulte [a Personalização de reclamações emitidas no token SAML para aplicações empresariais no Diretório Ativo do Azure](../develop/active-directory-saml-claims-customization.md).

Para os utilizadores de colaboração B2B, o mapeamento nameID e upn cross-tenant são impedidos por razões de segurança.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre as propriedades dos utilizadores de colaboração B2B, consulte [propriedades de um utilizador de colaboração Azure Ative Directory B2B.](user-properties.md)
- Para obter informações sobre tokens de utilizador para utilizadores de colaboração B2B, consulte [Understand tokens de utilizador em colaboração Azure AD B2B](user-token.md).

